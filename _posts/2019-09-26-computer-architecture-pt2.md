---
layout: post
title: "Computer Architecture. Pt-2"
date: 2019-09-28
---

This follows my previous post about my learnings from [But How Do It Know?](http://www.buthowdoitknow.com/index.html). 

In the previous post we talked about codes encoded in bits and fundamental building blocks for computers hardware the logic gates. Today we'll take a look at one of the main components of computers RAM. RAM stands for ... It comprises the physical structure where computers stored store all the data and the instructions they require to perform any procedure. RAm has 2 interesting properties:

It's volatile, 
  Information stored in RAM only exist while the computer is on, once the computer is turned off all the information stored in RAM is gone. This is contrary to information stored in "disk" which persist at all times.
Random access.
  Information can be stored and retrieved regardless of what position they occupy inside the RAM

On todays entry we'll explore the main elements that are required to build a RAM. Full disclosure, for the purpose of this and the following posts I'm going to treat some of these elements as "black boxes". I will not go in details into how you build them from scratch using simpler gates. The purpose of these entries is not to recreate the [book](http://www.buthowdoitknow.com/index.html) content but to have a higher level understanding what is it about. I will try to provide links when possible but I highly recommend checking out the original source for details.

## Memory Bit
The first step into understanding how to computer memory works is to understand how to store memory on a single bit. Using some of the gates we saw previously (4 NAND gates to be precise) it is possible to create a memory bit.

.... Memory bit diagram....

As the name implies the memory bit can only store information about 1 bit. This means that it can only remember whether a particular bit (in our case **i**) was 1 or 0 at a previous point in time. Now the computer needs to be able to control when it wants to change the state of a particular memory bit to store a new value, and that's where the **s** (for "set" if you will) bit comes in. The way the memory bit works is such that its current state **o** can only be change or reset when "s" is 1. To understand this let's take a look at the following time-line showing the state of the inputs at different points in time.

Initial s = 0, i = 1, o = 1
1s s = 0, i = 0, o = 1
2s s = 1, i = 0, o = 0
3s s = 0, i = 0, o = 0
4s s = 0, i = 1, o = 0
5s s = 1, i = 1, o = 1

<sub><strong>NOTE: This whole process of setting and un-setting memory usually happens on a fraction of a second. The timeline is stretched here for the purpose of the explanation.</strong></sub>

Initially, the current state of the memory bit **o** is 1 and the incoming input **i** is one 1 as well. Now let's say that after the first second we want to change the state of the memory bit **o** to 0 so we proceed to switch the i bit off. We can see that **o** still remains off regardless of the change in **i**. It isn't until second 2 where we turn **s** that the state of bit **o** finally changes to 1. We can then repeat the process again to flip the **o** bit back on as shown on second 4 and 5.

With this mechanism we are then able to remember the previous state of a bit **i** by manipulating a setter bit **s** on demand. The current stored memory state **o** is then available to be transmitted to other parts of the computer as needed.

## Registers
As we discussed before on the previous entry the amount of information that we can encode on single bit is very limited. So generally computers stack several of these memory bits to store more meaningful patterns. A set of memory bits holding a particular sequence of bit is known as a register. The amount of memory bits on a register will depend on the cpu architecture. 32 bit processors registers will have 32 bits, 64 bit processors register will have 64 bits and so on. We're gonna follow the  