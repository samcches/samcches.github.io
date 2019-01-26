---
layout: post
title:      "Why is This House So Expensive?!"
date:       2019-01-25 18:21:40 -0500
permalink:  why_is_this_house_so_expensive
---

## Variables Influencing House Price and How to Use a Heatmap to Judge Relationships

If you've ever lived in a city like New York or San Francisco, you've probably been bowled over by the sheer vulgarity of how expensive an apartment the size of a broom cupboard can be. Which means, you've probably taken a moment to ask yourself, "Why is it so expensive?"

For the two examples above, one can argue that it's a classic case of supply and demand. As the number of people who want to live in these cities grows, the number of available dwellings will decrease, driving up price. However, this would drive up the house price of all the houses in the city (when compared to houses outside the city) and isn’t really helpful when trying to discover what makes one house more expensive than another.

Which leads us to the next piece of real estate jargon: "'Location, location, location." But is that necessarily true?


### The Analysis
To test this piece of conventional wisdom, I took a look at housing data from King County,  Washington, USA.

After tidying up the data, I used a heat map to visualize the relationships between the various data columns:

![](https://github.com/samcches/dsc-1-final-project-online-ds-sp-000/blob/master/output_22_1.png)

If you're like me, your eye jumps immediately towards the bright red positive correlations and the bold blue negative correlations—but let’s restrain ourselves for a moment (we are scientists-in-training, after all) and focus on our initial question: Is location the primary variable influencing price?

No. Actually, it isn’t. 

If we look at zip code, it seems to have a neutral if any relationship with sale price, the square demonstrating their relationship barely visible in our vibrant table. 

So what *does* influence the sale price of a house?

From the heat map alone, it looks as if grade, square feet of living space, and number of bathrooms all have a positive relationship with price. Year built and condition has a negative relationship as one would expect, but year built also has a negative relationship with zip code, implying that developers have had to expand their development sites as the city (and its population grows).

How can we tell that? Easy. The key on the side of the heat map indicates the darker shade of blue as strong negative correlations, while deep reds indicate strong positive correlations. 

### The Process
Heat maps are great for visualizing the relationships between variables, or if they exist. They are also one of the many useful features of the Python package, Seaborn. The code used for this diagram is below:

    sns.set(style="white")

    corr = data.corr()

    mask = np.zeros_like(corr, dtype=np.bool)
    mask[np.triu_indices_from(mask)] = True


    f, ax = plt.subplots(figsize=(11, 9))

    cmap = sns.diverging_palette(220, 10, as_cmap=True)

    sns.heatmap(corr, mask=mask, cmap=cmap, vmax=.3, center=0, square=True, linewidths=.5, cbar_kws= {"shrink": .5})


First, we import the package and give it its shorthand name. Then, a mask is applied to give its diagonal shape. If this is not important to you and you don’t mind a square matrix heat map, you don’t have to use it. 

Finally, the axis information is encoded. 

From here, we can go on to test any of the relationships that catch our eye from the heat map. It makes a great starting point for posing questions in a multivariable analysis, and helps prevent us from wasting valuable time (and processing power) pursuing variable that have no relationship to one another. 

### Next Steps:&#x2028;
If we’re unsure which questions we want to ask, test the most vibrantly-colored squares with a correlation algorithm:

    import math
    def mean_normalize(var):

        norm = []
        n = 0
        mean = np.mean(var)
    
        for i in var:
            diff = var[n] - mean
            norm.append(diff)
            n = n + 1
    
        return norm

    def correlation(var1,var2):
        if len(var1) != len(var2):
            return None
        else: 
            mean_norm_var1 = mean_normalize(var1)
            mean_norm_var2 = mean_normalize(var2)
        
             var1_dot_var2 = [a * b for a, b in list(zip(mean_norm_var1, mean_norm_var2))]
             var1_squared = [i * i for i in mean_norm_var1]
             var2_squared = [i * i for i in mean_norm_var2]
        
            return np.round(sum(var1_dot_var2) / math.sqrt(sum(var1_squared) * sum(var2_squared)), 2)
    

    correlation(data['price'], data['bathrooms'])

First, we import the math package. Then we define a function which normalizes the mean by setting ‘norm’ as an empty list, set ’n’ to zero, and call the mean for the variable. 

Next, we should define a correlation function that takes two variables that are the same length and normalizes each of the two variables using our previously defined mean_normalize(var) function.

The next part of our function will dot multiply two individual variables in each of our two columns of variables. The following two equations will square each individual variable in each of the variable columns. 
The last part of our final function is to round our final answer to two decimal points after performing each of the calculations.

From there, we can pass the independent (x) and dependent (y) variables into the function to see the rate of correlation, which will be between 0.00 and 1.00. It is preferable that a positive correlation is greater than 0.85, but realistically data is rarely that cooperative. If the correlation function returns 0.60 or greater, you have a decent footing from which you can proceed with your statistical analysis. 

### Final Thoughts:
To be fair to the marketing experts responsible for the phrase, “Location, location, location”, they were, in fact, referring to the distance from the house to desirable schools, shopping centers and points of entertainment, rather than zip codes. The distance from each house to such locations would be another analysis, one for which we currently don’t have data.

As data scientists, we sometimes have to think of ourselves as actors. To find out what questions to ask about the real estate market, we have to think of the motivations of each of the individuals involved--from the developers to the brokers to the customers. Similarly, when dealing with multivariable data sets, it can be useful to visualize relationships between data columns with a Seaborn heatmap. 

For more information on heatmaps, check out this [link](https://seaborn.pydata.org/generated/seaborn.heatmap.html)!
