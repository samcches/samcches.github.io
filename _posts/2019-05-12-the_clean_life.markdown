---
layout: post
title:      "The Clean Life"
date:       2019-05-12 12:30:28 -0400
permalink:  the_clean_life
---


Some people love to clean. Some people adore the sudsy warm water on their hands as they wash dishes. Some people find great refreshment in scrubbing down the bathtub until they can see their face in the gleaming white surface. 

I have never been one of those people. I prefer to operate inside a system of organized chaos, clothes hung up at best and strewn across the floor in relative piles of once-worn and offensively filthy at worst. I used to wash dishes as I needed them and only cleaned out the fridge when things began to mold—until now.

Whether my newfound love for cleaning is because I finally have a one-bedroom apartment to myself, a recent addiction to [The Grove](https://www.grove.co/referrer/48888912/), or my plunge into data science, I will never know. But as I excitedly opened my box of Mrs. Meyer’s cleaning supplies, I was forced to confront a new sensation. Was I looking forward to cleaning products that didn’t smell chemically-harsh for once? Or was I genuinely beginning to appreciate a new form of organization?
![Imgur](https://i.imgur.com/LxHsUGT.jpg)
Or, worst of all, had turning thirty actually changed my mentality so that I now valued tidiness as a core principle of my adult life? *(Cue dramatic crisis sound effects)*

When skeptics hear about analysts “cleaning data” their first thought may turn to corrupt governments and corporations changing existing data so as to fit their own purposes. While I’ll be the first to say that any organization with human beings is subject to at least a little corruption (stealing office supplies counts: you know who you are), data scientists are rarely the ones purposely misusing data—it simply goes against what we’ve been trained to do. 

When a data scientist cleans data, he or she is preparing the data for an algorithm by eliminating null values and reducing the effects of outliers, which would otherwise alter the general trend or result of the model. Because outliers will almost always exist in a natural data set, they must be identified and acknowledged as such, else the results will be skewed and not reflect a true representation.  If the data is inconsistently formatted, for example, it may prove impossible to use in calculations.

So how does one accomplish this?

### **Data Cleaning Techniques**

#### *Standarization*
Standardization is a technique commonly used in order to eliminated outliers and acheive a more accurate mean. To do this we subtract the mean from the data and divide it by the standard deviation as seen below.

![](https://i.imgur.com/apx6egU.jpg)

#### *Normalization*
Data is normalized in order eliminate negative values. It is done by subtracting the minimum data point from each individual data point and dividing that by the difference between the maximum point and minimum point as seen in the equation below.

![](https://i.imgur.com/7BwtKy8.jpg)

This prevents negative values in the data set and rescales the data to values between zero and one.

#### *Applying log()*
One of the most popular ways data scientists “clean” data is to use the mathematical log function to normalize the data. As the log function is the inverse of the exponent, it is helpful in condensing a set of data with a large range of values to a smaller, more manageable range.

#### *Reformatting*
Another, more common and time consuming way data scientists clean data is by making sure it is in the same format. For example, “30” and “thirty” will not be read the same way by the algorithm; nor will “John” and “john” unless reformatted. Spelling mistakes, improper/inconsistent data entry, and duplicate information are all the germy foes of the metaphorical-mop-wielding data scientist. They must be confronted, dealt with, and vanquished. 


The consequences of not cleaning one’s data is much the same as not cleaning your house: the data equivalent of penicillin growing in your orange juice—poor business decisions based on a faulty analysis that could have been avoided with a good tidying-up. So just as you would rinse off your plates after dinner for whichever person responsible for washing dishes, make sure your databases follow a standardized protocol for entering information. 

Current estimates dictate that approximately 80% of a data scientist's time is spent cleaning data, while only 20% is spent creating and testing a model, emphasizing the importance of working with clean data in the first place.
