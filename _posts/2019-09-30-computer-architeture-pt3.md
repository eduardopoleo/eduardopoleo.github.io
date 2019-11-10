---
layout: post
title: "Computer Architecture. Pt-3"
date: 2019-09-30
published: false
---

In the previous entry we took at look at registers and how they can store or load information onto the bus. Today we'll explore how create a computers RAM out of a set of registers, here we go!

## Grid
Our computer RAM is made up of an organized grid of registers and it could look something like this:

TODO-IMAGE: very simplify picture of a grid where every intersection is a register. Maybe a bubble zooming in and saying register + extra NAND gates etc

Each intersection on the grid above represents a device that contains a register plus a few extra gates, we'll talk more about these extra gates in a moment. Every register in the RAM is connected to the computer bus, that way the computer can store and retrieve information from any of them at a moments notice. Now this setup while simple begs the following question: How can a computer target a specific register within the grid? It does it through the use of decoders.

## Decoder
The nice thing about the grid arrangement is that every intersection can be treated as a coordinate much like the Cartesian X/Y plane. The decoder is a device that will allow us to transform a set of bits into one of such coordinates. We can see how a decoder works below:

TODO-IMAGE: Diagram of a decoder. Translating a set of inputs into 1 active output

Notice that, contrary to most of the devices we've seen so far, a decoder have more outputs than inputs. In fact the decoder will have as many outputs as possible input state combinations there are. Also for any particular input one and only one of such outputs will ever be 1. The understand this better lets take a look at the 3x8 (3 inputs with 8 outputs) decoder above. When the state of the input is 000 only the output bit **a** will be 1, if the input combination happens to be 001 then only the output **b** will be 1, if the input combination is 010 then **c** will be 1 and so on.

## RAM
Our computer will have 256 registers for a grand powerful total of 256 bytes of RAM 😂, and they will be organized on a 16x16 grid. In order to produce 16 coordinates for each axis x and y, we can break up a byte and run each part through a 4x16 decoder as seen below:

TODO-IMAGE: Diagram of the 4x16 DECODER making up the grid

So when a computer wants to target a specific register all it has to do it to produce a particular byte address. Such byte will then be processed by the decoders which in turn will produce a unique (1,1) X,Y active bit pair combination. The register that sits under that coordinate will be active for whatever operation the CPU needs to do. This device combination of the byte address plus the 2 decoders is the part of the computer known as Memory Address Register or MAR for short.

## RAM-BUS connection
Now that we understand how a computer targets a specific register x,y coordinate we can now understand how each RAM register hooks into the bus.

TODO-IMAGE: Of a particular coordinate and the register underneath. Take diagram from the book.

We can see from the diagram above that in order for a particular register to load its information into the bus a few conditions are required:
- The specific register x coordinate bit should be 1
- The specific register y coordinate bit should be 1
- The **e** bit should be 1

Similarly if we want to store information from the bus into a particular registry:
- The specific register x coordinate bit should be 1
- The specific register y coordinate bit should be 1
- The **s** bit should be 1

Thanks for reading. On the next article we'll talk more about how to build a computer clock and how a computer uses it to coordinate operation between registers and the bus!


