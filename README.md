# Boomerang Beacon Catcher (PHP):

PHP application that enables Web Performance Optimization researchers and enthusiasts to collect beacon data collected from visitors browsers to a **temporary storage**. The application allows you to store the beacon data in RAW format. Later is possible to move the data to another location where operations like reading, decoding and reporting could be executed.

It stores any data passed over GET or POST request. Originally designed to store beacon data from Boomerang JS: https://github.com/SOASTA/boomerang .

Boomerang Beacon Catcher is really nothing more than a **temporary storage** for beacon data! My initial idea of creating it was that I wanted to deploy it at some cheap hosting location and start collecting beacon data very quickly. Basically this allowed me to separate collecting beacon data from reading and decoding it.

# How to use:

There are 2 important functions:
 - Catcher - Handles and stores beacon data in a temporary storage.
 - Digger - Serves collected data over HTTP to another service.
 
It's very good practice to use the Digger function often. For example multiple times per hour is a good idea because after transferring beacon data this data will be automatically removed from temporary storage. This will prevent from full disk the machine that is used as temporary storage.

## Catcher (located in app/public/beacon/catcher.php)

You can send beacon data to: www.example.com/beacon/catcher.php

### catcher.php will:
 - Provide needed HTTP headers for **Cross Origin requests**.

**File System** is used as data storage.

In case you would like to use another storage engine you should try to follow the implementation of **Catcher_Utility_Storage_Interface**.


## Digger (located in app/public/excavator/digger.php)

You can fetch the beacon data by:
```
curl --compressed www.example.com/excavator/digger.php
```

It's recommended to use **--compressed** option because this will minimize the transfer size and time.

Provided data will be in **JSON** format. Each entry in this structure after JSON decode will have structure of an associative array:
```
Array(
    [id]          => 2
    [beacon_data] => (JSON string of RAW beacon data)
    [created_at]  => 2017-12-29 18:34:42 (date formatted in 'Y-m-d G:i:s')
)
```

digger.php will **DELETE** the beacon data from the server once it gets transferred out.

### Securing digger.php

By default securing digger.php is **disabled** for simplicity and easy start. There is na option to use **Basic HTTP authentication**. 

You can enable it by:
 - Creating app/config/auth_config.php from app/config/auth_config.php.template
 - Set enabled: (true ot false) and specify username and password.

This should work in case you have this option enabled:
```
curl --compressed username:password@www.example.com/excavator/digger.php
```

## Extra / Docker

I also added basic NGINX and PHP-FPM containers that may speed up the setup for people familiar with docker. I am not sure how much time Docker could save because the basic setup of the application doesn't require many services or some complex setup but you can give it a try if you are familiar with Docker.

There are 2 things that you need to take care of:
 * Use Docker Compose version 3.
 * In **docker/nginx/nginx.conf** you may change `server_name ~.*;` to your own domain name e.g. `server_name  www.my-beacon-catcher.com;`.

You could check out what we have in: **docker/docker-compose.yaml**
