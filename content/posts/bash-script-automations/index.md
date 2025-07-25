+++
title = 'Bash Scripting Automation'
date = 2023-10-10
tags = ['Bash', 'Linux', 'Automation', 'Productivity']
draft = false
+++

## Introduction
On our machines, we often perform a very similar - if not identical - set of standard actions to complete our daily activities, this is especially true for programmers. Regardless of your programming language of choice, you will often find yourself opening the following programmes up as a part of setting up a coding session:
- A terminal instance
- A browser window
- Your IDE or text editor of choice

Once open you also typically organise them into your preferred layout on the display(s).

These actions are as simple as they get, easy, and even relatively quick, so you may wonder why you would even bother trying to make this set of activities more streamlined. As with any repetitive activity I often find myself thinking - could this be better/faster/easier? The answer: absolutely yes. Bash (a.k.a. shell) scripts are a powerful tool, at their core it is a way to automate sequences of commands while having the added advantage of using variables and logic to achieve more advanced results than you would usually attempt in a terminal. 

So what can we *actually* achieve with this approach? The sky is the limit, but with the example I opened with, this sequence of tasks can be transformed from 6+ clicks, 3+ cursor drags and 10+ seconds into 1 click or keyboard shortcut, all performed in under a second. 

In addition to the pure time and keystrokes/clicks saved, you also get a less tangible benefit - in automating these tasks you have avoided the mental overhead of locating, panning to, clicking and dragging each of the programmes, instead, I find myself starting off the session off on the right foot with a small psychological boost from appreciating the efficiency of my bash script. 

I often hear Vim users speaking about their config in a similar way, citing that actions can be performed at the 'speed of thought' - i.e. the only barrier between me and starting a coding session is just the time taken to think of wanting to code and pressing the macro button on my mouse.

I'll go into some of my most used bash scripts to highlight some different simple use cases. There are far more complex tasks that can be achieved by using the same approach which may end up saving you minutes per run rather than a few seconds - but even more micro-optimisations like the ones I will cover add up and are a great way to get your machine to do the heavy lifting and spare your time and energy, allowing you to focus on the programming itself.

## How to create a Bash script
To start a bash script, create an empty file with a `.sh` extension.

Your first line will specify the interpreter, which is bash for all of the examples used.

`#!/bin/bash`

Everything after the first line will form the commands you wish to execute.

```Shell
#!/bin/bash
echo "Hello World!"
```
To make the script executable, run the following command in your terminal: `chmod u+x scriptname.sh`

To get some inspiration, check out [Devhint's Bash Cheatsheet](https://devhints.io/bash), an excellent resource for an overview of syntax and functionality available to you.

## Examples
As a heads up, I use PopOS, so some commands may need to be adapted for your particular setup.
### Start coding session
Probably my most used bash script, I have this bound to the primary macro button on my mouse. The script:
- Opens my IDE: VSCode
- Launches a terminal instance and starts [tmux](https://github.com/tmux/tmux/wiki)
- Opens Firefox
- Uses keyboard shortcuts to move/snap the windows into my preferred positions on my displays
- Includes small delays to prevent any unexpected movement while programs are loading

```Shell
#!/bin/bash
code
xte 'keydown Shift_L' 'keydown Super_L' 'key Right' 'key m' 'keyup Shift_L' 'keyup Super_L' 
gnome-terminal -- "tmux"
sleep 0.1
xte 'keydown Control_L' 'keydown Super_L' 'key Left' 'keyup Control_L' 'keydown Shift_L' 'key Left' 'keyup Shift_L' 'keyup Super_L'
firefox
sleep 0.3
xte 'keydown Control_L' 'keydown Super_L' 'key Right' 'keyup Control_L' 'keydown Shift_L' 'key Left' 'keyup Shift_L' 'keyup Super_L' 
```

### Initialise a Python Project
The vast majority of my Python projects start with the same steps:
- Create a project directory
- Create a virtual environment
- Install a couple of go-to libraries e.g. `autopep8`
- Initialise an empty git repository
- Create an empty `README.md` file
- Copy a general purpose Python `.gitignore` file
- Open the directory with VSCode

```Shell
#!/bin/bash
DIR=~/Documents/code/$1

if [ $# -eq 0 ]
  then
    echo "Project name must be provided."
    exit
fi

if  [ ! -d "$DIR" ]; then
    mkdir -p $DIR
    python3 -m venv $DIR/venv
    source $DIR/venv/bin/activate
    pip3 install pipreqs
    pip3 install autopep8
    git init $DIR
    touch $DIR/README.md
    cp "$( dirname -- "$( readlink -f -- "$0"; )"; )/Py.gitignore" $DIR/.gitignore
    code $DIR
else
    echo "Project directory '$1' already exists."
fi
```
This script introduces a variable and some logic to ensure a project name is both provided and unique, otherwise an error is returned.

### Open existing Python Project
```Shell
#!/bin/bash
# When executing add source before calling the script
# to cd in and activate venv in the current terminal.
# Alternatively this can be added in the alias command .bashrc
DIR=/home/artfvl/Documents/code/$1
cd $DIR
source venv/bin/activate
```

### Run a JavaScript Project
```Shell
#!/bin/bash
# When executing add source before calling the script
# to cd in and activate venv in the current terminal.
# Alternatively this can be added in the alias command .bashrc
DIR=/home/artfvl/Documents/code/$1
cd $DIR
npm run dev
```
## Github Repo
{{< github repo="tomdeabreucodes/bash-scripts" >}}

## Conclusion
I hope this gives you a useful insight into Bash scripts and some practical use cases. Next time you find yourself getting frustrated at a repetitive task, challenge yourself if it could be offloaded to your machine - happy automation!