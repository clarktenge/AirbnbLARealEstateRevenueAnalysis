# Airbnb Los Angeles Revenue Analysis

## Overview
This project analyzes **Airbnb revenue performance across major Los Angeles neighborhoods** to identify which **bedroom–bathroom configurations** outperform local and citywide averages. The goal is to generate **data-driven insights for short-term rental investment decisions**.

## Objectives
- Identify top-performing property configurations by neighborhood  
- Compare average neighborhood revenue to predicted revenue for specific unit types  
- Quantify performance relative to both local and global benchmarks  

## Data
- **Listings data (~600K rows):** Property attributes, location, bedrooms, bathrooms  
- **Calendar data (~600K rows):** Availability and nightly pricing over time  
- **Source:** Inside Airbnb  
- **Storage:** Parquet format for efficient large-scale ETL  

## Methods
- Data cleaning and feature engineering using **Python (Pandas, NumPy)**  
- Revenue aggregation by neighborhood and unit configuration  
- **OLS regression with interaction terms** to model neighborhood effects  
- **Decision trees** to capture nonlinear performance patterns  
- Model interpretation via statistical summaries and visualizations  

## Key Insights
- Certain bedroom–bathroom combinations significantly outperform neighborhood averages  
- Neighborhood effects often dominate unit size in explaining revenue differences  
- Smaller, high-demand units frequently deliver higher relative revenue  

## Tech Stack
- Python (Pandas, NumPy, Statsmodels, Scikit-learn)  
- Parquet for large-scale data storage  
- Jupyter / Google Colab for analysis and visualization  

