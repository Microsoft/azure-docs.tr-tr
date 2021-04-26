---
title: Erişim yapılandırması sunucusu ve hizmet kayıt defteri
titleSuffix: Azure Spring Cloud
description: Azure Active Directory rol tabanlı erişim denetimi ile yapılandırma sunucusu ve hizmet kayıt noktalarına erişme.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 90ee39039b0734bf434e7339a973358989c08175
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311371"
---
# <a name="access-config-server-and-service-registry"></a>Erişim yapılandırması sunucusu ve hizmet kayıt defteri

Bu makalede, Azure Active Directory (Azure AD) rol tabanlı erişim denetimi (RBAC) kullanılarak Azure Spring Cloud tarafından yönetilen Spring Cloud config Server ve Spring Cloud Service kayıt defterine nasıl erişebileceğiniz açıklanır.

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Azure AD Kullanıcı/Grup, MSI veya hizmet sorumlusu 'na rol atama

Azure AD 'yi ve RBAC 'yi kullanmak için, *Azure Spring Cloud Data Reader* rolünü bir Kullanıcı, Grup veya hizmet sorumlusuna aşağıdaki yordamla atamanız gerekir:

1. Hizmet örneğinizin hizmet genel bakış sayfasına gidin.

2. Erişim denetimi dikey penceresini açmak için **Access Control (IAM)** seçeneğine tıklayın.

3. **Ekle** düğmesine tıklayın ve **rol atamaları ekleyin** (eklemek için yetkilendirme gerekebilir).

4. **Rol** altında *Azure yay bulutu veri okuyucuyu* bulun ve seçin.
5. `User, group, or service principal` `User assigned managed identity` Kullanıcı türüne göre veya erişimi atayın. Kullanıcı arayın ve seçin.  
6. Şuna tıklayın: `Save`

   ![rol atama](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>Access config Server ve hizmet kayıt defteri uç noktaları

Azure Spring Cloud Data Reader rolü atandıktan sonra, müşteriler Spring Cloud config sunucusuna ve Spring Cloud Service kayıt defteri uç noktalarına erişebilirler. Aşağıdaki yordamları kullanın:

1. Erişim belirteci alın. Azure AD kullanıcısına Azure Spring Cloud Data Reader rolü atandıktan sonra, müşteriler bir erişim belirteci almak için Kullanıcı, hizmet sorumlusu veya yönetilen kimlik ile Azure CLı 'de oturum açmak üzere aşağıdaki komutları kullanabilir. Ayrıntılar için bkz. [Azure CLI kimlik doğrulaması](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Uç noktası oluşturun. Spring Cloud config Server ve Spring Cloud Service kayıt defteri 'nin Azure Spring Cloud tarafından yönetilen varsayılan uç noktalarını destekliyoruz. Daha fazla bilgi için bkz. [üretime hazırlık uç noktaları](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints). Müşteriler, uç noktalara erişerek Azure Spring Cloud tarafından yönetilen Spring Cloud config Server ve Spring Cloud Service kayıt defterinin tam listesini de alabilir:

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/'* 

>[!NOTE]
> Azure Çin kullanıyorsanız lütfen `*.azuremicroservices.io` ile değiştirin `*.microservices.azure.cn` , [daha fazla bilgi edinin](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure).

3. Oluşturulan uç noktaya erişim belirteciyle erişin. Erişim belirtecini yetkilendirme sağlamak için bir üstbilgiye koyun.  Yalnızca "GET" yöntemi desteklenir.

    Örneğin, Eureka sistem durumunu görmek için *' https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health '* gibi bir uç noktaya erişin.

    Yanıt *401* ise, rolün başarıyla atanıp atanmadığından emin olun.  Rolün etkili olması veya erişim belirtecinin sona ermediğinden emin olmak birkaç dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure CLı kimlik doğrulaması](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Üretime hazırlık uç noktaları](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Ayrıca bkz.
* [Rol ve izinler oluşturma](how-to-permissions.md)
