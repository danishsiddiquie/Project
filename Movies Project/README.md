
## Background and Objective

In this project, I explored a system for recommending movies to viewers. These so-called “Recommender Systems” are incredibly popular and used in applications such as presenting products to shoppers on Amazon, finding new music for listeners on Pandora and Spotify, suggesting pages and users on social media, and identifying potential romantic candidates on dating websites. From 2006 to 2009, Netflix ran a competition to improve their recommendation system by 10%, and eventually gave a $1,000,000 prize to a team that used methods similar to what I explored in this project

The objective of this project is to predict the rating a user will give to a movie before they have seen it, based on user's preferences. This information is then used to recommend new movies to a user on the basis of which movies I predict they would rate the highest.

**Machine Learning Method used: _k-nearest neighbours algorithm_**

The first approach is called content based, and the theory would be to compare the movie we want a prediction of to similar movies the user has rated. For example, Danielle likes romantic comedies and dislikes action movies, so we could predict she will like a new romantic movie.
The secon approach is called collaborative filtering, and the theory is to match the user to other similar users on the basis of their ratings. For example, Adam, Betty, and Carlos all like sci-fi action movies and dislike documentaries, so we could predict Adam’s review of a new movie using Betty and Carlos’s reviews of that movie.

I have explored both approaches as two separate entities:

```
Content-based approach: Movies-1.rmd

Collaborative filtering: Movies-2.rmd
```

## Author

Danish Siddiquie

- Operations Research Major, **_Columbia University in the City of New York_**
- Data Analytics Major, Math Minor, **_Denison University_**

## Acknowledgements

- Denison University's Analytics Department: to help clean and filter the data for this specific Analysis
- Dr. Anthony Bonifante, Assistant Professor: for Guidance
