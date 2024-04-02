---
description: Install these prerequisite dependencies
---

# Local dependencies

<table><thead><tr><th width="159">Dependency</th><th width="114">Version</th><th>Description</th><th>Link</th></tr></thead><tbody><tr><td>Go</td><td>1.19+</td><td>Programming language used for Sei development</td><td><a href="https://golang.org/doc/install">https://golang.org/doc/install</a></td></tr><tr><td>Docker</td><td>Latest</td><td>Platform for developing, shipping, and running containers</td><td><a href="https://docs.docker.com/get-docker/">https://docs.docker.com/get-docker/</a></td></tr><tr><td>jq</td><td>Latest</td><td>Lightweight and flexible command-line JSON processor</td><td><a href="https://stedolan.github.io/jq/download/">https://stedolan.github.io/jq/download/</a></td></tr><tr><td>Git</td><td>Latest</td><td>Distributed version control system</td><td><a href="https://git-scm.com/downloads">https://git-scm.com/downloads</a></td></tr><tr><td>CMake</td><td>Latest</td><td>Cross-platform build system</td><td><a href="https://cmake.org/install/">https://cmake.org/install/</a></td></tr><tr><td>GCC</td><td>Latest</td><td>GNU Compiler Collection for C, C++, and other languages</td><td><a href="https://gcc.gnu.org/install/index.html">https://gcc.gnu.org/install/index.html</a></td></tr><tr><td>Rust</td><td><strong>1.69</strong></td><td>Programming language focused on performance and safety</td><td><a href="https://www.rust-lang.org/tools/install">https://www.rust-lang.org/tools/install</a></td></tr><tr><td>GNU Make</td><td>Latest</td><td>Build automation tool</td><td><a href="https://www.gnu.org/software/make/">https://www.gnu.org/software/make/</a></td></tr><tr><td>Cargo</td><td>Latest</td><td>Rust's package manager and build tool</td><td><a href="https://doc.rust-lang.org/cargo/getting-started/installation.html">https://doc.rust-lang.org/cargo/getting-started/installation.html</a></td></tr></tbody></table>

### macOS

#### Install Homebrew

Homebrew is a package manager for macOS that simplifies the installation of software. If you haven't installed Homebrew on your Mac, follow the instructions at [https://brew.sh/](https://brew.sh/).

#### Install Dependencies

Open a terminal and run the following command:

<pre class="language-bash"><code class="lang-bash">brew install go docker jq git cmake gcc rustup make
<strong>rustup-init
</strong></code></pre>

### Windows

#### Install Chocolatey

Chocolatey is a package manager for Windows that simplifies the installation of software. If you haven't installed Chocolatey on your Windows machine, follow the instructions at [https://chocolatey.org/install](https://chocolatey.org/install).

#### Install Dependencies

Open a Command Prompt or PowerShell with administrator privileges and run the following command:

```powershell
choco install golang docker-desktop jq git cmake mingw rust make
```

### Linux (General) / Ubuntu

#### Install Dependencies

Open a terminal and run the following commands:

```bash
sudo apt update
sudo apt install golang docker.io jq git cmake gcc make
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

### CentOS / RHEL / Fedora

These instructions cover CentOS, RHEL, and Fedora, which use the `yum` or `dnf` package managers.

#### Install Dependencies

Open a terminal and run the following commands:

```bash
sudo yum install golang docker jq git cmake gcc make
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
