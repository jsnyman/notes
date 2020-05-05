## RSYNC

[How To Use Rsync to Sync Local and Remote Directories on a VPS](https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories-on-a-vps)


```
rsync [OPTION...] SRC... [DEST]

# PUSH
rsync -a dir1 username@remote_host:destination_directory

#PULL
rsync -a username@remote_host:/home/username/dir1 place_to_sync_on_local_machine


# rsync on bauer
rsync -avP --rsh='ssh -p 1404' jsnyman@bauer.trustwave.com:/home/jsnyman/folder_on_bauer/ local_folder/
```

  * -r : recursively
  * -a : syncs recursively and preserves symbolic links, special and device files, modification times, group, owner, and permissions.
  * -z : use compression before sending
  * -P : combination of --progress (shows progress bar) and --partial (allows resumption)
  * --exclude : comma separated list
  * --include : comma separated
