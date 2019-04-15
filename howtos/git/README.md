# Git
>A guide to git version control
## Branches
```sh
git branch 	# ( returns current branch )
git branch -a	# ( returns all branches )
git branch -r 	# ( returns all remote brnaches )
git branch -b branch_name # ( checks out new or existing branch )
```
## Remotes
```sh
git remote 	# ( see all remotes )
git remote -v 	# ( shows the url of the git remotes )
git remote add [shortname] [url] 	# ( add a remote repo )
git fetch [shortname] 	# ( pull remote data down and sets it to master )
git remote rm [shortname] # ( removes remote repo )
git remote rename [shortname] [new-name] # (rename remote repo )
git remote show # ( shows a ll remtoe repos )
git push [remote-shortname] [branch-name] # ( push to remote server)
```
## Git bisect
```sh
git bisect start
git bisect bad       
git bisect good <commit hash>
git bisect good aa95f4a
### Git will now bisect good from bad commits ###
# Now test if problem is resolved:
# yes: 
git bisect good
# no: 
git bisect bad
# Once complete
git bisect reset
```
## Git bare repository
```sh
cd folder/to/become/repository.git
git init --bare
Local
git --work-tree=/var/www/html/<file directory> --git-dir=/<git bare server>.git checkout -f branch
```
## Git initialize repository
```sh
cd folder/to/become/local/repository
git init
```
## Git add remote server
```sh
# Add new remote
git remote add remote_name ssh://$username@remote.hostname/path/to/repository.git
git remote add remote_name /absolute/path/to/your/git/server/locally
# eg
git remote add af1 ssh://afello@afelloprint.com:/afellorepo.git
# Update remote url
git remote set-url af1 ssh://<user>@<host>:/<repo>.git
# eg
git remote set-url af1 ssh://hadley@hadleyfellows.com:/hadleysrepo.git
```

## Git fetch, pull, checkout, push
```sh
# git fetch
git fetch remote_name
# git pull
git pull remote_name master
# git checkout new local
git checkout -b branch_name
# git push
git push remote_name
```


## Git subtree
```sh
git remote add remote1 ssh://a@host.com/repo.git 
git subtree add --squash --prefix=<some directory>/ remote1 <branch name>
git subtree pull --squash --prefix=<some directory>/ remote1 <branch name>
```


## Git log
```sh
git log --pretty=format:"%h; author: %cn; date: %ci; subject:%s" <branch> <hash from>..<hash to> > <output_file>.txt
# eg
git log --pretty=format:"%h; author: %cn; date: %ci; subject:%s" master e19d00d8e0c7b00ca71d37278ced47beecb4f73b..040304d324b813c186e1cc7ba6f7b580d2d40155 > log1.txt
	git log --no-merges --pretty=format:"%h; author: %cn; date: %ci; subject:%s" master e19d00d8e0c7b00ca71d37278ced47beecb4f73b..040304d324b813c186e1cc7ba6f7b580d2d40155 > log3.txt
```


git cherry pick
git hooks (post-receive)
# vim /srv/<repo>.git/hooks/post-receive
#!/bin/sh                                                                                                                          	
while read oldrev newrev ref    
do                                                                                                 
  echo "oldrev: $oldrev"   
  echo "newrev: $newrev"
  echo "ref: $ref"
  pwd
  if [ "$ref" = "refs/heads/master" ];
  then
    echo "Master ref received.  Deploying master branch to production..."
    branchToDeploy="master"
  elif [ "$ref" = "refs/heads/master-deployment" ];
  then
    echo "Master-deployment ref received.  Deploying master branch to production..."
    branchToDeploy="master-deployment"
  elif [ "$ref" = "refs/heads/develop" ];
  then
    echo "Develop ref received.  Deploying develop branch to beta..."
    branchToDeploy="develop"
  else
    echo "Ref $ref successfully received.  Doing nothing: only master and develop branches may be deployed on this server."
    exit 0
  fi 
  mkdir -p /var/www/html/folder-${branchToDeploy}   
  git --work-tree=/var/www/html/folder-${branchToDeploy} --git-dir=/srv/mybarerepo.git checkout -f ${branchToDeploy}
  git rev-parse --short ${branchToDeploy} > /var/www/html/folder-${branchToDeploy}/folder-version
  chown -R apache:apache /var/www/html/folder-${branchToDeploy}
  pushd /var/www/html/folder-${branchToDeploy}
  if [ -f example1.sh ]                                                                                                  
    then   
      chmod +x example1.sh                                                                                                 
      ./example1.sh ${branchToDeploy}                                                                                      
    fi
  popd
done

git subdamin
git branch -r --no-merged

git branch -m old_branch new_branch         # Rename branch locally    
git push origin :old_branch                 # Delete the old branch    
git push --set-upstream origin new_branch   # Push the new branch, set local branch to track the new remote

git reset HEAD --hard
git reset origin/master --hard
