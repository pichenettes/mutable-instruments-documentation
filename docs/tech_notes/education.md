## From zero to boutique module manufacturer

Let’s say your starting point is a deep passion for patching and synthesis!

Learn a graphical patching language like pd or Reaktor. You can very well stop here if you’re only interested in creating virtual instruments.

Learn a synthesis/composition programming language like Csound, SuperCollider or Faust. This will teach you how to think in terms of linear code rather than in terms of boxes and virtual patch cables.

Brush up on high-school calculus and complex numbers, to get ready for signal processing theory. 3blue1brown’s youtube videos are good at communicating the right intuitions. Then you can read a book like “Understanding Digital Signal Processing” by R. G. Lyons, or do an online signals and systems class from MIT Open Courseware. Learn python and scipy, write scripts to generate signals, plot their spectrum.

Back to basics: write simple command line python programs doing stuff on .wav files, or programs generating .wav files (don’t get distracted with real-time processing, user interface, etc). Write signal generators, filters, etc using what you learnt from the signal processing book.

Your new-found understanding of signal processing theory will allow you to dig into Udo Zolzer’s “DAFX” book and learn about synthesis techniques, FX, etc. Try implementing some effects in the book as Python programs, or with pd. This is something you’ll end up doing a lot anyway to sketch ideas.

