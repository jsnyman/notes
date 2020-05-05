# Checking Security Issues in JavaScript

* My Docker instance
```
$ cat Dockerfile
FROM node:10.9-alpine

RUN apk add --update bash \
  && rm -rf /var/cache/apk/* \
  && npm install npm@latest -g \
  && npm i -g eslint eslint-plugin-standard eslint-plugin-import eslint-plugin-node eslint-plugin-promise eslint-config-standard eslint-config-semistandard \
  && npm i -g eslint-plugin-scanjs-rules \
  && npm i -g eslint-plugin-no-unsanitized \
  && npm i -g eslint-plugin-prototype-pollution-security-rules \
  && npm i -g eslint-plugin-angularjs-security-rules \
  && npm i -g eslint-plugin-react \
  && npm i -g eslint-plugin-security \
  && npm i -g eslint-plugin-no-wildcard-postmessage \
  && npm i -g eslint-plugin-no-unsafe-innerhtml \
  && npm i -g eslint-config-nodesecurity \
  && npm i -g eslint-config-scanjs

CMD /usr/lib/node_modules/.bin/eslint
```

* Building the images:
```
$ docker build -t eslint-sec:0.1 .ls
Sending build context to Docker daemon  4.096kB
Step 1/3 : FROM node:10.9-alpine
...
```

* To interact with this docker image bash:
```
$ docker run -it --rm eslint-sec:0.1 /bin/bash    OR
$ docker run -it --rm --privileged eslint-sec:0.1 /bin/bash    When privileges are needed
```

* The command that worked last on the Ubuntu VM:
```
$ docker run -it --rm -v `pwd`:/app eslint-sec:0.1 eslint -c app/light.js app/tmp.js

$ docker run -it --rm -v `pwd`:/app eslint-sec:0.1 "eslint -c app/light.js app/tmp.js"
```



## Talk on security / eslint

https://www.slideshare.net/LewisArdern/owasp-sf-reviewing-modern-javascript-applications-130932537

- Need to utilize developer tools to catch security vulnerabilities.

- Tools looking for issues
  * Retire.js
  * npm audit
  * yarn audit
  * GitHub
  * Snyk
  * auditjs

- Deobfuscators
  * Closure Compiler
  * JStillery
  * unminify


### ESLint

```
brew install npm
npm i -g eslint eslint-plugin-standard eslint-plugin-import eslint-plugin-node eslint-plugin-promise eslint-config-standard eslint-config-semistandard
npm i -g eslint-plugin-scanjs-rules
npm i -g eslint-plugin-no-unsanitized
npm i -g eslint-plugin-prototype-pollution-security-rules
npm i -g eslint-plugin-angularjs-security-rules
npm i -g eslint-plugin-react
npm i -g eslint-plugin-security
npm i -g eslint-plugin-no-wildcard-postmessage
```
#### Default Security Rule Configs

* NodeJS: [eslint-config-nodesecurity](https://github.com/nodesecurity/eslint-config-nodesecurity) - This is the base set of eslint rules for all nodesecurity projects
* VanillaJS: [eslint-config-scanjs](https://github.com/mozfreddyb/eslint-config-scanjs) - umbrella config to achieve scanjs-like functionality through eslint
* AngularJS: [eslint-plugin-angularjs-security-rules](https://github.com/LewisArdern/eslint-plugin-angularjs-security-rules) - Rules for detecting security issues in Angular 1.x
* ReactJS: [List of supported rules](https://github.com/yannickcr/eslint-plugin-react#list-of-supported-rules) - List of supported rules

#### Security Rules
* [eslint-config-scanjs](https://github.com/mozfreddyb/eslint-config-scanjs)
* [eslint-plugin-security](https://github.com/nodesecurity/eslint-plugin-security)
* [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react)
* [eslint-plugin-angularjs-security-rules](https://github.com/LewisArdern/eslint-plugin-angularjs-security-rules)
* [eslint-plugin-no-wildcard-postmessage](https://www.npmjs.com/package/eslint-plugin-no-wildcard-postmessage)
* [eslint-plugin-no-unsafe-innerhtml](https://www.npmjs.com/package/eslint-plugin-no-unsafe-innerhtml)
* [eslint-plugin-prototype-pollution-security-rules](https://www.npmjs.com/package/eslint-plugin-prototype-pollution-security-rules)



* How I (attempted to / ) created docker instance??
```
FROM node:10.9-alpine

RUN apk add --update bash \
  && rm -rf /var/cache/apk/* \
  && npm install npm@latest -g \
  && npm i -g
  eslint
  eslint-plugin-standard
  eslint-plugin-import
  eslint-plugin-node
  eslint-plugin-promise
  eslint-config-sta$
&& npm i -g eslint-plugin-scanjs-rules \
&& npm i -g eslint-plugin-no-unsanitized \
&& npm i -g eslint-plugin-prototype-pollution-security-rules \
&& npm i -g eslint-plugin-angularjs-security-rules \
&& npm i -g eslint-plugin-react \
&& npm i -g eslint-plugin-security \
&& npm i -g eslint-plugin-no-wildcard-postmessage  

CMD /usr/lib/node_modules/.bin/eslint


bash-4.4# npm -g ls --depth=0
/usr/local/lib
+-- eslint@5.16.0
+-- eslint-config-semistandard@13.0.0
+-- eslint-config-standard@12.0.0
+-- eslint-plugin-angularjs-security-rules@1.0.6
+-- eslint-plugin-import@2.16.0
+-- eslint-plugin-no-unsanitized@3.0.2
+-- eslint-plugin-no-wildcard-postmessage@0.1.3
+-- eslint-plugin-node@8.0.1
+-- eslint-plugin-promise@4.1.1
+-- eslint-plugin-prototype-pollution-security-rules@1.0.6
+-- eslint-plugin-react@7.12.4
+-- eslint-plugin-scanjs-rules@0.2.1
+-- eslint-plugin-security@1.4.0
+-- eslint-plugin-standard@4.0.0
`-- npm@6.9.0
```
