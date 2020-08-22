Introduction
March Madness is the annual NCAA basketball competition. 64 teams (really 68, with 4 qualifying games) compete in a bracket style tournament to win 6 games and subsequently the tournament. Big bucks are at stake : in 2014 Warren Buffet offered $1,000,000,000 (one Billion dollars!) to anyone who could predict a perfect bracket (every game predicted correctly). In 2018 he cheapened out, and offered only $1,00,000 a year for life for a perfect bracket.
Data
You are provided with basketball game data from 2003 to 2017. We will use 2003 - 2014 as the training set, and 2015-2017 as the test set.
‘RegularSeasonDetailedResults.csv’ contains the game results and stats for main season games. We will use each team’s average main season yearly performance statistics as thepredictors for which team will win the tournament games.
‘NCAATourneyDetailedResults.csv’ has the outcome statistics for the March Madness games. ‘NCAATourneySlots.csv’ and ‘NCAATourneySeeds.csv’ show how the tournament games are conducted. ‘Teams.csv’ shows which teams coorespond to which unique Ids.
Because there is extensive data cleaning and processing necessary for this lab, I have provided you the majority of the code for these steps, with some gaps for you to fill in to ensure you understand how the code is working.
All the data is loaded to the server if you choose to use it.
