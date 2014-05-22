# Considering Threadability

## Avoid Global Data

This includes statics and singletons

Global data leads to unintended side effects between functions and can make code difficult or impossible to parallelize. Even if the code is not intended today for parallelization, there is no reason to make it impossible for the future.

## Avoid Heap Operations

Much slower in threaded environments. In many or maybe even most cases, copying data is faster. Plus with move operations and such and things
