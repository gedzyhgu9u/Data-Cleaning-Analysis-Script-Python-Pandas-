# Data-Cleaning-Analysis-Script-Python-Pandas-
This Python script uses Pandas to automate data cleaning. It loads a messy sales dataset, then cleans and transforms it by fixing inconsistent text, handling missing values, and correcting data types (e.g., '$' prices). It then calculates key insights like total revenue and exports the final, clean data to a new CSV.
# Make sure you have 'pandas' installed!
# Open your PyCharm Terminal and run: pip install pandas
import pandas as pd
import numpy as np


def clean_and_analyze_data():
    """
    A self-contained script to demonstrate cleaning and analyzing
    a messy dataset using pandas.
    """

    # --- STEP 1: CREATE MESSY DATA ---
    # We'll create it here as a dictionary.
    messy_data = {
        'OrderID': [101, 102, 103, 104, 105, 106, 107, 108],
        'Product': ['Laptop', 'Mouse', 'Keyboard', 'Monitor', 'Laptop', 'Mouse', 'Webcam', 'Mouse'],
        'Quantity': [1, 2, None, 1, 1, 1, 2, 3],  # Has a missing value (None)
        'Price': ['$1200.00', '$25.50', '$80.00', '$300.00', '$1250.00', '$25.50', None, '$24.99'],
        # Strings, has a missing value
        'City': ['London', 'Manchester', 'london', 'Birmingham', 'Manchester', None, 'Bristol', 'MANCHESTER'],
        # Inconsistent cases, missing
        'InternalCode': ['XF-1', 'XF-2', 'XF-3', 'XF-4', 'XF-5', 'XF-6', 'XF-7', 'XF-8']  # Useless column
    }

    df = pd.DataFrame(messy_data)

    print("--- 1. Original Messy Data ---")
    print(df)
    print("\n" + "=" * 30 + "\n")

    # --- STEP 2: CLEAN THE DATA ---

    # 2a. Clean 'Price' column
    # Remove '$' and ',' characters, then convert to a number (float)
    # errors='coerce' will turn any unfixable values (like None) into 'NaN' (Not a Number)
    df['Price'] = df['Price'].replace('[\$,]', '', regex=True).astype(float)

    # Fill missing prices with the average price of all other products
    average_price = df['Price'].mean()
    df['Price'] = df['Price'].fillna(average_price)

    # 2b. Clean 'Quantity' column
    # Fill missing quantity with 0 (assuming it was a failed order)
    df['Quantity'] = df['Quantity'].fillna(0).astype(int)

    # 2c. Clean 'City' column
    # Standardize all city names to 'Title Case' (e.g., 'london' -> 'London')
    df['City'] = df['City'].str.title()
    # Fill missing cities with 'Unknown'
    df['City'] = df['City'].fillna('Unknown')

    # 2d. Remove useless 'InternalCode' column
    df = df.drop(columns=['InternalCode'])

    # 2e. Create a new 'TotalSale' column
    df['TotalSale'] = df['Quantity'] * df['Price']

    print("--- 2. Cleaned & Transformed Data ---")
    print(df)
    print("\n" + "=" * 30 + "\n")

    # --- STEP 3: ANALYZE THE DATA ---
    print("--- 3. Basic Analysis ---")

    # Total revenue
    total_revenue = df['TotalSale'].sum()
    print(f"Total Revenue: £{total_revenue:,.2f}")  # Formatted as Pounds

    # Average quantity per order
    avg_quantity = df['Quantity'].mean()
    print(f"Average Quantity per Order: {avg_quantity:.2f}")

    # Sales by city
    sales_by_city = df.groupby('City')['TotalSale'].sum().sort_values(ascending=False)
    print("\nTotal Sales by City:")
    print(sales_by_city)

    # Top selling products (by quantity)
    sales_by_product = df.groupby('Product')['Quantity'].sum().sort_values(ascending=False)
    print("\nTotal Quantity Sold by Product:")
    print(sales_by_product)
    print("\n" + "=" * 30 + "\n")

    # --- STEP 4: EXPORT THE CLEAN DATA ---
    # This will save the file in the same folder as your Python script
    output_filename = "cleaned_sales_data.csv"
    df.to_csv(output_filename, index=False)

    print(f"--- 4. Success! ---")
    print(f"Cleaned data has been saved to '{output_filename}'")
    print("Check your project folder to find this new file.")


# --- This line runs the function when you hit 'Run' in PyCharm ---
if __name__ == "__main__":
    clean_and_analyze_data()
