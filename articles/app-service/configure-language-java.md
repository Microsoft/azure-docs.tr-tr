---
title: Windows Java uygulamalarını Yapılandırma-Azure App Service | Microsoft Docs
description: Java uygulamalarını, Azure App Service varsayılan Windows örneklerinde çalışacak şekilde nasıl yapılandıracağınızı öğrenin.
keywords: Azure App Service, Web uygulaması, Windows, Oss, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: ad9ee8a21390126f20da4037a438a2655b8b5d47
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012269"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Azure App Service için bir Windows Java uygulaması yapılandırma

Azure App Service, Java geliştiricilerinin tam olarak yönetilen bir Windows tabanlı hizmette Tomcat Web uygulamalarını hızlıca oluşturmasına, dağıtmasına ve ölçeklendirmesine olanak tanır. Maven eklentilerine sahip uygulamaları, komut satırından veya IntelliJ, tutulma veya Visual Studio Code gibi düzenleyicilerde dağıtın.

Bu kılavuz, App Service ' de kullanarak Java geliştiricileri için temel kavramlar ve yönergeler sağlar. Azure App Service hiç kullanmadıysanız, önce [Java hızlı](app-service-web-get-started-java.md) başlangıcı ' nı okumanız gerekir. Java geliştirmeye özgü olmayan App Service kullanımı hakkında genel sorular, [App Service WINDOWS SSS](faq-configuration-and-management.md)' de yanıtlanmıştır.

## <a name="deploying-your-app"></a>Uygulamanızı dağıtma

[Azure App Service Için Maven eklentisini](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) ,. war dosyalarınızı dağıtmak için kullanabilirsiniz. Popüler Ides ile dağıtım, [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) veya [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)ile de desteklenir.

Aksi takdirde, dağıtım yönteminiz arşiv türüne bağlı olacaktır:

- . War dosyalarını Tomcat 'e dağıtmak için `/api/wardeploy/` uç noktasını kullanarak arşiv dosyanızı GÖNDERIN. Bu API hakkında daha fazla bilgi için lütfen [Bu belgelere](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)bakın.

FTP kullanarak. war 'nizi dağıtmayın. FTP aracı başlangıç betiklerini, bağımlılıklarını veya diğer çalışma zamanı dosyalarını karşıya yüklemek üzere tasarlanmıştır. Web uygulamalarını dağıtmak için en iyi seçenek değildir.

## <a name="logging-and-debugging-apps"></a>Uygulamaları günlüğe kaydetme ve hata ayıklama

