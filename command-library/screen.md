# Screen

the screen utility allows a user to deploy multiple terminal "windows" inside of one terminal. It is referred to as a **Terminal Multiplexer**. Processes running inside of *screen* virtual terminals continue continue to run even when the window is not visible to the user. It is very useful for running multiple long processes, and multitasking within a terminal

[User Manual - The GNU Foundation](https://www.gnu.org/software/screen/manual/screen.html)
[HowToGeek - screen utility - ALSO has a cool list of commands at the bottom](https://www.howtogeek.com/662422/how-to-use-linuxs-screen-command/)

--------

## Starting Screen and Getting Help

Screen has its own set of keyboard shortcuts and other controls, but in order to start a screen session all you need to do is run 

```bash
screen
```

Just one of the cool things about this utility is that you can run multiple screen sessions, so you can have multiple sessions of multiple windows.

The keyboard shortcuts in `screen` involve a lot of "latency" which is to say that you will be pressing multiple keys like `ctrl+a` and then `?` afterwards. In fact, the `ctrl + a` `?` (it is of note that you must actually input `?` as in you must hit `shift` and the`/|?` key to input `?`)
combination after opening a `screen` session will give you a list of commands for the program.

for ease of understanding, when saying `ctrl + a` you will not really be entering the `+` character as a keystroke, instead it is to imply you must enter both the `ctrl` and `a` keystrokes at the same time.


## Named Sessions

The cool thing about screen is that you can run multiple Sessions of the utility on your machine, but since they are identified by Session ID by default, you can name them to make it much easier to identify which session you will reattach to later.

```bash
screen -S session_name
```

-----

## Using Screen

OK, so you have started your screen session, but all you have been asked for is to send some fellers pizza & beer. In order to begin Multiplexing like a Mastermind you enter the following key combination to open a window with a shell

`ctrl + a`  `c`

when you enter this combination, the first available number between `0-9` will be assigned as its screen window #

### Session Keyboard Shortcuts

it is of note, you can use all of these WHILE a process or command you entered is running

`ctrl + a`  `c` - Create New Window with Shell

`ctrl + a`  `"` -  List all Windows

`ctrl + a`  `<window #>` - Swap to the Numbered Window of your choice

`ctrl + a`  'A' - Rename the current Window 

`ctrl + a`  `ctrl + a` - Toggle between Current and Previous Windows

### Session Visual Keyboard Shorcuts

shortcuts that effect how the session looks, such as split screening.
when "Region" is being used in this context, just imagine it as your Window currently, or the space within the terminal

`ctrl + a`  `S` - Split Current Region Horizontally

`ctrl + a`  `|` - Split Current Region Vertically

`ctrl + a`  `tab` - change your input cursor to another region

`ctrl + a`  `X` - Close CURRENT Region

`ctrl + a`  `Q` - Close All Regions BUT the current focused region

------

## Detaching and Reattaching

Detaching a session essentially means you leave the screen session you are currently in. Now, the best part of this utility is that every process you had running in the screen session you left will continue to run!
You can detach from a screen session by entering the following

`ctrl + a`  `d` 

This takes you back to your normal shell, however if you need to leap back in to your session, you simply run the following

```bash
screen -r
```

however, remember that you can have multiple sessions. Just running the above command will not help you at all, however you can run the following to discover the **Session ID** of the Session that you are looking for. 

list screen sessions
```bash
screen -ls
```

Here is an example of what this looks like

![[https://github.com/barkwoofdog/doclibrary/blob/43b25c455173a7c3a4dc13ab9530b1c882586324/images/screen_com.png]]

the numbers in front of `.ttys` are the Session ID's. 
So in order to get back into on of these sessions you would just run

```bash
screen -r 1496
```


## Killing Sessions

Inevitably you will have no use for your screen session anymore. This requires you to kill the session, which can be accomplished from both within and outside of the screen session in question.

In order to kill the current running window while still inside of it, you will use the following keyboard shortcut
```
ctrl+a  k  y
```

in order to kill an entire session, you have a couple options.

to kill a session from OUTSIDE of it (AKA while detached) you must Detach from the session, and then run the following after listing the screen sessions and locating the Session ID that you want to kill
```bash
screen -XS <session ID> quit
```
With the command operators involved being
`X` - Execute Command
`S` - Session PID to be Operated on 

to kill a session from INSIDE the session, you use the following keystrokes
`ctrl + a`  `:kill`

You can also use the `kill` command, as the Session ID is funnily enough, the Process ID.

```bash
kill <session ID>
```

------

## Sharing a Screen Session
[GNU Foundation - screen multisession](https://www.gnu.org/software/screen/manual/html_node/Multiuser-Session.html)

You can share sessions between users, which is particularly useful for collaborative efforts on remote machines. in order to do this, you can start a screen session daemon on the machine. Here is how you create a named session named "teamwork"

```bash
screen -dmS teamwork
```

`-dm` - these two are chained together because together they force a creation of a detached session  

`-S` - Session Naming

From inside of this session (remember you started it detached), you use the following keystrokes

`ctrl + a`  `:multiuser on`

this will enable multiuser mode, however in order for other users to join, you will need to add them to the access control list. You do this by using the following keystrokes inside of the now multiuser session

`ctrl + a`  `:acladd $USER`

now that you have added a user, that user just needs to use the following command to attach to your session and share it with you. Let's assume we are still using the "teamwork" session name. 
One thing that must be made clear here is that the user will need to join ON you, which is to say that they will not reference themselves in the following command, instead, they will join YOUR screen session.

for the command below to make sense, let's make some things up to understand

`moon` = user who CREATED the session, added multiuser, and added `sun` as a user allowed to connect to the session

`sun` = the second user who is joining `moon`'s screen session named "teamwork"

with that out of the way, here is the command that `sun` will run

```bash
sun@server:~$ screen -x moon/teamwork
```
You need to use `-x` because it is the operator used when a session is already attached and you are joining in on it. A more official explanation is of course available on the GNU manpage


On some systems this will work, however on others you will be met with the following output

```
sun@server:~$ screen -x moon/teamwork
Must run suid root for multiuser support.
```

Pretty much what this means is that you need to set the SUID bit for the screen binary. an [SUID](https://www.redhat.com/sysadmin/suid-sgid-sticky-bit) bit being set basically means that the program will always run as the user who owns it, no matter what user invokes the program (in this case root owns screen) for this case, root will always run screen so multiuser support is enabled. Keep in mind that the SUID bit is a dangerous thing, and is often not set on purpose. 
The following is how we enable our multiuser support

First, figure out which screen you are using

```bash
which screen
/usr/bin/screen
```

```bash
chmod u+s /usr/bin/screen

chmod 755 /var/run/screen
```

I found that when doing this, if I did not add all permissions to the owner and read-execute for groups and others that new sessions would cause problems.

You should now be able to connect to other users sessions, which brings in another issue if you are working collaboratively inside of a screen session and that is...


### writelock

this is a pretty simple concept within what now must seem like the not so simple `screen` utility! 
Essentially, writelock means that the first person who is in the window gets exclusive permission to write in that window until they leave (this is the auto setting). You can see how this could become an issue if you are working together with someone and they need to type something.
In order to get rid of your writelock, issue the following keystrokes

`ctrl + a `  `:writelock off` 

Now the other person is able to assume their typing as needed.

interestingly enough, setting `writelock on` will lock the input even if a user leaves the window

