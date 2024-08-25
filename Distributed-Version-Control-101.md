## Version control, basics.
In its simplest form, version control is the practice of tracking different versions of files as they're modified, with the ability to view or even revert files to older versions. 

Keep in mind that version control isn't a specific strategy for managing file versions, it says nothing about the actual implementation. You could theoretically for each version of a file, write it's binary data into a physical notebook and then format the storage medium housing the file. This is a needlessly inefficient way of implementing version control, but I just want to highlight the fact that version control can be accomplished in a lot of different ways. 
## Distributed or Centralized?
There are two main approaches when it comes to implementing version control, centralized or distributed. 
## Creating version control
How would we go about creating our own system for version control? A naive form of version control that is commonly used is simply to save each version separately. This is both easy to do and makes accessing older versions effortless. For smaller projects this strategy can work, but becomes more unfeasible as a project grows. This is because a lot of memory is being used to store data that remains unchanged between versions.  

A more sophisticated form of version control is to track changes between versions. This requires some overhead to implement but can eliminate all of the redundant data. It's easy to imagine how this could be done with a simple list, where a new version corresponds to adding a new element at the end of the list, with the element storing every change made since the previous version. If we wanted to view the nth version of a project, we would simply iterate through the list, adding up the contributions of each element until we reached the nth element.

While lists are an efficient way to implement version control, it limits the way we're able to represent versions and how they might relate to each other. It would be useful to always have a stable build of our project, while also being able to work on new features. Only once a feature has been implemented and deemed complete, should it be merged into the stable build. This is called branching and necessitates the use of a graph to manage how versions of the project might separate from, and later remerge with the main build (the graph is actually a special kind of graph called a DAG (Directed Acyclic Graph)). 

A lot of version control systems are based the same basic idea of using the structure of a DAG to allow versions to branch. I think that going through the ideas that motivates the underlying structure of systems like git makes it easier to use them an efficient way.








