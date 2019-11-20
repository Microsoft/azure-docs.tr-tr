---
title: Güvenliği geliştirmek için Azure Güvenlik Merkezi önerilerini kullanma | Microsoft Docs
description: " Güvenlik saldırısını azaltmaya yardımcı olması için Azure Güvenlik Merkezi 'nde güvenlik ilkeleri ve önerilerini nasıl kullanacağınızı öğrenin. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: 69fa9d24d24c26a7d945c27e86739475f9883b27
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200688"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Güvenliği geliştirmek için Azure Güvenlik Merkezi önerilerini kullanın
Bir güvenlik ilkesi yapılandırarak ve ardından Azure Güvenlik Merkezi tarafından sunulan önerileri uygulayarak önemli bir güvenlik olayı olasılığını azaltabilirsiniz. Bu makalede bir güvenlik saldırısını azaltmaya yardımcı olmak için Güvenlik Merkezi 'nde güvenlik ilkelerinin ve önerilerin nasıl kullanılacağı gösterilmektedir. 

Güvenlik Merkezi, Azure kaynaklarınızın güvenlik durumunu çözümlemek için sürekli taramaları otomatik olarak çalıştırır. Güvenlik Merkezi olası güvenlik açıklarını belirlediğinde, gerekli güvenlik denetimlerini yapılandırma sürecinde size kılavuzluk eden öneriler oluşturur. Güvenlik Merkezi, önerilerini 24 saat içinde, aşağıdaki özel durumlarla güncelleştirir:

- İşletim sistemi güvenlik yapılandırması önerileri 48 saat içinde güncelleştirilir
- Endpoint Protection sorunları önerileri 8 saat içinde güncelleştirilir

## <a name="scenario"></a>Senaryo
Bu senaryo, güvenlik merkezi önerilerini izleyerek ve işlem yaparak bir güvenlik olayının olasılığını azaltmaya yardımcı olmak için Güvenlik Merkezi 'ni nasıl kullanacağınızı gösterir. Senaryo, Güvenlik Merkezi [planlama ve işlemler kılavuzunda](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)sunulan kurgusal Şirket, contoso ve rolleri kullanır. Bu senaryoda, aşağıdaki kişilerin rollerine odaklanıyoruz:

![Senaryo rolleri](./media/security-center-using-recommendations/scenario-roles.png)

Contoso yakın zamanda şirket içi kaynaklarından bazılarını Azure 'a geçirmiştir. Contoso, kaynaklarını korumak ve buluttaki kaynaklarından gelen güvenlik açıklarını azaltmak istiyor.

## <a name="use-azure-security-center"></a>Azure Güvenlik Merkezi’ni kullanma
Contoso BT güvenliği 'nin sunduğu David, güvenlik açıklarını engellemek ve algılamak için Contoso 'nun Azure Güvenlik Merkezi aboneliklerine Güvenlik Merkezi 'ni eklemek üzere zaten seçilmiş. 

Güvenlik Merkezi, contoso 'nun Azure kaynaklarının güvenlik durumunu otomatik olarak analiz eder ve varsayılan güvenlik ilkelerini uygular. Güvenlik Merkezi olası güvenlik açıklarını belirlediğinde, güvenlik ilkesinde ayarlanan denetimlere göre **öneriler** oluşturur. 

David, sunulan tüm öneriler ve güvenlik özellikleri paketini almak için tüm aboneliklerinde Azure Güvenlik standardı katmanını çalıştırır. Jeff Ayrıca, [Windows](quick-onboard-windows-computer.md) ve [Linux](quick-onboard-linux-computer.md) sunucularında Güvenlik Merkezi 'nin karma desteğinden yararlanmasını sağlamak için henüz buluta geçirmemiş olan mevcut şirket içi sunucuların tümünü de onpanolar.

Jeff, bir bulut iş yükü sahibidir. Jeff, contoso güvenlik ilkelerine uygun olarak güvenlik denetimlerini uygulamaktan sorumludur. 

Jeff aşağıdaki görevleri gerçekleştirir:

- Güvenlik Merkezi tarafından sunulan güvenlik önerilerini izleme
- Güvenlik önerilerini değerlendirin ve önerileri uygulayıp uygulamamaları veya kapatmak için karar verin.
- Güvenlik önerilerini Uygula

### <a name="remediate-threats-using-recommendations"></a>Önerileri kullanarak tehditleri düzeltin
Günlük izleme etkinliklerinin bir parçası olarak, Jeff Azure 'da oturum açar ve Güvenlik Merkezi 'ni açar. 

1. Jeff, iş yükünün aboneliklerini seçer.

2. Jeff, aboneliklerin ne kadar güvenli olduğunu gösteren genel bir bakış elde etmek için **güvenli puanı** denetler ve puanın 548 olduğunu görür.

3. Jeff, ilk olarak hangi önerilerin işleneceğini belirlemek için gerekir. Jeff, güvenli puan ' i tıklatır ve [güvenli puan etkisini](security-center-secure-score.md)ne kadar geliştirdiğine göre önerileri işlemeye başlar.

4. Jeff, çok sayıda bağlı VM ve sunucu içerdiğinden, Jeff **işlem ve uygulamalara**odaklanmaya karar verir.

5. Jeff, **işlem ve uygulamaları**tıklattığında, önerilerin bir listesini görür ve bunları güvenli Puanlama etkisine göre işler.

6. Jeff, Internet 'e yönelik çok sayıda sanal makineye sahiptir ve bağlantı noktaları kullanıma sunulduğundan, bir saldırganın sunucular üzerinde denetim kazanmasını sağlayacağından endişelenirler. Bu nedenle Jeff (**tam ZAMANıNDA VM erişimi**) [Security-Center-Just-in-time.MD] kullanmayı seçer.

Jeff, yüksek öncelikli ve orta öncelikli önerilere ilerlemeye devam eder ve uygulama üzerinde kararlar verir. Her öneri için, Jeff, Güvenlik Merkezi tarafından hangi kaynakların etkilendiğini, ne kadar güvenli puanı etkilediğini, her bir önerinin ne anlama geldiğini, her bir sorunun nasıl azaltılacağını gösteren düzeltme adımlarını anlamak için sunulan ayrıntılı bilgilere bakar.

## <a name="conclusion"></a>Sonuç
Güvenlik Merkezi 'ndeki izleme önerileri, bir saldırı gerçekleşmeden önce güvenlik açıklarını ortadan kaldırmanıza yardımcı olur. Önerileri düzelttireceğiniz zaman, güvenli puanınızın ve iş yüklerinizin güvenlik sonrası güvenliğini iyileştirmenize neden olacak. Güvenlik Merkezi, dağıttığınız yeni kaynakları otomatik olarak bulur, güvenlik ilkenize göre değerlendirir ve bunların güvenliğini sağlamak için yeni öneriler sağlar.


## <a name="next-steps"></a>Sonraki adımlar
Kaynaklarınızın zaman içinde güvende tutulmasına emin olmak için Güvenlik Merkezi 'ndeki önerileri düzenli olarak kontrol ettiğiniz bir izleme sürecinizde olduğundan emin olun.

Bu senaryo, güvenlik saldırısını azaltmaya yardımcı olmak için Güvenlik Merkezi 'nde güvenlik ilkelerini ve önerilerini nasıl kullanacağınızı gösterdi.

[Güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md)ile tehditleri nasıl yanıtlayacağınızı öğrenin.
