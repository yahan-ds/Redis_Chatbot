## Purpose 
-	Creating a simple chatbot using Redis's Pub/Sub mechanism 
-	Through practice, obtaining a solid understanding of related concepts such as real-time messaging, Redis Pub/Sub, and integrating Redis with Python
-	Interacting with Docker containers and becoming proficient in the use of docker-compose

## Configuration
### container
```
docker-compose up -d
docker-compose exec <service_name> <interface>
```
service_name: python and redis <div></div>
interface: bash and redis-cli

### run chatbot interface
`python chatbot_app`

### Moniter using redis-cli
1. Ensure Redis server is running.
2. Open a terminal and run the following command
```
    docker-compose exec redis redis-cli
    MONITOR
 ```
>note: There are other available commands in redis-cli to play with, such as `KEYS *` and `DBSIZE`

## Database management
I use HASH to store user information because we have multiple kinds of information for one user to record. I also used a set to store all the user names for fast look-up. 

## Functionalities
- Options:<div></div>1. Identify yourself<div></div>2. Update your profile<div></div>3. Get info about a user<div></div>4. Join a channel<div></div>5. Leave a channel<div></div>6. Send a message to a channel <div></div>7. Read messages from a channel<div></div>8. Exit

- Commands:
    ```
    !help: List of commands
    !weather <city>: Weather update
    !fact: Show a random fun fact
    !whoami: Show your user information
    !channels: list all the channels the user subscribed
    ```

    
### user log in
a `username_set` is used to store all the user names for fast search, so when the user tries to log in, we just need to check if the name is in the set, which only takes O(1) time. Enter option 1 to log in. If the username is not in the username database, a new user will be created, and ask the user to input some information (see the user profile part). Below is the interaction in the user interface (left) and db (right).

<img src="screenshots/ui_identify.png" alt="ui_1. identify yourself" width="200" height="46"/>

<img src="screenshots/db_identify.png" alt="db_1. identify yourself" width="250" height="18"/>

### user inoformation
- Choose option 2 to update the user information or create a new user. If the username does not exist in the database, we will create a new one. <div></div>user interface:<div></div><img src="screenshots/ui_create_new_profile.png" alt="ui_2. update profile" width="600" height="270"/><div></div>database:<div></div><img src="screenshots/db_create_new_profile.png" alt="db_2. update profile" width="560" height="70"/>

- Once the user logged in, they can use the command `!whoami` to show their own profile.<div></div><img src="screenshots/cmd_whoami.png" alt="whoami" width="220" height="90"/>

- Choose option 3 to get the other users' information. This uses `HGETALL` in our redis database to retrieve the values in hash. <div></div>
<img src="screenshots/ui_get_user_info.png" alt="ui_3. get info about a user" width="330" height="220"/><div></div>
database:<div></div><img src="screenshots/db_get_user_info.png" alt="db_3. get info about a user" width="150" height="20"/>

### channel operations
Users can subscribe or unsubscribe from a channel using options 4 and 5 respectively. Below is a demo of it and using `!channle` command to get a list of channels the user has subscribed to. <div></div>
<img src="screenshots/ui_channel.png" alt="ui_4/5. channel" width="260" height="440"/>

database:<div></div>
<img src="screenshots/db_channel.png" alt="db_4/5. channel" width="300" height="50"/>


### pubsub message in Redis

Enter option 6 or 7 to send or read message(s). I use the `pubsub.get_message()` with a timeout parameter to "listen" to the channel because the `pubsub.listen()` does not support a timing mechanism and I don't want the user to use interruption keys to stop listening. I set the wait time to be 1 minute, during which the listener can read every message sent in this channel. Below is a demo for a short conversation on a news channel. Below is a demo for a conversation on the news channel.<div></div>
<img src="screenshots/ui_read_message.png" alt="read message in channel" width="350" height="100"/>
<img src="screenshots/ui_send_message.png" alt="ui_6/7. send message in channel" width="490" height="70"/>

database:<div></div>
<img src="screenshots/db_message.png" alt="messages in channel on db end" width="390" height="110"/>

### The other commands
I used ai to generate 50 random facts and some ramdom weather of 50 cities in the U.S.
<img src="screenshots/commands.png" alt="the rest commands" width="520" height="600"/>
