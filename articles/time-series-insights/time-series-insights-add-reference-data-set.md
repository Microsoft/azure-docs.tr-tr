---
title: Ortamınıza başvuru veri kümeleri ekleme-Azure Time Series Insights | Microsoft Docs
description: Bu makalede, Azure Time Series Insights ortamınızdaki verileri artırmak için bir başvuru verileri kümesinin nasıl ekleneceği açıklanmaktadır.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: d80d97a609aa3a464b9b114439fe7f4058e287c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001839"
---
# <a name="create-a-reference-data-set-for-your-azure-time-series-insights-gen1-environment-using-the-azure-portal"></a>Azure portal kullanarak Azure Time Series Insights Gen1 ortamınız için bir başvuru veri kümesi oluşturun

> [!CAUTION]
> Bu bir Gen1 makaledir.

Bu makalede, Azure Time Series Insights ortamınıza bir başvuru verileri kümesinin nasıl ekleneceği açıklanmaktadır. Başvuru verileri, değerleri artırmak için kaynak verilerinize katılması yararlı olur.

Başvuru veri kümesi, olay kaynağınızdan olayları geliştiren öğelerin bir koleksiyonudur. Azure Time Series Insights giriş altyapısı, olay kaynağınızdaki her bir olayı, başvuru veri kümesindeki karşılık gelen veri satırıyla birleştirir. Bu genişletilmiş olay daha sonra sorgu için kullanılabilir. Bu JOIN, başvuru veri kümesinde tanımlanan birincil anahtar sütunlara dayalıdır.

Başvuru verileri geriye dönük olarak katılmadı. Bu nedenle, yapılandırılıp karşıya yüklendikten sonra yalnızca geçerli ve gelecekteki giriş verileri eşleştirilir ve başvuru tarihi kümesine birleştirilir.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Zaman serisi öngörüleri başvuru veri modeli hakkında bilgi edinin.</br>

