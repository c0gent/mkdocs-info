## Initializing an [MkDocs](https://www.mkdocs.org/) Repository

These instructions assume a linux-based environment but should(?) work fine
elsewhere. [Git](https://git-scm.com/) and
[MkDocs](https://www.mkdocs.org/#installation) should be installed.

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

Ensure you have a `travis-ci.com` account, that it is
[activated](https://docs.travis-ci.com/user/getting-started/) for the
`my-mkdocs-project` repo, and that the [Travis
CLI](https://github.com/travis-ci/travis.rb#installation) is installed. Ensure
you are logged in `travis login --pro`. If you want to use a `travis-ci.org`
account instead, replace occurances of `--pro` with `--org`.

* `cd {...}/my-mkdocs-project`

* Create and encrypt a new SSH deploy key:

    * *Do not use an existing key.*
    * *Do not add this key to your personal github.com account (we will be
      adding it to the repository only).*
    * **Ensure git will not push the secret key to repo**:
        * `echo "deploy-key" >> .gitignore`.
            * We will also delete or move the key after encrypting.
    * Generate key:
        * `ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f deploy-key`.
            * The e-mail address doesn't matter; Use your own if you want.
        * Do **not** enter a password when prompted. Leave it blank. We will
          be encrypting the key ourselves.
    * Encrypt key:
        * `travis encrypt-file deploy-key --pro` (or `--org`).
        * You should see the decryption command printed (example: `openssl
          aes-256-cbc -K $encrypted_9186e51e85a9_key -iv
          $encrypted_9186e51e85a9_iv -in deploy-key.enc -out deploy-key -d`).

* Create a `.travis.yml` in the repo root with the following contents:

```yaml
env:
- - global
  - - GIT_NAME: my-mkdocs-project
    - GIT_EMAIL: your_email@example.com
    - GH_REF: << PASTE YOUR REPO URL HERE >>
language: python
before_script:
- pip install mkdocs
script:
- << PASTE OPENSSL DECRYPTION COMMAND HERE >>
- chmod 600 deploy-key
- eval `ssh-agent -s`
- ssh-add deploy-key
- git config user.name "Automatic Publish"
- git config user.email $COMMIT_AUTHOR_EMAIL
- git remote add gh-token "${GH_REF}";
- git fetch gh-token && git fetch gh-token gh-pages:gh-pages;
- if [ "${TRAVIS_PULL_REQUEST}" = "false" ]; then echo "Pushing to github"; PYTHONPATH=src/
  mkdocs gh-deploy -v --clean --remote-name gh-token; git push gh-token gh-pages;
  fi;
```

* Paste **your repo url** (from before) and the **openssl decryption command**
  where noted (remove brackets `<<`, `>>`). Change the `GIT_NAME` as
  appropriate. The `GIT_EMAIL` doesn't matter; Use your own if you want.

* Add the deploy public key to the repository deploy keys:
    * Repository 'Settings' tab -> 'Deploy keys' -> 'Add deploy key'.
    * Use `Travis Deploy` or some such title.
    * Paste the contents of `deploy-key.pub` into the 'Key' box.
    * Check the “Allow write access”. The deploy key will be used to
      authenticate the travis-ci build in order to push commits in the same
      way it automatically authenticates you when using git from the command
      line (when configured).
    * Click 'Add Key'.

* Delete or move the `deploy-key` and `deploy-key.pub` files somewhere safe
  (to `$HOME/.keys/my-mkdocs-project` for example).

* `git add -A`, commit, and push.

* Go to your `travis-ci.com` or `.org` dashboard and check the status of the
  build. If you run into problems, please [file an issue
  here](https://github.com/c0gent/mkdocs-test/issues)!

* Make a change to your docs (`docs/index.md` for example), push the changes,
  and ensure that they appear on `https://<USER>.github.io/<REPO>`.