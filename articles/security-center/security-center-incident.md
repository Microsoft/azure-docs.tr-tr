---
title: Azure Güvenlik Merkezi 'nde güvenlik olaylarını yönetme | Microsoft Docs
description: Bu belge, güvenlik olaylarını yönetmek için Azure Güvenlik Merkezi 'ni kullanmanıza yardımcı olur.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 722a508679c74f9d62df07575ffa1006528f4398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652115"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde güvenlik olaylarını yönetme

Güvenlik uyarılarını önceliklendirme ve araştırmak, en nitelikli Güvenlik analistlerinin bile zaman alıcı olabilir. Çoğu için nereden başlayabileceğinizi bilirsiniz. 

Güvenlik Merkezi, farklı [güvenlik uyarıları](security-center-managing-and-responding-alerts.md)arasındaki bilgileri bağlamak için [Analytics](./security-center-alerts-overview.md) 'i kullanır. Güvenlik Merkezi bu bağlantıları kullanarak, saldırganın eylemlerini ve etkilenen kaynakları anlamanıza yardımcı olmak için bir saldırı kampanyasının ve ilgili uyarıların tek bir görünümünü sağlayabilir.

Bu sayfa güvenlik merkezi 'ndeki olaylara genel bir bakış sağlar.

## <a name="what-is-a-security-incident"></a>Güvenlik olayı nedir?

Güvenlik Merkezi'nde bir güvenlik olayı, bir kaynağın [sonlandırma zinciri](alerts-reference.md#intentions) desenleri ile hizalanan tüm uyarılarının toplamıdır. Olaylar [güvenlik uyarıları](security-center-managing-and-responding-alerts.md) sayfasında görünür. İlgili uyarıları görüntülemek ve daha fazla bilgi almak için bir olay seçin.

## <a name="managing-security-incidents"></a>Güvenlik olaylarını yönetme

1. Güvenlik Merkezi 'nin Uyarılar sayfasında, uyarı adına göre filtrelemek için **Filtre Ekle** düğmesini kullanarak **birden çok kaynak üzerinde algılanan uyarı adı güvenlik olayına** göre filtreleme yapın. 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="Azure Güvenlik Merkezi 'ndeki Uyarılar sayfasında olayları bulma":::

    Liste artık yalnızca olayları gösterecek şekilde filtrelenmiştir. Güvenlik olaylarının güvenlik uyarılarına farklı bir simgesi olduğuna dikkat edin.

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Azure Güvenlik Merkezi 'ndeki Uyarılar sayfasında olayların listesi":::

1. Bir olayın ayrıntılarını görüntülemek için listeden birini seçin. Olay hakkında daha fazla ayrıntı içeren bir kenar bölmesi görüntülenir.

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="Olayın ayrıntılarını gösteren yan bölme":::

1. Daha fazla ayrıntı görüntülemek için **tüm ayrıntıları görüntüle**' yi seçin.

    [![Azure Güvenlik Merkezi 'nde güvenlik olaylarına yanıt verme](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    Güvenlik olayı sayfasının sol bölmesi, güvenlik olayı: Başlık, önem derecesi, durum, etkinlik süresi, açıklama ve etkilenen kaynak hakkında üst düzey bilgileri gösterir. Etkilenen kaynağın yanında ilgili Azure etiketlerini görebilirsiniz. Uyarıyı araştırırken kaynağın kuruluş bağlamını anlamak için bu etiketleri kullanın.

    Sağ bölme, bu olayın bir parçası olarak bağıntılı güvenlik uyarılarını içeren **Uyarılar** sekmesini içerir. 

    >[!TIP]
    > Belirli bir uyarı hakkında daha fazla bilgi için bunu seçin. 

    [![Olayın eylem al sekmesi](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    **Eylem al** sekmesine geçiş yapmak için sağ bölmenin altındaki sekmeyi veya düğmeyi seçin. Aşağıdaki gibi eylemler gerçekleştirmek için bu sekmeyi kullanın:
    - *Tehdidi azaltma* -bu güvenlik olayı için el ile düzeltme adımları sağlar
    - *Gelecekteki saldırıları önleme* -saldırı yüzeyini azaltmaya, güvenlik duruşunu artırmaya ve gelecekteki saldırıları önlemeye yardımcı olmak için güvenlik önerileri sağlar
    - *Otomatik yanıtı Tetikle* -bu güvenlik olayına yanıt olarak bir mantıksal uygulamayı tetikleme seçeneği sağlar
    - *Benzer Uyarıları Gizle* -uyarı kuruluşunuza uygun değilse, benzer özelliklerle gelecekteki uyarıları gösterme seçeneği sağlar 

   > [!NOTE]
   > Aynı uyarı, bir olayın parçası olarak ve tek başına bir uyarı olarak görülebilmesi için de bulunabilir.

1. Olaydaki tehditleri düzeltmek için, her uyarıyla birlikte sunulan düzeltme adımlarını izleyin.


## <a name="next-steps"></a>Sonraki adımlar

Bu sayfa güvenlik merkezi 'nin güvenlik olayı yeteneklerini açıklandı. İlgili bilgiler için, aşağıdaki sayfalara bakın:

- [Güvenlik Merkezi 'nde güvenlik uyarıları](security-center-alerts-overview.md)
- [Güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md)