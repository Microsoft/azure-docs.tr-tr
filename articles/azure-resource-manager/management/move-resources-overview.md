---
title: Azure kaynaklarını kaynak grupları, abonelikler veya bölgeler arasında taşıyın.
description: Kaynak grupları, abonelikler veya bölgeler arasında taşınabilecek Azure kaynak türlerine genel bakış.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730507"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>Azure kaynaklarını kaynak grupları, abonelikler veya bölgeler arasında taşıma

Azure kaynakları yeni bir kaynak grubuna veya aboneliğe ya da bölgeler arasında taşınabilir.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>Kaynakları kaynak grupları veya abonelikler arasında taşıma

Azure kaynaklarını başka bir Azure aboneliğine ya da aynı abonelik altındaki başka bir kaynak grubuna taşıyabilirsiniz. Azure portalı, Azure PowerShell, Azure CLI veya REST API ile kaynakları taşıyabilirsiniz. Daha fazla bilgi için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](move-resource-group-and-subscription.md).

### <a name="upgrade-a-subscription"></a>Aboneliği yükseltme

Azure aboneliğinizi gerçekten yükseltmek istiyorsanız (örneğin, ücretsiz 'den Kullandıkça Öde 'ye geçiş), aboneliğinizi dönüştürmeniz gerekir.

- Ücretsiz denemeyi yükseltmek için bkz. [ücretsiz deneme sürümünüzü yükseltme veya Azure aboneliğinizi Microsoft Imagine Kullandıkça Öde](../../cost-management-billing/manage/upgrade-azure-subscription.md).
- Kullandıkça Öde hesabını değiştirmek için bkz. [Azure Kullandıkça Öde aboneliğinizi farklı bir teklifine değiştirme](../../cost-management-billing/manage/switch-azure-offer.md).

Aboneliği dönüştüremiyoruz, [bir Azure destek isteği oluşturun](../../azure-portal/supportability/how-to-create-azure-support-request.md). Sorun türü için **Abonelik yönetimi** ' ni seçin.

## <a name="move-resources-across-regions"></a>Kaynakları bölgeler arasında taşıma

Azure coğrafi bölgeler, bölgeler ve kullanılabilirlik alanları, Azure genel altyapısının temelini oluşturur. Azure [geographıes](https://azure.microsoft.com/global-infrastructure/geographies/) genellikle iki veya daha fazla [Azure bölgesi](https://azure.microsoft.com/global-infrastructure/regions/)içerir. Bölge, Kullanılabilirlik Alanları ve birden çok veri merkezi içeren coğrafya içindeki bir alandır.

Belirli Azure bölgesindeki kaynakları dağıttıktan sonra, kaynakları farklı bir bölgeye taşımak isteyebileceğiniz birçok neden vardır.

- **Bir bölge başlatma kısmına hizalayın**: kaynaklarınızı önceden kullanılamayan yeni bir sunulan Azure bölgesine taşıyın.
- **Hizmetler/Özellikler Için hizalayın**: belirli bir bölgede bulunan hizmetlerden veya özelliklerden yararlanmak için kaynakları taşıyın.
- **İş gelişmeleri yanıtlayın**: birleşmeler veya alımlar gibi iş değişikliklerine yanıt olarak kaynakları bir bölgeye taşıyın.
- **Yakınlık Için Hizala**: kaynakları işletmenize yerel bir bölgeye taşıyın.
- **Veri gereksinimlerini karşılayın**: kaynakları veri yerleşimi gereksinimleriyle veya veri sınıflandırması gereksinimleriyle hizalamak için taşıyın. [Daha fazla bilgi edinin](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Dağıtım gereksinimlerine yanıt**: hata halinde dağıtılan kaynakları taşıyın veya kapasite ihtiyaçlarına yanıt olarak taşıyın.
- **Yetki alma Işlemine yanıt**: yetkisi alınmış bölgeler nedeniyle kaynakları taşıyın.

### <a name="move-resources-with-resource-mover"></a>Kaynak taşıyıcısı ile kaynakları taşıma

Kaynakları [Azure Kaynak taşıyıcısı](../../resource-mover/overview.md)ile farklı bir bölgeye taşıyabilirsiniz. Kaynak taşıyıcısı şunları sağlar:

- Kaynakları bölgeler arasında taşımak için tek bir hub.
- Daha az taşıma süresi ve karmaşıklığı. İhtiyacınız olan her şey tek bir konumda.
- Farklı türlerde Azure kaynaklarını taşımak için basit ve tutarlı bir deneyim.
- Taşımak istediğiniz kaynaklar arasında bağımlılıkları belirlemenin kolay bir yolu. Bu kimlik, taşıma işleminden sonra her şeyin hedef bölgede beklenildiği gibi çalışması için ilgili kaynakları birlikte taşımanızı sağlar.
- Taşıma işleminden sonra silmek istiyorsanız kaynak bölgedeki kaynakların otomatik olarak temizlenmesi.
- Edici. Bir taşımayı deneyebilir ve tam bir taşıma yapmak istemiyorsanız bu uygulamayı atabilirsiniz.

Birkaç farklı yöntem kullanarak kaynakları başka bir bölgeye taşıyabilirsiniz:

- **Kaynakları bir kaynak grubundan taşımaya başlayın**: Bu yöntemde, kaynak grubundan geçiş bölgesi 'ni başlatabilirsiniz. Taşımak istediğiniz kaynakları seçtikten sonra işlem, Kaynak bağımlılıklarını denetlemek ve taşıma işlemini düzenlemek için kaynak taşıyıcısı hub 'ında devam eder. [Daha fazla bilgi edinin](../../resource-mover/move-region-within-resource-group.md).
- **Kaynakları doğrudan kaynak taşıyıcısı hub 'ından taşımaya başlayın**: Bu yöntemde, bölge taşıma işlemini doğrudan hub 'da başlatabilirsiniz. [Daha fazla bilgi edinin](../../resource-mover/tutorial-move-region-virtual-machines.md).

## <a name="next-steps"></a>Sonraki adımlar

- Kaynak türünün taşınmakta olup olmadığını denetlemek için bkz. [kaynaklar Için taşıma işlemi desteği](move-support-resources.md).
- Bölge taşıma işlemi hakkında daha fazla bilgi edinmek için [taşıma Işlemi hakkında](../../resource-mover/about-move-process.md)bölümüne bakın.
