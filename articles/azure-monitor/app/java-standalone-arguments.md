---
title: Java için JVM arg-Azure Izleyici Application Insights ekleme
description: Java için Azure Izleyici Application Insights sağlayan JVM bağımsız değişkeni nasıl eklenir
ms.topic: conceptual
ms.date: 04/16/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 9e824af23fd83725b426d5860186d3c4b1496e18
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448908"
---
# <a name="adding-the-jvm-arg---azure-monitor-application-insights-for-java"></a>Java için JVM arg-Azure Izleyici Application Insights ekleme



## <a name="azure-environments"></a>Azure ortamları

[Uygulama hizmetleri](../../app-service/configure-language-java.md#set-java-runtime-options)'ni yapılandırın.

## <a name="spring-boot"></a>Spring Boot

Daha önce bir yere JVM bağımsız değişkeni ekleyin `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` `-jar` , örneğin:

```
java -javaagent:path/to/applicationinsights-agent-3.0.3.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Docker giriş noktası aracılığıyla Spring Boot

*Exec* formunu kullanıyorsanız, parametresini parametre `"-javaagent:path/to/applicationinsights-agent-3.0.3.jar"` listesine parametresinden önce bir yerde ekleyin `"-jar"` , örneğin:

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.3.jar", "-jar", "<myapp.jar>"]
```

*Kabuk* formunu kullanıyorsanız, daha önce bir yere JVM bağımsız değişkeni ekleyin `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` `-jar` , örneğin:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.3.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Veya ile yüklenen Tomcat `apt-get``yum`

Veya aracılığıyla Tomcat yüklediyseniz `apt-get` `yum` , bir dosyanız olmalıdır `/etc/tomcat8/tomcat8.conf` .  Bu satırı Bu dosyanın sonuna ekleyin:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.3.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat, indirme ve unzip ile yüklendi

İndirme ve unzip aracılığıyla Tomcat 'i yüklediyseniz [https://tomcat.apache.org](https://tomcat.apache.org) , bir dosyanız olmalıdır `<tomcat>/bin/catalina.sh` .  Aşağıdaki içerikle adlı aynı dizinde yeni bir dosya oluşturun `<tomcat>/bin/setenv.sh` :

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.3.jar"
```

Dosya `<tomcat>/bin/setenv.sh` zaten varsa, bu dosyayı değiştirip `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` öğesine ekleyin `CATALINA_OPTS` .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Komut satırından Tomcat çalıştırma

Dosyayı bulun `<tomcat>/bin/catalina.bat` .  Aşağıdaki içerikle adlı aynı dizinde yeni bir dosya oluşturun `<tomcat>/bin/setenv.bat` :

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.3.jar
```

Tırnak işaretleri gerekli değildir, ancak bunları dahil etmek istiyorsanız, doğru yerleştirme şu şekilde olur:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.3.jar"
```

Dosya `<tomcat>/bin/setenv.bat` zaten varsa, bu dosyayı değiştirip `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` öğesine ekleyin `CATALINA_OPTS` .

### <a name="running-tomcat-as-a-windows-service"></a>Windows hizmeti olarak Tomcat çalıştırma

Dosyayı bulun `<tomcat>/bin/tomcat8w.exe` .  Bu çalıştırılabiliri çalıştırın ve `-javaagent:path/to/applicationinsights-agent-3.0.3.jar` `Java Options` sekmesinde altına ekleyin `Java` .


## <a name="jboss-eap-7"></a>Jpatron EAP 7

### <a name="standalone-server"></a>Tek başına sunucu

`-javaagent:path/to/applicationinsights-agent-3.0.3.jar` `JAVA_OPTS` Dosyadaki `JBOSS_HOME/bin/standalone.conf` (Linux) veya (Windows) var olan ortam değişkenine ekleyin `JBOSS_HOME/bin/standalone.conf.bat` :

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.3.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Etki alanı sunucusu

`-javaagent:path/to/applicationinsights-agent-3.0.3.jar`' De var olan öğesine ekleyin `jvm-options` `JBOSS_HOME/domain/configuration/host.xml` :

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.3.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Tek bir konakta birden çok yönetilen sunucu çalıştırıyorsanız, `applicationinsights.agent.id` `system-properties` her birine için öğesine eklemeniz gerekir `server` :

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

Belirtilen `applicationinsights.agent.id` değer benzersiz olmalıdır. Her JVM işlemi kendi yerel ApplicationInsights yapılandırması ve yerel ApplicationInsights günlük dosyasına ihtiyaç duyduğunda, ApplicationInsights dizini altında bir alt dizin oluşturmak için kullanılır. Ayrıca, merkezi toplayıcıya raporlama yapıyorsanız, `applicationinsights.properties` dosya birden çok yönetilen sunucu tarafından paylaşılır ve bu nedenle, `applicationinsights.agent.id` `agent.id` Bu paylaşılan dosyadaki ayarı geçersiz kılmak için belirtilen gereklidir. `applicationinsights.agent.rollup.id` , `system-properties` `agent.rollup.id` yönetilen sunucu başına ayarı geçersiz kılmanız gerekiyorsa sunucuda benzer şekilde belirtilebilir.


## <a name="jetty-9"></a>Jetty 9

Bu satırları Ekle `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.3.jar
```


## <a name="payara-5"></a>Payara 5

`-javaagent:path/to/applicationinsights-agent-3.0.3.jar`' De var olan öğesine ekleyin `jvm-options` `glassfish/domains/domain1/config/domain.xml` :

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.3.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>Web küre 8

Yönetim konsolunu açın **sunucu > Web Sphere uygulama sunucuları > uygulama sunucuları**' na gidin, uygun uygulama sunucularını seçin ve açık ' a tıklayın: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
"Genel JVM bağımsız değişkenleri" içinde aşağıdakileri ekleyin:
```
-javaagent:path/to/applicationinsights-agent-3.0.3.jar
```
Bundan sonra uygulama sunucusunu kaydedin ve yeniden başlatın.


## <a name="openliberty-18"></a>OpenLiberty 18

Sunucu dizininde yeni bir dosya oluşturun `jvm.options` (örneğin `<openliberty>/usr/servers/defaultServer` ) ve şu satırı ekleyin:
```
-javaagent:path/to/applicationinsights-agent-3.0.3.jar
```
