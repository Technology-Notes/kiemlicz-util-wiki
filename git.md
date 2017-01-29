#Useful commands
Compare file with branch:

`git diff mybranch otherbranch -- somefile`

#Submodules
Project can depend on other projects. Root project is called **superproject**. Submodule is always a commit in other repostiory. In other words: submodule points to particular commit.

##Adding
`git submodule add https://repo.url... [path]` add submodule to project at _path_. If no _path_ is given then repo name is used.

##Deleting
1. `git submodule deinit <submodule>`
2. `git rm <submodule>`
3. `rm -rf .git/modules/asubmodule`

##Cloning
By default submodules are not cloned. In order to clone repository that contains submodules:

1. `git clone <repo url>`
2. `git submodule init`
3. `git submodule update`

##Upgrading
In order to upgrade the submodule (actually submodule's commit):

1. `cd submodule_dir`
2. `git checkout desired_branch`
3. `git pull`
4. `cd ..`
5. `git add submodule_dir && git commit -m "..."`

#Useful infographic
![](http://blog.podrezo.com/wp-content/uploads/2014/09/git-operations.png)