---
title: Azure Takiview 'ta verileriniz üzerinde rapor oluşturma öngörülerini kullanarak dosya uzantısı
description: Bu nasıl yapılır kılavuzunda, verileriniz üzerinde takip etme dosya uzantısının nasıl görüntüleneceği ve kullanılacağı açıklanmaktadır.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 5cbfb41d50e055f745864e4d5f8bc15a55d925e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668578"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Azure purview 'daki verilerinize ilişkin dosya uzantısı öngörüleri 

Bu nasıl yapılır kılavuzunda, verilerinizde bulunan dosya uzantıları veya dosya türleri hakkında öngörülere erişme, bunları görüntüleme ve bunlara filtre uygulama açıklanmaktadır.

Desteklenen veri kaynakları şunlardır: Azure Blob depolama, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Amazon S3 demetleri

Bu nasıl yapılır kılavuzunda şunları yapmayı öğreneceksiniz:
> [!div class="checklist"]
> * Azure 'dan purview hesabınızı başlatın 
> - Verileriniz üzerinde dosya uzantısı öngörülerini görüntüleme
> - Verilerinize ilişkin daha fazla dosya uzantısı ayrıntısı için detaya gidin

## <a name="prerequisites"></a>Önkoşullar 

Purview Insights 'ı kullanmaya başlamadan önce aşağıdaki adımları tamamladığınızdan emin olun:

- Azure kaynaklarınızı ayarlayın ve test verileriyle ilgili hesapları doldurulmuştur

- Her veri kaynağındaki test verileri üzerinde bir tarama ayarlayın ve işlemi tamamlanmıştır. Daha fazla bilgi için bkz. [Azure 'da veri kaynaklarını yönetme (Önizleme)](manage-data-sources.md) ve [tarama kuralı kümesi oluşturma](create-a-scan-rule-set.md).

- Bir [veri okuyucu veya veri seçkin rolü](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)ile birlikte bir hesapla oturum açıldı.


Daha fazla bilgi için bkz. [Azure purview 'ta veri kaynaklarını yönetme (Önizleme)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Purview dosya uzantısı öngörülerini kullanma

Varlıklarınızı tararken Azure purview, verilerinizde bulunan dosya türlerini tespit edebilir ve her dosya türü hakkında daha fazla ayrıntı sağlar. Ayrıntılar, sahip olduğunuz her türden dosyanın sayısını, bu dosyaların nerede olduğunu ve hassas veriler için tarama yapılıp yapılmayacağını içerir.

> [!NOTE]
> Kaynak türlerinizi taradıktan sonra, yeni varlıkları yansıtmak için **Dosya Uzantısı** öngörülerini birkaç saat verin.

**Dosya Uzantısı öngörülerini görüntülemek için:**

1. Azure portal **Azure purview** [örneği ekranına](https://aka.ms/purviewportal) gidin ve purview hesabınızı seçin.

1. **Genel bakış** sayfasında, **Başlarken** bölümünde, **purview hesabını Başlat** kutucuğunu seçin.

1. Takip görünümü ' nde,  :::image type="icon" source="media/insights/ico-insights.png" border="false"::: **Öngörüler** alanına erişmek için soldaki Öngörüler menü öğesini seçin.
    
1. **Öngörüler** içinde **Dosya uzantıları** sekmesini seçin.

    Bu rapor, seçilen zaman diliminde (varsayılan: 30 gün) çok sayıda benzersiz dosya uzantısının ve bulunan en iyi 10 uzantı grafiğinin bir özetini görüntüler.

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Dosya Uzantısı raporu-genel bakış" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Daha fazla bilgi edinmek için aşağıdakilerden birini yapın:

    - Dosya uzantılarının bulunduğu zaman aralığını değiştirmek için raporun en üstündeki **zaman** seçicisini seçin.
    
    - Bulunan dosya uzantılarının tam listesini görüntülemek için grafiğin altında **daha fazla görüntüle** ' yi seçin. Daha fazla bilgi için bkz. [Dosya Uzantısı öngörüleri ayrıntıya gitme](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Dosya Uzantısı öngörüleri ayrıntıya gitme

Verilerinizde bulunan dosya türleriyle ilgili üst düzey bilgileri görüntüledikten sonra, bulundukları konum hakkında daha fazla ayrıntı ve gizli veriler için taranıp taranamayacağını öğrenmek için detaya gidin.

Örnek:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Dosya Uzantısı raporu-ayrıntıya git" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

Kılavuzda, aşağıdakiler dahil olmak üzere bulunan her bir dosya uzantısının ayrıntıları gösterilmektedir:

- **Dosya sayısı**. Belirtilen uzantıya sahip dosya sayısı.
- **İçerik tarama**. Dosya uzantısının gizli verilerin taranması için desteklenip desteklenmediğini belirtir.
- **Abonelikler**. Belirtilen uzantının bulunduğu abonelik sayısı.
- **Kaynaklar**. Belirtilen dosya uzantısıyla bulunan kaynak sayısı.



Gösterilen verileri filtrelemek için kılavuzun üzerindeki filtreleri kullanın:

|Seçenek  |Açıklama  |
|---------|---------|
|**Anahtar sözcüğe göre filtrele**     |    Dosya türlerinizi ada göre filtrelemek için **anahtar sözcüğe göre filtrele**  kutusuna metin girin. Örneğin, yalnızca PDF 'Leri görüntülemek için girin `PDF` .     |
|**Saat**        | Verilerinizin oluşturulduğu zamana ait belirli bir zaman aralığına göre filtrelemek için seçin. <br>**Varsayılan:** 30 gün  |
|**Dosya Uzantısı**     |Kılavuza bir veya daha fazla dosya türüne göre filtre uygulamak için seçin.        |
|**Kaynaklar**    |Kılavuza belirli veri kaynaklarına göre filtre uygulamak için seçin. |
|**İçerik tarama**     |Yalnızca hassas veriler için daha fazla taranabilecek dosya türlerini veya **. CERT** veya **. jpg** dosyaları gibi taranmayan verileri göstermek için **desteklenen** veya **Desteklenmeyen**' ı seçin. |
| | |

Filtrelerin üzerinde **Sütunları Düzenle** ' yi seçerek :::image type="icon" source="media/insights/ico-columns.png" border="false"::: kılavuzunuzda daha fazla veya daha az sütun görüntüleyin veya sıralamayı değiştirin. 

Kılavuzu sıralamak için sütuna göre sıralanacak bir sütun üst bilgisi seçin.
## <a name="next-steps"></a>Sonraki adımlar

Azure purview Insight Reports hakkında daha fazla bilgi edinin
> [!div class="nextstepaction"]
> [Sözlük öngörüleri](glossary-insights.md)

> [!div class="nextstepaction"]
> [Öngörüleri Tara](scan-insights.md)

> [!div class="nextstepaction"]
> [Sınıflandırma öngörüleri](./classification-insights.md)

> [!div class="nextstepaction"]
> [Duyarlılık etiketi öngörüleri](sensitivity-insights.md)
