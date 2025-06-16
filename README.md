# PRODIGY_DS_05
Analyze traffic accident data to identify patterns related to road conditions, weather, and time of day. Visualize accident hotspots and contributing factors.

CODE:
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import folium
from folium.plugins import HeatMap

# Load dataset
df = pd.read_csv(r"C:\Users\Navaneet\Downloads\archive\US_Accidents.csv")

# Filter relevant columns
df = df[['Start_Time', 'Severity', 'Start_Lat', 'Start_Lng',
         'Weather_Condition', 'Visibility(mi)', 'Weather_Timestamp', 'Sunrise_Sunset']]

# Convert to datetime
df['Start_Time'] = pd.to_datetime(df['Start_Time'], dayfirst=True, errors='coerce')
df.dropna(subset=['Start_Time'], inplace=True)

# Extract time features
df['Hour'] = df['Start_Time'].dt.hour
df['DayOfWeek'] = df['Start_Time'].dt.day_name()
df['Month'] = df['Start_Time'].dt.month_name()

# Drop rows with missing location/weather info
df.dropna(subset=['Start_Lat', 'Start_Lng', 'Weather_Condition', 'Visibility(mi)'], inplace=True)

# Plot 1: Accidents by Hour
plt.figure(figsize=(12,6))
sns.countplot(x='Hour', data=df, palette='viridis')
plt.title('Accidents by Hour of Day')
plt.xlabel('Hour')
plt.ylabel('Number of Accidents')
plt.grid(True)
plt.tight_layout()
plt.show()

# Plot 2: Top 10 Weather Conditions
plt.figure(figsize=(14,6))
top_weather = df['Weather_Condition'].value_counts().head(10)
sns.barplot(x=top_weather.index, y=top_weather.values, palette='coolwarm')
plt.title('Top 10 Weather Conditions During Accidents')
plt.xticks(rotation=45)
plt.ylabel('Accident Count')
plt.grid(True)
plt.tight_layout()
plt.show()

# Plot 3: Accidents by Day of the Week
plt.figure(figsize=(10,5))
sns.countplot(data=df, x='DayOfWeek',
              order=['Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday'],
              palette='magma')
plt.title("Accidents by Day of the Week")
plt.ylabel('Number of Accidents')
plt.tight_layout()
plt.show()

# Create heatmap from sample data
sample_df = df[['Start_Lat', 'Start_Lng']].sample(n=10000, random_state=42)

# Create base map with proper tile and attribution
accident_map = folium.Map(
    location=[39.5, -98.35],
    zoom_start=4,
    tiles='https://stamen-tiles.a.ssl.fastly.net/toner/{z}/{x}/{y}.png',
    attr='Map tiles by Stamen Design, CC BY 3.0 — Map data © OpenStreetMap contributors'
)

# Add heatmap layer
HeatMap(data=sample_df.values, radius=8).add_to(accident_map)

# Save to HTML file
accident_map.save("accident_heatmap.html")

![Screenshot 2025-06-16 213750](https://github.com/user-attachments/assets/3d0e25b7-6e97-481c-8761-1e37f16d5fa5)
![Screenshot 2025-06-16 213827](https://github.com/user-attachments/assets/cf80ee66-aaeb-486b-a735-c500712ea860)
![Screenshot 2025-06-16 213846](https://github.com/user-attachments/assets/768ce65a-d8e5-4347-9281-3aff0e22fefd)
