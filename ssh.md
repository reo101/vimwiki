# Setting up ssh

## Downloading openssh
```bash
sudo pacman -S openssh
```
## Generating ssh keys

- Generate keys with this command (`-t rsa` can be omitted, because RSA is the default option)
```bash
ssh-keygen -t rsa -b 4096
```
- Your new keys will now be in `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub` respectively
- Now you can copy your key to another machine by running
```bash
ssh-copy-id reo101@192.168.0.101 # or just the ip address if the username matches yours
```
## Adding your new ssh key to github.com

- Firstly open the settings menu

![Open Settings](./githubOpenSettings.png)
- Then open the SSH and GPG Keys menu

![Open SSH Menu](./githubOpenSSHMenu.png)
- Click on New Key

![Open New SHH Key Menu](./githubOpenNewSSHKeyMenu.png)
- Enter a key name (identifier) and the key itself

![Add SSH Key](./githubAddSSHKey.png)
- Now enter you github password

![Confirm Password](./githubConfirmPassword.png)

- We're done! You can now use `ssh` to communicate with github
