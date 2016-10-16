## Install

If your computer doesn't already have `git`, follow these instructions to get `git` with bash-completions.

1. `xcode-select --install` # to install Xcode command line tools
2. Install [Homebrew](http://brew.sh/)
3. `brew install git bash-completion`
4. Follow the instructions to setup your .bashrc or similar to use Homebrew's bash-completions.

## Generate SSH key

You can use one SSH key for both code and host access, or use separate keys. Using separate keys can be more secure in rare cases such as the [Debian bad PRNG issue](https://github.com/g0tmi1k/debian-ssh) where knowing that a user has bad key may open the door to attacks, of if there is a reason to copy the code key to a VM for development work.

### One SSH key

1. `ssh-keygen -t rsa -b 4096 -C 'your_email@example.com'`
2. Enter a reasonably secure passphrase *that you can type*. macOS will not let you paste the passphrase into the dialog box, so don't use a random passphrase that needs to be copied from a password manager. Save the passphrase in your password manager anyway.
  - The key will be saved in the SSH agent (Keychain), so you will only have to type the passphrase when you restart the computer

### Multiple SSH keys

1. `mkdir -p ~/.ssh`
2. `chmod 700 ~/.ssh`
3. `ssh-keygen -t rsa -b 4096 -C 'your_work_email@example.com' -f id_rsa_your_name_source_code` # this is the source code key
4. Enter a passphrase as described above
5. `ssh-keygen -t rsa -b 4096 -C 'your_email@example.com' -f id_rsa_your_name` # this is the host access key
6. Enter a passphrase as described above
7. `vim ~/.ssh/config` # and add the following

```
IdentityFile ~/.ssh/id_rsa_your_name

Host github.com
    User git
    IdentityFile ~/.ssh/id_rsa_your_name_source_code
```

## Generate PGP key

You can either:
```
brew install gpg
```
Or [Install a MacOS GPG suite](https://help.github.com/articles/signing-commits-using-gpg/)

[How to generate a pgp key](https://help.github.com/articles/generating-a-new-gpg-key/)


## Add keys to GitHub

[Add your ssh *public* key to GitHub](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/).

Run this to view your key in importable armor format:

```
gpg --list-secret-keys --keyid-format LONG

gpg --armor --export B3D312F115260A6B(Your key id instead)
```

[Add your PGP key to GitHub](https://help.github.com/articles/adding-a-new-gpg-key-to-your-github-account/)

You can check if your commit was signed with pgp by running this: 

```
git log --show-signature 3c7e385a093095a164c04c2dc00a9d4b07f60aa9
```

## Config git

```
git config --global user.name 'Your Real Name'
git config --global user.email 'your_work_email@example.com'
git config --global core.excludesfile '~/.gitignore_global'
git config --global user.signingkey ED5CDE14(substitute with your key)
git config --global commit.gpgsign true
echo '.DS_Store' >> ~/.gitignore_global
```

## Using git with GitHub

GitHub has a helpful `git` command-line wrapper called [hub](https://hub.github.com/). The most helpful thing it provides is the `git fork` sub-command that fork and adds remotes for you. `brew install hub` if you want to use it.

### Cloning

*Replace some_repo with the repo name, and github_username with your GitHub username.*

```bash
# Make your src directory
mkdir -p ~/src
cd ~/src

# Clone the repo you want to work on
git clone git@github.com:westfield/some_repo.git
cd some_repo

# Fork the repo and add your fork as a remote
git fork

# Now you will have two remotes, `origin` for the main westfield remote,
# and `github_username` for your fork
git remote -v
# github_username    git@github.com:github_username/some_repo.git (fetch)
# github_username    git@github.com:github_username/some_repo.git (push)
# origin    git@github.com:westfield/some_repo.git (fetch)
# origin    git@github.com:westfield/some_repo.git (push)

# Now you are ready to start working on the repo
```

### Making a change

```bash
# Go to the repo
cd ~/src/some_repo

# Make sure you are on master
git checkout master

# Make sure you have the latest code from master
git pull

# Create a new branch for the feature (change) you are working on,
# and check that branch out
git checkout -b my-cool-feature

# Time to make your changes
subl .

# Run tests (project specific)

# Check what files have changed
git status

# Add your changes
git add <file one> <file two>

# Look at your changes
git diff HEAD

# Commit your changes, make sure $EDITOR is set to your preferred editor
git commit

# If you have been working on this feature for while, it a good idea to merge
# in a latest changes from origin/master... but don't `git merge`!

# Fetch the latest changes without merging
git fetch

# Rebase your changes *on top of* the latest changes on master
git rebase origin/master

# Push your changes to a branch on your fork
# The syntax below (`my-cool-feature:my-cool-feature`) is
# <local branch name>:<new remote branch name>,
# this will create the remote branch if it doesn't exist
git push github_username my-cool-feature:my-cool-feature

# Now go to https://github.com/westfield/some_repo and click the
# "Compare & pull request" button,
# or run `git pull-request` to use your local $EDITOR
```

See [how to code review](./How_To_Code_Review.md) for a commit message example, and the Git project's [commit guidelines](https://git-scm.com/book/ch5-2.html#Commit-Guidelines) for more explanation.

### Updating an existing PR

Sometimes there is a need to make changes to the code of a PR after it has been opened. To do that, simply commit new changes to your feature branch, and push to the same branch on your fork. GitHub will detect the changes to your fork and automatically update the PR with those changes.

```
# Commit your changes
git commit

# Push changes to the same branch on your fork
git push github_username my-cool-feature:my-cool-feature
```

If you need to rebase *after* you have already pushed to your fork, you need to force push. This is the *only* time you should force push, it is safe because the feature branch on your fork is only used for opening a PR, and you are rebasing on `origin/master`, which is where you will be merging.

If you don't force push, the push will be rejected. *Do not* `git pull` on your branch, it will result if merge commits, and your PR could include code written by other people.

```
# Commit your changes
git commit

# Fetch the latest changes without merging
git fetch

# Rebase your changes *on top of* the latest changes on master
git rebase origin/master

# Force push changes to the same branch on your fork
git push --force github_username my-cool-feature:my-cool-feature
```


### How to write a Pull Request

See [How to code review](./How_To_Code_Review.md).

### How review code

See [How to code review](./How_To_Code_Review.md).

### Merging

Think about the consequences of merging before doing so. Will it immediately deploy, is they desired?

[Squash the commits](https://github.com/blog/2141-squash-your-commits) before merging.

The PR title and body should be used for the merge commit title and body.

### Key concept

#### Never push to `origin`

Only push to your fork. The only way code should git into the main repo is when a PR is merged.

#### Never push to `master`

Push to a feature branch on your fork. Forget about the `master` branch on your fork. It will always be out of date, it is useless. Your local `master` branch should track `origin/master`.

#### Never merge to `master` locally

Don't merge your feature branches into your local `master` branch, and don't try to merge with your remote fork `master`.

Only merged PRs go to master. To update your local `master` branch after a PR is merged, `git checkout master` and `git pull`.