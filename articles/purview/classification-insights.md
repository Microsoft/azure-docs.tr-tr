---
title: Purview öngörülerini kullanarak Azure Takiview 'ta verileriniz üzerinde sınıflandırma raporlaması
description: Bu nasıl yapılır kılavuzunda, verileriniz üzerinde purview sınıflandırma raporlamasının nasıl görüntüleneceği ve kullanılacağı açıklanmaktadır.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b6ea3e762ad098e373baa8487d8926105820f226
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666514"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Azure purview 'daki verilerinize ilişkin öngörüleri sınıflandırma

Bu nasıl yapılır kılavuzunda verilerinize yönelik olarak nasıl erişim, görüntüleme ve filtreleme öngörüleri raporlarının nasıl yapılacağı açıklanmaktadır.

Desteklenen veri kaynakları şunlardır: Azure Blob depolama, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure SYNAPSE Analytics (eski adıyla SQL DW), Azure SQL veritabanı, Azure SQL yönetilen örneği, SQL Server, Amazon S3 demetleri

Bu nasıl yapılır kılavuzunda şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> - Azure 'dan purview hesabınızı başlatın
> - Verilerinize ilişkin sınıflandırma öngörülerini görüntüleme
> - Verileriniz hakkında daha fazla sınıflandırma ayrıntısı için detaya gidin

## <a name="prerequisites"></a>Önkoşullar

Purview Insights 'ı kullanmaya başlamadan önce aşağıdaki adımları tamamladığınızdan emin olun:

- Azure kaynaklarınızı ayarlayın ve test verileriyle ilgili hesapları doldurulmuştur

