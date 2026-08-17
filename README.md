# Git

## Install Git on Ubuntu

Check whether Git is already installed:

```bash
git --version
```

If you get something like:

```text
git version 2.x.x
```

Git is already installed.

Otherwise:

```bash
sudo apt update
sudo apt install git
```

Then verify:

```bash
git --version
```

You can also check where Git is installed:

```bash
which git
```

Usually:

```text
/usr/bin/git
```

## Configure Git

Git needs to know your name and email.

Set your name:

```bash
git config --global user.name "Your Name"
```

Set your GitHub email:

```bash
git config --global user.email "your-email@example.com"
```

Check the configuration:

```bash
git config --global --list
```

You should see something similar to:

```text
user.name=Your Name
user.email=your-email@example.com
```

We can also check individual values:

```bash
git config --global user.name
git config --gloabl user.email
```

## Create a GitHub account

If you don't already have one, create an account at: [GitHub](https://github.com/).

Your GitHub username is important because your repositories will normally appear under:

```text
https://github.com/<username>/<repository>
```

## Configure authentication — SSH is recommended

There are two common ways to authenticate Ubuntu with GitHub:
1. HTTPS
2. SSH

For your Ubuntu development environment, SSH is recommended.

The advantage is that after configuring it, you don't have to repeatedly enter GitHub credentials when pushing.

## Generate an SSH key

First check whether you already have one:

```bash
ls -la ~/.ssh
```

Look for files such as:

```text
id_ed25519
id_ed25519.pub
```

If you don't have them, create a new key:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

You'll see:

```text
Enter file in which to save the key:
```

Press Enter to accept:

```text
/home/ravi/.ssh/id_ed25519
```

Then:

```text
Enter passphrase:
```

You can enter a passphrase for better security, or press Enter for no passphrase.

You should then have:

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

Important:

```text
id_ed25519       ← PRIVATE KEY — NEVER SHARE
id_ed25519.pub   ← PUBLIC KEY — can be added to GitHub
```

## Start the SSH agent

Run:

```bash
eval "$(ssh-agent -s)"
```

Then add your key:

```bash
ssh-add ~/.ssh/id_ed25519
```

## Copy your public key

Run:

```bash
cat ~/.ssh/id_ed25519.pub
```

You'll get something like:

```text
ssh-ed25519 AAAAC3... your-email@example.com
```

Copy the entire line.

Do not copy:

```text
id_ed25519
```

Only:

```text
id_ed25519.pub
```

## Add the SSH key to GitHub

In GitHub:

Profile → Settings → SSH and GPG keys → New SSH key

Give it a name such as:

```
Ubuntu Laptop
```

Paste your public key.

Then save it.

## Test GitHub SSH connection

From Ubuntu:

```bash
ssh -T git@github.com
```

The first time you may see:

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Enter:

```text
yes
```

A successful authentication will give you a message indicating that you've authenticated with GitHub.

Now your Ubuntu machine is connected to GitHub through SSH.

## Break up `eval "$(ssh-agent -s)"`

### ssh-agent:

`ssh-agent` is a program that stores your SSH private keys in memory so that you don't have to repeatedly enter your passphrase when using SSH.

When you run:

```bash
ssh-agent -s
```

it starts an SSH agent and prints something similar to:

```text
SSH_AUTH_SOCK=/tmp/ssh-XXXXXX/agent.1234; export SSH_AUTH_SOCK;
SSH_AGENT_PID=1234; export SSH_AGENT_PID;
echo Agent pid 1234;
```

These are shell commands, not just informational text.

The important variables are:

- `SSH_AUTH_SOCK` → tells SSH programs where to communicate with the agent
- `SSH_AGENT_PID` → identifies the agent process


### What does `$(ssh-agent -s)` mean?

```bash
$(ssh-agent -s)
```
is a command substitution in Bash.

It means:

Run `ssh-agent -s` and substitute its output here.

For example, conceptually:

```bash
echo $(date)
```

might become:

```text
Sun Aug 16 07:20:00 PM IST 2026
```

So:

```bash
$(ssh-agent -s)
```

becomes the text printed by `ssh-agent -s`.

### What does `eval` do?

`eval` takes a string and executes it as shell commands.

So:

```bash
eval "$(ssh-agent -s)"
```

