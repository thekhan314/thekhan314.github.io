---
layout: post
title:      "The Wonder and Mystery of the Natural Logarithm."
date:       2019-12-06 00:09:22 -0500
permalink:  normalizing_with_the_natural_log
---


As I worked through the Module 1 lessons and Module 1 project, something struck me as interesting: the recommended technique for transforming features to make them more normally distributed. The lessons say to apply a logarithm function and show us how to do so. Since it's been a while since I had been exposed to the math, I thought I'd take a deeper dive and see how log normalization works behind the scene. 

So here is a refresher. When we raise a number to an exponent, we multiply it by itself that many times. So 10 raised to the power 2 is 10 x 10 = 100. This process has three parts: the number we want to raise (10),the number we want to raise it by i.e. the exponent(2), and the result of raising it by that much(100). Lets assign variable to each of these: x = 10, a=2,y=100. So to rewrite the equation:

  a
x   = y

The logarithm is essentially the reverse of this process, i.e. it is the "inverse function to exponentation". as per wikipedia. It is the answer to the question "to what exponent must we raise a given number to arrive at a second number"? Or to put it in terms of our variables, y is arrived at by raising x to what value of a? Traditionaly, this calculation is written as:


log   y = a
      x
			
How does this help us normalize our data sets? Well lets take a look at the 'price' data in the King county dataset. If you arent familiar, this basically is a list of the price of around 21,000 homes in King County, Washington, where Seattle is located.  The median price of a house is around 450,000. But on the upper end of the spectrum, we have houses that are as high as 7,700,000! This is fairly typical in the real estate markets.:The vast majority of houses fall within a pretty standard range butwe always have the super expensive mansions that lie towards the upper end of the scale, and they can get pretty up there. Bear in mind, our dataset has information on houses in Medina, a zipcode in King county called home by the likes of Jeff Bezos and Bill Gates. 

This wild difference in magnitude tends to degrade the quality of our regression models. Co-efficients are calculated funny when they try to account for these dramatically outlying values. To solve this problem, we want to try to reduce the magnitude of the deviation of the outlying values from the mean and median. 

The best way to do this is to use the natural logarithm of the values. Which is to say that instead of using the price of the house, we use the exponent to which the constant (e) must be raised in order to arrive at the number that is the actual price of the house is. We will talk more about 'e' in a bit, but basically e is roughly 2.718. So basically we ask for each house " to what power must we raise 2.781 in order to get the price of the house"? We then replace the price with the natural log of the price. 

Why do we do this? Lets look at some actual examples. 

The median price like we said was 450,000. The natural log of this is roughly 13.02, which is to say that we can calculate the mean price of a house by raising 2.718 to 13.02. i.e:

             13.02
2.718           = 540,000

Now lets get the natural log of the most expensive house. This turns out to be: 15.85 i.e

              15.85
3.2.718           = 7,700,000

You see what happened? Even though the maximum price is almost 14 times higher than the median price, however, the logarithm is only higher by 2 points!

We now start to see the reason why we replace the actual values with their natural logs. You see, raising the base e by even a slightly higher value raises the result by much more. In fact the result gets more and more sensitive and the ouput gets geometrically higher. Which is why when we use the log of a high number, it varies less to the log of a lower number than the actual numbers themselves. 

To see what I mean, take a look at this chart. It plots positive integers on the x axis and their natural logarithms (remember, the power to which we must raise e to get the number of on the x axis) on the y axis. 


As you can see, as x gets higher, y increases more and more slowly. so larger and larger changes in x translate into smaller and smaller changes in y. This why using natural logarithms of values instead of the values themselves basically penalizes larger values and reduces their significance in proportional way. 

Note that using the natural log of values to normalize a distribution is best used in cases where the original values have a distribution that is skewed to the right i.e. it has a long "tail" to the right. Home prices are an excellent example of such a distribution because most houses vary around the median but theres always super expensive houses because the richer people get the fancier houses they want. When we have such a scenario where there are huge values on the right side of distribution, the natural log of those values will reduce their impact on the distribution and be more symetrical and proportionate. 

Ofcourse one question remains. Why do we use the base 'e' to calculate the log? 

Good question. 

We could use a base of 10 or 2; and those bases are also commonly used in mathematics. But the constant e....has a special place in math. It's kind of a magical, mystical number that pops up from time to time throughout mathematics. It's right up there with other famous mathematical constants like Pi and the golden ration. 

Basically, the constant e is the limit approached by the following expression:

                n
(1 + 1/n)

As n approached infinity, this expression approaches e. 

This magical number was discovered by a gentleman named Jacob Bernoulli. If you are a data scientist, that name might be familiar (and if it isnt, trust me it will be). Back in the day Ol' JakeyB was rollin with the early enlightenment mathematicians in the early days of the calculus scene. He was homeboys with Leibniz, whose side he took in his beef with Isaac Newton. 

One day, Bernoulli decided he needed to figure out how to charge the right vig on some of action. (i.e. a calculate the growth of a loan amount that was subject to compound interest). 

Lets say I loan someone $1 and I charge them 100% interest per year. If I calculate the interest at the end of the year, then the interest is 100% of $1 i.e $1. 

What if I decide to charge the interest twice a year, and if its not paid I tack it on to the principle?  So lets say after six months, I decide to calculate how much interest I have earned on the $1 loan. Since I get 100% interest aftes one year, I'll only get 50% after 6 months, i.e. 50c fo interest. 

Lets say the the borrower cant pay me the interest, so I decide to start charging interest on the unpaid interest. So after six months the borrows owers me $1.50 and this is the new balance. The six months after that, this balance will earn another 50% in interest. 50% of 1.5 - 75 cents. Add this back to the total owed after the first 6 months and at the end of the year, I am owed $2.25

What if I decide to charge interest every week? Every day? As Bernouli did the math, he realized that as the number of periods for which the interest was computer got smaller and more numerous, the amount owed at the end of the year seemed to reach a limit that it never exceeded. 

That limit happens to be the constant 'e'.

The number e is pervasive in mathematics and nature. One of its most interesting properties is that the derivative of the equation 
   
	        x
y = e
                    x
is also    e    .

This makes calculations involving the derivative much simpler, which is why it is favored as a base for logarithmic functions. 

E will also come up in the study of statistics, specifically in Bernoulli trial processes (go figure). 

Here is a video showing a bunch of weird and interesting properties of e. 

The constant e is used to plot something known as the logarithmic spiral in a polar co-ordinate system. The size of the spiral increases but its shape is unaltered with each successive curve, a property known as self-similarity. 

The logarithimic spiral is ubiquitous in nature. Galaxies take the shape of logarithmic spirals, as do the shells of nautiluses, the nerves of the cornea, the arranement of seeds in a sunflower, the segments of a pine cone, the horns of a ram, the arrangement of hair follicles on the human head,cycones and hurricanes, the unfurling of new fern leaves.......

E seems to be hard coded into nature itself. Hence..."natural log". I dont know about you but I now kind of have the chills. So did Bernoulli. He was thoroughly fascinated by the logarthmic spiral. He called it "the marvelous spiral", Who wouldnt, once they realized they had stumbled on a pattern so deeply and pervasively etched into the very source code of nature? In fact, he was so obsessed with it that he left instructions for a logarithmic spiral to be carved on his tombstone after he died. 

They messed up and carved a regular Archimedean spiral instead. 









 



			



