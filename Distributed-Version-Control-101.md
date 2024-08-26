## Version control, the basic idea.
In its simplest form, version control is the practice of tracking different versions of files as they're modified, with the ability to view or even revert files to older versions. 

Keep in mind that version control isn't a specific strategy for managing file versions, it says nothing about the actual implementation. You could theoretically for each version of a file, write it's binary data into a physical notebook and then format the storage medium housing the file. This is a needlessly inefficient way of implementing version control, but I just want to highlight the fact that version control can be accomplished in a lot of different ways. 
## Creating a version control system
The purpose of this section is to gain an understanding of the basic implementation behind most version control systems, and why this particular implementation was chosen. To demonstrate this, I'll be showing how the basic idea of tools like Git naturally follow from very simple version control. We'll start with a naive approach and see how the implementation of today's version control systems naturally arise when we add desired features. 

So, how would we go about creating our own system for version control? A naive form of version control that is commonly used is to simply save each version separately. This is both easy to do and makes accessing older versions effortless. For smaller projects this strategy can work, but becomes more unfeasible as a project grows. This is because a lot of memory is being used to store data that remains unchanged between versions.  

A more sophisticated form of version control is to track changes between versions. This requires some overhead to implement but can eliminate most (if not all) of the redundant data. It's easy to imagine how this could be done with a singly linked list, where a new version corresponds to adding a new element at the end of the list, with the element containing every change made since the previous version. If we wanted to view the nth version of a project, we would simply iterate through the list, adding up the contributions of each element until we reached the nth element. We achieve a similar effect if we prepended new versions to the list instead, but is going to be an important distinction when working with graphs. 

While lists are an efficient way to implement version control, it limits the way we're able to represent versions and how they might relate to each other. It would be useful to always have a stable build of our project, while also being able to work on new features. Only once a feature has been implemented and deemed complete, should it be merged into the stable build. This is called branching and necessitates the use of a directed graph to manage how versions of the project might separate from, and later remerge with the main build. Note that we also can't permit graphs to contain cycles for this implementation to work. A cycle would cause an infinite loop, since nodes depend on previous nodes in a recursive pattern. Graphs with these restrictions (directed and without cycles) are a special type of graph called a DAG (Directed Acyclic Graph). I hinted at this earlier, that there is a big difference between:
1. letting a new version of a branch be pointed to by the versions it depends on
2. Having the new version itself point towards the versions it depends on.

With (1), if we wanted to view the most recent version of a branch, we'd have to start at the the first version and check until we found every path leading to the tip of our desired branch. If we instead used (2), we just need to follow and add up the paths starting from the tip of the branch and leading to the first version. (2) is clearly the better option, which is why git and I assume most other version control systems use it.

Most version control systems are based on the basic idea of using the structure of a DAG, and keeping this in mind can make it easier to understand how these tools work.

## Distributed?
So, Git is a distributed version control system. We've gotten a decent enough grasp on what a version control system is, but what does it mean for it to be distributed? 
## Git specifics


