# INSTALL

## How to create github.io pages to use Hugo 

Type the following command :
```
$ doas pkg_add hugo
```

Set default branch to main :
```
git config --global init.defaultBranch main
```

Create new site :
```
$ hugo new site m-fujimoto.github.io 
```

Change directory :
```
$ cd m-fujimoto.github.io
```

Initialize a repository :
```                                      
$ git init
```

Add a new remote to use SSH :
```
$ git remote add origin git@github.com:m-fujimoto/m-fujimoto.github.io.git
```

List existing remotes :
```
$ git remote -v
```

Adds new or changed files in your working directory to the Git staging area.
```
$ git add .
$ git commit -m "initial update"
$ git push --set-upstream origin main
```

* See [Host on GitHub Pages](https://gohugo.io/host-and-deploy/host-on-github-pages/)
* See [Quick start](https://gohugo.io/getting-started/quick-start/)
