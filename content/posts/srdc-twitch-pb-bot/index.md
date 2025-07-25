+++
title = 'SRCommand: A Self-hosted, Customisable Speedrun PB Bot for Twitch'
date = 2023-11-10
tags = ['Python', 'API', 'Django', 'Speedrunning', 'Open Source']
draft = false
+++

## The goal
Speedrunning is a niche rich with stats, and the one that counts the most is your Personal Best (PB) time in your game of choice. While there are some exceptions, the standard is for communities to have their game set up on Speedrun.com (SRDC) a speedrunning leaderboard website. 

Established speedrunning communities often have a presence on Twitch - the leading live-streaming platform - where viewers will cheer on their favourite streamers from the sidelines via the chat.

Another staple of Twitch chat is the bots, automated assistants that perform helpful functions for the audience/streamer. One particular type of bot I saw in a couple of different communities, including ones I actively participated in, was PB bots. Commands are used to retrieve the PB of the streamer. This piqued my interest and I saw potential in a more advanced version of this.

**I set out to create a PB bot which could be customised for any game on SRDC, while preventing hard coding and the constant reinvention of the wheel.**

## Approach
A simple REST API was the ideal solution in my opinion, with the public SRDC API as the data source and a minimal Django app to configure your desired games and categories, as well as handle and structure the data before sending it onwards for use on Twitch. While Twitch was my primary use case, it is trivial to adapt it for use on other platforms, such as Discord.

## Tools
- Python
- Django
- Django Rest Framework
- Python Anywhere (Recommended for free hosting, but could be deployed anywhere the user chooses)

## The Code
The code for this project is open source under the MIT licence and is intended for self-hosting. Detailed setup instructions have been documented in the project's README.
{{< github repo="tomdeabreucodes/SRCommand" >}}

## Features
As I mentioned, I had seen simple implementations of PB bots previously, but I saw opportunities for improvement. At the core of my project, I wanted it to embed these key features:
- Easy customisation of games and categories you want your bot to include
- Additional customisation of display names used in queries (to accommodate common abbreviations e.g. Super Mario Bros. -> SMB)
- Ability to search both the streamer's PB (default behaviour) 
- Plus option to specify another runner's username
- Completely free to set up and use

## Usage
### Configuration
The configuration is an initial setup, where you choose the games, categories and command names. The user only needs to return to this if they want to update any of these details but they will otherwise be stored in a SQLite database in their instance of the bot.
This is completed in a *very* simple, albeit intuitive GUI shown below, achieved with Django templating.

*Adding a game*
![Adding a game to SRCommand config](img/SRCommand_Game_Config.png)

*Adding a category*
![Adding a category](img/SRCommand_Game_Config.png)

*For more complex categories filters can be applied to give more control to the user*
![Adding a filter](img/SRCommand_Filter_Config.png)

### Commands
Once your command is set up in your general-purpose Twitch bot of choice, chatters in your stream will now be able to easily query PBs without having to switch tabs or close the mobile app!

*Example API response*
![Command execution example response](img/SRCommand_Response.png)

## Caveats
The SRDC API is versioned, and hopefully, any breaking changes will not be introduced into the version this tool is dependent on (V1). In case they are, this may result in unexpected or incomplete responses. Please feel free to raise an issue on Github if you notice anything. The way your general-purpose bot calls APIs may also change over time, so you may also find that an issue could be caused by this.

SRDC communities are fairly autonomous and have different ways to structure games and categories, some of which may not be arranged in a way that aligns with the design of SRCommand. I have attempted to cover a broad range of popular configurations, while not testing for more unique setups. As the project is open source, should you wish to adapt it, I would encourage you to fork the project.

Thanks for reading!