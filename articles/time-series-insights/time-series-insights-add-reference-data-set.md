---
title: Ortamınıza başvuru veri kümesi ekleme-Azure Time Series Insights | Microsoft Docs
description: Bu makalede, Azure Time Series Insights ortamınızdaki verileri artırmak için bir başvuru verileri kümesinin nasıl ekleneceği açıklanmaktadır.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 31ae271a4ad6aa1370828640884a54eb2669804d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012717"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Time Series Insights ortamınız için Azure portal kullanarak bir başvuru veri kümesi oluşturun

Bu makalede, Azure Time Series Insights ortamınıza bir başvuru verileri kümesinin nasıl ekleneceği açıklanmaktadır. Başvuru verileri, değerleri artırmak için kaynak verilerinize katılması yararlı olur.

Başvuru veri kümesi, olay kaynağınızdan olayları geliştiren öğelerin bir koleksiyonudur. Time Series Insights giriş altyapısı, olay kaynağınızdaki her bir olayı, başvuru veri kümesindeki karşılık gelen veri satırıyla birleştirir. Bu genişletilmiş olay daha sonra sorgu için kullanılabilir. Bu JOIN, başvuru veri kümesinde tanımlanan birincil anahtar sütunlara dayalıdır.

Başvuru verileri geriye dönük olarak katılmadı. Bu nedenle, yapılandırılıp karşıya yüklendikten sonra yalnızca geçerli ve gelecekteki giriş verileri eşleştirilir ve başvuru tarihi kümesine birleştirilir.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Zaman serisi öngörüleri başvuru veri modeli hakkında bilgi edinin.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Başvuru veri kümesi ekleme

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Mevcut Azure Time Series Insights ortamınızı bulun. Azure portal sol tarafındaki menüden **tüm kaynaklar** ' ı seçin. Zaman Serisi Görüşleri ortamınızı seçin.

1. **Genel bakış** sayfasını seçin. **Time Series Insights Explorer URL 'sini** bulun ve bağlantıyı açın.  

   Time Series Insights ortamınızın Gezginini görüntüleyin.

1. Time Series Insights Gezgini 'nde ortam seçicisini genişletin. Etkin ortamı seçin. Gezgin sayfasında sağ üstteki başvuru verileri simgesini seçin.

   [![başvuru verileri ekleme](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. Yeni bir veri kümesi eklemeye başlamak için **+ veri kümesi Ekle** düğmesini seçin.

   [![veri kümesi Ekle](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. **Yeni başvuru veri kümesi** sayfasında, verilerin biçimini seçin:

   - Virgülle ayrılmış veriler için **CSV** 'yi seçin. İlk satır bir başlık satırı olarak değerlendirilir.
   - JavaScript nesne gösterimi (JSON) biçimli veriler için **JSON dizisi** ' ni seçin.

   [![veri biçimini seçin.](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. İki yöntemden birini kullanarak verileri sağlayın:

   - Verileri metin düzenleyicisine yapıştırın. Ardından, **başvuru verilerini Ayrıştır** düğmesini seçin.
   - Yerel bir metin dosyasından veri eklemek için **Dosya Seç** düğmesini seçin.

   Örneğin, CSV verilerini Yapıştır: [![CSV verisi yapıştırılan](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   Örneğin, JSON dizisi verilerini yapıştırın: [![JSON verilerini yapıştırma](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   Veri değerlerini ayrıştırırken bir hata varsa, sayfanın alt kısmında `CSV parsing error, no rows extracted`gibi, hata kırmızı renkte görünür.

1. Veriler başarıyla ayrıştırıldıktan sonra, verileri temsil eden sütunları ve satırları gösteren bir veri kılavuzu gösterilir.  Doğruluk sağlamak için veri kılavuzunu gözden geçirin.

   [![başvuru verileri ekleme](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. Her bir sütunu inceleyerek veri türünün kabul edilen olduğunu görüntüleyin ve gerekirse veri türünü değiştirin.  Sütun başlığındaki veri türü sembolünü seçin: Double (sayısal veriler) için **#** , **t |** Boolean için F veya dize Için **ABC** .

   [![sütun başlıklarında veri türleri seçin.](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. Gerekirse sütun üstbilgilerini yeniden adlandırın. Anahtar sütun adı, olay kaynağınızdaki ilgili özelliğe katılması için gereklidir. Başvuru veri anahtarı sütun adlarının, büyük/küçük harf duyarlılığı dahil olmak üzere gelen verilerinize tam olarak olay adıyla eşleştiğinden emin olun. Anahtar olmayan sütun adları, gelen verileri karşılık gelen başvuru verileri değerleriyle artırmak için kullanılır.

1. Gerektiğinde daha fazla başvuru verisi değeri eklemek için **satır ekle** veya **sütun Ekle** ' yi seçin.

1. Gerektiğinde belirli satırları gözden geçirmek için **satırları filtrele...** alanına bir değer yazın. Filtre, verileri gözden geçirmek için kullanışlıdır, ancak veriler karşıya yüklenirken uygulanmaz.

1. Veri kılavuzunun üzerindeki **veri kümesi adı** alanını doldurarak veri kümesini adlandırın.

    [![veri kümesini adlandırın.](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. Veri kılavuzunun üstündeki açılan öğeyi seçerek veri kümesindeki **birincil anahtar** sütununu belirtin.

    [anahtar sütun (ler) i ![seçin.](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    İsteğe bağlı olarak, bileşik birincil anahtar olarak ikincil anahtar sütunu eklemek için **+** düğmesini seçin. Seçimi geri almanız gerekiyorsa, ikincil anahtarı kaldırmak için açılan listeden boş değeri seçin.

1. Verileri karşıya yüklemek için **satırları karşıya yükle** düğmesini seçin.

    [Karşıya yükleme ![](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    Sayfada tamamlanan karşıya yükleme onaylanır ve ileti **başarıyla karşıya yüklendi**iletisi görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

* [Başvuru verilerini programlamayla yönetin](time-series-insights-manage-reference-data-csharp.md).

* Tam API başvurusu için [Başvuru Verileri API'si](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) belgesine bakın.
