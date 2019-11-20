---
title: Linux 'ta Java Enterprise Web App derleme-Azure App Service | Microsoft Docs
description: Linux 'ta Azure App Service üzerinde çalışan bir Java Enterprise uygulamasını nasıl alabileceğinizi öğrenin.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 2d26d9e145030e5972289c224dc2f76078d67527
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498492"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Öğretici: Azure 'da Java EE ve Postgres Web uygulaması oluşturma

Bu öğreticide, Azure App Service üzerinde Java Enterprise Edition (EE) Web uygulaması oluşturma ve bunu bir Postgres veritabanına bağlama işlemlerinin nasıl yapılacağı gösterilir. İşiniz bittiğinde, [Linux üzerinde azure App Service](app-service-linux-intro.md)çalışan [Postgres için Azure veritabanı](https://azure.microsoft.com/services/postgresql/) 'nda veri depolayan bir yavama uygulamanız olur. [](https://www.wildfly.org/about/)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Maven kullanarak bir Java EE uygulamasını Azure 'a dağıtma
> * Azure'da Postgres veritabanı oluşturma
> * Yavalar sunucusunu, Postgres 'yi kullanacak şekilde yapılandırma
> * Uygulamayı güncelleştirme ve yeniden dağıtma
> * Birim testlerini açık bir şekilde Çalıştır

## <a name="prerequisites"></a>Önkoşullar

1. [Git'i indirin ve yükleyin](https://git-scm.com/)
2. [Maven 3 indirin ve yükleyin](https://maven.apache.org/install.html)
3. [Azure CLı 'yı indirme ve yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Örnek uygulamayı kopyalama ve düzenleme

Bu adımda, örnek uygulamayı kopyalacaksınız ve dağıtım için Maven proje nesne modelini (pod veya *Pod. xml*) yapılandıracaksınız.

### <a name="clone-the-sample"></a>Örneği kopyalama

Terminal penceresinde, çalışma dizinine gidin ve [örnek depoyu](https://github.com/Azure-Samples/wildfly-petstore-quickstart)kopyalayın.

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Maven Pod 'yi güncelleştirme

Maven Azure eklentisini App Service istenen adı ve kaynak grubuyla güncelleştirin. Önceden App Service planı veya örneği oluşturmanız gerekmez. Maven eklentisi, kaynak grubunu oluşturur ve henüz yoksa App Service.

Değişiklikleri yapmak için, aşağı `<plugins>` kaydırarak *Pod. xml*, satır 200 bölümüne kaydırabilirsiniz.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

`YOUR_APP_NAME` Ve`YOUR_RESOURCE_GROUP` App Service ve kaynak grubunuzun adlarını değiştirin.

## <a name="build-and-deploy-the-application"></a>Uygulama derleme ve dağıtma

Şimdi de Maven 'i kullanarak uygulamamızı derleyip App Service.

### <a name="build-the-war-file"></a>. War dosyasını oluşturun

Bu projedeki Pod, uygulamayı bir Web arşivi (WAR) dosyasına paketlemek üzere yapılandırılmıştır. Maven kullanarak uygulamayı oluşturun:

```bash
mvn clean install -DskipTests
```

Bu uygulamadaki test çalışmaları, uygulama bir Yavaya dağıtıldığında çalışacak şekilde tasarlanmıştır. Yerel olarak oluşturulacak testleri atlayacağız ve uygulama App Service dağıtıldıktan sonra testleri çalıştırırsınız.

### <a name="deploy-to-app-service"></a>App Service’e dağıtma

Artık WAR hazır olduğuna göre, App Service dağıtmak için Azure eklentisini kullanabiliriz:

```bash
mvn azure-webapp:deploy
```

Dağıtım tamamlandığında, bir sonraki adımla devam edin.

### <a name="create-a-record"></a>Kayıt oluştur

Bir tarayıcı açın ve gidin `https://<your_app_name>.azurewebsites.net/`. Tebrikler, Azure App Service için bir Java EE uygulaması dağıttıysanız!

Bu noktada, uygulama bellek içi H2 veritabanı kullanıyor. Gezinti çubuğunda "Yönetici" ye tıklayın ve yeni bir kategori oluşturun. App Service örneğinizi yeniden başlatırsanız, bellek içi veritabanınızdaki kayıt kaybedilir. Aşağıdaki adımlarda, Azure 'da bir Postgres veritabanı sağlayarak bunu çözeceğinizi ve Yavalarını kullanmaya yönelik olarak yapılandıracaksınız.

![Yönetim düğmesi konumu](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Bir Postgres veritabanı sağlama

Bir Postgres veritabanı sunucusu sağlamak için bir Terminal açın ve aşağıdaki örnekte gösterildiği gibi [az Postgres Server Create](https://docs.microsoft.com/cli/azure/postgres/server) komutunu kullanın. Daha önce App Service örneğiniz için verdiğiniz aynı kaynak grubunu kullanarak yer tutucuları (açılı ayraçlar dahil) tercih ettiğiniz değerlerle değiştirin. Sağladığınız yönetici kimlik bilgileri gelecekteki erişimi etkinleştirecek, bu nedenle daha sonra kullanmak üzere bunları bir yere uyguladığınızdan emin olun.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

Bu komutu çalıştırdıktan sonra, Azure portal gidin ve Postgres veritabanınıza gidin. Dikey pencere yukarı yüklendiğinde, "sunucu adı" ve "Sunucu Yöneticisi oturum açma adı" değerlerini kopyalayın, daha sonra ihtiyacınız olacaktır.

### <a name="allow-access-to-azure-services"></a>Azure hizmetlerine erişime izin ver

Azure veritabanı dikey penceresinin **bağlantı güvenliği** panelinde, "Azure hizmetlerine erişime izin ver" düğmesine **Açık** konuma geçiş yapın.

![Azure hizmetlerine erişime izin verme](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Postgres için Java uygulamanızı güncelleştirme

Şimdi, Postgres veritabanınızı kullanmasını sağlamak için Java uygulamasında bazı değişiklikler yapacağız.

### <a name="add-postgres-credentials-to-the-pom"></a>Pod 'ye Postgres kimlik bilgileri ekleme

*Poz. xml*dosyasında, büyük harfli yer tutucu değerlerini Postgres sunucu adı, yönetici oturum açma adı ve parola ile değiştirin. Bu alanlar Azure Maven eklentisinin içindedir. ( `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME`Ve `<value>` etiketlerinin içinde bulunduğundan emin olun... `YOUR_PG_PASSWORD` `<name>` Etiketler içinde değil!)

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Java Işlem API 'sini güncelleştirme

Daha sonra, Java uygulamamız, daha önce kullanmakta olduğumuz bellek içi H2 veritabanı yerine Postgres ile iletişim kuracak şekilde, Java Işlem API 'SI (JPA) yapılandırmanızı düzenlememiz gerekir. *Src/Main/Resources/meta-INF/kalıcılık. xml*için bir düzenleyici açın. `<jta-data-source>` değerini `java:jboss/datasources/postgresDS` ile değiştirin. JTA XML 'niz artık bu ayarı içermelidir:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>Yavaya uygulama sunucusunu yapılandırma

Yeniden yapılandırılmış uygulamamıza dağıtım yapmadan önce, Yavama uygulama sunucusunu Postgres modülü ve bağımlılıklarıyla güncelleştirmemiz gerekir. Daha fazla yapılandırma bilgisi, Yavama [sunucusunda](configure-language-java.md#configure-java-ee-wildfly)bulunabilir.

Sunucuyu yapılandırmak için *wildfly_config/* dizininde dört dosya gerekir:

- **PostgreSQL-42.2.5. jar**: Bu JAR dosyası, Postgres için JDBC sürücüsüdür. Daha fazla bilgi için [Resmi Web sitesine](https://jdbc.postgresql.org/index.html)bakın.
- **Postgres-Module. xml**: Bu XML dosyası, Postgres modülü (org. Postgres) için bir ad bildirir. Ayrıca, modülün kullanılması için gerekli kaynakları ve bağımlılıkları da belirtir.
- **jboss_cli_commands. CLI**: Bu dosya, Jpatron CLı tarafından yürütülecek yapılandırma komutlarını içerir. Bu komutlar, Yavares uygulama sunucusuna Postgres modülünü ekler, kimlik bilgilerini sağlar, bir JNDı adı bildirir, zaman aşımı eşiğini ayarlar vb. Jpatron CLı 'yı tanımıyorsanız, [resmi belgelere](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)bakın.
- **startup_script. sh**: Son olarak, App Service örneğiniz her başlatıldığında bu kabuk betiği yürütülür. Betik yalnızca bir işlev gerçekleştirir: *jboss_cli_commands. CLI* Içindeki komutları JPATRON CLI 'ya boru.

Özellikle *jboss_cli_commands. CLI*olan bu dosyaların içeriğini okumayı çok önertik.

### <a name="ftp-the-configuration-files"></a>Yapılandırma dosyalarını FTP

*Wildfly_config/* ' un içeriğini App Service ÖRNEĞIMIZE FTP ile kullanacağız. FTP kimlik bilgilerinizi almak için Azure portal App Service dikey penceresinde **Yayımlama profilini al** düğmesine tıklayın. FTP Kullanıcı adınız ve parolanız, indirilen XML belgesinde olacaktır. Yayımlama profili hakkında daha fazla bilgi için [Bu belgeye](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)bakın.

Tercih ettiğiniz FTP aracını kullanarak, *wildfly_config/* to */Home/site/Deployments/araçlar/* konumundaki dört dosyayı aktarın. (Dizini, yalnızca dosyaların kendilerini aktarmayacağınızı unutmayın.)

### <a name="finalize-app-service"></a>Sonlandırma App Service

App Service dikey penceresinde "uygulama ayarları" paneline gidin. "Çalışma zamanı" altında "başlangıç dosyası" alanını */Home/site/Deployments/Tools/startup_script.exe*olarak ayarlayın. Bu, App Service örneği oluşturulduktan sonra, ancak Yavaya sunucusu başlamadan önce kabuk betiğinin çalıştırılmasını sağlayacaktır.

Son olarak, App Service yeniden başlatın. Düğme, "genel bakış" masasında bulunur.

## <a name="redeploy-the-application"></a>Uygulamayı yeniden dağıtın

Bir Terminal penceresinde, uygulamanızı yeniden derleyin ve dağıtın.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Tebrikler! Uygulamanız şimdi bir Postgres veritabanı kullanıyor ve uygulamada oluşturulan tüm kayıtlar önceki H2 bellek içi veritabanı yerine Postgres 'de saklanacaktır. Bunu onaylamak için bir kayıt yapabilir ve App Service yeniden başlatabilirsiniz. Kayıtlar, uygulamanız yeniden başlatıldığında yine de orada olacaktır.

## <a name="clean-up"></a>Temizleme

Bu kaynaklara başka bir öğretici için ihtiyacınız yoksa (bkz. sonraki adımlar), aşağıdaki komutu çalıştırarak bunları silebilirsiniz:

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Maven kullanarak bir Java EE uygulamasını Azure 'a dağıtma
> * Azure'da Postgres veritabanı oluşturma
> * Yavalar sunucusunu, Postgres 'yi kullanacak şekilde yapılandırma
> * Uygulamayı güncelleştirme ve yeniden dağıtma
> * Birim testlerini açık bir şekilde Çalıştır

Özel bir DNS adını uygulamanıza nasıl eşleyeceğinizi öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: Özel DNS adını uygulamanıza eşleyin](../app-service-web-tutorial-custom-domain.md)

Ya da diğer kaynaklara göz atın:

> [!div class="nextstepaction"]
> [Java uygulamasını yapılandırma](configure-language-java.md)
