---
title: Ortamınızda saklama Yapılandırma-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights ortamınızda bekletmenin nasıl yapılandırılacağını öğrenin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: aa905d1686939346827d07419a62fd6f299387ec
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012608"
---
# <a name="configuring-retention-in-time-series-insights"></a>Time Series Insights bekletme yapılandırma

Bu makalede, Azure Time Series Insights ' de **veri saklama süresi** ve **depolama sınırı aşıldı davranışının** nasıl yapılandırılacağı açıklanır.

## <a name="summary"></a>Özet

Her Azure Time Series Insights ortamının **veri saklama süresini**yapılandırma ayarı vardır. Değer 1 ile 400 gün arasında yayılır. Veriler ortam depolama kapasitesi veya Bekletme süresine (1-400) göre silinir, hangisi önce gelir.

Her Time Series Insights ortamında, ek bir **depolama sınırı aşıldı davranışı**vardır. Bu ayar, bir ortamın maksimum kapasitesine ulaşıldığında giriş ve temizleme davranışını denetler. Aralarından seçim yapabileceğiniz iki davranış vardır:

- **Eski verileri temizle** (varsayılan)
- **Girişi Duraklat**

Bu ayarları daha iyi anlamak için ayrıntılı bilgiler için [Time Series Insights ' de saklama](time-series-insights-concepts-retention.md)süresini gözden geçirin.  

## <a name="configure-data-retention"></a>Veri saklamayı yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Mevcut Time Series Insights ortamınızı bulun. Azure portal sol tarafındaki menüden **tüm kaynaklar** ' ı seçin. Zaman Serisi Görüşleri ortamınızı seçin.

1. **Ayarlar** başlığı altında **Yapılandır**' ı seçin.

    [![ayarları seçin ve ardından yapılandırın](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. Kaydırma çubuğunu kullanarak bekletme 'yi yapılandırmak için **veri saklama süresini (gün olarak)** seçin veya metin kutusuna bir sayı yazın.

1. Bu yapılandırma, verileri depolamak için maksimum veri olayı miktarını ve toplam depolama kapasitesini etkilediğinden **Kapasite** ayarını dikkate alın.

1. **Depolama sınırı aşıldı davranış** ayarını değiştirin. **Eski verileri temizle** veya giriş davranışını **Duraklat** ' ı seçin.

    [![veri bekletme-kabul edin ve kaydedin.](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. Belgeleri gözden geçirdiğinizi ve veri kaybını karşılayan olası riskleri anladığınızı belirten onay kutusunu kabul edin. Değişiklikleri yapılandırmak için **Kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi için [Time Series Insights ' de saklama süresini anlama](time-series-insights-concepts-retention.md)konusunu gözden geçirin.

- [Time Series Insights ortamınızı ölçeklendirmeyi](time-series-insights-how-to-scale-your-environment.md)öğrenin.

- [Ortamınızı planlama](time-series-insights-environment-planning.md)hakkında bilgi edinin.