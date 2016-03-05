# web server part     
The Android part is in https://github.com/chinapumpkin/MobileserviceTool
## how to use   

###prerequisites
* A fresh install of a Wowza Media Server. This tutorial won't cover the installation of the Wowza Media Server, but don't worry, folks at Wowza have written pretty detailled [tutorials about that.](https://www.wowza.com/forums/content.php?217-How-to-install-and-configure-Wowza-Streaming-Engine&s=2250aea939a922907b8a7ca2f2d683b3)    
* An HTTP server like Apache 2, it will serve a page containing the flash player that will play the stream.        
* Those two servers should obviously be accessible from the web, and could be running on the same computer.     

###  how to configurate the Wowza Media Server

First, we need to create a Wowza *application* that we will call **live**. We are actually going to install one of the sample applications presented [here](http://www.wowza.com/forums/content.php?38). To do that, **start by creating a folder called "live" in your [wowza-install-dir]/applications folder**:

```bash
cd /usr/local/WowzaMediaServer
sudo mkdir applications/live
```

And another directory called **live** in your **[wowza-install-dir]/conf/** directory:

```bash
cd /usr/local/WowzaMediaServer/conf/
sudo mkdir live
```


Then, download and copy [this file](http://www.wowza.com/downloads/tutorials/live/Application.xml) in the "live" directory. On most Linux distributions, you can simply do that:

```bash
cd live
sudo wget http://www.wowza.com/downloads/tutorials/live/Application.xml
```

*Note: if you have configured Wowza to not run as root (and you should have), make sure that the live folder and the `Application.xml` file are accessible from the proper user.*

### 2 - Set a password for the RTSP server of Wowza

The RTSP server of Wowza can be configured to require user authentication. By default, it is configured to require user authentication to publish a stream, but not to play one. The example 3 will need this login and password pair to successfully stream to Wowza. That is why you have to add one in the [install-dir]/conf/admin.password file.

You can also choose to disable user authentication to publish a stream. To do that, replace **digest** with **none** in your `live/Application.xml` in this field:

```xml
<PublishMethod>digest</PublishMethod>
```

**Note: as of libstreaming 3.0, digest authentication is the only authentication scheme supported by the RTSP client.**

### 3 - Restart your Wowza Media Server

Finally, you need to restart Wowza.

On Ubuntu/Debian:

```bash
sudo service WowzaMediaServer restart
```

[Find out how to start/stop your WowzaMediaServer here](http://www.wowza.com/forums/content.php?217#startService)

## Step 3 - Streaming to the Wowza Media Server

Start the app called "example3" on your phone. Normally, you should see the video from the front camera of the phone as soon as the app starts. 
Fill the fields login and password with the username and password you chose earlier in the `publish.password` file.
Then fill the URI field with something like "rtsp://public-ip-of-the-wowza-server:1935/live/test".
Finally, press "start". The form will be saved in the SharedPreferences of the app, so you won't have to fill it anymore.

**If you see an error saying "failed to connect to blablabla", it means that your Wowza Media Server is not visible from your phone**. Here are some things you could try to find the issue:

* Check that you can ping the IP of your Wowza Media Server from your phone. The command `ping` exists on Android, all you have to do is to install [this app](https://play.google.com/store/apps/details?id=jackpal.androidterm) for example, and write that in the terminal:

```bash
ping ip-address-of-wowza
```

If that doesn't work, you may have connection issues, firewall issues or NAT issues.

* Check the firewall configuration of the server that hosts Wowza: it should be configured to allow incoming TCP connections to the port 1935 which is the port used by default by Wowza (if you changed it, then of course you need to configure your firefall accordingly). **Incoming connection from the outside is something that a computer running Windows may forbid by default, so be carefull if you use Windows.**

* You will find plenty of useful logs in `logcat`.

## Step 4 - Reception of the stream in the flash player

### 1 - Install the demo site on the HTTP server

First, copy the "web" directory in libstreaming-examples/example3/ in a directory accessible from the HTTP server. On most distribution the root directory served by Apache 2 is **/var/www**. This is where you will want to copy the "web" directory.

 **Some remarks:**

 * This HTTP server can be running on any computer as long as the Wowza Media Server is visible from it, it does not have to run on the same computer as Wowza. 

 * The flash player embedded in the index.htm page will open a TCP connection to the RTMP server of Wowza, that means that it will be able to receive the stream even if the browser is behind a NAT.

### 2 - Browse the demo site

Visit the /web/index.htm page of your HTTP server with your favorite browser, and in the field below the flash player write exactly the same URI as the one you chose in the example 3, but replace **rtsp** with **rtmp**. 

Finally, press the "Start" button, you should then see/hear the stream from your phone.
