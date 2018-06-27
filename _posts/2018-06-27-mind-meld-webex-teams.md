---
layout: post
title: Exploring MindMeld and connecting it to Webex Teams bot
subtitle: A post to describe the process of initial setup for MindMeld Workbench and connecting it to a Webex Teams bot.
tags: [development, mindmeld, webex teams, bots, aws]
---

We had a requirements to develop a Webex Team bot capable of Natural Language Processing. It's a highly complicate process if you are going to start from scratch, so, we've decided to use already established MindMeld (which is part of Cisco).

Hence, in this post I'm going to outline all the important steps of setting up the environment, creating a simple NLP application and connecting it to a Webex Teams bot.

I'm not going to cover the basics of the installation, which are pretty well described in the [Getting Started Guide](https://devcenter.mindmeld.com/userguide/getting_started.html), but rather on the complications and peculiarities that I've faced during the project.

# Introducing MindMeld Workbench
MindMeld Workbench is the machine learning toolkit at the core of the MindMeld platform, is a Python-based framework which encompasses all of the algorithms and utilities required for this purpose. Basically, it's a set of Open Source libraries glued together and tuned to work for Natural Language Processing tasks.

The architecture of MindMeld Workbench is illustrated below:
[![alt text](/img/2018-06-27-mind-meld-webex-teams/mindmeld1.png "The architecture of MindMeld Workbench")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2018-06-27-mind-meld-webex-teams/mindmeld1.png)

In order to install the MindMeld Workbench you would need to get the developers token from the MindMeld support team. It looks like recently they've changed the procedure of acquiring the token, so the easiest way would be to ask for it in the ["Ask MindMeld Workbench"](http://incis.co/YIt) Webex Teams space, and receive the latest procedure.

# Environment and Setup
For running our bots, we are using AWS AMI EC2 _micro_ instances. However, as it turned out, MindMeld Workbench requires more resources then micro instance could offer, so we needed to switch to the _small_ instance.

