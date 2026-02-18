# NYC-2020-Accident-Data-Cleaning--Python
Cleaned and Prepare this project and the **NYC 2020 Accident dataset** for analysis. The dataset contains information about vehicle types, contributing factors, locations, and casualties.

# data_cleaning.py
# Author: Abdul Rahman Kassim
# Purpose: Clean NYC 2020 Accident dataset

import pandas as pd

# -----------------------------
# Load Data
# -----------------------------
df = pd.read_csv("NYC_Accidents_2020.csv")

# -----------------------------
# Drop unwanted vehicle columns
# -----------------------------
vehicle_cols_to_drop = [
    "VEHICLE TYPE CODE 1",
    "VEHICLE TYPE CODE 3",
    "VEHICLE TYPE CODE 4",
    "VEHICLE TYPE CODE 5"
]

factor_cols_to_drop = [
    "CONTRIBUTING FACTOR VEHICLE 2",
    "CONTRIBUTING FACTOR VEHICLE 3",
    "CONTRIBUTING FACTOR VEHICLE 4",
    "CONTRIBUTING FACTOR VEHICLE 5"
]

df.drop(columns=vehicle_cols_to_drop + factor_cols_to_drop, inplace=True, errors='ignore')

# -----------------------------
# Split VEHICLE TYPE CODE 2 into Type and Category
# -----------------------------
df[["Vehicle Type", "Vehicle Category"]] = df["VEHICLE TYPE CODE 2"].str.split("/", n=1, expand=True)

# Fill missing vehicle information
cols = ["VEHICLE TYPE CODE 2", "Vehicle Type", "Vehicle Category"]
df[cols] = df[cols].fillna("Unknown")

# Drop the original code column
df.drop(columns="VEHICLE TYPE CODE 2", inplace=True)

# -----------------------------
# Drop unnecessary location columns
# -----------------------------
df.drop(columns=["CROSS STREET NAME", "OFF STREET NAME"], inplace=True, errors='ignore')

# -----------------------------
# Rename columns for readability
# -----------------------------
df.rename({
    "ON STREET NAME": "Street Name",
    "NUMBER OF PERSONS INJURED": "Persons Injured",
    "NUMBER OF PERSONS KILLED": "Persons Killed",
    "NUMBER OF PEDESTRIANS INJURED": "Pedestrians Injured",
    "NUMBER OF PEDESTRIANS KILLED": "Pedestrians Killed",
    "NUMBER OF CYCLIST INJURED": "Cyclist Injured",
    "NUMBER OF MOTORIST INJURED": "Motorist Injured",
    "NUMBER OF MOTORIST KILLED": "Motorist Killed",
    "NUMBER OF CYCLIST KILLED": "Cyclist Killed",
    "CONTRIBUTING FACTOR VEHICLE 1": "Factor",
    "COLLISION_ID": "Collision_Id",
    "CRASH DATE": "Crash Date",
    "CRASH TIME": "Crash Time",
    "BOROUGH": "Borough",
    "ZIP CODE": "Zip Code"
}, axis=1, inplace=True)

# -----------------------------
# Fill missing Boroughs
# -----------------------------
df["Borough"] = df["Borough"].fillna("Unknown")

# -----------------------------
# Drop duplicates
# -----------------------------
df.drop_duplicates(inplace=True)

# -----------------------------
# Handle missing Factor column values
# -----------------------------
df = df[df["Factor"].notna()]

# -----------------------------
# Optional: Date & Time Features
# -----------------------------
df["Crash Date"] = pd.to_datetime(df["Crash Date"], errors='coerce')
df["Month Crash"] = df["Crash Date"].dt.strftime("%b")
df["Day Crash"] = df["Crash Date"].dt.strftime("%a")
df["Year"] = df["Crash Date"].dt.year

df["Crash Time"] = pd.to_datetime(df["Crash Time"], errors='coerce').dt.hour

# -----------------------------
# Save cleaned data
# -----------------------------
df.to_csv("Accidents_2.csv", index=False)

print("Data cleaning complete! File saved as 'Accidents_2.csv'.")
