

# tmux配置文件

## .tmux.conf

---

set -g default-terminal "screen-256color" \# colors!

setw -g xterm-keys on

setw -g mode-keys vi

bind -Tcopy-mode-vi Escape send -X copy-selection-and-cancel

unbind -Tcopy-mode-vi MouseDragEnd1Pane



bind -n DoubleClick1Pane copy-mode\; send -X select-word

bind -Tcopy-mode-vi TripleClick1Pane send -X select-line

set -g terminal-overrides '\*88col\*:colors=88,\*256col\*:colors=256,xterm\*:XT:Ms=\E\]52;%p1%s;%p2%s\007,rxvt-uni\*:XT:Ms=\E\]52;%p1%s;%p2%s\007'

set -s escape-time 0                      \# fastest command sequences

set -sg repeat-time 600                   \# increase repeat timeout

set -s quiet on                           \# disable various messages

set -g mouse on



set -g prefix C-x                        \# GNU-Screen compatible prefix



set -g history-limit 5000                 \# boost history



\# edit configuration

bind e new-window -n '~/.tmux.conf.local' "sh -c '\${EDITOR:-vim} ~/.tmux.conf.local && tmux source ~/.tmux.conf && tmux display \"~/.tmux.conf sourced\"'"

\# reload configuration

bind r source-file ~/.tmux.conf \; display '~/.tmux.conf sourced'



\# see https://github.com/ChrisJohnsen/tmux-MacOSX-pasteboard

if 'which -s reattach-to-user-namespace' 'set -g default-command "exec initializing... 2&gt; /dev/null & reattach-to-user-namespace $SHELL -l"'





\# -- display -------------------------------------------------------------------



set -g base-index 1         \# start windows numbering at 1

setw -g pane-base-index 1   \# make pane numbering consistent with windows



setw -g automatic-rename on \# rename window to reflect current program

\# renumber windows when a window is closed

set -g renumber-windows on



set -g set-titles on                        \# set terminal title

set -g set-titles-string '\#h ❐ \#S ● \#I \#W'



set -g display-panes-time 800 \# slightly longer pane indicators display time

set -g display-time 1000      \# slightly longer status messages display time



set -g status-interval 10     \# redraw status line every 10 seconds



\# 24 hour clock

setw -g clock-mode-style 24



\# clear both screen and history

bind -n C-l send-keys C-l \; run 'tmux clear-history'



\# activity

set -g monitor-activity on

set -g visual-activity off





\# -- navigation ----------------------------------------------------------------



\# find session

bind C-f command-prompt -p find-session 'switch-client -t %%'



\# switch windows alt+number

bind -n M-1 select-pane -t 1

bind -n M-2 select-pane -t 2

bind -n M-3 select-pane -t 3

bind -n M-4 select-pane -t 4

bind -n M-5 select-pane -t 5

bind -n M-6 select-pane -t 6

bind -n M-7 select-pane -t 7

bind -n M-8 select-pane -t 8

bind -n M-9 select-pane -t 9



\# pane navigation

bind -r h select-pane -L  \# move left

bind -r j select-pane -D  \# move down

bind -r k select-pane -U  \# move up

bind -r l select-pane -R  \# move right

bind &gt; swap-pane -D       \# swap current pane with the next one

bind &lt; swap-pane -U       \# swap current pane with the previous one



\# maximize current pane

bind 1 run 'cut -c3- ~/.tmux.conf \| sh -s maximize\_pane'



\# pane resizing

bind -r H resize-pane -L 2

bind -r J resize-pane -D 2

bind -r K resize-pane -U 2

bind -r L resize-pane -R 2



\# window navigation

unbind n

unbind p

bind -r C-h previous-window \# select previous window

bind -r C-l next-window     \# select next window

bind Tab last-window        \# move to last active window



\# toggle mouse

bind m run "cut -c3- ~/.tmux.conf \| sh -s toggle\_mouse"





\# -- urlview -------------------------------------------------------------------



bind U run "cut -c3- ~/.tmux.conf \| sh -s urlview \#{pane\_id}"





\# -- facebook pathpicker -------------------------------------------------------



bind F run "cut -c3- ~/.tmux.conf \| sh -s fpp \#{pane\_id}"





\# -- list choice ---------------------------------------------------------------



bind -t vi-choice h tree-collapse

bind -t vi-choice l tree-expand

run -b 'tmux bind -t vi-choice K start-of-list 2&gt; /dev/null'

run -b 'tmux bind -t vi-choice J end-of-list 2&gt; /dev/null'

bind -t vi-choice H tree-collapse-all

bind -t vi-choice L tree-expand-all

bind -t vi-choice Escape cancel



\# -- copy mode -----------------------------------------------------------------



bind Enter copy-mode \# enter copy mode

bind b list-buffers  \# list paster buffers

bind p paste-buffer  \# paste from the top pate buffer

