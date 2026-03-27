
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