> [!VIDEO <https://www.youtube.com/embed/Z0NuWQUMv1o>]

## <a name="add-a-reference-data-set"></a>Başvuru veri kümesi ekleme

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Mevcut Azure Time Series Insights ortamınızı bulun. Azure portal sol tarafındaki menüden **tüm kaynaklar** ' ı seçin. Azure Time Series Insights ortamınızı seçin.

1. **Genel Bakış** sayfasını seçin. **Time Series Insights gezgin URL 'sini** bulmak ve bağlantıyı açmak için sayfanın üst kısmındaki **temel** bileşenler bölümünü genişletin.  

   [![Essentials bölümünü Genişlet](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Azure Time Series Insights ortamınızın Gezginini görüntüleyin.

1. Azure Time Series Insights Gezgini 'nde ortam seçicisini genişletin. Etkin ortamı seçin. Gezgin sayfasında sağ üstteki başvuru verileri simgesini seçin.

   [![Başvuru verileri ekleme](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Yeni bir veri kümesi eklemeye başlamak için **+ veri kümesi Ekle** düğmesini seçin.

   [![Veri kümesi Ekle](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. **Yeni başvuru veri kümesi** sayfasında, verilerin biçimini seçin:

   - Virgülle ayrılmış veriler için **CSV** 'yi seçin. İlk satır bir başlık satırı olarak değerlendirilir.
   - JavaScript nesne gösterimi (JSON) biçimli veriler için **JSON dizisi** ' ni seçin.

   [![Veri biçimi seçin.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. İki yöntemden birini kullanarak verileri sağlayın:

   - Verileri metin düzenleyicisine yapıştırın. Ardından, **başvuru verilerini Ayrıştır** düğmesini seçin.
   - Yerel bir metin dosyasından veri eklemek için **Dosya Seç** düğmesini seçin.

   Örneğin, CSV verilerini yapıştırın: [ ![ yapıştırılan CSV verileri](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Örneğin, JSON dizisi verilerini Yapıştır: [ ![ JSON verilerini Yapıştır](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Veri değerlerini ayrıştırırken bir hata oluşursa, hata sayfanın alt kısmında kırmızı renkte görünür `CSV parsing error, no rows extracted` .

1. Veriler başarıyla ayrıştırıldıktan sonra, verileri temsil eden sütunları ve satırları gösteren bir veri kılavuzu gösterilir. Doğruluk sağlamak için veri kılavuzunu gözden geçirin.

   [![Başvuru verilerini gözden geçirin](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Kabul edilen veri türünü anlamak için her sütunu gözden geçirin ve gerekirse veri türünü değiştirin.  Sütun başlığındaki veri türü sembolünü seçin: **#** Double (sayısal veriler) Için **T |** Boolean için F veya dize Için **ABC** .

   [![Sütun başlıklarında veri türleri ' ni seçin.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Gerekirse sütun üstbilgilerini yeniden adlandırın. Anahtar sütun adı, olay kaynağınızdaki ilgili özelliğe katılması için gereklidir.

   > [!IMPORTANT]
   > Başvuru veri anahtarı sütun adlarının, büyük/küçük harf duyarlılığı dahil olmak üzere gelen verilerinize tam olarak olay adıyla eşleştiğinden emin olun. Anahtar olmayan sütun adları, gelen verileri karşılık gelen başvuru verileri değerleriyle artırmak için kullanılır.

1. Gerektiğinde belirli satırları gözden geçirmek için **satırları filtrele...** alanına bir değer yazın. Filtre, verileri gözden geçirmek için kullanışlıdır, ancak veriler karşıya yüklenirken uygulanmaz.

1. Veri kılavuzunun üzerindeki **veri kümesi adı** alanını doldurarak veri kümesini adlandırın.

    [![Veri kümesini adlandırın.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Veri kılavuzunun üstündeki açılan öğeyi seçerek veri kümesindeki **birincil anahtar** sütununu belirtin.

    [![Anahtar sütun (ler) i seçin.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Isteğe bağlı)** **+** Bileşik birincil anahtar olarak ikincil anahtar sütunu eklemek için düğmeyi seçin. Seçimi geri almanız gerekiyorsa, ikincil anahtarı kaldırmak için açılan listeden boş değeri seçin.

1. Verileri karşıya yüklemek için **satırları karşıya yükle** düğmesini seçin.

    [![Satırları karşıya yükleyin ve verileri onaylayın.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Sayfada tamamlanan karşıya yükleme onaylanır ve ileti **başarıyla karşıya yüklendi** iletisi görüntülenir.

    > [!WARNING]
    > Başvuru veri kümeleri arasında paylaşılan sütunlarda veya özelliklerde **yinelenen özellik adı** karşıya yükleme hatası görüntülenir. Hata, başvuru veri kümelerinin başarıyla karşıya yüklenmesini engellemez. Yinelenen özellik adını paylaşan satırlar birleştirilerek kaldırılabilir.

1. Gerektiğinde daha fazla başvuru verisi değeri eklemek için **satır ekle**, **satırları toplu al** veya **sütun Ekle** ' yi seçin.

    [![Satır ekleyin, satırları toplu içe aktarın veya bir sütun ekleyin.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Benzersiz bir anahtarı başka bir satırla paylaşan herhangi bir satırın sütunları, bu benzersiz anahtarı paylaşan son satır tarafından geçersiz kılınır.

   > [!NOTE]
   > Eklenen satırların *dikdörtgen* **olması gerekmez,** başvuru veri kümesindeki diğer girişlerden daha az, daha büyük veya değişen sütunları olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Başvuru verilerini programlamayla yönetin](time-series-insights-manage-reference-data-csharp.md).

- Tüm API başvurusu için [başvuru verileri API 'si](/rest/api/time-series-insights/gen1-reference-data-api) belgesini okuyun.