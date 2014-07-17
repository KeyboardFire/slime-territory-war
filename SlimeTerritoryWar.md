# Slime: The Territory War

You are a glob of slime. Naturally, being slime, you want to ooze over as much area as possible. But there are 3 other slimes who want to do the exact same thing. Who will be the superior slime?

## Description

All slimes will be gathered into one arena. The judges (i.e. the control program) will go through an exhaustive list of all possible 4-slime combinations, place them on the corners of a table, and observe to see which slime oozes out over the most area.

Your slimes can do one of 3 actions each turn: spread out, jump, or merge. Further description on what these mean will be provided in the **Output** section.

## Board / Arena

The arena will be a square board (currently 8x8, but this may change in the future). Here is an example arena of a game in progress:

    11111222
    11111444
    11.22444
    .1222.4.
    333.3244
    33333.44
    333...44
    333....4
    
Slime is represented by the numbers 1 through 4 (players 1 to 4), and empty space is represented by a dot (`.`). Initially, the board starts out as all empty space except for a single unit of player 1's slime in the top left corner, player 2 in the top right, player 3 in the bottom left, and player 4 in the bottom right.

## Input

Your program's input will be which player you are (1, 2, 3, or 4), a comma (`,`), then the content of the board / arena (with newlines replaced with commas). For example, if you were player 3 in the above scenario, your input would be:

    3,11111222,11111444,11.22444,.1222.4.,333.3244,33333.44,333...44,333....4

## Output

Your program must output 4 integers. The first two are the X and Y coordinates of the slime you would like to move, and the next two are the X and Y coordinates of where you want to move them.

There are three choices you have on each turn: Spread out, jump, or merge.

- **Spread**

    To spread, the target coordinates must be exactly one square away from the slime being moved, and the square at the target coordinates must be empty space.
    
    When spreading, a new slime is created at the target coordinates and the old slime is *not* removed. After the new slime is created, all enemy slimes in the 8 squares around this new slime are converted to the player that moved.
    
    For example, with this board:
    
        11.22
        1..22
        ..22.
        .....
        
    If player 1 were to output `0 1 1 2`, the result would be:
    
        11.12
        1.112
        ..11.
        .....
	
- **Jump**

    To jump, the target coordinates must be exactly two squares away from the slime being moved, and the square at the target coordinates must be empty space.
    
    When jupming, a new slime is created at the target coordinates and the old slime is removed. After the new slime is created, all enemy slimes in the 8 squares around this new slime are converted to the player that moved.
    
    For example, with this board:
    
        11..2
        1...2
        ....2
        ...22
        
    If player 1 were to output `0 1 2 3`, the result would be:
    
        1...2
        1...1
        ...11
        ...11
    
- **Merge**

    To merge, the target coordinates must be exactly one square away from the slime being moved, and the square at the target coordinates must be the same player's slime.
    
    When merging, the old slime is removed. Then, all empty spaces in the 8 squares around the target slime are converted to the player that moved (not including the old slime being moved).
    
    For example, with this board:
    
        11..2
        1.1.2
        ....2
        ....2
        ..222
        
    If player 1 were to output `0 1 1 2`, the result would be:
    
        1.112
        11112
        .1112
        ....2
        ..222
        
You can also pass, by simply outputting invalid coordinates (ex. `0 0 0 0`).
        
## Scoring

When all squares on the board are filled, the game ends and scores are calculated. The final score of a player is the amount of squares that contain their slime at the end of the game.

At the end of the tournament, the scores from all games will be added up to calculate each player's final score, which will be posted on the leaderboard.

## Submitting an entry

In your entry, please include:

- A shell command to run your program (for example, `java MyBot.java`, `ruby MyBot.rb`, `python3 MyBot.py`, etc.).
 - Note that the input (your player and map) will be appended to this as a command line argument.
 - Programs will be tested on Ubuntu 14.04, so make sure your code can be run on it.
- Your bot's code.
- Instructions on how to compile the code, if necessary.

## Controller code / testing

The controller code is written in C++, and [can be found on Github](http://google.com/). Further instructions on how to run and test your code can be found there.

## Various rules

- You may read and write files within your own folder in order to persist data (submissions will be stored in `players/YourBotName/yourBotName.language`), but you may not modify or access anything else outside of it. Internet access is prohibited.
- Your submission may not be coded specifically to help or hurt another submission. (You may have multiple submissions, but they must not specifically interact with each other in any way.)
- Your submission must take no more than 0.1 seconds per turn. If your submission takes 0.105 seconds occasionally, that's fine, but it may not consistently take significantly longer than this time limit. (This is mainly a sanity check to avoid testing taking an overly long time.)
- Your submission must not be an exact duplicate (i.e. use the exact same logic) of another, even if it's in a different language.
- Your submission must be a serious submission. This is opinion-based, but if your submission is clearly not attempting to solve the challenge (ex. if you pass every turn), it will be disqualified.

If your submission breaks any of these rules or doesn't follow the specification, it will be disqualified, removed from `playerlist.txt`, and the game will restart from the beginning. If your submission is disqualified, I will leave a comment on your post explaining why. Otherwise, your submission will be added to the leaderboard. (If you don't see your submission on the leaderboard, have no explanatory comment on your post, and posted your submission before the "Last updated" time below, please tell me! I may have inadvertently overlooked it.)

## Example bot

`simplebot.rb`, a very simple bot that spreads or jumps a random slime to a random location each turn:

    me, board = ARGV.shift.split(',', 2)
    board = board.split(',').map{|row| row.split('') }
    bw = board.length
    bh = board[0].length

    meCoords = board.flat_map.with_index {|row, ri|
      row.map.with_index {|col, ci|
        (col == me) ? [ri, ci] : nil
      }.compact
    }

    spreadDeltas = [*-1..1].product([*-1..1]) - [0, 0]
    jumpDeltas = [*-2..2].product([*-2..2]) - spreadDeltas - [0, 0]
    spreadTargets = []
    jumpTargets = []
    meCoords.shuffle.each {|x, y|
      selBlock = ->dx, dy { (0...bw).include?(x + dx) && (0...bh).include?(y + dy) && board[x + dx][y + dy] == '.' }
      mapBlock = ->dx, dy { [x, y, x + dx, y + dy] }
      spreadTargets += spreadDeltas.select{|dx, dy| selBlock[dx, dy] }.map{|dx, dy| mapBlock[dx, dy] }
      jumpTargets += jumpDeltas.select{|dx, dy| selBlock[dx, dy] }.map{|dx, dy| mapBlock[dx, dy] }
    }
    
    if spreadTargets.length > 0
      puts spreadTargets.shuffle[0].join(' ')
    elsif jumpTargets.length > 0
      puts jumpTargets.shuffle[0].join(' ')
    else
      puts '0 0 0 0'
    end

## Leaderboard

There is no submission deadline; I'll continue to update the leaderboard periodically as new submissions come in.

*4 submissions are required before the leaderboard will appear.*

Last updated: N/A.