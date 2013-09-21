---
layout: post
title: "Mac OSX 10.8 Rails installation"
date: 2013-09-21 04:36
comments: true
categories: mac, osx, rails
---

## Dev workstation setup

These instructions are for Mac OSX 10.8 Mountain Lion. Following these instructions will prepare your workstation for developing in Ruby on Rails.

<!-- more -->

{% codeblock ex.sh %}

 Mac OS X 10.8 installation steps

#====================================================================================
# ** many commands require sudo but many do not
# ** this installation documentation is meant to describe a user level install 
# ** where sudo is needed it is explicitly referenced 
# ** pay attention to not add sudo where it is NOT listed
# ** it will affect the permissions negatively  
#====================================================================================

# prepare mac for installations and configuration
# changes have been made to 10.8, that require 
# modification in order to configure the way we want

# make the ~/Library directory visible so we can move bbedit customizations into it
chflags nohidden ~/Library/

# make directories for our projects
mkdir -p ~/Sites/shared/

# install xcode - needed for compilers and command line tools 
# **this requires an apple developer account 
# use the App Store app

# run the installer - the commandline tools contain the compilers 
# install the command line tools from within the xcode application
# from xode menu select preferences then select downloads tab, install commandline tools 


# ** Apple no longer ships an x client, which is required for certain libs being compiled 
# ** install xquartz to meet these requuirements
http://static.macosforge.org/xquartz/downloads/SL/XQuartz-2.7.2.dmg


# install homebrew  - http://mxcl.github.com/homebrew/
ruby <(curl -fsSkL raw.github.com/mxcl/homebrew/go)

# check brew config
brew doctor

# update brew
brew update

# install packages
brew tap homebrew/dupes
brew install wget
brew install git
brew install autoconf automake apple-gcc42
brew install ghostscript
brew install imagemagick
brew install mutt
brew install node
brew install redis
brew install bash-completion

brew install bash-completion

# configure git
git config --global color.ui true
git config --global core.editor bbedit
git config --global color.branch auto
git config --global color.diff auto
git config --global color.status auto


# create new github account for user @ http://github.com

# create keys for new computer / user
 ssh-keygen -t rsa -C "<your_email>"
 pbcopy < ~/.ssh/id_rsa.pub

# copy new keys to new user account @ http://github.com
https://help.github.com/articles/generating-ssh-keys

# install mysql
brew install mysql 
unset TMPDIR
mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp
mysql.server start

# make mysql start automatically (optional)
mkdir -p ~/Library/LaunchAgents
cp /usr/local/Cellar/mysql/5.6.12/homebrew.mxcl.mysql.plist ~/Library/LaunchAgents/
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist


# set mysql password
mysqladmin -u root password NEWPASSWORD


# install RVM and rubies 
curl -L https://get.rvm.io | bash -s stable --ruby
rvm requirements

# may or may not need these commands
# depending on your system
# rvm pkg install openssl
# export CPPFLAGS=-I/opt/X11/include
# CC=/usr/local/bin/gcc-4.2 rvm reinstall 1.8.7
# CC=/usr/local/bin/gcc-4.2 rvm install 1.9.3

# install ruby
rvm install 1.9.3

# use this ruby as default and create gemset
rvm use 1.9.3@lin --create --default


# create and edit .bash_profile for your environment

cat > ~/.bash_profile <<-ENDOFMESSAGE

#############################################
# GIT completions & highlighted prompt 
#############################################

if [ -f `brew --prefix`/etc/bash_completion ]; then
 . `brew --prefix`/etc/bash_completion
fi

GIT_PS1_SHOWDIRTYSTATE=true
PS1='\[\033[32m\]\u@\h\[\033[00m\]:\[\033[34m\]\w\[\033[31m\]$(__git_ps1)\[\033[00m\]\$ '


#############################################
# Bash ls colors
#############################################

export CLICOLOR=1
export LSCOLORS=DxGxcxdxCxegedabagacad


#############################################
# alias
#############################################

alias ll='ls -l'
alias la='ls -la'

########
## git

alias g='git status'
alias gg='git log'
alias gst='git status'

alias gb='git branch'
alias gba='git branch -a'

alias gc='git commit -v'
alias gca='git commit -v -a'
alias gce="git commit --amend" # fix last commit message
alias gco='git checkout'

alias gd='git diff | bbedit'
alias gdm='git diff master'

alias gl='git pull'
alias gp='git push'


#############################################
# update path for mysql and pear
#############################################

export PATH=/usr/local/mysql/bin:/usr/local/share/npm/bin:$PATH

#############################################
# RVM - for managing versions of ruby
#############################################

[[ -s "~/.rvm/scripts/rvm" ]] && source "~/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*


ENDOFMESSAGE

# prevent gems from installing docs
echo 'gem: --no-rdoc --no-ri' >> ~/.gemrc

# install gems 
gem install bundler
gem install mysql2

#### if you have issues with mysql2 
#### ** mysql 5.6.12 
# change the mysql_config file 
# then try installing again
# http://stackoverflow.com/questions/5919727/bundle-install-problem-mysql-h-is-missing
# http://www.randomactsofsentience.com/2013/05/gem-install-mysql2-missing-mysqlh-on-os.html
vi /usr/local/Cellar/mysql/5.6.12/bin/mysql_config
```