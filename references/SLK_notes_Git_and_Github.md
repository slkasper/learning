# Git
You can create repositories on your machine without using github.  It will version control, etc, but it will only be local to this machine.  Later, you can push it up to github, but that's another topic.

## Creating a repo
```bash
git init
```
Once you initialize a repo, you be able to do `ls -a` and see a .git folder in there now.

## Check status of repo
```bash
git status
```
Git status lets you see info on all the files in this repo. Use this like `ls` in normal bash.

## Add a file for staging
```bash
git add <name>.py
```
Using git is not as simple as hitting save.  You have to stage your file first, _then_ you can commit it. Can't commit without adding first.

You can also add a `.` flag or `-A` flag after the add to add all files *and* include any deletions you've done to your repository files (otherwise the repo won't know you've removed something).

So that looks like this:
```bash
git add -A
```
 or 
```bash
git add .
``` 

### Remove a file from staging before committing
```bash
git rm <name.py>
``` 
...I think. He did this during lecture. Might have to double check the syntax in stackexchange. (see here: https://stackoverflow.com/questions/19730565/how-to-remove-files-from-git-staging-area)



## Commit a staged change
```bash
git commit -m <'write a message here to save as a note for your commit'>
```
The `-m` flag allows you to write a single like message, as a kind of label for the commit. Note, you need to use '' or "" for this method.

It might be better to have a multi-line message. In which case, use this method instead:
```bash
git commit -v <'write a message here to save as a note for your commit'>
```
Jeff called this visual, but you can also use vim (ask Jon for clarification) and it seems to be similar, so I think v is for vim.

### Commands when using -v or vim
If you're using this one, it sort of locks up your terminal unless you use the correct commands.  Here is how you navigate this one:

```
i <message here>
```
This is input. You type i first, then you can add the verbiage you're going to use for the multi-line message.

When you finish youre message, hit `esc`
*THEN* 
```
:wq
```
To _write_ and _quit_

### Remove last commit (maybe)
I *think* this is how you undo the last commit:
```bash
git reset HEAD^
``` 
Where HEAD is the most recent commit
(check stack exchange if you need to do this and correct this if it's wrong)

## Viewing Commits
Couple of ways to see commits.  Log is like status.  Show has more info, but you would call the *sha* or the commit id.

### Check commit status using log
```bash
git log
```
### See only commits to only one branch
```bash
git log <branch_name>
```

Log will show you a list of commits and give you the id of each commit, also known as the SHA.
### See a specific commit with the show command
```bash
git show HEAD
```
OR 
```bash
git show <copied SHA from log list to see a specific commit>
```


## Getting Around Branches of Git Repository
Once you have a history, the goal is to make changes without hurting the current working code, or the Main branch.  When you come along and want to modify or add changes, it is best to create a new branch and work from there, working and testing until you are sure it's good to be merged back to the main branch.  Then you merge it.

These are the commands to follow this process correctly.

### See the Current Branch You Are In
```bash
git branch -a
```
You will see a list of branches, one will have a _*_ in front of it. That is your current branch.  You may just see one name "main" and that means you're in the only branch; main.

### Create a New Branch and Change to It
When you create a new branch, you move to it in the same step. Use the checkout command.
```bash
git checkout -b <new_branch_name>
```
At this moment, I don't know what -a and -b are all about, but these are the flags you need.  This command creates a new branch at your current point in history.  That means it has all the history up until now, but anything new will be added to the new branch, not the old.  New changes on this branch can and should be merged later after testing.

## Move Around Branches - back to main or to one that exists
To change branches without createing a new one, don't use the -b flag. You do this mostly to switch back to the main because you need to be in the main to merge.  You can also use it to go from main to the new branch if you need.
```bash
git checkout -
```
to go to the last branch.  This should be good enough to get you back to main. But to be more explicit, call the actual name of the branch

```bash
git checkout <main or other branch_name>
```

## Once changes made and checked, Merge
The above section shows you how to move to different branches.  Once you want to merge your changes into the current branch, make sure you are in the main branch.  Then call the merge command.
```bash
git merge <new_branch_name>
```

# Take a repo from Github and make changes to save to MY github
Usually this is if you want to start from someone else's repo and do a project to save to your own repo.

Here are the steps:
1. From the other repo, hit Fork.  This will let you fork the repo to your personal account.
2. Go back to your account now and copy the link from the Clone option so you can clone from the new repo you just created from YOUR account.
3. Locally on your machine, in the terminal, starting from whatever folder you want, copy the clone link and clone away
4. This cloned repo is connected to your github repo now, so no need to init!
5. Make your changes, commits and then push with the origin Head command and you should be set.

# Github
I really don't want to rewrite his lab, so I'm pasting it here:

https://colab.research.google.com/drive/1-ZVvidKxe9BcXYve03STVKKsUz3XsyOr?usp=sharing

Brief summary:
- Create a new repository in your Github account
    - Be sure to create a README - it will take your message when you commit
- Go back to terminal and init your repo if you haven't done it
- Commit your changes

Now you need to use the terminal to push this commit to github.

```bash
git remote add origin git@github.com:JeffKatzy/albums_songs.git
( <---git remote add origin git@github.com:<user_name/repo_name.git> --)
git push -u origin master
```

## Password Authentication was removed error
Had to run though this to get it to work. Should see my token in the Keychain access on this mac (the Jon's macbook air).  If you get a new comp or have to switch for any reason, you're going to need to do this step.

https://stackoverflow.com/questions/68775869/message-support-for-password-authentication-was-removed-please-use-a-personal

my personal access token is found under
under Jigsaw called personal_access_token_github_6-9-23.txt
