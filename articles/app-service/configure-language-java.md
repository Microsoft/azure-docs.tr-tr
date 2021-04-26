---
title: Java uygulamalarını yapılandırma
description: Java uygulamalarını Azure App Service üzerinde çalışacak şekilde nasıl yapılandıracağınızı öğrenin. Bu makalede en yaygın yapılandırma görevlerine yer verilmiştir.
keywords: Azure App Service, Web uygulaması, Windows, Oss, Java, Tomcat, jpatron
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 134ac04c4f6fb5f0e38a868adc735fc816fbc875
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829521"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Azure App Service için bir Java uygulaması yapılandırma

Azure App Service, Java geliştiricilerinin, tam olarak yönetilen bir hizmette Java SE, Tomcat ve Jpatron EAP Web uygulamalarını hızlıca oluşturmasına, dağıtmasına ve ölçeklendirmesine olanak sağlar. Maven eklentilerine, komut satırından veya IntelliJ, tutulma veya Visual Studio Code gibi düzenleyicilerde uygulamalar dağıtın.

Bu kılavuz, App Service kullanarak Java geliştiricileri için temel kavramlar ve yönergeler sağlar. Azure App Service hiç kullanmadıysanız, önce [Java hızlı](quickstart-java.md) başlangıcı ' nı okumanız gerekir. Java geliştirmeye özgü olmayan App Service kullanımı hakkında genel sorular [App SERVICE SSS](faq-configuration-and-management.md)' de yanıtlanmıştır.

## <a name="deploying-your-app"></a>Uygulamanızı dağıtma

[Maven Için Azure Web App eklentisini](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) ,. war veya. jar dosyalarınızı dağıtmak için kullanabilirsiniz. Popüler Ides ile dağıtım [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) veya [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)de desteklenir.

Aksi takdirde, dağıtım yönteminiz arşiv türüne bağlı olacaktır:

### <a name="java-se"></a>Java SE

. Jar dosyalarını Java mak 'a dağıtmak için `/api/zipdeploy/` kudu sitesinin uç noktasını kullanın. Bu API hakkında daha fazla bilgi için lütfen [Bu belgelere](./deploy-zip.md#rest)bakın. 

