---
title: Azure Spring Cloud uygulamanıza bir Azure Cosmos DB bağlama
description: Azure Cosmos DB Azure Spring Cloud uygulamanıza nasıl bağlayacağınızı öğrenin
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: bf761bded38ffa975610c7a7f217e911704ae3bf
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227951"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Azure Cosmos DB veritabanını Azure Spring Cloud uygulamanıza bağlama

**Bu makale şu şekilde geçerlidir:** ✔️ Java

Spring Boot uygulamalarınızı el ile yapılandırmak yerine Azure Spring Cloud kullanarak Azure hizmetlerini otomatik olarak uygulamalarınıza bağlayabilirsiniz. Bu makalede, uygulamanızı bir Azure Cosmos DB veritabanına nasıl bağlayacağınız gösterilmektedir.

Ön koşullar:

* Dağıtılmış bir Azure yay bulutu örneği. Kullanmaya başlamak için [Azure CLI aracılığıyla dağıtmaya yönelik hızlı başlangıç](spring-cloud-quickstart.md) Öğreticimizi izleyin.
* En düşük izin düzeyi katkıda bulunan Azure Cosmos DB hesabı.

## <a name="bind-azure-cosmos-db"></a>Bağlama Azure Cosmos DB

Azure Cosmos DB, bağlamayı destekleyen beş farklı API türüne sahiptir. Aşağıdaki yordamda bunların nasıl kullanılacağı gösterilmektedir:

1. Azure Cosmos DB veritabanı oluşturun. Yardım için [veritabanı oluşturma](../cosmos-db/create-cosmosdb-resources-portal.md) hızlı başlangıç kılavuzuna bakın. 

1. Veritabanınızın adını kaydedin. Bu yordam için veritabanı adı **TestDB**' dir.

1. Azure Spring Cloud uygulamanızın pom.xml dosyasına aşağıdaki bağımlılıklardan birini ekleyin. API türü için uygun olan bağımlılığı seçin.

    * API türü: çekirdek (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * API türü: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API türü: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API türü: Gremlin (grafik)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API türü: Azure tablosu

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. `az spring-cloud app update`Geçerli dağıtımı güncelleştirmek için kullanın veya `az spring-cloud app deployment create` Yeni bir dağıtım oluşturmak için kullanın. Bu komutlar uygulamayı yeni bağımlılık ile güncelleştirir veya oluşturur.

1. Azure portal Azure Spring Cloud Service sayfanıza gidin. **Uygulama panosu** ' na gidin ve Azure Cosmos DB bağlanacak uygulamayı seçin. Bu uygulama, önceki adımda güncelleştirdiğiniz veya dağıttığınız aynı bir uygulamadır.

1. **Hizmet bağlaması**' nı seçin ve **hizmet bağlamayı oluştur**' u seçin. Formu doldururken şunları seçin:
   * **Bağlama türü** değeri **Azure Cosmos DB**.
   * API türü.
   * Veritabanı adınız.
   * Azure Cosmos DB hesabı.

    > [!NOTE]
    > Cassandra kullanıyorsanız, veritabanı adı için bir anahtar alanı kullanın.

1. Uygulama sayfasında **Yeniden Başlat** ' i seçerek uygulamayı yeniden başlatın.

1. Hizmetin doğru şekilde bağlandığından emin olmak için bağlama adını seçin ve ayrıntılarını doğrulayın. `property`Alan bu örneğe benzer olmalıdır:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Spring Cloud uygulamanızı bir Azure Cosmos DB veritabanına bağlamayı öğrendiniz. Uygulamanıza hizmet bağlama hakkında daha fazla bilgi edinmek için bkz. [redsıs Cache Için Azure önbelleğine bağlama](spring-cloud-howto-bind-redis.md).