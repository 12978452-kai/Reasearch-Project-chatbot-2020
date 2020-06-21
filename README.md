# UTS FEIT Chatbot
Supervisor: [Wei Liu](https://www.uts.edu.au/staff/wei.liu)

## Description
UTS FEIT Chatbot is a chatbot directed for prospective high school students in search of universities to get into. The chatbot is able to answer user queries regarding courses at UTS including details of the course such as duration, credit points, as well as answering queries regarding the structure of courses and subjects at UTS. 

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

###

## RASA X Installation for MACOS
The recommend method for installing RASA X in MACOS is through Multipass. This is the installation link for Multipass : https://multipass.run/
After the installation of Multipass, you need to create an Ubuntu instance and access by below commands.
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

## Tracker Stores
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
## Rasa x installation for Linux
Before install Rasa X, you should create a virtual environment through Anaconda. This is link for Anaconda: https://www.anaconda.com/products/individual 
Rasa X is the improvement and update of Rasa, so you need to install Rasa firstly as well as pip package.
```
pip install rasa[spacy]
python -m spacy download en_core_web_md
python -m spacy link en_core_web_md en
```
Then, you should execute the command below to install Rasa X:
```
pip install -U rasa-x --extra-index-url https://pypi.rasa.com/simple
```
On a separate terminal, run an action server for custom actions with:
```
rasa run actions
```
Then open the second terminal and execute Rasa X:
```
Rasa X
```
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
