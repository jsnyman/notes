### Guest Additions
* Install on later versions  
  "You must use version 4.2.xx or higher of VirtualBox in order to take advantage of the improvements, including compatibility updates, and enhanced stability of both the core application and the Guest Additions."
  ```
  apt-get update
  apt-get install -y virtualbox-guest-x11
  reboot
  ```

### Mac OS X crashing problem
* Reported here:
  VirtualBox crashes after upgrading to macOS Catalina (#18990)  
  https://forums.virtualbox.org/viewtopic.php?f=8&t=95041  

* Solution here:
  https://www.virtualbox.org/ticket/18990

  - In CLI
  ```
  VBoxManage setextradata global GUI/HidLedsSync 0
  VirtualBox
  ```
