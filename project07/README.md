# Introduction
Description of the project

# Pseudocode
Put pseudocode in this box:

```
BWT func
expects as input: a string

1. Append $ to the end of the string

2. Generate every cyclic rotation of the string
    3. Initialize a list of rotations, starting with just the string
    4. So we repeat this once per position (for i in range of length)
    5. Use a string slice to move the first letter to the end (string[1:] + string[0]), throw this slice into a list

--If doing suffix array, do it HERE--
6. Do a lexicographic sort of the strings in the rotations list
["abc$", "bc$a", "c$ab", "$abc"]
7. Make the compressed string by:
    8. Initialize an empty string
    9. Iterate through the sorted list
    10. Append the last character of each rotated string

11. Report the compressed string



Suffix array func
takes as input the original, uncompressed string

1. Make an empty list, call it suffix_positions

2. Go through every character in the string (enumerate)
    3. Add an entry into the suffix_positions that is a tuple where the first element is the character and the second is its index in the original string

4. Lexicographically sort the tuples in suffix_mapping by what their character is (the first element)
[("a", 0), ("b", 1), ("c", 2), ("$", 3)]
abc$ 0
bc$a 1
c$ab 2
$abc 3

abc$
so each number here doubles as both "index in the original string" AND "what row on our cyclic rotation table starts with that character"
5. Grab the indices from suffix_mapping
    6. Make an empty list
    7. Iterate through the tuples 
    8. Append the current tuple's second value (the letter's index) to a list

9. Return the list of indices



Get the BWT from suffix array func
this takes a string as input

1. Add the $ to the string

2. Get the suffix array using our helper function

3. Initialize an empty string

4. Go through every index in the suffix array
    We want to grab the last character from this row of the cyclic rotation matrix
    Luckily, this index represents the position in the orignal string where we find the letter that this row's rotation would start with (i.e. in abc$, a value of 1 from the suffix array represents b from the original string AND the 2nd row of the table, which starts with b)
    The character at the end of that row in the cyclic rotation matrix is always the one that appears right in front of it in the original string (and if it was at the first position, it wraps back around)
    5. We take the current value (an index) from the suffix array, subtract one from it, and access that position in the orignal string
    6. Append this letter to our output string

7. Report the output string



Func to make the Count index array
This is the one that counts how many characters in the string are smaller than the character in question
Takes either the burrows-wheeler transform or the original string (shouldn't matter which)

1. Remove the $ just in case we were handed the BWT of the string

2. Make a set of the characters in the string

3. Initialize a dict mapping the characters in the set to 0 (it's a counts dict, can use the one from Collections here)

4. Iterate over the characters in the string:
    5. For each key in the dict, we ask if the current character smaller (lexicographically) than the key string
        6. If yes, add 1 to the count for this key

7. Return the counts dict



Func to make the occurrence array
This is the one that counts how many times the char in question has appeared SO FAR for every combination of position in the string + character
We want to set up a dict like this: {A: [0,1,1,2,3,3,4,5, ...], ...}

Takes the BWT of the text as input

1. Making a set of the characters in the string

2. Make a dictionary mapping the characters in the set to an array of 0's, where the length of the array is the length of the BWT string, call this the occurrence array

3. Make another dict mapping characters to 0 (again use the counts dict from collections)

4. Iterate over the positions (i) + characters of the BWT string
    5. Add 1 to the current character's entry in the counter
    6. For every key in the counter dict:
        7. Update position i in this character's entry of the occurrence array

8. Return the occurrence array



Update search window (update range) function
Takes the following as inputs:
    A character
    An occurrence array for the BWT we're searching
    A counts dict for the BWT we're searching
    Lower bound
    Upper bound

1. Check if the lower bound is 0
    2. If yes, we set the new lower bound to this character's value in the counts dict (the number of characters in the bwt that are lexicographically smaller than the char) + the current lower bound (which is 0) + 1
    
    3. If no, we set the new lower bound to the sum of the following:
        this char's value in the counts dict (# of chars in the bwt that are smaller lexicographically)
        the occurence array's value for this character at the index of [lower-bound minus 1] (the number of times this character has appeared by one position before where the lower bound on our search space is)
        +1

4. Calc the new upper bound as the sum of:
    the count dict's value for this character (# of characters in the bwt smaller lexicographically)
    the occurence array's value for this character at the idex of [upper bound]

5. Return the new upper bound and new lower bound



Find match function
This function returns the upper bound index in the suffix array AND the lower bound index in the suffix array (these are positions in the index array, not the values that represent positions in the original string)
Return the list slice containing the positions of the suffix index between and including the upper and lower bound (this is a list of the positions that the query appears at in the original string)

Takes as input: 
    the query sequence "pattern"
    BWT string
    counts dict for the bwt
    occurences array for the bwt
    suffix positions for the string

1. Initialize upper and lower bounds to be the start and end of the bwt

2. Iterate over the characters in the query
    3. update the upper and lower bounds using update_range(), passing in all the params we receive in this func
    4. check if the lower bound is greater than the upper bound
        5. If yes, return an empty list (also breaks from the loop)

6. If we make it this far, there is a match and we have an upper and lower bound

7. Return a slice of the suffix index from [lower bound, to upper bound + 1]



Run length encoding func
Takes a BWT string as input

1. Initialize the following:
    A variable tracking the consecutive count of the current character, starts at 0
    The curr_character, set to first letter from input string
    the encoded output string

2. Iterate over every character in the input string
    3. Check if this character (from the for loop) is equal to the curr_character variable (From outside the for loop)
        4. If yes, increase the count by one and continue to next iteration

        5. If not, we check if the curr_character's count is above 1:
            6. If yes, append curr_character + its count to the encoded output string
            7. If no, append JUST curr_character to the encoded output string
        8. Set the current count to 1
        9. Set curr_character to this character (the current one from the for loop)

10. Return the encoded output string



run length decoding func
Takes just an encoded bwt string as input
NOTE: we're assuming no numbers in the original string (if the character is a number, it's indistinguishable from the number of times in a row it occurs)

0. Initialize an empty string (this is the decoded string we'll use as output)

1. Iterate over the positions of the encoded string from i=0 to i=length - 1 (this is to avoid index errors)
    2. Check if the current character is a number via <str>.isnumeric()
        3. If the char is a number we skip it
        4. If the char isn't a number, 
            5. Initialize an empty string (its whole purpose is to concatenate the digits of the run length)
            6. Iterate over every character that comes after it in the encoded string (slice from current position + 1)
                7. Check if the character we're looking at is a number via <str>.isnumeric()
                    8. If it is, we concatenate it to the empty string
            9. Check if the run length digits string is still empty
                10. If yes, set it to 1
                11. If no, we convert that string to an int, this is the run length
            12. We generate a run of repeats using run_length * curr_character (e.g. 3 * "A" = "AAA")
            13. Append the run of repeats to the end of the decoded string

14. Return the decoded string


```

# Successes
Description of the team's learning points

# Struggles
Description of the stumbling blocks the team experienced

# Personal Reflections
## Justin (Group Leader)
Group leader's reflection on the project

## Meghana
The Burrows-Wheeler Transform was a little confusing to me at first. Our group spent a good amount of time working on the pseudocode, making sure it was really clear and detailed before we started coding. This made the implementation much more straightforward for me. Writing the actual functions ended up being much quicker and simpler because of how detailed each step in our pseudocode was. Justin and Sneha were great to work with, we talked through the algorithm a lot which helped my understanding and I learned a lot of new ways to approach certain steps in code from them!

## Sneha
Other members' reflections on the project

# Generative AI Appendix
As per the syllabus
