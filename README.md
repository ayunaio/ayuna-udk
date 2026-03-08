# Ayuna UDK

Unified development kit with helper utilities. This is primarily aimed for Debian/Ubuntu based, X86-64bit systems. You are welcome to adapt this for RPM based systems and raise a PR.

## Motto

The purpose of this project is to unclutter .bashrc a bit or, better yet, propose a version of it which I found simple and manageable. You are encouraged to use it at your own discretion. If you find it useful, please do not forget to add a star! Thanks.

## Setup UDK

Follow the steps to setup the bash customizations using `udk`.

- git clone `https://github.com/ayunaio/ayuna-udk.git` ~/.udk
- Edit your ~/.bashrc file and add the following lines.

  ```bash
  ## NOTE: udk configuration - Should be towards the end
  
  ## For BASH
  if [ -d "$HOME/.udk" ]; then
      source "$HOME/.udk/env.bash"
  fi
  ```

- Create `~/.config/direnv` folder, if not exists (`mkdir -p ~/.config/direnv`)
- Copy all the files under `skel/direnv` folder into `~/.config/direnv`. If you already have entries in the destination, you need to adjust your configuration manually, to avoid overwriting.

- Close the terminal and open a new one to access the scripts and custom configurations as configured in `udk`.

## Structure of UDK

- The ayuna-udk is expected to be cloned into `~/.udk` folder (*i.e., under user's home dir*)
- It uses [direnv](https://direnv.net/) for automated environment switching based on the folder. So, it honours the .envrc files to load the environment for a given folder in the terminal / bash
- It is recommended to use [starship](https://starship.rs/) for a modern, fast, customizable prompt experience
- It is recommended to use [fzfbash](https://github.com/junegunn/fzf) for a very handly, fuzzy finder functionality for bash
- The `scripts/devkit-setup.sh` script can be used to install multiple versions of python, openjdk, golang, nodejs, gradle and maven under `~/.udk/dist` folder
- The `.envrc` files can be used to control the version(s) of these runtimes and tools to use for a given project
- The devkit-setup script uses cache and dist folders internally to manage the installations and these two folders are git-ignored.

## Installing tools and devkits

### Install starship prompt

```bash
sudo apt-get update && sudo apt-get install -y curl && sync
mkdir -p ${HOME}/.local/bin
curl -sS https://starship.rs/install.sh | sh -s -- -b ${HOME}/.local/bin

# NOTE: Once you complete the `setup udk` section, the env.bash
# internally loads staship prompt for the bash terminal. No extra step is required.
```

### Install FZF-Bash

```bash
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install

# NOTE: Once you complete the `setup udk` section, the env.bash
# internally loads fzf key-bindings and completion. No extra step is required.
```

### Install Python-3.x.y

You can install any officially available [Python3](https://www.python.org/downloads/source/) using `scripts/devkit-setup.sh` script. The script automatically downloads, compiles and installs the python version under `~/.udk/dist/` folder.

For example, to install Python 3.12.12, you just need to run the following command.

```bash
## NOTE: This will install gcc toolchain and related dependencies
# since, python will be compiled and installed from source.
# Use the optional argument `--force=true` in order to ignore the cached
# python source package, if present and download it afresh.

bash ~/.udk/scripts/devkit-setup.sh --kit=python --version=3.12.12
```

### Install Golang

You can install any officially available version of Golang using the following command. Golang version 1.26.1 is used here for example.

```bash
bash ~/.udk/scripts/devkit-setup.sh --kit=golang --version=1.26.1

# NOTE: Use the optional argument `--force=true` in order to ignore the cached
# golang package, if present and download it afresh.
```

### Install Nodejs

You can install any officially available, LTS version of Nodejs using the following command. Nodejs version 24.14.0 is used here for example.

```bash
bash ~/.udk/scripts/devkit-setup.sh --kit=nodejs --version=24.14.0

# NOTE: Use the optional argument `--force=true` in order to ignore the cached
# nodejs package, if present and download it afresh.
```

### Install OpenJDK

You can install temurin openjdk from Adoptium using the following command. OpenJDK version 21 is used here for example.

```bash
bash ~/.udk/scripts/devkit-setup.sh --kit=openjdk --version=21

# NOTE: Use the optional argument `--force=true` in order to ignore the cached
# openjdk package, if present and download it afresh.
```

### Install Maven

You can any officially available version of Maven using the following command. Maven version 3.9.13 is used here for example.

```bash
bash ~/.udk/scripts/devkit-setup.sh --kit=maven --version=3.9.13

# NOTE: Use the optional argument `--force=true` in order to ignore the cached
# maven package, if present and download it afresh.
```

### Install Gradle

You can install any officially available version of Gradle using the following command. Gradle version 9.4.0 is used here for example.

```bash
bash ~/.udk/scripts/devkit-setup.sh --kit=gradle --version=9.4.0

# NOTE: Use the optional argument `--force=true` in order to ignore the cached
# gradle package, if present and download it afresh.
```

## Making use of .envrc

In the home folder create a file named `.envrc` and add the required version of the runtimes and tools, following the direnv convention. This sets up the default version of those runtimes and tools.

Note that, for python using uv as the package manager, ayuna-udk provides a special layout syntax - `layout pyuv <version> [novenv]` for direnv.

The content of the example ~/.envrc file with the above devkit installations, is as follows.

```bash
layout pyuv 3.12.12 novenv
use golang 1.26.1
use nodejs 24.14.0
use openjdk 21
use maven 3.9.13
use gradle 9.4.0
```

> **NOTE**: The `novenv` option for `pyuv` based python environment setting ensures that .venv folder is not created and it directly uses the python version under ~/.udk/dist/ folder. This is recommended for the top-level or home-dir python runtime setting. For project specific environment files, the `novenv` setting is not recommended.

The above configurations can be overridden in any of the project (sub)folders. For example, if there is a project named ~/Work/my-webapp/, and you would like to use python version 3.11.14 and nodejs 22.22.11, your ~/Work/my-webapp/.envrc file will look like below.

```bash
layout pyuv 3.11.14
use nodejs 22.22.11
```

This will create a `.venv` folder for python under `~/Work/my-webapp/` and will use the nodejs version 22.22.11 which is installed using `devkit-setup` script, under ~/.udk/dist/ folder.

## Helper scripts

The ayuna-udk also provides a bunch of helper scripts under `~/.udk/bin` folder for various uses. This path is automatically added to the PATH env, if you have completed the `setup udk` section.

## Creating Self-Signed CA (SSCA)

Under `~/.udk/bin` folder, there is a script named `udk-self-signed-ca` which is meant to create self-signed certificates and manage them. The script also provides command-line help if run without arguments. The sample configuration file at `~/.udk/skel/ssca/sample01.rc` can be copied into a new file and edited with appropriate values. The file can then be passed as an argument to the script to generate SSCA set for the given domain.
