---
title: "Most Used Linux Command"
date: 2015-12-01T10:41:58+02:00
draft: true
tags: ["linux", "vim", "nano"]
categories: ["devops"]
---

# Limit Bandwidth, simulate slow network

    $ sudo wondershaper {interface} {down} {up}

the {down} and {up} are bandwidth in kilobits. ,

    $ sudo wondershaper wlan0 1000 1000

To clear the limit,

    $ sudo wondershaper clear wlan0

http://jwalanta.blogspot.fr/2009/04/easy-bandwidth-shaping-in-linux.html

list all interface: 

    ifconfig
    netstat -i
    ip link show

# Suspend

    pm-suspend
    pm-hibernate

# System Info

    top
    df -h
    sudo lshw

# List order by last modified last

    ls -altr

# Vim

| Goto End of File | **G** |
| Goto Line | n**G** |
| Show Line Number | **:set nu** |

Fast move (word): <--**b** **w**-->

## Search

| Search Forward | **/**text |
| Search Backward | **?**text |
| Next | **n** |
| Previous | **N** |

## Copy Paste Cut

| Select Text | **v** | (**V**: select line) |
| Copy (yank to buffer) | **y** |
| Paste | **p** |
| Cut | **d** |

## Undo

| Undo | **u** | 3**u**: undo 3 times |
| Redo | **Ctrl+R** |

## File

| Reload file | **:e!** |
| Exit Discard | **:q!** |
| Save | **w** |

**!** means discard

# Nano

|~ On French Keyboard |~ Real Shortcut |~ Functions |
| Alt+A | Meta+A | Selection |
| Alt+Shift+6 | Meta+6 | Copy |
| Ctrl+U |   | Paste |
| Ctrl+W |   | Search |
| Alt+W |   | Last Search (Find Next) |
| Ctrl+K |   | Cut |
| ESC, { or } | ident or outdent | Cut |

Meta = Alt or ESC