---
title: "Building an Awesome WezTerm + Zsh Setup on macOS"
date: 2026-02-15T11:55:42+01:00
description: "Step-by-step guide to configuring WezTerm, Zsh, and related tools for a fast, modern terminal workflow on macOS."
tags: [wezterm, zsh, macos, terminal, neovim]
draft: false
---

Most macOS setups ship with a boring default terminal, but you can turn it into a fast, beautiful, and productive environment using **WezTerm**, Zsh, and a few plugins.

This post walks through a full setup you can copy and adapt to your own workflow on macOS.

## Prerequisites

You need the following on macOS before you start:

- Homebrew installed (package manager for macOS).
- Zsh as your default shell (it is the macOS default; verify with `echo $0` and expect `-zsh`).
- Basic familiarity with editing text files from the terminal.

If you do not have Homebrew yet, install it from the official Homebrew website before proceeding.

## Install core tools

### Install WezTerm

WezTerm is a GPU-accelerated, cross-platform terminal emulator written in Rust that uses Lua for configuration.

```bash
brew install --cask wezterm
```

After installation, launch WezTerm from Spotlight (Cmd + Space, type "WezTerm", press Enter) to confirm it opens correctly. If the application does not show up, open your "Applications" folder and double-click on "WezTerm".

### Install Git

Git is required to fetch configuration and plugins later.

```bash
brew install git
```

### Install a Nerd Font (Meslo)

A Nerd Font is required so that icons and glyphs in prompts and tools render correctly. This setup uses Meslo Nerd Font.

```bash
brew install --cask font-meslo-lg-nerd-font
```

After installation, Meslo will be available to WezTerm as a font choice.

## Create the WezTerm configuration

WezTerm reads configuration from a Lua file in your home directory called `~/.wezterm.lua`.

### Create the config file

```bash
touch ~/.wezterm.lua
```

Open it in your favorite editor:

- TextEdit

```bash
open -a TextEdit ~/.wezterm.lua
```

- VS Code

```bash
code ~/.wezterm.lua
```

- Neovim

```bash
nvim ~/.wezterm.lua
```

WezTerm will reload changes from this file after it has been created and WezTerm has been restarted once.

### Basic WezTerm config skeleton

Start with a minimal Lua skeleton that builds a configuration table and returns it.

```lua
local wezterm = require("wezterm")

local config = wezterm.config_builder()

-- (add options on the config table here)

return config
```

This pattern loads the WezTerm module, builds a config object, and returns it at the end of the file.

## Configure fonts, UI, and colors

### Set font and font size

Configure WezTerm to use Meslo Nerd Font so icons render properly.

```lua
config.font = wezterm.font("MesloLGS Nerd Font Mono")
config.font_size = 19.0
```

Adjust `font_size` based on your screen resolution and preference.

### Configure the tab bar

If you do not use WezTerm’s built-in tab bar, you can disable it for a cleaner look.

```lua
config.enable_tab_bar = false
```

If you keep it enabled, useful defaults include:

- Cmd + T: New tab
- Cmd + Shift + [: Previous tab
- Cmd + Shift + ]: Next tab

### Window decorations

You can control the title bar and resize controls via `window_decorations`.

Common values are:

- `"NONE"`: Hides title bar and resize controls (not recommended; you cannot resize with the mouse).
- `"RESIZE"`: Shows just enough UI to resize the window.
- `"TITLE|RESIZE"`: Shows title bar and resize controls (the default).

Example:

```lua
config.window_decorations = "RESIZE"
```

Using `"RESIZE"` keeps the window resizable while keeping the chrome minimal.

### Color scheme and theme

You can define a custom color scheme directly in `~/.wezterm.lua` using `config.colors`.

If you already have a theme (for example one designed to match your Neovim colorscheme), paste its Lua table into the config:

```lua
config.colors = {
  -- paste your color table here
}
```

### Opacity and background blur (macOS)

On macOS, WezTerm supports both window opacity and a background blur radius.

```lua
config.window_background_opacity = 0.8
config.macos_window_background_blur = 20
```

- `window_background_opacity` ranges from `0.0` (fully transparent) to `1.0` (fully opaque).
- `macos_window_background_blur` is typically in the range `0` to `100`.

Experiment until you get a comfortable level of transparency and blur. If you prefer a solid background again, remove or comment out these lines and reload WezTerm.

### Optional: Neovim transparency

If your Neovim config supports it, you can enable transparency in Neovim to match the terminal background (often via a `transparent = true` toggle in your colorscheme config).

