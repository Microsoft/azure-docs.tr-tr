---
title: Ortamınızda saklama Yapılandırma-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights ortamınızda bekletmenin nasıl yapılandırılacağını öğrenin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 468b4f7ca7b0af4abc32df5d9ef64a74154d3de1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91569403"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>Azure Time Series Insights Gen1 ' de bekletme yapılandırılıyor

> [!CAUTION]
> Bu bir Gen1 makaledir.

Bu makalede, Azure Time Series Insights ' de **veri saklama süresi** ve **depolama sınırı aşıldı davranışının** nasıl yapılandırılacağı açıklanır.

## <a name="summary"></a>Özet

Her Azure Time Series Insights ortamının **veri saklama süresini** yapılandırma ayarı vardır. Değer 1 ile 400 gün arasında yayılır. Veriler ortam depolama kapasitesi veya Bekletme süresine (1-400) göre silinir, hangisi önce gelir.

Her Azure Time Series Insights ortamında, ek bir **depolama sınırı aşıldı davranışı** vardır. Bu ayar, bir ortamın maksimum kapasitesine ulaşıldığında giriş ve temizleme davranışını denetler. Aralarından seçim yapabileceğiniz iki davranış vardır:

- **Eski verileri temizle** (varsayılan)
- **Girişi Duraklat**

Bu ayarları daha iyi anlamak için ayrıntılı bilgiler için [Azure Time Series Insights ' de saklama](time-series-insights-concepts-retention.md)süresini gözden geçirin.  

## <a name="configure-data-retention"></a>Veri saklamayı yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Mevcut Azure Time Series Insights ortamınızı bulun. Azure portal sol tarafındaki menüden **tüm kaynaklar** ' ı seçin. Azure Time Series Insights ortamınızı seçin.

1. **Ayarlar** başlığı altında **depolama yapılandırması**' nı seçin.

    [![Ayarlar altında depolama yapılandırması ' nı seçin.](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Kaydırma çubuğunu kullanarak bekletme 'yi yapılandırmak için **veri saklama süresini (gün olarak)** seçin veya metin kutusuna bir sayı yazın.

1. Bu yapılandırma, verileri depolamak için maksimum veri olayı miktarını ve toplam depolama kapasitesini etkilediğinden **Kapasite** ayarını dikkate alın.

1. **Depolama sınırı aşıldı davranış** ayarını değiştirin. **Eski verileri temizle** veya giriş davranışını **Duraklat** ' ı seçin.

    [![Girişi duraklatma-kabul etme ve kaydetme.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Veri kaybından oluşan olası riskleri anlamak için belgeleri gözden geçirin. Değişiklikleri yapılandırmak için **Kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi için [Azure Time Series Insights ' de saklama süresini anlama](time-series-insights-concepts-retention.md)konusunu gözden geçirin.

- [Azure Time Series Insights ortamınızı ölçeklendirmeyi](time-series-insights-how-to-scale-your-environment.md)öğrenin.

- [Ortamınızı planlama](time-series-insights-environment-planning.md)hakkında bilgi edinin.
