---
layout: post
title:      "Jupyter on your phone (or anywhere, really)"
date:       2019-11-16 06:28:46 +0000
permalink:  jupyter_on_your_phone_or_anywhere_really
---


Think of all the downtime in your daily life that goes to waste. That hour on the subway, that afternoon at the dmv, that 20 minutes at work when the servers are down and you're waiting for the IT department to get things up and running again. Would'nt it be great if you could use that time to get some data science work in? 

To that end, I started to wonder if I could get Jupyter to work on my phone. At first I tried to install Jupyter natively on my phone using the Termux command line app, but that didnt go so well. Then I thought to myself " wait a second: doesnt Jupyter use a server-client model and run on a browser? Why not just set up my own server?". In a couple of hours (okay maybe more because I had to constantly google stuff), I had my Jupyter notebooks hosted on a virtual server. I could now transition seamlessly from working on my labs on my laptop at my desk, to my tablet on the couch, to my phone on....well....lets just say other seats in the house. All data science, all the time. 

There are no easy, end to end tutorials on how to set up Jupyter on a remote machine, especially for newcomers. Once I managed to get mine working, I thought maybe other people could benefit from such a walkthrough. It' makes for a great blog post! 

So, without much further ado, lets get started on setting up a remote server running Jupyter!

1. Spin up a virtual server. 

To set up our Jupyter instance to run everywhere, we will need to create a virtual server. A virtual server is basically a server you rent from a third party. Actually, you rent like a small slice of their server that acts like an independant server. This way you dont need to buy, install and maintain fancy server hardware. It's not super expensive either, at least for a very basic server.  Theres a number of companies that offer this service. Amazon lightsail is one example, but I personally prefer Digital Ocean. 

Digital Ocean allows you to get a 'droplet', which is what they call their virtual servers. They start at $5 a month, and go up from there. The only downside is that, well, data science is kind of computationally intensive at times and the $5 server might take forever to run even marginally complex data science tasks. You can always upgrade your droplet to higher specs tho, and its pretty easy and doesnt require any kind of migration or anything. Ive been running the $10 droplet and its been working fine for me. If ever in the future I need to use the more serious computing power of my laptop with its oodles of ram and discrete GPU card, I can always branch the repo im on and work on it on my laptop. 

Head on over to digitalocean.com and create an account. After you confirm your email, it will ask you to enter a payment method. After that, it will ask you to create your first project. Creating a project has not cost associated with it, projects are just used to organize your servers, and you need to have at least one project. Name your project, choose 'python' from among the list of tools (its under DevTools) and hit start.

