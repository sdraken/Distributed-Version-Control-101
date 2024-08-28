## Version Control, The Basic Idea.
In its simplest form, version control is the practice of tracking different versions of a project as it's modified, with the ability to view or even revert the project to older versions. 

Keep in mind that version control isn't a specific strategy for managing versions of a project, it says nothing about the actual implementation. You could theoretically for each version of a project, write it's binary data into a physical notebook and then format the storage medium housing the project. This is a needlessly inefficient way of implementing version control, but I just want to highlight the fact that version control is more of an idea and can be achieved in a lot of different ways (including stupid ones).
## Creating A Version Control System
The purpose of this section is to gain an understanding of the basic implementation behind most version control systems, and why this particular implementation was chosen. To demonstrate this, I'll be showing how the basic idea of tools like Git follow from very simple version control. We'll start with a naive approach and see how the implementation of today's version control systems naturally arise when we add desired features. 
#### Save Versions Separately
How would we go about creating our own system for version control? A naive form of version control is to simply save each version separately. This is both easy to do and makes accessing older versions effortless. For smaller projects this strategy can work, but becomes more unfeasible as a project grows. This is because a lot of memory is being used to store data that remains unchanged between versions.  
#### Linear Version Control
A more sophisticated form of version control is to track changes between versions. This requires some overhead to implement but can eliminate most (if not all) of the redundant data. It's easy to imagine how this could be done with a singly linked list, where a new version corresponds to adding a new element at the end of the list, with the element containing every change made since the previous version. If we wanted to view the nth version of a project, we would simply iterate through the list, adding up the contributions of each element until we reached the nth element.
$$
\begin{align*}
\fbox{A} \rightarrow \fbox{B} \rightarrow\fbox{C} \rightarrow
\end{align*}\dots$$We could prepend new versions to the list instead to achieve a similar effect, but this is going to be an important distinction when working with graphs. 
#### Version Control And The DAG
While lists are an efficient way to implement version control, it limits the way we're able to represent versions in how they relate to each other. 

As an example, It would be useful to always have a stable build of our project, while also being able to work on new features. Development of the new feature should be in a separated environment and only merged into the stable build once completed. This is the basic idea of branching and it necessitates the use of a directed graph to manage how versions of the project might separate from, and later remerge with the main build. Note that we can't permit graphs to contain cycles for this implementation to work. A cycle would cause an infinite loop, since nodes depend on previous nodes in a recursive pattern. Graphs with these restrictions (directed and without cycles) are a special type of graph called a DAG (Directed Acyclic Graph).  

The direction we're adding new elements doesn't really matter when we're using a linear structure such as a list. Now that we're dealing with a non-linear structure, you might notice that there is a big difference between:
1. letting a version be pointed to by the versions it depends on
$$
\begin{align*}
&\,\,\,\,\,\,\,\,\,\,\fbox{E}\rightarrow\fbox{F}\\
&\nearrow\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\searrow

\\
\fbox{A} \rightarrow \fbox{B}& \rightarrow\fbox{C} \rightarrow \fbox{D}\rightarrow \fbox{K}\rightarrow\fbox{L}
\\
&\searrow\\
&\,\,\,\,\,\,\,\,\,\,\fbox{G}\rightarrow\fbox{H}\rightarrow\fbox{I}\rightarrow\fbox{J}
\end{align*}$$
2. Having the new version itself point towards the versions it depends on.
$$
\begin{align*}
&\,\,\,\,\,\,\,\,\,\fbox{E}\leftarrow\fbox{F}\\
&\swarrow\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\nwarrow

\\
\fbox{A} \leftarrow \fbox{B}& \leftarrow\fbox{C} \leftarrow \fbox{D}\leftarrow \fbox{K}\leftarrow\fbox{L}\\
&\nwarrow\\
&\,\,\,\,\,\,\,\,\,\,\fbox{G}\leftarrow\fbox{H}\leftarrow\fbox{I}\leftarrow\fbox{J}
\end{align*}$$
With (1), if we wanted to view $J$ we'd have to start at $A$ and check every possible path to make sure that we find all paths leading to $J$. there is no simple way of figuring out what $J$ depends on. If we instead used (2), then we're already given all paths between $J$ and $A$ . (2) is clearly the better option, which is why git and I assume most other version control systems use it.

Most version control systems are based on the basic idea of using the structure of a DAG. It can be a lot easier to understand how these tools work if we keep this underlying structure in mind. 
## Centralized Or Distributed
Git is a distributed version control system. We've gotten a decent enough grasp on what a version control system is, but what does it mean for it to be distributed?

Until now we've only really discussed how a version control system works as a single local copy. If multiple people are suppose to work on a project, or even if one person wants to work on a project from different devices, how are we suppose to handle that?  

One solution is through a centralized approach. The version control system of a project, with all it's different branches, merges, etc, are stored on a server. The server is the only place that reflects the current state of the project. Users can download parts of the project to their local device and start modifying it, but the changes need to actually be pushed onto the server.

In contrast, a distributed system has no central version of the project. There is usually some remote location for users to download the project but it's not considered a main version like in a centralized system. Each user has a complete copy of the project with all the different branches, merges, etc, and is allowed to add their own changes without having to push them to a server. This approach allows users to work more freely without having to rely on a server, but it's less intuitive compared to a centralized system which could lead to problems.

A lot more could be said about centralized and distributed version control systems, but this gives enough of the basic idea.

## Git specifics

My standard will be using the feature branch workflow as an outline so I'll be covering pretty few git specific commands.
#### init and clone
#### checkout
#### status
#### add
#### commit
#### push 

#### fetch
#### pull
#### reset
