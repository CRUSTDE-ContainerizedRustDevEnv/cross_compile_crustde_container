# cross_compile_rust_container

**09. Cross-compile Rust to Linux, Windows, Musl container, WASI and WASM with OCI container (2023-05)**  
***version: 1.0  date: 2022-09-06 author: [bestia.dev](https://bestia.dev) repository: [GitHub](https://github.com/bestia-dev/cross_compile_rust_container)***  

[![Lines in md](https://img.shields.io/badge/Lines_in_markdown-932-green.svg)](https://github.com/bestia-dev/cross_compile_rust_container/)
[![Lines in bash scripts](https://img.shields.io/badge/Lines_in_bash_scripts-1535-blue.svg)](https://github.com/bestia-dev/cross_compile_rust_container/)

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/bestia-dev/cross_compile_rust_container/blob/master/LICENSE)
![Hits](https://bestia.dev/webpage_hit_counter/get_svg_image/138544014.svg)

Hashtags: #rustlang #buildtool #developmenttool #tutorial #docker #ssh #container #podman #Linux #OCI  
My projects on GitHub are more like a tutorial than a finished product: [bestia-dev tutorials](https://github.com/bestia-dev/tutorials_rust_wasm).  

## Intro

Welcome to bestia.dev !
Learning Rust and Wasm programming and having fun.
I just love programming !

I upgraded my Containerized Rust Development Environment (CRDE) to enable cross-compilation to Linux, Windows, musl, WASI and WASM.  
Let's make a short tutorial on how it works!

## Containerized Rust development environment - CRDE

It is smart to start the development from scratch from time to time to avoid accumulating side effects that we forget about. Then the problem "but it works on my machine" arise. It is very easy to reset everything because we use containers.
We will start on Win10 with enabled WSL2.  
If exists, remove the Debian distro. Be careful to not lose any precious data!
We will install a fresh Debian distro in Powershell. It takes a minute.

```powershell
wsl --unregister Debian
wsl --install Debian
```

Open Debian bash, update and upgrade, then install curl and OpenSSH.
Just for info: Instead of the classic ctrl+c and ctrl+v, to Copy and Paste inside the bash terminal I use ctrl+shift+c and ctrl+shift+v. Cumbersome, but similar.

```bash
sudo apt update;
sudo apt upgrade;
cat /etc/debian_version;
   11.7
sudo apt install -y curl;
sudo apt install -y openssh-client;
# make a directory, go inside and download the first script
mkdir -p ~/rustprojects/docker_rust_development_install;
cd ~/rustprojects/docker_rust_development_install;
curl -Sf -L https://github.com/bestia-dev/docker_rust_development/raw/main/docker_rust_development_install/download_scripts.sh --output download_scripts.sh;
# you can read the bash script, it only creates the directory structure, downloads scripts and suggests what script to run next
clear;
cat download_scripts.sh;
clear;
sh download_scripts.sh;
# Read and follow the detailed instructions after every script.
```

Inside the ssh directory you will need 2 keys, one to access Github and the second to access your web server virtual machine.
You should already have these keys somewhere and you just need to copy them into the ssh folder.
I will call these keys githubssh1 and webserverssh1, but you can have other names.
Modify accordingly to your locations and run these commands.



```bash
cargo build --target x86_64-pc-windows-gnu

buildah from \
--name scratch_hello_world_img \
scratch

buildah config \
--author=github.com/bestia-dev \
--label name=scratch_hello_world_img \
--label source=github.com/bestia-dev/docker_rust_development \
scratch_hello_world_img

buildah copy scratch_hello_world_img  ~/rustprojects/rust_hello/musl/rust_hello /rust_hello

buildah commit scratch_hello_world_img docker.io/bestiadev/scratch_hello_world_img

# now run the container and executable

podman run scratch_hello_world_img /rust_hello

```

```bash

```




We will repeat how to pull and run the Containerized Rust development environment (CRDE). Things change over time and the old tutorials might be outdated.











## Cross-compile

We will cross compile to Linux, Windows, musl, WASI and WASM from our CRDE.





## Open-source and free as a beer

My open-source projects are free as a beer (MIT license).  
I just love programming.  
But I need also to drink. If you find my projects and tutorials helpful, please buy me a beer by donating to my [PayPal](https://paypal.me/LucianoBestia).  
You know the price of a beer in your local bar ;-)  
So I can drink a free beer for your health :-)  
[Na zdravje!](https://translate.google.com/?hl=en&sl=sl&tl=en&text=Na%20zdravje&op=translate) [Alla salute!](https://dictionary.cambridge.org/dictionary/italian-english/alla-salute) [Prost!](https://dictionary.cambridge.org/dictionary/german-english/prost) [Nazdravlje!](https://matadornetwork.com/nights/how-to-say-cheers-in-50-languages/) 🍻

[//bestia.dev](https://bestia.dev)  
[//github.com/bestia-dev](https://github.com/bestia-dev)  
[//bestiadev.substack.com](https://bestiadev.substack.com)  
[//youtube.com/@bestia-dev-tutorials](https://youtube.com/@bestia-dev-tutorials)  
