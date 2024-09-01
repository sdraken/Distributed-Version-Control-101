## Version Control, The Basic Idea.
In its simplest form, version control is the practice of tracking different versions of a project as it's modified, with the ability to view or even revert the project to older versions. 

Keep in mind that version control isn't a specific strategy for managing versions of a project, it says nothing about the actual implementation. You could theoretically for each version of a project, write it's binary data into a physical notebook and then format the storage medium housing the project. This is a needlessly inefficient way of implementing version control, but I just want to highlight the fact that version control is more of an idea and can be achieved in a lot of different ways (including stupid ones).
## Creating A Version Control System
The purpose of this section is to gain an understanding of the basic implementation behind most version control systems, and why this particular implementation was chosen. To demonstrate this, I'll be showing how the basic idea of tools like Git follow from very simple version control. We'll start with a naive approach and see how the implementation of today's version control systems naturally arise when we add desired features. 
#### Save Versions Separately
How would we go about creating our own system for version control? A very basic form of version control is to simply save each version separately. This is both easy to do and makes accessing older versions effortless. For smaller projects this strategy can work, but becomes more unfeasible as a project grows. This is because a lot of memory is being used to store data that remains unchanged between versions.  
#### Linear Version Control
A more sophisticated form of version control is to track changes between versions. This requires some overhead to implement but can eliminate most (if not all) of the redundant data. It's easy to imagine how this could be done with a singly linked list, where a new version corresponds to adding a new element at the end of the list, with the element containing every change made since the previous version. If we wanted to view the nth version of a project, we would simply iterate through the list, adding up the contributions of each entry until we reached the nth element.
```math
\begin{align*}
\fbox{A} \rightarrow \fbox{B} \rightarrow\fbox{C} \rightarrow
\end{align*}\dots
```
We could prepend new versions to the list instead to achieve a similar effect, but this is going to be an important distinction when working with graphs. 
#### Version Control And The DAG
While lists are an efficient way to implement version control, it limits the way we're able to represent versions in how they relate to each other. 

As an example, It would be useful to always have a stable build of our project, while also being able to work on new features. Development of the new feature should be in a separated environment and only added to the stable build once completed. This is the basic idea of branching and it necessitates the use of a directed graph to manage how versions of the project might separate from, and later remerge with the main build. Note that we can't permit graphs to contain cycles for this implementation to work. A cycle would cause an infinite loop, since nodes depend on previous nodes in a recursive pattern. Graphs with these restrictions (directed and without cycles) are a special type of graph called a DAG (Directed Acyclic Graph).  

The direction we're adding new elements doesn't really matter when we're using a linear structure such as a list. Now that we're dealing with a non-linear structure, you might notice that there is a big difference between:
1. letting a version be pointed to by the versions it depends on
```math
\begin{align*}
&\,\,\,\,\,\,\,\,\,\,\fbox{E}\rightarrow\fbox{F}\\
&\nearrow\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\searrow
\\
\fbox{A} \rightarrow \fbox{B}& \rightarrow\fbox{C} \rightarrow \fbox{D}\rightarrow \fbox{K}\rightarrow\fbox{L}
\\
&\searrow\\
&\,\,\,\,\,\,\,\,\,\,\fbox{G}\rightarrow\fbox{H}\rightarrow\fbox{I}\rightarrow\fbox{J}
\end{align*}
```
2. Having the new version itself point towards the versions it depends on.
```math
\begin{align*}
&\,\,\,\,\,\,\,\,\,\fbox{E}\leftarrow\fbox{F}\\
&\swarrow\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\nwarrow
\\
\fbox{A} \leftarrow \fbox{B}& \leftarrow\fbox{C} \leftarrow \fbox{D}\leftarrow \fbox{K}\leftarrow\fbox{L}\\
&\nwarrow\\
&\,\,\,\,\,\,\,\,\,\,\fbox{G}\leftarrow\fbox{H}\leftarrow\fbox{I}\leftarrow\fbox{J}
\end{align*}
```
With (1), if we wanted to view $J$ we'd have to start at $A$ and check every possible path to make sure that we found all paths leading to $J$. there is no simple way of figuring out what $J$ depends on. If we instead used (2), then we're already given all paths between $J$ and $A$ . (2) is clearly the better option, which is why git and I assume most other version control systems use it.

