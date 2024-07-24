# Marketing Web Modules
## Code Contribution Guidelines
These guidelines exist to help maintain a high level of quality and consistency. Adhering to the guidelines and considerations as much as you can will help make the process of contribution quick and easy.

### Branch formatting
Branches should be formatted following the standard set in the [Git Branches](/README.md#git-branches). Mostly, you will follow the jira/`[ldap]/[jira-ticket]` format.

- Make sure to follow this format: `jira/ldap/WORDPRESS-XXXX`
- Always make sure to branch off of `development`
- Always make sure to `pull` before creating your branch

#### Example workflow
```shell
git checkout development
git pull
git checkout -b jira/ldap/WORDPRESS-XXXX
```

### Committing updates
Contributors should try to commit working code when possible, and should commit often and with detailed and helpful commit messages that detail out the work being submitted.

- Commits are prefixed with the JIRA ticket number (Ex. `git commit -am "WORDPRESS-XXXX: detailed commit message"`)
- Commits should include code that has been linted and formatted. Make sure you have the appropriate code sniffers installed and settings configured for whichever IDE you are using ([More info](/docs/code-standards.md)).

#### Committing WIP code
If portions of code need to be committed before it is considered "working", then prefix your commit message with "WIP" (Ex. `git commit -am "WORDPRESS-XXXX: WIP - detailed commit message describing a portion of what was done"`).