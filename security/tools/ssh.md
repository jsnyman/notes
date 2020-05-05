# SSH:

https://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html


* Local Port Forwarding:  
  ```
  # ssh <gateway> -L <local port to listen>:<remote host>:<remote port>
  ```

* Remote Port Forwarding:  
  ```
  # ssh <gateway> -R <remote port to bind>:<local host>:<local port>
  ```

* Dynamic Prto Forwarding:  
  ```
  # ssh -D <local proxy port> <target>
  ```
