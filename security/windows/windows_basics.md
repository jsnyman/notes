# Windows Basics

## Versions of Windows


* Due to Windows irregular way of naming their operating systems it can be a bit hard to keep track on. So here is a list of the desktop OS, and then a list of Servers.

* Two ways to get this info:
  1. Command
  ```
  c:\> ver
  ```

  2. PowerShell  
  ```
  C:\> [System.Environment]::OSVersion.Version
  ```

* Version numbers are given in the format `[Major].[Minor].[Build](.Revision)`  
  See [THIS RESOURCE](https://docs.microsoft.com/en-us/windows/desktop/api/winnt/ns-winnt-_osversioninfoexa)

* For Versions also se41
......e https://en.wikipedia.org/wiki/Comparison_of_Microsoft_Windows_versions

#### Windows Desktops OS

```
Operating System 	Version Number

Windows 1.0                    1.04
Windows 2.0                    2.11
Windows 3.0                    3
Windows NT 3.1                 3.10.528
Windows for Workgroups 3.11    3.11
Windows NT Workstation 3.5 	3.5.807
Windows NT Workstation 3.51    3.51.1057
Windows 95                     4.0.950
Windows NT Workstation 4.0     4.0.1381
Windows 98                     4.1.1998
Windows 98 Second Edition      4.1.2222
Windows Me                     4.90.3000
Windows 2000 Professional      5.0.2195
Windows XP                     5.1.2600
Windows Vista                  6.0.6000
Windows 7                      6.1.7600
Windows 8.1                    6.3.9600
Windows 10                     10.0.10240
```

#### Windows Server

```
Windows NT 3.51                  NT 3.51
Windows NT 3.5                   NT 3.50
Windows NT 3.1                   NT 3.10
Windows 2000                     NT 5.0 	

    Windows 2000 Server
    Windows 2000 Advanced Server
    Windows 2000 Datacenter Server

Windows NT 4.0                   NT 4.0 	

    Windows NT 4.0 Server
    Windows NT 4.0 Server Enterprise
    Windows NT 4.0 Terminal Server Edition

Windows Server 2003              NT 5.2 	

    Windows Small Business Server 2003
    Windows Server 2003 Web Edition
    Windows Server 2003 Standard Edition
    Windows Server 2003 Enterprise Edition
    Windows Server 2003 Datacenter Edition
    Windows Storage Server

Windows Server 2003 R2           NT 5.2 	

    Windows Small Business Server 2003 R2
    Windows Server 2003 R2 Web Edition
    Windows Server 2003 R2 Standard Edition
    Windows Server 2003 R2 Enterprise Edition
    Windows Server 2003 R2 Datacenter Edition
    Windows Compute Cluster Server 2003 (CCS)
    Windows Storage Server
    Windows Home Server

Windows Server 2008               NT 6.0 	

    Windows Server 2008 Standard
    Windows Server 2008 Enterprise
    Windows Server 2008 Datacenter
    Windows Server 2008 for Itanium-based Systems
    Windows Server Foundation 2008
    Windows Essential Business Server 2008
    Windows HPC Server 2008
    Windows Small Business Server 2008
    Windows Storage Server 2008
    Windows Web Server 2008

Windows Server 2008 R2            NT 6.1 	

    Windows Server 2008 R2 Foundation
    Windows Server 2008 R2 Standard
    Windows Server 2008 R2 Enterprise
    Windows Server 2008 R2 Datacenter
    Windows Server 2008 R2 for Itanium-based Systems
    Windows Web Server 2008 R2
    Windows Storage Server 2008 R2
    Windows HPC Server 2008 R2
    Windows Small Business Server 2011
    Windows MultiPoint Server 2011
    Windows Home Server 2011
    Windows MultiPoint Server 2010

Windows Server 2012               NT 6.2 	

    Windows Server 2012 Foundation
    Windows Server 2012 Essentials
    Windows Server 2012 Standard
    Windows Server 2012 Datacenter
    Windows MultiPoint Server 2012

Windows Server 2012 R2            NT 6.3 	

    Windows Server 2012 R2 Foundation
    Windows Server 2012 R2 Essentials
    Windows Server 2012 R2 Standard
    Windows Server 2012 R2 Datacenter

Windows Server 2016 	2016       NT 10.0 	
```

## Important Files

* See also [local file inclusion](local_file_inclusion.md)

```
C:\Windows\win.ini
* Hashes are stored in the Windows SAM file. This file is located on your system at
C:\Windows\System32\config
C:\Windows/system32/config/RegBack/SAM.OLD
C:\Windows/system32/config/RegBack/SYSTEM.OLD
autoexec.bat
config.sys
io.sys
```

## Access Control List (ACL)
This is a list of permissions attached to an object. An ACL specifies which users or system processes are granted access to objects, as well as what operations are allowed on given objects.
```
An access control list (ACL) is a list of access control entries (ACE). Each ACE in an ACL identifies a trustee and specifies the access rights allowed, denied, or audited for that trustee.
```

See also: [Checking ACL's](windows_command_line.md -> Check Access)  
windows_command_line.md -> Check Access

## Networking

**Domains** and **workgroups** are two different methods for organizing computers in a network. The main difference between the two is in the manner they operate, chiefly how the computers and other resources on the networks are managed.

A **workgroup** is a type of **peer-to-peer** network. It is essentially the name for a Windows based peer-to-peer computer network. Computers in this kind of network can allow each other access to their files, printers, or Internet connection. In order to do this, every user must have an account on each of the workgroup’s computers that they require access to. These settings and access are managed by each computer’s user.

A **domain**, on the other hand, is a **client/server** network in which the security and resource management is centralized. This means that a singular administration (*domain controller*) has control over the domain and allows which users have access to which files. In a domain, there is a one single database for domain users. A user can log on at any workstation via their account and access the files.

#### Active Directory

**Active Directory** (AD) is a directory service that Microsoft developed for the Windows domain networks. It is a set of processes and services. A server running **Active Directory Domain Services (AD DS)** is called a **domain controller**. It authenticates and authorizes all users and computers in a Windows domain type network—assigning and enforcing security policies for all computers and installing or updating software.  

A **domain** is defined as a logical group of network objects (computers, users, devices) that share the same Active Directory database.


Active Directory uses **Lightweight Directory Access Protocol (LDAP)** versions 2 and 3, Microsoft's version of **Kerberos**, and **DNS**.  
Within a deployment, objects are grouped into domains. The objects for a single domain are stored in a single database (which can be replicated). Domains are identified by their DNS name structure, the **namespace**.  

A **tree** is a collection of one or more domains and domain trees in a **contiguous namespace**, and is linked in a transitive trust hierarchy.  

At the top of the structure is the **forest**. A **forest** is a collection of trees that share a common global catalog, directory schema, logical structure, and directory configuration. The forest represents the security boundary within which users, computers, groups, and other objects are accessible.





#### DNS, LLMNR & NetBIOS

References:  
--> [Windows Command Line: DNS, LLMNR & NetBIOS](windows_command_line.md)  
[Understanding DNS, LLMNR, and NETBIOS Name Resolution - MCTS 70-642 Training (YouTube)](https://www.youtube.com/watch?v=gzqjeds8gDg)
