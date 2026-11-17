```
 _____  _  _   
|  __ \(_)| |  
| |  \/ _ | |_ 
| | __ | || __|
| |_\ \| || |_ 
 \____/|_| \__|
```

**Author: Kayky Moreira Praxedes**

# 0. Basic Concepts

## Git and Version Control:

**Git** is a version control system: it records states of a project over time, allowing you to consult what changed, compare versions, and recover previous content. It also allows you to develop changes on independent lines and bring them together later.

The history stays on the computer itself. It is not necessary to have an account on a service or an internet connection to record changes locally.

**GitHub**, **GitLab**, and **Bitbucket** are services that host Git repositories and offer collaboration features. They use Git, but they are not Git; the use of remote repositories is explained in chapter 3.

## Repository and *Commits*:

A **repository** gathers the project files and the data Git uses to track its history. In a common local repository, this data stays in the hidden `.git` folder. Deleting it removes the history and the local Git configurations, even if the project files remain.

A ***commit*** is a record of a version of the project. It associates a logical snapshot of the files with information such as author, date, message, and previous versions. The message describes what was done; the identifier, also called a *hash*, allows locating that record.

The identifier can appear abbreviated, such as `a1b2c3d`. Changing the content or the message of a *commit* produces another *commit*, with another identifier.

## Working Directory, Staging, and History:

Git separates the files being edited from the content that will be recorded:

| Location | Function |
| --- | --- |
| **Working directory** | Project files that you open and modify. |
| **Staging area** (*staging area* or *index*) | State selected to form the next *commit*. |
| **History** | Versions already recorded through the *commits*. |

The common flow consists of editing the files, selecting the changes with `git add`, and recording them with `git commit`. Staging allows you to choose what goes into each record, without needing to include all changes at once.

A new file is **untracked** (*untracked*) until it is added to Git. After that, its changes can be **modified** (*modified*, not yet staged) or **staged** (*staged*, selected for the next record). When the file matches the recorded state, it is **unmodified** (*unmodified*).

The same file can have staged changes and other unstaged changes. For example, if you run `git add` and then edit the file again, the next *commit* will include the version staged before that last edit. Another `git add` is needed to update the staging.

## *Branches* and `HEAD`:

A ***branch*** represents a line of development. It points to a *commit* and advances when new records are made on that line. It is possible to create another *branch* to work on a change without immediately altering the main line.

Normally, **`HEAD`** indicates the selected *branch*; when used as a reference to a version, it represents the current *commit* of that *branch*. `HEAD~1` indicates its predecessor and `HEAD~2`, two steps back. In a merge, this count follows the line of the first parent, not the order of the *commits*' dates.

In the examples, `main` is the name of the main *branch*. This name is a convention and can be different in other projects.

## Installation:

