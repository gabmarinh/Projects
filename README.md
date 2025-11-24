# 🚴‍♂️ Case Study: Cyclistic Bike-Share Analysis
### Optimizing Marketing Strategy Through Data Insights

**Author:** Gabriel Marín Huerta
**Role:** Junior Data Analyst (Marketing Team)
**Date:** November 24, 2025
**Tools:** R (RStudio), Tableau

---

## 📑 Executive Summary
Cyclistic, a bike-share company in Chicago, seeks to maximize profitability by converting casual riders into annual members. My analysis of historical trip data reveals that **Casual riders use bikes primarily for leisure on weekends**, while **Annual Members use them for commuting during the week**. Based on these behaviors, I recommend introducing a "Weekend Membership" tier and launching seasonal summer campaigns to bridge the gap between user segments.

---

## 1. ❓ Ask Phase
### Business Task
The marketing director, Lily Moreno, has set a clear goal: Design marketing strategies aimed at converting casual riders into annual members. The finance team has concluded that annual members are much more profitable than casual riders.

### Key Question
> **"How do annual members and casual riders use Cyclistic bikes differently?"**

### Stakeholders
* **Lily Moreno:** Director of Marketing.
* **Cyclistic Executive Team:** The decision-makers who must approve the recommended program.

---

## 2. 💻 Prepare Phase
### Data Source
I used Cyclistic’s historical trip data (public data provided by Motivate International Inc.) for the previous 12 months.
* **Privacy:** The data has been anonymized. No personally identifiable information (PII) such as credit card numbers was used.
* **Organization:** The data is organized in 12 CSV files (one per month) containing ride details like `ride_id`, `rideable_type`, `started_at`, `ended_at`, `start_station_name`, and `member_casual`.

---

## 3. 🛠 Process Phase
To handle the large volume of data (millions of rows), I used **R** for data cleaning and manipulation instead of spreadsheets.

### Code Snippet: Setting up the environment
```r
# Loading key libraries for data analysis
library(tidyverse)  # For data wrangling
library(lubridate)  # For date attributes
library(ggplot2)    # For visualization

# Importing data (Example for Q1)
q1_2019 <- read_csv("Divvy_Trips_2019_Q1.csv")
q1_2020 <- read_csv("Divvy_Trips_2020_Q1.csv")
```
Data Cleaning Steps
1. Consolidation: Merged 12 months of data into a single dataframe.
2. Feature Engineering:
   Created a ride_length column by subtracting started_at from ended_at.
   Created a day_of_week column to analyze weekly patterns.
3. Data Quality Check:
   Removed trips with ride_length < 0 (system errors or maintenance).
   Removed trips to/from "HQ QR" (testing stations).
Removing "bad" data
```r
all_trips_v2 <- all_trips %>% 
  filter(ride_length > 0) %>% 
  filter(start_station_name != "HQ QR")
```
## 4. 📊 Analyze Phase
I conducted a descriptive analysis to find trends and relationships between the two user types.

Key Findings
1. Ride Duration: Casual riders ride for significantly longer durations than members.
   - Casual Average: ~45 minutes
   - Member Average: ~15 minutes
2. Weekly Patterns:
   - Members: High usage on weekdays (Mon-Fri), peaking at commute times (8 AM / 5 PM). This suggests utility usage.
   - Casuals: Usage peaks dramatically on Saturdays and Sundays. This suggests leisure usage.
3. Seasonality: Both groups ride more in summer, but Casual ridership drops near-zero in winter, while Members continue to ride (likely for necessary commuting).

Summary Statistics Calculation
```r
#Comparing members and casual users
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = mean)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = median)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = max)
```

## 5. 📉 Share Phase (Visualizations)
Note: The visualizations below were generated using ggplot2 in R.

Visualization 1: Number of Rides by Day of Week
The bar chart shows a clear crossover. Members dominate the weekdays, but Casual riders overtake them on weekends. This confirms the "Commuter vs. Tourist/Leisure" hypothesis.

Visualization 2: Average Trip Duration
Casual riders consistently have a higher average trip duration throughout the week. Even on Mondays, a casual rider keeps the bike longer than a member.

R Code for Visualization
```r
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(title = "Total Rides by User Type and Day of Week")
```

## 6. 🚀 Act Phase (Recommendations)
Based on the analysis that Casual riders are "Weekend Leisure Users", I propose the following strategies to the executive team:

. The "Weekender" Membership
   - Create a new membership tier specifically for weekends (Fri-Sun).
Why: Casual riders already dominate this timeframe. A full annual membership might feel "wasteful" for them if they don't commute, but a cheaper weekend pass converts them into subscribers.

. Summer Seasonal Push
   - Launch the main marketing campaign in May/June.
Why: Data shows Casual ridership peaks in summer. Marketing dollars spent in winter on this segment would have low ROI.

. Gamification for Long Rides
   - Since Casual riders take longer trips, the app could prompt: "You've ridden for 45 mins! As a member, you would have saved $X on this trip."
Why: Showing immediate value based on their specific behavior (long rides) is a powerful conversion trigger.
