Chicago: Cyclistic Bike-Share 2023
================
Lucio Colonna
<br>Last updated: Tuesday September 10, 2024

<br>

<p align="center">
  <img src="./img/cover.png">
</p>

<br>

The case study presented here is the final capstone project of the
[**Google Data
Analytics**](https://www.coursera.org/professional-certificates/google-data-analytics)
certification program.<br> I’ve undertaken the entire project using
**R** (perhaps a bit masochistic), but it’s all part of the fun.

The journey took a bit longer than anticipated, as life had its own
plans; specifically, my first child decided to join the party
mid-analysis! <br> Therefore, this work is **dedicated to my newborn**,
who brought an extra dose of joy to the process 🚀

This case study is also available on **Kaggle** at the following
[**link**](https://www.kaggle.com/code/lcolon/cyclistic-2023-google-da-capstone-project-r).

<br>

# Table of Contents:

- [Context](#context)
- [Timeframe and Data Overview](#timeframe-and-data-overview)
- [Data Preparation](Data-preparation.md)
- [Data Processing](Data-processing.md)
- [Data Analysis](Data-analysis.md)
- [Summary of Key findings](Key-findings.md)
- [Recommendations for the business](Recommendations.md)

<br>

# Context

<br>

[⬅️ **Back to Table of contents**](#table-of-contents)

<br>

- **Cyclistic** is a fictional bike-share company in Chicago, offering
  both electric and traditional bikes *(however, the underlying data of
  this case study pertains to [**Divvy**](https://divvybikes.com/), a
  real-world bike-sharing service operating in Chicago*)

<hr style="border: none; border-top: 1px solid #ccc; margin: 25px 0;">

- The company categorizes users into 2 main groups:
  - **“Casual”** users , who buy single-rides or full-day passes
  - **“Member”** users, who purchase annual memberships

<hr style="border: none; border-top: 1px solid #ccc; margin: 25px 0;">

- Cyclistic believes that **members are more profitable** than casual
  riders and aims at designing marketing strategies to **convert casual
  riders into annual members**

<hr style="border: none; border-top: 1px solid #ccc; margin: 25px 0;">

- In order to accomplish this, the company seeks the answer to the
  following question, which will also serve as the **objective of the
  analysis**:

<br>
<p align="center">
⭐ How do <strong>members</strong> and <strong>casual</strong> riders
use Cyclistic bikes differently? ⭐
</p>

<br>
<br>

# Timeframe and Data Overview

<br>

[⬅️ **Back to Table of contents**](#table-of-contents)

<br>

<span style="color: #0047AB;">📌 <strong>Time Scope</strong></span>

- January 2023 to December 2023

<hr style="border: none; border-top: 1px solid #ccc; margin: 25px 0;">

<span style="color: #0047AB;">📌 <strong>Data Used</strong></span>

- The
  <a href="https://divvy-tripdata.s3.amazonaws.com/index.html"><strong>original
  dataset</strong></a> is provided by Motivate International Inc., which
  operates the bicycle sharing service, and it is available under
  <a href="https://divvybikes.com/data-license-agreement"><strong>this
  license agreement</strong></a>. It is also available
  <a href="https://kaggle.com/datasets/ad7f2fc87d18cb5347535cf818f971073f3ad1076f1758bceefdca75a53ded81"><strong>here</strong></a>
  on Kaggle

<hr style="border: none; border-top: 1px solid #ccc; margin: 25px 0;">

<span style="color: #0047AB;">📌 <strong>External
Resources</strong></span>

- Chicago 2023 weather data was sourced from the National Oceanic and
  Atmospheric Administration (NOAA) Online Weather Data, accessible on
  the <a href="https://www.weather.gov/wrh/climate?wfo=lot"><strong>U.S.
  National Weather Service (NWS) website</strong></a> <em>(“2023 Monthly
  Avg Temperature for Chicago Area, IL”</em> and <em>“2023 Monthly Mean
  Precipitation for Chicago Area, IL”</em>), available under
  <a href="https://www.weather.gov/disclaimer/"><strong>this
  license</strong></a>
- The `.geojson` map of Chicago was obtained by the
  <a href="https://data.cityofchicago.org/Facilities-Geographic-Boundaries/Boundaries-Neighborhoods/bbvz-uum9"><strong>Chicago
  Data portal website</strong></a>, and is provided under
  <a href="https://www.chicago.gov/city/en/narr/foia/data_disclaimer.html"><strong>these
  terms of use</strong></a>
