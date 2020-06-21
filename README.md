# UTS FEIT Chatbot
Supervisor: [Wei Liu](https://www.uts.edu.au/staff/wei.liu)

## Description
UTS FEIT Chatbot is a chatbot directed for prospective high school students in search of universities to get into. The chatbot is able to answer user queries regarding courses at UTS including details of the course such as duration, credit points, as well as answering queries regarding the structure of courses and subjects at UTS. In this project, we aim to create a chat bot with course query for both UTS university students and postgraduate students, as well as a UI interface and a database to store chatbot’ conversions records. 

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
On a separate terminal, run an action server for custom actions with:
```
rasa run actions
```
Then open the second terminal and execute Rasa X:
```
Rasa X
```
### Ngrok installation for Linux
The link for Ngrok installation for Linux:
https://ngrok.com/download .
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


