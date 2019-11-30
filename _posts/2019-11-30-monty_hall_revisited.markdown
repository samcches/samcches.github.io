---
layout: post
title:      "Monty Hall Revisited"
date:       2019-11-30 21:56:54 +0000
permalink:  monty_hall_revisited
---


Anyone who's ever taken a statistics class knows of the Monty Hall problem--a theoretical game in which there are three doors: behind one is a car, behind the other two are goats. 

Monty, the host, asks you to choose which door you believe the car to be behind. After which, he opens one of the other two. Now you are given a choice--if you have doubts about your door, you can swap.

Statisticians will tell you that you would be better off switching your door now, and in most cases they would be right. But first, we have a few stipulations.

1. We are only better off changing if we are left with more than two doors. In the original example, we start off with three doors. After the first door is revealed to be a goat, the chance of our door being a car increases from 1/3 (0.333) to 1/2 (0.50), as we are now left with one goat and one car. (The first goat might as well be in the Bermuda Triangle for our purposes. It's no longer a valid choice, and therefore irrelevant to our calculations. And remember, the object is to find the door with the car.) As fifty is equal to fifty, we would be no betteror worse for changing our door now.

![img]https://i.imgur.com/0ryUAz0.jpg[/img

2. If we began with more than three doors, and the first door open proves to be a goat, we will, in fact, increase our chance of getting the car by swapping doors. 

While the chance of us being wrong decreases and the chance of us being right increases as the revealed door is eliminated, there is still a greater probability that the car will be behind one of the other doors, and that we will increase our likelihood of winning the car by switching our door.

### A Note on Intuition
Many statisticians and other hyper-logical minds will talk about intuition as if it is either some primitive beast that must be conquered or a mythical illusion only indulged by the delusional. However, your intuitive impulse to remain with your chosen door is really your conviction in your own decisions backed up by the fact that by eliminating one of the cards, your chance of being right increases (0.25 --> 0.33) and your chance of being wrong decreases (0.75 --> 0.67). While you might not consciously be thinking about it that way, the marvelous computer that is your brain is certainly processing that information. 

The secret is neither to beat down and ignore your intuition, nor give in to it without reason. We are all analysts in some form. We gauge the accuracy of the weather report when getting dressed in the morning and the chance that we'll be late given the excess traffic when a subway line malfunctions. The trick, as every good data scientist knows, is to add more data, and use memories of past experiences (in the everyday examples), which often gets processed as a "gut feeling". Use the information your intuition gives you to complete the picture and improve your chances. Just make sure that it's not the only tool that you use.  
