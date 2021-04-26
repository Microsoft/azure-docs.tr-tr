---
title: Azure App Service kaynaklarını taşıma
description: App Service kaynaklarını yeni bir kaynak grubuna veya aboneliğe taşımak için Azure Resource Manager kullanın.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531381"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service kaynakları için kılavuzu taşıma

Bu makalede App Service kaynaklarını taşıma adımları açıklanır. App Service kaynaklarını yeni bir aboneliğe taşımak için özel gereksinimler vardır.

## <a name="move-across-subscriptions"></a>Abonelikler arasında geçiş

Bir Web uygulamasını abonelikler arasında taşırken aşağıdaki kılavuz geçerli olur:

- Hedef kaynak grubu, mevcut App Service kaynak içermemelidir. App Service kaynaklar şunlardır:
    - Web Apps
    - App Service planları
    - TLS/SSL sertifikaları karşıya yüklendi veya içeri aktarıldı
    - App Service ortamları
- Kaynak grubundaki tüm App Service kaynakları birlikte taşınmalıdır.
- App Service ortamlar yeni bir kaynak grubuna veya aboneliğe taşınamaz. Ancak, bir Web uygulamasını ve App Service planını App Service Ortamı taşımadan yeni bir aboneliğe taşıyabilirsiniz. Taşıma işleminden sonra, Web uygulaması artık App Service Ortamı barındırılmaz.
- Sertifika, kaynak grubundaki diğer tüm kaynaklarla taşındığı sürece, TLS bağlamalarını silmeden bir sertifikayı bir Web 'e taşıyabilirsiniz.
- App Service kaynaklar yalnızca, başlangıçta oluşturulduğu kaynak grubundan taşınabilir. Bir App Service kaynağı artık özgün kaynak grubunda yoksa, özgün kaynak grubuna geri taşıyın. Ardından, kaynağı abonelikler arasında taşıyın.

Özgün kaynak grubunu anımsamıyorsanız tanılama aracılığıyla bulabilirsiniz. Web uygulamanız için **Tanılama ve sorunları çözme**' ı seçin. Ardından **yapılandırma ve yönetim**' i seçin.

![Tanılamayı seçin](./media/app-service-move-limitations/select-diagnostics.png)

**Geçiş seçeneklerini** belirleyin.

![Geçiş seçeneklerini belirleyin](./media/app-service-move-limitations/select-migration.png)

Web uygulamasını taşımak için önerilen adımlar seçeneğini belirleyin.

![Önerilen adımları seçin](./media/app-service-move-limitations/recommended-steps.png)

Kaynakları taşımadan önce gerçekleştirilecek önerilen eylemleri görürsünüz. Bilgiler, Web uygulaması için özgün kaynak grubunu içerir.

![Ekran yakalama, Microsoft dot Web kaynaklarını taşımak için önerilen adımları gösterir.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Taşıma desteği

Hangi App Service kaynaklarının taşınabileceğini öğrenmek için bkz. destek durumunu taşıma:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../move-resource-group-and-subscription.md).
