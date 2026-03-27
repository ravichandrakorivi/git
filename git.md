# Git

## Connect your local Git repo to GitHub and push code for the first time

```bash
# Link your local repo to a remote repo on GitHub.
# GitHub repo is where we want to push our code.
# origin is just a nickname (convention) for the remote repository URL.
git remote add origin https://github.com/ravichandrakorivi/python.git

# Rename your current working branch to main.
# -M : force rename (even if main already exists).
# GitHub’s default branch is main.
# Older Git versions use master as the default branch.
# Renaming the branch to main ensures consistency.
git branch -M main

# Push your local main branch to remote origin repo
git push -u origin main
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

