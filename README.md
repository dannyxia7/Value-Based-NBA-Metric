# DARYL Score: Value Based NBA Metric

Hello! I love watching the NBA but I also like discussing how teams are built, specifically how GMs create great teams under salary cap/luxury tax restrictions. One bad contract (such as a 5yr/180m) to the wrong player can hurt a team tremendously. My favorite team is the Golden State Warriors, and I believe that they have managed their salary cap/contracts far better than most teams in the NBA, though they have also been extremely lucky on their gambles, and have been at the right place at the right time.

It also seems to be the case in the nba that the “star” level players will “get theirs”. If you are a star-caliber player you will earn a max contract. Players such as LaMelo Ball and Domas Sabonis are perhaps not superstars yet but will receive similar amounts to players we consider superstars such as Luka Doncic.With the new CBA and the second apron it seems increasingly likely that teams will only be able to hold 2 max contracts on their payroll. Therefore once a team's two star players are set the success of the team will also be largely determined by the quality of players around them.

I sought out a way to find role players that perform relatively well but get paid relatively less. In other words I wanted to find the best “bang for your buck” role players. I named the statistic I would create after Daryl Morey because he is a GM that I admire.

I used a statistical measurement called z-scores to rate players on how “good” they perform and how much they get paid relative to other players. I used all-in-one statistics such as RAPTOR and DARKO to determine how good a player performed. I used public data from basketball reference for their salaries. There were several steps I had to take to normalize the data for appropriate analysis.

## Salary Z-Score

Determining a player's salary z-score was more straightforward since it is less abstract than “performance”. I obtained salary data from basketball-reference.com. The raw salary data was not a normal distribution. Therefore I had to get rid of outliers and consider using a log transformation. 
I determined outliers using the IQR * 1.5 rule. The rule determined outliers to be any players earning above $31 million. I used this measure to effectively determine what one considered a “star” player and a “role player”.

It may be odd to consider a role player making $30 million. However, without an objective cutting point such as IQR * 1.5, it is hard to determine an empirical cutoff. It seems to be the direction of the NBA that highly valued “role players” are earning up to $30 million. For instance Cam Johnson earns $108 million over 4 years.

I got rid of outliers and performed a log transformation (base e) on the data for the data to be of a more normal distribution. After doing a shapiro test the distribution was still not roughly normal. This is largely due to outliers that get paid very relatively little. Therefore the z-scores for the log transformed data should be taken with a grain of salt. Some of the data that contributed to the abnormality were outliers of players that got paid relatively less. They would get filtered out later when I filtered by playing time.

## Performance Z-Score

I considered several ways to measure a player's performance relative to others. I considered creating my own ranking statistic that focused on points scored and offensive efficiency. I realized that was a harder task than anticipated. There are many performance summary statistics such as BPM, PER, and LEBRON. I spent a weekend going through which summary statistics were most favored by the community.

I chose to use two publicly available summary statistics that are free to use: DARKO and RAPTOR. DARKO is a machine learning algorithm that projects how well a player will perform. RAPTOR is a more traditional “all-in-one” statistic that measures a player's value on the court with +/-. They are both widely respected measures in the NBA community.

I thought it would be better to use two measures to have a more robust appreciation of a player's “performance” value. Some may argue having multiple measures may just increase a model's error. I understand this point however, I find including both values is valuable since they seem to measure different aspects of a player's value (DARKO future value, and RAPTOR present value).

I collected DARKO and RAPTOR data from the DARKO web app and FiveThirtyEight respectively. I cleaned the data of both measures. It seemed there were a considerable number of players with high RAPTOR because they had a small sample size of minutes played. I set a threshold of 1,400 minutes played to be considered in this metric. This is arbitrary but it stems from a simple calculation of 1400 / 70 games = 20 minutes per game. I thought 20 minutes per game would be a rough measure of players that are quite active on their team. The 70 games out of 82 gives a bit of leeway. A different cutoff would likely lead to somewhat different results.

I was left with 175 players that met my cutoff for minutes played. This would be roughly 6 role players per team (175/30).

The RAPTOR and DARKO scores were off a normal distribution therefore I took their z-scores using their raw data.

Some of the top players by this metric were Walker Kessler, Desmond Bane, and Tyrese Haliburton. This is taking into account their current year salary which for Bane and Halliburton is still their rookie contract. These results make intuitive sense as these are players that are performing at a star level but are still being paid on their rookie contract. It was interesting to see where other players ranked using this metric.

## Creating DARYL Score

My goal was then to simply multiply the salary z-score and performance z-score so they would be treated roughly equally in the final calculation of DARYL score. However, this would not work as negative z-scores are considered better for salary. Negative * positive would give a negative. The formulas for how I calculated DARYL score are in one of the pictures in the repository.

The effective salary score subtracts the curr salary_zscore from the max of all salary z scores. Therefore a person with a negative salary z_score would be rated highly. (ie positive_value - (negative_value)) is positive. I noticed the performance_zscore standard deviation was higher than the salary z-score standard deviation so I adjusted for that as well.

The effective performance score adds the absolute value of the lowest performance z-score to the curr_performance_zscore. Therefore the lowest curr_performance_zscore would be at 0 and it would go up from there.

I then multiplied each effective score to give myself a DARYL score.

Interpreting DARYL Score

The top players in DARYL score include Walker Kessler, Desmond Bane, and Tyrese Haliburton. These are all players still on their rookie contracts that have performed at highly productive levels. I think this validates DARYL score as for the upcoming season they are still considered very “bang for your buck”-esque players as their second contract hasn't kicked in. Once their second contract arrives some will be earning max-level money.

## Top DARYL Score Players
| Player Name        | Salary      | DARYL Score |
|--------------------|-------------|-------------|
| Walker Kessler     | $2,831,160  | 55.39       |
| Desmond Bane       | $3,845,083  | 53.18       |
| Josh Okogie        | $2,815,937  | 51.28       |
| Tyrese Haliburton  | $5,808,435  | 49.62       |
| Immanuel Quickley  | $4,171,548  | 47.83       |

It was interesting to see which players' DARYL score rated highly. Josh Okogie and Keita Bates-Diop were both highly rated by DARYL score. This seems to match some testimonials of the players performance. Perhaps the Suns have more depth then many people consider them to have. Another team DARYL score was high on is the Knicks with Immanuel Quickley, Quentin Grimes, and Isaiah Hartenstein. Perhaps DARYL score is less good at analyzing playoff performance :P

Lastly, DARYL score seems to heavily discount players that get paid more than 10 million dollars as their salary z-score is determined to be quite bad. This is likely due to the salary z-score distribution being skewed to the right. Players such as Anthony Edwards and Derrick White should not be as low as they are despite their relatively higher salary. This would be the first amendment I make to changing the DARYL score algorithm.

While not a perfect measure I think a tool like this could be useful to help determine value in trades to ensure a contending team has enough money to pay their star players. As supermaxes increase and the penalties of the second apron get harsher, it is important for teams to be diligent with their cap space. I think DARYL score is a first step to achieving that goal.
