---
title: tmux配置文件
tags:
  - [tmux]
categories:
  - gallery
date: 2023-11-13 17:52:17
---
全部拷贝然后粘贴到`~/.tmux.conf`文件中即可
```conf
# ------------------
# URL: https://github.com/tao12345666333/dotfiles
# Author: Jintao Zhang
# Blog: https://moelove.info
# Last update: 2021.03.25
# ------------------


# ------------------
# Basic setting
# ------------------

# Because of copying text problems on macOS Sierra
# run `brew install reattach-to-user-namespace`
# and add following configurations
# via https://github.com/tmux/tmux/issues/543
# https://github.com/ChrisJohnsen/tmux-MacOSX-pasteboard/issues/53
set -g default-shell $SHELL
# for macOS reattach-to-user-namespace
# set -g default-command "reattach-to-user-namespace -l ${SHELL}"

set -g default-terminal 'screen-256color'
# enable utf-8 
# set -g status-utf8 on

set -g history-limit 100000
# use vi mode
setw -g mode-keys vi

set -g mouse on

set-option -g display-time 5000

# if the tmux's version above version 1.7, you can use this option
set-option -g renumber-windows on
# if the tmux's version bellow version 1.7, you can use this.
# bind-key C-s run "for i in $(tmux lsw|awk -F: '{print $1}'); do tmux movew -s \$i; done"


# ------------------
# Split window
# ------------------
# For tmux v1.9 the `default-path` option was removed. So we need `-c` option

unbind-key c
bind-key c new-window -c '#{pane_current_path}'
 
unbind-key '"'
bind-key '"' split-window -v -c '#{pane_current_path}'
 
unbind-key %
bind-key % split-window -h -c '#{pane_current_path}'


# ------------------
# Reload config
# ------------------

bind-key R source-file ~/.tmux.conf \; display-message "Reload configuration.."


# ------------------
# Choose pane
# ------------------

unbind-key h
bind-key h select-pane -L

unbind-key j
bind-key j select-pane -D

unbind-key k
bind-key k select-pane -U

unbind-key l
bind-key l select-pane -R


# ------------------
# Resize pane
# ------------------

bind-key H resize-pane -L 6
bind-key J resize-pane -D 6
bind-key K resize-pane -U 6
bind-key L resize-pane -R 6


# ------------------
# window title string
# ------------------
set -g set-titles-string '#T'


# -----------------
# plugin
# -----------------
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g set-clipboard on
set -g @plugin 'laktak/extrakto'
set -g @plugin 'tmux-plugins/tmux-yank'
set -g @plugin 'tmux-plugins/tmux-resurrect'


run '~/.tmux/plugins/tpm/tpm'


# This tmux statusbar config was created by tmuxline.vim
# https://github.com/edkolev/tmuxline.vim

```