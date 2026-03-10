# Introduction
The goal of this project was to implement the Smith-Waterman algorithm to locally align two genomic sequences. 

# Pseudocode
Put pseudocode in this box:

```
Cal_score function
Access the positions to the left, above, and diagonal to the given position i,j

Check if the two nucleotides at this position match
	If yes: set the match modifier/score to the ‘match’ parameter
	If no: set the match modifier/score to the mismatch penalty

we make a dict mapping direction names to the score that we’d get from that direction:
	“up”: above + gap_penalty
	“left”: left + gap_penalty
	“diag”: diag + match score
	“zero”: 0

calculate our max score as the max of those values or zero

for direction, score in that_dict:
	if the score from the current direction is equal to the max_score:
		append direction to the list of directions for this cell’s traceback

returning:
	max score- float -> write it to score matrix
	list-> directions from which this cell got its score -> write it to traceback matrix

Traceback function:
	given 2 sequences, their traceback matrix, and the coordinates of the highest-scoring position

Initialize a string tracking what our current move is
initialize two empty lists, representing nucleotides from each sequence
set values indicating the current row and column

enter a loop that breaks when our current_move is “BREAK”
	we set our current move to whatever value is in the traceback matrix at current row + current column
	we check if our current move is up, left, or diagonal
		If diagonal: 
both sequences have their current nucleotide added to their respective lists
curr_row and curr_column get decreased by 1
		If left: 
			the sequence along the top of the matrix (horizontal) has its current nucleotide added to its list
			The vertical sequence has a gap added to its list
only curr_column gets decreased by 1
		If up: 
			the vertical sequence has its current nucleotide added to its list
			The horizontal sequence has a gap added to its list
only curr_row gets decreased by 1

At the end, reverse the lists of nucleotides and package them as two separate strings

Smith_waterman function: 
	aka our driver function
Create graph shell based on len(reference) + 1 and len(query sequence) + 1
	Default value = 0

Make a duplicate where the default value is empty lists

Initialize a tuple for the coordinates of the max score position
Initialize the max score: 0

Calculate scores by index (i, j)  i = row, j = col
	Nested iteration over all pairwise comparisons to calculate matrix scores
		Call cal_score on current position in the matrix → gives us a score and a list of traceback directions
		Update the score at i,j in the score matrix
		Update the traceback direction for position i,j in the traceback matrix
		Check if the score at i,j is greater than the running max score, update the max score and the position with the max score if it is

Now both matrices are set up

We call the traceback function, feeding it the traceback matrix and the position for the max score
	Gives us the two sequences

After that, we just spit out the aligned sequences and the scoring matrix

```

# Successes
Description of the team's learning points

# Struggles
Description of the stumbling blocks the team experienced

# Personal Reflections
## Group Leader- Justin
Group leader's reflection on the project

## Other members
Other members' reflections on the project

### Shameem: 


### Nicholas: 
This assignment felt like a nice step back from the previous couple assignments. It was a relatively straightforward solution without much need for adaptation of our original pseudocode as went. Overall, it felt good to have a successful project that went as expected without many hiccups or obstacles along the way.

# Generative AI Appendix
As per the syllabus
