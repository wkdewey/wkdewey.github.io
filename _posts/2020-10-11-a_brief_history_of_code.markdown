---
layout: post
title:      "A Brief History of Code"
date:       2020-10-11 23:46:51 +0000
permalink:  a_brief_history_of_code
---


I have training as a historian from my academic studies, and have been interested in the history of computing for a long time. Looking at coding from a historical perspective is a good way to review some of the more popular programming paradigms, and why they came to be used. In some ways, the dominant paradigms reflect the ways that computers were used at any given point in time, responding to the needs of users and to the challenges of developers

One of the first popular computer languages was BASIC. It was invented in Dartmouth in 1964 for students to use in timesharing mainframes, then became popular in the early days of personal computers, often being included with home systems in  the 1970s  and 1980s. It is a good example of a high level **procedural programming language**, designed to be easy to use for the beginner, with statements closer to English than machine code. When I became interested in computers in the mid 1990s (I was 9 or 10), BASIC was on the decline, but there were a number of old computers laying around my house and the public library was filled with computer books from the 80's. For example, there were David Ahl's Basic Computer Games ([](https://www.atariarchives.org/basicgames/)) and Basic Computer Adventures([](https://www.atariarchives.org/basicgames/))
The code look like this
```
100 CLS : KEY OFF
110 LOCATE 10, 1 : X$ = "Voyage to Neptune, 2100" : GOSUB 1680
120 LOCATE 13, 1 : X$ = "(c) by David H. Ahl, 1986" : GOSUB 1680
130 LOCATE 23, 1 : GOSUB 1550
140 WHILE RN > 32767 : RN = RN - 65535! : WEND : RANDOMIZE RN : CLS
150 GOSUB 1180 : 'Display initial scenario
160 BREED = 120 : FUTOT = 3000 : GOSUB 1420 : 'Set initial values
170 '
180 'Loop through trip segments
190 SEG = SEG + 1 : 'Trip segment counter
200 IF SEG = 7 THEN 940 : 'Reach Neptune?
210 '
220 'Print current conditions
230 PRINT : PRINT "Current conditions are as follows:"
240 PRINT " Location :" PLAN$(SEG)
250 PRINT " Distance to Neptune:" 2701-DIST "million miles."
260 IF SEG = 1 THEN 350 : 'First trip segment?
270 PRINT " Distance from Earth:" DIST "million miles."
280 PRINT "Over the last segment, your average speed was" INT(RATE) "mph,"
290 PRINT " and you covered" DIS(SEG - 1) "million miles in" TIME "days."
300 TM = .81 * DIST : PRINT "Time est for this total distance:"; : GOSUB 1460
310 TM = TOTIME : PRINT "Your actual cumulative time was:"; : GOSUB 1460
320 PRINT "You used" UBREED "cells which produced" FUBR "pounds of fuel each."
330 IF FUDCY = 0 THEN 350
340 PRINT FUDCY "pounds of fuel in storage decayed into an unusable state."
350 PRINT "Pounds of of nuclear fuel ready for use:" FUTOT
360 PRINT "Operational breeder-reactor cells:" BREED : PRINT
370 '
```
An example of one of the GOSUB subroutines:
```
1580 '
1590 'Subroutine to read yes/no answer
1600 GOSUB 1630 : IF A$ = "Y" OR A$ = "N" THEN RETURN
1610 INPUT "Don't understand your answer. Enter 'Y' or 'N' please";A$ : GOTO 1600
1620 '
```
The code is striking for including some methods of control flow that are rarely used in modern programming, and omitting others that are taken for granted today. BASIC used line numbers, GOTO statements, and subroutines marked with GOSUB, features that are  not common in today's languages like Ruby and JavaScript. It did not have named functions, much less classes and objects. However, it was an easy language to pick up by reading the code and examples, and typing out programs on your computer, and it taught important concepts in procedural programming like for and while loops, variables, and conditional statements which are found in all modern languages

An early advance on this method of coding was **structured programming**. As computing matured from its early days, it was no longer enough just to string code together to tell the computer what to do. The new discipline of computer science sought to formalize programming and make it more logical and efficient, so that computers could run programs quickly, and perhaps more importantly,   One of the important proponents of this style was Edsger Dijkstra, who wrote a famous letter "Go To Statement Considered Harmful." Goto statements were criticized for creating convoluted "spaghetti code." Instead programmers were to use loops, conditional statements, functions, and blocks. Named functions were a common way that  languages provided an alternative to goto statements and numbered lines; instead of jumping to an arbitrary point, the programmer can encapsulate and abstract a commonly repeated series of instructions into a meaningful name. Here is an example of a function from C, which is a popular programming language created for the Unix operating system in the 1970s:
```
/* function returning the max between two numbers */
int max(int num1, int num2) {

   /* local variable declaration */
   int result;
 
   if (num1 > num2)
      result = num1;
   else
      result = num2;
 
   return result; 
}
```
This function can then simply be called with `max(a, b)`. One of the first examples we learned with Ruby was  
```
def greeting
  puts "Hello World"
end
```

A more modern advance on procedural programming is **object-oriented programming**. An early implementation was the Smalltalk language, published in 1980 by Alan Kay and Adele Goldberg who worked at Xerox's research laboratory PARC. Xerox PARC is also known for inventing the mouse and the graphical user interface, which later were adopted by the Macintosh and Windows platforms among others. This is not a coincidence, the common theme is human-computer interaction. The graphic user interface and related innovations brought computers to the masses, enabling them to interact woth computers with familiar metaphors (a desktop, folders, trash can) and visible representations. Similarly, object oriented programming allows programmers to model the real world in an easily understandable way.  A basic example is
```
class Dog

def name=(dogs_name)
    @this_dogs_name = dogs_name
  end
 
  def name
    @this_dogs_name
  end
  def bark
    puts "Woof!"
  end
end
 
```
Instances of objects can have instance variables and methods, representing their attributres and methods, in other words their nouns and their verbs. A dog has a name and can bark. This is easy for the programmer to understand, and other developers that read their code.

**Functional programming** is a paradigm that has been around for a long time, developed by theoretical computer scientists and mathematicians, but is recently gaining ground in mainstream languages, especially JavaScript. It is becoming something of a rival to object oriented programming. Functional programming is hard to understand at first if you are familiar with other paradigms. It emphasizes pure functions (those that have the same output for a given input), higher order state, and avoiding mutating state and side effects. A simple example is the following, which updates the state of an object without mutating the original object, instead creating a new one:

```
const x = {
  val: 2
};

const x1 = x => Object.assign({}, x, { val: x.val + 1});
```

The recent updates to JavaScript like ES6 have incorporated more features useful in functional programming, like the spread operator that can be used to nondestructively update state. Functional programming techniques are also important to the new web development framework React, which was created in 2013 by Facebook, one of companies that has the most influence on the modern Internet. For instance, state cannot be modified directly but through setState methods, and dispatcher methods through Redux also modify the state indirectly to avoid side effects. Why has functional programming risen in parallel with the modern Internet? Not everyone would make this connection but I think there may be one, if you consider how complex websites like Facebook can get. The old paradigms may not work so well on their own. The priority in the rising popularity of functional programming is not so much ease of use, but avoiding complex bugs and making sure interactive content can be updated cleanly and predictably.