Understand that python or pd are towers of abstractions and that writing code for an embedded processor is done at a much lower level. A good online course for understanding towers of abstraction, and what’s going on inside processors is [Nand 2 Tetris](https://www.nand2tetris.org/).

At this stage, you can learn a low-level programming language like C. Write C equivalents of the oscillators, filters, etc. you wrote in Python. “The Audio Programming Book” by Boulanger is great at this stage!

If you ever decide to make your own hardware, start with a development board (like the STM32F4 discovery). And study the code/schematics of Mutable Instruments’ products. If you want, you can spend a couple of weeks playing with Arduino boards, but don’t spend too much time learning it: to make it simple for beginners they often do things the “wrong” way (especially on questions related to timing, multi-tasking that are of prime importance in musical instruments), so remember that whatever arduinese you learn will have to be unlearned later.

If you ever want to get into analog electronics (for music), it’s easy at this stage to grab a book like “Operational Amplifiers & Linear Integrated Circuits” (beginner-ish, slow, but without too much handwaving), then follow Aaron Lanterman’s video lectures on analog electronics for sound synthesis (if you can find them online). Learn how to use a simulator (LTSpice) to test your circuits, but always breadbord them too! Every week, spend some time breadboarding, simulating, understanding a classic circuit from DIY modular resources like Yusynth or EFM.

When you’ll get closer to having devices manufactured, look into “The Circuit Designer’s Companion” by P. Wilson - which covers many practical aspects and “deviations from theory” stuff (reliability, manufacturing issues). Learn a PCB layout program, how to order PCB or assembled protos from a service like Beta Layout.

Business aspects are well covered in D. Lancaster’s “Incredible Secret Money Machine”, or by reading Steve Albini interviews.

## CS or EE degree?

**Don't take any decision regarding your education with the sole purpose of making audio gear in mind**

The degree title doesn't mean much, what matters is what you learn. Some guys have an "electronic engineering" degree and learnt everything and anything about industrial controllers and plant automation. Some guys have a "software engineering" degree and learnt everything about integration schemes for systems of non-linear differential equations. Guess who'll be the best at writing MS20 filter emulation DSP code.

It seems to me that in the US there's a strong division between electrical engineering and computer science. I studied in a place where there's no such divide and in which I had the ability to pick classes from both domains - and I strongly encourage you to seek such a balance. There's some EE you don't really need to know (say power electronics, RF), and there's some CS you don't need to know (say databases, networking, distributed systems). While there are exceptions, don't expect to get classes about Moog filters and VCOs :) It's not a problem, there are enough books/papers on that.

So check your syllabus and make sure that it covers:

-   Solid mathematical foundations ("Signals and systems" type of class) - Laplace/Z/Fourrier transforms, control theory, stability, filters, sampling.
-   Enough analog electronics to allow you to analyze with pen and paper transistor and op-amp circuits.
-   Enough digital electronics to allow you to design a simple microprocessor from scratch. So that you get a good understanding of what's going on in the DSP/microcontrollers you use.
-   C programming language, first on a Unix system, then on embedded systems. Both through projects. No point sitting in a classroom watching slides about peripherals initialization on a STM32F.
-   A software engineering project using a high-level language (Java, C++) - essential if you intend to do software plug-ins, or hardware with a complex, layered firmware (say a polysynth with menus, not just a box doing some I/O). Learn how to build "towers of abstraction", think about code as a way of cleanly describing/expressing how things are ("software engineer's code"), rather than a way of making a processor explicitly do a sequence of actions ("electrical engineer's code").
-   Some exposure to theoretical computer science and compilation, to get a good feeling about what happens to the code you write and why it's efficient (or not).
-   Exposure to numerical analysis (interpolation/approximation, integration schemes for differential equation) is at the root of analog circuit modelling.
-   As many practical sessions/projects/internships, to get a chance at having experienced people look over what you do and provide you with feedback.
-   Small business management. The best way to get a job in the field of electronic music instruments is to create your own company. Even if you hire a lawyer and an accountant to help you figure this out, you'd better have a good understanding of how to run a business.

Try to get an internship at a place doing "real" engineering (I don't know... Linear Technology, JPL, Raytheon, Tektronix...) - rather than at a music instruments company where half of the guys don't really know what they are doing.

## Synth DIY skill levels

### Soldering

-   Level 0. Through-hole board assembly.
-   Level 1. Through-hole desoldering, rework.
-   Level 2. 1206 and 0805 SMT, SOIC, large pitch TQFP.
-   Level 3. Fine pitch SMT, advanced techniques (drag soldering).
-   Level 4. Hot plate techniques.

### Sourcing

-   Level 0. Buying kits.
-   Level 1. Shopping from a pre-compiled BOM for Mouser & co.
-   Level 2. Knowing the various parameters of parts, and how to use Mouser/Digikey/Farnell to look for the parts you need.
-   Level 3. Knowing the different types of capacitors, IC logic families, how to make substitutions when repairing vintage gear.
-   Level 4. Knowing where the good deals are; having lost a reasonable amount of money with dodgy Chinese suppliers to be able to identify the good ones from the terrible ones.
-   Level 5. Scavenging mobile phone parts.

### PCB layout

(I personally recommend skipping the "etch your own PCBs" stage. It's dirty, messy and ultimately the PCBs you'll need for any serious synth DIY project will be out of reach of DIY etching, unless you're Schrab)

-   Level 0. Buying pre-made PCBs.
-   Level 1. Knowing PCB tech and parameters, Ordering your own PCBs from Eagle files.
-   Level 2. Understanding the Gerber format. Generating and proofing Gerber files.
-   Level 3. Designing a PCB from schematics (for example creating a PCB of a classic synth circuit whose schematics are publicly available).
-   Level 4. Designing a clean-looking PCB that other people will enjoy building.

### Digital/Microcontroller:

-   Level 0. Buying pre-programmed chips. Not frying them with ESD discharges.
-   Level 1. Logic gates. Learning how to flash a microcontroller (AVR, PIC, ARM...).
-   Level 2. Learning the basics of a microcontroller or development board (arduino) for simple tasks like MIDI control.
-   Level 3. Installing the development tools and compiling the firmware of an open-source DIY project. Making a simple code modification.
-   Level 4. C language. Understanding the various peripherals in a microcontroller (UART, PWM, SPI, timers...).
-   Level 5. Specializations such as DSP for audio applications; or more advanced system stuff (DMA, RTOS, audio codecs, USB, networking, displays, mass-storage).

### Analog electronics

-   Level 0. Identifying components, reading their values.
-   Level 1. Knowing what components do.
-   Level 2. Understanding circuit blocks and breaking down schematics into high-level functions.
-   Level 3. Cobbling together circuits by reusing existing bits of schematics.
-   Level 4. Basic circuit calculations - voltage dividers, op-amp circuits, RC filters.
-   Level 5. Synth-specific bits: expo converter, classic transistor circuits and cells, LM13700, V2164, comparators, waveshaping...
-   Level 6. Mathematical approach to circuit design - calculating transfer functions, running simulations.

### Synth-specific knowledge

-   Level 0. Functional blocks (VCO, VCF, VCA...).
-   Level 1. Implementation specifics (eg: what is a "tri-core" VCO? ; filter topologies...).
-   Level 2. Practical tricks to get circuits to work, stability, parts variability, reliability.
-   Level 3. Knowing how things were done in specific vintage machines. Having repaired/cloned/analyzed or simulated their circuits.

### Troubleshooting

-   Level 0. Complaining on a forum that it doesn't work.
-   Level 1. Intelligently using a multimeter to measure resistance, voltage, current. Accurately describing the state of "brokenness" of a system.
-   Level 2. Breaking down the "search space" - working step by step to identify which part of a system is broken. Roughly predicting how a damaged/incorrect part value would impact circuit behavior.
-   Level 3. Using an oscilloscope to view waveforms, measure frequencies/duty cycles, Vpp.
-   Level 4. Understanding service manuals and performing the calibration/troubleshooting procedures recommended by the manufacturer.
-   Level 5. Reading schematics and making predictions as to what would be the expected waveforms/measurements.
-   Level 6. Years of experience repairing stuff...

### Misc / getting things done

-   Wiring
-   Using services like Ponoko for custom plexiglas or MDF parts
-   Using front panel design tools and services
-   Cabinet and case construction, woodworking, paint
-   Metal etching, transfer techniques
-   Breadboarding / prototyping

