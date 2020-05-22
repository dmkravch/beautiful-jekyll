---
layout: post
title: How to bulk manage users in Control Hub via Admin APIs
subtitle: This post describes and shows examples of how to manage users via the Cisco Webex Control Hub Admin APIs.
tags: [webex, webexteams, ControlHub, user, cisco]
---
As more and more large organisations are adopting the Cisco Webex, there is a need to onboard users. Sometimes those are distributed organisations with multiple different LDAP Directories, so there is not really a way to deploy the [Hybrid Directory Connector](https://www.cisco.com/c/en/us/td/docs/voice_ip_comm/cloudCollaboration/spark/hybridservices/directoryconnector/cmgt_b_directory-connector-guide-admins/cmgt_b_directory-connector-guide-admins_chapter_01001.html). Of course there is an option to use the [CSV file bulk import](https://help.webex.com/en-us/nlkiw8e/Add-Multiple-Users-in-Cisco-Webex-Control-Hub-with-the-CSV-Template) which is perfectly fine and the way to go in such cases. However, there are just two small limitations, in case you need to onboard really big amount of users:
* It's possible to onboard **20 000** users at a time
* Speed of such onboarding (at least in my test runs were **0.6** users per second)

> **NOTE**: There is also one small thing that with the CSV import I didn't see an option to modify Title or Department of a person, but it might have been possible.

Giving that, there was a task to create a way to deploy a lot more users and in a faster way.


## Solution and results

I will start with the results that were possible to achieve with a simple python script using multithreading:
* **Any number** of users could be fed to a script and it will just run and execute
* I was able to achieve **4** users per second (which is much faster comparing to the CSV import)

### Actual code

> **WARNING**: Please note that the code might not be optimised or free of bugs

```python
from webexteamssdk import WebexTeamsAPI
import webexteamssdk
import time
import csv
import threading
import logging
#Before running this the best is to creat an environmental variable that will contain your token
#For Linux based: export WEBEX_TEAMS_ACCESS_TOKEN=OWI4OTc0YjgtNmRiMS0*********
api = WebexTeamsAPI()

#Introducing some logging
logging.basicConfig(filename='FROM_MULTI.log',level=logging.CRITICAL)

list_of_people = []
threads = []

#Here we specify the csv file, that can actually be in the same format that exported from the Control Hub
with open('Random_2000_users.csv', newline='') as f:
    reader = csv.reader(f)
    data = list(reader)

#Important step is to check the licenses that you want to assign to every users
#In the CSV import option it is specified by the True/False in the end columns
#In order to check license IDs, the best option is to use https://developer.webex.com/docs/api/v1/licenses/list-licenses

licenses = ['Y2lzY29zcGFyazovL3VzL0xJQ0VOU0UvOTE5NGIxZDQtMDE2ZS00NTRkLWFhNjgtMzRmZDU4NTdlZjJhOkVFXzEzZDhjMTlmLTY5OWEtNDY4ZC04YmQ0LTRhODZhMzM1OGI2MV9vbmJvYXJkdGVzdGluZy53ZWJleC5jb20','Y2lzY29zcGFyazovL3VzL0xJQ0VOU0UvOTE5NGIxZDQtMDE2ZS00NTRkLWFhNjgtMzRmZDU4NTdlZjJhOkNNUl82ZTAxN2U2OC0zZDgyLTQ2YTctYjc3Yy1lMmFjMTAxM2Y4ZmJfb25ib2FyZHRlc3Rpbmcud2ViZXguY29t','Y2lzY29zcGFyazovL3VzL0xJQ0VOU0UvOTE5NGIxZDQtMDE2ZS00NTRkLWFhNjgtMzRmZDU4NTdlZjJhOk1TX2VkMTFjNTA0LTljNjAtNDFlMS1iNjMxLTM1MzM2NTg2NGFjMw','Y2lzY29zcGFyazovL3VzL0xJQ0VOU0UvOTE5NGIxZDQtMDE2ZS00NTRkLWFhNjgtMzRmZDU4NTdlZjJhOkNGXzZjMzRjMDhjLWU5MTMtNDJjYi05OTQ5LTczY2MzOGRjMWYxMQ']

def chunks(lst, n):
    """Yield successive n-sized chunks from lst."""
    for i in range(0, len(lst), n):
        yield lst[i:i + n]

def create_a_persone(emails,displayName,firstName,lastName,licenses,email):
    try:
        created_person = api.people.create(emails=emails,displayName=displayName,firstName=firstName,lastName=lastName,licenses=licenses)
        print(created_person.id + " Has been successfully created")
        #list_of_people.append(created_person.id)
    except webexteamssdk.exceptions.ApiError as err:
        needed_person = api.people.list(email=email)
        for ids in needed_person:
            needed_person_id = ids.id
            try:
                updated_person = api.people.update(personId=needed_person_id,emails=emails,displayName=displayName,firstName=firstName,lastName=lastName,licenses=licenses)
                print(updated_person.id + " Has been successfully UPDATED ------------------------------ UPDATED")
                #list_of_people.append(updated_person.id)
            except webexteamssdk.exceptions.ApiError as err:
                #NOT_UPDATED.append(i[3])
                logging.critical(i[0] + ' '+ i[1] + ' ' + i[2] + ' ' + i[3])
                print("The user with email was NOT UPDATED either   " + i[3])
                pass
        pass

def update_a_persone(needed_person_id,emails,displayName,firstName,lastName,licenses):
    updated_person = api.people.update(personId=needed_person_id,emails=emails,displayName=displayName,firstName=firstName,lastName=lastName,licenses=licenses)
    print(updated_person.id + " Has been successfully UPDATED ------------------------------ UPDATED")


s = time.perf_counter()
split_data = chunks(data,80)  

for data in split_data:
    for i in data:
        email = i[3]
        emails=i[3].split()
        firstName=i[0]
        lastName=i[1]
        displayName=i[2]
        process = threading.Thread(target = create_a_persone, args=[emails,displayName,firstName,lastName,licenses,email],)
        threads.append(process)
        process.start()


    for x in threads:
        x.join()    
elapsed = time.perf_counter() - s
print(f"{__file__} executed in {elapsed:0.2f} seconds.")
```

With some tests I was able to discover that those parameters work best for me and were able to give me a great boost in the speed towards 4 users per second.

### Delete all Users

While testing the create script, sometimes I need to quickly delete big chunks of users as well. So, for this I've used the same strategy of multithreading:

```python
import logging
import threading
import time

from webexteamssdk import WebexTeamsAPI
import webexteamssdk
logging.basicConfig(filename='multi.log',level=logging.DEBUG)
#Before running this the best is to creat an environmental variable that will contain your token
#For Linux based: export WEBEX_TEAMS_ACCESS_TOKEN=OWI4OTc0YjgtNmRiMS0*********
api = WebexTeamsAPI()
people = api.people.list()

list_of_people = []

def delete_a_persone(person_id):
    api.people.delete(person_id)
    #print(person.id + ' deleted successfully')

i = 1
my_id = api.people.me().id   

s = time.perf_counter()
for person in people:
    #Filtering myself in order not to get and error
    if person.id == my_id:
        pass
    #Filtering some other users
    elif person.id=='Y2lzY29zcGFyazovL3VzL1BFT1BMRS9iMjZjNzA2Ny1lZTdjLTRkOGEtOTkzNy1kNmJmMjZiMTA3OTM':
        pass  
    else:
        #delete_a_persone(person.id)
        process = threading.Thread(target = delete_a_persone, args=[person.id],)
        process.start()
        print(str(i)+ ' ' + person.id + ' deleted successfully')
        i += 1
        list_of_people.append(person.id)
        #Here we are able to specify how many users we want to delete (in case we don't want to delete everyone)
        #otherwise just put the number higher than the actual number of users in the Control Hub
        if len(list_of_people)>1300:
            break
elapsed = time.perf_counter() - s
print(f"{__file__} executed in {elapsed:0.2f} seconds.")
```           


## Conclusion
Even thought the script is not polished enough and there still so many capabilities to include into it, still it can create large amount of users very fast, which is ideal for the tight schedules.

The good part of the Control Hub is that when, after some time, there will be a need to connect the Active Directory, it is pretty easy to accomplish, as Directory Connector matches the user email and just synchronises if it exists.

Thank you for your attention and free to contact me if you need to deploy large amount of users into the Control Hub, we will be glad to help!
