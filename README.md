# homebrew-command-not-found

[![Build Status](https://travis-ci.org/Homebrew/homebrew-command-not-found.svg?branch=master)](https://travis-ci.org/Homebrew/homebrew-command-not-found)

This project tries to reproduce Ubuntu’s `command-not-found` for Homebrew users
on OSX.

On Ubuntu, when you try to use a command that doesn’t exist locally but is
available through a package, Bash will suggest you a command to install it.

Using this script, you can replicate this feature on OSX:

```
# on Ubuntu
$ when
The program 'when' is currently not installed.  You can install it by typing:
sudo apt-get install when

# on OSX with Homebrew
$ when
The program 'when' is currently not installed. You can install it by typing:
  brew install when
```

## Install

First, tap this repository: 
```
brew tap homebrew/command-not-found
```

* **Bash and Zsh**: Add the following line to your `.bashrc` (bash) or `.zshrc` (zsh):
    ```bash
    if brew command command-not-found-init > /dev/null; then eval "$(brew command-not-found-init)"; fi
    ```
    
* **Fish**: Run the following command:
 ```
brew command-not-found-init-fish --fish
 ```
    * _Note_: If your functions directory isn't  `~/.config/fish/functions/` 
    then pass custom path as the second argument. You can find that out with 
    `echo $fish_function_path`. Eg.
    
    ```
    brew command-not-found-init-fish --fish /usr/local/fish/functions/
    ```

### Upgrade from 0.1.1

Before the 0.2.0 version this was a simple `handler.sh` script which `grep`-ed
in formulae to find the binaries. It worked well for most formulae but wasn’t
exhaustive.

The 0.2.0 version changed how it works. If you’d like to keep your existing
handler script, it’s fine, it’ll continue to work. If instead you’d like to
upgrade to 0.2.0 just remove it and follow the install instructions above.

## Support

This tool supports Bash (version 4 and higher), Zsh, and Fish (2.2.0).

## How does it work?

When you tap the repo you’ll get two more `brew` commands: `brew which-formula`
and `brew which-update`. The first one uses a file database to gives you which
formula you have to install in order to get a specific command. The file is
generated by the second command by crawling all installed formulae and collect
their binaries. If we regularly run this update script on multiple Homebrew
installations I hope we’ll cover most of the formulae. Having this as a tap
means you get an up-to-date binaries database each time you run `brew update`.

For Bash and Zsh, the `handler.sh` script defines a function
`command_not_found_handle` and `command_not_found_handler`, respectively.
Which is used when you try a command that doesn’t exist. The function calls
`brew which-formula` on your command, and if it finds a match it’ll print it to you.
If not, you’ll get an error as expected.

For Fish, the `handler.fish` defines a function `command-not-found-handler` with is
used by Fish when a command doesn't exist. The function is symlinked in your
functions directory. Fish recognises the function, and uses this instead of its default
handler. The function utilises `brew which-formula` to find and prints the relevant
information about the command; on the case where it finds nothing, the function
runs the default handler (`__fish_default_command_not_found_handler`).

Over 4300 formulae have been imported in the database, representing more than
19900 different commands (99% of the main Homebrew repo + 69% of all official
taps).
