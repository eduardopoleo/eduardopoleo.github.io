---
layout: post
title: "Computer Architecture. Pt-1"
date: 2019-09-28
---

This starts a series of posts where I summarize some of my learnings after reading [But How Do It Know?](http://www.buthowdoitknow.com/index.html). For those who do not know, this books is a "human-readable" introduction to the fundamentals of computer architecture. The author builds a theoretical computer starting from NAND gates all the way to the CPU's control section. He also touches on some other topics likes boolean arithmetic, computer peripherals, humans vs machines intelligence, among other things. It's an amazing read for anybody that wants to know how computers work not only engineers. The author assumes no technical background so the language throughout is very accessible and the content is very well supported with amazing graphics. Well, without further ado here's what I got from it.

## Codes

Computer are dumb machines that are able to perform simple calculations and procedures fast (like almost light speed fast). When enough of these procedures are put together in the right order computers are able to perform tasks that are useful (and seemingly complex) to humans. They do all this by controlling the electrical current that runs through some small wires known as bits. At any given point in time these bits can live in any of these two states: 
- "1", when electricity runs through.
- "0", when NO electricity runs through.

These bits can be arranged into sequences to express some arbitrary meaning we want to give to them. For example with a single bit, we can determine whether or not a lamp should be on or off. Our code could be something like this:

Bit state | Lamp light
:---      | :---:
1         | On
0         | Off 

Now, with 2 bits we can do more interesting stuff such as determining what light should be on a traffic light. A code for this could look like the following:

Bits state | Traffic Light
:---       | :---: 
00         | ❤️
01         | 💛
10         | 💚
11         | ⬅️

<sub><strong>NOTE: Apologies for the heart emojis but apparently colored circle emojis are not a thing just yet.</strong></sub>

Notice how we were able to encode 4 different states by only using 2 bits, this is because the order of the bits matter to the code we just created. In fact as we add bits the possible combinations increment by a factor of 2 for every bit added. Thus, 3 bits will yield 8 combinations, 4 will translate to 16 combinations and so on. The total number of combination can then be calculated by 2^(number of bits)!

Now is there anything interesting, besides lamps and traffic lights, that can be encoded with bits? Yes lots of things. For instance with 8 bits (and its corresponding 256 combinations) you have enough possible combinations to represent the whole latin alphabet, which usually consist of 26-30 characters depending on the language. In fact you even have some outstanding combinations to represent the capitalized version of each letter, numbers, punctuation symbols and some other weird characters. This exact same code exists already and it is known as [ASCII](https://www.ascii-code.com/).

This is a very useful code and it's probably employed by all computers nowadays but it's important to understand that, just like our lamp or traffic light code, this is a man-made construct. A convention established by a group of people in a room that one day arbitrarily decided that computers should understand "01100011" as the character "c". There is nothing intrinsically special about the bit combination "01100011" that enforces the meaning "c" on a particular computer. It's just part of a standard agreed upon, most likely to enable the representation of the letter "c" across all computers, and devices regardless of their manufacturer.

## Logic Gates

This thing about arbitrary codes is all well and good, but how do you go from bit combinations to full fledged systems capable of calculating and remembering things? It's quite the journey but it all starts with logic gates. Logic gates are hardware devices that operate on bits and they usually have the ability to produce output bit(s) given some input bit(s). A couple of very simple gates are

![and/or gates](https://eduardo-tutorial-videos.s3.us-east-2.amazonaws.com/HDIK/and_or.png)

Both of these gates operate on 2 input bits (**a**, **b**) and have 1 output (**o**). In the case of the AND gate **o** will only be 1 when both of the inputs **a** and **b** are 1. The OR gate on the other hand, produces a 1 bit if any of its inputs **a** or **b** are 1. If you're are a programmer it's easy to draw parallelism between these gates and the common logic "and" and "or" statements used on your day to day job.

There's another gate that's worth taking a look at, the NAND gate. As its name implies the NAND gate is the negation of the AND gate. All it's outputs on the truth table are inverted with respect to the "AND" gate. That is **o** is 1 for all combinations of inputs except for when both **a** and **b** are 1. Here's the truth table for it:

![nand gate](https://eduardo-tutorial-videos.s3.us-east-2.amazonaws.com/HDIK/nand.png)

One interesting property about the NAND gate is that it can be combined and arranged to create multiple other types of gates. For instance we can start by creating a NOT gate that is a gate that takes an single input **a** and produces the opposite output **o** by simply feeding the same input twice to the NAND gate. 

![not gate](https://eduardo-tutorial-videos.s3.us-east-2.amazonaws.com/HDIK/not.png)

Then we can use this to recreate both of the OR and the AND gates shown before:

![not gate](https://eduardo-tutorial-videos.s3.us-east-2.amazonaws.com/HDIK/and_not_redux.png)

You can in fact extrapolate this idea and create ALL (yes ALL!) the different gates and devices you'll ever need to build a computer only starting out from NAND gates! We will exploring such devices on subsequent posts. Thanks for reading. Bye for now!

