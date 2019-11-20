---
title: Özel listeler ve API konsolu ile orta boyutlu görüntüler-Content Moderator
titleSuffix: Azure Content Moderator
description: Özel görüntü listeleri oluşturmak için Azure Content Moderator 'da liste yönetimi API 'sini kullanırsınız.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 27d9b12d9e1a0237050243c2b5f07edaa8d8857a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757186"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>API konsolundaki özel görüntü listeleriyle orta

Özel görüntü listeleri oluşturmak için Azure Content Moderator 'da [liste YÖNETIMI API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) 'sini kullanırsınız. Görüntü denetleme API 'SI ile görüntülerin özel listelerini kullanın. Görüntü denetleme işlemi görüntünüzü değerlendirir. Özel listeler oluşturursanız, işlem onu özel listeinizdeki görüntülerle karşılaştırır. Görüntüyü engellemek veya buna izin vermek için özel listeler kullanabilirsiniz.

> [!NOTE]
> Listesi sayısı üst sınırı, her biri **10.000 görüntüyü aşmamak** kaydıyla **5 listedir**.
>

Aşağıdaki görevleri yapmak için liste yönetimi API 'sini kullanın:

- Liste oluşturma.
- Bir listeye görüntü ekleyin.
- Listedeki görüntülere karşı ekran görüntüleri.
- Listeden görüntüleri silin.
- Listeyi silme.
- Liste bilgileri düzenleme.
- Listede yapılan değişikliklerin yeni taramaya eklenmesi için dizini yenileyin.

## <a name="use-the-api-console"></a>API konsolunu kullanma
Çevrimiçi konsolda API 'YI test etmeden önce, abonelik anahtarınız olmalıdır. Bu, **Ayarlar** sekmesinde, **OCP-apim-Subscription-Key** kutusunda bulunur. Daha fazla bilgi için bkz. [Genel Bakış](overview.md).

## <a name="refresh-search-index"></a>Arama dizinini Yenile

Bir görüntü listesinde değişiklik yaptıktan sonra, gelecekteki taramalara dahil edilecek değişikliklerin dizinini yenilemeniz gerekir. Bu adım, masaüstünüzdeki bir arama altyapısının (etkinse) veya bir Web araması altyapısının, yeni dosya veya sayfaları içerecek şekilde dizinini sürekli olarak yenilediğine benzer.