Performans raporları, trafik görselleştirmeleri ve sistem durumu sağlaması, Azure portal aracılığıyla her uygulama için kullanılabilir. Daha fazla bilgi için bkz. [Azure App Service tanılamayı genel bakış](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Tanılama günlüklerini akışla aktarma

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Daha fazla bilgi için bkz. [Cloud Shell akış günlükleri](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Uygulama günlüğü

Uygulamanızın standart konsol çıkışını ve standart konsol hatası akışlarını yerel dosya sistemine veya Azure Blob depolama alanına yazmak üzere App Service yapılandırmak için Azure portal veya [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) aracılığıyla [Uygulama günlüğünü](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) etkinleştirin. Yerel App Service dosya sistemi örneğine günlük kaydı, yapılandırıldıktan sonra 12 saat devre dışı bırakılır. Daha uzun süre bekletmeye ihtiyacınız varsa, uygulamayı bir BLOB depolama kapsayıcısına çıktı yazacak şekilde yapılandırın. Java ve Tomcat uygulama günlüklerinizi */LogFiles/Application/* dizininde bulabilirsiniz.

Uygulamanız izleme için [Logback](https://logback.qos.ch/) veya [Log4J](https://logging.apache.org/log4j) kullanıyorsa, bu izlemeleri gözden geçirme için, [Java izleme günlüklerini keşfet ' de günlüğe kaydetme çerçevesi yapılandırma yönergelerini kullanarak Azure Application Insights 'e iletebilirsiniz Application Insights ](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Özelleştirme ve ayarlama

Azure App Service, Azure portal ve CLı aracılığıyla kullanıma hazır ayarlama ve özelleştirmeyi destekler. Java 'a özgü olmayan Web uygulaması yapılandırması için aşağıdaki makaleleri gözden geçirin:

- [Uygulama ayarlarını yapılandır](configure-common.md#configure-app-settings)
- [Özel etki alanı ayarlama](app-service-web-tutorial-custom-domain.md)
- [SSL bağlamalarını yapılandırma](configure-ssl-bindings.md)
- [CDN ekleme](../cdn/cdn-add-to-web-app.md)
- [Kudu sitesini yapılandırma](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Java çalışma zamanı seçeneklerini ayarla

Ayrılan belleği veya diğer JVM çalışma zamanı seçeneklerini ayarlamak için, seçeneklerle `JAVA_OPTS` adlı bir [uygulama ayarı](configure-common.md#configure-app-settings) oluşturun. App Service, bu ayarı başlatıldığında Java çalışma zamanına bir ortam değişkeni olarak geçirir.

Azure portal, Web uygulaması için **uygulama ayarları** altında, `-Xms512m -Xmx1204m`gibi ek ayarları içeren `JAVA_OPTS` adlı yeni bir uygulama ayarı oluşturun.

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

Azure portal, Web uygulaması için **uygulama ayarları** altında, `JAVA_OPTS` adlı yeni bir uygulama ayarı oluşturun `-Dfile.encoding=UTF-8`değeri.

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

## <a name="secure-applications"></a>Güvenli uygulamalar

App Service çalıştıran Java uygulamaları, diğer uygulamalarla aynı [güvenlik en iyi](/azure/security/security-paas-applications-using-app-services) uygulamaları kümesine sahiptir.

### <a name="authenticate-users-easy-auth"></a>Kullanıcıların kimliğini doğrulama (kolay kimlik doğrulaması)

**Kimlik doğrulama ve yetkilendirme** seçeneğiyle Azure Portal uygulama kimlik doğrulamasını ayarlayın. Buradan, Facebook, Google veya GitHub gibi Azure Active Directory veya sosyal oturum açma bilgilerini kullanarak kimlik doğrulamasını etkinleştirebilirsiniz. Azure portal yapılandırma yalnızca tek bir kimlik doğrulama sağlayıcısı yapılandırılırken kullanılabilir. Daha fazla bilgi için bkz. [App Service uygulamanızı Azure Active Directory oturum açma](configure-authentication-provider-aad.md) bilgilerini ve diğer kimlik sağlayıcılarının ilgili makalelerini kullanacak şekilde yapılandırma. Birden çok oturum açma sağlayıcısını etkinleştirmeniz gerekiyorsa, [App Service kimlik doğrulamasını özelleştirme](app-service-authentication-how-to.md) makalesindeki yönergeleri izleyin.

#### <a name="tomcat-and-wildfly"></a>Tomcat ve Yavaya

Tomcat veya Yavaya yönelik uygulamalar, birincil nesneyi bir harita nesnesine aktararak kullanıcının taleplerine doğrudan erişim sağlayabilir. Map nesnesi her talep türünü, bu tür için talepler koleksiyonuna eşler. Aşağıdaki kodda, `request` bir `HttpServletRequest`örneğidir.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Artık belirli bir talep için `Map` nesnesini inceleyebilirsiniz. Örneğin, aşağıdaki kod parçacığı tüm talep türleri boyunca yinelenir ve her bir koleksiyonun içeriğini yazdırır.

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

Kullanıcıları imzalamak için `/.auth/ext/logout` yolunu kullanın. Diğer eylemleri gerçekleştirmek için lütfen [App Service kimlik doğrulaması ve yetkilendirme kullanımı](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)hakkındaki belgelere bakın. Ayrıca, Tomcat [Httpservletrequest arabiriminde](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) ve yöntemleri için resmi belgeler vardır. Aşağıdaki servlet yöntemleri App Service yapılandırmanıza göre de alınır:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Bu özelliği devre dışı bırakmak için, `1`değeri olan `WEBSITE_AUTH_SKIP_PRINCIPAL` adlı bir uygulama ayarı oluşturun. App Service tarafından eklenen tüm servlet filtrelerini devre dışı bırakmak için, `1`değeriyle `WEBSITE_SKIP_FILTERS` adında bir ayar oluşturun.

### <a name="configure-tlsssl"></a>TLS/SSL 'yi yapılandırma

Var olan bir SSL sertifikasını karşıya yüklemek ve uygulamanızın etki alanı adına bağlamak için, [Özel BIR DNS adını Azure App Service BIR SSL bağlamasıyla güvenli hale](configure-ssl-bindings.md) getirin. Varsayılan olarak, uygulamanız HTTP bağlantılarına hala izin verir-SSL ve TLS 'yi zorlamak için öğreticideki belirli adımları izleyin.

### <a name="use-keyvault-references"></a>Keykasası başvurularını kullanma

[Azure Keykasası](../key-vault/key-vault-overview.md) , erişim ilkeleri ve denetim geçmişi ile merkezi gizli yönetim sağlar. Gizli dizileri (parolalar veya bağlantı dizeleri gibi) anahtar kasasında saklayabilir ve ortam değişkenleri aracılığıyla uygulamanızdaki bu gizli dizilere erişebilirsiniz.

İlk olarak, uygulamanıza [Key Vault erişim verme](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) ve [bir uygulama ayarında gizli diziniz Için bir Anahtar Kasası başvurusu yapma](app-service-key-vault-references.md#reference-syntax)yönergelerini izleyin. App Service terminaline uzaktan erişirken ortam değişkenini yazdırarak başvurunun gizli olarak çözümlendiğini doğrulayabilirsiniz.

Bu gizli dizileri Spring veya Tomcat yapılandırma dosyasına eklemek için ortam değişkeni ekleme söz dizimini (`${MY_ENV_VAR}`) kullanın. Spring yapılandırma dosyaları için lütfen [externalized yapılandırmalarında](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)bu belgelere bakın.


## <a name="configure-apm-platforms"></a>APM platformlarını yapılandırma

Bu bölümde, Newrelik ve AppDynamics uygulama performansı izleme (APM) platformlarıyla Linux üzerinde Azure App Service dağıtılan Java uygulamalarının nasıl bağlanacağı gösterilmektedir.

### <a name="configure-new-relic"></a>Yeni relik yapılandırma

1. [NewRelic.com](https://newrelic.com/signup) adresinde yeni bir relik hesabı oluşturun
2. Newrelik 'ten Java aracısını indirin, *newrelic-Java-x. x. x. zip*dosyasına benzer bir dosya adı olacaktır.
3. Lisans anahtarınızı kopyalayın, aracıyı daha sonra yapılandırmak için gerekir.
4. [Kudu konsolunu](https://github.com/projectkudu/kudu/wiki/Kudu-console) kullanarak */Home/site/Wwwroot/APM*adlı yeni bir dizin oluşturun.
5. Paketi açılan yeni relik Java aracı dosyalarını */Home/site/Wwwroot/APM*altındaki bir dizine yükleyin. Aracınızın dosyaları */Home/site/Wwwroot/APM/newrelik*konumunda olmalıdır.
6. */Home/site/Wwwroot/APM/newrelic/newrelic.exe* konumundaki YAML dosyasını değiştirin ve yer tutucu lisans değerini kendi lisans anahtarınızla değiştirin.
7. Azure portal, App Service uygulamanıza gidin ve yeni bir uygulama ayarı oluşturun.
    - Uygulamanız **Java SE**kullanıyorsa, `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`değeri ile `JAVA_OPTS` adlı bir ortam değişkeni oluşturun.
    - **Tomcat**kullanıyorsanız, `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`değeri ile `CATALINA_OPTS` adlı bir ortam değişkeni oluşturun.

### <a name="configure-appdynamics"></a>AppDynamics 'i yapılandırma

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) adresinde bir AppDynamics hesabı oluşturun
2. AppDynamics Web sitesinden Java aracısını indirin, dosya adı *AppServerAgent-x. x. x. xxxxx. zip* ile benzerdir.
3. [Kudu konsolunu](https://github.com/projectkudu/kudu/wiki/Kudu-console) kullanarak */Home/site/Wwwroot/APM*adlı yeni bir dizin oluşturun.
4. Java aracı dosyalarını */Home/site/Wwwroot/APM*altındaki bir dizine yükleyin. Aracınızın dosyaları */Home/site/Wwwroot/APM/AppDynamics*konumunda olmalıdır.
5. Azure portal, App Service uygulamanıza gidin ve yeni bir uygulama ayarı oluşturun.
    - **Java SE**kullanıyorsanız, `JAVA_OPTS` adında bir ortam değişkeni oluşturun; burada `<app-name>` App Service adınız `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`.
    - **Tomcat**kullanıyorsanız, `CATALINA_OPTS` adında bir ortam değişkeni oluşturun; burada `<app-name>` App Service adınız `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`.

>  `JAVA_OPTS` veya `CATALINA_OPTS`için bir ortam değişkeni zaten varsa, `-javaagent:/...` seçeneğini geçerli değerin sonuna ekleyin.

## <a name="data-sources"></a>Veri kaynakları

### <a name="tomcat"></a>Tomcat

Bu yönergeler tüm veritabanı bağlantıları için geçerlidir. Yer tutucuları, seçtiğiniz veritabanının sürücü sınıfı adı ve JAR dosyası ile doldurmanız gerekir. , Ortak veritabanları için sınıf adları ve sürücü indirmeleri içeren bir tablodur.

| Veritabanı   | Sürücü sınıfı adı                             | JDBC Sürücüsü                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [İndir](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [İndir](https://dev.mysql.com/downloads/connector/j/) ("platformdan bağımsız" seçeneğini belirleyin) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [İndir](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Tomcat 'i Java veritabanı bağlantısı (JDBC) veya Java Kalıcılık API 'SI (JPA) kullanacak şekilde yapılandırmak için, önce başlangıçta Tomcat tarafından okunan `CATALINA_OPTS` ortam değişkenini özelleştirin. Bu değerleri [App Service Maven eklentisindeki](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)bir uygulama ayarı aracılığıyla ayarlayın:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ya da Azure portal içindeki **yapılandırma** > **uygulama ayarları** sayfasında ortam değişkenlerini ayarlayın.

Daha sonra, veri kaynağının bir uygulama için mi yoksa Tomcat servlet üzerinde çalışan tüm uygulamalar için mi kullanılabilir olacağını saptayın.

#### <a name="application-level-data-sources"></a>Uygulama düzeyi veri kaynakları

1. Projenizin *meta INF/* dizininde bir *Context. xml* dosyası oluşturun. Yoksa *meta INF/* dizin oluşturun.

2. *Context. xml*dosyasında veri kaynağını bir JNDI adresine bağlamak için bir `Context` öğesi ekleyin. `driverClassName` yer tutucusunu, yukarıdaki tablodaki sürücünüzün sınıf adıyla değiştirin.

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

3. Uygulamanızdaki veri kaynağını kullanmak için uygulamanızın *Web. xml* ' i güncelleştirin.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
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

## <a name="configuring-tomcat"></a>Tomcat yapılandırma

Tomcat 'in `server.xml` veya diğer yapılandırma dosyalarını düzenlemek için önce portalda Tomcat ana sürümünüzü bir yere göz atın.

1. `env` komutunu çalıştırarak sürümünüz için Tomcat giriş dizinini bulun. `AZURE_TOMCAT`başlayan ve ana sürümünüzle eşleşen ortam değişkenini arayın. Örneğin, `AZURE_TOMCAT85_HOME` Tomcat 8,5 için Tomcat dizinine işaret eder.
1. Sürümünüz için Tomcat giriş dizinini tanımladıktan sonra yapılandırma dizinini `D:\home`kopyalayın. Örneğin, `AZURE_TOMCAT85_HOME` bir `D:\Program Files (x86)\apache-tomcat-8.5.37`değeri varsa, kopyalanmış dizinin yeni yolu `D:\home\apache-tomcat-8.5.37`olur.

Son olarak, App Service yeniden başlatın. Dağıtımlarınız daha önce olduğu gibi `D:\home\site\wwwroot\webapps` gitmelidir.

## <a name="java-runtime-statement-of-support"></a>Java Runtime desteği

### <a name="jdk-versions-and-maintenance"></a>JDK sürümleri ve bakım

Azure 'un desteklenen Java Geliştirme Seti (JDK), [Azul sistemleri](https://www.azul.com/)aracılığıyla bir [Zulu](https://www.azul.com/downloads/azure-only/zulu/) dili olarak sunulmaktadır.

Ana sürüm güncelleştirmeleri, Windows için Azure App Service yeni çalışma zamanı seçenekleri aracılığıyla sağlanacaktır. Müşteriler, App Service dağıtımını yapılandırarak ve ana güncelleştirmenin ihtiyaçlarını karşıladığından sorumlu olduğundan, bu yeni Java sürümlerine güncelleştirir.

Desteklenen JDKs, her yıl Ocak, Nisan, Temmuz ve Ekim ayında otomatik olarak üç ayda bir düzeltme eki uygulanır. Azure 'da Java hakkında daha fazla bilgi için lütfen [Bu destek belgesine](https://docs.microsoft.com/azure/java/jdk/)bakın.

### <a name="security-updates"></a>Güvenlik güncelleştirmeleri

Önemli güvenlik açıklarına yönelik düzeltme ekleri ve düzeltmeler Azul sistemlerinden kullanılabilir hale geldiğinde serbest bırakılır. "Ana" güvenlik açığı, [NIST ortak güvenlik açığı Puanlama sistemi, sürüm 2](https://nvd.nist.gov/cvss.cfm)üzerinde 9,0 veya üzeri bir taban puanı tarafından tanımlanır.

### <a name="deprecation-and-retirement"></a>Kullanımdan kaldırma ve kullanımdan kaldırma

Desteklenen bir Java çalışma zamanı devre dışı bırakırsa, etkilenen çalışma zamanını kullanan Azure geliştiricileri, çalışma zamanı kullanımdan kalkmadan önce en az altı ayda bir kullanımdan kaldırma olarak verilmeyecektir.

### <a name="local-development"></a>Yerel geliştirme

Geliştiriciler, Azul ['nin indirme sitesinden](https://www.azul.com/downloads/azure-only/zulu/)yerel geliştirme Için Azul Zulu kurumsal JDK üretim sürümünü indirebilir.

### <a name="development-support"></a>Geliştirme desteği

Azure [tarafından desteklenen Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) için ürün desteği, Azure için geliştirme sırasında veya [nitelikli bir Azure destek planı](https://azure.microsoft.com/support/plans/)ile [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) Microsoft aracılığıyla edinilebilir.

### <a name="runtime-support"></a>Çalışma zamanı desteği

Geliştiriciler, [tam destek planına](https://azure.microsoft.com/support/plans/)sahip olmaları durumunda Azul Zulu JDKs ile ilgili [bir sorunu](/azure/azure-supportability/how-to-create-azure-support-request) , Azure desteği aracılığıyla açabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu konuda, Windows üzerinde Azure App Service için desteklenen Java çalışma zamanı açıklaması sağlanmaktadır.

- Web uygulamalarını barındırma hakkında daha fazla bilgi için Azure App Service [App Service genel bakış](overview.md)bölümüne bakın.
- Azure 'da Java hakkında daha fazla bilgi için bkz. [Java Için Azure Geliştirme Merkezi](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