effectively does this:
- Start `ssh-agent`
- Get the environment-variable commands it prints
- Execute those commands in your current shell

For example, if `ssh-agent -s` produces:

```text
SSH_AUTH_SOCK=/tmp/ssh-abc/agent.1234; export SSH_AUTH_SOCK;
SSH_AGENT_PID=1234; export SSH_AGENT_PID;
```

then `eval` executes them, resulting in:

```bash
export SSH_AUTH_SOCK=/tmp/ssh-abc/agent.1234
export SSH_AGENT_PID=1234
```

Your current terminal therefore knows how to communicate with the SSH agent.

## Create your first Git project

Let's use one of your existing programming projects as an example.

Suppose you have:

```text
~/academics/shell-scripting
```

Go there:

```bash
cd ~/academics/shell-scripting
```

Look at the files:

```bash
ls
```

## Initialize Git

Run:

```bash
git init
```

You'll see something similar to:

```text
Initialized empty Git repository in ...
```

Git has now created a hidden directory:

```text
.git
```

Check it:

```bash
ls -la
```

You'll see:

```text
.git
```

This `.git` directory contains the information Git needs to track your project.

## Check Git status

Run:

```bash
git status
```

For example:

```text
Untracked files:
    list_files.sh
```

This means Git sees the file but isn't tracking it yet.

## Create a `.gitignore`

This is extremely important.

A `.gitignore` file tells Git which files should not be committed.

You might put:

```text
*.log
*.tmp
*.swp
__pycache__/
.env
venv/
.venv/
```

For Python projects, a useful `.gitignore` often includes:

```text
__pycache__/
*.py[cod]
.venv/
venv/
.env
```

## Add files to Git

You can add a specific file:

```bash
git add list_files.sh
```

Or everything in the current directory:

```bash
git add .
```

Then check:

```bash
git status
```

You should see something like:

```text
Changes to be committed:
    new file: list_files.sh
```

## Make your first commit

Run:

```bash
git commit -m "Initial commit"
```

A commit is basically a snapshot of your project at a particular point in time.

## Create a repository on GitHub

Go to GitHub and select:

New repository

For example:

```text
shell-scripting
```

Choose whether it should be:

- Public
- Private

**Important**: If you're going to push an existing local repository, don't unnecessarily initialize the GitHub repository with another README, .gitignore, etc. Create the empty repository.

## Connect your local repository to GitHub

GitHub will give you a repository URL.

For SSH it will look like:

```text
git@github.com:USERNAME/shell-scripting.git
```

Inside your Ubuntu project:

```bash
# Link your local repo to a remote repo on GitHub.
# GitHub repo is where we want to push our code.
# origin is just a nickname (convention) for the remote repository URL.
git remote add origin git@github.com:USERNAME/shell-scripting.git
```

Check:

```bash
git remote -v
```

You should see:

```text
origin  git@github.com:USERNAME/shell-scripting.git (fetch)
origin  git@github.com:USERNAME/shell-scripting.git (push)
```

## Rename your branch to `main`

Modern GitHub repositories generally use `main`.

Run:

```bash
# Rename your current working branch to main.
# -M : force rename (even if main already exists).
# GitHub’s default branch is main.
# Older Git versions use master as the default branch.
# Renaming the branch to main ensures consistency.
git branch -M main
```

Check:

```bash
git branch
```

You should see:

```text
* main
```

## Push your project to GitHub

```bash
# Push your local main branch to remote origin repo
git push -u origin main
```

The first push establishes the relationship between:

```text
local main
      ↓
origin/main
```

After that, normally you can simply use:

```bash
git push
```

## Upstream tracking

```bash
# Upload code to remote repo + remember destination
git push -u origin main
```

`-u` : sets upstream tracking

After this command, you can simply run:

```bash
git push
```

instead of:

```bash
git push origin main
```

Git now remembers where to push/pull.

## `origin` is NOT special

It’s just a convention to name the remote repo as `origin`. We could name it anything:

```bash
git remote add myrepo https://github.com/...
```

Then push using:

```bash
git push myrepo main
```

## Already having a remote

If you already added a remote repo earlier, then

```bash
git remote add origin ...
```
will fail.

```bash
git remote -v
```

```
origin  https://github.com/ravichandrakorivi/python.git (fetch)
origin  https://github.com/ravichandrakorivi/python.git (push)
```

