---
title: Make your local computer as a remote server
date: 2019-09-21 09:50:07
tags:
  - server
  - ngrok
categories: ngrok
description: Use ngrok to make you local computer as a remote server
---

## Preparation
1. Get a computer with system ubuntu(or other system you can use docker)
2. Register two free account in [ngrok](http://ngrok.com)

## Make your computer as a remote server
We can login in our computer from remote using ssh.
1. Download [ngrok](https://ngrok.com/download) and unzip it.
2. ./ngrok authtoken {{token}}, execute this command with your ngrok token
3. Use tmux to create a independent window and execute ./ngrok tcp 22.  We use tmux as we can close the ngrok window without close ngrok connection. you can close tmux window with command 'ctrl+b' and then type 'd'
4. You can see the remote host ngrok providing now. Now use another computer. You can login you server computer use ssh command. eg: ssh username@ngrok-host -p ngrok-port. Input you password and you can login.
```
./ngrok authtoken {{token}}
tmux
./ngrok tcp 22
ctrl+b +d
# In another computer
ssh username@ngrok-host -p
```

Now we have a remote server and we can ssh it. Ngrok can surely provide http host for remote request, but free ngrok account can only provide one remote request. As we use the first for ssh remote login, we can not use it for http request.
But we can use the second account for our docker server.

## Make your local web server accessed from remote.
1. You can run a web server using docker. eg: docker run guaneagler/drupal-web
2. After that, login it and use another ngrok token to export your web server.
3. Then you can access the server using host ngrok providing.
```
docker run guaneagler/drupal-web
docker exec -it container-name bash
./ngrok authtoken {{token}}
tmux
./ngrok http 80
ctrl+b +d
``` 