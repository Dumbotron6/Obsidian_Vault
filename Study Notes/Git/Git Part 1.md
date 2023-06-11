# What is Git?

Git is a version control system. A version control system records the changes made to the code over time in a database called repository. A history of what changes are made, by whom and why are recorded.

We can revert our project back to an earlier state if something goes wrong. Without version control, the entire project has to be stored separately every time a new change is made. It also makes it easier for multiple people to work together on a project.

VCS falls into two categories
1. Centralized
2. Distributed

In a centralized system, all team members connect to a single server to get a copy of the code and to share changes. Disadvantage is, it has a single point of failure i.e if the server goes down, we cannot save snapshots of our project until the server is back online. Example is Teams Foundation server.
In a distributed system, every team member has a copy of the project with its history on their local machine. So if the server is down, we can synchronize our work directly with others.
![[Attachments/1.png]]

Git is a distributed VCS. It's free and opensource and scalable.

Git can be used via the command line. Most modern code editors and IDEs also have Git integrated. There are also Graphical User Interfaces (GUIs) specifically for Git.

# Terms and Git Commands
![[Attachments/2.png]]
![[Attachments/3.png]]

# Configuring Git

For Git to be used, the following have to be configured-
Name, Email, Default editor and Line ending handling.

They have various levels
* System - All users in the computer
* Global - All repositories of the current user
* Local - The current repository
So different settings can be set for different projects.

`git config --global user.name "Prasana C"` sets the user name. Similarly, user.email can be set.
`git config --global core.editor "'C:\Program Files\Notepad++\notepad++.exe' -multiInst -notabbar -nosession -noPlugin"` will set the default editor to Notepad++.

Everything after the path are switches. The -nosession switch tells Notepad++ not to bring up any files from previous sessions, ensuring that the current commit is the only thing the text editor displays. The -noPlugin ensures no plugins of Notepad++ are loaded. -multiInst is so that Notepad++ can run multiple instances. -notabbar launches Notepad++ without tabbar. These are optional.

The next time you issue a git commit without specifying a commit message, Notepad++ will pop up. Just add a message, click save and close the window. The message you typed in will then become part of the history of the git branch upon which you are working.

`git config --global -e` will open the editor (Notepad++) with our configured global settings so that we can edit them. It is stored in a file named .gitconfig.

![[Attachments/4.png]]

In Windows, end of lines are marked with \\r\\n (carriage return and line feed). In macOs and Linux, it is marked with \\n.

Say two people are using the same repository, but one uses Windows and the other Linux, when the windows user checks in, the CR needs to be removed and when the code is checked out, CR needs to be added.

End of lines need to be configured properly in git which can be done using the property `core.autocrlf true`. The windows user needs to set it to true and the Linux user to input. Input ensures that if CR is accidentally added by the user, it is removed when checking in to the repo.

The `cutocrlf` and `editor` can be configured while installing git but to change them, we can use these commands.

Typing `git config --help` will display all the git commands and their purpose. Pressing space will display the next page on the terminal and pressing q will exit the help page. We can also use `git config --h` which will give a short summary.

## Initializing a repository

To initialize a repository, we have to navigate to the project folder and enter the  `git init` command. Doing so will create a hidden *.git* folder inside our directory. Deleting the folder will delete the repository but not the content in the directory. All our git progress will be lost.

`git init -b main` will allow us to name our branch where 'main' is the branch name.

![[Attachments/5.png]]

# Git workflow

A project directory created in local will have a git repository. The git repository will be a hidden folder call *.git* in our project directory.

Typically, we modify one or more files and when they reach a state we want to records, we *commit* the changes we made at that point to the repo. A ==*commit*== is like taking a snapshot of our project at that point in time.

In git, we have an intermediate step called ==*Staging area or Index*.== It's essentially what we are proposing for the next commit. It is a place for us to review the changes we made to the files. If we are satisfied, then we can commit. If we don't want a file to be part of the commit, we can remove from the staging area (aka unstage them) and commit the remaining files.

To add a file to the stating area, we use `git add file1` command. To add multiple files, we use `git add file1 file2` with spaces between the file names. To commit the changes, `git commit -m "commit message"` should be used. If we make multiple commits, each one is a snapshot. The commit message should tell us why and what we are committing.

As we fix bugs, implement new features or refactor our code, we make commits and each commit represents the state of the project at that point in time so we have a recorded history of the changes we made.

Once we commit changes, the staging area does not become empty. It keeps the file in the staging area and does not automatically record new changes. Even if we make changes to file1, the one in the staging area will still represent the old version. To update it, we need to add it to the staging area again using the `git add file1` command. Then we have to commit again. Even if say file2 is deleted, we still need to add it to the staging area so that the next commit we make will know that it is supposed to be deleted in the repo.

Each commit we make has a ID which is a unique identifier. It also has the commit message and the date and time of when the commit was made. It also has the author (i.e who made the change) information. Git also stores the full content each time, not just the changes made so that an earlier snapshot can be restored if necessary without having to compute the changes. Doing so does not waste space as git compresses the content and doesn't store duplicate content.

Continued in [[Git Part 2]]

