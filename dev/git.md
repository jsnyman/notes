# Git

* Retrieve locally deleted file:   
  If the deletion has not been committed, the command below will restore the deleted file in the working tree.
```
$ git checkout -- <file>
```

* Clone only one folder  
https://stackoverflow.com/questions/600079/how-do-i-clone-a-subdirectory-only-of-a-git-repository#13738951  
  What you are trying to do is called a sparse checkout, and that feature was added in git 1.7.0 (Feb. 2012). The steps to do a sparse clone are as follows:  
```
mkdir <repo>
cd <repo>
git init
git remote add -f origin <url>
```

This creates an empty repository with your remote, and fetches all objects but doesn't check them out. Then do:
```
git config core.sparseCheckout true
```

Now you need to define which files/folders you want to actually check out. This is done by listing them in `.git/info/sparse-checkout`, eg:
```
echo "some/dir/" >> .git/info/sparse-checkout
echo "another/sub/tree" >> .git/info/sparse-checkout
```

Last but not least, update your empty repo with the state from the remote:
```
git pull origin master
```

  You will now have files "checked out" for `some/dir` and `another/sub/tree` on your file system (with those paths still), and no other paths present.


* Clone only a single file  
https://stackoverflow.com/questions/1125476/retrieve-a-single-file-from-a-repository   
  In git version 1.7.9.5 this seems to work to export a single file from a remote
```
git archive --remote=ssh://host/pathto/repo.git HEAD README.md
```

### Common commands

* Shows you the URLs that Git has stored for the remote server associated with this folder.
```
$ git remote -v
```

* Fetch any new work that has been pushed to the server since you cloned (or last fetched from) it. It doesn’t automatically merge it with any of your work or modify what you’re currently working on.
```
$ git fetch origin
```

* Automatically fetch and then merge that remote branch into your current branch
```
$ git pull
```

* When you have your project at a point that you want to share, you have to push it upstream.
```
$ git push <remote> <branch>
$ git push origin master
```

* Information about the remote server and branches
```
$ git remote show origin
```