## Install and configure Powerlevel10k

Powerlevel10k is a highly customizable Zsh theme that provides a responsive prompt with icons and status information.

### Install Powerlevel10k

```bash
brew install powerlevel10k
```

### Enable Powerlevel10k in `~/.zshrc`

Add the Powerlevel10k theme to your `~/.zshrc` (exact path depends on your Homebrew prefix). Common examples:

```zsh
# Apple Silicon (default Homebrew prefix)
source /opt/homebrew/share/powerlevel10k/powerlevel10k.zsh-theme

# Intel Homebrew prefix (older Macs)
# source /usr/local/opt/powerlevel10k/powerlevel10k.zsh-theme
```

Reload your shell:

```bash
source ~/.zshrc
```

Reloading will start the Powerlevel10k configuration wizard.

### Run the Powerlevel10k configuration wizard

The wizard steps through styling options. Choices used in the referenced setup include:

- Confirm icons look correct (Nerd Font installed).
- Use the "lean" style.
- Use a single-line Unicode prompt.
- Use 8-color mode where suggested.
- Show the current time in 12-hour format.
- Use two lines for the final prompt layout with a dotted separator.
- Use a black prompt background.
- Choose sparse prompt elements with many icons.
- Disable transient prompts.
- Accept writing the generated configuration file.

You can rerun the configuration later by editing or deleting the Powerlevel10k config file.

## Zsh plugins: autosuggestions and syntax highlighting

Two important plugins improve usability: autosuggestions (ghost text from history) and syntax highlighting.

### Install the plugins

```bash
brew install zsh-autosuggestions zsh-syntax-highlighting
```

### Enable plugins in `~/.zshrc`

Add plugin initialization lines to `~/.zshrc` so they load on shell startup. One common pattern is:

```zsh
source /opt/homebrew/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

Reload Zsh:

```bash
source ~/.zshrc
```

You should now see gray suggestions while typing (accept with the right arrow) and syntax-highlighted commands (valid commands in green, invalid in red).

## Improve history search behavior

By default, up/down arrows cycle through history without respecting what you have already typed. You can configure Zsh so up/down arrows search history for commands matching your current prefix.

### Add history configuration to `~/.zshrc`

Edit `~/.zshrc`:

```bash
nvim ~/.zshrc
```

Add your preferred history tuning (for example: bigger history size, share history, and prefix-based search behavior), then save.

### Bind up/down arrows to prefix search

Capture the escape sequences produced by your arrow keys:

1. In a terminal, press Ctrl + V, then press the up arrow; note the printed sequence.
2. Press Ctrl + V, then press the down arrow; note the printed sequence.

Then add keybindings (replace placeholders with your captured sequences):

```zsh
bindkey "<your-up-sequence>" history-beginning-search-backward
bindkey "<your-down-sequence>" history-beginning-search-forward
```

Reload Zsh and test by typing a prefix (like `ca`) and pressing the up arrow to cycle through matching history.

## Better `ls` and `cd` with eza and zoxide

This setup uses `eza` as a modern `ls` replacement and `zoxide` as an intelligent directory jumper.

### Install eza and zoxide

```bash
brew install eza zoxide
```

### Add aliases and initialization to `~/.zshrc`

Add:

```zsh
alias ls="eza --icons"

eval "$(zoxide init zsh)"
alias cd="z"
```

Reload Zsh:

```bash
source ~/.zshrc
```

Example usage:

```bash
cd my-long-project-name   # first time, normal navigation
cd my-long                # later, jumps there via zoxide
```

### Using WezTerm pane splits

Common defaults:

- Vertical split: Ctrl + Shift + Alt + `%`
- Horizontal split: Ctrl + Shift + Alt + `"` (double quote)
- Navigate panes: Ctrl + Shift + Arrow keys

Close panes by running `exit` in each shell.


## Putting it all together

After completing these steps, you have:

- WezTerm as a fast, GPU-accelerated terminal with a custom font, clean chrome, and (optionally) a matching color scheme.
- Zsh with Powerlevel10k for a rich, responsive prompt.
- Autosuggestions, syntax highlighting, and improved history search for a smoother command-line experience.
- Modern replacements for `ls` and `cd` via `eza` and `zoxide`.

## Find my configurations in my dotfiles folder

- https://github.com/imago/dotfiles

From here, refine the Lua config, tweak colors and opacity, and extend your Zsh setups to match the rest of your development environment.
