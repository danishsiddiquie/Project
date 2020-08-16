
## Data

The data I used comes from MovieLens, a movie recommendation service. It contains 20,000,263 movie ratings from 138,493 users across 27,278 movies.
‘movies.csv’ contains the movieIds (a unique identifier), title, and genres of every movie.
‘ratings.csv’ contains ratings of users on the movies in a tidy format. Each row has a userId, movieId, and rating of that user of that movie. The file also contains a timestamp column of when the rating was created, which we will not use for this analysis. How might you incorporate this data as well?
‘genome-tags.csv’ contains descriptions for 1,128 movie properties (atmospheric, thought-provoking, realistic, etc.) called genomes and their corresponding unique identifier tagId number.
‘MovieGenome.csv’ contains data about how strongly movies exhibit each genome. The genome score was computed using a machine learning algorithm on user-contributed content including tags, ratings, and textual reviews. The structure is a dense matrix, meaning every movie in the genome has a value for every one of the 1,128 tags in the genome. This has the hilarious impact of Pixar’s “Toy Story” being 1.9% about vampire-human love, 2% neo-nazis, and 1.8% Jane Austen.

## Analysis

1. First, I read in the Movie Genome data, which contained one row per movie and one genome per column.

2. Performed a PCA (Principal Component Analysis). This data is perfect for a PCA: there are over a thousand columns, every row has a variable in every column, many columns are uninformative (What percentage of movies are primarily about Rio de Janeiro??), and there are high correlations between columns (if a movie is ‘silent’, it is almost certainly a ‘no dialog’ movie.)