This confirms:

`origin` → that URL

**Fix**:

```bash
git remote set-url origin https://github.com/...
```

## Your normal Git workflow

This is the most important part to remember.

Suppose you modify:

```text
list_files.sh
```

First:

```bash
git status
```

Then:

```bash
git add list_files.sh
```

Then:

```bash
git commit -m "Improve directory listing"
```

Then:

```bash
git push
```

So the normal cycle is:

```text
             modify files
                  ↓
             git status
                  ↓
              git add
                  ↓
             git commit
                  ↓
              git push
                  ↓
               GitHub
```

## `git add` vs `git commit` vs `git push`

`git add` moves changes into the staging area.

```bash
git add file.py
```

Think: "I want this change included in my next snapshot."

`git commit` creates a snapshot in your local repository.

```bash
git commit -m "Fix calculation"
```

Think: "Save this version permanently in my local Git history."

`git push` uploads your local commits to GitHub.

```bash
git push
```

Think: "Send my local commits to GitHub."

## See your commit history

Run:

```bash
git log
```

A more convenient version:

```bash
git log --oneline
```

For example:

```text
a8f31d2 Add error handling
4b72c91 Improve directory listing
91c2a44 Initial commit
```

## See what you changed

Suppose you modify a file.

Run:

```bash
git diff
```

This shows changes that haven't been staged.

After:

```bash
git add file.py
```

you can see staged changes with:

```bash
git diff --staged
```

## Clone an existing GitHub project

Suppose you find a repository on GitHub.

Instead of downloading ZIP, use:

```bash
git clone git@github.com:USERNAME/PROJECT.git
```

For example:

```bash
git clone git@github.com:USERNAME/shell-scripting.git
```

Then:

```bash
cd unix-scripting
```

Check:

```bash
git status
```

## The `git pull`

Suppose the repository on GitHub has newer changes.

```bash
git pull
```

Conceptually:

```text
GitHub
   │
   │ git pull
   ↓
Your local repository
```

A typical workflow when collaborating is:

```bash
git pull
# modify files
git add .
git commit -m "My changes"
git push
```

## Branches

Branches become important once your projects become larger.

Suppose your main branch contains stable code:

```text
main
 │
 ├── version 1
 ├── version 2
 └── version 3
 ```
 
 You want to experiment with new feature.

 Create a branch:

 ```bash
 git switch -c new-feature
 ```

 Now:

 ```text
 main
 │
 ├── stable code
 │
 └── new-feature
        │
        ├── experiment
        └── modifications
```

After completing the feature:

```bash
git add .
git commit -m "Add new feature"
git push -u origin new-feature
```

Then you can create a Pull Request on GitHub to merge it into `main`.

## Pull Request

A Pull Request (PR) is a request to merge the changes from the source/head branch (`new-feature`) into the destination/base branch (`main`).


### First, make sure branch `new-feature` is pushed to GitHub

```bash
git checkout new-feature
git status
git push origin new-feature
```

If `new-feature` has never been pushed before, use:

```bash
git push -u origin new-feature
```

You can verify:

```bash
git branch
git branch -r
```

You should see something like:

```text
* new-feature
  main

  origin/new-feature
  origin/main
```

### Create the Pull Request on GitHub

Open your repository on GitHub.

Then:
1. Select the `new-feature` branch from the branch dropdown.
2. Click Compare & pull request.
3. Check the branch selection carefully:
    - base: `main`
    - compare: `new-feature`
4. Review the Commits and Files changed tabs.
5. Enter a PR title, for example:
    ```text
       Merge it branch into main
    ```
6. Add a description explaining what you changed.
7. Click Create pull request.

GitHub specifically uses the base branch for the branch receiving the changes and the compare branch for the branch containing your changes.


## Removing the files which are already pushed to github


Add the files to be ignored to `.gitignore` file.


```bash
# Jupyter Notebook
.ipynb_checkpoints/

# Python
__pycache__/
*.pyc
.pytest_cache/

# Env files
.env
```

```bash
# Remove all the files from tracking.
# --cached removes the files from Git tracking only. Your local files stay safe.
git rm -r --cached .

# Re-add all the files except the ignored files for tracking.
git add .

# Commit the changes.
git commit -m "Clean repo and apply .gitignore"

# Push to GitHub.
git push origin main
```

