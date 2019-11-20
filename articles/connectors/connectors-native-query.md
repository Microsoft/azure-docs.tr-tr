---
title: Logic Apps 'e sorgu eylemi ekleme | Microsoft Docs
description: Filtre dizisi gibi eylemleri gerçekleştirmeye yönelik sorgu eylemine genel bakış.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 10332b95e0d385d7155003efcc52b4bae3969313
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973758"
---
# <a name="get-started-with-the-query-action"></a>Sorgu eylemini kullanmaya başlayın
Sorgu eylemini kullanarak, iş akışlarını gerçekleştirmek için toplu işlerle ve dizilerle çalışabilirsiniz:

* Bir veritabanından tüm yüksek öncelikli kayıtlar için bir görev oluşturun.
* E-postalar için tüm PDF eklerini bir Azure blobuna kaydedin.

Bir mantıksal uygulamadaki sorgu eylemini kullanmaya başlamak için bkz. [mantıksal uygulama oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Sorgu eylemini kullanma
Eylem, mantıksal uygulamada tanımlanan iş akışı tarafından yürütülen bir işlemdir. 
[Eylemler hakkında daha fazla bilgi edinin](../connectors/apis-list.md).  

Sorgu eyleminde Şu anda, tasarımcı içinde gösterilen filtre dizisi olarak adlandırılan bir işlem vardır. Bu, bir diziyi sorgulamanızı ve filtrelenmiş bir sonuç kümesi döndürbırakmanıza olanak tanır.

Mantıksal uygulamada nasıl ekleyebileceğiniz aşağıda açıklanmıştır:

1. **Yeni adım** düğmesini seçin.
2. **Eylem Ekle**' yi seçin.
3. Eylem arama kutusunda, filtre **dizisi** eylemini listelemek için **filtre** yazın.
   
    ![Sorgu eylemini seçin](./media/connectors-native-query/using-action-1.png)
4. Filtrelenecek bir dizi seçin. (Aşağıdaki ekran görüntüsünde, bir Twitter aramasının sonuçlarının dizisi gösterilmektedir.)
5. Her öğede değerlendirmek için bir koşul oluşturun. (Aşağıdaki ekran görüntüsünde 100 ' den fazla izleyici bulunan kullanıcılardan fazla sayıda Kullanıcı filtresi yapılır.)
   
    ![Sorgu eylemini tamamlar](./media/connectors-native-query/using-action-2.png)
   
    Eylem, yalnızca filtre gereksinimlerini karşılayan sonuçları içeren yeni bir dizi çıktısı alınacaktır.
6. Kaydetmek için araç çubuğunun sol üst köşesine tıklayın ve mantıksal uygulamanız hem kaydet hem de Yayımla (etkinleştir) olarak değişir.

\* bir HTTP uç noktası arıyorsanız ve bir JSON yanıtı alıyorsanız JSON yanıtını ayrıştırmak için JSON 'u _ayrıştırma_ eylemini kullanın. Bu adımı uygulamadan, _filtre dizisi_ yalnızca gövde görür ve JSON yükünün yapısını anlamaz.

## <a name="query-action"></a>Sorgu eylemi
Bu, bağlayıcının desteklediği eylemin ayrıntılarını aşağıda bulabilirsiniz. Bağlayıcının olası bir eylemi vardır.

| Eylem | Açıklama |
| --- | --- |
| Diziyi filtrele |Dizideki her öğe için bir koşulu değerlendirir ve sonuçları döndürür |

## <a name="action-details"></a>Eylem ayrıntıları
Sorgu eylemi olası bir eylemle birlikte gelir. Aşağıdaki tablolarda eyleme yönelik gerekli ve isteğe bağlı giriş alanları ve eylemi kullanımıyla ilişkili ilgili çıkış ayrıntıları açıklanır.

### <a name="filter-array"></a>Diziyi filtrele
Aşağıdakiler, bir HTTP giden isteği oluşturan eylem için giriş alanlarıdır.
Bir *, gerekli bir alan olduğu anlamına gelir.

| Görünen ad | Özellik adı | Açıklama |
| --- | --- | --- |
| 'Ten |Kaynak |Filtrelenecek dizi |
| Koşul |olmadığı |Her öğe için değerlendirilecek koşul |

<br>

### <a name="output-details"></a>Çıkış ayrıntıları
HTTP yanıtı için çıkış ayrıntıları aşağıda verilmiştir.

| Özellik adı | Veri türü | Açıklama |
| --- | --- | --- |
| Filtrelenmiş dizi |array |Filtrelenen her sonuç için bir nesne içeren bir dizi |

## <a name="next-steps"></a>Sonraki adımlar
Şimdi, platformu deneyin ve [bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Mantıksal uygulamalarda kullanılabilir olan diğer bağlayıcıları, [API](apis-list.md)'larımıza bakarak inceleyebilirsiniz.

