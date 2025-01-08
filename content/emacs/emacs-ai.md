---
title: "Using AI features within Emacs"
date: 2025-01-05T15:52:33+01:00
draft: false
---

To utilize different language models like ChatGPT in Emacs, you can follow these steps:

**1. Setting up Emacs:**
- Clone the Spacemacs repository:
  ```shell
  git clone https://github.com/syl20bnr/spacemacs ~/.emacs.d
  ```
- Clone example dotfiles:
  ```shell
  cd $HOME && git clone https://github.com/imago/dotfiles.git dotfiles && cp dotfiles/.spacemacs .
  ```


**2. Installing GPG on Mac using Homebrew:**
```shell
brew install gpg
```

**3. Creating and utilizing ChatGPT API key:**
- Generate an API key from [here](https://platform.openai.com/api-keys).
- Create a file named `.authinfo`:
  ```shell
  touch .authinfo
  chmod 600 ~/.authinfo
  ```
- Add the following line to `.authinfo`, inserting your information:
  ```
  machine api.openai.com login <your user> password <your api key>
  ```


Now you can leverage gptel with Emacs. For instance, create some text, select a region, and execute `M-x + gptel-rewrite`. Choose "r" to refine the text. Accept the changes by executing `M-x + gptel-rewrite` and then "E" to confirm modifications.

Feel free to explore gptel's features [here](https://github.com/karthink/gptel).

