## Introduction

I enjoy playing on
[Chess.com](https://www.chess.com/member/ienjoysomechess) and
[Lichess](https://lichess.org/@/iEnjoySomeChess) under the moniker
*iEnjoySomeChess*. (Challenge me!) I’m an amateur chess player, but I’ve
been following high-level chess tournaments since 2016, when Magnus
Carlsen defended his world championship title against Sergey Karjakin in
New York City. I began playing on Chess.com and Lichess in 2016 and
2017, respectively, so I have years of data, which I will analyze using
`tidyverse`, `lubridate`, and `chessR` in RStudio.

Download my .Rmd file [here](/portfolio/chess.Rmd).

## ChessR: Extracting Data

I’m using the package [ChessR](https://github.com/JaseZiv/chessR) to
extract data from Chess.com and Lichess. The `ChessR` functions query an
API, which is rate limited; the limit is unknown for Chess.com, but the
limit is 15 games per second for Lichess. Then, I write the data to
CSV’s, so that I can quickly load the data in the future.

    # Chess.com
    my_chesscom_data_raw <- get_game_data(usernames = 'iEnjoySomeChess')

    write_csv(my_chesscom_data_raw,
              file = 'my_chesscom_data_raw.csv',
              na = 'NA')

    # Lichess
    my_lichess_data_raw <- get_raw_lichess(player_names = 'iEnjoySomeChess')

    write_csv(my_lichess_data_raw, 
              file = 'my_lichess_data_raw.csv',
              na = 'NA')

## Cleaning the Data

### Chess.com

Extracting the data from Chess.com, there are 36 variables, and each row
has information about a single game. I keep, rename, and edit the core
variables, and I create other useful variables:

-   `site`: In this case, `Chess.com`.
-   `event`: The time category determined by `time_control`. For
    example, `bullet` (fast), `blitz` (fast but slower than `bullet`),
    `rapid` (slow and slower than `blitz`).
-   `time_control`: The time allotted to each player, with the number of
    minutes and the increment (time in seconds added to a player’s clock
    after each move). For example, `10+0`.
-   `white`: The player who played with the white pieces. For example,
    `iEnjoySomeChess`.
-   `white_elo`: The rating of the player who played with the white
    pieces.
-   `black`: The player who played with the black pieces. For example,
    `iEnjoySomeChess`.
-   `black_elo`: The rating of the player who played with the black
    pieces.
-   `rating_difference`: The absolute value of the rating difference
    between `white_elo` and `black_elo`.
-   `termination`: The ending of the game. For example, `time`,
    `checkmate`, `resignation`, among other values.
-   `n_moves`: The number of moves in the game, noting that both players
    each making one move counts as one move.
-   `result`: The result with format “white-black”, where a loss is zero
    points, a draw is half of a point, and a win is one point. For
    example, `1-0`.
-   `opening`: The opening, if the opening was recognized as a book
    opening on Chess.com. For example,
    `Pawn-Opening-Accelerated-London-System`.
-   `ECO`: The code (a shorthand) for the opening, if the opening was
    recognized as a book opening on Chess.com. For example, `A00`.
-   `opponent`: The opponent of the user whose games you requested.
-   `opponent_rating`: The rating of the opponent of the user whose
    games you requested.
-   `user`: The user whose games you requested. In this case,
    `iEnjoySomeChess`.
-   `user_color`: The color of the pieces played by the user whose game
    you requested.
-   `user_result`: The result of the game from the perspective of
    `user`. For example, `win`, `loss`, or `draw`.
-   `user_rating`: The rating of the user whose games you requested.
-   `time_UTC`: The date and time (UTC) at the beginning of the game.
    For example, `2022-08-23 21:22:52`.
-   `end_time_UTC`: The date and time (UTC) at the end of the game. For
    example, `2022-08-23 21:36:19`.
-   `time_CDT`: The date and time (CDT) at the beginning of the game.
    For example, `2022-08-23 16:22:52`.
-   `end_time_CDT`: The date and time (CDT) at the end of the game. For
    example, `2022-08-23 16:36:19`.
-   `previous_time_CDT`: The date and time (CDT) at the beginning of the
    previous game played by the user. For example,
    `2022-08-23 16:06:06`.
-   `time_diff`: The difference in time between `previous_time_CDT` and
    `time_CDT`.
-   `hour_interval`: The hour (CDT) during which the game was played.
    For example, `16:00` if the game was played at
    `2022-08-23 16:06:06`.
-   `link`: The link to the game on Chess.com.

<!-- -->

    my_chesscom_data %>% 
      glimpse()

    ## Rows: 7,423
    ## Columns: 28
    ## $ site              <chr> "Chess.com", "Chess.com", "Chess.com", "Chess.com", …
    ## $ event             <fct> rapid, rapid, rapid, rapid, rapid, rapid, blitz, rap…
    ## $ time_control      <chr> "10+0", "10+0", "10+0", "10+0", "10+0", "10+0", "3+2…
    ## $ date              <date> 2022-08-23, 2022-08-23, 2022-03-04, 2022-01-14, 202…
    ## $ white             <chr> "Piercepoo17", "iEnjoySomeChess", "abdfghjk", "iEnjo…
    ## $ white_elo         <dbl> 1818, 1804, 699, 1804, 1742, 1778, 1794, 1798, 1757,…
    ## $ black             <chr> "iEnjoySomeChess", "Piercepoo17", "iEnjoySomeChess",…
    ## $ black_elo         <dbl> 1804, 1818, 1804, 1693, 1795, 1784, 1642, 1771, 1774…
    ## $ rating_difference <dbl> 14, 14, 1105, 111, 53, 6, 152, 27, 17, 2, 37, 30, 1,…
    ## $ termination       <chr> "repetition", "checkmate", "checkmate", "abandonment…
    ## $ n_moves           <dbl> 24, 31, 17, 56, 6, 69, 21, 29, 15, 34, 32, 38, 52, 4…
    ## $ result            <chr> "1/2-1/2", "0-1", "0-1", "1-0", "0-1", "0-1", "1-0",…
    ## $ opening           <chr> "Geet-Opening-1...d5-2.d3-Nf6", "Gambit-Declined-Que…
    ## $ ECO               <chr> "A00", "D31", "D00", "D13", "D02", "B21", "D85", "B5…
    ## $ opponent          <chr> "Piercepoo17", "Piercepoo17", "abdfghjk", "Timurka55…
    ## $ opponent_rating   <dbl> 1818, 1818, 699, 1693, 1742, 1778, 1642, 1798, 1774,…
    ## $ user              <chr> "iEnjoySomeChess", "iEnjoySomeChess", "iEnjoySomeChe…
    ## $ user_color        <chr> "black", "white", "black", "white", "black", "black"…
    ## $ user_result       <chr> "draw", "loss", "win", "win", "win", "win", "win", "…
    ## $ user_rating       <dbl> 1804, 1804, 1804, 1804, 1795, 1784, 1794, 1771, 1757…
    ## $ time_UTC          <dttm> 2022-08-23 21:22:52, 2022-08-23 21:06:06, 2022-03-0…
    ## $ end_time_UTC      <dttm> 2022-08-23 21:36:19, 2022-08-23 21:22:41, 2022-03-0…
    ## $ time_CDT          <dttm> 2022-08-23 16:22:52, 2022-08-23 16:06:06, 2022-03-0…
    ## $ end_time_CDT      <dttm> 2022-08-23 16:36:19, 2022-08-23 16:22:41, 2022-03-0…
    ## $ previous_time_CDT <dttm> 2022-08-23 16:06:06, 2022-03-04 22:58:12, 2022-01-1…
    ## $ time_diff         <Period> 16M 46S, 5m 18d 17H 7M 54S, 1m 18d 1H 9M 59S, 1d …
    ## $ hour_interval     <chr> "16:00", "16:00", "22:00", "21:00", "16:00", "14:00"…
    ## $ link              <chr> "https://www.chess.com/game/live/55028774859", "http…

### Lichess

Extracting the data from Lichess, there are 23 variables, and each row
has information about a single game. Again, I keep, rename, and edit the
core variables, and I create other useful variables, with significant
overlap with Chess.com; however, Lichess lacks `n_moves`,
`end_time_UTC`, and `end_time_CDT`, while having some additional
information:

-   `white_rating_change`: The rating change of the player with the
    white pieces following the termination of the game.
-   `black_rating_change`: The rating change of the player with the
    black pieces following the termination of the game.
-   `user_rating_change`: The rating change of the user whose game you
    requested following the termination of the game.

<!-- -->

    my_lichess_data %>% 
      glimpse()

    ## Rows: 8,009
    ## Columns: 28
    ## $ site                <chr> "Lichess", "Lichess", "Lichess", "Lichess", "Liche…
    ## $ event               <fct> rapid, rapid, rapid, rapid, rapid, rapid, bullet, …
    ## $ time_control        <chr> "10+0", "10+0", "10+0", "10+0", "10+0", "10+0", "2…
    ## $ date                <date> 2022-10-12, 2022-10-11, 2022-10-11, 2022-10-11, 2…
    ## $ white               <chr> "iEnjoySomeChess", "Ibrahim780", "CleverRocky20", …
    ## $ white_elo           <dbl> 2175, 2137, 2245, 2172, 2077, 2183, 1893, 1887, 18…
    ## $ black               <chr> "adamnani", "iEnjoySomeChess", "iEnjoySomeChess", …
    ## $ black_elo           <dbl> 2137, 2177, 2190, 2219, 2193, 2028, 1938, 1905, 18…
    ## $ rating_difference   <dbl> 38, 40, 55, 47, 116, 155, 45, 18, 6, 93, 22, 3, 6,…
    ## $ termination         <chr> "Normal", "Normal", "Time Forfeit", "Normal", "Nor…
    ## $ result              <chr> "0-1", "1/2-1/2", "1-0", "1-0", "1-0", "1-0", "1-0…
    ## $ white_rating_change <dbl> -15, 1, 5, 18, 8, 10, 7, 6, 6, -4, -6, 6, -6, 15, …
    ## $ black_rating_change <dbl> 6, -2, -13, -6, -21, -3, -6, -6, -6, 5, 6, -6, 6, …
    ## $ opening             <chr> "Nimzo-Indian Defense: Classical Variation", "Sici…
    ## $ ECO                 <chr> "E32", "B90", "B23", "D37", "D03", "E32", "D20", "…
    ## $ opponent            <chr> "adamnani", "Ibrahim780", "CleverRocky20", "Divisi…
    ## $ opponent_rating     <dbl> 2137, 2137, 2245, 2219, 2077, 2028, 1938, 1905, 18…
    ## $ user                <chr> "iEnjoySomeChess", "iEnjoySomeChess", "iEnjoySomeC…
    ## $ user_color          <chr> "white", "black", "black", "white", "black", "whit…
    ## $ user_result         <chr> "loss", "draw", "loss", "win", "loss", "win", "win…
    ## $ user_rating         <dbl> 2175, 2177, 2190, 2172, 2193, 2183, 1893, 1887, 18…
    ## $ user_rating_change  <dbl> -15, -2, -13, 18, -21, 10, 7, 6, -6, -4, 6, 6, 6, …
    ## $ time_UTC            <dttm> 2022-10-12 14:33:07, 2022-10-11 18:22:08, 2022-10…
    ## $ time_CDT            <dttm> 2022-10-12 09:33:07, 2022-10-11 13:22:08, 2022-10…
    ## $ previous_time_CDT   <dttm> 2022-10-11 13:22:08, 2022-10-11 09:34:08, 2022-10…
    ## $ time_diff           <Period> 20H 10M 59S, 3H 48M 0S, 1H 37M 24S, 12H 14M 46S…
    ## $ hour_interval       <chr> "09:00", "13:00", "09:00", "07:00", "19:00", "19:0…
    ## $ link                <chr> "https://lichess.org/bQmZJs4r", "https://lichess.o…

## Analysis

While I want to compare data between Chess.com and Lichess, I first want
to perform separate analysis for Chess.com and Lichess. I will analyze
five main questions:

1.  How do my games end when I lose, draw, and win?
2.  How often do I play with the white pieces, and how often do I play
    with the black pieces?
3.  On average, how much rating do I gain/lose according to color of
    pieces? According to month?
4.  What is my longest break from playing on Chess.com? Lichess?
5.  What times of day am I most active?

After I perform separate analysis for Chess.com and Lichess, I will
combine the data sets, and I will perform a combined analysis for
Chess.com and Lichess.

### Chess.com

#### Game Termination

> Q: “How do I win games?”

First, let’s check how many games I’ve won in `bullet`, `blitz`, and
`rapid`. (Note: I have only played `bullet`, `blitz`, and `rapid` games
on Chess.com, but I’ve played more time controls on Lichess,
including`classical` and `chess960`. For the most part, I limit my
attention to `bullet`, `blitz`, and `rapid`.)

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Event</th>
<th style="text-align: right;">Won Games</th>
<th style="text-align: right;">Total Games</th>
<th style="text-align: right;">Win Percent</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">bullet</td>
<td style="text-align: right;">1861</td>
<td style="text-align: right;">3682</td>
<td style="text-align: right;">0.5054318</td>
</tr>
<tr class="even">
<td style="text-align: left;">blitz</td>
<td style="text-align: right;">1746</td>
<td style="text-align: right;">3569</td>
<td style="text-align: right;">0.4892127</td>
</tr>
<tr class="odd">
<td style="text-align: left;">rapid</td>
<td style="text-align: right;">92</td>
<td style="text-align: right;">172</td>
<td style="text-align: right;">0.5348837</td>
</tr>
</tbody>
</table>

![](chess_files/figure-markdown_strict/win-game-termination-chesscom-1.png)

> I win a significant portion (74.4%) of `bullet` games by `time`, which
> makes sense given that `bullet` is faster than `blitz` and `rapid`,
> where `time` is less of a factor. It’s interesting that `abandonment`
> becomes more important in `blitz` and `rapid` (with values of 1.83%
> and 2.17%, respectively), as opponents may start a game, but leave due
> to the time committment. Lastly, whereas my method of winning games is
> somewhat uniform for `blitz`, I win a significant portion (71.7%) of
> `rapid` games by `resignation`.

> Q: “How do I lose games?”

First, let’s see how many games I’ve lost across `bullet`, `blitz`, and
`rapid`.

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Event</th>
<th style="text-align: right;">Lost Games</th>
<th style="text-align: right;">Total Games</th>
<th style="text-align: right;">Loss Percent</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">bullet</td>
<td style="text-align: right;">1731</td>
<td style="text-align: right;">3682</td>
<td style="text-align: right;">0.4701249</td>
</tr>
<tr class="even">
<td style="text-align: left;">blitz</td>
<td style="text-align: right;">1624</td>
<td style="text-align: right;">3569</td>
<td style="text-align: right;">0.4550294</td>
</tr>
<tr class="odd">
<td style="text-align: left;">rapid</td>
<td style="text-align: right;">57</td>
<td style="text-align: right;">172</td>
<td style="text-align: right;">0.3313953</td>
</tr>
</tbody>
</table>

![](/assets/img/loss-game-termination-chesscom-1.png)

> I lose a significant portion (61.9%) of `bullet` games by `time`,
> which, again, makes sense given that `bullet` is faster than `blitz`
> and `rapid`, where `time` is less of a factor. Further, whereas my
> methods of *winning* `blitz` games were more uniform, I lose a large
> portion (56.2%) of `blitz` games by `time`.

> Q: “How do I draw games?”

First, let’s see how many games I’ve drawn across `bullet`, `blitz`, and
`rapid`.

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Event</th>
<th style="text-align: right;">Drawn Games</th>
<th style="text-align: right;">Total Games</th>
<th style="text-align: right;">Draw Percent</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">bullet</td>
<td style="text-align: right;">90</td>
<td style="text-align: right;">3682</td>
<td style="text-align: right;">0.0244432</td>
</tr>
<tr class="even">
<td style="text-align: left;">blitz</td>
<td style="text-align: right;">199</td>
<td style="text-align: right;">3569</td>
<td style="text-align: right;">0.0557579</td>
</tr>
<tr class="odd">
<td style="text-align: left;">rapid</td>
<td style="text-align: right;">23</td>
<td style="text-align: right;">172</td>
<td style="text-align: right;">0.1337209</td>
</tr>
</tbody>
</table>

![](/assets/img/draw-game-termination-chesscom-1.png)

> I draw a large portion (57.8%) of my `bullet` games by
> `timeout vs. insufficient material`, which indicates that in my drawn
> `bullet` games there are usually no remaining pieces on the board.
> Mostly, I do not draw my games by `50-move rule`. Further, my methods
> of drawing `blitz` games are somewhat uniformly distributed, whereas I
> draw a large portion (47.8%) of my `rapid` games by `agreed draw`.

#### Color of Pieces

> Q: “Do I play with the white or black pieces more?”

![](/assets/img/black-white-chesscom-1.png)

> I would expect to play with the white and black pieces with uniformly
> random probability, that is, 50%, which is approximately true, though
> for `rapid` I tend to play with the black pieces (52.9%) more than the
> white pieces (47.1%).

#### Rating Gain/Loss

As stated in **Cleaning the Data**, the data for Chess.com does not have
information about rating change, but we can create a
`user_rating_change` column, just like Lichess, since we have a complete
record of user games, which includes `user_rating`. (Note: Chess.com and
Lichess provide separate ratings for different values of `event`; thus,
we have to sort by `event`, and subsequently `time_CDT`, before finding
the difference between successive values of `user_rating`.)

> Q: “On average, how much rating do I gain/lose with the black and
> white pieces?”

![](/assets/img/rating-black-white-chesscom-1.png)

> I would expect to gain more rating with the white pieces, rather than
> the black pieces, since white plays the first opening move, and thus
> has the first chance to create an advantage, so it’s suprising that I
> gain more rating (it’s even suprising that I don’t lose rating) with
> the black pieces. Further, I tend to gain more rating from `rapid`
> than I gain from `bullet` or `blitz`. I have not played as many
> `rapid` games as `bullet` and `blitz` games, so perhaps my rating is
> cathcing up to my skill level.

> Q: “On average, how much rating do I gain/lose in a given month?”

![](/assets/img/month-performance-chesscom-1.png)

> I tend to lose rating around February, whereas other months are mostly
> consistent, or I gain rating. I don’t have an explanation for the
> rating loss in February.

#### Number of Moves

The extracted data from Chess.com has information about the number of
moves (`n_moves`) for each game. In addition to the five main questions
stated under **analysis**, I include the following question:

> Q: “How long are my games in number of moves?”

![](/assets/img/n_moves-chesscom-1.png)

> My games are typically between 30 and 50 moves, with some notable
> outliers. The [outlier
> game](https://www.chess.com/game/live/31112378653) with 142 moves is a
> game I played with a friend at UW-Madison, where I walked my king,
> being pursued by a queen, from h8 to b1 to h8, and I went on to win
> the game!

#### Longest Break

> Q: “What’s the longest break I’ve taken from playing on Chess.com?”

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Previous Game Time (CDT)</th>
<th style="text-align: left;">Next Game Time (CDT)</th>
<th style="text-align: right;">Time Difference</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">2016-04-13 18:35:49</td>
<td style="text-align: left;">2016-10-26 16:58:46</td>
<td style="text-align: right;">6m 12d 22H 22M 57S</td>
</tr>
</tbody>
</table>

> My longest break from Chess.com was 6 Months and 12 Days, which time
> elapsed between April 13, 2016 and October 26, 2016. Considering I
> started playing on Chess.com in January 2016, said break was probably
> a natural waning of interest, since I wasn’t as enamored with chess
> then as I am now.

#### Time of Day

> Q: “What times of day am I most active?”

![](/assets/img/time-of-day-chesscom-1.png)
![](/assets/img/time-of-day-chesscom-2.png)
![](/assets/img/time-of-day-chesscom-3.png)
![](/assets/img/time-of-day-chesscom-4.png)

> I have not played any games at 23:00 or 24:00 (00:00), and I play a
> lot of games in the evening, between 16:00 and 19:00. I tend to play
> `rapid` games either in the morning (08:00 to 09:00) or the evening
> (16:00 to 19:00).

### Lichess

#### Game Termination

The exported Lichess data only distinguishes between `Normal` and
`Time Forfeit` for the variable `termination`, so we don’t have the same
breakdown as Chess.com. As such, I exclude `classical` (longer time
control), since `Time Forfeit` is not a strong factor for `classical`.
(Note: Again, I did not play `classical` games on Chess.com.)

> Q: “How do I win games?”

First, let’s see how many games I’ve won across `bullet`, `blitz`, and
`rapid`.

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Event</th>
<th style="text-align: right;">Won Games</th>
<th style="text-align: right;">Total Games</th>
<th style="text-align: right;">Win Percent</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">bullet</td>
<td style="text-align: right;">1632</td>
<td style="text-align: right;">3452</td>
<td style="text-align: right;">0.4727694</td>
</tr>
<tr class="even">
<td style="text-align: left;">blitz</td>
<td style="text-align: right;">1932</td>
<td style="text-align: right;">4118</td>
<td style="text-align: right;">0.4691598</td>
</tr>
<tr class="odd">
<td style="text-align: left;">rapid</td>
<td style="text-align: right;">78</td>
<td style="text-align: right;">158</td>
<td style="text-align: right;">0.4936709</td>
</tr>
</tbody>
</table>

![](/assets/img/win-game-termination-lichess-1.png)

> Again, I win a large portion (52.5%) of `bullet` games by
> `Time Forfeit`, which makes sense given that `bullet` is faster than
> `blitz` and `rapid`, where `time` is less of a factor, as evidenced by
> the decreasing percentage of `Time Forfeit` from `bullet` to `blitz`,
> and from `blitz` to `rapid`.

> Q: “How do I lose games?”

First, let’s see how many games I’ve lost across `bullet`, `blitz`, and
`rapid`.

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Event</th>
<th style="text-align: right;">Lost Games</th>
<th style="text-align: right;">Total Games</th>
<th style="text-align: right;">Loss Percent</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">bullet</td>
<td style="text-align: right;">1651</td>
<td style="text-align: right;">3452</td>
<td style="text-align: right;">0.4782735</td>
</tr>
<tr class="even">
<td style="text-align: left;">blitz</td>
<td style="text-align: right;">1917</td>
<td style="text-align: right;">4118</td>
<td style="text-align: right;">0.4655172</td>
</tr>
<tr class="odd">
<td style="text-align: left;">rapid</td>
<td style="text-align: right;">69</td>
<td style="text-align: right;">158</td>
<td style="text-align: right;">0.4367089</td>
</tr>
</tbody>
</table>

![](/assets/img/loss-game-termination-lichess-1.png)

Whereas I won less and less games by `Time Forfeit` from `bullet` to
`blitz` to `rapid`, I lose a significant portion (62.3%) of `rapid`
games by `Time Forfeit`. As I play `rapid` games with a `time_control`
of `10+0` (no increment, that is, no time added after each move), I need
to work on my time management.

> Q: “How do I draw games?”

First, let’s see how many games I’ve drawn across `bullet`, `blitz`, and
`rapid`.

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Event</th>
<th style="text-align: right;">Drawn Games</th>
<th style="text-align: right;">Total Games</th>
<th style="text-align: right;">Draw Percent</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">bullet</td>
<td style="text-align: right;">169</td>
<td style="text-align: right;">3452</td>
<td style="text-align: right;">0.0489571</td>
</tr>
<tr class="even">
<td style="text-align: left;">blitz</td>
<td style="text-align: right;">269</td>
<td style="text-align: right;">4118</td>
<td style="text-align: right;">0.0653230</td>
</tr>
<tr class="odd">
<td style="text-align: left;">rapid</td>
<td style="text-align: right;">11</td>
<td style="text-align: right;">158</td>
<td style="text-align: right;">0.0696203</td>
</tr>
</tbody>
</table>

![](/assets/img/draw-game-termination-lichess-1.png)

> The breakdown of drawn games on Lichess isn’t too useful, since it’s
> rare to draw games by `Time Forfeit`, unless no pieces remain on the
> board, which appears to occur mostly for `bullet` games on Lichess. We
> saw the same trend on Chess.com.

#### Color of Pieces

> Q: “Do I play with the white or black pieces more?”

![](/assets/img/black-white-lichess-1.png)

> Again, I would expect to play with the white and black pieces with
> uniformly random probability, that is, 50%, which is approximately
> true, though for `rapid` I tend to play with the white pieces (54.4%)
> more than the black pieces (45.6%); more suprising, I have only played
> 8 `classical` games, of which 6 (75.0%) were with the black pieces and
> 2 (25.0%) were with the white pieces.

#### Rating Gain/Loss

> Q: “On average, how much rating do I gain/lose with the black and
> white pieces?”

![](/assets/img/rating-black-white-lichess-1.png)

> I tend to lose rating with the black pieces on Lichess, whereas I tend
> to gain rating with the white pieces on Lichess. Compared to
> Chess.com, this is more consistent with my expectations.

> Q: “On average, how much rating do I gain/lose in a given month?”

![](/assets/img/month-performance-lichess-1.png)

> I tend to lose rating during May, June, and July, whereas I tend to
> gain rating in other months. What’s up with summer?

#### Longest Break

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Previous Game Time (CDT)</th>
<th style="text-align: left;">Next Game Time (CDT)</th>
<th style="text-align: right;">Time Difference</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">2022-03-07 16:33:05</td>
<td style="text-align: left;">2022-07-14 08:10:09</td>
<td style="text-align: right;">4m 6d 15H 37M 4S</td>
</tr>
</tbody>
</table>

> My longest break from Lichess was 4 Months and 6 Days, which time
> elapsed between March 7, 2022 and July 14, 2022. Really, this year? I
> went to Alaska in June; otherwise, I don’t have an explanation for the
> break. *Zugzwang* with my courses (quantum mechanics, thermal physics,
> and laboratory), perhaps?

#### Time of Day

> Q: “What times of day am I most active?”

![](/assets/img/time-of-day-lichess-1.png)
![](/assets/img/time-of-day-lichess-2.png)
![](/assets/img/time-of-day-lichess-3.png)
![](/assets/img/time-of-day-lichess-4.png)

> I tend to play `rapid` games on Lichess much earlier in the day
> (07:00, 08:00) than I do `bullet` or `blitz` games. Otherwise, the
> times are somewhat uniform during mid-day, and they dropoff around
> nighttime hours.

## Comparison

Now, I will combine the data and perform a combined analysis for
Chess.com and Lichess. I drop the columns unique to each data set; then
I bind the data sets, so there is one large data set. (Note: The
percentages will now be over all games, from both Chess.com and
Lichess.)

#### Start Date/Total Games

> Q: “When did I start playing on either site?”

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Site</th>
<th style="text-align: left;">Earliest Game Time (CDT)</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">Chess.com</td>
<td style="text-align: left;">2016-01-03 01:35:30</td>
</tr>
<tr class="even">
<td style="text-align: left;">Lichess</td>
<td style="text-align: left;">2017-07-19 12:24:50</td>
</tr>
</tbody>
</table>

> Q: “How many games have I played on each site? On which site have I
> played more games?”

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Site</th>
<th style="text-align: right;">Games Played</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">Chess.com</td>
<td style="text-align: right;">7423</td>
</tr>
<tr class="even">
<td style="text-align: left;">Lichess</td>
<td style="text-align: right;">8009</td>
</tr>
</tbody>
</table>

    ## `summarise()` has grouped output by 'site'. You can override using the
    ## `.groups` argument.

![](/assets/img/games-played-both-sites-1.png)

> I started playing on Chess.com, but I now play primarily on Lichess.
> My total number of games played on Lichess has surpassed my total
> number of games played on Chess.com.

#### Time of Day

> # Q: “What times of day am I most active by site?”

![](/assets/img/time-of-day-both-sites-1.png)

#### Rating

> Q: “What is my highest rating achieved on either site by `event`?”

<table>
<thead>
<tr class="header">
<th style="text-align: left;">Site</th>
<th style="text-align: left;">Event</th>
<th style="text-align: right;">Highest Rating</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">Chess.com</td>
<td style="text-align: left;">bullet</td>
<td style="text-align: right;">1677</td>
</tr>
<tr class="even">
<td style="text-align: left;">Lichess</td>
<td style="text-align: left;">bullet</td>
<td style="text-align: right;">2010</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Chess.com</td>
<td style="text-align: left;">blitz</td>
<td style="text-align: right;">1794</td>
</tr>
<tr class="even">
<td style="text-align: left;">Lichess</td>
<td style="text-align: left;">blitz</td>
<td style="text-align: right;">2129</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Chess.com</td>
<td style="text-align: left;">rapid</td>
<td style="text-align: right;">1804</td>
</tr>
<tr class="even">
<td style="text-align: left;">Lichess</td>
<td style="text-align: left;">rapid</td>
<td style="text-align: right;">2194</td>
</tr>
</tbody>
</table>

> Lichess has higher ratings than Chess.com, and on average my rating is
> around 300-400 points lower on Chess.com.

## Conclusion

As always, there’s a lot of data, and there’s a lot more to do with the
data. For example, I could generate further insights by filtering the
data according to `year`, which could be useful, since my chess skills
have improved over time, and thus my above analysis might not reflect
the most recent trends in my chess games. Nonetheless, the above
analysis provided useful insights, and I plan to update and revisit my
chess data in the future.
