---
title: Bir markalar modelini özelleştirmek için Video Indexer Web sitesini kullanma-Azure
titleSuffix: Azure Media Services
description: Bu makalede, Video Indexer Web sitesiyle bir markalar modelinin nasıl özelleştirileceği gösterilmektedir.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f4b2f7a8b43c1723beb0dd8da82814f74cd7b135
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838356"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Video Indexer Web sitesiyle bir markalar modeli özelleştirme

Video Indexer, video ve ses içeriğinin dizin oluşturma ve yeniden dizin oluşturma sırasında konuşma ve görsel metinden marka algılamayı destekler. Marka algılama özelliği, Bing 'ün markalar veritabanı tarafından önerilen ürünlerin, hizmetlerin ve şirketlerin bahsetmelerini belirler. Örneğin, Microsoft bir video veya ses içeriğinde bahsedildiğinde veya videoda görsel metin gösteriyorsa, Video Indexer içeriği bir marka olarak algılar. Özel bir markalar modeli, Bing markalar veritabanından markalar belirleyip algılamadığına, belirli markaların algılanarak dışlanVideo Indexer mayacağını (esas olarak siyah bir marka listesi oluşturarak) ve modelinizin bir parçası olması gereken markalar dahil etmenize olanak tanır. Bu, Bing 'ün markalar veritabanında bulunmayabilir (aslında bir markaların beyaz listesini oluşturuyor).

Ayrıntılı bir genel bakış için bkz. [genel bakış](customize-brands-model-overview.md).

Bu konuda açıklandığı gibi, videoda algılanan özel markalar modellerini oluşturmak, kullanmak ve düzenlemek için Video Indexer Web sitesini kullanabilirsiniz. API 'yi, API ['leri kullanarak markalar modelini özelleştirme](customize-brands-model-with-api.md)bölümünde açıklandığı gibi de kullanabilirsiniz.

## <a name="edit-the-settings-of-the-brands-model"></a>Markalar modelinin ayarlarını düzenleyin  

Bing markaların veritabanından alınan markalar isteyip istemediğinizi belirleme seçeneğiniz vardır. Bunun için, markalar modelinizin ayarlarını düzenlemeniz gerekir.

1. [Video Indexer](https://www.videoindexer.ai/) web sitesine gidip oturum açın.
2. Hesabınızdaki bir modeli özelleştirmek için sayfanın sağ üst köşesinde bulunan **içerik modeli özelleştirmesi** düğmesine tıklayın.
 
   ![İçerik modelini Özelleştir](./media/content-model-customization/content-model-customization.png) 
3. Markalar düzenlemek için **markalar** sekmesini seçin.

    ![Markalar modelini özelleştirme](./media/customize-brand-model/customize-brand-model.png)
4. Video Indexer Bing tarafından önerilen markalar içermesini istiyorsanız **Bing tarafından önerilen marka göster** seçeneğini işaretleyin. İçeriğinizdeki Bing tarafından önerilen markaların algılamasını Video Indexer istemiyorsanız, seçeneğini işaretlenmemiş olarak bırakın. 

## <a name="include-brands-in-the-model"></a>Modele markalar dahil etme

**Markaların dahil** edilmesi bölümünde, video Indexer Bing tarafından önerilmese bile algılamasını istediğiniz özel markalar temsil eder.  

### <a name="add-a-brand"></a>Marka ekleyin

1. "+ Marka Ekle" seçeneğine tıklayın.

    ![Markalar modelini özelleştirme](./media/customize-brand-model/add-brand.png)

    Ad (gerekli), kategori (isteğe bağlı), açıklama (isteğe bağlı) ve başvuru URL 'SI (isteğe bağlı) girin.
    Kategori alanı, markalarınızı etiketleyerek size yardımcı olmak için tasarlanmıştır. Bu alan, Video Indexer API 'Leri kullanılırken markasının *etiketleri* olarak gösterilir. Örneğin, "Azure" markası, "Cloud" olarak etiketlenebilir veya kategorilere ayrılmıştır.

    Başvuru URL 'SI alanı, Vican sayfasının bağlantısı gibi, marka için herhangi bir başvuru Web sitesi olabilir.
2. "Marka Ekle" ye tıklayın, markaların **dahil olan markalar** listesine eklendiğini görürsünüz.

### <a name="edit-a-brand"></a>Marka düzenleme

1. Düzenlemek istediğiniz markasının yanındaki kurşun kalem simgesine tıklayın.

    Bir markasının kategorisini, açıklamasını veya başvuru URL 'sini güncelleştirebilirsiniz. Markaların adları benzersiz olduğundan, markaların adını değiştiremezsiniz. Marka adını değiştirmeniz gerekiyorsa, tüm markı silin (sonraki bölüme bakın) ve yeni adla yeni bir marka oluşturun.
2. Markanızı yeni bilgilerle güncelleştirmek için **Güncelleştir** düğmesine tıklayın.

### <a name="delete-a-brand"></a>Marka silme

1. Silmek istediğiniz markasının yanındaki çöp kutusu simgesine tıklayın.
2. "Sil" e tıklayın ve marka, marka *marka* listenizde yer alır.

## <a name="exclude-brands-from-the-model"></a>Markaların modelden dışlanmasını

**Markaların hariç tutulması** bölümü, video Indexer algılaması için istediğiniz markalarını temsil eder.

### <a name="add-a-brand"></a>Marka ekleyin

1. "+ Marka Ekle" seçeneğine tıklayın.

    Ad (gerekli), kategori (isteğe bağlı) girin.
2. "Marka Ekle" ye tıklayın, markaların *dışlama* listesine eklendiğini görürsünüz.

### <a name="edit-a-brand"></a>Marka düzenleme

1. Düzenlemek istediğiniz markasının yanındaki kurşun kalem simgesine tıklayın.

    Yalnızca bir markasının kategorisini güncelleştirebilirsiniz. Markaların adları benzersiz olduğundan, markaların adını değiştiremezsiniz. Marka adını değiştirmeniz gerekiyorsa, tüm markı silin (sonraki bölüme bakın) ve yeni adla yeni bir marka oluşturun.
2. Markanızı yeni bilgilerle güncelleştirmek için **Güncelleştir** düğmesine tıklayın.

### <a name="delete-a-brand"></a>Marka silme

1. Silmek istediğiniz markasının yanındaki çöp kutusu simgesine tıklayın.
2. "Sil" e tıklayın ve marka, *hariç tutma markası* listenizde görünmez.

## <a name="next-steps"></a>Sonraki adımlar

[API 'Leri kullanarak markalar modelini özelleştirme](customize-brands-model-with-api.md)