The next screen will take you to the dashboard for your project. Click the green 'Create' button on the top right and choose 'Droplet' from the menu.  Digital Ocean has a guide on how to create a droplet (https://www.digitalocean.com/docs/droplets/how-to/create/) but ill walk you through it. 

On the Create Droplet page, for Distribution, keep the default Ubuntu selection, and go for the standard plan. Below that you will see the mothly plan options; pick whichever one you like (scroll left for the lower priced ones). You can add block storage if you want, its not required. Choose a data center regions thats relatively closest to you. Leave the "additional options" blank. 

For authentication, you cam choose ssh keys if you want and if your familiar with how those work. For this tutorial I recommend you go with 'one time password' and I'll show you how to set up ssh keys later (maybe lol). 

Only one droplet under "How many Dropets", ofcourse. In "choose a hostname", enter any name you like for your soon to be created baby server! You can enable backups if you like, its a dollar extra. After that, just click the big green "Create Droplet" button and Voila!! You are now the proud owner of a virtual server!

In a few minutes you will get an e-mail with the password and ip address of your new server (it might be the secodn of two emails from digital ocean). Once you have those, we will be diving into the command line to access your new server and set it up. 

2. Setting up your new server. 

The e-mail from digital ocean will have the following information:

* The IP address for the droplet
* The password for the droplet. 

We will be using ssh to connect to the server. SSH is a command line utility that creates secure connections between machines. Head on over to a command line terminal. If your on windows, you have  a few options. The easiest is git bash, which works just fine. Download the git package for windows from https://gitforwindows.org/. Alternatively, you can enable the linux subsystem for windows and install Ubuntu natively on your machine (check out https://docs.microsoft.com/en-us/windows/wsl/install-win10). The most annoying way is to use PuTTY, which I wont go into. 

If youre on a mac...well I hear macs have a terminal built in so....

Once your at your command line, you will enter

ssh root@[ip address for your machine].

It will ask you if your cure you want to connect. Type yes. It will ask you for root@[your ip] 's password. You can type out the password you were e-mailed. Or you can carefully paste it from the email (watch out for trailing whitespace). Remember that when you type or paste text into password prompts in the command line, it doesnt actually display any text and the cursor stays the same. The first time you login it will ask you to change your password. 

Congratulations!! you are now in your server. The very first thing I like to do when I access a fresh new machine is to do some updates. 

First off:

`sudo apt update`

Then 

`sudo apt upgrade`

This will bring your system up to date. 

After updating, the next step is typically to set up a new user account with sudo priveleges.

```
adduser [enter a user name here]
usermod -aG sudo [the username you made]
```

Now switch over to this user:

su [new user name].

Awesome! Lets move on to setting up Anaconda and all our Python packages, including Jupyter. 

3. Installing Anaconda (and thus, Jupyter)

You'd think you'd be able to use apt-get to install Anaconda, right?  Unfortunately thats not the case.  Here's a video I found of a method that actually works (https://youtu.be/jo4RMiM-ihs). As usual, I'll walk you through as well, partly as this video doesnt tell you how to get the link itself. 

Head on over to Anaconda.com Hit Download. At the top, switch to the Linux option and make sure you are looking at the Linux installer downloads. Right click on the Python 3.7 version Download button and click 'copy link address'.
![](<blockquote class="imgur-embed-pub" lang="en" data-id="a/kqx68HA" data-context="false" ><a href="//imgur.com/a/kqx68HA"></a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>)

Now head over to your terminal, which should be ssh'd into your server. Make a new folder and cd into it ( you do know how to do that, right?). Then, run the following command:

`sudo wget [paste link you copied from anaconda here]`

This will download the Anaconda bash script to the new folder we made. To run it, type:

`sudo bash [name of the downloaded file ( start typing Anaconda then hit tab should work)]`

Hit yes and more and yes etc etc. Just let it do its thing.  Might take a minute. At some point it will tell you the location where it will install Anaconda; you can specify a diff location if you like but definitely make a note of wherever it does get installed. Once its done, Congrat!! you now have Anaconda on your server!

4. Configure Jupyter

Now lets set up Jupyter so you can access it over the internet. My sincere gratitude to Dr. Lerner, whose own [blog post](https://lerner.co.il/2017/02/01/five-minute-guide-setting-jupyter-notebook-server/) was an invaluable pointer in the right direction and is the foundation of my own post. Our approach has been slightly different from his, but we will be configuring Jupyter in the same way. 

Make sure you are ssh'd into your server, and logged in using the user account you created (and using which you installed Anaconda). Make sure that your base conda is active (the command line should say 'base' before the user name). Now, enter the following command into the terminal:

`jupyter notebook --generate-config`

This will generate a configuraiton file which we will have to modify and edit. When you run the command, it should say 'Writing default config to [path to file]'. Copy this path and open it for editing. You can do this by:

`sudo nano [path to config file]`

Now go ahead and find following four lines in nano, changing them to match what Im showing here, and uncommenting them so they take effect(i.e. delet the hash(#) marks before the start of the line). In nano, you can go to a line by searching for it. 

c.NotebookApp.open_browser = False
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.password = ' '     
c.NotebookApp.token = ' '

To be clear, password and token are to be left blank. There is nothing between the single quotes so make sure there isnt a space there. If your gonna copy paste the lines above make sure you take the space out. I just put it there to make it clear it wasnt a single double quote. 

PLEASE NOTE: You can set passwords for Jupyter notebooks. And you probably should. Otherwise anyone can open your notebooks and delete all your data. Some sick people out there. Just sayin'

So far, I've shown you the steps to just get it to work. But to add a password, we will do a couple of things differently. 

First of all, in the config file, instead of setting the value of c.NotebookApp.token, leave it at the default value ( usually '<generated>').  If you want, you can run the jupyter notebook --generate-config file and hit 'yes', this will basically reset the config file to its original state (And you just have to redo the other three lines we described above). 

Once you have done that, go back to your command line and run the following command:

`jupyter notebook password`

It will ask you to enter and reenter a password. Do so, make a note of the password and hang to it. When the time comes, we will use it. For now, just note that if you wanted to change the password you would just run the above command again (wont ask you for your old password to set a new one -handy if you forget your old one).

make sure you arent in root when starting jupyter

5. Configure firewall

 

!! WARNING !! I am, by no stretch of the imagination, any kind of expert on firewalls or network security. I basically just hack my way through to get done what I need to get done. I am not responsible for any security vulnerabilities or breaches that result from your application of my methods. I advise you to do your own research and think about your security strategy. This tutorial is for educational purposes only, and is really just meant for data science students using toy data.  If you have any kind of sensitive data I would think twice before deploying it to vps like this. 

We're in the home stretch. Time to set up your firewall.

in the command line, type the following commands one by one:

`sudo ufw default deny incoming`

`sudo ufw default deny outgoing`

`sudo ufw allow ssh`

`sudo ufw allow 8888`

`sudo ufw enable`

The preceeding will basically deny all incoming and outgoing connections to your virtual server, except for ssh connections (make sure you allow these otherwise you might get locked out of your server) and traffic on port 8888, where the jupyter server runs. For added security, its a good idea to only allow traffic from the ip addressed of devices you own. To do that I think you would deny incoming and outgoing by default, and then open up ports 22 and 8888 to specific ip addressed running a command like:

```
sudo ufw allow from [ip of device you own] to any port 22
sudo ufw allow from [ip of device you own] to any port 8888
```

Or use iptables. But im not 100%. See disclaimer above. 

6. And now.....Jupyter!

cd into any folder other than your root folder. I like to cd to a folder where I houe all the repositories for my Jupyter projects and labs. Once here, run the following command

`nohup jupyter notebook &`

Now open your web browser and navigate to [your servers ip address]:8888. Remember, your servers IP address was e-mailed to you when you created the droplet. So the url you go to would look somehting like :        

167.34.187.34:8888

If you set the password like I outlined above, you will see a login page asking your for a password or token. Simply enter that password, and voila! you should see Jupyter running before your eyes. 

You can basically browse to the address I described above from any browser. The transition is seamless. You can also actually set a domain name to forward to your server so instead of typing in your ip address you can just do www.dataperson.com:8888 or whatever. This is something you do through your server hosting company. If you used Digital Ocean, go to your project dashboard, and hit the three dot menu next to your droplet's name and click "Add a Domain". I'm sure you can figure out the rest yourself ;)

You are now set to Jupyter everywhere.

Happy coding!










































