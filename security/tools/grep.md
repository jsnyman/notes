## Grep

See also [How To Use grep Command In Linux / UNIX](https://www.cyberciti.biz/faq/howto-use-grep-command-in-linux-unix/)


* Syntax
  ```
  grep 'word' filename
  grep 'word' file1 file2 file3
  grep 'string1 string2'  filename
  ```

* Grep multiple patterns
  ```
  grep 'word1\|word2\|word3' /path/to/file
  grep 'wordA*'\''wordB' *.py
  grep -E 'word1|word2' *.doc
  grep -e string1 -e string2 *.pl
  egrep "word1|word2" *.c
  ```

* Grep, ignore case [-i]
  ```
  $ grep -i "boo" /etc/passwd
  ```

* Grep recursively [-r OR -R]
  ```
  $ grep -r "192.168.1.5" /etc/
  $ grep -R "192.168.1.5" /etc/
  ```

* Grep, but suppress output of filename [-h]
  ```
  $ grep -h -R "192.168.1.5" /etc/
  ```

* Grep invert (NOT) [-v]
  ```
  $ grep -v bar /path/to/file
  ```

* Grep and list only filenames [-l]
  ```
  $ grep -l 'main' *.c
  ```
