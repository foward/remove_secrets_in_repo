# Remove Secrets or Sensitive data in a Git Repository
## 🦾 Cookbook 🚀

First of all, if you've pushed secrets to a public repository, you should consider them compromised and immediately rotate or invalidate them. Then, there are a few steps you can take to remove these secrets from your git history.

One of the most common and effective ways of doing this is by using the BFG Repo-Cleaner, a simpler, faster alternative to git filter-branch specifically designed for cleansing bad data out of your Git repository history.

Here are the steps to remove the secrets:

Before you start, make sure to backup your repository. The following operations will modify your git history and can be destructive if not done correctly.

Clone your repository, if you haven't already.

```bash
git clone --mirror git://example.com/some-big-repo.git
```
This is a bare clone, which means your normal files won't be visible, but it is a full copy of the Git database of your repository, making all your commits and branches available for the cleaning process.

Download and install the BFG Repo-Cleaner:

```bash
wget https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar
alias bfg='java -jar bfg-1.14.0.jar'
```
Run the BFG to clean your repository:

BFG will replace the text you want to remove with "REMOVED" and will also take care of all the corresponding check-in comments.

- Remove Files
```bash
bfg --delete-files file_with_sensitive_data.extension your-repo.git
```

- Remove content in the repo
```bash
echo 'secret_token_1' >> passwords.txt
echo 'secret_token_2' >> passwords.txt
echo 'secret_token_3' >> passwords.txt

bfg --replace-text passwords.txt some-big-repo.git
```

Once you're happy with the updates, strip out the unwanted dirty data:

```bash
cd your-repo.git
git reflog expire --expire=now --all && git gc --prune=now --aggressive
```

```bash
git push --force
```
Remember, the BFG won't modify the contents of your latest commit on your master (or HEAD), because it assumes that it’s been fully updated and any secret key material has been removed from it already. If not, remove any secrets from the latest commit manually.

Also, anyone who has pulled/cloned your repository will need to re-clone the repository or do a __´´´git pull --rebase´´´__ to avoid merging in the dirty history again.

## Troubleshooting Git Push

- __fatal: options '--mirror' and '--tags' cannot be used together__ :
   - ```bash git config --unset remote.origin.mirror```
   - and try again :  ```bash git push --force ```
   - if is not working, try this:  ```bash git push --set-upstream origin main ```
