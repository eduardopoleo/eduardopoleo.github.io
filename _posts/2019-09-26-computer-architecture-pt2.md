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

<sub><strong>NOTE: This whole process of setting and un-setting memory usually happens on a fraction of a second. The time line is stretched here for the purpose of the explanation.</strong></sub>

Initially, the current state of the memory bit **o** is 1 and the incoming input **i** is one 1 as well. Now let's say that after the first second we want to change the state of the memory bit **o** to 0 so we proceed to switch the i bit off. We can see that **o** still remains off regardless of the change in **i**. It isn't until second 2 where we turn **s** that the state of bit **o** finally changes to 1. We can then repeat the process again to flip the **o** bit back on as shown on second 4 and 5.

With this mechanism we are then able to remember the previous state of a bit **i** by manipulating a setter bit **s** on demand. The current stored memory state **o** is then available to be transmitted to other parts of the computer as needed.

## Registers. Pt-1
As we discussed before on the previous entry the amount of information that we can encode on single bit is very limited. So generally computers stack several of these memory bits to store more meaningful patterns. A set of memory bits holding a particular sequence of bit is known as a register. The amount of memory bits on a register will depend on the cpu architecture. 32 bit processors registers will have 32 bits, 64 bit processors register will have 64 bits and so on. We're going to be using 8 bits (also known as a byte) size registers in this series. We can see a register representation below:

..... Register image....

Notice how each individual bit has it's own input **i** but they all share the same setter bit **s**. This is because as we'll see later it makes more sense for the CPU to load full size data or instructions patterns into its registers as opposed to controlling bits individually. NOTE: We'll be using the condensed imagery (8 bits = 2 lines, 1 register = 1 box) from now own to save some space.

## Bus
The CPU needs to have access to the information stored on any register at all times, also it needs to be able to freely move data from and to different registers. For this to happen there needs to be some wiring in place that allows any register to reach any part of the computer. Such wiring is known as bus or data bus. The bus size must match that of the register, which in our case is 8 bits (or wires). This same 8 wires will go across the whole computer and will be available to almost all registers. That way whenever a register needs to send its data across all it has to do it to load it into the bus and let it "flow" to its destination, as shown in the diagram below:

.... different registers load into a bus

Now, as you might as noted there is a problem with this. What would happen if multiple registers want to load their information onto the bus at the same time? Well the bus information would end up getting unpredictably overwritten and there would be no guarantee that a register content reached its desired destination. To solve this issue we need to introduce the concept of an enabler.

## Registers. Pt-2
An enabler consist of a bit and 8 and gates. This device will sit in front of the register byte (shown above) and will only allow its information to freely flow onto the bus when the enabler bit **e** is on. We can see the full representation of register below:



On our fictional computer most registers will contain an enabler except for those that do not require loading info into the bus.

## Putting it all together.
The final representation of the bus and the registers will be something like this:

... bus with a couple of registers ...

Let's say we want to move R1 data into R3. The sequence to achieve this will be:
- Enable R1 data onto the bus by setting e1 = 1
- Set R3 state to whatever is on the bus by settings s3 = 1
- Lock R3 state by s3 = 0
- Disable R1 by setting e1 = 0

This concludes our discussion about registers. On the next blog we'll be putting this together to build a RAM.

