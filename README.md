**Netflix Shows & Movies**

##### Name: Sai Vishal D
##### Register Number: 212223230180

**Aim**

To analyze Netflix dataset and compare movies vs TV shows, top producing countries, and release year trends.

**Procedure / Algorithm**

Step 1: Load and Explore the Dataset

Step 2: Analyze Missing Values

Step 3: Perform Data Indexing and Selection

Step 4: Create New Columns

Step 5: Perform Vectorized String Operations

Step 6: Perform Aggregation and Grouping

Step 7: Perform Frequency Analysis

Step 8: Create Pivot Tables

Step 9: Perform Hierarchical Indexing (MultiIndex)

Step 10: Combine DataFrames

Step 11: Analyze Netflix Additions Over Time

Step 12: Generate Summary Report
**Program**
```
import pandas as pd
##1) Load and Explore the Dataset
df=pd.read_csv("https://raw.githubusercontent.com/allenkong221/netflix-titles-dataset/main/netflix_titles.csv")
df
df.head()
df.tail()
print(df.shape)
print("Rows :",df.shape[0])
print("Columns :",df.shape[1])
for col in df.columns:
    print(col)
print(df.columns)
print(df.dtypes)
df.info()
df.describe()

##2) Analyze Missing Values
## a) Identify missing values in each column
df.isnull()
df.isnull().sum()
## b) Calculate missing-value percentage
(df.isnull().sum()/len(df))*100
print(df['director'].isnull().sum()/len(df['director'])*100)
## c) Replace missing values in director
df['director']=df['director'].fillna('Unknown')
print(df['director'].isnull().sum()/len(df['director'])*100)
## d) Replace missing values in country
df['country']=df['country'].fillna('Unknown')
## e) Replace missing values in cast
df['cast']=df['cast'].fillna('Unknown')
## f) Replace missing values in rating
df['rating']=df['rating'].fillna('Unknown')
## g) Check missing values after filling
df.isnull().sum()
## h) Create another DataFrame by removing rows with missing director and country
df_drop=df.dropna(subset=['director','country'])
## i) Display the new DataFrame
df_drop.head()

## 3) Perform Data Indexing and Selection
##a) Display only Movie records
movies=df.loc[df['type']=='Movie']
print(movies.head())
## b) Display only TV Show records
tv_show=df.loc[df['type']=='TV Show']
print(tv_show)
## c) Display only title, country, and release_year
print(df[['title','country','release_year']])
##d) Display rows from index 100 to 120
print(df.loc[100:120])
## e) Display Movies released after 2018
print(df.loc[(df['type']=='Movie') & (df['release_year']>2018)])
## f) Display titles produced in India
df.loc[df['country'].str.contains('India',na=False),['title','country']]
## g) Display TV Shows from the United States
df.loc[(df['type']=='TV Show') & (df['country'].str.contains("United States",na=False))]
## h) Display Movies with rating PG-13
df.loc[(df['type']=='Movie') & (df['rating']=='PG-13')]

##4) Create New Columns
## a) Create Title Length
df['Title Length']=df['title'].str.len()
print(df['Title Length'].head())
## b) Create Movie Age
df['Movie Age']=2026-df['release_year']
print(df['Movie Age'])
## c) Create Is Recent Content
df['Is Recent Content']=df['release_year']>2018
print(df['Is Recent Content'])
## d) Display the new columns
print(df[['title','release_year','Title Length','Movie Age','Is Recent Content']].head())
## e) Longest title
longest_index=df['Title Length'].idxmax()
print(df.loc[longest_index,'title'])
## f) Shortest title
shortest_index=df['Title Length'].idxmin()
print(df.loc[shortest_index,'title'])
## g) Average Movie Age
print(df['Movie Age'].mean())
##h) Oldest content
oldest_index=df['release_year'].idxmin()
print(df.loc[oldest_index,['title','release_year']])
##i) Newest content
newest_index=df['release_year'].idxmax()
print(df.loc[newest_index,['title','release_year']])

##5) Perform Vectorized String Operations
##a) Titles in uppercase
print(df['title'].str.upper())
## b) Titles in lowercase
print(df['title'].str.lower())
## c) Length of each title
print(df['title'].str.len())
## d) Titles containing Love
df.loc[df['title'].str.contains('Love',na=False,case=False),['title']]
## e) Titles containing Life
df.loc[df['title'].str.contains('Life',na=False,case='False'),['title']]
## f) Titles starting with The
df.loc[df['title'].str.startswith('The',na='False'),['title']]
## g) Titles ending with Story
df.loc[df['title'].str.endswith('Story',na='False'),['title']]
## h) Extract first genre from listed_in
df['Primary Genre']=df['listed_in'].str.split(',').str[0]
print(df['Primary Genre'].head())
## i) Display Primary Genre
df[['title', 'listed_in', 'Primary Genre']].head()

## 6) Perform Aggregation and Grouping
## a) Number of Movies and TV Shows
df.groupby('type')['title'].count()
## b) Average release year by content type
df.groupby('type')['release_year'].mean()
## c) Number of titles in each rating category
df.groupby('rating')['title'].count()

## 7) Perform Frequency Analysis
## a) Top 10 countries with highest number of Netflix titles
countries=df['country'].dropna().str.split(',').explode().str.strip()
countries.value_counts().head(10)
## b) Top 10 directors by number of titles
directors=df['director'].dropna().str.split(',').explode().str.strip()
directors.value_counts().head(10)
## c) Top 10 most common genres
genres=df['listed_in'].dropna().str.split(',').explode().str.strip()
genres.value_counts().head(10)

## 8) Create Pivot Tables
##a) Distribution of Movies and TV Shows across countries
country_data = df.assign(
    country=df['country'].str.split(',')
).explode('country')

country_data['country'] = country_data['country'].str.strip()

pivot_country = country_data.pivot_table(
    index='country',
    columns='type',
    values='title',
    aggfunc='count',
    fill_value=0
)
print(pivot_country)

## b) Distribution of Movies and TV Shows by release year
pivot_year = df.pivot_table(
    index='release_year',
    columns='type',
    values='title',
    aggfunc='count',
    fill_value=0
)
print(pivot_year)

## 9) Perform Hierarchical Indexing (MultiIndex)
## a) Create MultiIndex using country and type
multi_df = df.set_index(['country', 'type'])
## b) Display Movies from India
multi_df.loc['India', 'Movie']
## c) Display TV Shows from United States
multi_df.loc['United States', 'TV Show']

## 10) Combine DataFrames
## a) Split dataset into Movies and TV Shows
movies = df[df['type'] == 'Movie']
tv_shows = df[df['type'] == 'TV Show']
## b) Concatenate Movies and TV Shows
combined_df = pd.concat([movies, tv_shows])
print(combined_df.head())
## c) Create Title & Director DataFrame
title_director = df[['title', 'director']]
## d) Create Title & Country DataFrame
title_country = df[['title', 'country']]
## e) Merge using title
merged_df = pd.merge(
    title_director,
    title_country,
    on='title'
)
print(merged_df.head())

## 11) Analyze Netflix Additions Over Time
## a) Convert date_added
df['date_added'] = pd.to_datetime(df['date_added'], errors='coerce')
## b) Extract Year Added
df['Year Added'] = df['date_added'].dt.year
## c) Extract Month Added
df['Month Added'] = df['date_added'].dt.month_name()
## d) Display the new columns
print(df[['title', 'date_added', 'Year Added', 'Month Added']].head())
## e) Number of Movies and TV Shows added each year
yearly_additions = df.groupby(
    ['Year Added', 'type']
).size().unstack(fill_value=0)

print(yearly_additions)
## f) Number of Movies and TV Shows added each month
monthly_additions = df.groupby(
    ['Month Added', 'type']
).size().unstack(fill_value=0)
print(monthly_additions)

## 12) Generate a Summary Report
## a) Which country has the highest Netflix content?
countries = df['country'].dropna().str.split(',').explode().str.strip()
top_country = countries.value_counts().idxmax()
print("Country with highest Netflix content:", top_country)
print(countries.value_counts().head(10))

## b) Which year has the highest number of additions?
year_counts = df['Year Added'].value_counts()
highest_year = year_counts.idxmax()
highest_count = year_counts.max()
print("Year with highest additions:", highest_year)
print("Number of additions:", highest_count)
## c) What is the most common rating?
most_common_rating = df['rating'].value_counts().idxmax()
print("Most common rating:", most_common_rating)
## d) What is the most common genre?
genres = df['listed_in'].dropna().str.split(',').explode().str.strip()
most_common_genre = genres.value_counts().idxmax()
print("Most common genre:", most_common_genre)
## e) Percentage of Movies and TV Shows
type_counts = df['type'].value_counts()
type_percentage = (type_counts / len(df)) * 100
print(type_percentage)

## Meaningful observations
print("Country with highest content:", countries.value_counts().idxmax())
print("Year with highest additions:", df['Year Added'].value_counts().idxmax())
print("Most common rating:", df['rating'].value_counts().idxmax())
print("Most common genre:", genres.value_counts().idxmax())
print("Content percentage:")
print((df['type'].value_counts() / len(df)) * 100)
```