> [!NOTE]
>  `app.jar`Uygulamanızı tanımlamak ve çalıştırmak için,. jar uygulamanızın adı App Service olmalıdır. Maven eklentisi (yukarıda bahsedilen), dağıtım sırasında uygulamanızı sizin için otomatik olarak yeniden adlandıracaktır. JAR 'nizi *app. jar*'e yeniden adlandırmak istemiyorsanız,. jar uygulamanızı çalıştırmak için komutuyla bir kabuk betiği yükleyebilirsiniz. Bu betiğin mutlak yolunu portalın yapılandırma bölümündeki [başlangıç dosyası](faq-app-service-linux.md#built-in-images) metin kutusuna yapıştırın. Başlatma dizesi içine yerleştirildiği dizinden çalıştırılmaz. Bu nedenle başlatma dizenizde her zaman başvuru dosyalarının mutlak yollarını kullanın (örneğin: `java -jar /home/myapp/myapp.jar`).

### <a name="tomcat"></a>Tomcat

. War dosyalarını Tomcat 'e dağıtmak için, arka `/api/wardeploy/` uç noktasını kullanarak arşiv DOSYANıZı gönderin. Bu API hakkında daha fazla bilgi için lütfen [Bu belgelere](./deploy-zip.md#deploy-war-file)bakın.

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

. War dosyalarını Jpato 'a dağıtmak için, `/api/wardeploy/` Arşiv DOSYANıZı göndermek için uç noktasını kullanın. Bu API hakkında daha fazla bilgi için lütfen [Bu belgelere](./deploy-zip.md#deploy-war-file)bakın.

. Ear dosyalarını dağıtmak için [FTP 'yi kullanın](deploy-ftp.md).

::: zone-end

FTP kullanarak. war veya. jar 'nizi dağıtmayın. FTP aracı başlangıç betiklerini, bağımlılıklarını veya diğer çalışma zamanı dosyalarını karşıya yüklemek üzere tasarlanmıştır. Web uygulamalarını dağıtmak için en iyi seçenek değildir.

## <a name="logging-and-debugging-apps"></a>Uygulamaları günlüğe kaydetme ve hata ayıklama

Performans raporları, trafik görselleştirmeleri ve sistem durumu sağlaması, Azure portal aracılığıyla her uygulama için kullanılabilir. Daha fazla bilgi için bkz. [Azure App Service tanılamayı genel bakış](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Tanılama günlüklerini akışla aktarma

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Daha fazla bilgi için bkz. [Cloud Shell akış günlükleri](troubleshoot-diagnostic-logs.md#in-cloud-shell).

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>SSH konsol erişimi

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Sorun giderme araçları

Yerleşik Java görüntüleri, [alp Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) işletim sistemini temel alır. `apk`Herhangi bir sorun giderme aracını veya komutunu yüklemek için paket yöneticisini kullanın.

::: zone-end

### <a name="flight-recorder"></a>Uçuş Kaydedicisi

Azul JVM 'Leri kullanılarak App Service üzerindeki tüm Java çalışma zamanları, Zulu Uçuş Kaydedicisi ile gelir. Bunu kullanarak JVM, sistem ve uygulama olaylarını kaydedebilir ve Java uygulamalarınızda sorunları giderebilirsiniz.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Zamanlanmış kayıt

Zamanlanmış bir kayıt almak için Java uygulamasının PID 'sine (Işlem KIMLIĞI) ihtiyacınız olacaktır. PID 'yi bulmak için, https://adresinde Web uygulamanızın SCM sitesinde bir tarayıcı açın <-site-adı>. scm.azurewebsites.net/ProcessExplorer/. Bu sayfada Web uygulamanızdaki çalışan süreçler gösterilmektedir. Tabloda "Java" adlı işlemi bulun ve karşılık gelen PID 'yi (Işlem KIMLIĞI) kopyalayın.

Ardından, SCM sitesinin üst araç çubuğunda **hata ayıklama konsolunu** açın ve aşağıdaki komutu çalıştırın. `<pid>`Daha önce kopyaladığınız Işlem kimliğiyle değiştirin. Bu komut, Java uygulamanızın 30 saniyelik profil oluşturucu kaydını başlatır ve dizininde adlı bir dosya oluşturur `timed_recording_example.jfr` `D:\home` .

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

App Service SSH 'yi çalıştırın ve `jcmd` çalıştıran tüm Java işlemlerinin bir listesini görmek için komutunu çalıştırın. Jcmd 'nin yanı sıra, bir işlem KIMLIK numarası (PID) ile çalışan Java uygulamanızı görmeniz gerekir.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

JVM 'nin 30 saniyelik bir kaydını başlatmak için aşağıdaki komutu yürütün. Bu, JVM profilini oluşturacak ve giriş dizininde *jfr_example. jfr* adlı bir jfr dosyası oluşturacaktır. (116 değerini Java uygulamanızın pid 'si ile değiştirin.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

30 saniyelik Aralık sırasında, çalışırken kaydın gerçekleşdiğini doğrulayabilirsiniz `jcmd 116 JFR.check` . Bu, verilen Java işleminin tüm kayıtlarını gösterir.

#### <a name="continuous-recording"></a>Sürekli kayıt

Çalışma zamanı performansı ([kaynak](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)) üzerinde en az etkiyle Java uygulamanızı sürekli olarak profil oluştururken, Zulu uçuş kaydedicisini kullanabilirsiniz. Bunu yapmak için, gerekli yapılandırmayla JAVA_OPTS adlı bir uygulama ayarı oluşturmak için aşağıdaki Azure CLı komutunu çalıştırın. Uygulamanız başlatıldığında JAVA_OPTS uygulama ayarının içeriği `java` komutuna geçirilir.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Kayıt başladıktan sonra, komutunu kullanarak istediğiniz zaman geçerli kayıt verilerinin dökümünü alabilirsiniz `JFR.dump` .

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>Dosyaları analiz etme `.jfr`

JFR dosyanızı yerel makinenize indirmek için [FTPS](deploy-ftp.md) 'yi kullanın. JFR dosyasını çözümlemek için, [Zulu görevi denetimini](https://www.azul.com/products/zulu-mission-control/)indirip yükleyin. Zulu dili görev denetimi yönergeleri için [Azul belgelerine](https://docs.azul.com/zmc/) ve [yükleme yönergelerine](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)bakın.

### <a name="app-logging"></a>Uygulama günlüğü

::: zone pivot="platform-windows"

Uygulamanızın standart konsol çıkışını ve standart konsol hatası akışlarını yerel dosya sistemine veya Azure Blob depolama alanına yazmak üzere App Service yapılandırmak için Azure portal veya [Azure CLI](/cli/azure/webapp/log#az_webapp_log_config) aracılığıyla [Uygulama günlüğünü](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) etkinleştirin. Yerel App Service dosya sistemi örneğine günlük kaydı, yapılandırıldıktan sonra 12 saat devre dışı bırakılır. Daha uzun süre bekletmeye ihtiyacınız varsa, uygulamayı bir BLOB depolama kapsayıcısına çıktı yazacak şekilde yapılandırın. Java ve Tomcat uygulama günlüklerinizi */Home/LogFiles/Application/* dizininde bulabilirsiniz.

::: zone-end
::: zone pivot="platform-linux"

Uygulamanızın standart konsol çıkışını ve standart konsol hatası akışlarını yerel dosya sistemine veya Azure Blob depolama alanına yazmak üzere App Service yapılandırmak için Azure portal veya [Azure CLI](/cli/azure/webapp/log#az_webapp_log_config) aracılığıyla [Uygulama günlüğünü](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) etkinleştirin. Daha uzun süre bekletmeye ihtiyacınız varsa, uygulamayı bir BLOB depolama kapsayıcısına çıktı yazacak şekilde yapılandırın. Java ve Tomcat uygulama günlüklerinizi */Home/LogFiles/Application/* dizininde bulabilirsiniz.

Linux tabanlı uygulama hizmetleri için Azure Blob depolama günlüğü, yalnızca [Azure izleyici (Önizleme)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) kullanılarak yapılandırılabilir 

::: zone-end

Uygulamanız izleme için [Logback](https://logback.qos.ch/) veya [Log4J](https://logging.apache.org/log4j) kullanıyorsa, bu izlemeleri gözden geçirme için [Application Insights Java izleme günlüklerini keşfet](../azure-monitor/app/java-trace-logs.md)' de günlüğe kaydetme çerçevesi yapılandırma yönergelerini kullanarak Azure Application Insights iletebilirsiniz.

## <a name="customization-and-tuning"></a>Özelleştirme ve ayarlama

Linux için Azure App Service, Azure portal ve CLı aracılığıyla kullanıma hazır ayarlama ve özelleştirmeyi destekler. Java 'a özgü olmayan Web uygulaması yapılandırması için aşağıdaki makaleleri gözden geçirin:

- [Uygulama ayarlarını yapılandırma](configure-common.md#configure-app-settings)
- [Özel etki alanı ayarlama](app-service-web-tutorial-custom-domain.md)
- [SSL bağlamalarını yapılandırma](configure-ssl-bindings.md)
- [CDN ekleme](../cdn/cdn-add-to-web-app.md)
- [Kudu sitesini yapılandırma](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Java çalışma zamanı seçeneklerini ayarla

Ayrılan belleği veya diğer JVM çalışma zamanı seçeneklerini ayarlamak için, seçenekleriyle adlı bir [uygulama ayarı](configure-common.md#configure-app-settings) oluşturun `JAVA_OPTS` . App Service, bu ayarı başlatıldığında Java çalışma zamanına bir ortam değişkeni olarak geçirir.

Azure portal, Web uygulaması için **uygulama ayarları** altında adlı yeni bir uygulama ayarı oluşturun, örneğin, gibi `JAVA_OPTS` ek ayarları içerir `-Xms512m -Xmx1204m` .

Uygulama ayarını Maven eklentisi ' nden yapılandırmak için, Azure eklentisi bölümüne ayar/değer etiketleri ekleyin. Aşağıdaki örnek, belirli bir minimum ve en büyük Java yığın boyutunu ayarlar:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Tek bir uygulama çalıştıran geliştiriciler App Service planında bir dağıtım yuvası ile, aşağıdaki seçenekleri kullanabilir:

- B1 ve S1 örnekleri: `-Xms1024m -Xmx1024m`
- B2 ve S2 örnekleri: `-Xms3072m -Xmx3072m`
- B3 ve S3 örnekleri: `-Xms6144m -Xmx6144m`

Uygulama yığını ayarlarını ayarladığınızda, en iyi bellek ayırmayı bulmak için App Service planı ayrıntılarınızı gözden geçirin ve birden çok uygulama ve dağıtım yuvası ihtiyaçlarına sahip olmak üzere birden fazla uygulama ve dağıtım yuvası

### <a name="turn-on-web-sockets"></a>Web yuvalarını aç

Uygulamanın **uygulama ayarlarındaki** Azure Portal Web Yuvaları desteğini açın. Ayarın etkili olması için uygulamayı yeniden başlatmanız gerekir.

Aşağıdaki komutla Azure CLı kullanarak Web soketi desteğini açın:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Sonra uygulamanızı yeniden başlatın:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Varsayılan karakter kodlamasını ayarla

Azure portal, Web uygulaması için **uygulama ayarları** altında, değeri ile adlı yeni bir uygulama ayarı oluşturun `JAVA_OPTS` `-Dfile.encoding=UTF-8` .

Alternatif olarak, App Service Maven eklentisini kullanarak uygulama ayarını yapılandırabilirsiniz. Eklenti yapılandırmasına ayar adı ve değer etiketlerini ekleyin:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP dosyalarını önceden derle

Tomcat uygulamalarının performansını artırmak için, App Service dağıtım yapmadan önce JSP dosyalarınızı derleyebilirsiniz. Apache Sling tarafından sağlanmış [Maven eklentisini](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) veya bu [ant derleme dosyasını](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)kullanarak kullanabilirsiniz.

## <a name="secure-applications"></a>Uygulamaları güvenli hale getirme

App Service çalıştıran Java uygulamaları, diğer uygulamalarla aynı [güvenlik en iyi](../security/fundamentals/paas-applications-using-app-services.md) uygulamaları kümesine sahiptir.

### <a name="authenticate-users-easy-auth"></a>Kullanıcıların kimliğini doğrulama (kolay kimlik doğrulaması)

**Kimlik doğrulama ve yetkilendirme** seçeneğiyle Azure Portal uygulama kimlik doğrulamasını ayarlayın. Buradan, Facebook, Google veya GitHub gibi Azure Active Directory veya sosyal oturum açma bilgilerini kullanarak kimlik doğrulamasını etkinleştirebilirsiniz. Azure portal yapılandırma yalnızca tek bir kimlik doğrulama sağlayıcısı yapılandırılırken kullanılabilir. Daha fazla bilgi için bkz. [App Service uygulamanızı Azure Active Directory oturum açma](configure-authentication-provider-aad.md) bilgilerini ve diğer kimlik sağlayıcılarının ilgili makalelerini kullanacak şekilde yapılandırma. Birden çok oturum açma sağlayıcısını etkinleştirmeniz gerekiyorsa, [App Service kimlik doğrulamasını özelleştirme](app-service-authentication-how-to.md) makalesindeki yönergeleri izleyin.

#### <a name="java-se"></a>Java SE

Spring Boot geliştiricileri tanıdık yay güvenlik ek açıklamalarını ve API 'Lerini kullanarak uygulamaları güvenli hale getirmek için [Azure Active Directory Spring Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) 'ı kullanabilir. *Application. Properties* dosyanızdaki en büyük üst bilgi boyutunu artırdığınızdan emin olun. Değerini öneririz `16384` .

#### <a name="tomcat"></a>Tomcat

Tomcat uygulamanız, birincil nesneyi bir harita nesnesine aktararak kullanıcının taleplerine doğrudan erişim sağlayabilir. Map nesnesi her talep türünü, bu tür için talepler koleksiyonuna eşler. Aşağıdaki kodda `request` bir örneğidir `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Artık `Map` belirli bir talep için nesneyi inceleyebilirsiniz. Örneğin, aşağıdaki kod parçacığı tüm talep türleri boyunca yinelenir ve her bir koleksiyonun içeriğini yazdırır.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Kullanıcıları imzalamak için `/.auth/ext/logout` yolunu kullanın. Diğer eylemleri gerçekleştirmek için lütfen [App Service kimlik doğrulaması ve yetkilendirme kullanımı](./app-service-authentication-how-to.md)hakkındaki belgelere bakın. Ayrıca, Tomcat [Httpservletrequest arabiriminde](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) ve yöntemleri için resmi belgeler vardır. Aşağıdaki servlet yöntemleri App Service yapılandırmanıza göre de alınır:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Bu özelliği devre dışı bırakmak için, bir değeri olan adlı bir uygulama ayarı oluşturun `WEBSITE_AUTH_SKIP_PRINCIPAL` `1` . App Service tarafından eklenen tüm servlet filtrelerini devre dışı bırakmak için, bir değeri ile adlı bir ayar oluşturun `WEBSITE_SKIP_FILTERS` `1` .

### <a name="configure-tlsssl"></a>TLS/SSL 'yi yapılandırma

Var olan bir SSL sertifikasını karşıya yüklemek ve uygulamanızın etki alanı adına bağlamak için, [Özel BIR DNS adını Azure App Service BIR SSL bağlamasıyla güvenli hale](configure-ssl-bindings.md) getirin. Varsayılan olarak, uygulamanız HTTP bağlantılarına hala izin verir-SSL ve TLS 'yi zorlamak için öğreticideki belirli adımları izleyin.

### <a name="use-keyvault-references"></a>Keykasası başvurularını kullanma

[Azure Keykasası](../key-vault/general/overview.md) , erişim ilkeleri ve denetim geçmişi ile merkezi gizli yönetim sağlar. Gizli dizileri (parolalar veya bağlantı dizeleri gibi) anahtar kasasında saklayabilir ve ortam değişkenleri aracılığıyla uygulamanızdaki bu gizli dizilere erişebilirsiniz.

İlk olarak, uygulamanıza [Key Vault erişim verme](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) ve [bir uygulama ayarında gizli diziniz Için bir Anahtar Kasası başvurusu yapma](app-service-key-vault-references.md#reference-syntax)yönergelerini izleyin. App Service terminaline uzaktan erişirken ortam değişkenini yazdırarak başvurunun gizli olarak çözümlendiğini doğrulayabilirsiniz.

Bu gizli dizileri Spring veya Tomcat yapılandırma dosyasına eklemek için ortam değişkeni ekleme sözdizimi ( `${MY_ENV_VAR}` ) kullanın. Spring yapılandırma dosyaları için lütfen [externalized yapılandırmalarında](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)bu belgelere bakın.

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Java anahtar deposunu kullanma

Varsayılan olarak, [App Service Linux 'a yüklenen](configure-ssl-certificate.md) tüm ortak veya özel sertifikalar, kapsayıcı başladığında Ilgili Java anahtar depolarına yüklenir. Sertifikanızı karşıya yükledikten sonra, Java anahtar deposuna yüklenmek üzere App Service yeniden başlatmanız gerekir. Ortak sertifikalar, konumundaki anahtar deposuna yüklenir `$JAVA_HOME/jre/lib/security/cacerts` ve özel sertifikalar içinde depolanır `$JAVA_HOME/lib/security/client.jks` .

Java anahtar deposundaki sertifikalarla JDBC bağlantınızı şifrelemek için ek yapılandırma gerekebilir. Lütfen seçtiğiniz JDBC sürücünüz için belgelere bakın.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Java anahtar deposunu başlatma

Nesneyi başlatmak için `import java.security.KeyStore` , anahtar deposu dosyasını parolasıyla yükleyin. Her iki anahtar deposu için varsayılan parola "changeıt" dir.

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Anahtar deposunu el ile yükleme

Sertifikaları anahtar deposuna el ile yükleyebilirsiniz. `SKIP_JAVA_KEYSTORE_LOAD` `1` Sertifikaları anahtar deposuna otomatik olarak yüklemek App Service devre dışı bırakmak için değeri olan bir uygulama ayarı oluşturun. Azure portal üzerinden App Service yüklenen tüm genel sertifikalar altında depolanır `/var/ssl/certs/` . Özel sertifikalar altında depolanır `/var/ssl/private/` .

App Service [BIR SSH bağlantısı açıp](configure-linux-open-ssh-session.md) komutu çalıştırarak Java anahtar aracında etkileşim kurabilir veya hata ayıklayabilirsiniz `keytool` . Komutların listesi için bkz. [anahtar araç belgeleri](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) . KeyStore API 'SI hakkında daha fazla bilgi için lütfen [resmi belgelere](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)başvurun.

::: zone-end

## <a name="configure-apm-platforms"></a>APM platformlarını yapılandırma

Bu bölümde, Newrelik ve AppDynamics uygulama performansı izleme (APM) platformlarıyla Linux üzerinde Azure App Service dağıtılan Java uygulamalarının nasıl bağlanacağı gösterilmektedir.

### <a name="configure-new-relic"></a>Yeni relik yapılandırma

::: zone pivot="platform-windows"

1. [NewRelic.com](https://newrelic.com/signup) adresinde bir Newrelik hesabı oluşturun
2. Newrelik 'ten Java aracısını indirin, *newrelic-java-x.x.x.zip* benzer bir dosya adı olacaktır.
3. Lisans anahtarınızı kopyalayın, aracıyı daha sonra yapılandırmak için gerekir.
4. [App Service örneğine SSH ekleyin](configure-linux-open-ssh-session.md) ve */Home/site/Wwwroot/APM* adlı yeni bir dizin oluşturun.
5. Paketi açılan Newrelik Java aracı dosyalarını */Home/site/Wwwroot/APM* altındaki bir dizine yükleyin. Aracınızın dosyaları */Home/site/Wwwroot/APM/newrelik* konumunda olmalıdır.
6. */Home/site/Wwwroot/APM/newrelic/newrelic.exe* konumundaki YAML dosyasını değiştirin ve yer tutucu lisans değerini kendi lisans anahtarınızla değiştirin.
7. Azure portal, App Service uygulamanıza gidin ve yeni bir uygulama ayarı oluşturun.

    - **Java SE** uygulamaları için, değeriyle adlı bir ortam değişkeni oluşturun `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - **Tomcat** için, değeriyle adlı bir ortam değişkeni oluşturun `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. [NewRelic.com](https://newrelic.com/signup) adresinde bir Newrelik hesabı oluşturun
2. Newrelik 'ten Java aracısını indirin, *newrelic-java-x.x.x.zip* benzer bir dosya adı olacaktır.
3. Lisans anahtarınızı kopyalayın, aracıyı daha sonra yapılandırmak için gerekir.
4. [App Service örneğine SSH ekleyin](configure-linux-open-ssh-session.md) ve */Home/site/Wwwroot/APM* adlı yeni bir dizin oluşturun.
5. Paketi açılan Newrelik Java aracı dosyalarını */Home/site/Wwwroot/APM* altındaki bir dizine yükleyin. Aracınızın dosyaları */Home/site/Wwwroot/APM/newrelik* konumunda olmalıdır.
6. */Home/site/Wwwroot/APM/newrelic/newrelic.exe* konumundaki YAML dosyasını değiştirin ve yer tutucu lisans değerini kendi lisans anahtarınızla değiştirin.
7. Azure portal, App Service uygulamanıza gidin ve yeni bir uygulama ayarı oluşturun.
   
    - **Java SE** uygulamaları için, değeriyle adlı bir ortam değişkeni oluşturun `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - **Tomcat** için, değeriyle adlı bir ortam değişkeni oluşturun `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Veya için bir ortam değişkeniniz zaten varsa `JAVA_OPTS` `CATALINA_OPTS` , `-javaagent:/...` geçerli değerin sonuna bu seçeneği ekleyin.

### <a name="configure-appdynamics"></a>AppDynamics 'i yapılandırma

::: zone pivot="platform-windows"

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) adresinde bir AppDynamics hesabı oluşturun
2. AppDynamics Web sitesinden Java aracısını indirin, dosya adı *AppServerAgent-x.x.x.xxxxx.zip* benzerdir.
3. [Kudu konsolunu](https://github.com/projectkudu/kudu/wiki/Kudu-console) kullanarak */Home/site/Wwwroot/APM* adlı yeni bir dizin oluşturun.
4. Java aracı dosyalarını */Home/site/Wwwroot/APM* altındaki bir dizine yükleyin. Aracınızın dosyaları */Home/site/Wwwroot/APM/AppDynamics* konumunda olmalıdır.
5. Azure portal, App Service uygulamanıza gidin ve yeni bir uygulama ayarı oluşturun.

   - **Java SE** uygulamaları için, `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` App Service adınız olan değer ile adlı bir ortam değişkeni oluşturun `<app-name>` .
   - **Tomcat** uygulamaları için, `CATALINA_OPTS` App Service adınız olan değer ile adlı bir ortam değişkeni oluşturun `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` .

::: zone-end
::: zone pivot="platform-linux"

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) adresinde bir AppDynamics hesabı oluşturun
2. AppDynamics Web sitesinden Java aracısını indirin, dosya adı *AppServerAgent-x.x.x.xxxxx.zip* benzerdir.
3. [App Service örneğine SSH ekleyin](configure-linux-open-ssh-session.md) ve */Home/site/Wwwroot/APM* adlı yeni bir dizin oluşturun.
4. Java aracı dosyalarını */Home/site/Wwwroot/APM* altındaki bir dizine yükleyin. Aracınızın dosyaları */Home/site/Wwwroot/APM/AppDynamics* konumunda olmalıdır.
5. Azure portal, App Service uygulamanıza gidin ve yeni bir uygulama ayarı oluşturun.

   - **Java SE** uygulamaları için, `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` App Service adınız olan değer ile adlı bir ortam değişkeni oluşturun `<app-name>` .
   - **Tomcat** uygulamaları için, `CATALINA_OPTS` App Service adınız olan değer ile adlı bir ortam değişkeni oluşturun `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` .

::: zone-end

> [!NOTE]
>  Veya için bir ortam değişkeniniz zaten varsa `JAVA_OPTS` `CATALINA_OPTS` , `-javaagent:/...` geçerli değerin sonuna bu seçeneği ekleyin.

## <a name="configure-data-sources"></a>Veri kaynaklarını yapılandırma

### <a name="java-se"></a>Java SE

Spring Boot uygulamalarındaki veri kaynaklarına bağlanmak için bağlantı dizeleri oluşturmanızı ve bunları *uygulamanızın. Properties* dosyasına ekleme.

1. App Service sayfasının "yapılandırma" bölümünde, dize için bir ad belirleyin, JDBC Bağlantı dizenizi değer alanına yapıştırın ve türü "özel" olarak ayarlayın. İsteğe bağlı olarak, bu bağlantı dizesini yuva ayarı olarak ayarlayabilirsiniz.

    Bu bağlantı dizesine, uygulamamız tarafından adlı bir ortam değişkeni olarak erişilebilir `CUSTOMCONNSTR_<your-string-name>` . Örneğin, yukarıda oluşturduğumuz bağlantı dizesinin adı olacaktır `CUSTOMCONNSTR_exampledb` .

2. *Application. Properties* dosyanızda, bu bağlantı dizesine ortam değişkeni adı ile başvur. Örneğimiz için aşağıdakileri kullanacağız.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Bu konuyla ilgili daha fazla bilgi için lütfen veri erişimi ve [externalized yapılandırmalarında](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) [Spring Boot belgelerine](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) bakın.

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Bu yönergeler tüm veritabanı bağlantıları için geçerlidir. Yer tutucuları, seçtiğiniz veritabanının sürücü sınıfı adı ve JAR dosyası ile doldurmanız gerekir. , Ortak veritabanları için sınıf adları ve sürücü indirmeleri içeren bir tablodur.

| Veritabanı   | Sürücü sınıfı adı                             | JDBC sürücüsü                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [İndir](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [İndir](https://dev.mysql.com/downloads/connector/j/) ("platformdan bağımsız" seçeneğini belirleyin) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [İndir](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Tomcat 'i Java veritabanı bağlantısı (JDBC) veya Java Kalıcılık API 'SI (JPA) kullanacak şekilde yapılandırmak için, önce başlangıçta `CATALINA_OPTS` Tomcat tarafından okunan ortam değişkenini özelleştirin. Bu değerleri [App Service Maven eklentisindeki](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)bir uygulama ayarı aracılığıyla ayarlayın:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Veya Azure Portal içindeki **yapılandırma**  >  **uygulaması ayarları** sayfasında ortam değişkenlerini ayarlayın.

Daha sonra, veri kaynağının bir uygulama için mi yoksa Tomcat servlet üzerinde çalışan tüm uygulamalar için mi kullanılabilir olacağını saptayın.

#### <a name="application-level-data-sources"></a>Uygulama düzeyi veri kaynakları

1. Projenizin *meta INF/* dizininde bir *context.xml* dosyası oluşturun. Yoksa *meta INF/* dizin oluşturun.

2. *context.xml*, `Context` veri kaynağını bir JNDI adresine bağlamak için bir öğe ekleyin. `driverClassName`Yer tutucusunu, yukarıdaki tablodaki sürücünüzün sınıf adıyla değiştirin.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Uygulamanızdaki veri kaynağını kullanmak için uygulamanızın *web.xml* güncelleştirin.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Paylaşılan sunucu düzeyi kaynakları

Windows üzerinde App Service Tomcat yüklemeleri App Service planında paylaşılan alanda bulunuyor. Sunucu genelinde yapılandırma için Tomcat yüklemesini doğrudan değiştiremezsiniz. Tomcat yüklemenizde sunucu düzeyinde yapılandırma değişiklikleri yapmak için, Tomcat 'i yerel bir klasöre kopyalamanız gerekir. Bu, Tomcat 'in yapılandırmasını değiştirebilirsiniz. 

##### <a name="automate-creating-custom-tomcat-on-app-start"></a>Uygulama başlatma sırasında özel Tomcat oluşturmayı otomatikleştirin

Bir Web uygulaması başlamadan önce eylemleri gerçekleştirmek için bir başlangıç betiği kullanabilirsiniz. Tomcat 'i özelleştirmeye yönelik başlatma komut dosyasının aşağıdaki adımları tamamlaması gerekir:

1. Tomcat 'in zaten kopyalanıp kopyalanmadığını ve yerel olarak yapılandırılıp yapılandırılmadığını denetleyin. Varsa, başlangıç betiği buradan sona bitebilirler.
2. Tomcat 'i yerel olarak kopyalayın.
3. Gerekli yapılandırma değişikliklerini yapın.
4. Yapılandırmanın başarıyla tamamlandığını belirtin.

Aşağıdaki adımları tamamlayan bir PowerShell betiği aşağıda verilmiştir:

```powershell
    # Check for marker file indicating that config has already been done
    if(Test-Path "$LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$LOCAL_EXPANDED\tomcat"){
        Remove-Item "$LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$AZURE_TOMCAT90_HOME\*" -Destination "$LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    {... customization ...}

    # Mark that the operation was a success
    New-Item -Path "$LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
```

##### <a name="transforms"></a>Dönüştürmeler

Tomcat sürümünü özelleştirmenin yaygın kullanım durumu `server.xml` ,, `context.xml` ya da `web.xml` Tomcat yapılandırma dosyalarını değiştirmektir. App Service, platform özellikleri sağlamak için bu dosyaları zaten değiştiriyor. Bu özellikleri kullanmaya devam etmek için, bunlarda değişiklikler yaptığınızda bu dosyaların içeriğinin korunması önemlidir. Bunu gerçekleştirmek için bir [XSL dönüşümü (XSLT)](https://www.w3schools.com/xml/xsl_intro.asp)kullanmanızı öneririz. Dosyanın özgün içeriğini korurken XML dosyalarında değişiklik yapmak için bir XSL dönüşümü kullanın.

###### <a name="example-xslt-file"></a>Örnek XSLT dosyası

Bu örnek Transform öğesine yeni bir bağlayıcı düğümü ekler `server.xml` . Dosyanın özgün içeriğini koruyan *kimlik dönüşümünü* aklınızda edin.

```xml
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:output method="xml" indent="yes"/>
  
    <!-- Identity transform: this ensures that the original contents of the file are included in the new file -->
    <!-- Ensure that your transform files include this block -->
    <xsl:template match="@* | node()" name="Copy">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()"/>
      </xsl:copy>
    </xsl:template>
  
    <xsl:template match="@* | node()" mode="insertConnector">
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                   contains(., '&lt;Connector') and
                                   (contains(., 'scheme=&quot;https&quot;') or
                                    contains(., &quot;scheme='https'&quot;))]">
      <xsl:value-of select="." disable-output-escaping="yes" />
    </xsl:template>
  
    <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                     comment()[contains(., '&lt;Connector') and
                                               (contains(., 'scheme=&quot;https&quot;') or
                                                contains(., &quot;scheme='https'&quot;))]
                                    )]
                        ">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()" mode="insertConnector" />
      </xsl:copy>
    </xsl:template>
  
    <!-- Add the new connector after the last existing Connnector if there is one -->
    <xsl:template match="Connector[last()]" mode="insertConnector">
      <xsl:call-template name="Copy" />
  
      <xsl:call-template name="AddConnector" />
    </xsl:template>
  
    <!-- ... or before the first Engine if there is no existing Connector -->
    <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                  mode="insertConnector">
      <xsl:call-template name="AddConnector" />
  
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template name="AddConnector">
      <!-- Add new line -->
      <xsl:text>&#xa;</xsl:text>
      <!-- This is the new connector -->
      <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
                 maxThreads="150" scheme="https" secure="true" 
                 keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
                 clientAuth="false" sslProtocol="TLS" />
    </xsl:template>

</xsl:stylesheet>
```

###### <a name="function-for-xsl-transform"></a>XSL dönüşümü işlevi

PowerShell, XML dosyalarını XSL dönüştürmeleri kullanarak dönüştürmek için yerleşik araçlara sahiptir. Aşağıdaki betik, dönüşümü gerçekleştirmek için ' de kullanabileceğiniz örnek bir işlevdir `startup.ps1` :

```powershell
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);

        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }
```

##### <a name="app-settings"></a>Uygulama ayarları

Platformun, Tomcat 'in özel sürümünün yüklü olduğu yeri de bilmeleri gerekir. Uygulamanın ayarında yükleme konumunu ayarlayabilirsiniz `CATALINA_BASE` .

Bu ayarı değiştirmek için Azure CLı 'yi kullanabilirsiniz:

```powershell
    az webapp config appsettings set -g $MyResourceGroup -n $MyUniqueApp --settings CATALINA_BASE="%LOCAL_EXPANDED%\tomcat"
```

Ya da Azure portal ayarı el ile değiştirebilirsiniz:

1. **Ayarlar**  >  **yapılandırma**  >  **uygulaması ayarları**' na gidin.
1. **Yeni uygulama ayarı** seçin.
1. Ayarı oluşturmak için bu değerleri kullanın:
   1. **Ad**: `CATALINA_BASE`
   1. **Değer**: `"%LOCAL_EXPANDED%\tomcat"`

##### <a name="example-startupps1"></a>Örnek startup.ps1

Aşağıdaki örnek betik özel bir Tomcat 'i yerel bir klasöre kopyalar, bir XSL dönüşümü gerçekleştirir ve dönüştürmenin başarılı olduğunu gösterir:

```powershell
    # Locations of xml and xsl files
    $target_xml="$LOCAL_EXPANDED\tomcat\conf\server.xml"
    $target_xsl="$HOME\site\server.xsl"

    # Define the transform function
    # Useful if transforming multiple files
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);
        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }

    # Check for marker file indicating that config has already been done
    if(Test-Path "$LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$LOCAL_EXPANDED\tomcat"){
        Remove-Item "$LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$AZURE_TOMCAT90_HOME\*" -Destination "$LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    $success = TransformXML -xml $target_xml -xsl $target_xsl -output $target_xml

    # Mark that the operation was a success if successful
    if($success){
        New-Item -Path "$LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
    }
```

#### <a name="finalize-configuration"></a>Yapılandırmayı Sonlandır

Son olarak, sürücü JARs ' ı Tomcat Sınıfyoluna yerleştireceğiz ve App Service yeniden başlatacağız. JDBC sürücü dosyalarının, */Home/Tomcat/lib* dizinine yerleştirerek Tomcat ClassLoader için kullanılabilir olduğundan emin olun. (Henüz yoksa, bu dizini oluşturun.) Bu dosyaları App Service örneğine yüklemek için aşağıdaki adımları uygulayın:

1. [Cloud Shell](https://shell.azure.com), WebApp uzantısını yüklerken:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. App Service için yerel sisteminizden bir SSH tüneli oluşturmak için aşağıdaki CLı komutunu çalıştırın:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. SFTP istemcinizdeki yerel tünel oluşturma bağlantı noktasına bağlanın ve dosyaları */Home/Tomcat/lib* klasörüne yükleyin.

Alternatif olarak, bir FTP istemcisini kullanarak JDBC sürücüsünü karşıya yükleyebilirsiniz. [FTP kimlik bilgilerinizi almak için bu yönergeleri](deploy-configure-credentials.md)izleyin.

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Bu yönergeler tüm veritabanı bağlantıları için geçerlidir. Yer tutucuları, seçtiğiniz veritabanının sürücü sınıfı adı ve JAR dosyası ile doldurmanız gerekir. , Ortak veritabanları için sınıf adları ve sürücü indirmeleri içeren bir tablodur.

| Veritabanı   | Sürücü sınıfı adı                             | JDBC sürücüsü                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [İndir](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [İndir](https://dev.mysql.com/downloads/connector/j/) ("platformdan bağımsız" seçeneğini belirleyin) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [İndir](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Tomcat 'i Java veritabanı bağlantısı (JDBC) veya Java Kalıcılık API 'SI (JPA) kullanacak şekilde yapılandırmak için, önce başlangıçta `CATALINA_OPTS` Tomcat tarafından okunan ortam değişkenini özelleştirin. Bu değerleri [App Service Maven eklentisindeki](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)bir uygulama ayarı aracılığıyla ayarlayın:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Veya Azure Portal içindeki **yapılandırma**  >  **uygulaması ayarları** sayfasında ortam değişkenlerini ayarlayın.

Daha sonra, veri kaynağının bir uygulama için mi yoksa Tomcat servlet üzerinde çalışan tüm uygulamalar için mi kullanılabilir olacağını saptayın.

#### <a name="application-level-data-sources"></a>Uygulama düzeyi veri kaynakları

1. Projenizin *meta INF/* dizininde bir *context.xml* dosyası oluşturun. Yoksa *meta INF/* dizin oluşturun.

2. *context.xml*, `Context` veri kaynağını bir JNDI adresine bağlamak için bir öğe ekleyin. `driverClassName`Yer tutucusunu, yukarıdaki tablodaki sürücünüzün sınıf adıyla değiştirin.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Uygulamanızdaki veri kaynağını kullanmak için uygulamanızın *web.xml* güncelleştirin.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Paylaşılan sunucu düzeyi kaynakları

Paylaşılan, sunucu düzeyinde bir veri kaynağı eklemek, Tomcat 'in server.xml düzenlemenizi gerektirir. İlk olarak, bir [Başlangıç betiğini](faq-app-service-linux.md#built-in-images) karşıya yükleyin ve yolu **yapılandırma**  >  **Başlangıç komutunda** betiğin yolunu ayarlayın. [FTP](deploy-ftp.md)kullanarak başlangıç betiğini karşıya yükleyebilirsiniz.

Başlangıç betiğinizin server.xml dosyasına bir [XSL dönüştürmesi](https://www.w3schools.com/xml/xsl_intro.asp) yapıp elde edilen XML dosyasının çıktısını almak için kullanılır `/usr/local/tomcat/conf/server.xml` . Başlangıç betiği APK aracılığıyla libxslt 'yi yüklemelidir. XSL dosyanız ve başlangıç betiğimiz FTP aracılığıyla karşıya yüklenebilir. Aşağıda örnek bir başlangıç betiği verilmiştir.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Örnek bir xsl dosyası aşağıda verilmiştir. Örnek xsl dosyası, Tomcat server.xml yeni bir bağlayıcı düğümü ekler.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Yapılandırmayı Sonlandır

Son olarak, sürücü JARs ' ı Tomcat Sınıfyoluna yerleştirip App Service yeniden başlatın.

1. JDBC sürücü dosyalarının, */Home/Tomcat/lib* dizinine yerleştirerek Tomcat ClassLoader için kullanılabilir olduğundan emin olun. (Henüz yoksa, bu dizini oluşturun.) Bu dosyaları App Service örneğine yüklemek için aşağıdaki adımları uygulayın:

    1. [Cloud Shell](https://shell.azure.com), WebApp uzantısını yüklerken:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. App Service için yerel sisteminizden bir SSH tüneli oluşturmak için aşağıdaki CLı komutunu çalıştırın:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. SFTP istemcinizdeki yerel tünel oluşturma bağlantı noktasına bağlanın ve dosyaları */Home/Tomcat/lib* klasörüne yükleyin.

    Alternatif olarak, bir FTP istemcisini kullanarak JDBC sürücüsünü karşıya yükleyebilirsiniz. [FTP kimlik bilgilerinizi almak için bu yönergeleri](deploy-configure-credentials.md)izleyin.

2. Sunucu düzeyinde bir veri kaynağı oluşturduysanız App Service Linux uygulamasını yeniden başlatın. Tomcat `CATALINA_BASE` `/home/tomcat` , güncelleştirilmiş yapılandırmayı sıfırlayacak ve kullanacak şekilde başlatılır.

### <a name="jboss-eap"></a>JBoss EAP

[Bir veri kaynağını Jpatron EAP ile kaydederken](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)üç temel adım vardır: JDBC sürücüsünü karşıya yükleme, JDBC sürücüsünü bir modül olarak ekleme ve modülü kaydetme. App Service, durum bilgisi olmayan bir barındırma hizmetidir, bu nedenle veri kaynağı modülünü eklemek ve kaydettirmek için yapılandırma komutlarının, kapsayıcı başladığı sürece betikleştirilmiş ve uygulanmış olması gerekir.

1. Veritabanınızın JDBC sürücüsünü edinin. 
2. JDBC sürücüsü için bir XML Modülü tanım dosyası oluşturun. Aşağıda gösterilen örnek, PostgreSQL için bir modül tanımıdır.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Jpatron CLı komutlarınızı adlı bir dosyaya yerleştirin `jboss-cli-commands.cli` . Jpatron komutlarının modülü eklemesi ve bunu bir veri kaynağı olarak kaydetmesi gerekir. Aşağıdaki örnekte PostgreSQL için Jpatron CLı komutları gösterilmektedir.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. `startup_script.sh`Jpatron CLI komutlarını çağıran bir başlangıç betiği oluşturun. Aşağıdaki örnekte, nasıl çağrılacağını gösterilmektedir `jboss-cli-commands.cli` . Daha sonra, kapsayıcı başladığında bu betiği çalıştırmak için App Service configyeniden kullanacaksınız. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. İstediğiniz FTP istemcisini kullanarak, JDBC sürücünüzü, `jboss-cli-commands.cli` , `startup_script.sh` ve modül tanımınızı öğesine yükleyin `/site/deployments/tools/` .
2. Sitenizi kapsayıcı başladığında çalışacak şekilde yapılandırın `startup_script.sh` . Azure portalında, **yapılandırma**  >  **Genel ayarlar**  >  **başlangıç komutu**' na gidin. Başlangıç komut alanını olarak ayarlayın `/home/site/deployments/tools/startup_script.sh` . Değişikliklerinizi **kaydedin** .

Veri kaynağının Jpatron sunucusuna eklendiğini doğrulamak için, WebApp ile SSH ve çalıştırın `$JBOSS_HOME/bin/jboss-cli.sh --connect` . Jpatron 'a bağlandıktan sonra, `/subsystem=datasources:read-resource` veri kaynaklarının bir listesini yazdırmak için öğesini çalıştırın.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Java çalışma zamanı sürümü seçme

App Service, kullanıcıların Java 8 veya Java 11 gibi birincil sürümünü ve 1.8.0 _232 veya 11.0.5 gibi ikincil sürümü seçmesini sağlar. Yeni ikincil sürümler kullanılabilir hale geldiğinde, ikincil sürümün otomatik olarak güncelleştirilmesini de seçebilirsiniz. Çoğu durumda, üretim siteleri sabitlenmiş küçük JVM sürümlerini kullanmalıdır. Bu, küçük bir sürüm otomatik güncelleştirmesi sırasında beklenmeyen kesintileri engeller.

İkincil sürümü sabitlemeyi seçerseniz, sitedeki JVM ikincil sürümünü düzenli olarak güncelleştirmeniz gerekir. Uygulamanızın daha yeni alt sürümde çalıştığından emin olmak için, hazırlama yuvası oluşturun ve hazırlama sitesindeki ikincil sürümü artırın. Uygulamanın yeni ikincil sürümde doğru şekilde çalıştığını onayladıktan sonra hazırlama ve üretim yuvalarını değiştirebilirsiniz.

## <a name="jboss-eap-hardware-options"></a>Jpatron EAP donanım seçenekleri

Jpatron EAP yalnızca Premium ve yalıtılmış donanım seçeneklerinde kullanılabilir. Genel Önizleme sırasında ücretsiz, paylaşılan, temel veya Standart katmanda bir Jpatron EAP sitesi oluşturan müşteriler, beklenmeyen davranışları önlemek için Premium veya yalıtılmış donanım katmanına kadar ölçeklendirmelidir.

## <a name="java-runtime-statement-of-support"></a>Java Runtime desteği

### <a name="jdk-versions-and-maintenance"></a>JDK sürümleri ve bakım

Azure 'un desteklenen Java Geliştirme Seti (JDK), [Azul sistemleri](https://www.azul.com/)aracılığıyla bir [Zulu](https://www.azul.com/downloads/azure-only/zulu/) dili olarak sunulmaktadır. Azul Zulu kurumsal Derlemeleriyle OpenJDK, Azure için OpenJDK ve Microsoft ve Azul sistemleri tarafından desteklenen Azure Stack için ücretsiz, çok platformlu, üretime hazırlı bir dağıtımıdır. Java SE uygulamalarını oluşturmak ve çalıştırmak için gereken tüm bileşenleri içerir. JDK 'yi [Java JDK yüklemesinden](/azure/developer/java/fundamentals/java-jdk-long-term-support)yükleyebilirsiniz.

Ana sürüm güncelleştirmeleri, Azure App Service yeni çalışma zamanı seçenekleri aracılığıyla sağlanacaktır. Müşteriler, App Service dağıtımını yapılandırarak ve ana güncelleştirmenin ihtiyaçlarını karşıladığından sorumlu olduğundan, bu yeni Java sürümlerine güncelleştirir.

Desteklenen JDKs, her yıl Ocak, Nisan, Temmuz ve Ekim ayında otomatik olarak üç ayda bir düzeltme eki uygulanır. Azure 'da Java hakkında daha fazla bilgi için lütfen [Bu destek belgesine](/azure/developer/java/fundamentals/java-jdk-long-term-support)bakın.

### <a name="security-updates"></a>Güvenlik güncelleştirmeleri

Önemli güvenlik açıklarına yönelik düzeltme ekleri ve düzeltmeler Azul sistemlerinden kullanılabilir hale geldiğinde serbest bırakılır. "Ana" güvenlik açığı, [NIST ortak güvenlik açığı Puanlama sistemi, sürüm 2](https://nvd.nist.gov/vuln-metrics/cvss)üzerinde 9,0 veya üzeri bir taban puanı tarafından tanımlanır.

Tomcat 8,0 [, 30 eylül 2018 Itibariyle yaşam sonuna (EOL)](https://tomcat.apache.org/tomcat-80-eol.html)ulaştı. Çalışma zamanı Azure App Service kullanılabilir olmaya devam ederken, Azure, Tomcat 8,0 güvenlik güncelleştirmelerini uygulamacaktır. Mümkünse, uygulamalarınızı Tomcat 8,5 veya 9,0 ' ye geçirin. Azure App Service hem Tomcat 8,5 hem de 9,0 kullanılabilir. Daha fazla bilgi için [resmi Tomcat sitesine](https://tomcat.apache.org/whichversion.html) bakın. 

### <a name="deprecation-and-retirement"></a>Kullanımdan kaldırma ve kullanımdan kaldırma

Desteklenen bir Java çalışma zamanı devre dışı bırakırsa, etkilenen çalışma zamanını kullanan Azure geliştiricileri, çalışma zamanı kullanımdan kalkmadan önce en az altı ayda bir kullanımdan kaldırma olarak verilmeyecektir.


### <a name="local-development"></a>Yerel geliştirme

Geliştiriciler, Azul ['nin indirme sitesinden](https://www.azul.com/downloads/azure-only/zulu/)yerel geliştirme Için Azul Zulu kurumsal JDK üretim sürümünü indirebilir.

### <a name="development-support"></a>Geliştirme desteği

Azure [tarafından desteklenen Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) için ürün desteği, Azure için geliştirme sırasında veya [nitelikli bir Azure destek planı](https://azure.microsoft.com/support/plans/)ile [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) Microsoft aracılığıyla edinilebilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure hızlı başlangıç, öğreticiler ve Java başvuru belgelerini bulmak için [Java geliştiricileri Için Azure](/java/azure/) 'u ziyaret edin.

Java geliştirmeye özgü olmayan Linux için App Service kullanma hakkında genel sorular, [App Service LINUX SSS](faq-app-service-linux.md)bölümünde yanıtlanmıştır.