Please, follow the [Getting Started Guide](https://devcenter.mindmeld.com/userguide/getting_started.html) to install the MindMeld Workbench and all the dependencies.
> Note: You need to have the developers token to be able to install it.

> Reference: That's how the directory structure looked like for us:
> [![alt text](/img/2018-06-27-mind-meld-webex-teams/mindmeld2.png "Directory structure of the Apps")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2018-06-27-mind-meld-webex-teams/mindmeld2.png)


# MindMeld Application
The main task of our application is to distinguish between 6 intents: exit, greet, help, phase1, phase2 and phase3. The first 3 are default ones, that could be used for any application.

We were using the Kwik-E-Mart from [Workbench Blueprints](https://devcenter.mindmeld.com/blueprints/overview.html) page as a skeleton for our application, then removing unnecessary parts.

An example of the main app.py file could be found below:
~~~python
from __future__ import unicode_literals
from builtins import next
from mmworkbench import Application
#import DesignThinkingBot as dt

app = Application(__name__)


@app.handle(intent='greet')
def welcome(context, responder):
    try:
        responder.slots['name'] = context['request']['session']['name']
        prefix = 'Hello, {name}. '
    except KeyError:
        prefix = 'Hello. '
    responder.reply(prefix + 'I could help you facilitate the Design Thinking Process '
                             'Please, let me know how could I help or from which phase should we start? ')
    responder.listen()


@app.handle(intent='exit')
def say_goodbye(context, responder):
    responder.reply(['Bye', 'Goodbye', 'Have a nice day.'])

@app.handle(intent='phase1')
def say_phase1(context, responder):
    responder.reply(['What would you like to build today?','Do you have any ideas that you would like to develop further using Design Thinking? Please, provide the name for the idea'])

@app.handle(intent='phase2')
def say_phase2(context, responder):
    responder.reply(['What are your users’ biggest challenges? Can you help me with documenting the main frustrations and pain points your users experienced and expressed in the Discover phase'])

@app.handle(intent='phase3')
def say_phase3(context, responder):
    responder.reply(['You have a clear sense of who you’re building for, the opportunity at hand, and the key problems to be solved. Now it’s time for the team to start identifying creative solutions. ' + '\n' + 'How can we creatively solve the problems we’ve identified? Do these solutions directly solve the user problems we defined in Problem so be Solved Statement?'])


@app.handle(intent='help')
def provide_help(context, responder):
    prompts = ["I could help you facilitate the Design Thinking Process  "
               "say 'Let's initiate phase 1' or 'Go to phase 2'"]
    responder.reply(prompts)
    responder.listen()

if __name__ == '__main__':
    app.cli()
~~~
So, as you can see there are 6 handlers for each of 6 intents, if the intent is triggered, the action in the _responder.reply_ will occur.
Now, we could determine and train the model to understand specific responses from users as one of those intents. For example, for the intent **phase1**, we are supplying the following phrases as an example of how users might enter the phase1. File _train.txt_ under _domains/des_thin/phase1_ and which looks like this:
~~~
enter phase 1
phase 1
start phase 1
Discover phase
Discover
~~~
Of course, it's a oversimplified version, just to catch some simple phrases that users might use to get inside of the phase1.
For example for the file _train.txt_ under _domains/des_thin/greet_:
~~~
...
Yo, what's good?
Yo, what's up?
Namasthe, How are you
Good morning. How are you?
Salutations!
Sup!
Salutations my friend
Welcome.
What are you up to?
Great seeing you!
whatcha doing
Hey-o!
How are ya doing?
...
~~~

After we have filled all the _train_ files, we could train the model to understand those. From Python console:
~~~
>>> from mmworkbench.components import NaturalLanguageProcessor
>>> nlp = NaturalLanguageProcessor('kwik_e_mart') #We are still using the blueprint name, but it could be your own app
>>> nlp.build()
~~~
After building the Natural Language Processing models that power the app, we could interact with it:
~~~
>>> from mmworkbench.components.nlp import NaturalLanguageProcessor
>>> nlp = NaturalLanguageProcessor('kwik_e_mart')
>>> from mmworkbench.components.dialogue import Conversation
>>> conv = Conversation(nlp=nlp, app_path='kwik_e_mart')
>>> conv.say('Hello!')
/usr/local/lib/python3.6/site-packages/sklearn/preprocessing/label.py:171: DeprecationWarning: The truth value of an empty array is ambiguous. Returning False, but in future this will result in an error. Use `array.size > 0` to check that an array is not empty.
  if diff:
['Hello. I could help you facilitate the Design Thinking Process Please, let me know how could I help or from which phase should we start? ', 'Listening...']
~~~
Above, the intent _greet_ is matched and reply in the handler _intent_ is returned.

~~~
>>> conv.say('Start with phase 1')
/usr/local/lib/python3.6/site-packages/sklearn/preprocessing/label.py:171: DeprecationWarning: The truth value of an empty array is ambiguous. Returning False, but in future this will result in an error. Use `array.size > 0` to check that an array is not empty.
  if diff:
['What would you like to build today?']
~~~
For the example above, please note, that we don't have explicit phrase _Start with phase 1_, but still the app could match the handler _phase1_ and return a random reply from the list, which is specified in the _responder.reply_ of _phase1_ handler.

For me, that's the greatest power of the MindMeld Workbench, that without much of work, we could design a lot of intents and specify phrases that could trigger actions. And if we dig more, we could also extract the data from users responses and act on it.


# Webex Teams bot application
Now, we are going to connect that MindMeld app with the bot, so any user could interact with it. Webex Teams is a modern and powerful interface of communication, from any device, anywhere.

The bot's identity was created from [Webex Teams Developer Portal](https://developer.webex.com/apps.html). A very detailed step-by-stem on how to create it could be found at [Automating Webex Teams (Python)](https://learninglabs.cisco.com/tracks/collab-cloud/automating-spark-appdev/collab-spark-overview-rest/step/1) tutorial.

I'm not going to describe all the code of the Webex Teams bot, just the integration points with MindMeld. If you are interested in example script for a bot, feel free to visit and use the [SparkScrumBot](https://github.com/dmkravch/SparkScrumBot).

So, in order to connect MindMeld to the _DesignThinkingBot.py_, we need to do the following:
~~~python
#Imports
import mmworkbench as wb
from mmworkbench.components import NaturalLanguageProcessor
from mmworkbench.components.dialogue import Conversation
#Building and creating the conversation interface
blueprint = '/home/ec2-user/DesignThinkingBot/kwik_e_mart'
nlp = NaturalLanguageProcessor(blueprint)
nlp.build()
conv = Conversation(nlp=nlp, app_path=blueprint)
~~~
And the last part, is how to pass the response to Webex Teams:
~~~python
#Bot related imports
accesstoken = cfg.bot['token']
spark_api = CiscoSparkAPI(accesstoken)
accesstoken = "Bearer " + accesstoken

def _url(path):
    a = 'https://api.ciscospark.com/v1' + path
    return a
def get_message(at, messageId):
    headers = {'Authorization': at}
    # resp = requests.get(_url('/messages/{:s}'.format(messageId)),headers=headers)
    resp = requests.get(_url('/messages/{0}'.format(messageId)), headers=headers)
    dict = json.loads(resp.text)
    dict['statuscode'] = str(resp.status_code)
    return dict

@app.route("/", methods=['POST'])
def handle_message():
  me = spark_api.people.me()
  data = request.get_json()
  if data["id"] != webhook_id:
    logging.debug("Retereived Webhook_id doesnt match. Retreived: " + data["id"])
    msgid = data["data"]["id"]
    txt = get_message(accesstoken, msgid)
    return 'ok'
  txt = get_message(accesstoken, msgid)
  roomid = data["data"]["roomId"]
  message = str(txt["text"]).lower()
  First_Name=spark_api.people.get(personid).firstName
  if personid == me.id:
    return 'OK'
  else:
    resp_dict = post_message(accesstoken, roomid, conv.say(message)[0])

if __name__ == '__main__':
    #logging.basicConfig(filename='LogDesignThinkingBot.log', level=logging.DEBUG, format='%(asctime)s %(message)s')
    #main()
    app.run(host='11.22.33.44', port=8080)
~~~
>Note: Flask, which is a micro web service, that is powering this bot, is not covered in this post.

And now, the bot will reply by the message, taken from the MindMeld app.

Below, you could find the example of how it should work:
[![alt text](/img/2018-06-27-mind-meld-webex-teams/mindmeld3.png "Directory structure of the Apps")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2018-06-27-mind-meld-webex-teams/mindmeld3.png)


# Explanation of the MindMeld App logic and how to verify it

One extra explanation that could help you troubleshoot why some intent is being chosen instead of the other, is to use the _probability_ setting. For me, it took some time to figure out how to set this setting. For this test I'm using different blueprint application, called _home_assistant_:
~~~
>>> from mmworkbench.components.nlp import NaturalLanguageProcessor
>>> nlp = NaturalLanguageProcessor('home_assistant')
>>> nlp.domain_classifier.fit(model_settings={'classifier_type': 'svm'},params={'kernel': 'linear','probability':'1'})
>>> nlp.domain_classifier.fit(model_settings={'classifier_type': 'svm'},params={'kernel': 'linear','probability':'1'})
>>> nlp.domain_classifier.config.to_dict()
{u'model_type': 'text', u'param_selection': None, u'params': {'kernel': 'linear', 'probability': '1'}, u'features': {'in-gaz': {}, 'edge-ngrams': {'lengths': [1, 2]}, 'bag-of-words': {'lengths': [1, 2]}, 'freq': {'bins': 5}, 'exact': {'scaling': 10}, 'gaz-freq': {}}, u'model_settings': {'classifier_type': 'svm'}}
>>> nlp.domain_classifier.fit(model_settings={'classifier_type': 'svm'},params={'kernel': 'linear','probability':True})
>>> nlp.domain_classifier.predict_proba('Play my jazz playlist.')                            
[(u'times_and_dates', 0.53199099542978712), (u'greeting', 0.18047037512864109), (u'smart_home', 0)]
~~~

# Summary
We just scratched the surface of what MindMeld could do and planning to explore it further. Hopefully it is useful to someone.

If you would have any questions, don't hesitate to reach out to me for more details or assistance. Thank you.
