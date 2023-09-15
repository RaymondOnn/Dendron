---
id: 18o45o02qk5zkz1d51cohsj
title: Linux
desc: ''
updated: 1692162973615
created: 1692059913921
---

https://linuxsurvival.com/linux-home-directories/
CLI Commands
```bash
# navigation
cd <folder_name>         # change directory
cd ..                    # shift up one level of directory
pwd                      # print working directory
clear                    # clear up display
history


# files
ls                        # list directories
ls -l                     # list directories, with permissions (long format)
more <file_name>          # view file. spacebar for next page
cat <file_name>           # print file contents
head <file_name>          # print first 10 lines
tail <file_name>          # print last 10 lines
less <file_name>          # print file contents in small chunks
    q   # Used to quit out of less and go back to your shell.
    Page up, Page down, Up and Down     # Navigate using the arrow keys and page keys.
    g   # Moves to beginning of the text file.
    G   # Moves to the end of the text file.
    /search # You can search for specific text inside the text document. Prefacing the words you want to search with /
    h   # If you need a little help about how to use less while you’re in less, use help.
touch <file_name>         # create file
rm <file_name>            # delete file
mv <file_name> <new_location>  # move files/folders to new location
cp <file_name> <new_location>  # copy files/folders to new location
# folders
mkdir <folder_name>       # create folder
rmdir <folder_name>       # delete folder


# relative paths
/                        # root (lowest)
..                      # parent directory (one level up)

# properties
(rwx)
# eg. rw- means can read, write but not execute 
r          # can read
w          # can write
x          # can execute
-          # not allowed

# change mode to edit file permissions
chmode [u/g/o][+/-][rwx] <file_name>  
# [u/g/o] : whose permission (Owner/Group/the Rest)
# [+/-]: add/remove permissions
# [rwx]: type of permission (read/write/execute)  
groups     # view my own access control list

# wildcards
*          # multi character wildcard
?          # single character wildcard    


curl <URL>                   # Client URL: data transfer to/from url
curl <url> > <local-file>    # download file 
sudo <command>              # SUperuser DO: run command with admin rights

```