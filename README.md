# UTS FEIT Chatbot
Supervisor: [Wei Liu](https://www.uts.edu.au/staff/wei.liu)

## Description
UTS FEIT Chatbot is a chatbot directed for prospective high school students in search of universities to get into. The chatbot is able to answer user queries regarding courses at UTS including details of the course such as duration, credit points, as well as answering queries regarding the structure of courses and subjects at UTS. In this project, we aim to create a chat bot with course query for both UTS university students and postgraduate students, as well as a UI interface and a database to store chatbot’ conversions records. 
A live instance of the chatbot is [live on webpage](https://xinghaoyang.github.io/webchat/). A Youtube demo can be found below.

<a href="https://www.youtube.com/watch?v=6bxHvMHs3BA" target="_blank">Watch YouTube Demo</a>
## Functionality

### Greeting/Introduction
As a bare minimum for chatbots, the bot is able to say hello and good morning, and respond to thank you. In addition, the bot would tell users what it is able to do when asked.

### Describe an item by code or name
The bot is able to respond with a short description of a course/major/subject when presented with the code or name of the item. The description is followed by an URL to the UTS Handbook. 

### Staying in contet with slots
The chatbot stores the current entity (course) in a slot and can further continue conversations regarding said entity.

### Questions regarding details of the item
The chatbot can retrieve details of the course from the dataset, i.e. credit points, duration, as well as boolean details (is it a combined degree/honours degree or not)

### Structure information
The chatbot is able to retrieve substructures of a structure when asked (what subjects are in a subject, what majors are in a choice block, etc.)

### Database to store conversations
we also designed a database (SQLite and PostgreSQL) for chatbots to test, to collect and store the content of students' conversations with the chatbots on web pages, facilitate real-time updates. At the same time, we could expand and improve the conversation model of AI chatbot.

### Interface
Create a graphical interface for the interaction between the user and the robot to improve the user experience.
###

## Installation
This chatbot utilises [Rasa](https://rasa.com) with [spaCy](https://spacy.io) for its language processing. Install using pip with:
```
pip install rasa[spacy]
python -m spacy download en_core_web_md
python -m spacy link en_core_web_md en
```

## Command Line Interface
To train the bot from the files, use:
```
rasa train
```

Then run the bot with:
```
rasa run
```
Or to test a conversation with Rasa in the command line, run:
```
rasa shell
```

On a separate terminal, run an action server for custom actions with:
```
rasa run actions
```

## Data files
There are a few editable data files to modify the training of the chatbot

### The dataset
There are a few .csv files that contain the structure of courses and subjects at UTS. The dataset consists of recursive relationships of abstracted structures. ```data/items.csv``` simply contains the code and name of an entry in the UTS Directory, while ```data/relations.csv``` contains the said recursive relations. ```data/courses.csv``` is a specialised type of structure for courses since courses have more attributes such as credit points, ATAR, and other details as mentioned above. ```data/alt_names.csv``` contains alternate names for courses which is used for searching course names.

### NLU
```data/nlu.md``` contains training sentences to train the NLU for intents and entities. Below is an example intent:
```
## intent:intent_name
- sample sentence with [entity](entity_type)
```
```
## intent:details
- can you tell me about [c10148](code)
```
### Stories
```data/stories.md``` contains story examples which are lists of intents, entities, and responses from the bot, with * denoting user input, and - denoting bot utterance/actions Example below:
```
## story_name
* intent
- utterance
* intent{"entity_type":"entity"}
- action
```
```
## story_details_02
* greet
- utter_greet
* details{"name":"bachelor of science in it"}
- action_details
* thanks
- utter_thanks
* goodbye
- utter_goodbye  
```
### Domain
Domain (```domain.yml```) is described as the ‘universe’ of the chatbot which contains lists of intents, entities, slots, and actions, where slots are saved entities/variables that define the story, while actions are custom responses that are executed after a command from the user. The domain also contain lists of simple utterances (as opposed to custom actions). For example:
```
utter_greet:
- text: Hi there! I am Stu from UTS!
- text: Hi! My name is Stu!
- text: Hi there! I am Stu. Do you have any questions about FEIT at UTS?
- text: Hey! I am a chatbot from UTS. Ask me what I can do!
- text: Hello! I am Stu and I am from UTS 
```
### HDU new questions

Users can ask questions about the time of the IT course. Some examples of new questions：

- which semester does 48024 take classes?
- which semester does 48024 in bachelor of information technology take classes?
- when is the 41092 in Bachelor of Information Technology?
- which semester does 41889 start?


### Other yml files

```endpoints.yml``` contains endpoints required by the bot (i.e. action server), while ```credentials.yml``` are credentials required when running the chatbot on other platforms such as Facebook.

### Python script
```actions.py``` contains python codes that are run on action calls. Actions are represented as classes which has a function ```name``` that returns a string that corresponds to the name on the domain. The method ```run``` takes three attributes, a dispatcher, tracker, and domain that are used for the run methods.
```directory_loader.py``` is a python script that loads the dataset in an Object-Oriented environment. The script overrides the [] operators for easy access for entries in the dataset. Functions in the file include:
```
code()                  -> returns full code (e.g. C10219 for 10219)
get_name()              -> returns official item name
get_search_list()       -> returns list of alt. names paired with ID
get_type()              -> returns type (course, major, subject, etc.)
url()                   -> returns url on UTS handbook 
```

## Steps for making a webchat tool
Before using the webchat widget, one need to register and install the [ngrok](https://dashboard.ngrok.com/signup) by following the "Setup & Installation" four steps. Ngrok allows you to expose a web server running on your local machine to the internet. After installation, the following 6 steps help to enable the webchat.

**Step 1:** Firstly, you should have an editable webpage, and add the following code into your page ```<body/>```:
```
<div id="webchat"/>
<script src="https://storage.googleapis.com/mrbot-cdn/webchat-0.5.0.js"></script>
<script>
  WebChat.default.init({
    selector: "#webchat",
    initPayload: "/get_started",
    interval: 1, // 1000 ms between each message
    customData: {"userId": "123"}, // arbitrary custom data. Stay minimal as this will be added to the socket
    socketUrl: "https://c0c20d0c.ngrok.io",
    socketPath: "/socket.io/",
    title: "FEIT Course Assistant",
    subtitle: "Welcome!",
    inputTextFieldHint: "Type a message...",
    connectingText: "Waiting for UTS server...",
    hideWhenNotConnected: false,
    fullScreenMode: false,
    profileAvatar: "https://upload.wikimedia.org/wikipedia/en/thumb/d/d6/UTS_emblem.png/150px-UTS_emblem.png",
    openLauncherImage: 'myCustomOpenImage.png',
    closeLauncherImage: 'myCustomCloseImage.png',
    params: {
      images: {
        dims: {
          width: 300,
          height: 200,
        }
      },
      storage: "local"
    }
  })
</script>
```
Now, the webchat widget should be appearred on your webpage, but the status is "Waiting for UTS server...". Notably, many parameters in this code can be redefined by user. For example, the "interval" controls the waiting time for each query, and the "profileAvatar" is the picture in chat, etc.

**Step 2:** Edit the ```credential.yml``` file as follows
```
socketio:
  user_message_evt: user_uttered
  bot_message_evt: bot_uttered
  session_persistence: true
```
**Step 3:** Edit the ```endpoints.yml``` file as follows
```
action_endpoint:
    url: http://localhost:5055/webhook
```
**Step 4:** Open the first terminal (all the terminals should be opened under the "capstone-master" folder) and start the *ngrok*
```
 ./ngrok http 5005
```
Ngrok will generate two random links (like https://c0c20d0c.ngrok.io) with different head, i.e., "http://" or "https://". Use one of them to update the "socketUrl" parameter defined in the first step.

**Step 5:** Open the second terminal and start the action server
```
rasa run actions
```
**Step 6:** Open the third terminal and run the chatbot
```
rasa run -m models/20190717-114901.tar.gz --endpoints endpoints.yml --credentials credentials.yml
```
This starts the well-trained chatbot stored under the "models" folder, and the webchat tool should be run fine. 

## Steps for deploying RASA X, storing conversation data and publishing chatbot through ngrok

**Step 1:** 
RASA X Installation for MACOS
The recommend method for installing RASA X in MACOS is through Multipass. This is the installation link for Multipass : https://multipass.run/ .After the installation of Multipass, you need to create an Ubuntu instance and access by below commands.
```
multipass launch --name k3s --mem 4G --disk 50G
multipass shell k3s

```
After creating the instance, you can download the RASA X now.
The command is: 
```
curl -s get-rasa-x.rasa.com | sudo bash

```
Once it finish loading, you can execute the command to check the ipv4 to access the RASA X.
The command is: 
```
multipass info k3s

```
**Alternatives for Step 1:** 
RASA X installation for Linux
Before install Rasa X, you should create a virtual environment through Anaconda. This is link for Anaconda: https://www.anaconda.com/products/individual .Rasa X is the improvement and update of Rasa, so you need to install Rasa firstly as well as pip package.
```
pip install rasa[spacy]
python -m spacy download en_core_web_md
python -m spacy link en_core_web_md en
```
Then, you should execute the command below to install Rasa X:
```
pip install -U rasa-x --extra-index-url https://pypi.rasa.com/simple
```

**Step 2:** 
Ngrok installation for Linux
The link for Ngrok installation for Linux: https://ngrok.com/download .
First, download the ngrok client.On Linux or OSX you can unzip ngrok from a terminal with the following command.
```
unzip /path/to/ngrok.zip
```
Running this command will add your authtoken to your ngrok.yml file. 
```
./ngrok authtoken <YOUR_AUTH_TOKEN>
```
To start a HTTP tunnel on port 5005, run this next:
```
./ngrok http 5005
```
Finally, you can find "http://" or "https://",open any of them and this is the network address of the chatbot.

**Step 3:** 
Now, you can check whether the installation is successful by exmining Command Line for RASA X 

On a separate terminal, run an action server for custom actions with:
```
rasa run actions
```
Then open the second terminal and execute Rasa X:
```
Rasa X
```

**Step 4:** Tracker Stores
All conversations are stored within a tracker store. In this chatbot, the conversations are stored in rasa.db. The official compatible databases is: PostgreSQL, Oracle and SQLite. For setting up the database with SQL, user need to add the require configuration in endpoints.yml.
The configuration is the following:
tracker_store:
```
    type: SQL
    dialect: "sqlite"  # the dialect used to interact with the db
    url: ""  # (optional) host of the sql db, e.g. "localhost"
    db: "rasa"  # path to your db
    username:  # username used for authentication
    password:  # password used for authentication
    query: # optional dictionary to be added as a query string to the connection URL
    driver: my-driver
```
After adding the configuration, the conversation data can be saved the the database.
