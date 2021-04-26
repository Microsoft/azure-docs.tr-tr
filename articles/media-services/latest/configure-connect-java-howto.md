---
title: Azure Media Services v3 API 'sine bağlanma-Java
description: Bu makalede, Java ile Azure Media Services v3 API 'sine nasıl bağlanabileceğinizi açıklamaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 06923d7c198edc324d85b726cf91694d8cf7e1ca
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961362"
---
# <a name="connect-to-media-services-v3-api---java"></a>Media Services v3 API 'sine bağlanma-Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makalede hizmet sorumlusu oturum açma yöntemi kullanılarak Azure Media Services v3 Java SDK 'sına nasıl bağlanabilmeniz gösterilmektedir.

Bu makalede, örnek uygulamayı geliştirmek için Visual Studio Code kullanılır.

## <a name="prerequisites"></a>Önkoşullar

- Yüklemek için [Visual Studio Code Java yazma](https://code.visualstudio.com/docs/java/java-tutorial) ' yı izleyin:

   - JDK
   - Apache Maven
   - Java Uzantı paketi
- `JAVA_HOME`Ve ortam değişkenlerini ayarladığınızdan emin olun `PATH` .
- [Media Services hesabı oluşturun](./account-create-how-to.md). Kaynak grubu adını ve Media Services hesap adını unutduğunuzdan emin olun.
- [Erişim API 'leri](./access-api-howto.md) konusundaki adımları izleyin. Abonelik KIMLIĞI, uygulama KIMLIĞI (istemci KIMLIĞI), kimlik doğrulama anahtarı (gizli) ve daha sonraki bir adımda ihtiyacınız olan kiracı KIMLIĞINI kaydedin.

Ayrıca şunları gözden geçirin:

- [Visual Studio Code 'de Java](https://code.visualstudio.com/docs/languages/java)
- [VS Code 'de Java proje yönetimi](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> [Adlandırma kurallarını](media-services-apis-overview.md#naming-conventions)gözden geçirin.

## <a name="create-a-maven-project"></a>Maven projesi oluşturma

Bir komut satırı aracı açın ve `cd` Projeyi oluşturmak istediğiniz dizine gidin.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Komutunu çalıştırdığınızda,, `pom.xml` `App.java` ve diğer dosyalar oluşturulur. 

## <a name="add-dependencies"></a>Bağımlılık Ekle

1. Visual Studio Code, projenizin bulunduğu klasörü açın
1. Bul ve aç `pom.xml`
1. Gerekli bağımlılıkları ekleyin.

   Bkz `pom.xml` . [video kodlama](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESCustomPreset/pom.xml) örneği.

## <a name="connect-to-the-java-client"></a>Java istemcisine bağlanma

1. `App.java`Altındaki dosyayı açın `src\main\java\com\azure\ams` ve paketinizin en üste eklendiğinden emin olun:

    ```java
    package com.azure.ams;
    ```
1. Paket deyimi altında bu içeri aktarma deyimlerini ekleyin:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. İstek yapmak için ihtiyaç duyduğunuz Active Directory kimlik bilgilerini oluşturmak için, uygulama sınıfının Main yöntemine aşağıdaki kodu ekleyin ve [erişim API 'lerinden](./access-api-howto.md)aldığınız değerleri ayarlayın:
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Uygulamayı çalıştırın.

## <a name="see-also"></a>Ayrıca bkz.

- [Media Services kavramlar](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java başvurusu](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01: Azure-MGMT-medya](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Sonraki adımlar

Artık `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` varlıkları ekleme ve düzenleme ' ye başlayabilirsiniz.

Daha fazla kod örneği için bkz. [Java SDK örnek](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) deposu.
