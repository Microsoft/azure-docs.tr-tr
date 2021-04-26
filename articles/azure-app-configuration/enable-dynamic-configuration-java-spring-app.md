---
title: Spring Boot uygulamasında dinamik yapılandırma kullanma
titleSuffix: Azure App Configuration
description: Spring Boot uygulamaları için yapılandırma verilerini dinamik olarak güncelleştirme hakkında bilgi edinin
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 590f221b0a4980d462267dd8c3a73ca7d02583fd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625526"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Öğretici: bir Java Spring uygulamasında dinamik yapılandırma kullanma

Uygulama yapılandırmasının Spring için iki kitaplığı vardır. `spring-cloud-azure-appconfiguration-config` Spring Boot gerektirir ve bir bağımlılığı alır `spring-cloud-context` . `spring-cloud-azure-appconfiguration-config-web` Spring Boot ile birlikte Spring Web 'i gerektirir. Her iki kitaplık de yenilenen yapılandırma değerlerini denetlemek için el ile tetikleme desteği sağlar. `spring-cloud-azure-appconfiguration-config-web` Ayrıca yapılandırma yenileme otomatik denetimi için de destek ekler.

Yenile, uygulamanızı yeniden başlatmak zorunda kalmadan yapılandırma değerlerinizi yenileirsiniz, ancak içindeki tüm adamlar yeniden oluşturulmasına neden olur `@RefreshScope` . İstemci kitaplığı, yapılandırma deposuna çok fazla çağrı yapmaktan kaçınmak için şu anda yüklü olan yapılandırmaların karma kimliğini önbelleğe alır. Yenileme işlemi, değeri yapılandırma deposunda değiştiği zaman, önbelleğe alınan değerin süresi doluncaya kadar değeri güncelleştirmez. Her istek için varsayılan sona erme saati 30 saniyedir. Gerekirse, geçersiz kılınabilir.

`spring-cloud-azure-appconfiguration-config-web`Otomatik yenileme, özellikle de Web 'e yay ve etkinlik temel alınarak tetiklenir `ServletRequestHandledEvent` . Bir `ServletRequestHandledEvent` tetiklenemez, `spring-cloud-azure-appconfiguration-config-web` önbelleğin süre sonu süresi dolmuşsa bile otomatik yenileme yenileme tetiklemez.

## <a name="use-manual-refresh"></a>El ile yenileme kullan

Uygulama Yapılandırması `AppConfigurationRefresh` , önbelleğin kullanım dışı olup olmadığını denetlemek için kullanılabilir ve zaman aşımına uğradı bir yenileme tetikleyin.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh``refreshConfigurations()` `Future` , bir yenileme tetikleniyorsa true, değilse false döndürür. Yanlış, önbelleğin süre sonu zamanının süresinin dolmadığı, hiçbir değişiklik olmadığı veya başka bir iş parçacığının Şu anda yenileme işlemi denetlemesi anlamına gelir.

## <a name="use-automated-refresh"></a>Otomatik yenilemeyi kullan

Otomatik yenilemeyi kullanmak için, uygulama [yapılandırması Için Spring Boot hızlı](quickstart-java-spring-app.md)başlangıcını izleyerek oluşturduğunuz uygulama gibi uygulama yapılandırması kullanan bir Spring Boot uygulamasıyla başlayın.

Sonra, *pom.xml* dosyasını bir metin düzenleyicisinde açın ve için bir ekleyin `<dependency>` `spring-cloud-azure-appconfiguration-config-web` .

**Yay bulutu 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Yay bulutu 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Uygulamayı yerel olarak çalıştırma ve test etme

1. Spring Boot uygulamanızı Maven’le oluşturun ve çalıştırın.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Bir tarayıcı penceresi açın ve URL 'ye gidin: `http://localhost:8080` .  Anahtarınızla ilişkili iletiyi görürsünüz.

    Ayrıca, uygulamanızı test etmek için *kıvrımlı* da kullanabilirsiniz, örneğin: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Dinamik yapılandırmayı test etmek için uygulamanızla ilişkili Azure uygulama yapılandırma portalını açın. **Yapılandırma Gezgini**' ni seçin ve görüntülenmiş anahtarın değerini güncelleştirin, örneğin:

    | Anahtar | Değer |
    |---|---|
    | uygulama/yapılandırma. ileti | Merhaba-güncelleştirildi |

1. Yeni iletinin görüntülendiğini görmek için tarayıcı sayfasını yenileyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulama yapılandırmasından yapılandırma ayarlarını dinamik olarak yenilemek için Spring Boot uygulamanızı etkinleştirdiniz. Azure yönetilen kimliğin uygulama yapılandırmasına erişimi kolaylaştırmak için nasıl kullanılacağını öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)
