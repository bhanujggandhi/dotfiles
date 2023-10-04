# dotfile

1. Prior to the installation make sure you have committed the alias to your ```.bashrc``` or ```.zsh```:

```sh
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
```

2. And that your source repository ignores the folder where you'll clone it, so that you don't create weird recursion problems:
```sh
echo ".cfg" >> .gitignore
```

3. Now clone your dotfiles into a bare repository in a "dot" folder of your $HOME:
```sh
git clone --bare https://github.com/bhanujggandhi/dofiles.git $HOME/.cfg
```

4. Define the alias in the current shell scope:
```sh
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
```

5. Checkout the actual content from the bare repository to your $HOME:
```sh
config checkout
```

*The step above might fail with a message like:*
```
error: The following untracked working tree files would be overwritten by checkout:
    .bashrc
    .gitignore
Please move or remove them before you can switch branches.
Aborting
```

*This is because your $HOME folder might already have some stock configuration files which would be overwritten by Git. The solution is simple: back up the files if you care about them, remove them if you don't care.*
--
I provide you with a possible rough shortcut to move all the offending files automatically to a backup folder:
```sh
mkdir -p .config-backup && \
config checkout 2>&1 | egrep "\s+\." | awk {'print $1'} | \
xargs -I{} mv {} .config-backup/{}
```

6. Re-run the check out if you had problems:
```sh
config checkout
```

7. Set the flag showUntrackedFiles to no on this specific (local) repository:
```sh
config config --local status.showUntrackedFiles no
```
***
8. You're done, from now on you can now type config commands to add and update your dotfiles:
```sh
config status
config add .vimrc
config commit -m "Add vimrc"
config add .bashrc
config commit -m "Add bashrc"
config push
```

[Reference](https://www.atlassian.com/git/tutorials/dotfiles)
