---
layout: post
title: My Experience with Fiddler Configuration
---

I was first introduced to [Fiddler](https://www.telerik.com/fiddler) back in October 2018, when I was assigned to rewrite the API call to speed up saving one of the components. Even with the help from the experienced developer, I was not able to configure a test iPad to the Fiddler server. **FINALLY** I have this resolved today, and now Fidder can track traffics on both the test iPad and my personal Android device. I'd like to summarize what I've done (despite numerous instructions already available online) from a newbie's standpoint; hopefully this would be beneficial to Fiddler newcomers (like I do).

## Step-by-Step instruction
1. Install Fiddler [here](https://www.telerik.com/download/fiddler).
2. To allow external devices to connect to the Fiddler server: Tools > Options > Connections > check "Allow remote computers to connect". Once checked, click "yes" in the popup window. You might need to restart Fiddler to save changes.
  * You may want to remember the port number there as well (default should be 8888).
3. On the top right corner, there should be a computer icon with text "Online". Hover your mouse over the text. You should be able to see the hostname and the IPv4 address at the very bottom. Take a note of the IP address.
4. Pull up a web browser on your device and visit **http://[IP address in step 3]:[port number in step 2]** (e.g. http://10.84.53.22:8888).
5. Once you receive a 200 response from the Fiddler Echo Service, continue based on your device types.
  * [Android](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureForAndroid). I would say install the HTTPS certificate is not required, but helpful.
  * [iOS](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureForiOS). Note that **you would have to manually enable full trust for the FiddlerRoot root certificate** or iOS won't be able to connect to the Internet.


## My Notes
* Make sure your device and the hosted computer are under the same network. I had the issue this morning as my test iPad was not able to talk to the Fiddler server, since the computer was connected through the Ethernet and the test iPad was under the guest Wifi. Once they were both under the staff Wifi, everything *magically* worked.
* If necessary, try to disable your firewall (so that the devices can at least connect to the Fiddler Echo Service). If you don't feel safe doing so, type "Firewall" in the Win 10 search box, open up Windows Defender Firewall, and click "Allow an app or feature through Windows Defender Firewall". Double check that Fiddler is allowed to access private and public traffics. (Admin access is required to change settings)
* *ALWAYS* turn off manual proxy setting when you're done testing! Otherwise if Fiddler server turns off, the device can't surf the Internet as usual.
* If you have any local server running, Android can just visit it via *https://localhost:[port number]*. There is no need to explicitly translate "localhost" to your device IP address. If a translation is needed, my guess is that you'll have to find local IP address (similar to 192.168.x.x) and use that instead of "localhost". This might not be always achievable as your network administrators might have turned off a functionality (I don't remember the exact term, something like cross-file access?). It's a bit challenging to connect to the localhost server through an iOS device; in fact I've not found a solution yet and ended up deploying my website to the cloud (which was painful as the deployment took at least 15 mins). My guess is that iOS may need to visit *https://[local ip address]:[port number]* instead.

 It's fun to use Fiddler to track network traffics and I'm still in the learning phase. Hopefully it will turn out to be a nice hacking tool for me :)
