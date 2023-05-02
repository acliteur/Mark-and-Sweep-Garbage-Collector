Name(s): Cass Tierney, Adrian Cliteur
Project Choice: 2.3 Mark-Sweep Garbage Collector for uScheme
Time spent on assignment: ~14 Hours collectively 
Additional Collaborators:


... description of your solution to the problem ...
The basic outline of our solution is we are creating an allocator that works with our mark procedure.
This allocator will run the allocation, unmark and sweep phases. We are also keeping track of 
memory usage and live data within the heap. This entails using nalloc, ncollections,
and nmarks. We must record this information and print the total memory usage statistics. 
The way this is done is after every 10th garbage collection and when the interpreter exits,
we print the total number of cells allocated and the current size of the heap. Finally, when the
interpreter exits, print the total number of collections and number of objects marked during those
collections.

The allocator algorithm:  
The solution derived followed the algorithm in exercise 8 of the textbook.
In allocloc, the function first increases the nalloc (number of allocations) variable.
This function uses a nested loop. The outer being the iteration through each page until the
end of the heap. The inner loop iterates through each MValue and checks if they are live (aka marked).
If they are live, we mark it as not live and keep going. otherwise, we unmark, sweep past
the object and return it. When hp reaches the heaplimit, we look at the next page. 
If we reach the end of the heap without finding an unmarked object, we should call mark(). Once we call 
mark(), we call makecurrent(pagelist) to reset hp, heaplimit, and curpage to point into first page. 
If it happens that every cell on the heap is live, we must enlarge the heap so we call a procedure named growheap.


... description of how you tested your submission ...
... ( why are you convinced that it works correctly ) ..
To compile: make
To run: cat eval.scm evaltest.scm | ./uscheme-ms -qq
Our goal was to compare the output this gave us (aka the statistics printed)
to the output given in eval_evaltest.out. We used debugging prints to determine
whether the garbage collection statistics match.


... description of your submission 's functionality ...
... ( what is working , what is not working ) ...
Though we had a good plan as for our garbage collector functionality, there is a couple flaws we came across. 
For starters, when testing the code, we get this error:

uscheme-ms: gcdebug.c:54: gc_debug_pre_allocate: Assertion `mem->alt == INVALID' failed.

In trying to fix this, we also came across a cons cycle error. This was not fun.
The main idea we have as to what is going wrong is when we return an unmarked object.
In our current implementation, it doesn't continue to sweep past the object, but instead
just returns the value and ends the allocation function. One thought we had to fix this was
to use a helper function (as recommended in the textbook) to return the unmarked value and allow
the current function to keep sweeping.

Disregarding those two errors which we failed to debug (and thus couldn't really run our program),
we know that our mark() procedure works well, visiting the roots (mark phase), printing out the
statistics, and incrementing ncollections. We modified visitloc() and allocloc() to keep track of
the statistics nmarks and nalloc respectively. 

Collecting the statistics was straightforward and documented in our code, and we have them
printed in our mark() procedure. Because of the previous errors making us unable to run
our garbage collector, we can only assume it runs and prints correctly.