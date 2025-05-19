# Value of Hitter Swing Decisions

*Raw Average Swing Value Results Can be Found in data/final_datasets/swing_values.csv*

## Basic Details

This family of stats attempt to value hitter decision making. The swing vaule for each player is based on a weighted average of the leauge average wobacon value (75% of value) of a swing for that pitch, and individual average hitter wobacon for that pitch (25% of value.). It then compares the value of a swing vs. the expected woba value of continuing to the next count. For example, if a pitch was a ball in a 0-0 count this metric would compare the xwoba value of the pitch based on zone and pitch type, to the expected value of a 1-0 count. If the batter was to swing, they would be credited the woba swing value and if they were to take the pitch they would be credited the expected woba value following that count. The resulting woba value from this calculation is then subtrated by the expected value of a leauge average hitter receving the same pitches. This helps isolate true decison making skill.

## Data Used

In all calculations, all data available from 2018-2024 was used. No calculations, outside of normalization, were split by season as this caused greater instability in metrics as there were not enough samples to find accurate woba values for each pitch type and zone.

## Scale and Distribution

Average Swing Value (ASV) is scaled around 100 with fifteen representing one std from the mean. ASV is only scaled by looking at other players within the same season, meaning it is not scaled globally.

## Process

All data was pulled from Statcast using pybaseball. After pulling data from pybaseball I needed to find expected woba value following each count as well as woba value for pitches in each zone. Expected xwoba value for each count was found by finding the weighted mean of every possible count combination following that count. For example, to find mean expected xwoba value of a 2-1 count the weighted mean of xwoba values in 2-1, 2-2, and 3-2 counts were taken. This data was mapped back to the original data frame based on what would happen if a hitter was to take a pitch. For example, if the pitch thrown crossed the plate as a strike as defined by ABS (automatic strike-ball) in a 1-0 count, the 1-1 expected woba value was mapped into the data frame.

The xwoba value for a pitch was found by separating pitches into groups based on pitch traits, but also while ensuring enough instances remained to still be able to take stable averages. When pulling from statcast, only the basic 14 gameday zones are provided. I thought this was insufficient in capturing the true nuances of pitch zones, so I manually defined 33 zones that better describe pitch locations. These zones are very similar to attack zones and consist of nine heart zones, sixteen shadow zones, and eight chase zones. Data was split into right and left-handed data frames where xwoba was averaged out. The resulting xwoba value was then mapped back to the original df based on batter handedness, zone, and pitch type.

The average swing value, the value we have been seeking to create, was created by subtracting the expected xwoba value where a hitter did not swing from the expected woba value of a pitch where a hitter did swing. Finally this sum was subtracted by the overall expected woba value of a leauge average hitter based on the pitches each hitter saw.

All code for this process can be seen in swing_value.ipynb.

## Defense of Average Swing Value

Average swing value (ASV) provides greater context for hitter decision making.

I created multiple flavors of average swing value, simply changing the zones of pitches the stat used. Average swing value for balls (ASVB) considers only pitches ABS identifies as a ball. This considers the same pitches that O-Swing% considers, however it more effectively considers count context and magnitude of decision. O-Swing% fails to consider very important context to hitter decision making, particularly the count. A swing on the corner in a 0-2 count is a very reasonable swing, whereas a swing on the same pitch in a 0-0 count is less defensible. Average swing value properly values these decisions. A swing on a fastball up and on the edge in a 0-0 count would have a woba value of -.200 whereas a swing on the same pitch in a 0-2 count would have a woba value of -0.080. This discrepancy properly considers the count context that is key to baseball and is an overall better metric for quantifying hitter decision making.

Average Swing Value overall considers all pitches that a hitter sees. ASV is less correlated with walks then ASVB but is highly correlated with woba. ASV considers the value added by a hitter, compared to a leauge average hitter who saw the exact same pitches as them. This is important as a hitter like Juan Soto is going to be pitched much diffrently compared to Keibert Ruiz. ASV is much more stable then woba, as it removes all batted ball luck from the equation and isolates the value of decsion making. ASV, unlike other swing decison metrics, is only loosely correlated with Swing%, as taking quality pitches is greatly punished.

## Import R^2 Values

*all values based on only hitters in 2023 and 2024 who have seen more then 500 pitches*

Year to Year Stability:

- ASVB .735
- ASV .869
- woba .385
- BB% .706
- O-Swing% .837
- xwoba .620

ASVB and O-Swing% R^2 with same year:

- BB% .830 | -.753
- xwoba .450 | -232
- SwStr% -.286 | .458
- K-BB% -.340 | .314
- Swing% -.710 | .887

ASVB and O-Swing% R^2 with next year:

- BB% .645 | -.674
- woba .32 | .180

ASV R^2 with same year:

- xwoba .618
- BB% . 495

ASV and xwoba R^2 with next year:

- woba .464 | .447
- BB%  .367 | .322


## Use of LLMs and Contact

The steps for implementation and process were implemented by me. Gemini was used to help debug as well as rewrite inefficient code. I am confident I could implement this code myself, it would just take significantly longer.
Please contact <dalton.lowery@emory.edu> with questions or feedback!
