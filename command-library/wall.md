# wall

`wall` is a utility that allows you to send a message to all users that are currently logged in on the system. While this utility found quite a bit more use back in the day when many users were often logged on to a machine to do various computations and other tasks, this still find its use these days. A good example would be an SSH Jump Box. 
It is typically used to announce maintenance, or to let users know the state of the box if something wonky is going on

More recently I used `wall` to tell a coworker who was logged in to the same box as I was that we were going to lunch at 11:30 to which he used the utility to say "ok I want Panda Express"
If that alone does not show the value of this utility, then I don't know what will

[geeksforgeeks - wall](https://www.geeksforgeeks.org/wall-command-in-linux-with-examples/)  
[phoenixnap - wall](https://phoenixnap.com/kb/linux-wall)  
[linuxize - wall](https://linuxize.com/post/wall-command-in-linux/)

________

## Using wall

### Standard Use

The typical syntax for a `wall` use is 
```bash
wall [OPERATORS] [FILE|MESSAGE]
```

As you can see, you could use a file as the message input, or you could simply write your message after the command invocation, just like you would with `echo` the utility will read from standard input

Here is what a `wall` message looks like with just standard input

```bash
wall hello all users, the system will be rebooted @ 10:30
```

If you would like your message to have multiple lines, you can either invoke `wall` with a file as the input, or you can still use standard input. just invoke the utility with no arguments and end your input with `ctrl + d`

```bash
wall
hello
the system will be rebooted
thank you !
```

_______

### Using wall with Operators

wall is quite the simple utility and really only has a few operators available to it, however don't underestimate it!

#### Timed Delivery

one of the disadvantages of using no operators with the utility is that it will not be displayed to users who log in after it is broadcasted. Like anything in Information Technology, important and unfortunate things will always happen at the worst time. someone could make a change to a configuration but then the system is rebooted because they missed a `wall` message from the administrator.
In order to avoid situations like this, you can set a timer for the message to be delivered to fresh-login users

here is the syntax for how you would deliver a timed delivery message

```bash
wall -t <timeInSeconds> <messageContents>
```

so for instance if I would like all users that login for the next 30 minutes to know that I will be rebooting the server at 11:00 I will run something like the following

```bash
wall -t 1800 "The system will be rebooted at 11:00. Please save all work"
```


#### Send a Message to a Specific Group

Much like when you were in school and the announcements came on, no matter what you stopped what you were doing and listened. `wall` is kind of like that announcement. Maybe you don't want to announce something to everyone and interrupt what they are doing. Perhaps your information is only relevant to a certain group of people. that's with the `-g` operator is for! It is quite simple to use

```bash
wall -g webdevs "hello, the new version of Tomcat will be installed at 10:30"
```
