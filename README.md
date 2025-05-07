# Value of Hitter Swing Decisions

*Raw Average Swing Value Results Can be Found in data/final_datasets/swing_values.csv*

## Basic Details

These stats attempt to value hitter decision making. It does this by ignoring hitter batting skill and assuming that all swings obtain league average results. It then compares the value of a league average swing vs. the expected woba value of continuing to the next count. For example, if a pitch was a ball in a 0-0 count this metric would compare the xwoba value of the pitch based on zone and pitch type, to the expected value of a 1-0 count. If the batter was to swing, they would be credited the woba swing value and if they were to take the pitch they would be credited the expected woba value following that count.

## Data Used

In all calculations, all data available from 2021-2024 was used. No calculations, outside of normalization, were split by season as this caused greater instability in metrics as there were not enough samples to find accurate woba values for each pitch type and zone. Players with less than 50 pitches seen were removed from the dataset. Including them would have caused O-Swing% to perform much better in comparison, however this is an insignificant group and number of players. Average swing value is highly predictive following the removal of such players and performs better than O-Swing%.

## Scale and Distribution

Average Swing Value (ASV) is scaled like Stuff+ for interpretability. 100 represents the mean, and 101 represents 1% better than average. Data was standardized within each year, not in the entire dataset. Over the years, the mean of ASV_Chase_Shadow was 100.07, Standard deviation was 17.16, 75th percentile was 111.3, 25th percentile was 89.36 with a global minimum of 38.23 and a global max of 156.78. These distributions remained consistent year to year.

## Process

All data was pulled from Statcast using pybaseball. After pulling data from pybaseball I needed to find expected woba value following each count as well as woba value for pitches in each zone. Expected xwoba value for each count was found by finding the weighted mean of every possible count combination following that count. For example, to find mean expected xwoba value of a 2-1 count the weighted mean of xwoba values in 2-1, 2-2, and 3-2 counts were taken. The count was included in the average following itself because it was used as an expected value if a hitter hypothetically was in a 1-1 count and was to take a ball. This data was mapped back to the original data frame based on what would happen if a hitter was to take a pitch. For example, if the pitch thrown crossed the plate as a strike as defined by ABS in a 1-0 count, the 1-1 expected woba value was mapped into the data frame.
The xwoba value for a pitch was found by separating pitches into groups based on pitch traits, but also while ensuring enough instances remained to still be able to take stable averages. I also made the choice of adding in swinging strikes with a woba value of 0 for each swinging strike in each zone and pitch type. I thought this nuance was important because while some pitches might result in better batted ball outcomes on average, they have varied levels of swinging strike percent. This change also increases R^2 with K-BB% as well as SwSrt%.  When pulling from statcast, only the basic 14 gameday zones are provided. I thought this was insufficient in capturing the true nuances of pitch zones, so I manually defined 33 zones that better describe pitch locations. These zones are very similar to attack zones and consist of nine heart zones, sixteen shadow zones, and eight chase zones. Data was split into right and left-handed data frames where xwoba was averaged out. The resulting xwoba value was then mapped back to the original df based on batter handedness, zone, and pitch type.
The average swing value, the value we have been seeking to create, was created by subtracting the expected xwoba value where a hitter did not swing from the expected woba value of a pitch where a hitter did swing.

## Average Swing Value vs O-Swing%

Average swing value (ASV) provides greater context for hitter decision making and is an improved version of O-Swing%. I created multiple flavors of average swing value, simply changing the zones of pitches the stat used. The one I want to focus on is average swing value in the chase and shadow zone, where a pitch is a ball. This considers the same pitches that O-Swing %considers, however it more effectively considers count context and magnitude of decision. O-Swing% fails to consider very important context to hitter decision making, particularly the count. A swing on the corner in a 0-2 count is a very reasonable swing, whereas a swing on the same pitch in a 0-0 count is less defensible. Average swing value properly values these decisions. A swing on a fastball up and on the edge in a 0-0 count would have a woba value of -.200 whereas a swing on the same pitch in a 0-2 count would have a woba value of -0.080. This discrepancy properly considers the count context that is key to baseball and is an overall better metric for quantifying hitter decision making.

## Import R^2 Values

ASV_Shadow_Chase R^2 with same year:
BB% | .760
xwOBA | .287
SwStr% | -.431
K-BB% | -0.345

O-Swing% R^2 with same year:
BB% | -.711
xwOBA | -.266
SwStr% | .447
K-BB% | -0.316

ASV_Shadow_Chase R^2 with next year:
BB% | 0.583
WOBA | 0.15
ASV_Shadow_Chase | 0.754

O-Swing% R^2 with next year:
BB% | -0.561
WOBA | -0.132
O-Swing% | 0.763

### In Season Stabilization

O-Swing% is more reliable at very small sample sizes. The weakness of ASV_Shadow_Chase is that it is almost useless for samples of less than 50 pitches whereas O-Swing at least retains some predictive value. O-Swing% stabilizes quickly when removing samples of less than 50 pitches and maintains a similar level of stability to O-Swing% after.

## Use of LLMs and Contact

The steps for implementation and process were implemented by me. Gemini was used to help debug as well as rewrite inefficient code. I am confident I could implement this code myself, it would just take significantly longer.
Please contact <dalton.lowery@emory.edu> with questions or feedback!
