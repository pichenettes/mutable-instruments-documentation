## EMC certification for modules?

Expect total cluelessness from the engineers and technicians you're going to interact with, because the idea of a modular system with non-standardized components brought by several manufacturers is very alien to them. Expect to tell them for the 5th time that you're not the one who's selling the wall-wart, you're not even selling the power distro board, you're not selling the assembled system.

## What to test?

Unless it's horribly designed or is a power-hungry FPGA beast with bits of PCB exposed and a badly covered OLED, a digital Eurorack module in a Doepfer minicase with some blind panels, powered by an AC wall-wart, receiving its CVs from a CV source in the control room, will meet the requirements for FCC part 15 class B.

My recommendation: build a system. You can actually test several modules in one go (at the risk of making it harder to pinpoint a problem if a test fails). Even better: test several systems with different PSUs, case materials, patches, configurations of modules (and make sure modules appear in 2 cases).

## Some tips

* The EE department at the local college might have some equipment or a test room you can rent. That could be a cheap option for pre-compliance tests.
* I suggest doing things in two phases: pre-compliance tests (they do faster frequency sweeps, measure less datapoints and the whole thing is done in a few hrs) with protos. You still have a chance to fix things, then! Then do your pre-prod run, make sure this is the final hardware revision that's going to be mass-marketed, and do the final test with one unit from the pre-prod run. Don't test anything unless you're 100% sure that it's going to be manufactured and sold as is.
* Set up a template in your DAW with a couple of tracks ready to record to, and audio analysis plug-ins as master track inserts (spectral analyzer, rolling spectrogram showing the past 30 seconds). I really enjoyed working with Reaper for that.
* Don't bother bringing ferrites, copper tape, tinfoil hats, passive components: you'll find all those staples at the lab. What you won't find, though: replacement audio cables, adapters.
* Get a battery powered headphone amp with high-impedance input and an extra pair of headphones. Use it if you need to do adjustments or check for faults on your system while it is in the Faraday cage, without changing anything in the control room.
* Bring a JTAG adapter, and have a dev environment ready on your laptop. Some tests might shed the light on software bugs!
* Build and bring a Eurorack load. A bunch of 150 ohm 1W resistors between +12V and ground and -12V and ground, on a little board that can be plugged on the power distribution board.
* Build a second unit of every device you need to test.

## The actual tests

### Radiated emissions (2 hours)

* Blind panels are your friends.
* Worst offender: SMPS and long bus boards. You'll find that a commercial case with no load and covered in blind panels may violate class B requirements. Oops.
* FCC tests are performed on 120V equipment, so you can't bring your A100 case with its super clean linear PSU (unless it's the version with a 120V transformer inside, but then you won't be able to use it for tests running on 220V).
* If unplugging a module improves things, it doesn't necessarily mean that the module emits something bad. It can also be that it draws a lot of current and make the PSU work harder (more current flowing on the bus boards, higher switching transients). Check this hypothesis with a load, and conclude on the sad state of rack power generation and distribution.

### Conducted emissions (1 hour)

Same as above: It's all about the PSU and power distribution board.

### Immunity to radiated emissions (2 hours)

* Not much happens with the continuous wave (unmodulated RF signal), but you'll get surprises when this signal is amplitude-modulated by a 1kHz tone!
* Define the pass criteria before the test. Eg: "tone not audible", or "tone < -80dB", or "tone burried in the noise floor". Changing your criteria mid-flight is very bad manners.
* Keep in mind that there are not so many AM transmitters sending 1kHz tones in nature. This test makes it easy to spot problems, but they might not necessarily translate into the same flavour of audio disturbances in the wild.
* The culprit is going to be an analog circuit with a non-linear element and a lot of gain. Faint disturbances can also be picked up by reverse polarity protection diodes/transistors and passed to your rails (shame on you if you use them in your CV conditioning circuits).
* Make sure your patch doesn't have any frequency content at 1kHz, otherwise you won't notice if your device picks up anything.
* Reversely, make sure your patch doesn't have a lot of gain at 1kHz. For example, if your patch has a very resonant BP (or tuned delay line, or a modal resonator filter) giving 48dB of gain at 1kHz, it can make a benign disturbance sound very noticeable.
* Monitoring a silent patch makes it easy to spot disturbances coupling into the audio path, but you are going to miss other things.

### Immunity to conducted emissions (2 hours)

* Same as above. Bad things are much less likely to happen here.

### Immunity to transients on the mains network (4 hours)

* Nothing to report here, there will be many opportunities for the transients to be absorbed down the chain (in the power adapter, the power board, whatever filters are on your module's inputs).
* This test is boring, since you will have to monitor your device for close to 3 hours. Note that the test protocol requires a 60s delay between pulses. You can ask for 40s or 30s instead - the stress on your device is higher but the test will take a shorter amount of time to complete.
* Set up a patch or test pattern that is not too boring to listen to.

### Immunity to ESD (30 mins)

* This test might fry modules. Sorry :(
* Benign stuff that might happen: audio clicks, spurious note triggers. Tell the test engineer that this is normal.
* More annoying stuff that can happen: MCU crashes or weird software glitches. Your firmware can include some "self-health" routines (watchdog timer, extra sanity checks on the value of state variables).
* If you observe an unusual behaviour, it could also be a software problem: for example, even if an ESD pulse reaching a CV input is absorbed by protection circuitry, the resulting fast and wide modulation of a synthesis parameter might cause a bug you never observed before when everything was modulated by smooth LFOs.
* Ceradiodes on all your inputs (check the schematics of recent modules!) are a no-brainer.

### Immunity to magnetic fields (20 mins)

* What's probably going to happen: the frequency of the modulated magnetic field will show up as a -90dB spike, that is not due to the electronics in your module but just this big network of copper and cables acting as a receptor. Switch everything off, and the little spike won't bulge. You should have stated before that it is acceptable.