# Managing dotfiles with [git](git)
## How to backup your dotfiles with git
- Firstly create a folder for the `.git` files
```bash
git init --bare $HOME/dotfiles
```
- Secondly make an alias for easily managing the _hidden_ repo and add it to the shell profile
```bash
echo "alias dfr='/usr/bin/git --git-dir=$HOME/dotfiles/ --work-tree=$HOME'" >> $HOME/.zsh_profile
exec zsh # to refresh the aliases
```
- Thirdly set up the repo to ignore untracked files because we only want it to track a handful of dotfiles
```bash
dfr config --local status.showUntrackedFiles no
```
- You can now start backing up dotfiles (remember to use `dfr` instead of `git`)

### Adding normal files
```bash
dfr add .config/nvim/init.vim
dfr commit -m "Add nvim config"
dfr add .zsh*
dfr commit -m "Add zsh config"
dfr push
```
- ***IMPORTANT NOTE*** On your first push from fresh clone use
```bash
dfr push -u origin master
```
* This is needed because we're working with a bare repo and you have to explicitly say what branch to track (local to track remote)
* After that you can just use `dfr push` to push
### Adding submodules
```bash
dfr submodule add git@github.com:reo101/vimwiki.git vimwiki
```
## How to restore your dotfiles with git
- Prior to cloning the repo define the alias in the current shell scope (terminal) and add the dotfiles folder to the global `.gitignore` (to prevent weird recursion when cloning)
```bash
alias dfr='/usr/bin/git --git-dir=$HOME/dotfiles/ --work-tree=$HOME'
echo "dotfiles" >> .gitignore
```
- It's now time to clone the actual repo (be sure to set up [ssh](ssh) keys beforehand)
```bash
git clone --bare git@github.com:reo101/dotfiles.git $HOME/dotfiles
dfr submodule update --init --recursive
```
- We can now try to `checkout` the dotfiles from the repo
```bash
dfr checkout
```
- Note that the above command might spit out an error like this
```
error: The following untracked working tree files would be overwritten by checkout:
    .zshrc
    .gitignore
Please move or remove them before you can switch branches.
Aborting
```
- This is because we might already haves the same dotfiles in our `$HOME` directory. We can easily backup the current ones so we can safely `checkout` without overriding anything ( **note** - you way need to run this a few times because the `checkout` commnd truncates the list of conflicting items at one point )
```bash
mkdir .old-dotfiles &&  \ # makes directory for the backup
dfr checkout 2>&1 | egrep "\s+\." | awk {'print $1'} | \ # grabs all files from the error message
xargs -I{} sh -c 'path={} && \
mkdir -p .old-dotfiles/${path%/*} && \
mv {} .old-dotfiles/{}' # creates necessary subfolders and copies old dotfiles over
```
- We can now retry to `checkout` and update submodules
```bash
dfr checkout
dfr submodule update --init --recursive
```
- And finally hide untracked files again
```bash
dfr config --local status.showUntrackedFiles no
```
- P.S. You can update all submodules to their own master by running
```bash
dfr submodule update --remote --merge
```