bind P choose-buffer \# choose which buffer to paste from



\# copy to Mac OSX pasteboard

if -b 'which -s reattach-to-user-namespace' 'bind y run "tmux save-buffer - \| reattach-to-user-namespace pbcopy"'

\# -- user defined --------------------------------------------------------------



if '\[ -f ~/.tmux.conf.local \]' 'source ~/.tmux.conf.local'



\# -- 8&lt; ------------------------------------------------------------------------



run 'cut -c3- ~/.tmux.conf \| sh -s apply\_configuration'



## .tmux.conf.local

---

\# \(‑●‑●\)&gt; released under the WTFPL v2 license, by Gregory Pakosz \(@gpakosz\)



\# use the powerline patched font variant of the theme, possible values are:

\#   - default

\#   - powerline

tmux\_conf\_theme=default

\#tmux\_conf\_theme=powerline



\# display an indicator in the status line when the prefix key has been pressed,

\# possible values are:

\#   - enabled

\#   - disabled

tmux\_conf\_theme\_prefix=disabled

\#tmux\_conf\_theme\_prefix=enabled



\# display the battery information in the status line, possible values are:

\#   - enabled

\#   - disabled

tmux\_conf\_theme\_battery=enabled

\#tmux\_conf\_theme\_battery=disabled



\# style to use for the battery status, possible values are:

\#   - bar

\#   - percentage

tmux\_conf\_battery\_style=bar

\#tmux\_conf\_battery\_style=percentage



\# symbols to use for battery status, possible values are:

\#   - block

\#   - heart

tmux\_conf\_battery\_symbol=block

\#tmux\_conf\_battery\_symbol=heart



\# number of symbols to use for battery status

tmux\_conf\_battery\_symbol\_count=auto

\#tmux\_conf\_battery\_symbol\_count=5



\# palette used for the battery status, possible values are:

\#   - 'colour\_full\_fg,colour\_empty\_fg,colour\_bg'

\#   - heat

\#   - gradient

tmux\_conf\_battery\_palette='\#d70000,\#e4e4e4,\#000000'

\#tmux\_conf\_battery\_palette=colour160,colour254,colour16

\#tmux\_conf\_battery\_palette=heat

\#tmux\_conf\_battery\_palette=gradient



\# display the battery status: charging \(U+26A1\) / discharging \(U+1F50B\)

tmux\_conf\_battery\_status=enabled

\#tmux\_conf\_battery\_status=disabled



\# or alternatively use an external tool, e.g. https://github.com/Goles/Battery

\#tmux\_conf\_battery='\#\(battery -t\) '



\# display the time in the status line, possible values are:

\#   - enabled

\#   - disabled

tmux\_conf\_theme\_time=enabled

\#tmux\_conf\_theme\_time=disabled



\# display the date in the status line, possible values are:

\#   - enabled

\#   - disabled

tmux\_conf\_theme\_date=enabled

\#tmux\_conf\_theme\_date=disabled



\# display the username in the status line, possible values are:

\#   - enabled

\#   - disabled

\#   - ssh

tmux\_conf\_theme\_username=enabled

\#tmux\_conf\_theme\_username=disabled

\#tmux\_conf\_theme\_username=ssh



\# display the hostname in the status line, possible values are:

\#   - enabled

\#   - disabled

\#   - ssh

tmux\_conf\_theme\_hostname=enabled

\#tmux\_conf\_theme\_hostname=ssh

\# highlight focuse pane, possible values are:

\#   - enabled

\#   - disabled

tmux\_conf\_theme\_highlight\_focused\_pane=disabled

\# should new windows retain current path, possible values are:

\#   - true

\#   - false

tmux\_conf\_new\_windows\_retain\_current\_path=false

\#tmux\_conf\_new\_windows\_retain\_current\_path=true



\# should new panes reatin current path, possible values are:

\#   - true

\#   - false

tmux\_conf\_new\_panes\_retain\_current\_path=true

\#tmux\_conf\_new\_panes\_retain\_current\_path=false



\# prompt for session name when creating a new session, possible values are:

\#   - true

\#   - false

tmux\_conf\_new\_session\_prompt=false

\#tmux\_conf\_new\_session\_prompt=true



\# if you're running tmux within iTerm2

\#   - and tmux is 1.9 or 1.9a

\#   - and iTerm2 is configured to let option key act as +Esc

\#   - and iTerm2 is configured to send \[1;9A -&gt; \[1;9D for option + arrow keys

\# then uncomment the following line to make Meta + arrow keys mapping work

\#set -g terminal-overrides "\*:kUP3=\e\[1;9A,\*:kDN3=\e\[1;9B,\*:kRIT3=\e\[1;9C,\*:kLFT3=\e\[1;9D"

\# ------------------------------------------------------------------------------

\# this is the place to further customize tmux's configuration

\#set -g history-limit 10000