**Ouptut**
<img width="557" height="727" alt="image" src="https://github.com/user-attachments/assets/53b02d04-a479-4fbc-a33f-7887fb50bdde" />
<img width="495" height="367" alt="image" src="https://github.com/user-attachments/assets/8671c2f0-aaef-4d0c-b41f-4733376d2070" />
<img width="1733" height="517" alt="image" src="https://github.com/user-attachments/assets/28585650-4940-436d-b1e9-9c83a8fd4591" />
<img width="582" height="578" alt="image" src="https://github.com/user-attachments/assets/57cc6504-ed7d-4b7b-85a4-c5bb2db06894" />
<img width="1800" height="486" alt="image" src="https://github.com/user-attachments/assets/90514fef-2981-41e6-af36-e66264c59387" />
<img width="730" height="762" alt="image" src="https://github.com/user-attachments/assets/922a1cca-5e9b-43ef-b62f-29202655434f" />
<img width="1057" height="245" alt="image" src="https://github.com/user-attachments/assets/e554bbe8-5058-4cb8-ad85-32c86d6e1f39" />
<img width="292" height="708" alt="image" src="https://github.com/user-attachments/assets/b6afbbb5-33a7-433c-83e4-7ba857ab473b" />
<img width="432" height="533" alt="image" src="https://github.com/user-attachments/assets/c20f6f54-148a-41d3-96aa-a633da2a3fc5" />
<img width="411" height="668" alt="image" src="https://github.com/user-attachments/assets/c095c5a6-3c94-4a96-917e-5b723444be0a" />
<img width="1782" height="667" alt="image" src="https://github.com/user-attachments/assets/5eec040c-533c-4b1f-a921-dac1f7864570" />
<img width="1783" height="160" alt="image" src="https://github.com/user-attachments/assets/ee040921-262b-4c5b-b638-7671ea7a8b94" />












**Result**
Helps Netflix in content planning & investments.
