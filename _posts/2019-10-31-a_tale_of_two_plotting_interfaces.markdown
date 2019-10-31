---
layout: post
title:      "A Tale of Two Plotting Interfaces"
date:       2019-10-31 20:19:05 +0000
permalink:  a_tale_of_two_plotting_interfaces
---


As I was working through Module 1, Section 6 of the Data Science track (i.e. the section on visualization), I was struck my something I found peculiar. 

There seems to be two ways to do essentially the same thing when it comes to plotting data and specifically making subplots using matplotlib.

There is the plt.plot() method and the plt.subplot() method

Then there is ax.plot() method and the plt.subplots() method.

Why do we have two ways of doing basically the same thing? I had to find out. So I went digging. It turns out that the reason for these different cat-skinning methods had to do in part with the historical development of matplotlib. 

Jake VanderPlass explains in "The Data Science Handbook" that matplotlib came about as a means of enabling MATLAB style visualzations in IPython (the predecessor the Jupyter).  These original MATLAB style tools are contained in the pyplot (plt) interface. This originial interface is "stateful" i.e. it keeps track of the "current" figure and axes, to which all plt commands are applied. However, as VanderPlas explains:

"While this stateful interface is fast and convenient for simple plots, it is easy to run
into problems. For example, once the second panel is created, how can we go back
and add something to the first? This is possible within the MATLAB-style interface,
but a bit clunky. Fortunately, there is a better way."

It seems that better way is to use the second interface, the object oriented one. In this interface, there is no "active" figure or axes. There are specific figure and axes objects, and we plot data onto these by explicitly calling the plot method on the specific object we are interested in. 

For example, using the old plt method, if we wanted to add axes we would do it so:

```
ax1 = plt.axes()
ax2 = plt.axes([[co-rdinates here]) # This will have co-ordinates for the second axes
```

This will add two axes to the current and active figure. If we wanted to plot a whole new figure, we would end up losing this one. 

To do the same thing in the object oriented interface:

```
fig = plt.figure()
ax1 = fig.add_axes([co-rdinates here])
ax2 = fig.add_axes([co-rdinates here])
```

Now we have this two axes contained in a separate figure object we have named 'fig'. We can create a new figure object called fig2 and store a completely different set of axes in there.

Similarly, subplots work differently using the plt interface and the object oriented interface. 

With plt we have to add each subplot one by one using the following method:

`plt.subplot(rows,columns,subplotnumber)`

To generate a whole series of subplots we would need to wrap this in some kind of loop. With the object oriented interface, we can we can predefine our subplots all in one go. 

`fig, ax = plt.subplots(nrows,ncols)`


This returns two objects: a figure object and either one axes object or an array holding a collection of axes objects if we pass in nrow and ncol paraeters creating more than one plot. Once these objects have been captured by the variables we use, we can then easily refer to them specifically when needed instead of worrying what is showing on our "current" figure. This allows for flexibility and portability. We can also refer to a specific set of axes by using the relevant index number of the list containing the axes objects.

Under the object oriented paradigm, in order to plot data or modify the plots, we essentially call the same methods we would ordinarily call on` plt` on `ax` (the axes object) instead. There are however, some differences in syntax. For example:

• plt.xlabel() → ax.set_xlabel()
• plt.ylabel() → ax.set_ylabel()
• plt.xlim() → ax.set_xlim()
• plt.ylim() → ax.set_ylim()
• plt.title() → ax.set_title()

In addition, the object oriented plt.subplots() method allows us to easily set a common x and y axis for the subplots by using the sharex and sharey parameters in the plt.subplots() method. This allows for cleaner looking and less cluttered subplots. 

`fig, ax = plt.subplots(2, 3, sharex='col', sharey='row')`

VanderPlass seems to indicate that the object oriented method is the way to go. That also seems to be the general consensus at stackoverflow and the internet large. The question then is, when, if ever should we use the old plt interface? What are the use cases? is it simply a vestigial holdover from another era? It hasnt been deprecated...does that mean it still serves some use? Is it purely to support development and maintainance of legacy code? Or does it offer unique benefits over the object oriented interface? I cant say for sure. Seems like i must, for the time being, be content with just a partial satsifaction of my curiosity. 

On the bright side, it leaves another question for us to explore tomorrow. 

For a more detailed discussion, definitely read the relevant sections from The Python Data Science Handbook, a book you should be reading in any case. Also helpful is this blog post by Kapil Mathur :https://medium.com/@kapil.mathur1987/matplotlib-an-introduction-to-its-object-oriented-interface-a318b1530aed






