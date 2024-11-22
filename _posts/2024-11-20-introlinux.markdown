---
layout: post
title:  "Getting Started with Linux"
description: Explanations and thoughts
date:   2024-11-20
tags: ["Blog", "Linux", "Beginner"]
category: [Blog]
---

## What is Linux?

Linux is an open-source operating system kernel. It's the core part of various operating systems, known as **distributions** or **distros**.

Unlike proprietary operating systems like Windows and macOS, Linux is collaboratively developed by a global community. Because it's open-source, it's easier to modify, view, and share, offering users more freedom.

## Where Linux excels
- **Servers**: It's stable, efficient, and scalable, making it the go-to choice for most websites and cloud services.
- **Development**: It has native support for many programming languages and development tools.
- **Security**: Linux is modular and has a strong permission-based security model, making it less susceptible to malware.
- **Performance**: Linux is resource-efficient, making it ideal for older hardware.
- **Freedom**: You own your system and can do with it as you wish, all for free.

## What's a Distro?

A **distro** (short for distribution) is a package that includes the Linux kernel along with additional software such as:

- [Desktop environments](https://en.wikipedia.org/wiki/Desktop_environment)
- [Package managers](https://en.wikipedia.org/wiki/Package_manager)
- Utilities to help with daily tasks and user experience

Choosing a Linux distro is like deciding what type of house you want to live in. Do you want to build everything yourself from scratch? Do you want to customize a ready-made house? Or do you just want a fully-furnished house with no effort? Similarly, distros offer different levels of customization and user experience.

Here are some examples:

- **Ubuntu**: User-friendly and beginner-friendly.
- **Kali Linux**: Pre-installed with tools for penetration testing.
- **Debian**: Stable and versatile, great for servers.
- **Fedora**: Cutting-edge with a strong community focus.
- **Arch**: A DIY (Do-It-Yourself) approach for advanced users.

### Types of Distros

Distros can be categorized based on their **stability** and **release cycle**:

1. **Stable**
   - Focus on reliability and tested software.
   - Great for users who want a plug-and-play experience.
   - Example: **Debian Stable**, **Ubuntu LTS**, **CentOS**.

2. **Unstable**
   - Focus on providing the latest software but with fewer tests.
   - More prone to bugs, but perfect for developers who want the latest tools.
   - Example: **Debian Unstable (Sid)**, **Fedora Rawhide**, **Arch Linux**.

There are also **subcategories** such as *Rolling Release*, *Fixed Release*, *Testing*, and *Specialized*. But for now, think of distros as either **stable** or **unstable**, as these are the primary categories.

## What's a Package Manager?

A **package manager** is a tool that helps you install, update, configure, and remove software from your Linux system. It's essentially a huge library of programs.

For example, on Windows, you might search for Firefox, download a `.exe` file, and run the installer. On Ubuntu, however, you can simply open a terminal and run a command like `sudo apt install firefox`.

### Popular Package Managers

Linux distros typically use specific package managers, based on their family tree. Here are some of the most common:

- **apt**: Used by Debian-based distros (e.g., Ubuntu, Mint).
- **dnf**: Used by Red Hat-based distros (e.g., Fedora, RHEL).
- **pacman**: Used by Arch-based distros (e.g., Arch, Manjaro).

Because Linux is open-source, many distros are based on each other and share package managers. For example, many distros are derived from **Debian**, and thus use **apt**. Here’s a simplified **Debian Family Tree**:

```
Debian (The Base)
├── Ubuntu
│   ├── Linux Mint
│   │   ├── LMDE (Linux Mint Debian Edition)
│   ├── Pop!_OS
│   ├── Elementary OS
│   ├── Zorin OS
│   ├── Kubuntu
│   ├── Xubuntu
│   ├── Lubuntu
│   ├── Ubuntu Studio
│   ├── KDE Neon
│   ├── Bodhi Linux
│   ├── Trisquel
│   ├── Linux Lite
│   ├── Voyager
│   └── Regolith Linux
├── Kali Linux
│   ├── Parrot OS
│   └── BackBox
├── Raspbian (Now Raspberry Pi OS)
│   ├── Twister OS
│   └── DietPi
├── Devuan (Debian without systemd)
│   ├── Refracta
│   └── Heads
├── MX Linux
│   ├── antiX
│   └── AV Linux
├── PureOS
├── Knoppix
├── Deepin (Base for Deepin OS distributions)
│   └── UOS (China-focused)
├── SparkyLinux
├── Q4OS
├── Siduction
└── BunsenLabs
```


While this might seem overwhelming, it’s actually easy to switch between Debian-based distros, since they share many similarities.

## Explaining Package Managers

Let’s take a closer look at three common package managers:

1. **apt (Advanced Package Tool)**
   - **Used by**: Debian-based distros (Ubuntu, Mint, Debian, etc.)
   - **Purpose**: Manages `.deb` packages (software)
   - **Features**:
     - Easy to use
     - Extensive software repositories
     - Works alongside `dpkg`
   - **Example Command**: `sudo apt install firefox`

2. **dnf (Dandified Yum)**
   - **Used by**: Red Hat-based distros (Fedora, RHEL, Alma, etc.)
   - **Purpose**: Manages software in `.rpm` format
   - **Features**:
     - Faster package management than older tools like Yum
     - Strong dependency resolution
   - **Example Command**: `sudo dnf install firefox`

3. **pacman**
   - **Used by**: Arch-based distros (Arch, Manjaro)
   - **Purpose**: Manages packages in `.pkg` format
   - **Features**:
     - Simple and lightweight
     - Powerful package handling, ideal for experienced users
   - **Example Command**: `sudo pacman -S firefox`

## Explaining Desktop Environments

In short, a desktop environment (or DE) is a collection of software that will provide you with a GUI to interact with your system. It'll be icons, windows, menus, and more. As well as tools for file management, system settings, etc. Think of it as the "look and feel" of your operating system.

There is an entire [subreddit](https://www.reddit.com/r/unixporn/) dedicated to sharing different DE customizations (or "rices"). I recommend quickly giving it a look, you'll start to understand how customizable Linux is.

## Picking a Distro

Personally, I like to recommend beginners to a **Debian-based** distro. This is because they're stable and common. Let's go over some Debian-based distros and their advantages:


**Ubuntu**
   - **Advantages**:
     - **User-friendly**: Great for beginners with a large community and lots of tutorials.
     - **Long-Term Support (LTS)**: LTS versions get updates for 5 years, making it ideal for those who want stability.
     - **Large software repository**: Easy access to a wide range of software and applications.
   - **Disadvantages**:
     - **Heavy on system resources**: The default GNOME desktop environment can be resource-intensive, especially on older hardware.
     - **Snap packages**: The use of Snap packages can be slower and consume more disk space compared to traditional `.deb` packages.

---

**Linux Mint**
   - **Advantages**:
     - **Beginner-friendly**: Designed to be easy for those new to Linux, especially with a familiar Windows-like interface.
     - **Customizable desktop environments**: Comes with Cinnamon, MATE, and Xfce, giving users options for their desktop setup.
     - **Out-of-the-box functionality**: Includes a lot of essential software by default, making it ready to use right away.
   - **Disadvantages**:
     - **Not as cutting-edge**: Mint tends to lag behind Ubuntu in terms of software updates, as it focuses on stability.
     - **Less focus on innovation**: While stable, it doesn't always include the latest features compared to more rolling-release distros.

---

**Debian**
   - **Advantages**:
     - **Stability**: Known for its rock-solid stability, especially in its "Stable" release.
     - **Customizable**: You can install only the components you need, making it lightweight and versatile.
     - **Large repository**: Debian has one of the most extensive software repositories available.
   - **Disadvantages**:
     - **Older software**: Debian's stable release often has older software versions compared to other distros, as packages are thoroughly tested before release.
     - **Not as beginner-friendly**: It can be more challenging for beginners, as the installation and configuration might require more manual intervention.


> This is the part where I have to clarify that these are *my* opinions, this post was made with the intent of saving my friends from unnecessary elitism, confusion, and suffering.
{: .prompt-info}
