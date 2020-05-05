# Android

## What to test

* Can activities in AndroidManifest.xml be started from command line, do they crash the app?  
  *Null Intent Fuzzing of Broadcast Activities*  

* What is logged to the system log
  *Sensitive Data Logged to Device Console*




## ADB

### ADB Commands

* Start an activity **am**
```
am start -n yourpackagename/.activityname
```

* View the system log
```
$ adb shell logcat

OR
$ adb shell
# logcat
```
