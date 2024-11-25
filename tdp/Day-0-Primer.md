In my personal opinion, python in Houdini has been one of the most rewarding things to learn. In Houdini, python is a very open ended and well documented, and allows you to customize and enhance your daily experience in the software.

The aim of this advent challenge is to take anyone from nothing to comfortable building tools to aid your experience.

If you are interesting in following this, I suggest downloading this template package, and following the instructions on getting installed.

There are no rules, except to join the Discord, ask questions, and have fun!

Each day will ideally have a "Extra Info" section with some useful things at a similar level of difficulty to the days info. Read these and try and implement them where you see fit.

### **Prompt**: Plan out some common pain points or workflow slowdowns to optimize in your personal pipeline.

## Installing the template package

Download the template package from [here](https://www.dropbox.com/scl/fo/gqg5ehh1z6523cg22a8xm/AEOTHMObxHn8SGb_5ZdppyI?rlkey=jceug0kd1tcotlny88guo1sdr&dl=0), and place it somewhere in your assets folder. Open the TDP.json. Inside it, it will look like this:

```json
{
    "env": [
        {
            "TDP": "path/to/folder"
        }
    ],
    "path": "$TDP"
}
```

> Replace `path/to/folder` with the actual path to the TDP folder, for example: `D:/Assets/TDP`, and then copy this .json file to your Houdini preference folder (`houdini20.5/packages`)

For this template, I am using the name **T**welve **D**ays of **P**ython, so TDP. We set the environment variable up with this name, and in most cases, I will prefix any file names with this, this ensures all the references to files down the line wont be ambiguous. eg. `utils.py` is probably going to exist somewhere, so calling it `TDPUtils.py` has as much higher chance of being unique.

**Verify the install by opening Houdini, pressing CTRL+S and typing $TDP in the path, and check if it directs you to the correct folder.**