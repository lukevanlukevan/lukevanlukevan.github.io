---
layout: page
title: Building a QMK/Vial firmware from scratch
permalink: qmk-vial-guide
---

> This guide assumes you have the QMK CLI installed and have forked your own [qmk-vial](https://github.com/vial-kb/vial-qmk) repository.

## 0. Environment

### Windows
You will have QMK MSYS installed. What you need to do is point MSYS to your Vial fork rather than the QMK one. You can use `qmk setup -H <YOUR PATH HERE>` to do this. For example: `qmk setup -H /d/Code/vial-qmk`. It doesn't like the colons, but the path will be correct when you hit enter.

### Mac

Install the QMK CLI tools, then inside your IDE or Terminal of choice: Point QMK to your Vial fork rather than the QMK one. You can use `qmk setup -H <YOUR PATH HERE>` to do this. For example: `qmk setup -H /d/Code/vial-qmk`.

## 1. Create new keyboard

I opt for opening the `vial-qmk` folder in VS Code and then using the terminal there, you can do as you please.

To make a new keyboard, you can use `qmk new-keyboard`, this will as you for a keyboard name. this can be whatever you want, but its good practice to put all your builds under a named folder. Most go for their GitHub usename. I will call this example `fleetline`, so for the name I put `lukevanlukevan/fleetline`.

Then it will ask a few things around names and usernames, fairly self explanatory.

When you get to the base layout question, this is where it can really help you. For this example, I selected `ortho_4x12`. The reason this is helpful is you'll get a few pre generated files that will make the whole process a bit faster.

Next is "What powers your project?" mine is a ProMicro, so I select that. If you are unsure, there are many places to find out. Googling your microcontroller will yeild answers.