Most version control systems are based on the basic idea of using the structure of a DAG. It can be a lot easier to understand how these tools work if we keep this underlying structure in mind. 
## Centralized Or Distributed
Git is a distributed version control system. We've gotten a decent enough grasp on what a version control system is, but what does it mean for it to be distributed?

Until now we've only really discussed how a version control system works as a single local copy. If multiple people are suppose to work on a project, or even if one person wants to work on a project from different devices, how are we suppose to do that?  

One solution is through a centralized approach. The version controlled project, with all it's different branches, merges, etc, are stored on a server. The server can be seen as the true location of our project. Users can then download parts of the project to their local device and start modifying it on their own, but the changes need to actually be pushed onto the server.

In contrast, a distributed system has no central version of the project. There is usually some remote location for users to download the project but it's not considered a main version like in a centralized system. Each user has a complete copy of the project with all the different branches, merges, etc, and is allowed to add their own changes without having to push them to a server. This approach allows users to work more freely without having to rely on a server, but it can be less intuitive compared to a centralized system which could lead to problems.

A lot more could be said about centralized and distributed version control systems, but this gives enough of a basic idea.
## Git Specifics
Git provides tools with a lot of freedom in how they are used. For example, Git does not prevent someone from working on a branch $B1$ but committing the changes to a totally unrelated branch $B2$. When discussing Git specifics I will be doing it while keeping in mind how its tools are usually used.
#### The Repository (.git) and Working Directory
The `.git` folder (also called the **repository**) is what gives Git the ability to track all the changes inside some directory $D$. We can imagine that `.git` stores the DAG discussed previously, with every node, branch, merge, etc, making up the different versions of $D$ that Git has been told to save. $D$ is called the **working directory**. Its content at any given time can be changed into any of the saved revisions of the project. 
#### Staging and Committing
If we change files within the working directory, we're then able to specify a subset of those changes to save into a new revision. In git, specifying what changes to include is called **staging**, the staged files are then **committed** to the repository. Staged and committed changes could be thought of in terms of the DAG. Every commit corresponds to creating a new node, recording all the staged changes. Lets say that we're working on commit $X$, what happens when we stage and commit our changes? 

If $X$ is the newest commit on a branch then our new commit $Y$ will simply become the next node on the same branch
```math
\begin{align*}

&\,\,\,\,\,\,\,\,\,\,\fbox{G}\leftarrow\fbox{H}\leftarrow\fbox{X}\leftarrow\fbox{Y}\\
&\swarrow\\
\fbox{A} \leftarrow \fbox{B}& \leftarrow\fbox{C} \leftarrow \fbox{D}\leftarrow \fbox{E}\\

\end{align*}
```
otherwise it creates a new branch for our new commit $Y$

```math
\begin{align*}
&\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\fbox{Y}\\
&\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\swarrow\\
&\,\,\,\,\,\,\,\,\,\,\fbox{G}\leftarrow\fbox{X}\leftarrow\fbox{H}\\
&\swarrow\\
\fbox{A} \leftarrow \fbox{B}& \leftarrow\fbox{C} \leftarrow \fbox{D}\leftarrow \fbox{E}\\

\end{align*}
```

we can also explicitly tell Git if we want to create a new branch
```math
\begin{align*}
&\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\fbox{Y}\\
&\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\swarrow\\
&\,\,\,\,\,\,\,\,\,\,\fbox{G}\leftarrow\fbox{H}\leftarrow\fbox{X}\\
&\swarrow\\
\fbox{A} \leftarrow \fbox{B}& \leftarrow\fbox{C} \leftarrow \fbox{D}\leftarrow \fbox{E}\\

\end{align*}
```

