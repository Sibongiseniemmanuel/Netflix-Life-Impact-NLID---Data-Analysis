Netflix Life Impact Dataset (NLID) - Data Analysis Documentation
1. Introduction
The Netflix Life Impact Dataset (NLID) contains various attributes related to Netflix movies, including ratings, number of reviews, genres, and the percentage of users who suggested movies to friends and family. This documentation provides a detailed walkthrough of the steps involved in cleaning, transforming, and analyzing the dataset, along with the visualizations generated during the process.
________________________________________
2. Data Loading and Setup
Objective: Import necessary libraries and load the dataset into a pandas DataFrame.
Code:
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_csv(r"C:\Users\nteny\Desktop\DATASETS\Netflix Life Impact Dataset (NLID).csv", delimiter=';')
df.head()

Libraries Used:
•	pandas: For data manipulation and handling.
•	matplotlib: For data visualization (creating charts and plots).
•	seaborn: For statistical data visualization (enhanced plots).
Dataset:
•	The dataset is loaded from a CSV file located on the user's local system. The delimiter used in this dataset is ;.
________________________________________
3. Data Cleaning and Preprocessing
Objective: Prepare the dataset for analysis by cleaning and transforming the data.
Steps:
3.1 Standardizing Movie Titles
To ensure consistency, the movie title "Ma Raineys Black Bottom" was corrected to "Ma Rainey's Black Bottom".
Code:
df["Movie Title"] = df["Movie Title"]. replace ("Ma Raineys Black Bottom", "Ma Rainey's Black Bottom")

3.2 Extracting Information from Suggested to Friends/Family (Y/N %) Column
The original column "Suggested to Friends/Family (Y/N %)" was split into two new columns:
•	"Suggested Percentage": Contains the numeric percentage.
•	"Suggested": Contains either Y or N indicating whether the movie was suggested.

Code:
df[["Suggested Percentage", "Suggested"]] = df["Suggested to Friends/Family (Y/N %)"]\
    .str.extract(r"(\d+)%\s*(Y|N)", expand=True)

3.3 Dropping the Original Suggested to Friends/Family (Y/N %) Column
After extracting the relevant information, the original column was dropped from the dataset.

Code:
df.drop(columns=["Suggested to Friends/Family (Y/N %)"], inplace=True)

3.4 Converting Suggested Percentage to Numeric Format
The "Suggested Percentage" column, initially a string, was cleaned by removing the % symbol and converting the values to float
 
Code:
df['Suggested Percentage'] = df['Suggested Percentage'].str.replace('%', '').astype(float)

3.5 Exploding the Genre Column
The movies in the dataset could belong to multiple genres. The Genre column was split by the / delimiter, and rows were expanded so each genre has its own row.

Code:
df['Genre'] = df['Genre'].apply(lambda x: x.replace("['", "").replace("']", "").replace("\\", "").replace("'", '').strip())
df['Genre'] = df['Genre'].str.split('/')
df1 = df.explode('Genre')
df1['Genre'] = df1['Genre'].str.strip()

4. Exploratory Data Analysis (EDA)
Objective: Perform initial analysis to uncover insights from the dataset.
4.1 Top 10 Movies by Suggested Percentage
We identified the top 10 movies with the highest percentage of users recommending them to friends or family.

top10_suggested = df.nlargest(10, 'Suggested Percentage')

Visualization: Top 10 Most Suggested Netflix Movies

Code:

plt.figure(figsize=(18, 6))
sns.barplot(x='Suggested Percentage', y='Movie Title', data=top10_suggested, palette='viridis')
plt.title('Top 10 Most Suggested Netflix Movies')
plt.xlabel('Suggested Percentage')
plt.ylabel('Movie Title')
plt.tight_layout()
plt.show()

4.2 Top 10 Movies by Average Rating
We identified the top 10 movies with the highest average ratings.
Code:
top10_suggested = df1.nlargest(10, ‘Average Rating’)

Visualization: Top 10 Highly Rated Netflix Movies
Code:

plt.figure(figsize=(18, 6))
sns.lineplot(x='Average Rating', y='Movie Title', data=top10_suggested, marker='o')
plt.title("Top 10 Netflix Movies that are Highly Rated")
plt.xlabel("Average Rating")
plt.ylabel("Movie Title")
plt.tight_layout()
plt.show()


4.3 Genre-based Reviews Analysis
We grouped the data by genre and summed up the number of reviews for each genre.

Code:

genre_reviews = df1.groupby('Genre')['Number of Reviews'].sum().sort_values(ascending=False).reset_index()


Visualization: Top Genres by Total Number of Reviews

Code:
plt.figure(figsize=(14, 8))
sns.barplot(x='Number of Reviews', y='Genre', data=genre_reviews, palette='viridis')
plt.title('Top Genres by Total Number of Reviews on Netflix')
plt.xlabel('Total Number of Reviews')
plt.ylabel('Genre')
plt.tight_layout()
plt.show()
5. Saving the Cleaned Dataset
Objective: Save the transformed and cleaned dataset for future analysis or sharing.

Code:
df1.to_csv(r"C:\Users\nteny\Desktop\DATASETS\Netflix Life Impact Dataset (NLID)_cleaned.csv")

The cleaned dataset is saved as a CSV file for future use, and it is now ready for deeper analysis or integration into reporting tools.
________________________________________
6. Conclusion
Key Insights:
1.	The dataset has been cleaned and transformed, including the extraction of useful features from mixed columns.
2.	The analysis of the top 10 most suggested movies and top-rated movies provides valuable insights into user preferences.
3.	The genre-based review analysis shows which genres are receiving the most attention from Netflix users.
Next Steps:
•	Further exploration into additional factors such as user demographics, sentiment analysis, or movie duration could provide deeper insights.
•	Integration of the dataset into business intelligence tools such as Power BI or Dash for dynamic visualization and reporting.
•	Further statistical analysis or machine learning models to predict ratings based on genre or other features.


