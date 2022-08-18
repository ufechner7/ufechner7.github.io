---
title: "Installing Julia 1.8 and VSCode"
date: 2022-08-18
tags: Julia
published: true
---
# Installing Julia 1.8 and VSCode

## Introduction
Installing Julia is easy, but perhaps you also want to install an integrated development environment (IDE) or a version control system (e.g. git), therefore I give some hints how to do that in this blog post.

Furthermore there are different ways to install multiple Julia versions in parallel and to keep your version up-to-date which is also explained in this blog post.

## Installation of Julia

### Windows
Please download and install Julia as explained [here](https://julialang.org/downloads/) .
Choose the 64-bit (installer). Make sure to check the option "Add julia to path" when running the installer.
For advanced users it is suggested also to install [git for Windows](https://gitforwindows.org/) which also includes the `bash` command line interface, which is very useful for small automation tasks. The git version control system keeps track of the changes of your files and allows the SW development in a team.

It is suggested to launch Julia from the command line, using either the "Windows command prompt" or "bash" by typing `julia` or `julia --project` (if you work with projects). If you never used a command prompt before, read the [Beginners Guide](https://www.makeuseof.com/tag/a-beginners-guide-to-the-windows-command-line/).

#### Juliaup
An installer and version manager for Julia called [juliaup](https://github.com/JuliaLang/juliaup) is available in the Microsoft Store. It can be used to install specific Julia versions or update to the latest release. This package handles all PATH related aspects of Julia, and alerts users when new Julia versions are released.

#### Uninstallation
Uninstallation is preferably performed by using the Windows uninstaller. The directory in %HOME%/.julia can then be deleted if you want to remove all traces of Julia (this includes user installed packages).

### Linux

Copy and past the following line to install the latest stable version of Julia:
```
bash -ci "$(curl -fsSL https://raw.githubusercontent.com/abelsiqueira/jill/master/jill.sh)"
```
If you want to be able to easily switch between different versions of Julia consider to install
the Python version of jill, see https://github.com/johnnychen94/jill.py
```bash
pip install jill --user -U
jill install 1.8
```
If you should later install version 1.9 with `jill install 1.9` you can then switch between the versions with `jill switch 1.8` etc.

It is suggested to add the following line to your ```.bashrc``` file:
```
alias jl='./bin/run_julia'
```
This makes it possible to run julia with the shortcut `jl` later, if you have a `run_julia` script in the `bin` folder of your projects. I suggest to use such a script, the most simple version of it would just contain the line `julia --project` . 

### Mac
Please download Julia from at https://julialang.org/downloads/

A julia-1.8.0-mac64.dmg file is provided, which contains Julia-1.8.app. Installation is the same as any other Mac software: drag the Julia-1.8.app to Applications Folder's Shortcut. The Julia download runs on macOS 10.9 Mavericks and later releases. 

You can launch Julia by opening the Julia app like any other application.

#### Add Julia to PATH
If you want to launch Julia from the command line, first open a new terminal window, then run the following snippet from your shell (e.g., using the Terminal app, not inside the Julia prompt).

```bash
sudo mkdir -p /usr/local/bin
sudo rm -f /usr/local/bin/julia
sudo ln -s /Applications/Julia-1.8.app/Contents/Resources/julia/bin/julia /usr/local/bin/julia
```

This code creates a symlink to a Julia version (here 1.8) of your choosing. To launch Julia, simply type ```julia``` inside your shell and press return. If you are working with projects, use the command ```julia --project```.

#### Installing multiple versions in parallel
The jill installers will most likely also work on Mac and allow easy switching of different Julia versions (see Linux section).

#### Uninstallation
You can uninstall Julia by deleting `Julia.app` and the packages directory in `~/.julia` . Multiple `Julia.app` binaries can co-exist without interfering with each other. If you would also like to remove your preferences files, remove `~/.julia/config/startup.jl` and `~/.julia/logs/repl_history.jl` .

## Installation of the IDE VSCode
It is useful to install the integrated development environment VSCode, even though it is not
required. You can also use any editor of your choice. 

VSCode provides syntax highlighting, but also the feature "goto definition" which can help to understand and explore the code. 

<p align="center"><img src="https://raw.githubusercontent.com/ufechner7/ufechner7.github.io/main/_posts/vscode.png" width="600" /></p>

You can download and install VSCode for all operating systems from this location: https://code.visualstudio.com/

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
