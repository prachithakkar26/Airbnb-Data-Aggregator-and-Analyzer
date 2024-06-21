# Airbnb Data Aggregator and Analyzer

This Python script aggregates and analyzes Airbnb listing data from multiple CSV files. It cleans the data and provides visualizations for numerical columns, including price distributions.

## Prerequisites

To run this code, you need to have the following libraries installed:

- Pandas (`pandas`)
- Matplotlib (`matplotlib`)
- re (standard library)

You can install the required libraries using pip:

    pip install pandas matplotlib


## Usage

1. Import the required libraries:

```python
import pandas as pd
import matplotlib.pyplot as plt
import re
```

2. Define the file paths for the CSV files:

```python
file_paths = [
    '/Users/prachithakkar/Desktop/airbnb_scraper/listing1.csv',
    '/Users/prachithakkar/Desktop/airbnb_scraper/listing2.csv',
    '/Users/prachithakkar/Desktop/airbnb_scraper/listing3.csv',
    '/Users/prachithakkar/Desktop/airbnb_scraper/listing4.csv'
]
```

3. Load and concatenate the CSV files into a single DataFrame:
```python
# List to hold dataframes
df_list = []

# Load each CSV file into a DataFrame and append to list
for file_path in file_paths:
    df = pd.read_csv(file_path)
    df_list.append(df)

# Concatenate all DataFrames into one
df = pd.concat(df_list, ignore_index=True)
```

4. Display basic info about the DataFrame:

```python
print("Original DataFrame:")
print(df.head())  # Displaying the first few rows to understand the data structure
print(df.info())  # Displaying DataFrame information
```

5. Drop unnecessary columns:

```python
columns_to_drop = [
    'City',  # Entirely null
    'Early Bird Discount',  # Entirely null
    'Check-in Date (Search Input)',  # Entirely null
    'Check-out Date (Search Input)',  # Entirely null
    '# of Adults (Search Input)',  # Entirely null
    'Subtitle',  # Entirely null
    'Guests (Premium Data)',  # Entirely null
    'Data as Of (Premium Data)',  # Entirely null
    'Data Start Date (for 30 Day Intervals) (Premium Data)',  # Entirely null
    '% Booked 1st 30 Days (Premium Data)',  # Entirely null
    '% Booked Days 31-60 (Premium Data)',  # Entirely null
    '% Booked Days 61-90 (Premium Data)',  # Entirely null
    'Data Start Date (for Months) (Premium Data)',  # Entirely null
    'Month 1 (Premium Data)',  # Entirely null
    '% Booked in Month 1 (Premium Data)',  # Entirely null
    'Month 2 (Premium Data)',  # Entirely null
    '% Booked in Month 2 (Premium Data)',  # Entirely null
    'Month 3 (Premium Data)',  # Entirely null
    '% Booked in Month 3 (Premium Data)',  # Entirely null
    'Data Start Date (for Reviews) (Premium Data)',  # Entirely null
    "Past Year's # of Reviews (Premium Data)"  # Entirely null
]

df.drop(columns=columns_to_drop, inplace=True)
```

6. Drop rows where all values are null:

```python
df.dropna(axis=0, how='all', inplace=True)
```

7. Drop rows where all values are null:

```python
# Function to clean and convert Price column to numeric
def clean_price(price_str):
    # Remove non-numeric characters and convert to float
    cleaned_price = re.sub(r'[^\d.]', '', price_str)  # Remove non-digit characters except '.'
    try:
        return float(cleaned_price)
    except ValueError:
        return None

# Clean 'Price' column
df['Price'] = df['Price'].apply(clean_price)

# Drop rows where 'Price' couldn't be converted
df.dropna(subset=['Price'], inplace=True)
```
8. Print summary information after cleaning:

```python
print("\nDataFrame after cleaning:")
print(df.head())  # Displaying the first few rows after cleaning
print(df.info())  # Displaying DataFrame information after cleaning
```

9. Plot histograms for numeric columns:

```python
# Plot histograms for numeric columns
numeric_cols = df.select_dtypes(include='number').columns

# Define bins for Price histogram (adjust as needed)
price_bins = [0, 50, 100, 150, 200, 250, 300, 350, 400, 450, 500]

# Plot histograms
for col in numeric_cols:
    plt.figure(figsize=(8, 5))
    if col == 'Price':
        df[col].plot(kind='hist', bins=price_bins, edgecolor='black', alpha=0.7)
    else:
        df[col].plot(kind='hist', bins=20, edgecolor='black', alpha=0.7)
    
    plt.title(f'Histogram of {col}')
    plt.xlabel(col)
    plt.ylabel('Frequency')
    plt.grid(True)
    plt.show()
```