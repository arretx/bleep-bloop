DRAFT

## Introduction

This project involves adding multiple audio sources to a zone capable amplifier to deliver audio to one of 6 areas in the house.  In light of the fact that I'm using Home Assistant to automate our home, the one option that I could find for a controllable multi-zone amplifier that was designed for whole house audio was the Monoprice 6-Zone Multi-zone Home Audio Distribution system.

### Hardware Specific to this Project

- Home Assistant (About my configuration)
- Monoprice 6-Zone Rack Mount Amplifier
- Network to RS232 Converter to control Amplifier
- In-Ceiling Speakers

The amplifier includes IR capabilities and comes with cat5 connected zone controls, but it is not network compatible, so you need a device to convert your ethernet traffic from Home Assistant to RS232 commands which the amplifier can interpret.

