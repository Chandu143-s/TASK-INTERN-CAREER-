#task1

import requests
from bs4 import BeautifulSoup
import pandas as pd
from datetime import datetime

def scrape_times_of_india():
    # URL of the Times of India news section
    url = 'https://timesofindia.indiatimes.com/home/headlines'
    
    # Send a GET request to the website
    response = requests.get(url)
    
    # Check if the request was successful
    if response.status_code == 200:
        # Parse the content with BeautifulSoup
        soup = BeautifulSoup(response.content, 'html.parser')
        
        # Find the relevant data (e.g., headlines)
        headlines = soup.find_all('span', class_='w_tle')
        
        # Extract and organize the data
        data = []
        for headline in headlines:
            title = headline.get_text()
            link = headline.find('a')['href']
            full_link = f"https://timesofindia.indiatimes.com{link}"
            data.append({'Title': title, 'Link': full_link, 'Date': datetime.now().strftime('%Y-%m-%d %H:%M:%S')})
        
        # Save the data to a CSV file
        df = pd.DataFrame(data)
        df.to_csv('times_of_india_headlines.csv', index=False)
        print('Scraping completed and data saved to times_of_india_headlines.csv')
    else:
        print(f"Failed to retrieve the webpage. Status code: {response.status_code}")

# Run the scraper
scrape_times_of_india()

#task2

#Title,Link,Date
#"Headline 1","https://timesofindia.indiatimes.com/...", "2024-05-25 08:00:00"
#"Headline 2","https://timesofindia.indiatimes.com/...", "2024-05-25 08:05:00"
#...

import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

def read_dataset(file_path):
    """Reads the dataset from a CSV file."""
    return pd.read_csv(file_path)

def calculate_summary_statistics(df):
    """Calculates summary statistics."""
    stats = df.describe()
    print("Summary Statistics:\n", stats)
    return stats

def filter_data(df, keyword):
    """Filters data based on a specific keyword in the title."""
    filtered_df = df[df['Title'].str.contains(keyword, case=False, na=False)]
    return filtered_df

def generate_visualizations(df):
    """Generates data visualizations."""
    # Convert 'Date' to datetime for better analysis
    df['Date'] = pd.to_datetime(df['Date'])

    # Plot the number of headlines per day
    plt.figure(figsize=(10, 6))
    df['Date'].dt.date.value_counts().sort_index().plot(kind='bar')
    plt.title('Number of Headlines per Day')
    plt.xlabel('Date')
    plt.ylabel('Number of Headlines')
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.savefig('headlines_per_day.png')
    plt.show()

def save_processed_data(df, output_path):
    """Saves the processed data to a new CSV file."""
    df.to_csv(output_path, index=False)

def main(file_path, output_path, keyword):
    df = read_dataset(file_path)
    calculate_summary_statistics(df)
    filtered_df = filter_data(df, keyword)
    generate_visualizations(filtered_df)
    save_processed_data(filtered_df, output_path)

if __name__ == "__main__":
    file_path = 'times_of_india_headlines.csv'
    output_path = 'filtered_headlines.csv'
    keyword = 'COVID'  # Example keyword for filtering
    main(file_path, output_path, keyword)
import sys

def main(file_path, output_path, keyword):
    df = read_dataset(file_path)
    calculate_summary_statistics(df)
    filtered_df = filter_data(df, keyword)
    generate_visualizations(filtered_df)
    save_processed_data(filtered_df, output_path)

if __name__ == "__main__":
    if len(sys.argv) != 4:
        print("Usage: python script.py <input_file_path> <output_file_path> <keyword>")
        sys.exit(1)
    file_path = sys.argv[1]
    output_path = sys.argv[2]
    keyword = sys.argv[3]
    main(file_path, output_path, keyword)
