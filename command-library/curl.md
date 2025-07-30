# curl

curl is an extremely useful and versatile data transfer tool. It supports many protocols and is often installed by default on many systems. More often than not, it is used for downloading files onto your local machine, and finds its place on servers where no GUI exists for a web browser. However, don't think that this means that it is only used for data download!

[DigitalOcean - curl](https://www.digitalocean.com/community/tutorials/workflow-downloading-files-curl)

[geeksforgeeks - curl](https://www.geeksforgeeks.org/curl-command-in-linux-with-examples/)

[curl documentation](https://curl.se/docs/tutorial.html)

______

this command can be typed as simply curl, or cURL, because that is what it allows you to do! "see" URL's! by default if you just run the curl command with no operators it will grab the file and display to stdout

because the command is so multipurpose, you must specify the protocol you will be using in your links, as you would with a browser of any kind. this involves protocols like
`ftp`  `sftp`  `https`

For the purposes of this documentation we will just use DigitalOcean and a few other reputable sites for our purposes

## Fetching Content

run the following command to grab `robots.txt` and display its contents to stdout in your terminal

```bash
curl https://www.digitalocean.com/robots.txt
```

here is what you will see (well, a small bit of it)
![example output](https://github.com/barkwoofdog/doclibrary/blob/77a449a8a3e4b3212bc4a113ae916e8b483a6748/images/curl-quickscreen.png)

All `curl` did was fetch the contents of `robots.txt` and displayed it. Think about how useful this command would be when browsing FTP site contents from a terminal !

It should be noted that `curl` is not limited to fetching the content of only one site. all you need to do is something like this

```bash
curl https://example.com/file1.txt https://example.com/file2.txt
```

This will show the contents of both `file1.txt` and `file2.txt`

of course, maybe you do not want to type all that out! If the filenames are numerically sequential, you could do something like this ! (let's say there are 5 files in total)

```bash
curl https://example.com/file[1-5].txt
```

That will grab all 5 files !

Grabbing files with keys and passwords will be covered in a bit.

_______

## Downloading Files

As stated previously, `curl` is very useful for downloading files from the terminal. you can download files from sources with the `-o` and `-O` operators on the command. Below we will display that.

using the `-o` operator we will simply download the file specified with a name that we give it to our current working directory

```bash
curl -o oneMB.zip http://ipv4.download.thinkbroadband.com/1MB.zip
```

this saves the files from the link we provided to `oneMB.zip` in our current directory. If you do not want to worry or do not care about the filename then you can use `-O` as it will save the file with the same name as the remote host.

```bash
curl -O http://ipv4.download.thinkbroadband.com/1MB.zip
```

this saves the files we grabbed as `1MB.zip` as it is named on the remote host.

If you are ever downloading a file and are interrupted, you can use the `-C` operator to resume your download!

```bash
curl -C -O http://ipv4.download.thinkbroadband.com/1GB.zip
```

_____

## Uploading Files

Uploading a file is just as easy as downloading one ! all you need is one operator which is `-T`
this allows you to upload files to remote hosts, and can be used in concert with other operators, such as `-u` for the use of credentials (which will be discussed in the next section)

Unlike downloading, this operator only works with the following protocols
FTP, SFTP, FTPS, SCP

Uploading files with HTTP POST will not be covered here, but you can find an example in the [official curl docs](https://curl.se/docs/tutorial.html)

First, let's simply upload a file to a remote host. I will not actually be uploading anything, but the syntax will be correct.

```bash
curl -T myUploadFile ftp://ftp.example.com/uploadedFile
```

You can specify what the filename will be on the remote host as you can see.
There is not too much to discuss with this operator. It is quite simple.

_____

## Using Authentication and Passwords

If you are ever working with a remote host that requires you to authenticate to it in order to upload/download you are going to need the `-u` flag which allows you to input a set of credentials, or use an SSH key via SCP. You can use authentication with HTTP as well. HTTPS typically will use certificates for this manner of upload/download

Using a username and password combo to download a file
```bash
curl -u dog:goodpassword -o passwordFile.txt ftp://ftp.dog.com/passwords.txt
```

Using an SSH key with SCP
```bash
curl -u dog: --key ~/.ssh/dogskey scp://example.com/dog/display.txt
```

Keep in mind with the above command that `dog` is being used in place of your own username.

You will use this same syntax with HTTP, and with Transferring files to the remote host

_______

## Bonus! Dictionary

the DICT protocol allows one to grab data from websites that offer their content with the DICT protocol. With this, you can quickly look up a word that you need the definition for. Personally, I find using a good ole web browser and a search engine to be the easiest way to find a word's definition, but maybe you are desperate!

I suggest reading up on this protocol on [wikipedia](https://www.wikiwand.com/en/DICT) because it is quite interesting. Little things like this are what make the internet great!

Here is how you would get the definition for the word "hello"
```bash
curl dict://dict.org/d:hello
```

You will receive output that looks like this
![dict](https://github.com/barkwoofdog/doclibrary/blob/139bffc83b37146b7f3c7d98b6173bc30c8c40df/images/dict.png)
