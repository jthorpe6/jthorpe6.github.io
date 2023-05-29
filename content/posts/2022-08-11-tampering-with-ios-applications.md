---
layout: post
title:  "Tampering with iOS Applications"
date:   2022-08-11 22:00:00 +0000
---

On mobile assessments, we often report the "No Anti-Tampering" finding, but Id's like to explore this a bit, and maybe show you a different technique to do this.

A lot of the time, is show by attaching Frida to the application, which is fine, assuming that the reader knows how Fida/Objection work under the hood. But let’s assume the reader does not know how Frida works and they only have our finding to go off.

I think it would be clearer for the reader to see that the application is being debugged, and it’s actually quite easy to show this.

![debugging application](/images/2022-08-11-tampering-with-ios-applications/debugging.png)

You'll need a jailbroken device, and the ability to `ssh` into it. Once you've got that, you can install the debugserver with `apt`.

![install debugserver](/images/2022-08-11-tampering-with-ios-applications/install-debug.png)

The `debugserver` allows us to debug an application on the iPhone remotely. In the above example I chose to debug SpringBoard, but that could be replaced with the clients application.

To debug the target application, you first what to have the application launched on the device, you can confirm this by running `ps -ef | grep <name of app>` on the device.

![ps -ef](/images/2022-08-11-tampering-with-ios-applications/ps.png)

You next want to run the `debugserver` specifying the ip and port to connect to and the target application. For example:
limit this to a specific IP address, or use `iproxy` to forward this `debugserver-10 0.0.0.0:6666 -a SpringBoard` You may want to over USB.

![debugserver](/images/2022-08-11-tampering-with-ios-applications/debugserver.png)

In a new terminal window, we now want to connect to the debugserver . I’ll be using `lldb` but I think you can do it with
the IP address of the iPhone and the port you told debugserver gdb too. Inside `lldb` issue the process connect command with to listen on in the previous step, for example: `process connect connect://192.168.0.92:6666`

![debugserver](/images/2022-08-11-tampering-with-ios-applications/lldb.png)

We are now debugging and tampering with the application, on the iPhone.