For installation information, consult the [official page](https://git-scm.com/install/).

After installation, open a terminal and run `git --version` to check whether the command is available.

## Initial Configuration:

Git needs a name and an email to identify the authorship of the *commits*. This identification **does not perform a login** on any remote service.

```bash
git config --global user.name "Seu Nome"
git config --global user.email "email@exemplo.com"
git config --global init.defaultBranch main
```

The `--global` option applies the configuration to the current user. To use another identity only in a project, run the command inside its repository with `--local` instead of `--global`. The local configuration prevails over the global one in that project.

`init.defaultBranch` defines the initial name for new repositories; it does not rename the *branches* of existing projects.

- `git config --get {key}` - Queries a configuration, such as `user.email`.

- `git config --list` - Lists the applicable configurations. `--show-origin` also shows which file each one came from.

---

# 1. Basic Commands

## Command Structure:

The basic form is `git {command} {options} {arguments}`. The command chooses the operation; the options, also called *flags*, adjust its behavior; the arguments indicate what it acts on.

> In the syntax models, elements between `{braces}` represent content to be replaced, without keeping the braces. `{options}` indicates optional modifiers; the main description presents the behavior without them, considering the indicated arguments. When an argument can also be omitted, this will be explained in the text. The concrete examples already present the form used in the terminal.

> Usage variations that significantly change the operation appear in subordinate lists, with the complete command. Simple adjustments are explained in the main description. Specific forms can also appear separately in the section corresponding to their purpose.

In `git commit -m "Corrige a leitura do botão"`, `commit` is the operation, `-m` indicates that the message will be provided in the command, and the text in quotes is that message.

## Manuals:

- `git --help` - Shows the most common commands and the global options.

- `git {command} -h` - Shows a summary of the syntax and the options, such as `git restore -h`.

- `git help {options} {command}` - Opens the complete manual. The documentation is also available on the [Git site](https://git-scm.com/docs).
  
    - `git help --all` - Lists all available commands.
    - `git help --guides` - Lists the installed conceptual guides.

## Starting a Repository:

- `git init {options} {directory}` - Starts a repository. Without the directory, it uses the current folder.

> The command prepares the repository, but does not yet record the files: this is done with `add` and `commit`.

## Staging and Recording Changes:

- `git add {options} {paths}` - Stages the current state of the indicated files or directories.

    - `git add .` - Selects the current folder (includes changes from it onward).
    - `git add -A` - Includes changes from the entire repository, including deletions.

- `git commit {options}` - Records the staged content and opens the configured editor to write the message.

    - `git commit -m "{message}"` - Records the staged content, providing the message directly, without opening the editor.
    - `git commit -a -m "{message}"` - Automatically stages modifications and deletions of files already tracked before recording, without adding new untracked files.

    > A new file becomes tracked when it is added with `git add`, even before its first *commit*. Unlike `commit -a`, `git add -A` also stages new untracked files.

```bash
git add arquivo_1.txt diretorio_1/arquivo_2.md diretorio_2/
git commit -m "Atualiza a documentação."
```

If other files are already staged (besides this last `add`), they will also enter the *commit*.

## Ignoring Files:

The **`.gitignore`** file, normally placed at the root of the project, defines patterns of files that should not be included in versioning. It is useful for build files, downloaded dependencies, and local configurations.

```gitignore
# Diretório de compilação
build/

# Arquivos com extensão .o
*.o

# Configuração local
.env
```

The `.gitignore` does not interrupt the tracking of files already added. To stop tracking one of them while keeping the local copy:

- `git rm --cached {file}` - Stages the removal of the file from versioning, without deleting the local copy. `-r` allows applying the operation to a directory.

Then, include the path in `.gitignore` and record both changes in a *commit*. This does not remove the file from previous *commits*. If a credential has already been published, removing it from the project is not enough: it must be revoked on the corresponding service.

## Restoring Files and Removing from Staging:

- `git restore {options} {file}` - Replaces the file content with the version from the staging area, discarding its changes not yet staged.

    - `git restore --staged {file}` - Removes the changes from staging, keeping the file content in the working directory. It is the common way to undo an `add` after the repository already has a *commit*.
    - `git restore --source={version} {file}` - Recovers into the working directory the content the file had in the indicated version. It does not remove *commits* nor change the position of the *branch*.

```bash
git restore --staged main.cpp          # Desfaz a preparação, preservando a edição.
git restore --source=HEAD~1 main.cpp   # Recupera o arquivo da versão anterior.
```

The two lines show independent operations. After recovering an old version, use `add` and `commit` if you want to record that content as a new change.

**Restoring the content discards the replaced edit.** The default source of `restore` is staging; with `--staged`, it is `HEAD`. Therefore, a common `restore` does not necessarily recover the last *commit*: it may recover an edit you had already staged.

## Fixing the Last *Commit*:

- `git commit --amend {options}` - Replaces the last *commit* with another, incorporating the staged state and allowing the message to be edited.

    - `git commit --amend --no-edit` - Keeps the previous message.
    - `git commit --amend -m "{message}"` - Provides a new message directly.

To include a forgotten correction in the last record:

```bash
git add main.cpp
git commit --amend --no-edit
```

To change only the message, use `git commit --amend -m "Nova mensagem"` without other staged changes. If they exist, they will also be included.

The *commit* identifier changes. Use this correction preferably before publishing the record; replacing *commits* that other people already use requires coordination.

## Undoing *Commits*:

There are two distinct situations: moving the *branch* back to a previous version or creating a new record that undoes a change while keeping the existing history.

### Moving the *Branch* Back with `reset`:

- `git reset {options} {version}` - Moves the current *branch* to the indicated version. Without a mode option, it uses `--mixed`: it resets staging to that version, preserving the working directory files. Later *commits* stop being part of the history of that *branch*. If the version is omitted, it uses `HEAD`.

| Option | Staging Area | Working Directory |
| --- | --- | --- |
| `--soft` | Keeps the staged state. | Keeps the files and their edits. |
| `--mixed` | Returns to the state of the indicated version. | Keeps the files and their edits. |
| `--hard` | Returns to the state of the indicated version. | Restores the tracked files to that version, discarding the affected changes. |

To undo the last *commit* while keeping its content staged for a new record:

```bash
git reset --soft HEAD~1
```

If you prefer to choose again what will be staged, use `git reset --mixed HEAD~1`. These commands require a previous *commit* to exist; they do not serve to remove the first record of the repository.

**`--hard` can eliminate work not yet recorded**, including changes prior to the attempt to undo the *commit*. It can also remove untracked files that are in the restoration path. It is not an alternative for those who only want to reorganize the records.

### Undoing a Change with `revert`:

- `git revert {options} {commit}` - Creates a new *commit* that undoes the changes introduced by the indicated record, preserving the existing history. `git revert HEAD` reverts the last common record, without merge.

It is the usual option for undoing changes already shared. Reverting an old *commit* does not necessarily restore the entire project to that time: it undoes the changes of that record, possibly generating conflicts with later changes.

If there are conflicts, edit the files, stage the resolution with `git add`, and proceed with `git revert --continue`. To cancel the revert still in progress, use `git revert --abort`.

> `reset` and `--amend` reorganize the local history; they do not automatically update the server. The `revert` examples deal with common *commits*: reverting a merge *commit* requires choosing the reference line and is not part of this basic flow.

## Recovering a Lost *Commit*:

- `git reflog {options}` - Lists recent movements of `HEAD`, such as *branch* switches and `reset` operations. It helps to locate a *commit* that stopped appearing in the current history.

- `git branch {name} {commit}` - Creates a *branch* pointing to the found record. The operation preserves that point without replacing the current files; the operations with *branches* are detailed in the next chapter.

Look in the `reflog` for the identifier of the desired state and use it in the second command. The entries are local and expire: the `reflog` is not a permanent backup nor does it generally recover edits that were never recorded.

## Consulting Changes and History:

- `git status {options}` - Shows the current *branch*, new files, and staged or not yet staged changes. `--short` presents a compact output.

- `git diff {options}` - Shows the changes of tracked files that have not yet been staged.

    - `git diff --staged` - Shows what was staged in relation to the last *commit*.
    - `git diff {version_1} {version_2}` - Compares the files between two versions. To limit the comparison to a file, add `-- {file}`.

- `git log {options}` - Shows the *commits* of the current *branch*'s history, including identifier, authorship, and message. `--oneline` summarizes each record in one line; `--graph` draws the relationships between the *commits*.

    - `git log --all` - Includes the histories of the other *branches* and other references of the repository, instead of starting only from the current *branch*.

```bash
git diff --staged                  # Confere o conteúdo preparado.
git diff HEAD~1 HEAD -- main.cpp   # Compara o arquivo entre as duas versões.
git log --oneline --graph --all    # Visualiza o histórico das branches.
```

`status` informs **which files** changed; `diff` shows **what changed**; `log` shows **what has already been recorded**. New untracked files appear in `status`, but their content does not appear in the common `git diff`.

> If the output opens in a page viewer, such as `less`, press `q` to exit.

---

# 2. *Branches* and Merging

## How *Branches* Work:

Creating a *branch* does not create another project folder: it creates a reference to a point in the history. Initially, two *branches* can point to the same *commit*. As each one receives new records, their lines of development separate.

When switching *branch*, Git updates the working directory files to the selected version. Changes not yet recorded do not automatically belong to a *branch*: they can accompany the switch when compatible, or prevent the operation when they would be overwritten.

## Managing *Branches*:

- `git branch {options}` - Lists the local *branches* and marks the current one with `*`.

    - `git branch -a` - Includes the references of remote *branches* (chapter 3).
    - `git branch -vv` - Shows the last *commit* and the remote association of each *branch*, when it exists.
    - `git branch {name} {version}` - Creates a *branch* from the indicated version. If no version is passed, it creates from `HEAD`. It does not switch to it (stays on the current one).
    - `git branch -m {old_name} {new_name}` - Renames a *branch*. If no specific *branch* is passed (without `old_name`), it renames the current *branch*.
    - `git branch -d {branch}` - Deletes a *branch* after checking whether its *commits* are integrated into the line it follows or, without this association, into the current line.
    - `git branch -D {branch}` - Forces deletion even without this integration.

- `git switch {options} {branch}` - Switches to an existing *branch*.

    - `git switch -c {name}` - Creates a *branch* at the current point and already switches to it.

Switch to another *branch* before deleting the current one. Deletion removes the name that pointed to the history; it does not undo changes already merged into another *branch*. With `-D`, work not integrated can become difficult to locate.

> In older tutorials, `checkout` appears both for switching *branch* and for restoring files. Here, `switch` and `restore` are used, which separate these purposes.

## Temporarily Saving Changes:

- `git stash {options}` - Saves changes not yet recorded to allow a pause in work, such as switching *branch* to solve another problem, without creating a provisional *commit*. Without a subcommand, it performs the `stash push` operation.

    - `git stash push {options}` - Saves the changes of tracked files and removes those edits from the working directory and staging. `-m "{description}"` identifies the saved content.
    - `git stash push -u` - Also includes untracked files, besides the tracked changes.
    - `git stash list` - Lists the available entries.
    - `git stash apply` - Reapplies the most recent entry without removing it from the list.
    - `git stash pop` - Reapplies the most recent entry and removes it if the application ends without conflicts.
    - `git stash drop` - Deletes the most recent entry without reapplying it.

`apply`, `pop`, and `drop` accept a specific entry, such as `git stash apply 'stash@{1}'`. In that case, the braces are part of the identifier shown by `stash list`.

```bash
git stash push -u -m "Leitura do botão em andamento"
git switch main
# Depois de terminar o outro trabalho, volte à branch original.
git switch leitura-botao
git stash pop
```

The example assumes that the original *branch* is called `leitura-botao`. Reapplying can generate conflicts if the files have changed; in that case, the entry is kept. Files ignored by `.gitignore` are not included by `-u`.

## Merging *Branches*:

**Merging** (*merge*) integrates the work of another *branch* into the current *branch*. Before starting it, record or save pending changes to facilitate checking and a possible cancellation.

- `git merge {options} {branch}` - Integrates the indicated *branch* into the current one.

    - `git merge --ff-only {branch}` - Accepts only a direct advance; refuses the integration if the histories have diverged.
    - `git merge --no-ff {branch}` - Creates a merge *commit* even when a direct advance would be possible, as long as there is something to integrate.

```bash
git switch main
git merge leitura-botao
```

In this example, `main` receives the work of `leitura-botao`. The command does not switch to the indicated *branch* nor delete it.

If `main` did not receive its own *commits* after the separation, it can simply advance to the point of `leitura-botao`: it is the ***fast-forward***. If both received records, Git normally creates a merge *commit* to unite the two histories.

**Divergent histories do not necessarily mean conflict.** Git can combine the changes automatically when they are compatible.

## Resolving Conflicts:

A **conflict** occurs when Git cannot combine the changes alone, such as when two *branches* modify the same excerpt in different ways. The merge remains pending until the final content is defined.

### Choosing the Content and Concluding:

In a textual conflict, the file may present something like this:

```text
<<<<<<< HEAD
intervalo = 500;
=======
intervalo = 1000;
>>>>>>> leitura-botao
```

In this *merge*, the part above `=======` came from the current *branch*; the one below, from the *branch* being incorporated. The markers delimit the conflict and must be removed along with the content that will not be kept.

1. Run `git status` to locate the conflicting files.
2. Edit each file, choosing one of the contents or combining the changes. In the example, if the desired interval is 1000, leave only `intervalo = 1000;` in that excerpt.
3. Save and check the result. Run `git add {file}` to mark the resolution.
4. After resolving all conflicts, run `git commit` to conclude the merge.

The `add` informs that you finished the resolution; it does not check whether the file's logic is correct.

### Keeping One of the Sides of a File:

During a *merge* with a content conflict, it is also possible to choose the entire version of a file:

- `git restore --ours {file}` - Keeps the version from the current *branch*.

- `git restore --theirs {file}` - Keeps the version from the *branch* being incorporated.

They are alternatives: choose only one for the file, check, and run `git add {file}`. They select the entire file from that side, not just a conflicting excerpt, and do not discard the entire other *branch*.

### Canceling the Merge:

- `git merge --abort` - Cancels a merge still in progress and tries to restore the previous state. It does not serve to undo an already concluded merge.

Cancellation may not reconstruct edits that were already pending before the start; therefore, the staging prior to the *merge* is important.

---

# 3. Remote Repositories

## Local and Remote Repository:

A **remote repository** is another copy of the repository with which Git can exchange history, normally hosted on a server. The local repository remains independent: recording a *commit* does not automatically send it to the remote.

GitHub, GitLab, and Bitbucket use the same Git commands for this exchange. The address, permissions, and authentication methods offered by each service change.

A remote is identified locally by a short name and a URL. **`origin`** is the conventional name used in cloning, but it could be another.

## Cloning a Repository:

- `git clone {options} {URL} {directory}` - Creates a local copy of an existing repository, bringing its history and the files of an initial *branch*. It also configures the `origin` remote. If the directory is omitted, it creates a subfolder in the current directory, normally with the repository name.

```bash
git clone https://github.com/usuario/projeto.git
cd projeto
```

> After cloning, it is not necessary to run `git init` or register the same remote again.

## Connecting and Managing Remotes:

If the project started locally, create a repository on the chosen service and register its URL. To publish an existing local history, an initially empty remote avoids creating two independent histories; do not add a README through the service at this stage.

- `git remote {options}` - Lists the names of the configured remotes. `-v` also shows their addresses.

    - `git remote add {name} {URL}` - Registers a remote; it does not send the files yet.
    - `git remote set-url {name} {new_URL}` - Changes the address of an existing remote.
    - `git remote remove {name}` - Removes the local configuration of the remote; it does not delete the hosted repository.

```bash
git remote add origin https://github.com/usuario/projeto.git
```

## Authentication and Credentials:

The name and email configured in `user.name` and `user.email` identify the authorship of the *commits*. **Authentication** determines which account accesses the server and which operations it can perform. Changing the local email does not switch the authenticated account.

Public repositories normally allow reading without authentication. Sending changes and accessing private projects require credentials and permission. The chosen URL determines the method used, mainly HTTPS or SSH.

### HTTPS and Credential Storage:

An HTTPS URL has the form `https://server/user/project.git`. The service can authenticate through the browser or request a credential, such as a **token**: a code that authorizes operations within the granted permissions.

On GitHub, the account's common password does not authenticate Git operations over HTTPS. When entering credentials manually, inform the user and use a *Personal Access Token* in the password field. Other services have their own procedures.

A ***credential helper*** stores or retrieves credentials to avoid typing them on each operation. The mechanisms below are alternatives, not steps to run in sequence.

| Mechanism | Purpose and Basic Use |
| --- | --- |
| `cache` | Stores temporarily in memory, on supported systems, such as Linux. Configure with `git config --global credential.helper 'cache --timeout=3600'` to keep for up to one hour. |
| `store` | Writes to a file without encryption. It is enabled with `git config --global credential.helper store`, but exposes the token to anyone who can read the file; prefer a credential vault. |
| Git Credential Manager | Manages HTTPS authentication and integration with credential storage. Once installed, `git credential-manager configure` configures its use by Git. |
| GitHub CLI | Offers authentication through the browser and can provide the credential to Git. It is specific to GitHub, including its enterprise installations. |

In Git Credential Manager, Windows and macOS have default vaults; on Linux it is necessary to select and configure compatible storage. Consult the official instructions for [installation](https://github.com/git-ecosystem/git-credential-manager#download-and-install), [usage](https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/usage.md), and [storage](https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/credstores.md).

With [GitHub CLI](https://cli.github.com/) installed:

- `gh auth login` - Starts authentication; choose HTTPS and the browser flow for this use.

- `gh auth setup-git` - Configures Git to take advantage of GitHub CLI authentication.

- `gh auth status` - Checks the account and authentication used by the tool.

GitHub CLI tries to use the system vault, but may resort to a file without encryption if it is not available. Check the authentication warnings. Tokens should not be included in the project nor in the remote URL.

### SSH:

In SSH, authentication uses a key pair: the **public** one is registered on the service; the **private** one stays on the computer. A protection password (*passphrase*) can protect the private key file.

To create and load a key, in Bash or Git Bash:

```bash
ssh-keygen -t ed25519 -C "email@exemplo.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

The last two commands assume that you accepted the default path on creation. If a key already exists at that path, do not overwrite it without checking its use. Register on the service the content of `~/.ssh/id_ed25519.pub`; never send the file without `.pub`.

Copy the SSH URL provided by the service and use it in cloning or in the remote configuration. For example, on GitHub:

```bash
ssh -T git@github.com
git remote set-url origin git@github.com:usuario/projeto.git
```

On the first connection, check the server identification with the official documentation before accepting it. The first command tests authentication; the second changes an already existing remote. The `ssh-agent` allows reusing the loaded key during the session.

## Sending and Receiving Changes:

- `git push {options} {remote} {branch}` - Sends the *commits* and tries to update the corresponding *branch* on the remote.

    - `git push -u {remote} {branch}` - Sends the *commits* and configures the tracking of the remote *branch* (*upstream*), normally allowing the names to be omitted in the next `push` and `pull` of that *branch*.

```bash
git push -u origin main
# Depois de criar novos commits, permanecendo na branch main:
git push # No fluxo deste exemplo, equivale a "git push origin main".
```

This first send assumes that there is at least one *commit* and a local *branch* called `main`. It does not include files or edits that have not yet been recorded.

**`main` and `origin/main` are not the same reference.** `main` tracks the local work; `origin/main` records the known position of the *branch* on the server. This local information needs to be updated through communication with the remote.

- `git fetch {options} {remote}` - Obtains the novelties and updates the local remote references, without automatically merging into the working *branch*. If the remote is omitted, it uses the one associated with the current *branch* or, in the absence of that association, `origin`.

    - `git fetch --prune {remote}` - Also cleans references of *branches* that have already been deleted on the server; it does not delete the local working *branches*.

- `git pull {options} {remote} {branch}` - Obtains the novelties and tries to integrate them into the current *branch*. Without integration options, it follows the behavior configured in the environment. With remote tracking configured, the names can be omitted.

    - `git pull --ff-only {remote} {branch}` - Accepts only direct advance; refuses the integration if the histories have diverged.
    - `git pull --no-rebase {remote} {branch}` - Selects merging, as in chapter 2.

To check the novelties before incorporating them, while on `main`:

```bash
git fetch origin
git log --oneline HEAD..origin/main
git diff HEAD origin/main
git merge origin/main
```

In this use of `log`, `HEAD..origin/main` shows the *commits* known on the remote that are not yet part of the current line. The `diff` compares the contents. Integration happens only in the last command.

If you want to obtain and integrate in a single operation, use `git pull --no-rebase origin main`, also while on the *branch* that should receive these changes. The explicit option avoids depending on the integration configuration of that environment.

## Synchronization Problems:

### Send Refused or Divergent Histories:

A send refused with an indication of *non-fast-forward* or need to fetch changes usually means that the server has *commits* that your local line does not yet incorporate. Do not resolve automatically with forced send, as it can discard shared work.

While on the *branch* that should be synchronized, with pending changes recorded or saved, obtain and integrate the remote history:

```bash
git pull --no-rebase origin main
# Se houver conflitos, resolva-os e conclua o commit de mesclagem.
git push origin main
```

Replace `main` with the corresponding *branch*. If Git informs that it needs a strategy to reconcile divergent lines, `--no-rebase` chooses merging. If the previous attempt used `--ff-only`, the refusal indicates that the direct advance is not sufficient.

In case of conflict, follow [Resolving Conflicts](#resolving-conflicts). A refusal caused by lack of permission or *branch* protection requires correcting access or following the project's collaboration flow; a `pull` does not resolve these restrictions.

### Incorrect Remote, *Branch*, or Authentication:

| Situation | What to Check |
| --- | --- |
| `remote origin already exists` | Use `git remote -v`. To correct the address, use `remote set-url`, instead of registering the same name again. |
| Absence of *upstream* | Check the *branch* and, on the first send, use `git push -u origin {branch}`. |
| `src refspec main does not match any` | Check whether a *commit* exists and whether the *branch* is really called `main`. |
| Authentication failure or `Permission denied (publickey)` | Check the URL, the access method, and the credential or key registered on the authorized account. |
| Local changes would be overwritten | Use `git status` and record or save the work with `stash` before updating. |

---

# Sources

- GIT PROJECT. *Pro Git*. 2nd ed., 2014. Available at: [https://git-scm.com/book/pt-br/v2](https://git-scm.com/book/pt-br/v2). Accessed on: Nov. 17, 2026.
- GIT PROJECT. *Git Reference Documentation*. [S. l.], [n. d.]. Available at: [https://git-scm.com/docs](https://git-scm.com/docs). Accessed on: Nov. 17, 2026.
- GIT PROJECT. *git-credential-cache*. [S. l.], [n. d.]. Available at: [https://git-scm.com/docs/git-credential-cache](https://git-scm.com/docs/git-credential-cache). Accessed on: Nov. 17, 2026.
- GIT PROJECT. *git-credential-store*. [S. l.], [n. d.]. Available at: [https://git-scm.com/docs/git-credential-store](https://git-scm.com/docs/git-credential-store). Accessed on: Nov. 17, 2026.
- GIT ECOSYSTEM. *Git Credential Manager*. [S. l.], [n. d.]. Available at: [https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/usage.md](https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/usage.md). Accessed on: Nov. 17, 2026.
- GITHUB. *GitHub CLI Manual*. [S. l.], [n. d.]. Available at: [Authentication](https://cli.github.com/manual/gh_auth_login) and [Git integration](https://cli.github.com/manual/gh_auth_setup-git). Accessed on: Nov. 17, 2026.
- GITHUB. *Managing your personal access tokens*. [S. l.], [n. d.]. Available at: [https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens). Accessed on: Nov. 17, 2026.
- GITHUB. *Generating a new SSH key and adding it to the ssh-agent*. [S. l.], [n. d.]. Available at: [SSH keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent). Accessed on: Nov. 17, 2026.
