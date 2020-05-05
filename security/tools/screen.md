* Create a named screen session:
```
$ screen -S [SESSION_NAME]
```

* Connect to a screen session belonging to someone else:
```
$ screen -x [SESSION_NAME]
$ sudo screen -x [SESSION_NAME]
```

* Renaming a session
  - Type `Ctrl`, `a`, `:sessionname` to get the current session name
  - Type `Ctrl`, `a`, `:sessionname <new_session_name>` to change current session name
```
Ctrl-a
:sessionname <NewSessionName>
```

* To quit a session
```
$ screen -XS [session # you want to quit] quit
```

---
#### References
[Screen Basics](https://kb.iu.edu/d/acuy)  
[Reattaching to a screen session](https://kb.iu.edu/d/ahrm)  
[Renaming a screen session](https://superuser.com/questions/370510/rename-screen-session)  
