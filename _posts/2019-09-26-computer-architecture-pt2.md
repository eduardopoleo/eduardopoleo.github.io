---
layout: post
title: "Computer Architecture. Pt-2"
date: 2019-09-28
---

This follows my previous post about my learnings from [But How Do It Know?](http://www.buthowdoitknow.com/index.html). 

In the [previous post](https://eduardopoleo.github.io/2019/09/28/computer-architechture.html) we talked about bits, codes and logic gates. These are the fundamental building blocks for the 2 main elements that make up a computer, the Random Access Memory (RAM) and the Central Processing Unit (CPU). These 2 pieces work in tandem to store and process all the instructions and data that allow computers to do the things they do.

On todays entry we'll talk about registers, the main elements that are required to build the RAM. For the purpose of this and the following posts I'm going to treat some of components described as "black boxes". I will not go in details into how you build them from scratch using gates. The purpose of these entries is not to recreate the [book](http://www.buthowdoitknow.com/index.html) but to have a higher level understanding what is it about. I highly recommend checking the original source if you want more details.

## Memory Bit
The first step to understanding how the RAM works is to explore how to store memory on a single bit. Using some of the gates we saw previously it is possible to create a memory bit.

![memory bit](https://eduardo-tutorial-videos.s3.us-east-2.amazonaws.com/HDIK/memory_bit.png)
<sub><strong>NOTE: It's possible to build this memory bit using 4 NAND gates</strong></sub>

As the name implies the memory bit can only store information about 1 bit. This means that it can only remember whether a particular bit (in our case **i**) was 1 or 0 at a previous point in time. Now the computer needs to be able to control when it wants to change the state of a particular memory bit to store a new value, and that's where the **s** (for "set" if you will) bit comes in. The way the memory bit works is such that its current state **o** can only be changed or reset when "s" is 1. To understand this let's take a look at the following time-table showing the state of the inputs at different points in time.

Time. |   s  |  i  |  o
:---: | :---:|:---:|:---:
0     | 0    |1    |1
1     | 0    |0    |1
2     | 1    |0    |0
3     | 0    |0    |0
4     | 0    |1    |0
5     | 1    |1    |1

Initially, the current state of the memory bit **o** is 1 and the incoming input **i** is one 1 as well. Now let's say that after the first fraction of time we want to change the state of the memory bit **o** to 0 so we proceed to switch the i bit off. We can see that **o** still remains off (0) regardless of the change in **i**. It isn't until the fraction of time 2 when we turn **s** to 1 that the state of bit **o** finally changes to 1. We can then repeat the process again to flip the **o** bit back on as shown on second 4 and 5.

With this mechanism we are then able to remember the state of a bit **i** by manipulating a setter bit **s** on demand. The current stored memory state **o** is then available to be transmitted to other parts of the computer as needed.

## Registers. Pt-1
As we discussed before on the previous entry the amount of information that we can encode on single bit is very limited. So generally computers stack several of these memory bits to store more meaningful patterns. A set of memory bits holding a particular sequence is known as a register. The amount of memory bits on a register will depend on the cpu architecture. 32 bit processors will have 32 bits registers, 64 bit processors 64 bits registers and so on. We're going to be using 8 bits processor which also happens to be the size of a byte. We can see a memory byte representation below:

![memory byte](https://eduardo-tutorial-videos.s3.us-east-2.amazonaws.com/HDIK/memory_byte.png)

Notice how each individual bit has it's own input **i** but they all share the same setter bit **s**. This is because as we'll see later it makes more sense for the CPU to load full size data or instructions patterns into its registers as opposed to control each bit individually. Also from now onwards I'll be using the simplified version of the register to save some space on the graphics. Every instance where there would normally be 8 wires will be replace by 2 parallel lines as shown above.

## Bus
The CPU needs to have access to the information stored on any register at all times, also it needs to be able to freely move data from and to different registers. For this to happen there needs to be some wiring in place that allows any register to reach (almost) any part of the computer. Such wiring is known as bus or data bus. The bus size must match that of the register, which in our case is 8 bits. The same 8 wires will go across the whole computer and will be available to almost all registers. That way whenever a register needs to send its data across all it has to do it to load it into the bus and let it "flow" to its destination, as shown in the diagram below:

TODO: Diagram with several registers connected to a bus. (Simplified notation)

Now, as you might as noted there is a problem with this. What would happen if multiple registers want to load their information onto the bus at the same time? Well the bus information would end up getting unpredictably overwritten and there would be no guarantee that a register content reached its desired destination. To solve this issue we need to introduce the concept of an enabler.

## Registers. Pt-2
An enabler consist of a bit (e) and 8 AND gates. This device will sit in front of the register byte (shown above) and will only allow its information to freely flow onto the bus when bit **e** is on. We can now see the full representation of register below:

TODO: Register with enabler.

In our fictional computer most registers will contain enablers except for those that do not have direct contact with the data bus.

## Putting it all together.
The final representation of the bus and its registers will be something like this:

TODO: bus with several registers and enablers.

Let's say we want to move R1 data into R3. The sequence to achieve this will be:
- Enable R1 **o** bit onto the bus by setting e1 = 1
- Set R3 s3 = 1 state to whatever is on the bus by settings s3 = 1
- Lock R3 state by setting s3 = 0
- Stop R1 **o** from flowing onto the bus e1 = 0

This concludes our discussion about registers. On the next blog we'll be putting this together to build a RAM.