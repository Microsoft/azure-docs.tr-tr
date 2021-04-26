---
title: Azure purview 'da verileriniz hakkındaki öngörüleri tarayın
description: Bu nasıl yapılır kılavuzunda, verileriniz üzerinde purview Insights tarama raporlaması 'nın nasıl görüntüleneceği ve kullanılacağı açıklanmaktadır.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548700"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Azure purview 'da verileriniz hakkındaki öngörüleri tarayın

Bu nasıl yapılır kılavuzunda verileriniz için Azure purview tarama öngörüleriyle ilgili nasıl erişme, görüntüleme ve filtreleme açıklanmaktadır.

Bu nasıl yapılır kılavuzunda şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * Purview hesabınızdan öngörüleri görüntüleyin.
> * Taramalarınızın bir kuşbakışı görünümünü alın.

## <a name="prerequisites"></a>Önkoşullar

Purview Insights 'ı kullanmaya başlamadan önce aşağıdaki adımları tamamladığınızdan emin olun:

* Azure kaynaklarınızı ayarlayın ve hesabı verilerle doldurun.
* Veri kaynağındaki bir taramayı ayarlayın ve doldurun.

Daha fazla bilgi için bkz. [Azure purview 'ta veri kaynaklarını yönetme (Önizleme)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Purview tarama öngörülerini kullanma

Azure purview ' de kaynak türlerini kaydedebilir ve tarayabilirsiniz. Tarama öngörülerine zaman içinde tarama durumunu görüntüleyebilirsiniz. Öngörüler, belirli bir süre içinde başarısız olan, başarılı veya alma işlemlerinin kaç kez iptal edildiğini söyler.

### <a name="view-scan-insights"></a>Tarama içgörülerini görüntüleme

1. Azure portal **Azure purview** örneği ekranına gidin ve purview hesabınızı seçin.

1. **Genel bakış** sayfasında, **Başlarken** bölümünde, **purview Studio 'yu aç** kutucuğunu seçin.

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Azure portal Takiview 'ı başlatın":::

1. Takip görünümü **giriş** sayfasında, Öngörüler alanına erişmek için **öngörüleri görüntüle** kutucuğunu seçin  :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Azure portal öngörülerinizi görüntüleyin":::

1. **Öngörüler** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: alanında **taramalar** ' i seçerek purview **tarama öngörüleri** raporunu görüntüleyin.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Her taramanın durumuna göre tarama sayısını göstermek için üst düzey KPI 'Ları görüntüleme ve derinlemesine bakış
 
1. Üst düzey KPI 'ler, bir dönem içinde çalıştırılan toplam tarama gösterir. Zaman aralığı, son 30 gün için varsayılan olarak ayarlanır. Ancak, son yedi gün ' yı da seçebilirsiniz. Zaman filtresine bağlı olarak, KPI değerleri tarama sayısını uygun şekilde yansıtır.


1. Seçilen zaman filtresi değerine bağlı olarak, başarılı, başarısız ve iptal edilmiş taramaların haftaya göre veya grafikteki güne göre dağıtımını görebilirsiniz.

1. Grafiğin en altında, daha fazla araştırma yapmanız için **daha fazla bakış** bağlantısı bulunur. Bağlantı, öngörüleri Tara deneyimi içinde  **tarama durumu** sayfasını açar. Burada, son 30 gün içinde bir tarama adı ve başarılı, başarısız veya iptal edilme süresi hakkında bilgi edinebilirsiniz.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Tarama durumunu zamana göre görüntüle":::

4. Belirli bir taramayı daha ayrıntılı bir şekilde araştırarak, Azure purview 'ın **kaynaklar** deneyiminde tarama geçmişine bağlanmanızı sağlayacak **tarama adına** tıklayın. Çalışma geçmişi sayfasında, daha fazla hata araştırmada yardımcı olacak çalıştırma KIMLIĞINI alabilirsiniz.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Tarama ayrıntılarını görüntüle":::

5. Son olarak, çalışma geçmişi sayfasının sol üst köşesindeki birlikte bulunan Crumbs ' yi izleyerek öngörüleri **tarama durumu** sayfasına geri dönebilirsiniz.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Tarama geçmişini görüntüleyin"::: 

## <a name="next-steps"></a>Sonraki adımlar

* [Veri öngörüleri](./concept-insights.md) Ile Azure purview **öngörüleri** hakkında daha fazla bilgi edinin

* [Veri kaynaklarını yönetme](./manage-data-sources.md) Ile Azure purview 'ın **kaynaklar** deneyimi hakkında daha fazla bilgi edinin