1. [Görüntü listesi yönetim API 'si başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)' nda, sol menüsünde, **görüntü listeleri**' ni seçin ve ardından **Arama dizinini Yenile**' yi seçin.

   **Görüntü listeleri-arama dizinini Yenile** sayfası açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 
 
    ![Görüntü listeleri-arama dizini sayfa bölgesi seçimini Yenile](images/test-drive-region.png)

    **Görüntü listeleri-arama dizinini Yenile** API konsolu açılır.

3. **ListId** kutusuna liste kimliğini girin. Abonelik anahtarınızı girin ve ardından **Gönder**' i seçin.

   ![Görüntü listeleri-arama dizini konsol yanıtı içerik kutusunu Yenile](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Görüntü listesi oluşturma

1. [Görüntü listesi yönetim API 'si başvurusuna](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)gidin.

   **Görüntü listeleri-oluştur** sayfası açılır. 

3. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin.

   ![Görüntü listeleri-sayfa bölgesi seçimi oluşturma](images/test-drive-region.png)

   **Görüntü listeleri-** API konsolu Oluştur açılır.
 
4. **OCP-apim-Subscription-Key** kutusuna abonelik anahtarınızı girin.

5. **İstek gövdesi** kutusuna **ad** (örneğin, myList) ve **Açıklama**değerlerini girin.

   ![Görüntü listeleri-konsol Isteği gövdesi adı ve açıklaması oluşturma](images/try-terms-list-create-1.png)

6. Listenize daha açıklayıcı meta veriler atamak için anahtar-değer çifti yer tutucuları kullanın.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Asıl görüntüleri değil, anahtar-değer çiftleri olarak liste meta verileri ekleyin.
 
7. **Gönder**’i seçin. Listeniz oluşturulur. Yeni liste ile ilişkili olan **kimlik** değerini unutmayın. Diğer görüntü listesi yönetim işlevleri için bu KIMLIĞE ihtiyacınız vardır.

   ![Görüntü listeleri-konsol yanıt içeriği oluştur kutusu liste KIMLIĞINI gösterir](images/try-terms-list-create-2.png)
 
8. Ardından, MyList 'e görüntüler ekleyin. Sol taraftaki menüden **görüntü**' i seçin ve ardından **Görüntü ekle**' yi seçin.

   **Resim ekle** sayfası açılır. 

9. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin.

   ![Görüntü-görüntü sayfası bölge seçimi ekleme](images/test-drive-region.png)

   Görüntü **ekleme** API 'si konsolu açılır.
 
10. **ListId** kutusunda, oluşturduğunuz liste kimliğini girin ve sonra eklemek istediğiniz görüntünün URL 'sini girin. Abonelik anahtarınızı girin ve ardından **Gönder**' i seçin.

11. Görüntünün listeye eklendiğini doğrulamak için, Sol menüdeki **görüntü**' i seçin ve ardından **tüm görüntü kimliklerini al**' ı seçin.

    **Görüntü-tüm görüntü kimliklerini al** API konsolu açılır.
  
12. **ListId** kutusuna liste kimliği ' ni girin ve ardından abonelik anahtarınızı girin. **Gönder**’i seçin.

    ![Görüntü-tüm görüntü kimliklerini al konsol yanıtı içerik kutusu, girdiğiniz görüntüleri listeler](images/try-image-list-create-11.png)
 
10. Birkaç resim daha ekleyin. Özel bir görüntü listesi oluşturduğunuza göre, özel görüntü listesini kullanarak [görüntüleri değerlendirmeyi](try-image-api.md) deneyin. 

## <a name="delete-images-and-lists"></a>Görüntüleri ve listeleri sil

Bir görüntüyü veya listeyi silme işlemi basittir. API 'YI kullanarak aşağıdaki görevleri gerçekleştirebilirsiniz:

- Görüntüyü silin. (**Görüntü-silme**)
- Listeyi silmeden listedeki tüm görüntüleri silin. (**Görüntü-tüm görüntüleri Sil**)
- Listeyi ve tüm içeriğini silme. (**Görüntü listeleri-silme**)

Bu örnek tek bir görüntüyü siler:

1. [Görüntü listesi yönetim API 'si başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)' nda, sol menüsünde **görüntü**' i seçin ve **Sil**' i seçin. 

   **Görüntü silme** sayfası açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

   ![Görüntü-sayfa bölgesi seçimini silme](images/test-drive-region.png)
 
   **Görüntü silme** API 'si konsolu açılır.
 
3. **ListId** kutusuna bir görüntünün SILINECEĞI listenin kimliğini girin.  Bu, MyList için **Image-tüm görüntü kimliklerini al** konsolunda döndürülen sayıdır. Ardından, silinecek görüntünün **ImageID** 'sini girin. 

Örneğimizde, liste KIMLIĞI, **ContentSource**değeri **58953**' dir. Görüntü KIMLIĞI, **ContentID 'ler**için değer **59021**' dir.

1. Abonelik anahtarınızı girin ve ardından **Gönder**' i seçin.

1. Görüntünün silindiğini doğrulamak için görüntü **-tüm görüntü kimliklerini al** konsolunu kullanın.
 
## <a name="change-list-information"></a>Liste bilgilerini değiştir

Bir listenin adını ve açıklamasını düzenleyebilir ve meta veri öğeleri ekleyebilirsiniz.

1. [Görüntü listesi yönetim API 'si başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)' nda, sol menüsünde, **görüntü listeleri**' ni seçin ve ardından **Güncelleştirme ayrıntıları**' nı seçin. 

   **Görüntü listeleri-Güncelleştirme ayrıntıları** sayfası açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin.  

    ![Görüntü listeleri-Güncelleştirme ayrıntıları sayfa bölge seçimi](images/test-drive-region.png)

    **Görüntü listeleri-Güncelleştirme ayrıntıları** API konsolu açılır.
 
3. **ListId** kutusuna liste kimliği ' ni girin ve ardından abonelik anahtarınızı girin.

4. **İstek gövdesi** kutusunda, düzenlemelerinizi yapın ve ardından sayfadaki **Gönder** düğmesini seçin.

   ![Görüntü listeleri-Güncelleştirme ayrıntıları konsolu Istek gövdesi düzenlemeleri](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızla birleştirmek için kodunuzda REST API kullanın veya görüntü ile başlayın [.net hızlı](image-lists-quickstart-dotnet.md) başlangıcı.
