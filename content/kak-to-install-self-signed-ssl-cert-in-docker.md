+++
title = "Как добавить самоподписной SSL-сертификат для JVM в Docker"
description = "Решение проблемы javax.net.ssl.SSLHandshakeException: PKIX path building failed"
date = 2021-08-10
updated = 2021-08-10

[taxonomies]
tags = ["linux", "docker", "ssl", "java"]
categories = ["linux", "docker"]
+++

Представим что у нас есть Java-приложение, которое делает запросы к другим Web-сервисам. В свою очередь, Web-сервисы работают с HTTPS, но сертификат доверенный только для домена компании.

В логах приложения подобные ошибки:

```
PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
    at sun.security.ssl.Alert.createSSLException(Alert.java:131)
    at sun.security.ssl.TransportContext.fatal(TransportContext.java:324)
    at sun.security.ssl.TransportContext.fatal(TransportContext.java:267)
    at sun.security.ssl.TransportContext.fatal(TransportContext.java:262)
    at sun.security.ssl.CertificateMessage$T12CertificateConsumer.checkServerCerts(CertificateMessage.java:654)
    at sun.security.ssl.CertificateMessage$T12CertificateConsumer.onCertificate(CertificateMessage.java:473)
    at sun.security.ssl.CertificateMessage$T12CertificateConsumer.consume(CertificateMessage.java:369)
    at sun.security.ssl.SSLHandshake.consume(SSLHandshake.java:377)
    at sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:444)
    at sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:422)
    at sun.security.ssl.TransportContext.dispatch(TransportContext.java:182)
    at sun.security.ssl.SSLTransport.decode(SSLTransport.java:152)
```

Нам необходимо добавить самоподписной корневой сертификат в хранилище сертификатов JVM.

В JVM сертификаты хранятся в файле-хранилище `${JAVA_HOME}/jre/lib/cacerts`.

Находим где располагается JVM, для этого заходим в контейнер приложения:

```
docker exec -it app /bin/bash
```

и ищем:

```
root@458b6bbf28b5:/app# whereis java
java: /usr/local/openjdk-8/bin/java
```

Соответственно, наш `JAVA_HOME` это `/usr/local/openjdk-8`.

Теперь выходим из контейнера и копируем каталог `security`:

```
docker cp app:/usr/local/openjdk-8/jre/lib/security .
```

Правим `docker-compose.yml` для проброса каталога внутрь контейнера:

```
version: '3.3'

services:

  app:
    container_name: app
    image: registry.company.ru/app:1.3.7-12
    restart: always
    volumes:
     - ./application.properties:/app/application.properties
     - ./data:/app/data
     - ./logs:/app/logs
     - ./security:/usr/local/openjdk-8/jre/lib/security
    ports:
     - "35000:8080"
```

Добавляем сертификат в файл `cacerts`:

```bash
keytool -importcert -keystore security/cacerts -file custom-ca.crt -alias "custom-ca"
```

Пароль по умолчанию для доступа в хранилище `changeit`.

Рестартуем приложение:

```bash
docker-compose down
docker-compose up -d
```
