# Experiment 3: Delhi Air Quality Analysis

```text
**Name:** Oswald Shilo  
**Reg.No:** 212223040139  
```

## Aim
To analyze the temporal variation of PM2.5 air quality parameters in Delhi, focusing on monthly distributions, daily averages against WHO limits, and hourly trends to understand pollution patterns.


## Algorithm / Procedure

1. **Load Data**
   - Import the `delhi_dataset_3.csv` file using Pandas.

2. **Preprocessing**
   - Convert `period.datetimeFrom.utc` to `datetime` objects.
   - Convert the `value` column to numeric, handling errors.
   - Drop rows with missing dates or values.

3. **Feature Engineering**
   - Extract `date`, `month`, `year`, and `hour` into new columns.

4. **Monthly Analysis**
   - Create a box plot to visualize monthly PM2.5 spread and outliers.
   - Compute and plot monthly average PM2.5 to identify seasonal trends.

5. **Safety Compliance Check**
   - Calculate daily average PM2.5.
   - Count days exceeding WHO PM2.5 limit (25 µg/m³) and compute percentage.

6. **Diurnal Analysis**
   - Group data by hour to analyze pollution levels throughout the day.

7. **Extremes**
   - Identify and print the top 5 days with the highest daily average PM2.5.


## Program (Python)

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# 1. Load Data
df = pd.read_csv('delhi_dataset_3.csv')

# 2. Inspect Data
print(df.head())
print(df.info())
print(df.describe())

# 3. Preprocessing
df['period.datetimeFrom.utc'] = pd.to_datetime(
    df['period.datetimeFrom.utc'], errors='coerce'
)
df['value'] = pd.to_numeric(df['value'], errors='coerce')
df.dropna(subset=['period.datetimeFrom.utc', 'value'], inplace=True)

# 4. Feature Extraction
df['date'] = df['period.datetimeFrom.utc'].dt.date
df['month'] = df['period.datetimeFrom.utc'].dt.month_name()
df['year'] = df['period.datetimeFrom.utc'].dt.year
df['hour'] = df['period.datetimeFrom.utc'].dt.hour

# 5. Visualization: Monthly Distribution (Box Plot)
plt.figure(figsize=(10, 6))
sns.boxplot(
    x='month',
    y='value',
    data=df,
    order=[
        'January', 'February', 'March', 'April', 'May', 'June',
        'July', 'August', 'September', 'October', 'November', 'December'
    ],
    palette='coolwarm'
)
plt.xticks(rotation=45)
plt.title('Monthly Distribution of PM2.5 Concentration')
plt.xlabel('Month')
plt.ylabel('PM2.5 (µg/m³)')
plt.tight_layout()
plt.show()

# 6. Visualization: Monthly Averages (Line Plot)
monthly_order = [
    'January', 'February', 'March', 'April', 'May', 'June',
    'July', 'August', 'September', 'October', 'November', 'December'
]

monthly_avg = (
    df.groupby('month')['value']
    .mean()
    .reindex(monthly_order)
)

plt.figure(figsize=(10, 5))
sns.lineplot(x=monthly_avg.index, y=monthly_avg.values, marker='o', color='crimson')
plt.xticks(rotation=45)
plt.title('Monthly Average PM2.5 Levels')
plt.xlabel('Month')
plt.ylabel('Average PM2.5 (µg/m³)')
plt.tight_layout()
plt.show()

print("\nMonthly Average PM2.5 (µg/m³):")
print(monthly_avg.round(2))

# 7. WHO Limit Compliance Check
daily_avg = df.groupby('date')['value'].mean()
total_days = len(daily_avg)
exceed_days = (daily_avg > 25).sum()
exceed_pct = (exceed_days / total_days) * 100

print(f"\nTotal days recorded: {total_days}")
print(f"Days exceeding WHO PM2.5 limit (25 µg/m³): {exceed_days}")
print(f"Percentage of exceedance days: {exceed_pct:.2f}%")

# 8. Visualization: Hourly Trends
hourly_avg = df.groupby('hour')['value'].mean()

plt.figure(figsize=(10, 5))
sns.lineplot(x=hourly_avg.index, y=hourly_avg.values, marker='o', color='darkorange')
plt.xticks(range(0, 24))
plt.title('Average PM2.5 by Hour of Day')
plt.xlabel('Hour (0–23)')
plt.ylabel('Average PM2.5 (µg/m³)')
plt.grid(True)
plt.tight_layout()
plt.show()

print("\nAverage PM2.5 by Hour of Day:")
print(hourly_avg.round(2))

# 9. Top 5 Worst Days
top5 = daily_avg.sort_values(ascending=False).head(5)
print("\nTop 5 Worst-Polluted Days (Highest Daily Average PM2.5):")
print(top5.round(2))
```


## Output
**Figures generated from the above code:**

<img width="1680" height="1050" alt="1" src="https://github.com/user-attachments/assets/a8859f62-eb39-424d-bdb2-74064a2349f5" /> <img width="1680" height="1050" alt="2" src="https://github.com/user-attachments/assets/7d875006-c0c0-4bb3-bafd-5523a88b3699" /> <img width="1680" height="1050" alt="3" src="https://github.com/user-attachments/assets/a6d94e10-0488-4d59-bcb2-cd05a3c1f670" /> <img width="1680" height="1050" alt="4" src="https://github.com/user-attachments/assets/f10d3c7e-4638-463b-af6f-fad831bcf1b6" /> <img width="1680" height="1050" alt="5" src="https://github.com/user-attachments/assets/c4dc2c6a-f029-4b0b-a7cf-1d8740e896b8" /> <img width="1680" height="1050" alt="6" src="https://github.com/user-attachments/assets/306e0167-657d-454a-925d-839657008f8d" /> <img width="1680" height="1050" alt="7" src="https://github.com/user-attachments/assets/eed43f21-0a8a-4963-9963-1770f613019e" />



## Inference
**Seasonal Variation:**
PM2.5 levels peak significantly during winter months (November–January), consistent with inversion layers and increased emissions. Levels drop during monsoon months (June–August) due to rainfall and better dispersion.

**Diurnal Patterns:**
Higher PM2.5 concentrations are observed at night and early morning, likely tied to lower boundary-layer height and traffic patterns. Levels dip during the afternoon when atmospheric mixing improves.

**Health Hazard:**
A high percentage of days exceed the WHO daily safety limit of 25 µg/m³, indicating chronic exposure and serious public health risks, especially for respiratory and cardiovascular systems.

## Result
The dataset was successfully loaded, cleaned, and analyzed. The temporal analysis confirmed strong seasonal and diurnal fluctuations in Delhi’s PM2.5 levels, reinforcing concerns about persistent air pollution and its health impacts.
