#linux #shell

# tmux config

One can promote a pane into a window with `<prefix>+!`, this creates a new window with one pane.
To revert this (ie. demote a window and move it to an another window) there is no bind for this. There is indeed a workaround: `:split-pane -t :<window-number>`.
The best way in my opinion is to create a new bind in the config: `bind @ command-prompt -p "move to:" "split-pane -t ':%%'"`. This will prompt the user for a number, and will send the current selected pane to the corresponding window.
