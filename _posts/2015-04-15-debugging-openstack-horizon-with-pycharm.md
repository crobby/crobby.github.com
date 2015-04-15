---
layout: post
title: "Debugging Openstack Horizon with PyCharm"
description: ""
category : lessons
tagline: "ftw"
tags : [tutorial, pycharm, horizon, openstack, debug]
---
{% include JB/setup %}

I have been using PyCharm to code and debug Openstack Horizon for about a year now.  
Call me weak for not just using pdb or my raw programmer instincts, but I
love my IDE and find that I'm much more effective when using it.

## My setup

You may have a slightly different setup and preferences, but you should be able to adapt these steps to your environment.
For this example, I'm using PyCharm 4.0.6, but I have also run successfully under PyCharm 3.4.x.

1)  I am running <a href="http://docs.openstack.org/developer/devstack/">devstack</a> on fedora 20.  
  This shouldn't really matter much as you should be able to run and debug Horizon with any setup, but I'm including it fyi.
  
2)  In PyCharm, choose New Project and then choose Check out from Version Control.

3)  Choose GitHub and for the Git Repository URL enter: git://github.com/openstack/horizon.git  
choose your parent directory and target directory names as well, then click Clone.  After it finishes
the clone, go ahead and tell it to open the newly created directory.

4)  I like to run in a virtual environment, so now we need to create that venv.  Open up a terminal window and change
to the directory where you cloned the Horizon source.  Run the command `python tools/install_venv.py` to trigger
the installation of your virtual environment.

5) Back in PyCharm, click on File and then Settings.  Under Project Interpreter, use the select box at the top to choose 
the interpreter from <your local horizon directory>/.venv/bin/python.  Click Ok.

5)  Click on Run and then Edit Configurations.  It should have recognized the project as a Django server project.

6)  I recommend changing the port to any port that you know to be unused on your system.  If the default of 8000 works for you, great.

7) Check the "No reload" checkbox to prevent the server from restarting at every code change.

8) Add an Environment variable:  DJANGO_SETTINGS_MODULE=openstack_dashboard.settings

9) Under the Python Interpreter dropdown, make sure that the "Project Default" interpreter is chosen (ie: the same one from step 5).  Click "Ok".

10) You will need to create a local_settings.py file.  The easiest way to do this is to go to openstack_dashboard/local and copy the local_settings.py.example file
to a file in the same directory named "local_settings.py". If you are running PyCharm on the same machine as your devstack instance
(or whatever Openstack installation you have), you probably don't need to make any changes to the file.  If your PyCharm is on a separate machine from
your devstack installation, you will need to change the OPENSTACK_HOST config param to the IP or hostname of your devstack machine.  Obviously,
you will also want to make sure that any firewalls involved will allow the required access.  Be sure to save your local_settings.py if you edited it.

11) Click on Run, Debug and then choose the "horizon" configuration that you just created.  It is probably named "horizon" unless you changed the default.
You may be prompted to "Add files to Git".  We don't actually want to have these files stored in git, so you should uncheck them all and click "Ok".

You should see something like the following in your Console tab:
```
    pydev debugger: starting

    Connected to pydev debugger (build 139.1659)
    Performing system checks...

    System check identified no issues (0 silenced).
    April 15, 2015 - 18:25:41
    Django version 1.7.7, using settings 'openstack_dashboard.settings'
    Starting development server at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.
```

12) You should be able to go to http://127.0.0.1:8000 and be greeted with the Horizon login page.

13) You can set breakpoints in PyCharm by clicking on the bar to the left of the line of code where
you'd like to set the breakpoint.  A pinkish-red circle should appear when your breakpoint is set.
Give it a try, set a breakpoint and then navigate to the page that should trigger that piece of code.
By default, PyCharm will bring up the "Debugger" tab at the bottom and you can see your current call
stack and variables.  Here you can inspect/edit any variables you desire as well as do any of the
standard debugging step operations that you'd expect.  To resume your program's execution you can
press F9 or just click on the green arrow that points to the right (it's located to the left of your
call stack by default).
