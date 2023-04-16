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

### Checking whether Production Budget has a relation with the worldwide gross
![budget vs cost](https://user-images.githubusercontent.com/127976914/232334117-3dc0f5bc-8b1d-49dd-9b35-64a42e23c004.png)

### Top and Bottom 5 Genres with worldwide gross returns in the box office
![top bottom genres](https://user-images.githubusercontent.com/127976914/232334178-36ee71f2-c850-4aec-8928-1892e795b0bd.png)

### Per year look at the worldwide Gross mean of each month in the past 6 years
![per year monthly](https://user-images.githubusercontent.com/127976914/232334193-baa42cf9-0f9d-4954-b451-b8987ba99e05.png)


## Findings
### Budget
The production budget has very strong positive correlation with the domestic and worldwide grosses

### Average rating
-movies rated higher did much better in the box office,both for domestic and worlwide audiences, and in fact, movies rated lower than 5 did very poor

-curiously, the rating isn't really affected by increased budget, probably due to the fact that many things go into a production

-it would seem the average rating has a weak positive correlation with both domestic and worldwide gross,

### Genre
Generally genres dont really affect a rating, but the top 5 that make money worldwide are anything of the "adventure genre" involving scifi, animation and comedy

### Runtime
-Rating has a strong positive correlation with the runtime of the movie, but closer investigation shows the dirstibution is clustered within a value range of 80 to 140 minutes

### Month of release
-it would seem from the per-year and mean of 5 years bar plots, that in the middle of the year and in November, these are the best times to release movies

## Conlusions an Recommendations
1) Mirosoft should invest in the Genres of Adventure, with scifi,comedy,and/or animation, with main projects being of the genre "Adventure,Drama,Scifi" as it gets high reviews as well as high grossing worldwide

2) A high budget in these genres will give better returns worldwide, somewhere above the 100 million mark

3)A runtime of Between 80 minutes and 140 minutes is the most consistent at good ratings

4) Releasing between months 4-6, and at the tail end of the year might also give good worldwide grosses...probably because these are the months in which holidays occur eg easter, christmas
