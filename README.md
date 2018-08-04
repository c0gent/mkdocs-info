## Initializing an [MkDocs](https://www.mkdocs.org/) Repository

These instructions assume a linux-based environment but should(?) work fine
elsewhere. MkDocs should already be
[installed](https://www.mkdocs.org/#installation).

Our project will be named `my-mkdocs-project`. Rename as needed.

### Getting Set Up

* Create a new github repository:
    * Default settings
        * Don't add a license, readme, etc.
        * Don't follow any of the github setup instructions provided after
          creation but, to save time, copy the repo url to your clipboard for
          later.
* Create a new MkDocs project:
    * `mkdocs new my-mkdocs-project`
    * `cd my-mkdocs-project`
    * If you'll be building locally for testing: `echo "site/" >> .gitignore`
* Initialize git repo and push:
    * `git init`
    * `git add -A`
    * `git commit -m "Init."`
    * `git remote add origin <REPO_URL>` (paste the repo url)
    * `git push -u origin master`
* Build and Deploy:
    * `mkdocs gh-deploy`
        * This will automatically create a `gh-pages` branch, build the
          documentation, commit, and push (pretty damn handy).
        * Your documentation is now visible at
          `https://<USER>.github.io/<REPO>)`.


### Travis

Assumes you have a `travis-ci.com` account and that it is [activated for the
`my-mkdocs-project` repo](https://docs.travis-ci.com/user/getting-started/).

*