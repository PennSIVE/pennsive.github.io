---
author: "Büşra Tanrıverdi, Ph.D."
date: 2026-08-25
---

# Git & GitHub Basics

## Learning objectives

This short tutorial is here to help you get started with (or refresh the basics of) Git/GitHub. First, here is a list of most useful commands, which we'll revisit throughout:

| Command | Purpose |
|----------|---------|
| `git status` | Show repository status |
| `git add .` | Stage all files |
| `git commit -m "message"` | Save a snapshot |
| `git push` | Upload changes |
| `git pull` | Download changes |
| `git clone URL` | Download a repository |
| `git remote -v` | Show connected repositories |
| `git log` | View commit history |

## What are Git and GitHub?

**Git** is a version control system that tracks changes to files over time.

It allows you to:

- recover previous versions
- collaborate safely with others
- keep a complete history of your project

**GitHub** is an online hosting service for Git repositories.

Think of Git as the software that manages version history, and GitHub as the website where repositories are stored and shared 

  - similar to a Word document that is stored on OneDrive.

### Local and remote repositories

When you clone a project, you create a **local repository** on your computer. The version stored on GitHub is the **remote repository**.

You edit files in the local repository. You then use `git push` to send committed changes to GitHub and `git pull` to bring newer changes from GitHub back to your computer.

### The basic workflow

```text
Pull → Edit → Stage → Commit → Push
```
In most projects you will repeat this cycle many times:

1. Pull the latest changes.
2. Edit files locally.
3. Stage the changes with `git add`.
4. Save a snapshot with `git commit`.
5. Upload the commit with `git push`.

---

Now, let's get started with an example.

## Create a GitHub account

