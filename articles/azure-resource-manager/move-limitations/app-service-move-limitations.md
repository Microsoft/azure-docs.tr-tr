---
title: Azure App Service kaynaklarını taşıma
description: App Service kaynaklarını yeni bir kaynak grubuna veya aboneliğe taşımak için Azure Resource Manager kullanın.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: dca9b3a8f328192683cfde586f0ccdb01e84dc16
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150906"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service kaynakları için kılavuzu taşıma

App Service kaynaklarını taşıma adımları, kaynakları bir abonelik içinde veya yeni bir aboneliğe taşısanız göre farklılık gösterir.

## <a name="move-in-same-subscription"></a>Aynı abonelikte taşı

Bir Web uygulamasını _aynı abonelik içinde_taşırken, üçüncü taraf SSL sertifikalarını taşıyamazsınız. Ancak, üçüncü taraf sertifikasını taşımadan bir Web uygulamasını yeni kaynak grubuna taşıyabilirsiniz ve uygulamanızın SSL işlevselliği hala işe yarar.

SSL sertifikası ile Web uygulaması taşımak istiyorsanız, şu adımları izleyin:

1. Web uygulamasından üçüncü taraf sertifikayı silin, ancak sertifikanızın bir kopyasını saklayın
2. Web uygulaması taşıyın.
3. Taşınan Web uygulamasına üçüncü taraf sertifikayı yükleyin.

## <a name="move-across-subscriptions"></a>Abonelikler arasında geçiş

Bir Web uygulaması taşınırken _abonelikler arasında_, aşağıdaki sınırlamalar geçerlidir:

- Hedef kaynak grubu mevcut App Service kaynaklar olmaması gerekir. App Service kaynakları şunlardır:
    - Web Apps
    - App Service planları
    - Karşıya yüklenen veya içeri aktarılan SSL sertifikaları
    - App Service ortamları
- Kaynak grubundaki tüm App Service kaynakların birlikte taşınması gerekir.
- App Service kaynaklarını, bunlar ilk olarak oluşturulduğu kaynak grubunun yalnızca taşınabilir. Bir App Service kaynağı artık özgün kaynak grubunda yoksa, özgün kaynak grubuna geri taşıyın. Ardından, kaynağı abonelikler arasında taşıyın.

Özgün kaynak grubunu anımsamıyorsanız tanılama aracılığıyla bulabilirsiniz. Web uygulamanız için **Tanılama ve sorunları çözme**' ı seçin. Ardından **yapılandırma ve yönetim**' i seçin.

![Tanılamayı seçin](./media/app-service-move-limitations/select-diagnostics.png)

**Geçiş seçeneklerini**belirleyin.

![Geçiş seçeneklerini belirleyin](./media/app-service-move-limitations/select-migration.png)

Web uygulamasını taşımak için önerilen adımlar seçeneğini belirleyin.

![Önerilen adımları seçin](./media/app-service-move-limitations/recommended-steps.png)

Kaynakları taşımadan önce gerçekleştirilecek önerilen eylemleri görürsünüz. Bilgiler, Web uygulaması için özgün kaynak grubunu içerir.

![Öneriler](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>App Service Sertifikası taşı

App Service Sertifikası yeni bir kaynak grubuna veya aboneliğe taşıyabilirsiniz. App Service Sertifikası bir Web uygulamasına bağlıysa, kaynakları yeni bir aboneliğe taşımadan önce bazı adımları uygulamanız gerekir. Kaynakları taşımadan önce Web uygulamasındaki SSL bağlamasını ve özel sertifikayı silin. App Service Sertifikası silinmesi gerekmez, yalnızca Web uygulamasındaki özel sertifika.

## <a name="move-support"></a>Taşıma desteği

Hangi App Service kaynaklarının taşınabileceğini öğrenmek için bkz. destek durumunu taşıma:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../resource-group-move-resources.md).
