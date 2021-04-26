---
title: Uçtan uca aktarım katmanı güvenliğini etkinleştir
titleSuffix: Azure Spring Cloud
description: Bir uygulama için uçtan uca aktarım katmanı güvenliğini etkinleştirme.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227815"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>Bir uygulama için uçtan uca TLS 'yi etkinleştirme

Bu konu başlığı altında, bir giriş denetleyicisinden HTTPS 'yi destekleyen uygulamalara trafiğin güvenliğini sağlamak için uçtan uca SSL/TLS 'nin nasıl etkinleştirileceği gösterilmektedir. Uçtan uca TLS 'yi etkinleştirdikten ve anahtar kasasından bir sertifika yükledikten sonra Azure Spring Cloud içindeki tüm iletişimler TLS ile güvenli hale getirilir.

   ![TLS ile güvenli iletişim grafiği.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Önkoşullar 

- Dağıtılmış bir Azure yay bulutu örneği. Kullanmaya başlamak için [Azure CLI aracılığıyla dağıtmaya yönelik hızlı başlangıç](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) Öğreticimizi izleyin.
- Uçtan uca TLS hakkında bilgi sahibi değilseniz, [uçtan uca TLS örneğine](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl)bakın.
- Gerekli sertifikaları Spring Boot uygulamalarına güvenli bir şekilde yüklemek için [keykasaspring Boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates)' ı kullanabilirsiniz.


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>Mevcut bir uygulamada uçtan uca TLS 'yi etkinleştirme

`az spring-cloud app update --enable-end-to-end-tls`Bir uygulama için uçtan uca TLS 'i etkinleştirmek veya devre dışı bırakmak için komutunu kullanın.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>Özel etki alanı bağladığınızda uçtan uca TLS 'yi etkinleştirme

`az spring-cloud app custom-domain update --enable-end-to-end-tls` `az spring-cloud app custom-domain bind --enable-end-to-end-tls` Bir uygulama için uçtan uca TLS 'i etkinleştirmek veya devre dışı bırakmak için komutunu kullanın.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>Uçtan uca TLS durumunu doğrulama

`az spring-cloud app show`Değerini denetlemek için komutunu kullanın `enableEndToEndTls` .
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Sonraki adımlar
* [Erişim yapılandırması sunucusu ve hizmet kayıt defteri](how-to-access-data-plane-azure-ad-rbac.md)
