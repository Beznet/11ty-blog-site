---
title: 'Building My First App: The Process'
date: 2018-06-23T20:56:31.791Z
summary: >-
  A ton of beginners believe that they need to learn an immense amount of code
  before they can ever build anything useful. I've felt like that before, hell I
  still get that feeling of extreme self-doubt...
tags:
  - coding
  - beginner
---
![coding_hands](/static/img/embed-javascript-in-weebly.jpg)

A ton of beginners believe that they need to learn an immense amount of code before they can ever build anything useful. I've felt like that before, hell I still get that feeling of extreme self-doubt but its less frequent than before. I'm here to prove you CAN build an app that serves a purpose with very limited programming knowledge. There's a few prerequisites that you must have that are not even coding related before you decide to build just anything. I'll list the ones I find most important:

_1. Do you really want to solve problems and build things that make a difference for people?_

_2. Can you identify a need or want that you have a desire to solve?_

_3. Are you willing to put aside leisure activities to solve this problem?_

I find the most successful programmers are not the people with the best coding skills, its the people who want to genuinely make a difference.  If you're still struggling with those aspects then you maybe need to re-look at the whole reason you are in this game.

Ok so if you've got those 3 questions above answered, then we can move on. Just as a little background, I currently work at a marketing agency and we use a program called Google Adwords to setup and deploy advertisements. Without getting into too much boring details, we use these things called 'keywords' to basically figure out who we want to show the ads to when people search for things on Google. There's a few modifications we can make to these keywords to alter their properties. For example, I can take the word "find a plumber" and change its properties in 3 different ways, like this:

**+find a +plumber**

**"find a plumber"**

**\[find a plumber]**

I used to manually adjust these keywords one by one so it took a stupid amount of time to complete. I knew there had to be a better way, so I searched around and found this cool website called Keyword Toaster. This was great because I could now save a ton of time by just copying/pasting my chunk of keywords here and have this website convert them for me. Though, there were a few problems I identified that I feel could be improved:

Prepositions shouldn't be include for modified broad keywords

I had rather edit the keywords in the same window as Adwords

So it dawned on me, what if I just made a Chrome Extension that fulfilled these needs? All the app is essentially doing is taking a string, altering it, then returning the altered string in an output box. Easy right? Well, kinda...

I looked into what it takes to create a Chrome Extension and found that I should primarily be using Javascript. One thing that made me go with a Chrome Extension is A) I could use the extension in the same window as Adwords and B) No backend/server-side coding needed. I rarely see anyone suggest creating a Chrome Extension to newbies but this was the easiest, most straightforward platform to launch my simple app to the world.

Alright so, I had a minimal amount of JS knowledge beforehand but I knew I really needed to brush up on the basics. So I went through a highly recommended course on watchandcode.com that taught me these essential concepts:

* How to read Javascript
* How to debug
* Functions/Arrays
* Basic DOM manipulation

The course basically teaches you how to create a simple Todo app in your browser. This is perfect because all a Todo app does is take an input and display an output. If you think about it, that's almost the core function of most applications at a very basic level. So what I did was run through the whole course once, then a second time. During the second pass of the course, I essentially took what was being taught and applied to to the concept of the app I was trying to create. I was able to take 80% of the code taught and use it in creating Keyword Modifier. The only real difference in this case was altering the output, which I accomplished with a little REGEX.

To live test the code I was writing I used glitch.com. This made it super easy to see my changes in real time for an app of this size. I wasn't overly worried about CSS so it looked pretty fugly but all I cared about was the core functionality. Trust me, I make it sound like this was a simple process but even though most of the code I used was from the Watch and Code lesson, I had to reach out to people on different Slack channels and refer to Stack Overflow countless times for help.

After many months, my first little application was born on the Chrome Web Store. Here is a link to the first version I had initially launched.: https://github.com/Beznet/keyword-mod . Its a little cleaner now after some refactoring and added functionality, so you will see some minor changes to it if you download it today.

The point of this whole article is to prove that if you have the desire to create and build, its going to carry you through the hurdles of coding regardless of how little you know.

If you're reading this and currently struggling with your idea for an app, leave me a comment! I'd be happy to talk about your process and help you along.
