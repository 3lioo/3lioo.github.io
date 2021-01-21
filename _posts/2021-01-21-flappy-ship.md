---
title: "Flappy ship challenge (Syberctf writup)"
layout: post
categories: writups
---
## Challenge name : Flappy ship
## Description :  
Have fun

and share your fun! :-)

Controls: up, down, right, left

[Download link][download-link]  (20MB)




Actualy i didn't solve this cahallenge at the ctf runing time but after 3 monthes i do it adn solve, It turned out to be easy to solve, but I was making it more complicated than it is

## Solution :
After downloading the challenge files, you can tell that you have to hack the game score to get the flag and when you say game hacking  [Cheat Engine][weki-cheat] comes to your mind.

P.S : during ctf time i wasted more than hour just playing the game.

First step is to know what score we have to reach to get the flag so after doing simple strings extraction we can notice interesting string ("Score 0xab56")


![](/assets/images/flappy-ship/00.png?raw=true)

we need to make your score = 0xab56 (43862 in dec) but when you make it equal to this number nothing happens 
actually this made me give up on trying solve the challenge during ctf running time but now i can see what was wrong 
simply when you cahnge the value using cheat engine it will not change until you hit an asteroid by your rocket so after we hit the asteroid it will be 43862 + 1 = 43863 and this will not show you the flag 
we should make it 43861 and hit astroid to get a score = 43861 + 1 = 43862 and this will give us the flag 
sadly this will give us half of the flag (when you hacke the score you will get a printed message "flag{you_got_ban")
to get the second half we have to hack the lives counter now (a hint from the author of the challenge )
but we have no clue what the value should be to give us the rest of the flag 
we can do a little trick  with cheat engine 
we can search for the lives address then find what access to it

![](/assets/images/flappy-ship/01.png)

the instruction at 0040442A seems interesting, when we inspect it by clicking more information button 

![](/assets/images/flappy-ship/03.png)

{% highlight asm %}
cmp [rcx+10],000056AB
{% endhighlight %}

it compares the lives number with the value (0x56ab in dec 22187)
and again when we change it we have to change the value to 22188 so when the asteroid hits our ship 22188 -1 = 22187 and this will print the second half of flag 

![](/assets/images/flappy-ship/04.png)

### Useful resources :

[Cheat engine tutorial guide][ref1]

[Introduction to Game Hacking!][ref2]


[download-link]: https://drive.google.com/file/d/1w6QR70c2UwU6wtj-p8uq98usoJ4JQBkJ/view?usp=sharing
[weki-cheat]: https://en.wikipedia.org/wiki/Cheat_Engine

[ref1]: https://wiki.cheatengine.org/index.php?title=Tutorials:Cheat_Engine_Tutorial_Guide_x64
[ref2]: https://medium.com/ax1al/introduction-to-game-hacking-fb70e29de60f