- Her veri kaynağındaki test verileri üzerinde bir tarama ayarlayın ve işlemi tamamlanmıştır. Daha fazla bilgi için bkz. [Azure 'da veri kaynaklarını yönetme (Önizleme)](manage-data-sources.md) ve [tarama kuralı kümesi oluşturma](create-a-scan-rule-set.md).

- Bir [veri okuyucu veya veri seçkin rolü](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)ile birlikte bir hesapla oturum açıldı.

Daha fazla bilgi için bkz. [Azure purview 'ta veri kaynaklarını yönetme (Önizleme)](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>Purview sınıflandırma öngörülerini kullanma

Azure purview ' de, sınıflandırmalar konu etiketlerine benzerdir ve tarama sırasında veriniz içinde bulunan belirli bir türün verilerini işaretlemek ve tanımlamak için kullanılır.

Purview, Microsoft 365 ile aynı hassas bilgi türlerini kullanır ve bu, mevcut güvenlik ilkelerinizi ve korumanızı tüm veri yayılmanız genelinde uzatmanızı sağlar.

> [!NOTE]
> Kaynak türlerinizi taradıktan sonra, yeni varlıkları yansıtmak için **Sınıflandırma** öngörülerini birkaç saat boyunca sunun.

**Sınıflandırma öngörülerini görüntülemek için:**

1. Azure portal **Azure purview** [örneği ekranına](https://aka.ms/purviewportal) gidin ve purview hesabınızı seçin.

1. **Genel bakış** sayfasında, **Başlarken** bölümünde, **purview hesabını Başlat** kutucuğunu seçin.

1. Takip görünümü ' nde,  :::image type="icon" source="media/insights/ico-insights.png" border="false"::: **Öngörüler** alanına erişmek için soldaki Öngörüler menü öğesini seçin.

1. **Öngörüler** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: alanında, purview **Sınıflandırma öngörüleri** raporunu görüntülemek için **Sınıflandırma** ' yı seçin.

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Sınıflandırma öngörüleri raporu" lightbox="media/insights/select-classification-labeling.png":::

   Ana **Sınıflandırma öngörüleri** sayfasında aşağıdaki bölgeler görüntülenir:

   |Alan  |Description  |
   |---------|---------|
   |**Sınıflandırmalarla kaynaklara genel bakış**     |Şunları sağlayan kutucukları görüntüler: <br>-Verilerinizde bulunan aboneliklerin sayısı <br>-Verilerinizde bulunan benzersiz sınıflandırmaların sayısı <br>-Bulunan sınıflandırılmış kaynakların sayısı <br>-Bulunan sınıflandırılmış dosya sayısı <br>-Bulunan sınıflandırılmış tablo sayısı         |
   |**Sınıflandırılmış verileri olan en popüler kaynaklar (son 30 gün)**     |Sınıflandırılmış verilerle bulunan kaynak sayısının son 30 günü boyunca eğilimi gösterir.            |
   |**Kaynaklara göre popüler sınıflandırma kategorileri**     |Sınıflandırma Kategorisi tarafından bulunan **finans** veya **kamu** gibi kaynak sayısını gösterir.      |
   |**Dosyalar için en iyi sınıflandırmalar**     |Verilerinize uygulanan, kredi kartı numaraları veya ulusal kimlik numaraları gibi en üst sınıflandırmaları gösterir.         |
   |**Tablolar için üst sınıflandırmalar**     | Verilerinize uygulanan ve kişisel tanımlama bilgileri gibi en üst sınıflandırmaların gösterir. |   
   |  **Sınıflandırma etkinliği** <br>(dosyalar ve tablolar) |  Her biri seçili zaman diliminde sınıflandırılan dosya veya tablo sayısını gösteren dosyalar ve tablolar için ayrı grafikler görüntüler. <br>**Varsayılan**: 30 gün<br>Görüntülenecek farklı bir zaman çerçevesini seçmek için grafiklerin üzerindeki **zaman** filtresini seçin.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Sınıflandırma öngörüleri ayrıntıya gitme

Aşağıdaki **Sınıflandırma öngörülerinin** hiçbirinde daha fazla ayrıntı için ayrıntıya git **bağlantısını seçin** :

- **Kaynaklara göre popüler sınıflandırma kategorileri**
- **Dosyalar için en iyi sınıflandırmalar**
- **Tablolar için üst sınıflandırmalar**
- **Sınıflandırma verileri > sınıflandırma etkinliği**

Örnek:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Tüm sınıflandırmaları görüntüle" lightbox="media/insights/view-classifications.png":::

Daha fazla bilgi edinmek için aşağıdakilerden birini yapın:

|Seçenek  |Açıklama  |
|---------|---------|
|**Verilerinizi filtreleme**     |  Sınıflandırma adı, abonelik adı veya kaynak türü dahil olmak üzere gösterilen verileri filtrelemek için kılavuzun üzerindeki filtreleri kullanın. <br><br>Tam sınıflandırma adından emin değilseniz, adın sonuna bir kısmını veya tümünü **filtre ölçütü anahtar** kutusuna girebilirsiniz.       |
|**Kılavuzu sıralama** |Kılavuzu bu sütuna göre sıralamak için bir sütun üst bilgisi seçin. | 
|**Sütunları düzenle**     |  Kılavuzunuzda daha fazla veya daha az sütun görüntülemek için **Sütunları Düzenle** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: ' yi seçin ve ardından siparişi görüntülemek veya değiştirmek istediğiniz sütunları seçin.   |
|**Daha fazla ayrıntıya gitme**     | Belirli bir sınıflandırmanın ayrıntısına gitmek için **Sınıflandırma** sütunundaki bir adı seçerek **kaynak raporuna göre sınıflandırmayı** görüntüleyin. <br><br>Bu rapor, kaynak adı, kaynak türü, abonelik KIMLIĞI ve sınıflandırılan dosya ve tablo sayıları dahil olmak üzere seçili sınıflandırmanın verilerini görüntüler.      |
|**Varlıklara gözatamıyorum**     |  Belirli bir sınıflandırma veya kaynakla bulunan varlıklara gözatmak için, görüntülediğiniz rapora bağlı olarak bir sınıflandırma veya kaynak seçin ve ardından filtrelerin üzerindeki **varlıklara göz at** ' ı seçin :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: . <br><br>Arama sonuçları, seçili filtre için bulunan tüm sınıflandırılan varlıkları görüntüler.  Daha fazla bilgi için bkz. [Azure purview veri kataloğunu arama](how-to-search-catalog.md).       |
| | |

## <a name="next-steps"></a>Sonraki adımlar

Azure purview Insight Reports hakkında daha fazla bilgi edinin
> [!div class="nextstepaction"]
> [Sözlük öngörüleri](glossary-insights.md)

> [!div class="nextstepaction"]
> [Öngörüleri Tara](scan-insights.md)

> [!div class="nextstepaction"]
> [Duyarlılık etiketleme öngörüleri](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Dosya Uzantısı öngörüleri](file-extension-insights.md)