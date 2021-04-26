---
title: Notification Hubs güvenlik modeli
description: Azure Notification Hubs için güvenlik modeli hakkında bilgi edinin.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 07600b1fe0cb7420989fbbfbe55c2f1a4197d2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548259"
---
# <a name="notification-hubs-security"></a>Notification Hubs güvenliği

## <a name="overview"></a>Genel Bakış

Bu konuda Azure Notification Hubs 'in güvenlik modeli açıklanmaktadır.

## <a name="shared-access-signature-security"></a>Paylaşılan erişim Imzası güvenliği

Notification Hubs, *paylaşılan erişim imzası* (SAS) adlı bir varlık düzeyinde güvenlik şeması uygular. Her kural, [güvenlik taleplerinden](#security-claims)daha sonra açıklandığı gibi bir ad, anahtar değer (paylaşılan gizlilik) ve bir haklar kümesi içerir. 

Bir hub oluştururken, otomatik olarak iki kural oluşturulur: bir **dinleme** haklarıyla (istemci uygulamanın kullandığı) ve biri **Tüm** haklara sahip (uygulamanın arka ucu tarafından kullanılır):

- **Defaultlistensharedaccesssignature**: yalnızca **dinleme** izni verir.
- **Defaultfullsharedaccesssignature**: **dinleme**, **yönetme** ve **gönderme** izinleri verir. Bu ilke yalnızca uygulamanızın arka ucunda kullanılacaktır. Bunu istemci uygulamalarında kullanmayın; yalnızca **dinleme** erişimiyle bir ilke kullanın. Yeni bir SAS belirteciyle yeni bir özel erişim ilkesi oluşturmak için, bu makalede daha sonra [erişim ilkeleri Için SAS belirteçleri](#sas-tokens-for-access-policies) bölümüne bakın.

İstemci uygulamalarından kayıt yönetimi gerçekleştirirken, bildirimler aracılığıyla gönderilen bilgiler hassas değilse (örneğin, hava durumu güncelleştirmeleri), bir Bildirim Hub 'ına erişmenin yaygın bir yolu, kuralın anahtar değerini istemci uygulamasına yalnızca dinlemesi ve kuralın anahtar değerini uygulama arka ucuna tam erişim olarak vermektir.

Uygulamalar anahtar değerini Windows Mağazası istemci uygulamalarına katıştırmamalıdır; Bunun yerine, istemci uygulamasını başlangıçta uygulama arka ucuna al.

**Dinleme** erişimi olan anahtar, bir istemci uygulamanın herhangi bir etikete kaydolmaya izin verir. Uygulamanızın kayıtları belirli istemcilerle kısıtlanması gerekiyorsa (örneğin, Etiketler Kullanıcı kimliklerini temsil ediyorsa), uygulamanızın arka ucunuzun kayıtları gerçekleştirmesi gerekir. Daha fazla bilgi için bkz. [kayıt yönetimi](notification-hubs-push-notification-registration-management.md). Bu şekilde, istemci uygulamanın Notification Hubs doğrudan erişimine sahip olamayacağını unutmayın.

## <a name="security-claims"></a>Güvenlik talepleri

Diğer varlıklara benzer şekilde, Bildirim Hub 'ı işlemlerine üç güvenlik talebi için izin verilir: **dinle**, **Gönder** ve **Yönet**.

| İste   | Description                                          | İzin verilen işlemler |
| ------- | ---------------------------------------------------- | ------------------ |
| Dinle  | Tek kayıt oluşturma/güncelleştirme, okuma ve silme | Kayıt oluştur/güncelleştir<br><br>Kaydı oku<br><br>Bir tanıtıcı için tüm kayıtları okuma<br><br>Kaydı Sil |
| Gönder    | Bildirim Hub 'ına ileti gönderme                | İleti gönder |
| Yönetme  | Notification Hubs CRUDs (PNS kimlik bilgilerini güncelleştirme ve güvenlik anahtarları dahil) ve etiketlere göre kayıtları okuma |Hub oluşturma/güncelleştirme/okuma/silme<br><br>Kayıtları etikete göre oku |

Notification Hubs, doğrudan Hub üzerinde yapılandırılmış paylaşılan anahtarlarla oluşturulan SAS belirteçlerini kabul eder.

Birden fazla ad alanına bildirim göndermek mümkün değildir. Ad alanları Notification Hubs için mantıksal kapsayıcılardır ve bildirim göndermeye dahil değildir.

Ad alanı düzeyindeki işlemler için ad alanı düzeyinde erişim ilkelerini (kimlik bilgileri) kullanın; Örneğin: hub 'ları listeleme, hub oluşturma veya silme, vb. Yalnızca hub düzeyi erişim ilkeleri size bildirim göndermenizi sağlar.

### <a name="sas-tokens-for-access-policies"></a>Erişim ilkeleri için SAS belirteçleri

Yeni bir güvenlik talebi oluşturmak veya mevcut SAS anahtarlarını görüntülemek için aşağıdakileri yapın:

1. Azure portalında oturum açın.
2. **Tüm kaynaklar**’ı seçin.
3. Talep oluşturmak istediğiniz Bildirim Hub 'ının adını seçin veya SAS anahtarını görüntüleyin.
4. Sol taraftaki menüde **erişim ilkeleri**' ni seçin.
5. Yeni bir güvenlik talebi oluşturmak için **Yeni ilke** ' yi seçin. İlkeye bir ad verin ve vermek istediğiniz izinleri seçin. Ardından **Tamam**’ı seçin.
6. Tam bağlantı dizesi (yeni SAS anahtarı dahil), erişim Ilkeleri penceresinde görüntülenir. Daha sonra kullanmak üzere bu dizeyi panoya kopyalayabilirsiniz.

SAS anahtarını belirli bir ilkeden ayıklamak için, istediğiniz SAS anahtarını içeren ilkenin yanındaki **Kopyala** düğmesini seçin. Bu değeri geçici bir konuma yapıştırın, sonra bağlantı dizesinin SAS anahtar kısmını kopyalayın. Bu örnek, **mytestnamespace1** adlı bir Notification Hubs ad alanı ve **policy2** adlı bir ilke kullanır. SAS anahtarı, bir dizenin sonundaki, **Sharedaccesskey** tarafından belirtilen değerdir:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![SAS anahtarları al](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Notification Hubs genel bakış](notification-hubs-push-notification-overview.md)
