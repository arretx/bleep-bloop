# Whole House Audio Over a Dante Network

What's a Dante network?  Go visit https://audinate.com to learn about Dante.  It's a networking protocol that allows you to send and receive multiple channels of digital audio over a network.

## The Problem

I want to be able to route my MacBook Pro main 1/2 audio outputs (that's stereo left/right for common uses like Netflix, YouTube, Spotify, etc.) through the computer network to a whole-house amplification system without using Bluetooth, or any cloud services or third party devices such as Google Home or Alexa.

### Additionally...

I wanted the ability to route two independent outputs from something like Ableton Live, Logic Pro, or any other DAW, as I'm a keyboard player, and it would be nice to be able to fill the house with the sound of a piano with the touch of a button _as well as_ headphones _and perhaps even_ a studio monitor setup, _simultaneously_.

"Hey babe, listen to this..."  Now she can hear it where she is.

It would also be fun to be able to use Ableton Live to cue up music on one output, and sound effects on another, mixing them in the computer and routing them to the outdoor spooky halloween zone.

## The Dante AVIO Adapter

**Cost**: About $200.00

The AVIO Adapter comes in 4 flavors.  The flavor I needed for this setup was the digital to analog network connector.  Basically, this device connects to a port on your network switch with Power over Ethernet enabled (or supplemented with a PoE injector).  Once the device fires up, it obtains its network address from your router and becomes a digital to analog audio interface.

Basically, it becomes a networked two channel audio output device.

Imagine connecting your laptop headphone jack to your whole house amplifier input.  You'd have to run a really long headphone extension cable, or set your laptop right next to your amplifier.  Not ideal.  The Dante AVIO Adapter allows you to keep your laptop at your desk, or your computer, whatever.

## Wireless Limitation

Dante does not perform well over WIFI.  As a result, the Dante software will not transmit audio data over WIFI, so while you can route audio from a remote location, you need to have a wired connection to your router to send audio through the network.  Dante's software will simply ignore WIFI network adapters, so don't even try.

## What Hardware is Needed?

At minimum, two pieces of hardware are needed.  The first is your computer which will be running the Dante software in order to generate a Dante audio network signal (transmitter).

The second is something that can receive the signal and convert it into analog output (unless you have digital powered speakers with Dante built in.  Highly unlikely.)  The next best thing is a Dante AVIO network adapter.

### Amplification...

Okay, there's another bit of hardware you need at the end of the audio path.  The AVIO adapter has two XLR outputs connected to it.  Those outputs need to be connected to some sort of input device (a mixer, an amplifier, whatever you want) and then connected to a speaker system.  

In this case, we're using the Monoprice 6-Zone Amplifier and we're simply using a physical connector to convert from XLR to 3.5mm which is what's on the back of the amplifier.  Sadly, at some point along the audio chain, we have to convert from digital to analog.  A more ideal solution would be to find a multi-zone amplifier that had built-in Dante capabilities, which means we could skip the AVIO adapter altogether and push the conversion to analog all the way to the output of the speaker.  As far as I know, it doesn't exist such that it would be simple to setup with Home Assistant, as is the case with the current Monoprice 6-Zone Amplifier.

## What Software is Needed?

There are three pieces of software that you can use to configure and control audio on your network.  One is required in order to route audio, and one of the other two is required to allow your computer to transmit a Dante signal.

All of them have trials, and all need to be licensed, but it's not really that expensive to have fully working copies on a single machine.

### Dante Virtual Sound Card (VSC)

This is a utility that creates a virtual audio module with 64 inputs and 64 outputs on your computer.  Remember, the AVIO adapter will only receive 2 channels, and this particular adapter will ONLY receive, not send.  You can send two independent mono signals through the virtual sound card, or you could send a 1/2 left/right stereo signal.  Additional adapters could be added to your network to create more endpoints, if you want to get crazy.

### Dante VIA

Dante VIA is an application that allows you to route audio between applications on your computer (think Soundflower, for those of you who know what that is), and as a bonus, you can enable Dante on each application's input or output to create a stream that becomes part of the Dante traffic.  It limits you to 32 channels, but that's still 30 more than you need for this particular device. (AVIO Adapter)

Dante VIA and Dante Virtual Sound Card can both be installed on the same computer, but ONLY ONE CAN BE ACTIVE AT ANY GIVEN TIME.

### Dante Controller (Required to Route Audio)

The Dante Controller detects all Dante enabled devices on your network and allows you to route audio using a matrix where each device "subscribes" to the stream that you choose.  In this case, it will see your computer if either Dante VIA or Dante VSC are active, and it will see any and all other Dante devices on your network if you happen to have them.  That could be another laptop running one of the software tools, or hardware such as the AVIO adapter, or even more advanced Dante hardware, which is unlikely in a home, but still possible.  In this case, Dante controller will see the AVIO controller on the network.

With Dante controller, you can specify which outputs on your laptop should be sent _to_ the adapter.

Note: Two computers cannot send audio simultaneously to the same AVIO adapter.  This is managed by Dante Controller, and any and all computers running the controller will see the same information, and any of those computers can control the configuration of the audio routing.  As an example, you could have Dante software on more than one computer, and one could easily route audio from one or the other to the AVIO adapter.  The cool thing about Dante is that one computer can send audio, and two other devices can subscribe to that stream...so you could have a computer receiving audio from another computer _and_ that same stream could be going to the AVIO adapter, or another computer, or...well, anywhere.

## Setup Notes

### Subnets

I.T. Guys like to isolate Dante traffic to its own VLAN.  In a home environment, that's not necessary because your network won't have enough traffic for it to be a problem.  However, all Dante devices need to be on the same subnet in order to work with each other.

So, if you have VLANS on your network, just make sure that you know how to route traffic between them, and make sure your transmitting computer and your receiving device (AVIO Adapter) are on the same subnet.

As an example, if your computer address is 192.168.1.20 and your Dante adapter is 192.168.2.12, you will have problems.  Make sure that your computer is on 192.168.2.X or vice versa and you're golden.

### Sample Rates

Two Dante devices must share the same sample rate in order to transmit audio between them.  If your laptop is sending at 48K, then the AVIO needs to be set to 48K, etc.  This is done on the configuration tab in the Dante Controller for each device.

More to come...

