## Handling multiple github accounts

Consider you have two different github acounts and want to commit on either different repos or same repos from a single machine.

| Username      | Email              | Type     |
| ------------- | ------------------ | -------- |
| personal_name | personal@email.com | Personal |
| work_name     | work@email.com     | Work     |

Steps for setting up 2 diffferent accounts in the same machine

1. Open terminal and check for existing ssh keys
   ```
   ls -al ~/.ssh
   ```

    You should have existing keys ending with`.pub`  extension. In case you don't have any we will still create.

2. Generate new keys

   ```
   cd ~/.ssh
   ssh-keygen -t ed25519 -C "personal@email.com" -f "personal_name"
   ssh-keygen -t ed25519 -C "work@email.com" -f "work_name"
   ```
3. Start SSH agent

   ```
   eval `ssh-agent -s`
   ```
4. Add SSH Key to SSH agent

   ```
   ssh-add -K ~/.ssh/personal_name
   ssh-add -K ~/.ssh/work_name
   ```
5. Adding public ssh to github

   ```
   nano ~/.ssh/personal_name.pub
   nano ~/.ssh/work_name.pub
   ```
6. Copy the content from the public key and paste it in Github settings

   ```
   Github -> Settings -> SSH and GPG Keys -> Add new SSH Key

   ```

![1708106755437](image/readme/1708106755437.png)

7. Create config file

   ```
   touch config
   nano config
   ```
8. Add the following content in the config file

   ```
   Host *
     IgnoreUnknown AddKeysToAgent,UseKeychain
     AddKeysToAgent yes
     UseKeychain yes
     IdentityFile ~/.ssh/personal_name
   ```
9. Go to root directory and add the following files

   ```
   cd ~
   touch .gitignore
   touch .gitignore.company
   touch .gitignore.personal
   ```

```
#inside ~/.gitignore.company

[user]
email = work@email.com
name = work_name

[github]
user = "work_name" #should be inisde double quote.

[core]
sshCommand = "ssh -i ~/.ssh/work_name" #should be inisde double quote.

```

```
#inside ~/.gitignore.personal

[user]
email = personal@email.com
name = personal_name

[github]
user = "personal_name" #should be inisde double quote.

[core]
sshCommand = "ssh -i ~/.ssh/personalname" #should be inisde double quote.
```

```
#inside ~/.gitignore

[includeIf "gitdir:~/.Documents/personal/repo/"] # include for all .git projects under Personal/
path = ~/.gitconfig.personal

[includeIf "gitdir::~/.Documents/personal/work/"] # include for all .git projects under Company/
path = ~/.gitconfig.company

[core]
excludesfile = ~/.gitignore      # Ignore .gitignore files valid everywhere
```

10. Clone the repository and then paste them in the corresponding folders to perform commits and pushes as per required accounts


Resources

1. How To Manage Multiple GitHub Accounts In VSCode Using SSH Keys. | One-Time Process [link](https://plainenglish.io/blog/how-to-manage-multiple-github-accounts-in-vscode-using-ssh-keys-one-time-process)
2. Stack Overflow [link](https://stackoverflow.com/questions/3860112/multiple-github-accounts-on-the-same-computer)
