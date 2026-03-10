# Introduction
The goal of this project was to implement the Smith-Waterman algorithm to locally align two genomic sequences. 

# Pseudocode

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
^^^ NOTE: we actually didn't use this loop because the notebook comes with the scheme: 0 = none, 1 = diag, 2 = up, 3 = left
		  additionally, the toy sequences we need to assemble don't have the kinds of repetitive regions that necessitate a
		  branched traceback (we can use this simpler scheme we were given)

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
We were able to work well as a team and found that communication flowed pretty naturally as we worked together. Since it didn't take long for us to get on the same page in terms of both our understanding of the algorithm and how we wanted to implement it in python, we were able to hit the ground running and quickly formulate a detailed plan for implementing it. Implementing our plan went off without a hitch, and we got our algorithm to match the expected output (and make it look nicer by pretty-printing the scoring matrix as a Pandas dataframe). 

# Struggles
We had difficulties lining up our schedules for our followup scripting meeting due to one of our teammates getting sick. Two of us had to meet without him to get the scripting done, though it wound up not being much of a problem because we didn't have to deviate from the plan we'd written out when all three of us had previously met, meaning that our sick teammate who couldn't join was already deeply aware of what we were doing and how we did it. 

We were so used to the high time and space complexities of the previous projects (Gibbs Sampling and De Bruijn Graph assembly) that we spent some time on this project more concerned with optimization than we needed to be. Particularly, we did not notice at first that the method of encoding the information about a position's traceback direction had actually been given to us in the Jupyter notebook for the project. We realized that a more complex solution, while robust against edge cases, would be unnecessary for the toy sequences we'd been tasked with aligning. Thankfully, we didn't lose too much time thinking about that, and it was still an interesting optimization problem to talk about. 

Justin also encountered issues running the Jupyter notebook on his device after we finished scripting because the Linux distro included with his WSL didn't allow him to manage the packages in his environment and actually run the notebook. This was quickly resolved by upgrading the linux distro, and it was barely an inconvenience. 

# Personal Reflections

### Justin: 
I had a lovely time working with Shameem and Nicholas on this project. We had a very productive first meeting where we planned out how we wanted to write the algorithm and all got on the same page about how we wanted to implement everything in Python. While we started off with some concerns over optimizing our runtime and memory complexity, we decided that for the toy example we were tasked with running, optimizing for things like branching paths through the alignment matrix (these are common when repetitive regions occur in both sequences) was simply not necessary. With that said, though, I still enjoyed taking time to not only think about how to handle a branching path but also how to optimize it. I'm almost disappointed that the documentation and calls we'd been handed in the Jupyter notebook already laid out a scheme for how to encode information in our traceback matrix, as I really liked the idea I'd come up with to encode a position having multiple valid tracebacks without relying on an object like a list or tuple. I wanted to implement a design where our traceback matrix has a default value of 1 for all positions and a position's value is multiplied by 2, 3, or 5 (or really any set of 3 prime numbers) to indicate that the position got its score from above, to the left, or diagonally, respectively. Then during traceback, we'd just have to check which of the prime numbers the position's traceback value is divisible by to see which direction that position got its score from. We'd determined this wasn't necessary because the Jupyter notebook's coding by contract included a simpler scheme for encoding the traceback directions, and while that scheme wasn't robust against a position having multiple equally valid traceback directions, the lack of shared repetitive elements in the toy example meant we didn't have much of a need to code around such a case. After our planning meeting, I was able to meet up with Nicholas to implement everything we'd laid out in our pseudocode, where the only speedbump we'd encountered was my WSL's Linux distro needing to be upgraded before I could manage packages in my virtual environment, a problem that was completely independent of how we implemented everything. It's very fortunate that this project was easier for us than the previous couple assignments, though it doesn't give us a whole lot to reflect on here. We worked quite well together and didn't have much difficulty communicating and getting each other on the same page. 

### Shameem: 
I really enjoyed working with both Justin and Nicholas on this project. Something I’m particularly glad about was the pseudocoding and how we were able to come together and get our ideas on the same page during our first meeting. The pseudocoding in every project before has really been foundational, and it was the same with this one as well, since it made the scripting easier. I also appreciated bouncing ideas and clearing up doubts with my peers, as it helped me reinforce the concepts we were applying. Overall, this was definitely not as challenging as the previous projects, but the experience was enriching, and I'm glad with the cooperation and collaboration we had.


### Nicholas: 
This assignment felt like a nice step back from the previous couple assignments. It was a relatively straightforward solution without much need for adaptation of our original pseudocode as went. Overall, it felt good to have a successful project that went as expected without many hiccups or obstacles along the way.

# Generative AI Appendix
No generative AI was used for the planning or execution of this project. 
