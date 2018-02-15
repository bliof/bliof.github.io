---
layout: post
title:  "Настройка на електронен подпис от InfoNotary"
date:   2018-02-03 00:30:00 +0200
categories: bg
tags: [notes]
---

### tl;dr

* Записвам `opensc`, може би `pcscd` ако няма

* Драйверите от [wiki-то на InfoNotary](http://wiki.infonotary.com/)

* В Firefox → `about:preferences#privacy` → `Security Devices` добавям `onepin-opensc-pkcs11.so`

```bash
brew -> /usr/local/Cellar/opensc/0.17.0/lib/onepin-opensc-pkcs11.so
```

* В Firefox → `about:preferences#privacy` → добавям [InfoNotary CSP Root](http://repository.infonotary.com/files/certificates/INotaryCertChain-MacOS.p7b) → trust за имейли

### Друго:

* [Сертификати на InfoNotary](http://www.infonotary.com/site/?p=doc_g1_3)
* [OpenSC](https://github.com/OpenSC/OpenSC)
