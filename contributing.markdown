---
layout: page
title: Contributing to Wiki
permalink: /contributing/
nav_order: 9
---
# Contributing to the PennSIVE Wiki

You'll need a Github account
<!--Some nice into here and overview. -->

## 1. Fork and clone the wiki repo

The first step to collaborate on a project is to fork the original project on Github. Navigate to `https://github.com/pennsive/pennsive.github.io` while logged in to GitHub and click `Fork` on the upper right hand corner. Then, clone your copy of the repository from GitHub onto your local machine. 
To do so, run the linew below (editing <your-username> on a Terminal inside a directory of your choice, which will create a directory called `pennsive.github.io` with all the code needed to run the website.

```sh
git clone https://github.com/<your-username>/pennsive.github.io
```

Then, we'll need to configure the original repository as the `upstream` repository 

```sh
cd pennsive.github.io
git remote add upstream https://github.com/pennsive/pennsive.github.io.git
```

We'll get into details of why this is needed in section 4


<!--Bit about origin -->

## 2. Install Ruby dependencies

Check Home brew installation or Install Homebrew. First run

```sh
brew --version
```

If that does not work then you need to install homebrew

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then, install Ruby and the Bundler gem.

```sh
brew install ruby
gem install bundler --user-install
```

Now, make sure you're inside `pennsive.github.io` and run 

```sh
bundle install 
```

This will look for a Gemfile and install the necessary dependencies. Once that's done you can test the website! Run `bash run.sh` and on a browser go to `http://127.0.0.1:4000/`. Voilà your own local copy of the PennSIVE wiki!

## 4. Work on your contribution article

Now we're ready to make some changes. Open your favorite text editor and create an empty file with `.markdown` as extension. Give it a descriptive name!
Then add a YAML section to the top, which will tell Jekyll how to render the page. For instance, you may edit the YAML of this page

```sh
---
layout: page
title: Contributing to Wiki
permalink: /contributing/
nav_order: 9
---
```

Write your article with the usual markdown syntax. You may look at the other `.markdown` files for examples. 

Note: If run `bash run.sh` and add changes while that process is running, you may see those changes by refreshing your browser.

# 5. Push to your

Once you're happy with your progress you may push to your own repository