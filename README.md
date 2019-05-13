# GPT-2 Test

## Intention

To investigate the generated content provided by the GPT-2 117M learned model - [GPT-2 repo](https://github.com/openai/gpt-2.git).

## Feature creep

-   Download each iteration as .txt
-   Download all iterations as .txt
-   On live, create request and output logs

## Setup

-   Clone this repo
-   Open terminal and `cd` to the cloned repo location

```
brew install python
pip3 install virtualenv
cd src
virtualenv venv
source venv/bin/activate
pip3 install Flask flask-cors tensorflow==1.13.1
pip3 install -r requirements.txt
python3 download_model.py 117M
python3 download_model.py 345M
```

## Running

In terminal:

```
python3 application.py
```

-   Go to [http://0.0.0.0:80](http://0.0.0.0:80)
-   Insert the text you want to use as a seed in the text field and hit submit
-   A **_waiting for repsonse..._** message will appear until the generated content is...uh...generated, then the content will replace the message

## GPT-2 repo README

This repo is based on the [original GPT-2 repo](https://github.com/openai/gpt-2.git), you can read the setup instructions in this repo's _GPT2-README.md_

## Creating the live server

Tried to get this up on Heroku or a Lambda but both have storage size limits which are too small for the 500MB model + repo code and requirements.

-   Heroku - 500MB
-   Lambda - 250MB

So had to settle for a custom EC2 build.

### AWS EC2 instance

-   **Step 1**
    -   Amazon Linux 2 AMI
-   **Step 2**
    -   Select t2.medium
-   **Step 3:**
    -   Auto-assign Public IP to _Enable_
    -   Otherwise Default settings
-   **Step 4**
    -   Default settings
-   **Step 5**
    -   `teamDL: labs@ft.com`
    -   `systemCode: ftlabs-gpt2`
    -   `environment: p`
-   **Step 6**
    -   Use `ftlabs-gpt2SG` security gruop which grants:
        -   SSH - FT internal IPs
        -   HTTP - 0.0.0.0/0, ::/0
        -   HTTPS - 0.0.0.0/0, ::/0
-   **Step 7**
    -   Create using new key (currently `ftlabs-gpt.pem`) or ley you already have access to

### Installing the repo and requirements

```
sudo yum install python3 git gcc python3-devel
git clone https://github.com/ftlabs/gpt2-gui.git
cd ~/gpt2-gui/src
sudo pip3 install Flask flask-cors tensorflow==1.13.1
sudo pip3 install -r requirements.txt
sudo python3 download_model.py 117M
sudo python3 download_model.py 345M
```

### Add a cron job to restart the server on reboot

-   `crontab -e`
-   `i`
-   paste this line: `screen -dmS gpt2-gui-server bash -c 'cd ~/gpt2-gui/src; sudo python3 application.py; exec sh'`
-   press esc
-   type `:wq`

### Running

-   Use `screen -r` to see running script (should be one from booting up server)
-   if not:

```
cd ~/gpt2-gui/src
screen -dmS gpt2-gui-server bash -c 'cd ~/gpt2-gui/src; sudo python3 application.py; exec sh'
```

_cmd + shift + a_ then _d_

### Updating

-   ssh into server
-   screen -r gpt2-gui-server
-   _cmd + shift + a_ then _d_
-   _y_

```
cd gpt2-gui
git pull origin master
cd src
screen -dmS gpt2-gui-server bash -c 'cd ~/gpt2-gui/src; sudo python3 application.py; exec sh'
```

Server is now running and you can exit the ssh session.

### Checking

-   `screen -ls` - see current running screens
-   `screen -r <name>` - access a specific screen

#### Close an exisitng screen

-   `screen -r <name>` - access a specific screen
-   _cmd + shift + a_ then _k_
-   _y_

## Appendix

-   [Deploying flask to AWS tutorial](https://www.codementor.io/dushyantbgs/deploying-a-flask-application-to-aws-gnva38cf0)
