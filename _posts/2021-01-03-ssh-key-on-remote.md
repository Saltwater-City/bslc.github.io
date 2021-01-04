---
layout: post
title: Adding SSH Key to Remote Server
tags: ssh 
---

You could setup ssh keys so that user name and password are not required on every login on remote server.

1. Generate key-pair on local

   ``` bash
   mkdir ~/.ssh #if .ssh does not already exist
   cd ~/.ssh
   ssh-keygen #follow on screen instructions. use no password
   ssh-add -K newly_created_key # add key to ssh-agent
   ```

2. Put key on remote server

   ``` bash
   ssh-copy-id -i newly_created_key.pub remote_user_name@remote_host
   ```

   You may see a warning:
   > The authenticity of host '203.0.113.1 (203.0.113.1)' can't be established.
   ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
   Are you sure you want to continue connecting (yes/no)? yes

   You can safely put yes. 

3. Test the setup
    ``` bash
    ssh remote_user_name@remote_host
    ```
