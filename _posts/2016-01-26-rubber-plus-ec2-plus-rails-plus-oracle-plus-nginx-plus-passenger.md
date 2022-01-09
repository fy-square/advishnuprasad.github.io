---
layout: post
title: "Rubber + EC2 + Rails + Oracle + nginx + Passenger"
date: 2012-02-02 03:10:36 +0530
comments: true
categories: [rails, capistrano, rubber]
---
## Deploying rails app into EC2 using Rubber 

#### Step 1 

Get the key from aws and store it in ~/.ec2 folder  

```bash
chmod 400 keyname
```

Generate the publickey     

```bash
ssh-keygen -y -f keyname > keyname.pub
```

#### Step 2

Add rubber gem in your gemfile

```ruby
gem rubber
bundle install
```

#### Step 3

Now do vulcanize. Am using nginx and passenger 

```ruby
rails g vulcanize minimal_passenger_nginx
```

#### Step 4

Now edit following fields in `config/rubber/rubber.yml`

    access key
    secret access key
    account name
    app name
    app user
    image id
    image type

#### Step 5 : 

Deploy Part :

```ruby
cap rubber:create
cap rubber:bootstrap 
cap deploy
```

If you are using oracle instant client set the environment variable in server and then do `cap deploy`

#### Troubleshooting:

Here is the way to bypass fingerprint authentication prompt

add these two lines in deploy script. 

```ruby
ssh_options[:keys] = [File.join(ENV["HOME"], ".ssh", "id_rsa")] 
set :deploy_to, "your_deploy_folder"
```
 


          



   