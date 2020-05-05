# nmap

### Scripting

* Getting a list of the safe scripts that would be run in nmap
  ```
  nmap --script-updatedb ; NSCRIPTS='all'; NL=$'\n'; nmap --script-help "$NSCRIPTS" -oX - | xmlstarlet sel -T -t -m nse-scripts/script -v "substring-before(substring-after(@filename,'/scripts/'),'.nse')" -o $'\t' -m categories/category -v . -o ' ' -b -o $'\t' -v "substring-before(description,'$NL')" -n- | sort -u > nmap_script_info.tsv
  ```

* OS Discovery via SMB
  ```
  # nmap 10.0.0.19 --script smb-os-discovery.nse
  ```

* DNS zone transfer
  ```
  # nmap --script=dns-zone-transfer -p 53 ns2.megacorpone.com
  ```
