
# Staging and unstaging files

When new files are added to the workflow, they are not automatically *tracked* by git. We have to add them to the staging area so that they are tracked. To know which files are tracked, we can use `git status` command.

![[Attachments/6.png]]

We can then track them using the `add` command. To add a file to the stating area, we use `git add file1` command. To add multiple files, we use `git add file1 file2` with spaces between the file names.  `git add .` will add the entire directory recursively.

We can unstage files from the index using `git reset HEAD -- file1` command. It can also be done using  `git rm -r --cached file1`. The -r means recursively so any file present in the folder (if specified instead of file1) will be removed. The --cached means it will be removed from the index only.

![[Attachments/7.png|600]]

![[Attachments/8.png]]

It can also be done using `git restore --staged file1` command. It does this by restoring the file in the staging area with a copy from the last commit. So the staging area has the last commit's file but the working directory's changes are not affected. We can stage them again. This is recommended for unstaging files.

Simply entering `git restore file1` will replace the file even in the working directory so all the changes we made will be lost. We have to be careful with this.

-f will force git to remove even untracked files and -d will remove folders (directories). So `git clean -fd` will restore everything to the latest commit and delete the untracked files.

`git restore --source=HEAD~1 file1` will take the file from a specified commit. This is useful if we accidentally delete a file and commit it and then want to recover it.

# Committing changes

To commit staged changes, we can use `git commit -m "commit message"` command. -m stands for message. Sometimes, we might need to add additional details to the commit we are making. To do that, simply typing `git commit` will open the editor that we configured. There, we can type the commit message followed by the longer description. 

![[Attachments/9.png]]

Saving and closing the editor will commit the changes and show us a summary.

![[Attachments/10.png]]

It is not advisable to commit for every small change but it is recommended to commit often whenever a complete change is done. Each commit should have separate purposes. For example, one commit should be for bug fixes and a separate one for fixing typos. Each bug fix should have a separate commit.

The staging area can be skipped using `git commit -a -m "commit message"` but this is also not recommended. The -a stands for all.

A file can directly be removed from the project directory and the staging area using `git rm file1.txt` command.

# Renaming, ignoring and viewing files

If we rename a file, git will think the original file was removed and a new one was added. So we have to stage them both again. Once we do so, git will recognize that the file has been renamed. In the below example, file.txt was renamed to main.js

![[Attachments/11.png]]

Alternatively, the file can directly be renamed in git using the `git mv file1.txt main.js` command.

A *.gitignore* file can be created in the project directory and all the file names that should never be tracked should be added to this file. When this file is committed, the file names mentioned in it will not be tracked by git.

github.com/gitignore has a list of common files to be excluded in each programming language.

`git status -s` gives us a short summary of the files.

##### Comparing file #skippable

![[Attachments/12.png]]

`git diff --staged` will show us the actual changes we have made in the file though viewing them in the terminal is cumbersome so it is recommended to use GUIs or difftools. `git diff` will also show us unstaged changes.

![[Attachments/13.png]]

-1,4 means the old file and the number of lines. Same with +1,4 for the new file.

The below command sets a default difftool in this case VScode.
![[Attachments/14.png]]

LOCAL and REMOTE are placeholders for the old and new files.

![[Attachments/15.png]]
![[Attachments/16.png]]

# Viewing history

We can view the history of commits we made in the repo using the `git log` command.

![[Attachments/17.png]]

HEAD is a reference to the current branch we are working on and our current branch is 'master' (more on branches below).

`git log --oneline` will show us a short summary of the commits made.
![[Attachments/18.png]]

`git log --online --reverse` shows the order in reverse.

`git show e1909f7` will display the changes in a specific commit. The last part is the id of the commit. It can also be done using `git show HEAD~1` where 1 is the number of commits we want to go back to.
![[Attachments/19.png]]

`git show HEAD~1:"Study Notes/Java/Basics"` will show changes in a specific file.
`git ls-tree Head~1` will show all the files and folders (Git objects) in the directory in that commit.

Following are the types of git objects
* Commits
* Blobs (Files)
* Trees (Directories)
* Tags

# Github repository

Although we have created a local repository and made changes, we need to push them to a repository like github or gitlab so that they can be stored in the cloud.

To do that, we first need a github account. Once created, we have to provide authorization for us to connect from our local to our github account. This can be done using SSH key.

`ssh-keygen -t ed25519 -C "your_email@example.com"` This command will generate a new SSH key for us. A passphrase can optionally be added for additional security.
![[Attachments/20.png]]

Once the key is generated, we need to add it to our ssh agent. To start the ssh agent, use `eval "$(ssh-agent -s)"`. This can also be set to start automatically whenever gitbash is opened. To do so, refer to the below.

#Reference  https://docs.github.com/en/authentication/connecting-to-github-with-ssh/about-ssh

Key can be added using `ssh-add ~/.ssh/id_ed25519`
![[Attachments/21.png]]

It will add two files. The default name will be id_ed25519 which is the private key and id_ed25519.pub which is the public key which needs to be added to Github. The contents of the file need to be added to our github ssh keys list. Follow the steps in the above url. Once done, we can push our local directory to the repo.

Now our code can be pushed to github using the below commands.

```shell
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:profile_Name/Obsidian_Vault.git
git push -u origin main
```

![[Attachments/22.png]]