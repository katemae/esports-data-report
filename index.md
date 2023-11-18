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
5. [Assessment of Missingness](#assess_missingness) <br>
    a. [NMAR Analysis](#NMAR) <br>
    b. [Missingness Dependency](#missingness) <br>
&nbsp;  i.  [Reject the Null](#reject_null) <br>
&nbsp;  ii. [Fail to Reject the Null](#fail_reject_null)
6. [Hypothesis Testing](#hypothesis)

## **Introduction** <a name="introduction"></a>
League of Legends is a game with an extremely diverse cast of characters or "champions," each with their own strengths. Some have very high damage, but lack defenses. Some do not deal as much damage, but are very durable and have a variety of disruption tools. Certain champions have characteristics that make them very strong when played in a coordinated team setting. For example, Jinx can output a massive amount of damage from a long distance, but she often requires her teammates to protect her since she can be killed very quickly. Such champions are picked very often by professional players, who can rely on their teammates to coordinate with them.


Another one of these champions that is picked rather often in professional play is Renekton. With his great early game strength, high mobility, and a reliable immobilization tool, Renekton is a champion that many players have come to think of as a "pro play champion;" that is, one can expect to see Renekton fairly often when watching professional games.


A somewhat popular joke among League players is that such "pro play champions" are often buffed, or made stronger, in the patches leading up to the World Championships in the late fall. This idea stems from the idea that Riot Games (the publishers and developers of League of Legends) would want to buff champions that people are expecting to see at the World Championships, thus leading to a more engaging experience and increased viewership. In this project, we will examine whether this sentiment is true with regards to Renekton in 2022. Thus, we will be answering the question: <br>
**Is the champion Renekton "buffed" for World Championships?**

### **Description of Raw Data** <a name="data_desc"></a>
In order to answer this question, we will be using data from the 2022 League of Legends competitive matches provided by [Oracle's Elixir](https://oracleselixir.com/). The raw data set includes 123 columns of game statistics ranging from first bloods to kill and death counts, with every 12 rows being data for two teams going head to head in one game. These 12 rows can also be broken down into two groups of 6 rows, each of which consists of individual statistics for the 5 players in a team plus a row for team aggregate statistics over that game, and this goes on for a total of 149,400 rows corresponding to 12,450 different competitive matches!

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

As mentioned earlier, this dataset contains far more information than what is necessary to answer our core question. For the purposes of this analysis, we removed rows containing aggregate team statistics, as well as the columns `'datacompleteness'`, `'url'`, `'participantid'`, `'year'`, `'split'`, `'playoffs'`, and `'game'`. We also ignored the columns that refer to other game statistics, such as gold difference, dragons, towers, etc., since they will also not factor into our analysis. Additionally, we cast the values in the `'result'` column, which contained ones and zeroes, to booleans, and renamed that column to `'win'` to make its values make more sense.

We removed the unnecessary rows and columns in the raw data, modified the `'result'` column, and stored the values we obtained in the DataFrame `'league`', which can be seen below:

|        | gameid                | league   | split   | playoffs   | date                |   patch | side   | position   | playername   | playerid                                  | teamname                 | teamid                                  | champion   | ban1    | ban2    | ban3   | ban4   | ban5   |   gamelength | win   |
|-------:|:----------------------|:---------|:--------|:-----------|:--------------------|--------:|:-------|:-----------|:-------------|:------------------------------------------|:-------------------------|:----------------------------------------|:-----------|:--------|:--------|:-------|:-------|:-------|-------------:|:------|
|      0 | ESPORTSTMNT01_2690210 | LCKC     | Spring  | False      | 2022-01-10 07:44:08 |   12.01 | Blue   | top        | Soboro       | oe:player:38e0af7278d6769d0c81d7c4b47ac1e | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Renekton   | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 | False |
|      1 | ESPORTSTMNT01_2690210 | LCKC     | Spring  | False      | 2022-01-10 07:44:08 |   12.01 | Blue   | jng        | Raptor       | oe:player:637ed20b1e41be1c51bd1a4cb211357 | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Xin Zhao   | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 | False |
|      2 | ESPORTSTMNT01_2690210 | LCKC     | Spring  | False      | 2022-01-10 07:44:08 |   12.01 | Blue   | mid        | Feisty       | oe:player:d1ae0e2f9f3ac1e0e0cdcb86504ca77 | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | LeBlanc    | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 | False |
|      3 | ESPORTSTMNT01_2690210 | LCKC     | Spring  | False      | 2022-01-10 07:44:08 |   12.01 | Blue   | bot        | Gamin        | oe:player:998b3e49b01ecc41eacc392477a98cf | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Samira     | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 | False |
|      4 | ESPORTSTMNT01_2690210 | LCKC     | Spring  | False      | 2022-01-10 07:44:08 |   12.01 | Blue   | sup        | Loopy        | oe:player:e9741b3a238723ea6380ef2113fae63 | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Leona      | Karma   | Caitlyn | Syndra | Thresh | Lulu   |         1713 | False |
|    ... |                   ... |      ... |     ... |        ... |                 ... |     ... |    ... |        ... |          ... |                                       ... |                      ... |                                     ... |        ... |     ... |     ... |    ... |    ... |    ... |          ... |   ... |
| 149393 | 9687-9687_game_5      | DCup     | nan     | False      | 2022-12-27 12:43:43 |   12.23 | Red    | top        | Bin          | oe:player:fb66ef5885b4be9323905b821dc3a42 | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Jax        | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 | True  |
| 149394 | 9687-9687_game_5      | DCup     | nan     | False      | 2022-12-27 12:43:43 |   12.23 | Red    | jng        | XUN          | oe:player:814f33b3479f1ebf6cd22cba78f30e0 | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Vi         | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 | True  |
| 149395 | 9687-9687_game_5      | DCup     | nan     | False      | 2022-12-27 12:43:43 |   12.23 | Red    | mid        | Yagao        | oe:player:4860d1660ce45bd15a600953309135c | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Ahri       | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 | True  |
| 149396 | 9687-9687_game_5      | DCup     | nan     | False      | 2022-12-27 12:43:43 |   12.23 | Red    | bot        | Elk          | oe:player:a58a0d41103cdc5b58cbb547eeaca2d | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Varus      | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 | True  |
| 149397 | 9687-9687_game_5      | DCup     | nan     | False      | 2022-12-27 12:43:43 |   12.23 | Red    | sup        | ON           | oe:player:90651ebea9a35ec4e018c8157492e17 | Bilibili Gaming          | oe:team:d356a144644879dabb5f34cd99c886d | Ashe       | K'Sante | Syndra  | Lucian | Gwen   | Sylas  |         1778 | True  |

### **Univariate Analysis** <a name="uni_analysis">

We can look at the counts of each value in the `'champions'` column to see which champions were picked the most often during the 2022 season. The graph containing all champions can be seen below:

<iframe src="assets/fig/champ_counts_fig.html" width=800 height=600 frameBorder=0></iframe>

League of Legends has over 160 playable champions, which makes this graph rather difficult to read. To make it simpler, let's look at only the fifteen most and least picked champions.

The graph displaying the 15 most picked champions cna be seen here:
<iframe src="assets/fig/top_champ_counts_fig.html" width=800 height=600 frameBorder=0></iframe>

And the graph displaying the 15 least picked champions cna be seen here:
<iframe src="assets/fig/bot_champ_counts_fig.html" width=800 height=600 frameBorder=0></iframe>

These graphs shows that champions such as Nautilus and Jinx were picked very often, possibly alluding to their overall strength in pro games. Meanwhile, champions such as Warwick and Teemo are not picked very often. Our main focus, Renekton, sits around the middle of the graph.

<br>

However, it must be noted that simply the pick rate of a champion does not on its own correlate to their strength. Many times, popular champions will be "banned" from being chosen, so to examine the overall prescence of any given champion, we can look at a different statistic that takes this into account (but we will get to this later, during the hypothesis testing section).

Next, we can examine how long pro League games usually go on for.

Below is a box plot depicting the distribution of game times (in minutes) of games played during the 2022 season.

<iframe src="assets/fig/game_times_fig.html" width=800 height=600 frameBorder=0></iframe>

As we can see here, many pro League games average to around 30 minutes in length, with a roughly right skewed distribution. This is important to consider because certain champions (like Renekton) are stronger in the earlier stages of the game (i.e. the first 25 or so minutes) than in the later stages of the game. If many games go on for a long time, then it may affect Renekton's overall strength, and by extension, whether he received buffs before Worlds.

### **Bivariate Analysis** <a name="bi_analysis">

We can also take a look at the distribution of games played on each patch.

Patches are modifications to champions or systems in the game with the intent of making the game more balanced and fun to play. These patches are released on a (approximately) two week cycle, which makes them an effective method of analyzing the peaks and valleys of the League pro scene.

Below, we can see the number of games played on each patch. From this, we can see that many games were played on the patches at the beginning of the year, then fewer game were played, but games spiked around patch 12.12 or 12.13.

There is also a spike around 12.17 to 12.18, which corresponds to the patches that were released right before Worlds (the world championships).

<iframe src="assets/fig/patch_counts_fig.html" width=800 height=600 frameBorder=0></iframe>

Another statistic we could analyze is which champions had the highest win rates. To ensure that we only analyze champions with a decent sample size, we will take the champions with at least 100 picks from `'champ_counts'`. The graph containing all champions can be seen below:

<iframe src="assets/fig/champ_wins_fig.html" width=800 height=600 frameBorder=0></iframe>

There are still very many champions that fall under this criteria. Like before, let's only look at the champions with the highest and lowest fifteen winrates.

The top 15 are here:
<iframe src="assets/fig/top_champ_wins_fig.html" width=800 height=600 frameBorder=0></iframe>

And the bottom fifteen are here:
<iframe src="assets/fig/bot_champ_wins_fig.html" width=800 height=600 frameBorder=0></iframe>

Keep in mind that winrate is not the only factor that determines the strength of a champion. Some champions are only played as counters to other champions, and thus naturally have a higher winrate, since players only play those champions into favorable matchups.

For example, the pick rate of Darius, the champion with the highest winrate, is very low--only 181 games. This is because in many cases, Darius is picked only when the player picking him knows--based the enemy team's champion picks and bans--that they will be able to perform well on him.

### **Interesting Aggregates** <a name="aggr">

Let's take a look at how many games each team won with and without picking Renekton.

**Won With Renekton Count**

| teamname                  |   False |   True |
|:--------------------------|--------:|-------:|
| 100 Thieves               |      40 |      3 |
| 100 Thieves Academy       |      66 |      2 |
| 100 Thieves Next          |      58 |      1 |
| 1907 Fenerbahçe Academy   |       2 |      0 |
| 300                       |       5 |      0 |
| ...                       |     ... |    ... |
| paiN Gaming Academy       |      40 |      2 |
| piratesports              |       2 |      0 |
| unknown team              |     152 |      8 |
| İstanbul Wildcats         |      40 |      2 |
| İstanbul Wildcats Academy |      25 |      1 |

From this table, we can see that teams often win more games when not picking Renekton than they do when picking Renekton. This seems inconsistent with our initial impression of Renekton--if he is such a strong champion in pro play, why are teams winning more games when they don't play him?

<br>

The answer is simple--teams simply play far more games without picking Renekton than they do when picking Renekton. 

Let's modify our pivot table to instead show the number of games each team **played** with and without Renekton.

**Picked Renekton Count**

| teamname                  |   False |   True |
|:--------------------------|--------:|-------:|
| 100 Thieves               |      71 |      5 |
| 100 Thieves Academy       |     113 |      4 |
| 100 Thieves Next          |      76 |      1 |
| 1907 Fenerbahçe Academy   |       2 |      1 |
| 300                       |      12 |      0 |
| ...                       |     ... |    ... |
| paiN Gaming Academy       |      53 |      2 |
| piratesports              |       7 |      1 |
| unknown team              |     281 |     13 |
| İstanbul Wildcats         |      57 |      6 |
| İstanbul Wildcats Academy |      39 |      4 |

There could be numerous reasons for a team playing many games without Renekton. For example, the opposing team may have simply chosen to ban Renekton. If they did not ban Renekton, they likely deliberately chose champions that fared well against Renekton, thus making Renekton a less viable choice.

Indeed, if we divide the values in the first table by the values in the second table:

**Renekton Win Rate**

| teamname                  |    False |       True |
|:--------------------------|---------:|-----------:|
| 100 Thieves               | 0.56338  |   0.6      |
| 100 Thieves Academy       | 0.584071 |   0.5      |
| 100 Thieves Next          | 0.763158 |   1        |
| 1907 Fenerbahçe Academy   | 1        |   0        |
| 300                       | 0.416667 | nan        |
| ...                       |      ... |        ... |
| paiN Gaming Academy       | 0.754717 |   1        |
| piratesports              | 0.285714 |   0        |
| unknown team              | 0.540925 |   0.615385 |
| İstanbul Wildcats         | 0.701754 |   0.333333 |
| İstanbul Wildcats Academy | 0.641026 |   0.25     |

We can see that in some cases, the teams win a greater percentage of their games when they pick Renekton, and in other cases, they win a greater percentage when they don't pick Renekton.

<br>

The NaN values in this case represent situations where teams did not pick Renekton, and thus have an undefined win rate with him.

## **Assessment of Missingness** <a name="assess_missingness"></a>
Some of the data in our dataset are missing; however, we don't necessarily know whether there is an explicit pattern to this missingness, or whether these data are missing at random (either completely or conditional on the values in another column).

### **NMAR Analysis** <a name="NMAR"></a>
We can conclude, based on domain knowledge, that the values in the `'teamname'` column are likely not missing at random, or NMAR. More popular teams, such as T1 or Team Liquid, are less likely to have their team names missing, since their games are more likely to be covered. On the contrary, lesser known teams are less likely to have their games covered, so their team name is more likely to be missing.


### **Missingness Dependencys** <a name="missingness"></a>
We will examine a few of the columns in our dataset to determine whether the values they are missing are dependent on another column, or missing at random (MAR). 

#### **Reject the Null** <a name="reject_null"></a>

**Comparing missingness of `'playerid'` to `'league'`**

| league   |   playerid_missing = False |   playerid_missing = True |
|:---------|---------------------------:|--------------------------:|
| CBLOL    |                 0.0198641  |                0.0179541  |
| CBLOLA   |                 0.017657   |                0.0159592  |
| CDF      |                 0.00579575 |                0.00616942 |
| CT       |                 0.00197824 |                0.00258598 |
| DCup     |                 0.00473306 |                0.0127452  |
|      ... |                        ... |                       ... |
| UL       |                 0.0227252  |                0.0205401  |
| UPL      |                 0.0327554  |                0.0346152  |
| VCS      |                 0.0261749  |                0.0248993  |
| VL       |                 0.0128504  |                0.0172891  |
| WLDs     |                 0.0126705  |                0.0114522  |

<iframe src="assets/fig/league_dist_fig.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/fig/league_id_missingness.html" width=800 height=600 frameBorder=0></iframe>

#### **Fail to Reject the Null** <a name="fail_reject_null"></a>

**Compare missingness of `playerid` to `side`**

| side   |   playerid_missing = False |   playerid_missing = True |
|:-------|---------------------------:|--------------------------:|
| Blue   |                   0.499931 |                  0.500314 |
| Red    |                   0.500069 |                  0.499686 |

<iframe src="assets/fig/side_dist_fig.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/fig/side_id_missingness.html" width=800 height=600 frameBorder=0></iframe>

## **Hypothesis Testing** <a name="hypothesis"></a>

Now, let's begin answering our core question: Is it true that Renekton was buffed for Worlds in 2022?

To do this, we will attempt to quantify the overall presence of Renekton during the first half of the year and compare it to the second half of the year. Since the World Championships take place at the end of the year, if Renekton was really buffed in the patches leading up to Worlds, he should have a greater presence during the second half of the year than the first half of the year. 

<br>

As mentioned earlier, to measure the overall presence of a champion, we must look at a statistic other than pick rates and win rates. A common statistic used in the League community to measure the prescence of a champion is the **pick/ban rate**, which is calculated as the proportion of games in which the champion was either picked or banned. Champions with high pick/ban rates are often very dominant, since teams will want to either utilize the champion's strength or prevent their enemy from doing so. We will use the pick/ban rate as the test statistic for our analysis.

In summary, we want to find whether Renekton's pick/ban rate is significantly greater during the second half of the year (that is, patches 12.13 through 12.23) than in the first half of the year (12.01 through 12.12). We choose to separate this way because patch 12.12 was released at the very end of June.

<br>

Our null and alternate hypotheses are:

**Null hypothesis**: Renekton's pick/ban rate does not change during the year.

**Alternate hypothesis**: Renekton's pick/ban rate is the greater during the second half of the year than the first half of the year.

**Our chosen test statistic**: the difference between the pick/ban rates during patches 12.13 to 12.23 and the pick/ban rates during patches 12.01 to 12.12

We will work with a significance level of **0.01, or 1%**, for this permutation test.

Our observed statistic--the overall difference in the pick/ban rates across the entire dataset--is 0.038.

After shuffling the `'patch'` column of the dataset and recording the test statistic on each shuffled DataFrame 500 times, we obtained the following distribution of test statistics:

<iframe src="assets/fig/overall_ht.html" width=800 height=600 frameBorder=0></iframe>

When we compare our observed statistic to the ones we obtained through permutation testing:

<iframe src="assets/fig/hyp_testing.html" width=800 height=600 frameBorder=0></iframe>

We can see that our observed statistic is extremely significant--that is, it is highly unlikely that our data was obtained from a distribution in which Renekton's pick/ban rate was the same throughout the year.

Our p-value of 0.0 (so small that it is essentially zero) confirms this, since it is lower than our significance level of 0.01.

<br>

With this data, we have enough evidence to reject the null hypothesis that Renekton's pick/ban rate does not change over the year.

Through doing this permutation test, we have cast doubt on the claim that Renekton's presence in the League of Legends professional scene does not increase in the second half of the year.

<br>

However, it is important to note that this test does not necessarily *prove* that Renekton was "buffed for worlds". We have simply obtained evidence that supports this hypothesis.


