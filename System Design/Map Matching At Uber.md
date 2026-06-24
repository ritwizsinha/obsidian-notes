[[Uber]]
[[System Design]]
[[Company Blog or Video Analysis]]


#### Representation
Intersections represented as nodes, roads represeented as graphs. Some other details like n way roads or some streets ending with no intersection not talked about here

#### Types of distances
- Havenshtein Distance: Shortest path across the curvature of earth.
- Shortest Path Distance: Simple distance across two nodes, following the topology of the graph.
### Map Matching
Taking in raw GPS signals and matching it with the langitude latitutude present on the street.
Input: Raw GPS Signals(Latitude, Longitude, Speed, Course)
Output: Positions on a Road network(Lat, Long,  Road Segment Id, Road Name, Direction, Heading)


#### Online Map Matching
1. Driver Position for online map matching
2. Offline Map Matching for fare calculation


#### Markov Chain
Satisfies the Markov property ie the probability of the current state depend only on the current state.


#### Hidden Markov Model
![[Pasted image 20250330000915.png]]

We have hidden states in a markov chain and we cannot see the states directly, instead we can just make observation states on a hidden states having some emission probability

For map matching we use this Hidden Markov model
![[Pasted image 20250330001235.png]]

Where we predict the probable road segment given some observation states O1, O2, O3, O4, O5
For every GPS observation we have we find which road segment could produce the signal. We do a k nearest neighbour lookup using geospatial index lookup for various road segments for a particular gps signal. Find a projection of the signal on the road segment. This leads to candidate selection. 

![[Pasted image 20250330001639.png]]We then calculate the emission probabilities. 
After that Viterbi Algorithm gives us the most possible road segments which can be shown.