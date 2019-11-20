---
title: Azure cdn'de gerçek zamanlı İstatistikler | Microsoft Docs
description: Gerçek zamanlı istatistikler, istemcilerinize içerik sunarken performansı Azure CDN ile ilgili gerçek zamanlı veriler sağlar.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d56007e5a196a0857f3b69ac51f5e3b5a88c4f6e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593500"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Microsoft Azure cdn'de gerçek zamanlı İstatistikler
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Genel Bakış
Bu belgede, Microsoft Azure cdn'de gerçek zamanlı İstatistikler açıklanmaktadır.  Bu işlev, istemcilerinize içerik sunarken bant genişliği, önbellek durumları ve CDN profiliniz için eş zamanlı bağlantılar gibi gerçek zamanlı veriler sağlar. Bu, hizmetin sistem durumunu go-live olayları dahil olmak üzere, herhangi bir zamanda sürekli izleme sağlar.

Aşağıdaki grafiklerde kullanılabilir:

* [Bant genişliği](#bandwidth)
* [Durum kodları](#status-codes)
* [Önbellek durumları](#cache-statuses)
* [Bağlantılar](#connections)

## <a name="accessing-real-time-stats"></a>Gerçek zamanlı İstatistikler erişme
1. İçinde [Azure portalı](https://portal.azure.com), CDN profilinize gidin.
   
    ![CDN profili dikey penceresi](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. CDN profili dikey penceresinden tıklayın **Yönet** düğmesi.
   
    ![CDN profili dikey penceresi Yönet düğmesi](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    CDN yönetim portalına açılır.
3. Üzerine **Analytics** sekmesine ve ardından üzerine **gerçek zamanlı istatistikleri** açılır öğesi.  Tıklayarak **HTTP büyük nesne**.
   
    ![CDN yönetim portalına](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Gerçek zamanlı İstatistikler grafik görüntülenir.

Grafiklerin her sayfa yüklendiğinde başlatılıyor, seçili zaman aralığı için gerçek zamanlı istatistikler görüntüler.  Grafik, her birkaç saniyede otomatik olarak güncelleştirilir.  **Grafı Yenile** , varsa, Temizle düğmesi sonra onu yalnızca görüntüler seçili veri grafiği.

## <a name="bandwidth"></a>Bant genişliği
![Bant genişliği grafiği](./media/cdn-real-time-stats/cdn-bandwidth.png)

**Bant genişliği** grafik, seçilen zaman aralığı geçerli platform için kullanılan bant genişliği miktarını görüntüler. Gölgeli grafiğin bant genişliği kullanımını gösterir. Tam şu anda kullanılan bant genişliği miktarı, doğrudan aşağıdaki çizgi grafikte görüntülenir.

## <a name="status-codes"></a>Durum kodları
![Durum kodu grafiği](./media/cdn-real-time-stats/cdn-status-codes.png)

**Durum kodları** graf belirli HTTP yanıt kodları seçili zaman aralığı ne sıklıkla gerçekleştiğini gösterir.

> [!TIP]
> Her HTTP durum kodu seçeneği açıklaması için bkz: [Azure CDN HTTP durum kodları](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

HTTP durum kodlarının listesi doğrudan grafiğin görüntülenir. Bu liste, çizgi grafik ve bu durum kodu için saniyede oluşum geçerli sayısını dahil her durum kodu gösterir. Varsayılan olarak, bu durum kodları grafikteki her bir satır görüntülenir. Ancak, yalnızca CDN yapılandırmanızı özel öneme sahip durum kodları izlemek seçebilirsiniz. Bunu yapmak için istenen durum kodları denetleyin ve diğer tüm seçenekleri temizleyin ve ardından tıklayın **Grafı Yenile**. 

Bir özel durum kodunu günlüğe kaydedilen verileri geçici olarak gizleyebilirsiniz.  Grafiğin altındaki doğrudan göstergeden gizlemek istediğiniz durum kodu'a tıklayın. Durum kodu hemen grafikten gizlenir. Bu durum kodu tekrar tıklayarak yeniden görüntülenmesi için bu seçeneği neden olur.

## <a name="cache-statuses"></a>Önbellek durumları
![Önbellek durumları grafiği](./media/cdn-real-time-stats/cdn-cache-status.png)

**Önbellek durumları** graf önbellek durumları belirli türlerdeki seçili zaman aralığı ne sıklıkla gerçekleştiğini gösterir. 

> [!TIP]
> Her önbellek durumu kodu seçeneği açıklaması için bkz: [Azure CDN önbelleğe durum kodları](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Önbellek durum kodlarının listesi doğrudan grafiğin görüntülenir. Bu liste, çizgi grafik ve bu durum kodu için saniyede oluşum geçerli sayısını dahil her durum kodu gösterir. Varsayılan olarak, bu durum kodları grafikteki her bir satır görüntülenir. Ancak, yalnızca CDN yapılandırmanızı özel öneme sahip durum kodları izlemek seçebilirsiniz. Bunu yapmak için istenen durum kodları denetleyin ve diğer tüm seçenekleri temizleyin ve ardından tıklayın **Grafı Yenile**. 

Bir özel durum kodunu günlüğe kaydedilen verileri geçici olarak gizleyebilirsiniz.  Grafiğin altındaki doğrudan göstergeden gizlemek istediğiniz durum kodu'a tıklayın. Durum kodu hemen grafikten gizlenir. Bu durum kodu tekrar tıklayarak yeniden görüntülenmesi için bu seçeneği neden olur.

## <a name="connections"></a>Bağlantılar
![Bağlantıları grafiği](./media/cdn-real-time-stats/cdn-connections.png)

Bu grafik, kaç bağlantıları edge sunucularınıza kurulmuş gösterir. Her istek için CDN sonuçları bir bağlantı içinde geçtiği bir varlık.

## <a name="next-steps"></a>Sonraki Adımlar
* İle bildirim alın [Azure cdn'de gerçek zamanlı uyarılar](cdn-real-time-alerts.md)
* İle daha derine inin [Gelişmiş HTTP raporları](cdn-advanced-http-reports.md)
* Analiz [kullanım desenleri](cdn-analyze-usage-patterns.md)

