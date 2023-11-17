### DSC 80 Project
***By Katelyn Abille and Aneesh Pamula*** <br> <br>
Curated by students of DSC 80, this academic project is intended to demonstrate the process of cleaning and performing exploratory data analysis, assessing missingness, and conducting permutation tests. Our website stands as a holistic report of our findings.
<br> <br> 


### Table of contents
1. [Introduction](#introduction) <br>
    a. [Description of Raw Data](#data_desc) <br>
    b. [Description of Columns](#col_desc)
3. [Cleaning and EDA](#cleaning) <br>
    a. [Data Cleaning](#data_clean) <br>
    b. [Univariate Analysis](#uni_analysis) <br>
    c. [Bivariate Analysis](#bi_analysis) <br>
    d. [Interesting Aggregates](#aggr)
5. [Assessment of Missingness](#missingness)
6. [Hypothesis Testing](#hypothesis)

## **Introduction** <a name="introduction"></a>
League of Legends is a game with an extremely diverse cast of characters or "champions," each with their own strengths. Some have very high damage, but lack defenses. Some do not deal as much damage, but are very durable and have a variety of disruption tools. Certain champions have characteristics that make them very strong when played in a coordinated team setting. For example, Jinx can output a massive amount of damage from a long distance, but she often requires her teammates to protect her since she can be killed very quickly. Such champions are picked very often by professional players, who can rely on their teammates to coordinate with them.


Another one of these champions that is picked rather often in professional play is Renekton. With his great early game strength, high mobility, and a reliable immobilization tool, Renekton is a champion that many players have come to think of as a "pro play champion;" that is, one can expect to see Renekton fairly often when watching professional games.


A somewhat popular joke among League players is that such "pro play champions" are often buffed, or made stronger, in the patches leading up to the World Championships in the late fall. This idea stems from the idea that Riot Games (the publishers and developers of League of Legends) would want to buff champions that people are expecting to see at the World Championships, thus leading to a more engaging experience and increased viewership. In this project, we will examine whether this sentiment is true with regards to Renekton in 2022. Thus, we will be answering the question: <br>
**Is the champion Renekton "buffed" for World Championships?**

### **Description of Raw Data** <a name="data_desc"></a>
In order to answer this question, we will be using data from the 2022 League of Legends competitive matches provided by [Oracle's Elixir](https://oracleselixir.com/). The raw data set includes 123 columns of game statistics ranging from first bloods to kill-to-death ratios, with every 12 rows being data for two teams going head to head in one game. To break it down further, every 6 rows are statistics for the 5 players in a team plus a row for team aggregate statistics, and this goes on for a total of 149,400 rows corresponding to 12,450 different competitive matches!

For the purposes of our analysis, we will not be using a majority of these columns and will focus on a select few:

### **Description of Columns** <a name="col_desc"></a>

The columns in our cleaned data set are: 

* `'gameid'` : The unique ID assigned to every professional League game that is played.
* `'league'` : The league that the game was played in. This typically corresponds to region; for example, LCS is the tier 1 league for North America, and LCK is the tier 1 league for South Korea.
* `'date'` : The date that the game was played on.
* `'patch'` : The patch, or version, of the game that the game was played on.
* `'side'` The side of the map (blue or red) that the player's team was playing on.
* `'position'` : The position that the player was playing in. This could be 'top', 'jng' (short for 'jungle'), 'mid', 'bot' (short for 'bottom'),  or 'sup' (short for 'support')
* `'playername'` : The IGN (in-game name) of the player.
* `'playerid'` : The unique ID corresponding to the player.
* `'teamname'` : The name of the player's team at the time the game was played.
* `'teamid'` : The unique ID corresponding to the player's team.
* `'champion'` : The champion, or character, that the player was playing as.
* `'ban1'` - `'ban5'`: The 5 champions that the player's team chose to ban.
* `'gamelength'` : The length of the game, in seconds.
* `'win'` : Originally `'result'` in the raw data, represents whether the team won the game.


## **Cleaning and EDA** <a name="cleaning"></a>
### **Data Cleaning** <a name="data_clean">

|        | gameid                | league   | split   |   playoffs | date                |   patch | side   | position   | playername   | playerid                                  | teamname                 | teamid                                  | champion   | ban1    | ban2    | ban3   | ban4   | ban5   |   gamelength |   win |
|-------:|:----------------------|:---------|:--------|-----------:|:--------------------|--------:|:-------|:-----------|:-------------|:------------------------------------------|:-------------------------|:----------------------------------------|:-----------|:--------|:--------|:-------|:-------|:-------|-------------:|------:|
|      0 | ESPORTSTMNT01_2690210 | LCKC     | Spring  |          0 | 2022-01-10 07:44:08 |   12.01 | Blue   | top        | Soboro       | oe:player:38e0af7278d6769d0c81d7c4b47ac1e | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Renekton   | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 |     0 |
|      1 | ESPORTSTMNT01_2690210 | LCKC     | Spring  |          0 | 2022-01-10 07:44:08 |   12.01 | Blue   | jng        | Raptor       | oe:player:637ed20b1e41be1c51bd1a4cb211357 | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Xin Zhao   | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 |     0 |
|      2 | ESPORTSTMNT01_2690210 | LCKC     | Spring  |          0 | 2022-01-10 07:44:08 |   12.01 | Blue   | mid        | Feisty       | oe:player:d1ae0e2f9f3ac1e0e0cdcb86504ca77 | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | LeBlanc    | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 |     0 |
|      3 | ESPORTSTMNT01_2690210 | LCKC     | Spring  |          0 | 2022-01-10 07:44:08 |   12.01 | Blue   | bot        | Gamin        | oe:player:998b3e49b01ecc41eacc392477a98cf | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Samira     | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 |     0 |
|      4 | ESPORTSTMNT01_2690210 | LCKC     | Spring  |          0 | 2022-01-10 07:44:08 |   12.01 | Blue   | sup        | Loopy        | oe:player:e9741b3a238723ea6380ef2113fae63 | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Leona      | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 |     0 |
|    ... |                   ... |      ... |     ... |        ... |                 ... |     ... |    ... |        ... |          ... |                                       ... |                      ... |                                     ... |        ... |     ... |     ... |    ... |    ... |    ... |          ... |   ... |
| 149393 | 9687-9687_game_5      | DCup     | nan     |          0 | 2022-12-27 12:43:43 |   12.23 | Red    | top        | Bin          | oe:player:fb66ef5885b4be9323905b821dc3a42 | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Jax        | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 |     1 |
| 149394 | 9687-9687_game_5      | DCup     | nan     |          0 | 2022-12-27 12:43:43 |   12.23 | Red    | jng        | XUN          | oe:player:814f33b3479f1ebf6cd22cba78f30e0 | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Vi         | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 |     1 |
| 149395 | 9687-9687_game_5      | DCup     | nan     |          0 | 2022-12-27 12:43:43 |   12.23 | Red    | mid        | Yagao        | oe:player:4860d1660ce45bd15a600953309135c | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Ahri       | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 |     1 |
| 149396 | 9687-9687_game_5      | DCup     | nan     |          0 | 2022-12-27 12:43:43 |   12.23 | Red    | bot        | Elk          | oe:player:a58a0d41103cdc5b58cbb547eeaca2d | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Varus      | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 |     1 |
| 149397 | 9687-9687_game_5      | DCup     | nan     |          0 | 2022-12-27 12:43:43 |   12.23 | Red    | sup        | ON           | oe:player:90651ebea9a35ec4e018c8157492e17 | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Ashe       | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 |     1 |
league

### **Univariate Analysis** <a name="uni_analysis">

### **Bivariate Analysis** <a name="bi_analysis">

### **Interesting Aggregates** <a name="aggr">

| teamname                  |   False |   True |
|:--------------------------|--------:|-------:|
| 100 Thieves               |      40 |      3 |
| 100 Thieves Academy       |      66 |      2 |
| 100 Thieves Next          |      58 |      1 |
| 1907 Fenerbahçe Academy   |       2 |      0 |
| 300                       |       5 |    nan |
| ...                       |     ... |    ... |
| paiN Gaming Academy       |      40 |      2 |
| piratesports              |       2 |      0 |
| unknown team              |     152 |      8 |
| İstanbul Wildcats         |      40 |      2 |
| İstanbul Wildcats Academy |      25 |      1 |

## **Assessment of Missingness** <a name="missingness"></a>
test text


## **Hypothesis Testing** <a name="hypothesis"></a>
test text

