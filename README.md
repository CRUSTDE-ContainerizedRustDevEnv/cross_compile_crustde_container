# cross_compile_rust_container

**09. Cross-compile Rust to Linux, Windows, Musl container, WASI and WASM with OCI container (2023-05)**  
***version: 1.0  date: 2022-09-06 author: [bestia.dev](https://bestia.dev) repository: [GitHub](https://github.com/bestia-dev/cross_compile_rust_container)***  

 ![status](https://img.shields.io/badge/tutorial-yellow)

 [![Lines in md](https://img.shields.io/badge/Lines_in_markdown-932-green.svg)](https://github.com/bestia-dev/cross_compile_rust_container/)
 [![Lines in bash scripts](https://img.shields.io/badge/Lines_in_bash_scripts-1535-blue.svg)](https://github.com/bestia-dev/cross_compile_rust_container/)

 [![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/bestia-dev/cross_compile_rust_container/blob/master/LICENSE)
 ![cross_compile_rust_container](https://bestia.dev/webpage_hit_counter/get_svg_image/1031674425.svg)

Hashtags: #rustlang #buildtool #developmenttool #tutorial #docker #ssh #container #podman #Linux #OCI  
My projects on GitHub are more like a tutorial than a finished product: [bestia-dev tutorials](https://github.com/bestia-dev/tutorials_rust_wasm).  

## Intro

Welcome to bestia.dev !  
Learning Rust and Wasm programming and having fun.  
I just love programming !  

I upgraded my Containerized Rust Development Environment (CRDE) to enable cross-compilation to Linux, Windows, musl, WASI and WASM.  
Let's make a short tutorial on how it works!

This project has also a youtube video tutorial. Watch it:
<!-- markdownlint-disable MD033 -->
[<img src="https://bestia.dev/youtube/cross_compile_rust_container.jpg?_" width="400px">](https://bestia.dev/youtube/cross_compile_rust_container.html)
<!-- markdownlint-enable MD033 -->

## But it works on my machine !

It is smart to start the development from scratch from time to time to avoid accumulating side effects that we forget about. Then the problem "But it works on my machine!" arise. It is very easy to reset everything because we use containers and virtual machines.

## Install Debian in WSL2 on Windows

We will start on Win10 with enabled WSL2.  
If exists, remove the Debian distro. Be careful to not lose any precious data!  
We will install a fresh Debian distro in Powershell. It takes a minute.

```powershell
wsl --unregister Debian
wsl --install Debian
```

Open Debian bash, update and upgrade, then install curl and OpenSSH.

```bash
cat /etc/debian_version;
   11.6
sudo apt update;
sudo apt upgrade;
cat /etc/debian_version;
   11.7
sudo apt install -y curl;
sudo apt install -y openssh-client;
```

Just for info: Instead of the classic ctrl+c and ctrl+v, to Copy and Paste inside the bash terminal I use ctrl+shift+c and ctrl+shift+v.  
Cumbersome, but similar.  

## Download and run the first bash script

Make a directory, go inside and download the first script.  

```bash
mkdir -p ~/rustprojects/docker_rust_development_install;
cd ~/rustprojects/docker_rust_development_install;
curl -Sf -L https://github.com/bestia-dev/docker_rust_development/raw/main/docker_rust_development_install/download_scripts.sh --output download_scripts.sh;
# You can read the bash script, it only creates the directory structure, downloads scripts and suggests what script to run next.
clear;
cat download_scripts.sh;
clear;
sh download_scripts.sh;
# Read and follow the detailed instructions after every script.
```

## Import SSH keys and personal data

Inside the ssh directory, you will need 2 keys, one to access Github and the second to access your web server virtual machine.
You should already have these keys somewhere and you just need to copy them into the ssh folder.
I will call these keys github_com_ssh_1 and bestia_dev_ssh_1, but you can have other names.
Modify accordingly to your locations and run these commands.

Now you can run the first script with 4 parameters.
Change the parameters with your personal data. They are needed for the container.
The files will be stored in the ssh directory for later use.  
Then follow the instructions from the next script.

## Install Podman and create rust_dev_pod

Now you can install Podman and set up the keys rust_dev_pod_keys.
Give a passphrase to the ssh keys and remember it, you will need it.

When installing Podman and the setup will finish, you can create the pod rust_dev_pod.
On the first run, it will download around 1.2 GB from DockerHub and store it in the cache for later use.
After that, follow the detailed instructions.

The bash script will create the rust_dev_pod.
It contains Rust, cargo, rustc and VSCode development environment.
All outbound network traffic from the container is restricted through the Squid firewall proxy.

To start this 'pod' it is mandatory to run the bash script rust_dev_pod_after_reboot.

```bash
sh ~/rustprojects/docker_rust_development_install/rust_dev_pod_after_reboot.sh
```

Be sure to push your code to GitHub frequently because sometimes containers just stop working.

## VSCode

You can open VSCode directly on an existing project inside the container from the Linux host bash terminal.

```bash
code --remote ssh-remote+rust_dev_vscode_cnt /home/rustdevuser/rustprojects 
```

The fresh empty container works perfectly!

## Compile to standard Linux executable (dynamically linked to glibc)

We will now create a new simple rust project as a proof-of-concept.  
We can compile it for Linux right away.

```bash
cargo auto build
./target/debug/rust_hello upper world
```

Nothing new here. It works like a charm!

## Cross-compile for Windows

Now cross-compile to Windows. Just add the correct target option.
If we need to do this frequently, add it to the automation tasks.

```bash
cargo build --target x86_64-pc-windows-gnu
```

We are connected over ssh and we can download the exe file to Windows.
On Windows, we can run the CLI in Powershell and it works!

## Compile to standalone Linux executable with Musl

If we want to compile and build a true standalone executable for Linux we need to explicitly use the Musl target and statically link to the Musl library.  
That makes it perfect to run inside a scratch Linux OCI container without any dependencies.

```bash
cargo build --target x86_64-unknown-linux-musl
```

## Create an "application" container from scratch

We will download the binary (from VSCode to Windows) and move it to the Debian in WSL where we will use Buildah to create a Linux Container image from scratch.
Using Buildah is very simple. We can run commands step by step and check things in between.
Later we can put these commands inside a bash script and it works perfectly.

```bash
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
podman run scratch_hello_world_img /rust_hello upper world
```

Then I use Podman to run the container. It works!

## Cross-compile to WASI

I will compile for the target wasi now and run it with the wasmtime wasi runtime.

```bash
cargo build --target wasm32-wasi
wasmtime ./target/wasm32-wasi/debug/rust_hello.wasm upper world
```

Everything works!

## Cross-compile to WASM in-browser

WASM runs inside a browser as a library and I cannot use the exact CLI project. 
I need just a few adjustments because of the user interface.

I will use the 'cargo auto new_wasm' command to create a simple yet fully functional wasm program inside the browser.

```bash
cargo auto new_wasm rust_wasm_hello
cargo auto build
basic-http-server -a 0.0.0.0:4000 ./web_server_folder
https://localhost:4000/rust_wasm_hello#upper/world
```

On purpose, the project is made very similar to the CLI we already know. It reads 2 arguments from the URL local hash fragment and interprets it in the same way as the CLI.  
I then use the exact same Rust code for the project logic except for the user interface. The User interface is different and it is smart to isolate it in separate modules. So the logic can be reused.  
The structure of the modules in the wasm project is made similar as possible to the CLI. There are some minor differences though.  
The wasm program needs a basic web server and it then runs entirely inside the browser.

## Outro

We have seen live how cross-compiling works inside the Containerized Rust Development environment. It works nicely!  
This is all for today. Thank you for watching and see you next time.  
Find my code and tutorials on bestia.dev or GitHub.

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
