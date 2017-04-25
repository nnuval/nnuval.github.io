---
layout: post
title: Parallel Programming/ Forest Fire Simulation
author: Nate Nuval
---
One of my favorite assignments in the Parallel Programming course I took was the Forest Fire Simulation. The goal of the project was to create a program that simulates a forest fire. In order to make this program run more efficiently, we used MPI (message passing interface) to use multiple processes simultaneously. This made the program run quicker than a non-parallel program.
This post is going to address some of the basic functionality of the forest fire simulation as well as how MPI was used. 

Here is a link to the full program:
<a href="https://github.com/nnuval/425cpsc/blob/master/forestFire.c">forestFire.c</a>

To compile a C program that uses MPI, use this command:

{% highlight ruby %}
$ mpicc forestFire.c
{% endhighlight %}

To run a program using MPI, use this command:

{% highlight ruby %}
$ mpirun -np 8 a.out random.txt 1000 0.001 0.001
{% endhighlight %}

The “8” passed is the number of processes that we want to use in the program.
“a.out” is the compiled version of forestFire.c (the program we want to run).
The forest fire simulation we created takes four other arguments:
“random.txt” is the “forest” that this program uses. It is simply text files consisting of 40 rows and 80 columns. 

![randomForest](/assets/randomForest.png)

In the file a “T” represents a tree and an “X” represents a burning tree.

“1000” is the number of generations we want to run the simulation. 

The first “0.001” is the probability of ignition. For every tree in the file, a random number between 0 and 1 is generated. If this number is less than or equal to the probability of ignition, than that tree will become a burning tree in the next generation.

The final argument passed is the probability of growth. Similar to the probability of ignition, a random number between 0 and 1 is generated. Except this time for every whitespace. If the random number falls below or is equal to the probability of growth, then that empty space will be a tree in the next generation.
Some other rules of the simulation are any tree that is next to a burning tree, will become a burning tree in the next generation. Every burning tree will become an empty space in the next generation.

The way MPI is used in this program starts here in main:

{% highlight ruby %}
int main(int argc, char** argv){
int rank, size;

	/* initialize MPI */
	MPI_Init(&argc, &argv);

	/*get the rank (process id) and size (number of processes) */
	MPI_Comm_rank(MPI_COMM_WORLD, &rank);
	MPI_Comm_size(MPI_COMM_WORLD, &size);
{% endhighlight %}

The rank is now stored in the “rank variable” and the size is now stored in the “size” variable.
When you initialize MPI, each processes gets it’s own copy of the same code. Multithreaded programs run using the same code and are able to pass information into global variables to each other. Because multiprocessor programs do not have global variable they share, the trick is to send the information between them in messages, hence the name Message Passing Interface.

To divide the work as evenly as possible we use the size and rank to partition the sections of the forest each process will be examining.

{% highlight ruby%}
/* calculate the start and end points by evenly dividing the range */
	int start = (3240/ size) * rank;
	int end = start + (3240/size) - 1;
	
	/* The last process needs to do all the remaining ones */
	if (rank == (size - 1)){
		end = 3239;
	}
{% endhighlight%}

We then run the simulation:

{% highlight ruby %}
/* runs the simulation based on the number of generations */
		for(curGen = 0; curGen < N; curGen++){	
{% endhighlight %}
This line of code essentially loops through the file, making all of the necessary changes, until it reaches the number of generations the user has passed in.

Each process takes care of it’s own section:

{% highlight ruby %}
int row = 0; // row index
		int col = 0; // column index

		/* runs through each cell in the array */
		for(i = start; i <= end; i++){
			
			/* if the cell is a newline char */
			if(isNewline(forest[i])){
				row++; // increment the row index
				col = 0; // set column index to 0
				nextGen[i] = forest[i]; // copy the char to the nextGen array
				continue; // go back to top of the for loop
			}

			/* use testTree() to determine what the cell will be in the next
			 * generation */
			nextGen[i] = testTree(i,row,col,forest,I,G,rank,size);
			col++;
		}
{% endhighlight %}

Since each the result of a tree depends on it’s neighbors we need to pass the neighboring rows between all of the processes.
{% highlight ruby %}
/* Pass rows to neighboring processes */
		temp = 0;
		for(k = 0; k < (size-1); k++){
{% endhighlight %}
This loop runs 1 less than the number of processes.

Here is where the MPI magic happens
{% highlight ruby %}
			if(rank == temp){
				
				/* Store the proc's last row into the partial array */
				for(i = 0; i <= 80; i++){
					partial[i] = nextGen[(end-80) + i];
				}

				/* Send the last row to the neighbor below */
				MPI_Send(partial, 81, MPI_CHAR, (temp+1), 0, MPI_COMM_WORLD);
				
				/* Receive the row from the neighbor below */
				MPI_Recv(partial, 81, MPI_CHAR, (temp+1), 0, MPI_COMM_WORLD,
						&status);

				/* Stores the received row into this proc's nextGen 
				 * array */
				for(i = 0; i <= 80; i++){
					nextGen[(end+1)+i]=partial[i]; 
				}	
			}else if(rank == (temp+1)){			
				/* Receive the row from the neighbor above */
				MPI_Recv(partial, 81, MPI_CHAR, temp, 0, MPI_COMM_WORLD, 
						&status);
				
				for(i = 0; i <= 161; i++){
					if(i >= 81){
						/* Stores this proc's first row into the partial 
						 * array */
						partial[i-81] = nextGen[start+(i-81)];
					}else{
						/* Stores the received row into this proc's nextGen
						 * array */	
						nextGen[(start-81) + i] = partial[i];
					}
				}
				
				/* Sends the newly refilled partial array to the neighbor 
				 * above */	
				MPI_Send(partial, 81, MPI_CHAR, temp, 0, MPI_COMM_WORLD);
			}
			temp++; //Increment to move onto the next processes
		}
{% endhighlight %}

Each process starts the “temp” variable at 0 and then increments it at the end of the for loop. 

{% highlight ruby %}
			if(rank == temp){
{% endhighlight %}

At the first run, we are asking all of the processes if they are process #0.

If they are not we check if they are the next process (process #1 in this case).

{% highlight ruby %}
			}else if(rank == (temp+1)){	
{% endhighlight %}

Here is where the message passing begins. If you are process #0 then you run:

{% highlight ruby%}
/* Send the last row to the neighbor below */
				MPI_Send(partial, 81, MPI_CHAR, (temp+1), 0, MPI_COMM_WORLD);
				
				/* Receive the row from the neighbor below */
				MPI_Recv(partial, 81, MPI_CHAR, (temp+1), 0, MPI_COMM_WORLD,
						&status);
{% endhighlight %}

If you are the next process, in this case process #1 then you run:

{% highlight ruby %}
/* Receive the row from the neighbor above */
				MPI_Recv(partial, 81, MPI_CHAR, temp, 0, MPI_COMM_WORLD, 
						&status);
				…
				/* Sends the newly refilled partial array to the neighbor 
				 * above */	
				MPI_Send(partial, 81, MPI_CHAR, temp, 0, MPI_COMM_WORLD);

{% endhighlight %}

MPI_Send() and MPI_Recv are both blocking calls. This means that once the process calls one of those methods it will wait until it is completed. The order that the two processes call these methods are VERY important. MPI_Send() will only return when the process it is sending to calls MPI_Recv (and vice versa).

This loop runs until all the processes pass their neighboring rows. After that we start back at the top to run another generation. 
There was a high level explanation of the project. I would definitely love to go back and use this simulation as my guideline if I am every writing multiprocessor programs.
