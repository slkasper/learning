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

#### Be sure you're local and remote are *synced!!!*. Before making changes, make sure your branch starts from the latest remote location by pulling from remote with `git pull origin main`
```bash
git checkout -b <new_branch_name>
```
At this moment, I don't know what -a and -b are all about, but these are the flags you need.  This command creates a new branch at your current point in history.  That means it has all the history up until now, but anything new will be added to the new branch, not the old.  New changes on this branch can and should be merged later after testing.

Now make changes and commits.  Note, until you push, your local branch does not exist in the remote repository. To push your changes to remote, use this command:
```bash
git push origin <branch>
```
What does this mean?  `origin` is a shorthand label for the remote repository (see below on how to set that up).  So you are saying push to the repository, specifically to the given branch.  If it doesn't exist on the remote repo, like in this case where you are pushing your local branch which has changes ahead of the remote, it will create the remote branch for you, syncing them.

## Move Around Branches - back to main or to one that exists
To change branches without createing a new one, don't use the -b flag. You do this mostly to switch back to the main because you need to be in the main to merge.  You can also use it to go from main to the new branch if you need.
```bash
git checkout -
```
to go to the last branch.  This should be good enough to get you back to main. But to be more explicit, call the actual name of the branch

```bash
git checkout <main or other branch_name>
```

## Once changes made and checked..
The above section shows you how to move to different branches.  Once you want to merge your changes into the current branch, make sure you are in the main branch.  Then call the merge command.
```bash
git merge <new_branch_name>
```

This is not what I've done, because, usually in a corporate setting, you need a pull request before merging. So instead, I'd push changes to the remote branch. Once that is ready to merge, createa a pull request explaining the changes and add a reviewer.  

Once changes are oked, I usually merge in github, not the command line.  This means essentially that I've moved the remote farther ahead then the local! so MAKE SURE YOU GO BACK TO LOCAL AND PULL THE CHANGES!!!

Move back to main in your local git:
```bash
git checkout main
```

Then pull from remote (which is now merged and the most updated!)
```bash
git pull origin main
```

You can then delete your dangling branch with  (this might actually pull and prune...I'm not sure)
```bash
git fetch --prune
```

# Github
I really don't want to rewrite his lab, so I'm pasting it here:

https://colab.research.google.com/drive/1-ZVvidKxe9BcXYve03STVKKsUz3XsyOr?usp=sharing

Note, these instructions are starting from scratch.  Once you have a repo in both remote and local, it's super important to keep them synced before making changes!

Brief summary:
- Create a new repository in your Github account
    - Be sure NOT to create a README - it will mess up your connecting local to remote!
- Go back to terminal and init your repo if you haven't done it
- Commit your changes

Now you need to use the terminal to push this commit to github.

```bash
git remote add origin git@github.com:JeffKatzy/albums_songs.git
( <---git remote add origin git@github.com:<user_name/repo_name.git> --)
git push -u origin master
```
Here's a really nice quick start (from this link https://www.warp.dev/terminus/git-create-repository):
```
$ cd my-project

# initialise a git repository
$ git init

 # Add all files to be tracked
 $ git add .

 # commit tracked files with a message
 $ git commit -m “some message”

 # configure a remote repo - if you haven't already, go to github, create a new repo and get the remote url for this step
 $ git remote add origin <remote_url>

 # set up origin push to a remote repository
 $ git push --set-upstream origin main

# once that's set, you can now use this to push future changes to github
 $ git push origin main

```
## If you want to see what remote branch is synced

Sometimes it might be good to check what your remote branch is synced to.  If you set it up with the `git remote add origin` line above, you should be on whatever you set it to.  But you can always start from cloning someone elses repo. In which case, you might not have set a remote origin.  Or maybe you just stepped away from a project for a long time. Whatever the reason, if you want to make sure you are pushing to the correct branch before you make any mistakes, use this command:
```bash
git remote show origin
```
It will show you all the remote branches and the local configured for pull locations.  Here's what it might look like:

```bash
(jigsaw) ➜  docs git:(issues/1-slk) git remote show origin
* remote origin
  Fetch URL: git@github.com:Faraday1221/parkwise.git
  Push  URL: git@github.com:Faraday1221/parkwise.git
  HEAD branch: main
  Remote branches:
    issues/1-slk   tracked
    issues/1-slk-1 new (next fetch will store in remotes/origin)
    main           tracked
  Local branch configured for 'git pull':
    main merges with remote main
  Local refs configured for 'git push':
    issues/1-slk pushes to issues/1-slk (fast-forwardable)
    main         pushes to main         (up to date)
```

## Create a Repo on GitHub and start from there
The above is a good method if you are creating a new repo from files you already have on your local machine but have not saved in git yet.  This is great if you started something, decided it would be good to add to github and go from there. Or you forgot to add your project to github, so you need to get it up there.

But what if you want to create a new repo and start your project from scratch, knowing you want to have github and git and have them synced up.  In that case, before you create your files, you can start by creating a new repo on Github, and in this case you CAN let it add a readme.  

Once you have your repo, you can either keep working on the browser and add files there, or clone the repo to your local folder.  Just clone as usual in your terminal and everything is ready to go, just like we did for class.  But in this case, we can push to origin and it will be updating our repo (something we didn't do on Jeffs repos).


# Take a repo from Github and make changes to save to MY github
Usually this is if you want to start from someone else's repo and do a project to save to your own repo.

Here are the steps:
1. From the other repo, hit Fork.  This will let you fork the repo to your personal account.
2. Go back to your account now and copy the link from the Clone option so you can clone from the new repo you just created from YOUR account.
3. Locally on your machine, in the terminal, starting from whatever folder you want, copy the clone link and clone away
4. This cloned repo is connected to your github repo now, so no need to init!
5. Make your changes, commits and then push with the origin Head command and you should be set.

## Do it without forking - change `origin` and `upstream` locations instead 
this is well written here:

https://stackoverflow.com/questions/18200248/cloning-a-repo-from-someone-elses-github-and-pushing-it-to-a-repo-on-my-github

copied here, but go to the link for a cleaner view.

#### GitHub: `git clone` someone else's repository & `git push` to your own repository

I'm going to refer to _someone else's_ repository as the _other repository._

##### 1. Create a new repository at github.com. (this is your repository)
  - Give it the same name as the other repository.
  - Don't initialize it with a README, .gitignore, or license.
##### 2. Clone the other repository to your local machine. (if you haven't done so already)
  - `git clone https://github.com/other-account/other-repository.git`
##### 3. Rename the local repository's current 'origin' to 'upstream'.
  - `git remote rename origin upstream`
##### 4. Give the local repository an 'origin' that points to your repository.
  - `git remote add origin https://github.com/your-account/your-repository.git`
##### 5. Push the local repository to your repository on github.
  - `git push origin main`

#### Now 'origin' points to your repository & 'upstream' points to the other repository.

- Create a new branch for your changes with `git checkout -b my-feature-branch`.
- You can `git commit` as usual to your repository.
- Use `git pull upstream main` to pull changes from the other repository to your main branch.

## Password Authentication was removed error
Had to run though this to get it to work. Should see my token in the Keychain access on this mac (the Jon's macbook air).  If you get a new comp or have to switch for any reason, you're going to need to do this step.

https://stackoverflow.com/questions/68775869/message-support-for-password-authentication-was-removed-please-use-a-personal

my personal access token is found under
under Jigsaw called personal_access_token_github_6-9-23.txt
