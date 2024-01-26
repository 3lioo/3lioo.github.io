---
title: Phishing Case
layout: post
categories: Phishing
---


## Introduction

In this post I'll go through interesting URL submitted I saw on the any run public submission.

## Hunting the source

hxxps://pub-e03b84ede40949d783b7ef41e8b36c50[.]r2[.]dev/web.html#Mahran.Sheikh@syriatel[.]com.sy

when visiting the URL it looks like an outlook login page with prefilled email address Mahran.Sheikh@syriatel[.]com[.]sy which is written in the URL after the [#] character (I think this email received the spam email that leads to the phish site).
The view source is disabled so I inspect the HTTP request using burp after refreshing the page.


![](/assets/images/phishing_case/phish_page.png)
<p style="text-align: center;">Outlook phishing page</p> 



![](/assets/images/phishing_case/requsted_js_files.png)
<div align=center>The source of the phishing page</div>

Now we have tow js files to analyze 
### First one:

The first script is responsible for creating the html template for the phishing page

![](/assets/images/phishing_case/first_js.png)

<div align="center">The first obfuscated js file</div>

after deobfuscating the file it uses the [unescape](https://www.w3schools.com/jsref/jsref_unescape.asp) function to generate html page

![](/assets/images/phishing_case/debof_first_js.png)

 

![](assets/images/phishing_case/first_html.png)

<div align="center">The generated html page</div>

### Second file

After deobfuscating the second file It appears to be the js file which is responsible for sending the entered credentials to the attacker.

![](/assets/images/phishing_case/second_js.png)

<div align="center">The sending part of code </div>

It sends a post request after using the [atob](https://developer.mozilla.org/en-US/docs/Web/API/atob) function to decode the base64 encoded URL. 
The decoded URL is hxxps://gsenddata[.]com/web/dropbox[.]php


![](/assets/images/phishing_case/reciv1.png)

The domain is hosting additional files that looks like serving the same purpose.

![](/assets/images/phishing_case/addfile2.png)
![](/assets/images/phishing_case/addfiles1.png)

## Back to start point

Using some google search and parsing any trace for using the same domain that hosts the js files hxxps://eu.starton-ipfs[.]com I found an interesting file hxxps://eu.starton-ipfs[.]com/ipfs//bafybeiffgj723rrq4ejwm7iydforlu2gu4xaog6xhhf2knoyk4ktt53y4y
![](/assets/images/phishing_case/int.png)

by scrolling  to end of the file we can assume that it writes the em variable to the document 

![](/assets/images/phishing_case/intdown.png)

saving this js file and debugging it to extract the string stored in em variable resulting a new html code  

![](/assets/images/phishing_case/newhtml.png)
The generated html file seems to be an old phishing page, and by viewing its source there is a using of similar method to the one we saw before, It also stores the base64 encoded URL (in a variable named file) then sends the entered credentials to the URL after decoding it.


![](/assets/images/phishing_case/inthtml1.png)
![](/assets/images/phishing_case/inthtml2.png)

The decoded URL is hxxps://lslamic-relief[.]org/Irworldwide/oba[.]php which is down in the time of writing this post.

### Image of interest

While trying to search for any other clues in the generated html file I came across this weird PNG link for the background image 

![](/assets/images/phishing_case/intimg.png)


the image is hosted on hxxps://swatantraindialive7[.]com/wp-includes/  which means it probably compromised by the threat actor

![](/assets/images/phishing_case/imgopen.png)

back to the PNG file and looking through the web it looks like this PNG file is used by a lot of similar phishing pages and seems to be linked to a phish-kit, searching for the PNG hash using urlscan.io results thousands of phish sites 

![](/assets/images/phishing_case/imgsearch1.png)

so I decoded the base64 image which have been used in the generated html file by the first js file and searched  for its hash using urlscan.io and got thousands of phishing sites similar to the one we had (a lot of this sites are hosted by Cloudflare Pages.dev and Workers.dev domains ).

![](/assets/images/phishing_case/imgcontainer.png)
<div align="center">The base64 PNG image </div>



![](/assets/images/phishing_case/resultcloud.png)

<div align="center">The results of searching for the base64 image </div>

## IOC

| Type | Indicator | Description |
| :--- | :--: | :--: |
| URL | hxxps://pub-e03b84ede4094hxxps://pub-e03b84ede40949d783b7ef41e8b36c50[.]r2[.]dev/web.html | Outlook login phishing site |
| URL | hxxps://eu.starton-ipfs[.]com/ipfs/bafybeicnzy4miwpnysban6yu5taxmsxjslw4nhmsykbfr6k6prqeohdgyi | JS file that loads the html file (first one) |
| URL | hxxps://eu.starton-ipfs[.]com/ipfs/bafkreifwfl4267c7mwuq4yfvedvjgulgmwmk5tveeik6wqjnb7bwunf2ki | JS file that sends the credentials (second file) |
| URL | hxxps://gsenddata[.]com | Domain hosting the php files for receiving the data |
| URL | hxxps://eu.starton-ipfs[.]com/ipfs//bafybeiffgj723rrq4ejwm7iydforlu2gu4xaog6xhhf2knoyk4ktt53y4y | JS file generates an old phish site |
| URL | hxxps://lslamic-relief[.]org/Irworldwide/oba[.]php | Receiver php file (domain is dead now) |
| URL | hxxps://swatantraindialive7[.]com/wp-includes/ | Open Dir hosting the background PNG file |
| SHA-256 | cdff0a47d3bb27e0015ed5332bb2614a5cc8ff8879b9469b531f18fb9dbc9822 | PNG file (background one) |
| SHA-256 | d9ed6586942003696afe4e52b09f343f8342244b51a9e175b75162d7e615207b | PNG file (base64 one) |


## References

- [https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/its-raining-phish-and-scams-how-cloudflare-pages-dev-and-workers-dev-domains-get-abused/](https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/its-raining-phish-and-scams-how-cloudflare-pages-dev-and-workers-dev-domains-get-abused/)
- [https://obf-io.deobfuscate.io/](https://obf-io.deobfuscate.io/)