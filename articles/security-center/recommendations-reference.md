---
title: Tüm Azure Güvenlik Merkezi önerileri için başvuru tablosu
description: Bu makalede, kaynaklarınızı korumanıza ve korumanıza yardımcı olan Azure Güvenlik Merkezi 'nin güvenlik önerileri listelenmiştir.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: e994aead1840fd3ef9b57e92cf95e94837608d7a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719142"
---
# <a name="security-recommendations---a-reference-guide"></a>Güvenlik önerileri - başvuru kılavuzu

Bu makalede, Azure Güvenlik Merkezi 'nde görebileceğiniz öneriler listelenir. Ortamınızda gösterilen öneriler, koruduğunuz kaynaklara ve özelleştirilmiş yapılandırmanıza bağlıdır.

Güvenlik Merkezi 'nin önerileri, [Azure Güvenlik kıyaslaması](https://docs.microsoft.com/security/benchmark/azure/introduction)temel alınarak hesaplanır.
Azure Güvenlik kıyaslaması, yaygın uyumluluk çerçevelerine göre güvenlik ve uyum en iyi uygulamaları için Microsoft tarafından yazılan, Azure 'a özgü bir dizi kılavuzlardır. Bu, yaygın olarak kullanılan kıyaslama, [Internet güvenliği (CIS) Için merkezden](https://www.cisecurity.org/benchmark/azure/) ve [ulusal standartlar ve teknolojı Enstitüsü (NIST)](https://www.nist.gov/) ile bulut merkezli güvenliğe odaklanarak yapılar oluşturur.

Bu önerilere yanıt verme hakkında bilgi edinmek için bkz. [Azure Güvenlik Merkezi 'nde önerileri düzeltme](security-center-remediate-recommendations.md).

Güvenli puanınız, tamamladığınız Güvenlik Merkezi önerilerinin sayısını temel alır. İlk olarak hangi önerilerin çözümlenmeyeceğine karar vermek için, her birinin önem derecesine ve güvenli puanınızın olası etkilerine göz atın.

> [!TIP]
> Bir önerinin açıklaması "ilişkili ilke yok" ifadesini içeriyorsa, bu, genellikle Bu önerilerin farklı bir öneriye _ve ilkesine bağlı_ olması nedeniyle oluşur. Örneğin, "Endpoint Protection sistem durumu arızaları düzeltildi..." önerisi, bir uç nokta koruma çözümünün da _yüklü_ olup olmadığını denetleyen öneriye dayanır ("Endpoint Protection çözümü yüklenmelidir..."). Temel _alınan önerinin_ bir ilkesi vardır.
> İlkeleri yalnızca temel öneriyle sınırlamak, ilke yönetimini basitleştirir.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>AppServices önerileri

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>İşlem önerileri

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Kapsayıcı önerileri

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Veri önerileri

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Identityandaccess önerileri

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>IoT önerileri

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Ağ önerileri

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Kullanım dışı öneriler

|Öneri|Açıklama & ilgili ilke|Önem derecesi|
|----|----|----|
|Uygulama Hizmetleri 'ne erişim kısıtlı olmalıdır|Ağ yapılandırmasını değiştirerek, çok geniş aralıklardan gelen trafiği reddedecek şekilde uygulama hizmetlerinize erişimi kısıtlayın.<br>(İlgili ilke: [Önizleme]: uygulama hizmetlerine erişim kısıtlı olmalıdır)|Yüksek|
|IaaS NSG 'lerdeki Web uygulamalarına yönelik kurallar sağlamlaştırılmış olmalıdır|Web uygulamaları çalıştıran sanal makinelerinizin ağ güvenlik grubu (NSG), Web uygulaması bağlantı noktalarıyla ilgili aşırı izin veren NSG kuralları ile.<br>(İlgili ilke: IaaS üzerinde Web uygulamalarına yönelik NSG kuralları sağlamlaştırılmış olmalıdır)|Yüksek|
|Gereksiz uygulama ayrıcalıklarını kaldırarak saldırı vektörünü azaltmak için pod güvenlik Ilkeleri tanımlanmalıdır (Önizleme)|Gereksiz uygulama ayrıcalıklarını kaldırarak saldırı vektörünü azaltmak için pod güvenlik Ilkeleri tanımlayın. Yalnızca erişim izni verilen kaynaklara erişebilmeleri için pod güvenlik ilkelerinin yapılandırılması önerilir.<br>(İlgili ilke: [Önizleme]: Pod güvenlik Ilkeleri Kubernetes hizmetlerinde tanımlanmalıdır)|Orta|
|IoT cihazlarınıza daha fazla görünürlük sağlamak için IoT güvenlik modülü için Azure Güvenlik Merkezi 'ni yükler|IoT cihazlarınıza daha fazla görünürlük sağlamak için IoT güvenlik modülü için Azure Güvenlik Merkezi 'ni yükler.|Düşük|
|Sistem güncelleştirmelerinin uygulanabilmesi için makineleriniz yeniden başlatılmalıdır|Sistem güncelleştirmelerini uygulamak ve makinenin güvenlik açıklarına karşı güvenliğini sağlamak için makinelerinizi yeniden başlatın. (İlgili ilke: sistem güncelleştirmelerinin makinelerinizde yüklü olması gerekir)|Orta|
|İzleme Aracısı makinelerinizde yüklü olmalıdır|Bu eylem, seçili sanal makinelere bir izleme Aracısı kurar. Aracının raporlamak için bir çalışma alanı seçin. (İlgili ilke yok)|Yüksek|
||||

## <a name="next-steps"></a>Sonraki adımlar

Öneriler hakkında daha fazla bilgi edinmek için aşağıdakilere bakın:

- [Güvenlik ilkeleri, girişimler ve öneriler nelerdir?](security-policy-concept.md)
- [Güvenlik önerilerinizi gözden geçirin](security-center-recommendations.md)
