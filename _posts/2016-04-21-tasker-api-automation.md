---
layout: post
title: API Automation Using Tasker And Voice Input
category: Misc
tags: [automation]
---

As an avid board gamer, I pay attention to the rankings on [BoardGameGeek](https://boardgamegeek.com/browse/boardgame) to keep up-to-date on what the new and popular games are. Additionally, the overall rankings provide me with a general idea of whether a game is roughly good or bad when making a buying decision. But looking this data up is slightly cumbersome as it requires searching for the game and bringing up its page. Combining this with my recent discovery of the [AutoVoice](https://play.google.com/store/apps/details?id=com.joaomgcd.autovoice&hl=en) Tasker plugin, I decided to make a handy assistant that could provide me this information more easily and quickly.

## Purpose

I wanted to create a voice command that would hook into Google Voice. The command would listen for the phrase "BGG rank", followed by a board game name and simply provide me with it's board game rank from the BoardGameGeek website. It can get tripped up with difficult names, or games re-released at different times, but these are limitations with the API.

Writing this command had me interacting with multiple APIs, parsing vocal input, and using Regex, all within Tasker. I've included the details of each step in order to follow along. The only requirements for setting this up are to install Tasker and its plugin AutoVoice. Note that AutoVoice will require the paid version - see [Step 1](#retrieving-game-rank-part-1). 

Additionally, I included a gist at the end of the final result XML exported from Tasker.

## Steps

### Create Listening Command
The first step was having AutoVoice listen to for my keywords "BGG rank". This was easy enough to do by simply setting the `Command Filter` on an `AutoVoice Recognized` context. This would run the associated task whenever it heard that phrase. Even better, it would also automatically populate the various AutoVoice variables (eg `%avcommnofilter` and `%avcomm`) with the spoken text.

At this point if the task is wired up to show these variables, it should show the text properly. But that is not the case. It turns out if you only use the trial version of AutoVoice, it will truncate the search string to 4 (non-space) _characters_. Meaning if you spoke "BGG rank Agricola", you would only get "BGG r". Because of this, a full version of the plugin is required.

### Retrieving Game Rank (Part 1)
There is no direct way to go from search term to board game rank using the [BGG XML API](https://boardgamegeek.com/wiki/page/BGG_XML_API). Instead it requires a call to retrieve the board game ID and then a call to get the stats for the game.

The board game ID can be retrieved using the search API https://www.boardgamegeek.com/xmlapi/search?search=&lt;GameSearch&gt;. But first, we need to do a simple `Variable Search Replace` to convert any spaces to URL encoded spaces as the name will be in the query string.

Once encoded,  it can simply be injected into the URL and called via an `HTTP Get` action. Additional filtering for the API does not currently exist, so the result will return an unsorted, unfiltered list of results.

### Retrieving Game Rank (Part 2)

From the result of the first API call, the game's ID can be retrieved. An example output searching for "Pandemic Legacy" (https://www.boardgamegeek.com/xmlapi/search?search=Pandemic%20Legacy) is shown below:

```xml
<boardgames termsofuse="http://boardgamegeek.com/xmlapi/termsofuse">
	<boardgame objectid="161936">
		<name primary="true">Pandemic Legacy: Season 1</name>
		<yearpublished>2015</yearpublished>
	</boardgame>
</boardgames>
```
The ID can be retrieved by a Regex using positive lookbehind like this: `(?<=\bobjectid=")[^"]*`.

With the ID, a second API call can be made to https://www.boardgamegeek.com/xmlapi/boardgame/&lt;ID&gt; to retrieve the game's stats. A sample response from our example, with some extraneous data removed for brevity, looks like this:

```xml
<boardgames termsofuse="http://boardgamegeek.com/xmlapi/termsofuse">
    <boardgame objectid="161936">
        ...
        <statistics page="1">
            <ratings>
                <ranks>
                    <rank bayesaverage="8.34145" friendlyname="Board Game Rank" id="1" name="boardgame" type="subtype" value="1"/></ranks>
            </ratings>
        </statistics>
    </boardgame>
</boardgames>
```

The rank in question lies in the "Board Game Rank" rank element in the statistics. This can be parsed out via Regex again, or done via two `Variable Search Replace` actions. To vary things up, the latter was chosen in the final result here.

With the rank finally in hand, it can be outputted via the `Say` action.

## Conclusion

This somewhat complicated automation demonstrated the relative ease with which any task can be converted into a voice command. With the extensive buildout of Tasker actions and third party plugins, many things which used to require days worth of work and wan insubstantial amount of coding can be done in an afternoon.

Additionally, it is worth commenting on the brittleness of the solution. Parsing XML using Regex is typically a [bad idea](http://stackoverflow.com/questions/8577060/why-is-it-such-a-bad-idea-to-parse-xml-with-regex) and it also relies on the structure of the two API calls to not change. But between Tasker's handy debugging features and the simplicity of the task, this is something that can be worried about when the problem finally crops up. The amount of actual code that would be needed to be written and used in Tasker to make the solution more robust would outweigh the benefits.

## Script

{% gist 865e4f8aa9b7da77439c3f498819b1c5 %}
