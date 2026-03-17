---
name: oh-my-zsh
description: Install and configure Oh My Zsh with recommended plugins. Use when setting up a new machine or configuring shell environment.
---

# Oh My Zsh Setup

## Installation Command
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## Recommended Plugins
Add these to the plugins array in `~/.zshrc`:
```bash
plugins=(
  git                      # Git aliases and completions
  zsh-autosuggestions      # Fish-like autosuggestions (requires separate install)
  zsh-syntax-highlighting  # Syntax highlighting for commands (requires separate install)
  history                  # History search shortcuts
  history-substring-search # Type partial command + up arrow to search
  z                        # Jump to frequently used directories
  docker                   # Docker completions (if using Docker)
  npm                      # npm completions and aliases
  node                     # Node.js completions
  extract                  # Universal archive extraction with `x` command
  colored-man-pages        # Colorized man pages
)
```

## External Plugin Installation
Some plugins require separate installation:
```bash
# zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# history-substring-search
git clone https://github.com/zsh-users/zsh-history-substring-search ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-history-substring-search
```

## Post-Installation
After editing `~/.zshrc`, reload the shell:
```bash
source ~/.zshrc
```