Create a free account at: [https://github.com](https://github.com)

!!! tip

    If you want to host a personal website with GitHub Pages in the future, consider choosing a professional username (for example, your first and last name). This allows you to host your personal site at username.github.io (e.g., busratanriverdi.github.io).

    Check out the Resources section at the end for the links to previous PennSIVE workshops on hosting your personal website via GitHub Pages.


## Install Git

Check whether Git is already installed:

```bash
git --version
```

If Git is not installed, download it from: [https://git-scm.com/downloads](https://git-scm.com/downloads)

## Configure Git

Tell Git who you are:

```bash
git config --global user.name "Busra Tanriverdi"
git config --global user.email "busrasemail@gmail.com"
```

Verify:

```bash
git config --list
```

---

## Set up SSH authentication

Generate a new SSH key pair:

```bash
ssh-keygen -t rsa -b 4096 -C "busrasemail@gmail.com"
```
Accept the default location by pressing **Enter**.

!!! tip

    | Part | Meaning |
    |------|---------|
    | `ssh-keygen` | The program that creates SSH keys. |
    | `-t` | Specifies the **type** of key to generate. |
    | `rsa` | Generates an *RSA* key pair. (Note, GitHub more recently recommends *ed25919* for it is secure, fast, and produces shorter keys than older RSA keys.) |
    | `-b` | Specifies the number of **bits** in the key. |
    | `-C` | Adds a **comment** to the public key, usually your email address, making it easier to identify the key later. The comment does **not** affect security. |
    | `"busrasemail@gmail.com"` | The comment that will be attached to the key. GitHub recommends using the email associated with your GitHub account. |


    This creates two files:

    - `~/.ssh/id_rsa` — your **private key** (never share this file)
    - `~/.ssh/id_rsa.pub` — your **public key** (this is the file you'll upload to GitHub)


### Start the SSH agent

The SSH agent securely stores your private key so Git can use it when connecting to GitHub.

Start the SSH agent and add your private key:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

You should see a message similar to:

```text
Identity added: /Users/username/.ssh/id_rsa
```

### Add your public key to GitHub

Display the public key:

```bash
cat ~/.ssh/id_rsa.pub
```
Copy the entire output and add it to:

**GitHub → Settings → SSH and GPG keys → New SSH key**

### Test the connection:

Finally, verify that GitHub recognizes your key:

```bash
ssh -T git@github.com
```

A successful connection will return something similar to:

```text
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

Note: Although the message says "GitHub does not provide shell access," this is the expected response. It means your SSH authentication is working correctly, and you are now ready to clone, pull, and push repositories.

---

## Example Workflows with Clone or Fork

You can clone and work on a repository directly, if option enabled by repository creaters, or you are the creator. Alternatively, you can fork a copy of a repository to your own GitHub account and work with that copy (most likely when you are using someone else's pipeline). Below we cover the basic workflow for both options.


=== "Cloning a repository"

    Here is the basic workflow for cloning: 

    ```bash
    git clone git@github.com:USERNAME/my_repo.git

    # Check what is in the repository you cloned:
    cd my_repo
    ls


    # Check repository status
    # Note: Git reports whether files are unchanged, modified, or untracked.
    git status


    # Make your first change & check status again to see your changes:
    touch notes.txt
    echo "Hello GitHub!" >> notes.txt
    git status


    # Stage files
    git add notes.txt # stage 1 specific file
    git add . # alternatively, you can stage everything all at once


    # Commit changes
    # A commit is a snapshot of your project. Try to make your commit text (following the `-m` flag) brief but explanatory to help your future self!
    git commit -m "Add notes file"

    # Push to GitHub
    # After this, your GitHub repository will be synchronized with your local copy.
    git push


    # Pull updates, ie download changes from GitHub directly:
    git pull
    ```

=== "Forking a repository"
    Sometimes you do not have permission to push directly to a repository. Instead, create your own copy (**fork**) first.

    For example, you will likely need to fork the [PennSIVE pipelines](https://github.com/PennSIVE/PennSIVE_neuro_pip) at some point:

    First, open the repository on GitHub and click **Fork**. A copy will appear under your own GitHub account. Then, you can follow the steps below to check & edit your copy. 

    ```bash
    # Clone your fork:
    git clone git@github.com:YOUR_USERNAME/PennSIVE_neuro_pip.git
    cd PennSIVE_neuro_pip

    # Check your remote:
    git remote -v

    # Optional but recommended: Connect the original PennSIVE repository as the upstream repository & verify:
    git remote add upstream https://github.com/PennSIVE/PennSIVE_neuro_pip.git
    git remote -v

    ```

    Note, once you complete upstream, you should see both:

    - `origin`: your fork under your GitHub account
    - `upstream`: the original PennSIVE repository

    !!! note "Keep your fork synchronized"
        As the original PennSIVE repository changes over time, you may want to update your own fork with those latest changes.

        Run the following commands:

        ```bash
        git fetch upstream
        git merge upstream/main
        git push origin main
        ```

        What each command does:

        | Command | What it does |
        |---------|--------------|
        | `git fetch upstream` | Downloads the latest changes from the original PennSIVE repository (**upstream**) without changing any of your local files. |
        | `git merge upstream/main` | Merges those downloaded changes into your current local branch (usually `main`). |
        | `git push origin main` | Uploads your newly updated **main** branch to the **main** branch of your GitHub fork (**origin**), keeping your online fork synchronized with the original repository. |

        After these three commands, both your **local repository** and your **GitHub fork** now contain the latest updates from the original PennSIVE repository.


---
## Resources

The topics below are useful next steps, and recommended if you are a beginner or need refreshing your Git skills.

- [Ignoring files with `.gitignore`](https://docs.github.com/en/get-started/getting-started-with-git/ignoring-files): Learn how to prevent temporary files, generated outputs, credentials, and other files from being tracked by Git.
- [Writing and managing commits](https://docs.github.com/en/desktop/managing-commits/options-for-managing-commits-in-github-desktop): A beginner-friendly explanation of small, focused commits and clear commit messages.
- [GitHub Hello World exercise](https://docs.github.com/en/get-started/start-your-journey/hello-world): A guided exercise covering repositories, branches, commits, and pull requests.
- [Learn GitHub within GitHub](https://learn.github.com/skills): GitHub's own tutorial page that will help you get from beginner to advanced over a period of time.
- [Elizabeth DuPre's tutorial](https://emdupre.github.io/git-course/): A more comprehensive introductory tutorial that extends from basics to collaborating on remote repositories.

!!! tip "Bonus"

    As mentioned in previous sections, you can use GitHub Pages to freely host your personal website. We previously hosted a 3-part tutorial at PennSIVE, which you can access [here](personal_website.md).


