---
title: Azure Spring Cloud 'da giden genel IP adreslerini belirleme
description: Veritabanı, depolama, Key Vault vb. gibi dış kaynaklarla iletişim kurmak için statik giden genel IP adreslerini görüntüleme.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878729"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Azure Spring Cloud 'da giden genel IP adreslerini belirleme

Bu sayfada, Azure Spring Cloud uygulamalarının statik giden genel IP adreslerinin nasıl görüntüleneceği açıklanır.  Ortak IP 'Ler, veritabanları, depolama ve Anahtar kasaları gibi dış kaynaklarla iletişim kurmak için kullanılır.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Azure Spring Cloud 'da IP adresleri nasıl çalışır?

Azure Spring Cloud Service bir veya daha fazla giden genel IP adresine sahiptir. Giden genel IP adreslerinin sayısı, katmanlara ve diğer faktörlere göre farklılık gösterebilir. 

Giden genel IP adresleri genellikle sabittir ve aynı kalır, ancak özel durumlar vardır.

## <a name="when-outbound-ips-change"></a>Giden IP 'Ler değiştiğinde

Her Azure yay bulutu örneği, belirli bir zamanda bir dizi giden genel IP adresine sahiptir. Bir arka uç veritabanı gibi uygulamalardan giden tüm bağlantılar, giden genel IP adreslerinden birini kaynak IP adresi olarak kullanır. IP adresi çalışma zamanında rastgele seçilir, bu nedenle arka uç hizmetiniz tüm giden IP adreslerine güvenlik duvarını açması gerekir.

Aşağıdaki eylemlerden birini gerçekleştirdiğinizde giden genel IP sayısı değişir:

- Azure Spring Cloud örneğinizi katmanlar arasında yükseltin.
- İş ihtiyaçları için daha fazla giden genel IP için bir destek bileti yükseltin.

## <a name="find-outbound-ips"></a>Giden IP 'Leri bulma

Azure portal hizmet örneğiniz tarafından şu anda kullanılan giden genel IP adreslerini bulmak için örneğinizin sol gezinti bölmesindeki **ağ iletişimi** ' ne tıklayın. Bunlar **gıden IP adresleri** alanında listelenir.

Cloud Shell, aşağıdaki komutu çalıştırarak aynı bilgileri bulabilirsiniz

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
* [Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinin](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Azure Spring Cloud 'da Anahtar Kasası hakkında daha fazla bilgi edinin](spring-cloud-tutorial-managed-identities-key-vault.md)
