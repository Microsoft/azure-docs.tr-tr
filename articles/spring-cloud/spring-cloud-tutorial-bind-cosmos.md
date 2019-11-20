---
title: Azure Spring Cloud uygulamanıza bir Azure Cosmos DB bağlama | Microsoft Docs
description: Azure Cosmos DB Azure Spring Cloud uygulamanıza nasıl bağlayacağınızı öğrenin
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 76a2b9f305f041a19b8d7ace8234a804825f6a0e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607124"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Öğretici: Azure Spring Cloud uygulamanıza bir Azure Cosmos DB bağlama

Azure yay bulutu, Spring Boot uygulamanızı el ile yapılandırmak yerine, Azure hizmetlerini uygulamalarınıza otomatik olarak bağlamanıza olanak tanır. Bu makalede, uygulamanızı bir Azure Cosmos DB bağlama gösterilmektedir.

Ön koşullar:
* Dağıtılmış bir Azure yay bulutu örneği.  Başlamak için [hızlı](spring-cloud-quickstart-launch-app-cli.md) başlarımızı izleyin.
* En düşük izin düzeyi katkıda bulunan Azure Cosmos DB hesap.

## <a name="bind-azure-cosmos-db"></a>Bağlama Azure Cosmos DB

Azure Cosmos DB, bağlamayı destekleyen beş farklı API türüne sahiptir:

1. Azure Cosmos DB veritabanı oluşturun. Veritabanı oluşturma konusunda yardım için [Bu makaleye başvurun](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) . Veritabanınızın adını kaydedin. Bizde `testdb`olarak adlandırılmıştır.

1. Aşağıdaki bağımlılıklardan birini, API türüne göre Spring Cloud uygulamanızın `pom.xml` ekleyin.
    
    #### <a name="api-type-core-sql"></a>API türü: çekirdek (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>API türü: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>API türü: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>API türü: Gremlin (grafik)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>API türü: Azure tablosu

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. `az spring-cloud app update` kullanarak geçerli dağıtımı güncelleştirin veya bu değişiklik için `az spring-cloud app deployment create`kullanarak yeni bir dağıtım oluşturun.  Bu komutlar uygulamayı yeni bağımlılık ile güncelleştirir veya oluşturur.

1. Azure portal Azure Spring Cloud Service sayfanıza gidin. Bu, önceki adımda güncelleştirdiğiniz veya dağıttığınız bir uygulamadır. **Uygulama panosunu** bulun ve Cosmos DB bağlanacak uygulamayı seçin. Sonra `Service binding` ' yi seçin ve `Create service binding` düğmesini seçin. Formu doldurun, **bağlama türü** `Azure Cosmos DB`, API türü, veritabanınızın adı ve Azure Cosmos DB hesabı ' nı seçin.

    > [!NOTE]
    > Cassandra kullanıyorsanız, veritabanı adı için bir anahtar alanı kullanın.

1. Uygulama sayfasındaki **Yeniden Başlat** düğmesini seçerek uygulamayı yeniden başlatın.

1. Hizmetin doğru şekilde bağlandığından emin olmak için bağlama adını seçin ve ayrıntılarını doğrulayın. `property` alanı şuna benzer olmalıdır:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Spring Cloud uygulamanızı CosmosDB 'ye bağlamayı öğrendiniz.  Uygulamanızı bir Azure Redis Cache nasıl bağlayacağınızı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Spring Cloud uygulamasını bir Azure Redis Cache bağlayın](spring-cloud-tutorial-bind-redis.md).
