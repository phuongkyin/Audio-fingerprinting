# Audio-fingerprinting

Audio fingerprinting is the process of identifying unique characteristics from a fixed duration audio stream. Such unique characteristics can be identified for all existing songs and stored in a database. When we hear a new song, we can extract similar characteristics from the recorded audio and compare against the database to identify the song. However, in practice there will be two challenges with this approach:
High dimensionality of the unique characteristic/feature vector required to identify songs
Comparison of the recorded audio features against features of all songs in the database is expensive in terms of time and memory

The first challenge can be addressed using a dimensionality reduction technique like PCA and the second using a combination of clustering and nearest neighbor search. Locality Sensitive Hashing (hereon referred to as LSH) can address both the challenges by
reducing the high dimensional features to smaller dimensions while preserving the differentiability
grouping similar objects (songs in this case) into same buckets with high probability

Applications of LSH
Before jumping into understanding LSH, it is worth noting that the application areas include
Recommender systems
Near-duplicate detection (document, online news articles, etc.)
Hierarchical clustering
Genome-wide association study
Image similarity identification (VisualRank)
Audio similarity identification
Digital video fingerprinting

What is LSH?
LSH is a hashing based algorithm to identify approximate nearest neighbors. In the normal nearest neighbor problem, there are a bunch of points (let’s refer to these as training set) in space and given a new point, objective is to identify the point in training set closest to the given point. Complexity of such process is linear [for those familiar with Big-O notation, O(N), where N is the size of training set]. An approximate nearest neighboring algorithm tries to reduce this complexity to sub-linear (less than linear but can be anything). Sub-linear complexity is achieved by reducing the number of comparisons needed to find similar items.
LSH works on the principle that if there are two points in feature space closer to each other, they are very likely to have same hash (reduced representation of data). LSH primarily differs from conventional hashing (aka cryptographic) in the sense that cryptographic hashing tries to avoid collisions but LSH aims to maximize collisions for similar points. In cryptographic hashing a minor perturbation to the input can alter the hash significantly but in LSH, slight distortions would be ignored so that the main content can be identified easily. The hash collisions make it possible for similar items to have a high probability of having the same hash value.
Locality Sensitive Hashing (LSH) is a generic hashing technique that aims, as the name suggests, to preserve the local relations of the data while significantly reducing the dimensionality of the dataset.
Now that we have established LSH is a hashing function that aims to maximize collisions for similar items, let’s formalize the definition:
A hash function h is Locality Sensitive if for given two points a, b in a high dimensional feature space, 1. Pr(h(a) == h(b)) is high if a and b are near 2. Pr(h(a) == h(b)) is low if a and b are far 3. Time complexity to identify close objects is sub-linear.

Implementation of LSH
Having learnt what LSH is, it’s time to understand how to implement it. Implementing LSH is down to understanding how to generate hash values. Some popular approaches to construct LSH are
Min-wise independent permutations
Nilsimsa Hash (Anti-Spam focused)
TLSH (For security and digital forensic applications)
Random Projection aka SimHash


Random Projection Method
Random projection is a technique for representing high-dimensional data in low-dimensional feature space (dimensionality reduction). It gained traction for its ability to approximately preserve relations (pairwise distance or cosine similarity) in low-dimensional space while being computationally less expensive.

Consider a high-dimensional data represented as a matrix D, with n observations (columns of matrix) and d features (rows of the matrix). It can be projected onto a lower dimensional space with k-dimensions, where k<<d, using a random projection matrix R. Mathematically, the lower dimensional representation P can be obtained as
(Image for post)
Columns of the random projection matrix R are called random vectors and the elements of these random vectors are drawn independently from gaussian distribution (zero mean, unit variance


Back to music identification
Going back to the problem of music identification, the general algorithm would be:
Construct a feature vector for all the songs in the database
Construct LSH Hash tables using the above defined classes with appropriate choice for number of tables and hash size.
For a newly recorded audio, construct the feature vector and query the LSH tables
Compare the feature vector of the recorded audio with the matches returned in step 3. Metrics for comparison can be L2 distance, cosine similarity or Jaccard similarity, depending on the elements of feature vector.
Return the result that has lowest/highest metric value (depending on the chosen metric) as the match
In step 4, the comparison for similar song identification is performed on a subset of data (much smaller than the entire database). This is the main reason for speedy computations. In addition, none of the hash tables are storing the high-dimensional feature vector, saving significant memory. Lastly, step 5 can be modified to make this a recommendation system.
