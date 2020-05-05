
* Azure Analysis Services has a address like:
```
<protocol>://<region>/<servername>
```
  See [Azure / Analysis Services](https://docs.microsoft.com/en-us/azure/analysis-services/analysis-services-connect)


## Testing Azure

1. ScoutSuite - NCC Group
  * https://github.com/nccgroup/ScoutSuite
  * Support: Azure (early alpha) !!

  ```
  pip install scoutsuite

  ```

2. Azucar - NCC Group
  * Security auditing tool for Azure environments
  * https://github.com/nccgroup/azucar
  * PowerShell script
  ```
  git clone https://github.com/nccgroup/azucar.git
  ```

  * Before to start, you need to unblock files. Once you have unzipped the zip file, you can use the fantastic PowerShell V3 Unblock-File cmdlet that will do this task for you:
  ```
  Get-ChildItem -Recurse c:\Azucar_V10 | Unblock-File
  ```


3. MicroBurst - NetSPI
  * A collection of scripts for assessing Microsoft Azure security
  * https://github.com/NetSPI/MicroBurst
  * https://github.com/NetSPI/MicroBurst.gi