or, if we're merging branches then our new commit $Y$ will be connected to every branch involved in the merge. 
```math
\begin{align*}


&\,\,\,\,\,\,\,\,\,\,\fbox{G}\leftarrow\fbox{H}\leftarrow\fbox{X}\\
&\swarrow\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\nwarrow\\
\fbox{A} \leftarrow \fbox{B}& \leftarrow\fbox{C} \leftarrow \fbox{D}\leftarrow \fbox{E}\leftarrow \fbox{Y}\\

\end{align*}
```
#### The Remote
By having a remote copy of the `.git` folder on a service like GitHub, we can collaborate with others. Whenever a user downloads the repository they get a complete `.git` folder. They can then make their own changes (creating new nodes, branching, merging, etc) and push them onto the remote repository. Allowing users to make and push independent changes to a remote has some unique challenges. For example, if two users make local changes and push those onto a remote branch without restrictions, then the first push would essentially be erased by the second. This is why (by default) the branch on a local repository needs to be up to date with the remote, to allow for a push. There are many other complications with remote repositories and collaborative work, but that falls outside the scope of this text.
## Git Commands
My standard will be using the feature branch workflow as an outline, so I'll only be going over git commands related to that workflow. 
#### init and clone
`git init` is used to (as the name suggests) initialize a git repository by creating a `.git` folder. If the repository already exists at some remote location (like GitHub), we can instead use `git clone` to copy the remote into a local repository. 
#### log
`git log` is not a strictly necessary command but provides some utility. it can be used to summarize all the commits of the entire repo, or just specific parts (like commits from some particular branches). It's a useful tool to get a general overview of the repository.
#### branch
`git branch` has a lot of different uses, all relating to branches. Our main use is going to be explicitly creating new branches when we want to start working on a new feature. The command can also list, rename and delete branches.
#### switch
`git switch` is used to change the working directory between branches. The command is fairly new, being added in Git 2.23. Prior to that you used `git checkout`, a command that can be used to switch between branches and to restore files to previous versions. `git checkout` was split into `git switch` and `git restore` to clarify its two distinct use cases.
##### add
`git add` is used to stage files. The command can stage entire folder/files or specific parts of a file. This is particularly useful when a file contains changes that don't relate to each other, as we're able to split them into multiple commits.
#### commit
`git commit` commits our staged changes into the current branch.
#### merge
`git merge` can be looked at as a special kind of commit. It incorporates the changes made from multiple commits into the current branch.
#### status
`git status` shows the status of the working directory. It shows which files are tracked and what changes are staged, as well as the status of the current branch compared to any remotes.
#### push 
`git push` is how you transfer the local changes to the remote. I've mentioned that there are many intricacies and potential conflicts with remote repositories and collaborative work, but again, that falls outside the scope of this text.
#### fetch
`git fetch` checks a remote for any updates and downloads them into the local repository. If the remote is ahead our local repository in commits, we'll need to incorporate the new remote commits into our local repo. This is often done with git pull `git pull`, a command that combines fetch and merge.

## References

- E. Raymond. "Understanding Version-Control Systems". catb.org. Accessed: Sep. 1, 2024. Available: http://www.catb.org/~esr/writings/version-control/version-control.html
- F. Francis. "Key Insights on How Git Version Control System Works". evoketechnologies.com. Accessed: Sep. 1, 2024. Available: https://www.evoketechnologies.com/blog/insights-git-version-control-system/
- M. Hajirajabi. "A brief history of Version Control Systems". medium.com. Accessed: Sep. 1, 2024. Available: https://medium.com/@mehran.hrajabi98/a-brief-history-of-version-control-systems-vcss-5881f07ba0e1
- S. Chacon and B. Straub *Pro Git*, 2th ed. New York, NY, USA: Apress, 2014.
