---
title: Redsıs için Azure önbelleğini Azure Spring Cloud uygulamanıza bağlama | Microsoft Docs
description: Reda için Azure önbelleğini Azure Spring Cloud uygulamanıza bağlamayı öğrenin
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: a901e4194909df85f53799d5937515e42ea87a69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607566"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Öğretici: Azure hizmetlerini Azure Spring Cloud uygulamanıza bağlama: Redsıs için Azure önbelleği

Azure yay bulutu, Spring Boot uygulamanızı el ile yapılandırmak yerine, Azure hizmetlerini uygulamalarınıza otomatik olarak bağlamanıza olanak tanır. Bu makalede, redin için uygulamanızı Azure önbelleğine bağlama işlemi gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar

* Dağıtılan bir Azure yay bulutu örneği
* Redsıs hizmet örneği için bir Azure önbelleği
* Azure CLı için Azure yay bulutu uzantısı

Dağıtılmış bir Azure yay bulut örneğiniz yoksa, ilk Spring Cloud uygulamanızı dağıtmak için bu [hızlı](spring-cloud-quickstart-launch-app-portal.md) başlangıçta bulunan adımları izleyin.

## <a name="bind-azure-cache-for-redis"></a>Redsıs için Azure önbelleğini bağlama

1. Aşağıdaki bağımlılığı projenizin `pom.xml` ekleyin

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. `application.properties` dosyasında, varsa `spring.redis.*` özellikleri kaldırın

1. `az spring-cloud app update` kullanarak geçerli dağıtımı güncelleştirin veya `az spring-cloud app deployment create`kullanarak yeni bir dağıtım oluşturun.

1. Azure portal Azure Spring Cloud Service sayfanıza gidin. **Uygulama panosunu** bulun ve Redsıs Için Azure önbelleğine bağlanacak uygulamayı seçin.  Bu, önceki adımda güncelleştirdiğiniz veya dağıttığınız bir uygulamadır. Sonra `Service binding` öğesini seçin ve `Create service binding` düğmesini seçin. **Bağlantı türü** `Azure Cache for Redis`, redsıs sunucunuzu ve birincil anahtar seçeneğini seçtiğinizden emin olmak için formu doldurun. 

1. Uygulamayı yeniden başlatın ve bu bağlamanın şimdi çalışması gerekir.

1. Hizmet bağlamasının doğru olduğundan emin olmak için bağlama adını seçin ve ayrıntılarını doğrulayın. `property` alanı şöyle görünmelidir:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Spring Cloud uygulamanızı Azure Redsıs önbelleğine bağlamayı öğrendiniz.  Uygulamanıza hizmet bağlama hakkında daha fazla bilgi edinmek için, bir uygulamayı MySQL DB 'ye bağlama öğreticisine geçin.

> [!div class="nextstepaction"]
> Azure [MySQL hizmetini Azure Spring Cloud Service 'e bağlamayı öğrenin](spring-cloud-tutorial-bind-mysql.md).