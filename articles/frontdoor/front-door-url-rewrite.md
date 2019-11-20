---
title: Azure ön kapı hizmeti-URL yeniden yazma | Microsoft Docs
description: Bu makale, yapılandırılmışsa Azure ön kapı hizmeti 'nin yollarınız için URL yeniden yazma şeklini anlamanıza yardımcı olur.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: dc2126276e3e8e0d35ce8ed1f835544386659eff
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "60736199"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL yeniden yazma (özel iletme yolu)
Azure ön kapı hizmeti, arka uca iletme isteği oluştururken kullanılacak isteğe bağlı bir **özel Iletme yolu** yapılandırmanıza ızın vererek URL yeniden yazmayı destekler. Özel yönlendirme yolu sağlanmazsa Front Door varsayılan olarak yönlendirilen istekte kullanılan URL alanına gelen URL yolunu kopyalar. Yönlendirilen istekte kullanılan Konak üst bilgisi seçilen arka uç için yapılandırılmış olan değerdir. Ne yaptığını ve nasıl yapılandırabileceğinizi öğrenmek için [arka uç ana bilgisayar üst bilgisini](front-door-backend-pool.md#hostheader) okuyun.

URL yeniden yazma işleminin özel iletme yolunu kullanarak güçlü bir bölümü, gelen yolun, iletilen yola bir joker karakterle eşleşen herhangi bir bölümünü kopyalayacaktır (Bu yol kesimleri aşağıdaki örnekteki **yeşil** kesimlerdir):
</br>
![Azure ön kapı URL 'SI yeniden yazma][1]

## <a name="url-rewrite-example"></a>URL yeniden yazma örneği
Aşağıdaki ön uç konakları ve yapılandırılmış yolların bulunduğu bir yönlendirme kuralı düşünün:

| Konaklar      | Yollar       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | foo\*     |
|            | /ç/Bar/\* |

Aşağıdaki tablonun ilk sütununda gelen istek örnekleri gösterilmektedir ve ikinci sütun "en özel" eşleşen yol ' Path ' olarak gösterilir.  Tablonun ilk satırının üçüncü ve sonraki sütunları, yapılandırılmış **özel Iletme yolları**örneğidir ve bu sütunlarda, iletilen istek yolunun, bu durumda gelen istekle eşleştirildiği durum örnekleri temsil eder. sırada.

Örneğin, ikinci satırda okuduk, gelen istek `www.contoso.com/sub`için özel iletme `/`yolu ise, iletilen yolun `/sub`ne olduğunu söyledi. Özel iletme yolu `/fwd/` `/fwd/sub`olsaydı, iletilen yol olur. Daha sonra, kalan sütunlar için. Aşağıdaki yolların **vurgulanmış** kısımları, joker karakter eşleşmesi kapsamında olan bölümleri temsil eder.


| Gelen istek       | En özel eşleşme yolu | /          | /FWD/          | foo          | /ç/Bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /FWD/          | foo          | /ç/Bar/          |
| www\.contoso.com/**Sub**     | /\*                      | /**alt**   | /FWD/**Sub**   | /alt/**alt**   | /alt/bar/**alt**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /FWD/**a/b/c** | /k/**a/b/c** | /ç/Bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /FWD/          | foo          | /ç/Bar/          |
| www\.contoso.com/foo/        | foo\*                  | /          | /FWD/          | foo          | /ç/Bar/          |
| www\.contoso.com/foo/**çubuğu** | foo\*                  | /**çubuktaki**   | /FWD/**Bar**   | /Altbilgi/**çubuk**   | /ç/Bar/**çubuk**   |


## <a name="optional-settings"></a>İsteğe bağlı ayarlar
Belirli bir yönlendirme kuralı ayarları için de belirtebileceğiniz ek isteğe bağlı ayarlar vardır:

* **Önbellek yapılandırması** -devre dışı bırakılırsa veya belirtilmemişse, bu yönlendirme kuralıyla eşleşen istekler önbelleğe alınmış içeriği kullanmaya çalışmaz ve bunun yerine her zaman arka uca alınır. [Ön kapılı önbelleğe alma](front-door-caching.md)hakkında daha fazla bilgi edinin.



## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg