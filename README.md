# Phase 1 Project



## Project Overview


### Business Problem

Microsoft sees all the big companies creating original video content and they want to get in on the fun. They have decided to create a new movie studio, but they don’t know anything about creating movies. You are charged with exploring what types of films are currently doing the best at the box office. You must then translate those findings into actionable insights that the head of Microsoft's new movie studio can use to help decide what type of films to create.

### The Data

In the folder `zippedData` are movie datasets from:

* [Box Office Mojo](https://www.boxofficemojo.com/)
* [IMDB](https://www.imdb.com/)
* [Rotten Tomatoes](https://www.rottentomatoes.com/)
* [TheMovieDB](https://www.themoviedb.org/)
* [The Numbers](https://www.the-numbers.com/)

I used datasets from these 3

[Box Office Mojo](https://www.boxofficemojo.com/)
[IMDB](https://www.imdb.com/)
 [The Numbers](https://www.the-numbers.com/)


# Loading the datasets
tn_df = pd.read_csv("zippedData/tn.movie_budgets.csv.gz", index_col=0)
imdb_df = pd.read_sql("""SELECT * FROM movie_basics  JOIN movie_ratings USING(movie_id);""", conn)
bom_df = pd.read_csv("zippedData/bom.movie_gross.csv.gz")

## Data Cleaning


### IMDB Dataset
imdb_df["genres"].fillna("missing", inplace=True)
imdb_df.drop(columns=["movie_id","original_title"], inplace=True)


### Box office Mojo Dataset
bom_df.drop(columns=["foreign_gross"], inplace=True)
bom_df = bom_df.rename(columns={'title': 'movie'})

### The Numbers Dataset

#### This was how I hanged the values to number type, and got rid of  Dollarsigns and Commas
tn_df['domestic_gross'] = tn_df['domestic_gross'].str.replace('$', '').str.replace(',', '')
tn_df['production_budget'] = tn_df['production_budget'].str.replace('$', '').str.replace(',', '')
tn_df['worldwide_gross'] = tn_df['worldwide_gross'].str.replace('$', '').str.replace(',', '')

tn_df["domestic_gross"]=pd.to_numeric(tn_df["domestic_gross"])

### Combining the datasets with Merge
merged_df = imdb_df.merge(bom_df, on=['movie', 'year']).merge(tn_df, on=['movie', 'year'])

### creating a grouped dataset to compare monthly data
merged_df['month'] = merged_df['release_date'].dt.month 
grouped_df = merged_df.groupby(['year', 'month'])["worldwide_gross"].mean()


# Visualizations

### Scatter plot and Histogram to compare the Average rating and Worldwide Gross as well as Domestic

![rating vs gross](https://user-images.githubusercontent.com/127976914/232333724-d267ce0d-7381-4598-93cc-4abcdb59cc04.png)
