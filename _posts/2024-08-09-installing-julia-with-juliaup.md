---
title: "Installing Julia 1.10 and VSCode"
date: 2024-08-09
tags: Julia
published: false
---
# Installing Julia 1.10 and VSCode

## Introduction
Installing Julia is easy, but perhaps you also want to install an integrated development environment (IDE) or a version control system (e.g. git), therefore I give some hints how to do that in this blog post.

Furthermore there are different ways to install multiple Julia versions in parallel and to keep your version up-to-date which are also explained in this blog post.

Highlights of version 1.8 of Julia are explained [here](https://julialang.org/blog/2023/12/julia-1.10-highlights/) .

## Installation of Julia

<details>
  <summary>Windows</summary>

### Windows
Please download and install Julia using `juliaup`. Launch the `Command Prompt` app and type:

```
winget install julia -s msstore
juliaup add 1.10
juliaup update
```
If that doesn't work, download https://install.julialang.org/Julia.appinstaller and double-click on the downloaded file to install it.

#### Optional
It is suggested to install [Windows Terminal](https://learn.microsoft.com/en-us/windows/terminal/install) . Copy and paste works better, unicode works much better and you can use it with `bash` or `Command Prompt`, whatever you prefer. It is suggested to set one of these two as default using the `Settings` menu of Windows Terminal.

#### Uninstallation
Uninstallation is preferably performed by using the Windows uninstaller. The directory in %HOME%/.julia can then be deleted if you want to remove all traces of Julia (this includes user installed packages).

</details>

<details>
  <summary>Linux</summary>

### Linux

Copy and past the following line to install julia:
```
curl -fsSL https://install.julialang.org | sh
```
Restart your terminal, and then execute:
```
juliaup add 1.10
juliaup update
```

It is suggested to add the following line to your ```.bashrc``` file:
```
alias jl='./bin/run_julia'
```
This makes it possible to run Julia with the shortcut `jl` later, if you have a `run_julia` script in the `bin` folder of your projects. I suggest to use such a script, the most simple version of it would just contain the line `julia --project` .

</details>

<details>
  <summary>Mac</summary>

### Mac
Please download and install `juliaup` as explained at https://github.com/JuliaLang/juliaup .

Restart your terminal, and then execute:
```
juliaup add 1.10
juliaup update
```

</details>

## Installation of the IDE VSCode
It is useful to install the integrated development environment VSCode, even though it is not required. You can also use any editor of your choice. 

VSCode provides syntax highlighting, but also the feature "goto definition" which can help to understand and explore the code. 

<p align="center"><img src="https://raw.githubusercontent.com/ufechner7/ufechner7.github.io/main/_posts/vscode.png" width="600" /></p>

You can download and install VSCode for all operating systems [here](https://code.visualstudio.com/) .

For Ubuntu Linux the following ppa can be used to install vscode and to keep it up-to-date: [https://www.ubuntuupdates.org/ppa/vscode](https://www.ubuntuupdates.org/ppa/vscode) .

### Installing the Julia extension
- Start or open Visual Studio Code.
- Select **View** and then click **Extensions** to open Extension View.
- Enter the term `julia` in the marketplace search box. Click the green **Install** button to download the extension.

![Julia VSCode extension](https://raw.githubusercontent.com/ufechner7/ufechner7.github.io/main/_posts/julia_vscode_extension.png)

You successfully downloaded the Julia extension for VS Code.

*NOTE:* It is recommended that you restart VS Code after installation.

Julia development with VSCode is well documented here: [Julia Visual Studio Code Documentation](https://www.julia-vscode.org/docs/stable/)

I would NOT use all the advanced features of julia-vscode, I prefer to just use the vscode terminal and launch julia from the terminal. This makes it easy to launch Julia with any command line options and also to start and restart Julia quickly.

### Other useful VSCode extensions
- Project Manager
- Better TOML
- Code Spell Checker

VScode supports git out-of-the box.