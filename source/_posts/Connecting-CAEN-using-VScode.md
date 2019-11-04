---
title: Connecting CAEN using VScode
date: 2019-10-06 08:18:17
categories:
- Others
tags:
- SFTP
- Umich
---

### What and Why

CAEN is the information technology (IT) services department for the University of Michigan (U-M) College of Engineering, and offers IT resources to support the College’s educational, research, and administrative needs. It's quite unefficient to manage files on CAEN using command line tools if I need to text our code in CAEN environment. I need to type the whole sftp command and path every time. Plugins for editors is a great solution. There are many tutorials about connecting using Sublime Text Editor on the Internet, but there is no documentations about VScode. As a fan of VScode, that's why I want to write this article.

### Environment

This is my own running environment may but not necessary.

- Operating system: Windows 10
- VSCode Version: 1.36.1
- Plugin: SFTP (by liximomo)

<!--more -->
### Getting Start

After downloading the plugin, press `Ctrl+Shift+P` and run `SFTP: config` command. This command will build a configuration file named `sftp.json` on your folder (you may need to open a folder) and it may looks like:

```json
{
    "name": "My Server",
    "host": "localhost",
    "protocol": "sftp",
    "port": 22,
    "username": "username",
    "remotePath": "/",
    "uploadOnSave": true
}
```

Name your server casually in `name` and the type the host address of CAEN machine in `host`. The host will be something looked like `login.engin.umich.edu`. It's not necessary to change the `protocol` and `port`. The `username` is your Umich unique name.

Here is the explanation of these parameters.

- `name` is your own name of this server, you can name it casually.
- `host` is the host address of CAEN machine, like `login.engin.umich.edu`.
- `protocol` is the protocol of connection, you don't need to change the default `sftp`.
- `port` is the port of the connecting server.
- `username` is yout own Umich uniquename which is needed for signing in the server.
- `remotePath` is the path ***on the CEAN machine*** where will upload your local file to. For example `home/username`.
- `uploadOnSave` is the switch of autouploading to the server. If the value is true, the files will be automatically uploaded to your server when you save your files locally.

After all these settings are saved, you will see a new icon on the Activity Bar.

### Two-factor Authentication

The University of Michigan uses two-factor authentication to authenticate your account. So we need to add a new parameter to handle this. Add a new attribute `interactiveAuth` in the json file and set it to `true`. So the whole configuration file will looks like

```json
{
    "name": "My Server",
    "host": "xxx.xxx.xxx.xxx",
    "protocol": "sftp",
    "port": 22,
    "username": "username",
    "remotePath": "/home/username",
    "uploadOnSave": true,
    "interactiveAuth": true
}
```

### Connecting

Double click the server in the SFTP option on the activity bar with the following icon.

 ![sftp_icon](https://raw.githubusercontent.com/hongxin-y/picture4blog/master/sftp_icon.png?token=AKSH2LZDEWQACKGI3TC6FPK5ZCIZ2)

After connecting, you will see a input window above looks like

![password_window](https://raw.githubusercontent.com/hongxin-y/picture4blog/master/pwd_window.jpg?token=AKSH2L76E2MJD5QQOGJZDFS5ZCI26)

Then your will see a two-factor authentication window like

![duo_window](https://raw.githubusercontent.com/hongxin-y/picture4blog/master/duo_window.jpg?token=AKSH2L3O5PMR5WCNHFECIVS5ZCI4A)

input and pass the authentication through app or message, then you will see the dictionary of your server machine.

![dictionary](https://raw.githubusercontent.com/hongxin-y/picture4blog/master/dictionary.png?token=AKSH2L7OPAU4DDZQ3R4P3S25ZCI5A)

Mention that if you use address `"/"` in the `remotePath`, you will connect to the public area and will ***have not permission to open the private folders including yours***.
