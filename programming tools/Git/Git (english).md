```
 _____  _  _   
|  __ \(_)| |  
| |  \/ _ | |_ 
| | __ | || __|
| |_\ \| || |_ 
 \____/|_| \__|
```

# 00. Basic Concepts

## Git and GitHub:

**Git** is a version control system that lets you compare changes, recover previous states, and develop features along independent lines. Each regular copy of a repository contains its own history and is maintained locally.

**GitHub** is a service that hosts Git repositories and adds collaboration features such as issues, pull requests, and access control. Git and GitHub are not the same tool: you can use Git without GitHub and host Git repositories on other services such as GitLab and Bitbucket.

## Repository:

A **repository** is the collection formed by the project and the data Git uses to maintain its history. This data is stored in the hidden `.git` directory, which contains objects, references, and local settings. Deleting `.git` does not delete the project files, but it removes the Git history and configuration.

Git records each version as a **snapshot** (a logical picture of the project's staged state). A **commit** references one of these snapshots and contains metadata such as the author, date, message, and previous commit. Its identifier is calculated from the stored content and metadata.
> Changing a commit produces a different identifier.

## File States:

A file can be in the following main states:

- **Untracked** (untracked): it exists in the working directory but is not yet part of Git's history.
- **Unmodified** (unmodified): it is tracked and matches the recorded version.
- **Modified** (modified): it is tracked, but its content in the working directory differs from the staged or recorded state.
- **Staged** (staged): its current state has been copied to the staging area and will be included in the next commit.
- **Committed** (committed): its state is stored in a commit in the local repository.

## References and `HEAD`:

A **branch** is a movable reference to a commit. Normally, `HEAD` points to the currently selected branch, and that branch points to its latest commit. As new commits are created, the branch reference moves forward.

`HEAD~1` represents the first parent of the commit indicated by `HEAD`; `HEAD~2` represents the first parent of that parent, and so on. In histories with merges, this notation repeatedly follows the first parent, which is not necessarily the second most recent commit by date.

When `HEAD` points directly to a commit instead of a branch, the repository is in a **`detached HEAD`** state. You can inspect and even create commits in this state, but you should create a branch to preserve this new line of development easily.

## Command Structure:

The general form of the interface is `git {global_options} {command} {options} {arguments}` (the notation is instructional and simplified; it is not fully standardized).

Git also allows you to create custom aliases with `git config set --global alias.{name} "{expansion}"`. The expansion is written without the initial `git`.
> These aliases are only user-configured abbreviations and should not be confused with separate commands that have partially similar features.

## Manuals:

- `git --help` - Shows the most common commands and global options.

- `git {command} -h` - Shows a short summary of the command's syntax and options.

- `git help {command}` - Opens the complete manual for the command. `git {command} --help` is an equivalent form.

- `git help --all` - Lists all available commands.

- `git help --guides` - Lists the installed conceptual guides.

You can also consult the [Git Reference Documentation](https://git-scm.com/docs) and the book [Pro Git](https://git-scm.com/book/en/v2).

---

# 01. Configuration and Identity

## Configuration Scopes:

Git settings can be applied at different scopes. A more specific setting normally overrides an equivalent setting from a broader scope.

- **System** (`--system`): applies to the users of that system installation.
- **Global** (`--global`): applies to the current user and is normally stored in `~/.gitconfig` or `~/.config/git/config`.
- **Local** (`--local`): applies only to the current repository and is stored in `.git/config`; this is the default scope when none is specified inside a repository.

- `git config set {scope} {name} {value}` - Defines a setting. Some of the most important keys are:

  - `user.name` - Name normally recorded in the user's commits.
  - `user.email` - Email address normally recorded in the user's commits.
  - `init.defaultBranch` - Initial branch name for new repositories, such as `main`.

- `git config get {options} {name}` - Shows the effective value of a specific setting (settings such as `user.name` defined by `set`).

- `git config list {options}` - Lists the settings that apply to the current context. The `--show-origin` option also shows the files from which they were read.

`user.name` and `user.email` define the **identity recorded in the commit**; they do not authenticate the user on GitHub. The account used to send the commit to a server may differ from the name and email address recorded in it.

---

# 02. Versioning and Files

## Initialization:

- `git init {options} [{directory}]` - Initializes a new Git repository. When a directory is omitted, the repository is initialized in the current directory.

- `git -C {directory} {command}` - Runs a command as if Git had been started in that directory.

Most commands search for `.git` in the current directory and its parent directories. Therefore, they can usually be run from a project subdirectory without the terminal being exactly at the repository root.

## Inspecting the State:

- `git status {options}` - Summarizes the differences between `HEAD`, the Index, and the working directory, and also indicates untracked files and the current branch. The `--short` option shows the state in a compact format.

- `git diff {options} [{references}] [--] [{paths}]` - Shows differences between project states. With no references, it compares the working directory with the Index; `--staged` compares the Index with `HEAD`; one reference compares the working directory with it; and two references compare the indicated states. `--` can separate references from paths.

## Staging and Recording:

- `git add {options} {paths}` - Stages the current state of the selected paths in the Index. `.` selects changes from the current directory.

- `git commit {options}` - Creates a commit with the state currently in the Index. `-m "{message}"` supplies the message on the command line, while `-a` automatically stages modifications and deletions of files that are already tracked, but does not include new files.

- `git log {options} [{references}] [--] [{paths}]` - Shows the history. The `--oneline`, `--graph`, `--decorate`, and `--all` options produce a compact view of the relationships among branches and other references.

A commit includes only the staged state. Untracked files or changes made after the last `git add` are not included automatically in the commit.

## Excluding Files:

The `.gitignore` file contains patterns for **untracked** files that Git should ignore, such as downloaded dependencies, build files, and local credentials. Examples:

```gitignore
# Directory at any level
node_modules/

# Files with this extension
*.o

# Local environment files
*.env

# Exception to a previous pattern
!config.example.env
```

`.gitignore` does not stop tracking a file that has already been included in commits. To keep it in the working directory but remove it from the Index, you can use `git rm --cached {file}` and then record that removal in a commit.
> Secrets that have already been published remain in the history and must be revoked, even after the file is removed.

## Restoration:

- `git restore {options} {paths}` - Restores the selected paths. By default, it updates the working directory from the Index, discarding changes that have not yet been staged.

  - `--staged` - Updates the Index from `HEAD`, removing the changes from the staging area without discarding them from the working directory.
  - `--source={commit}` - Selects another reference as the restoration source.

Because the Index usually matches `HEAD` before `git add`, `git restore {file}` often appears to restore the last commit. However, the default source is the Index.

---

# 03. History and Recovery

## Changing the Last Commit:

The `--amend` option of `git commit` replaces the last commit using the currently staged content. It does not edit the existing commit: it creates another commit (it still needs `-m "{message}"` to provide a new message) and moves the branch to it.
> If the Index contains changes, they will also be incorporated.

## `reset`:

- `git reset {options} {commit}` - Moves `HEAD` and, normally, the current branch to another commit. The selected mode determines what also happens to the Index and the working directory:

  - `--soft` - Moves the branch but keeps the content of the undone commits in the Index and working directory.
  - `--mixed` - Moves the branch and resets the Index, but keeps the changes in the working directory. This is the default mode.
  - `--hard` - Moves the branch and makes the Index and tracked files in the working directory match the indicated commit, discarding the affected tracked changes.
  > `reset --hard` does not normally remove untracked files that do not interfere with the restoration, but it may delete untracked files or directories that are in the path of tracked files that must be written.

## Reverting:

- `git revert {options} {commits}` - Creates new commits that apply the inverse of the changes introduced by the indicated commits.
> The `--no-commit` option applies the reversions to the Index and the working directory without recording them immediately.

`revert` preserves the existing history and is therefore usually the safest option for undoing changes that have already been published. The result may cause conflicts if the project has changed since the reverted commit.

## Recovery with `reflog`:

- `git reflog [{subcommand}] {options} [{reference}]` - Queries or manages local records of reference movements. With no subcommand, it shows the history of `HEAD`; `show` queries a specific reference.

- `git branch {new_branch} {commit}` - Creates a branch that points to a commit recovered through the `reflog`.

The `reflog` can help locate commits that became unreachable after a `reset`, `rebase`, or branch deletion. It is local and its entries expire, so it should not be treated as a permanent backup.

---

# 04. Branches and Integration

## Branches:

When initializing a repository, Git defines the name of an initial branch with no commits yet, called an **unborn branch**. The branch begins pointing to a commit when the first one is created; before then, many commands that need a commit as a reference cannot operate normally.

- `git branch {options} [{name} [{start_point}]]` - Lists, creates, renames, or deletes branches, depending on the arguments and options. With no arguments, it lists local branches and uses `*` to indicate the current one. Some of the most important options are:

  - `-a` - Includes remote branch references in the list.
  - `-vv` - Also shows the latest commit and the upstream of each branch, when configured.
  - `-m [{old_name}] {new_name}` - Renames the current branch or the specified branch.
  - `-d {branches}` - Deletes branches that have already been integrated into their upstream, or, if none exists, into the history reachable from `HEAD`.
  - `-D {branches}` - Forces deletion of the references, even without integration.

- `git switch {options} {branch}` - Switches to the indicated branch and updates the working directory. The `-c {new_branch}` option creates a branch from the current position and immediately switches to it.

Deleting a branch removes its reference, but not necessarily its commits immediately. Even so, work that has not been integrated may become difficult to locate.

## Merge:

- `git merge {options} {commits}` - Integrates into the current branch the histories reachable from the indicated commits or branches. `--ff-only` accepts only a fast-forward, while `--no-ff` forces the creation of a merge commit when integration is possible.

When the current branch can simply move forward to the same point as the other branch, a **fast-forward** occurs. When the lines of development have diverged, Git normally creates a merge commit with more than one parent, provided that it can combine the changes.

## Conflicts:

A **conflict** occurs when Git cannot automatically decide how to combine changes. It marks the conflicting sections in the files and pauses the integration so the user can resolve the content.

The basic workflow for completing a merge with conflicts is:

1. Run `git status` to identify the conflicting files.
2. Edit the files and remove the conflict markers, keeping the correct content.
3. Run `git add {file}` to mark each conflict as resolved.
4. Run `git commit` to complete the merge when Git does not complete it automatically.

The `--abort` option of `git merge` attempts to return to the state before the integration began.

---

# 05. Remote Repositories

## Concepts:

A **remote** is a repository accessible through a URL and registered locally with a short name. `origin` is only the conventional name assigned to the remote created by `git clone`; it is not a required word and does not necessarily represent the project's original repository.

A reference such as `origin/main` is a **remote-tracking branch**: a local record of the observed position of the `main` branch on the `origin` remote during the last communication. It is not the same reference as the local `main` branch.

A local branch can have an **upstream branch** configured, for example, `main` tracking `origin/main`. This relationship allows commands such as `git pull` and `git push` to determine their default targets and allows `git status` to compare the two branches.

## Remote Configuration:

- `git remote [{subcommand}] {options} {arguments}` - Queries and manages the configured remotes. With no subcommand, it lists their names. The main operations are:

  - `-v` - Includes fetch and push URLs in the list.
  - `add {name} {URL}` - Registers a remote repository with the chosen name.
  - `set-url {name} {new_URL}` - Changes a remote's URL.
  - `rename {old_name} {new_name}` - Renames a remote.
  - `remove {name}` - Removes its local configuration and associated tracking references; it does not delete the hosted repository.

The `--set-upstream-to={remote}/{remote_branch}` option of `git branch` explicitly defines the upstream of the current branch or of a local branch supplied as an argument. The `-vv` option presented earlier lets you check this association.

## Transfer and Integration:

- `git clone {options} {URL} [{directory}]` - Creates a local copy of the repository, including its history, configures a remote normally called `origin`, and checks out a working version. Important options: `--branch {branch}` selects the initial branch, `--depth {n}` limits the depth of the retrieved history, and `--recurse-submodules` initializes the configured submodules.

- `git fetch {options} [{remote} [{refspecs}]]` - Downloads objects and updates references without automatically integrating the changes into the local branch. `--all` queries all remotes, `--prune` removes tracking references that no longer exist on the remote, and `--tags` fetches all tags.

- `git pull {options} [{remote} [{refspecs}]]` - First runs a `fetch` and then integrates the retrieved content into the current branch. `--rebase` uses rebase, `--no-rebase` uses merge, and `--ff-only` accepts only a fast-forward.
`fetch` allows you to inspect the changes before integration, for example, with `git log HEAD..origin/main` and `git diff HEAD..origin/main`. It does not ask for confirmation to merge because it does not perform the merge. In contrast, `pull` can immediately change the branch and working directory.

- `git push {options} [{remote} [{refspecs}]]` - Attempts to update references on the remote with local content. `-u` or `--set-upstream` also configures the upstream, `--tags` sends all tags, and `--force-with-lease` makes a forced update conditional on the expected remote state.
> It may be rejected when the remote contains commits that the local update would discard. In that case, you normally need to retrieve and integrate the remote work before trying again. A forced push rewrites the remote history and should not be used as an automatic solution. `--force-with-lease` adds a safety check, but still requires care and coordination.

---

# 06. Authentication and Credentials

## Identity and Authentication:

There are two independent mechanisms:

- **Commit identity**: `user.name` and `user.email` are metadata recorded in the history.
- **Remote authentication**: proves to GitHub or another server which account is performing an operation and which resources it can access.

Purely local operations such as `add`, `commit`, `branch`, and `log` do not require signing in to GitHub. Reading public repositories may also require no authentication, but pushing changes and accessing private repositories normally require an authorized credential. The method used depends on the remote URL protocol, mainly **HTTPS** or **SSH**.

## HTTPS and Token:

For Git operations over HTTPS, GitHub does not accept the regular account password as a credential. When authentication is entered manually, you use the username and a **Personal Access Token (PAT)** in the password field. The token is the part that actually authenticates the operation.

A PAT authorizes actions on behalf of the user, limited both by the access the account already has and by the permissions granted to the token. GitHub recommends **fine-grained** tokens when they meet the use case because they can be limited to an owner, specific repositories, specific permissions, and an expiration date. Treat the token like a password: do not put it in the repository, in the remote URL, in commands that remain in the shell history, or in messages.

When using a PAT manually:

1. Create the token in the GitHub account's developer settings.
2. Select only the necessary repositories and permissions, and set an appropriate expiration date.
3. Run the Git operation using an HTTPS URL.
4. Enter the username when prompted and insert the PAT in the password field.

You do not need to generate a token for every `push`. A **credential helper** can retrieve a previously authorized credential. The general configuration is `git config set --global credential.helper {helper}`. Basic helpers include:

- `cache` - Keeps the credential temporarily in memory; it is requested again after expiration or after the cache service stops.
- `store` - Saves the credential persistently in a file **without encryption**, normally in `~/.git-credentials`. It prevents new prompts, but is not recommended for important tokens or shared computers.

For persistent storage, prefer GitHub CLI, Git Credential Manager, or a helper integrated with the system credential vault, such as GNOME Keyring or KDE Wallet when integration is available. Secure storage depends on the installed tools and the system session.

## GitHub CLI:

GitHub CLI provides a practical browser-based authentication flow and can configure Git to reuse the credential:

- `gh auth login` - Authenticates GitHub CLI. In the interactive flow, you can choose `HTTPS`, browser authentication, and allow the tool to configure credentials for Git.

- `gh auth status` - Shows the authentication state and active account.

- `gh auth setup-git` - Configures GitHub CLI as a credential helper for the hosts on which it is authenticated.

- `gh auth logout` - Removes the locally stored authentication for the chosen account from GitHub CLI; it does not necessarily revoke the token on the server.

When secure storage is available, GitHub CLI can store the token obtained through the web flow in it. If it is not available, the tool itself warns that it may resort to less secure storage; therefore, read the warnings displayed during sign-in.

## SSH:

With the SSH protocol, authentication uses a key pair. The **public key** is registered on GitHub, while the **private key** remains on the computer and must never be sent to third parties. The server verifies that the client holds the corresponding private key without needing to receive it.

A common workflow is:

```bash
$ ssh-keygen -t ed25519 -C "email@example.com"
$ eval "$(ssh-agent -s)"
$ ssh-add ~/.ssh/id_ed25519
```

Then, the content of `~/.ssh/id_ed25519.pub` must be registered in the account's SSH key settings. You can test the connection with:

```bash
$ ssh -T git@github.com
```

To use SSH for a remote that is already configured with HTTPS:

```bash
$ git remote set-url origin git@github.com:user/repository.git
```

A passphrase protects the private key if the file is copied. `ssh-agent` keeps the key unlocked during a session, and integration between the graphical environment and the system vault can securely persist this unlocked state across sessions, depending on the system configuration.

---

# 07. Additional Tools

## `stash`:

The **stash** temporarily stores changes that have not yet been recorded in a commit and restores a cleaner working directory, which is useful for switching contexts without creating a temporary commit.

- `git stash push {options}` - Temporarily stores tracked changes. `-m "{description}"` assigns a message and `-u` also includes untracked files.

- `git stash list` - Lists the existing stashes.

- `git stash apply {stash}` - Reapplies a stash without removing it from the list.

- `git stash pop` - Reapplies the most recent stash and attempts to remove it from the list.

- `git stash drop {stash}` - Removes a specific entry.

## Tags:

A **tag** assigns a stable name to a point in the history and is often used to mark versions such as `v1.0.0`. Unlike a branch, it does not move forward automatically with new commits.

- `git tag {options} [{name} [{commit}]]` - Lists or creates tags. With no arguments, it lists local tags; `-a` creates an annotated tag and `-m "{message}"` records its message. When the commit is omitted, the tag points to `HEAD`.

Tags are not necessarily sent by a regular `push`. A specific tag can be sent as a refspec, for example, `git push {remote} {tag}`; the `--tags` option sends all local tags.

## Rebase:

**Rebase** reapplies a sequence of commits onto a new base. For example, while on a feature branch, `git rebase main` reapplies that branch's unique commits onto the current position of `main`, producing a linear history.

Because the reapplied commits receive new identifiers, rebase rewrites that part of the history. It is useful for organizing local work, but it should not be applied without coordination to published commits that other people already use.

- `git rebase {options} [{new_base}]` - Reapplies the commits from the current branch onto the indicated base. During an interruption caused by conflicts, `--continue` proceeds after the files have been resolved and staged, while `--abort` cancels the process and attempts to restore the previous state.

---

# Sources:

- CHACON, Scott; STRAUB, Ben. *Pro Git*. 2nd ed. New York: Apress, 2014. Available at: <https://git-scm.com/book/en/v2>. Accessed on: Sep. 15, 2026.

- GIT PROJECT. *Git Reference Documentation*. Version 2.55.0. [N.p.]: Software Freedom Conservancy, 2026. Available at: <https://git-scm.com/docs>. Accessed on: Sep. 15, 2026.

- GITHUB. *About authentication to GitHub*. [N.p.]: GitHub, [n.d.]. Available at: <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github>. Accessed on: Sep. 15, 2026.

- GITHUB. *Caching your GitHub credentials in Git*. [N.p.]: GitHub, [n.d.]. Available at: <https://docs.github.com/en/get-started/git-basics/caching-your-github-credentials-in-git>. Accessed on: Sep. 15, 2026.

- GITHUB. *Managing your personal access tokens*. [N.p.]: GitHub, [n.d.]. Available at: <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens>. Accessed on: Sep. 15, 2026.

- GITHUB. *Generating a new SSH key and adding it to the ssh-agent*. [N.p.]: GitHub, [n.d.]. Available at: <https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent>. Accessed on: Sep. 15, 2026.

- GITHUB. *Testing your SSH connection*. [N.p.]: GitHub, [n.d.]. Available at: <https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection>. Accessed on: Sep. 15, 2026.

- GITHUB. *GitHub CLI Manual: gh auth*. [N.p.]: GitHub, [n.d.]. Available at: <https://cli.github.com/manual/gh_auth>. Accessed on: Sep. 15, 2026.
