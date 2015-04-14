PyGtalkRobot is an open source python gtalk bot framework using XMPPPY library  referencing the source code of [python-jabberbot](http://thpinfo.com/2007/python-jabberbot/).


---

# Introduction #

Instant Message(IM) bot is very convenient and helpful for users. Users can just type a few of words to get what they needs, no need to open a browser or other software.

There are many IM bots that can serve users very conveniently, for example, mini blog bots(such as  http://fanfou.com/ ), dictionary bots( such as a Chinese-English dictionary bot **imdict@imdict.net**), RSS bots(such as **anothr@gmail.com**).


---

PyGtalkRobot is an open source python gtalk bot framework for developing Gtalk bot very easily.

You can just write tens of lines of python code to make your own bots online.

PyGtalkRobot use **Regular Expression Pattern** as the command controller, which means you can develop very complicated command system and more intelligent Gtalk bots.


---

# Details #

Such as:

command 1: **_email ldmiao@gmail.com hello, nice to meet you_**

means send an email to **_ldmiao@gmail.com_** with subject **_hello_** and content **_nice to meet you_**

and

command 2: **_email is a very convenient way to communicate with friends._**

means just save the content to the chat history for further use.


These two commands can be distinguished by regular expression:

command 1:
  * **`(email)\s+(.*?@.+?)\s+(.*?),\s*(.*?)`**

command 2:
  * **`(.*)`**

In which, command 1 have the higher priority than command 2.


---

# Install #
## Prerequisite ##
  * Python 2.4+: http://www.python.org/ or ActivePython:http://www.activestate.com/Products/activepython/

## Dependencies ##
  * xmpppy: http://xmpppy.sourceforge.net/
  * pydns: http://pydns.sourceforge.net/

## Install ##
  * Checkout the source code form SVN server:

`svn checkout http://pygtalkrobot.googlecode.com/svn/trunk/pygtalkrobot/src pygtalkrobot-read-only`

## How to Use ##
  1. Just import the lib PyGtalkRobot.py: `from PyGtalkRobot import GtalkRobot`.
  1. Subclass the GtalkRobot class
  1. Add methods starting with **_`command_`_**, these will be exported as commands (e.g. def **_`command_01_display_id(self, user, message, args)`_**);
  1. The methods should send the message (or None if the command gives no reply) back to the **_`user`_** using method **_`replyMessage(self, user, message)`_**
  1. Create an instance of your bot, supplying username and password
  1. Call the start() method of your instance
  1. Please reference the sampleRobot.py for a sample.


---


## Sample Bot code([python syntax highlighted](http://code.google.com/p/pygtalkrobot/wiki/sampleRobotCode) [download](http://pygtalkrobot.googlecode.com/files/sampleRobot.py)) ##


```

import sys
import time

from PyGtalkRobot import GtalkRobot

#########################################################################################

class SampleBot(GtalkRobot):
    
    #Regular Expression Pattern Tips:
    # I or IGNORECASE <=> (?i)      case insensitive matching
    # L or LOCALE <=> (?L)          make \w, \W, \b, \B dependent on the current locale
    # M or MULTILINE <=> (?m)       matches every new line and not only start/end of the whole string
    # S or DOTALL <=> (?s)          '.' matches ALL chars, including newline
    # U or UNICODE <=> (?u)         Make \w, \W, \b, and \B dependent on the Unicode character properties database.
    # X or VERBOSE <=> (?x)         Ignores whitespace outside character sets
    
    #"command_" is the command prefix, "001" is the priviledge num, "setState" is the method name.
    #This method is used to change the state and status text of the bot.
    def command_001_setState(self, user, message, args):
        #the __doc__ of the function is the Regular Expression of this command, if matched, this command method will be called. 
        #The parameter "args" is a list, which will hold the matched string in parenthesis of Regular Expression.
        '''(available|online|on|busy|dnd|away|idle|out|off|xa)( +(.*))?$(?i)'''
        show = args[0]
        status = args[1]
        jid = user.getStripped()

        # Verify if the user is the Administrator of this bot
        if jid == 'ldmiao@gmail.com':
            print jid, " ---> ",bot.getResources(jid), bot.getShow(jid), bot.getStatus(jid)
            self.setState(show, status)
            self.replyMessage(user, "State settings changed！")

    #This method is used to send email for users.
    def command_002_SendEmail(self, user, message, args):
        #email ldmiao@gmail.com hello dmeiao, nice to meet you, bla bla ...
        '''[email|mail|em|m]\s+(.*?@.+?)\s+(.*?),\s*(.*?)(?i)'''
        email_addr = args[0]
        subject = args[1]
        body = args[2]
        #call_send_email_function(email_addr, subject,  body)
        
        self.replyMessage(user, "\nEmail sent to "+ email_addr +" at: "+time.strftime("%Y-%m-%d %a %H:%M:%S", time.gmtime()))
    
    #This method is used to response users.
    def command_100_default(self, user, message, args):
        '''.*?(?s)(?m)'''
        self.replyMessage(user, time.strftime("%Y-%m-%d %a %H:%M:%S", time.gmtime()))

#########################################################################################
if __name__ == "__main__":
    bot = SampleBot()
    bot.setState('available', "Simple Gtalk Robot")
    bot.start("account_name@gmail.com", "password")

```




---

# Related projects #
  * python-jabberbot: A simple Jabber Bot for Python: http://thpinfo.com/2007/python-jabberbot/