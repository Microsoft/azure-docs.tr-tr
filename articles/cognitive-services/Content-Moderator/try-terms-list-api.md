---
title: Özel terim listeleriyle orta metin-Content Moderator
titleSuffix: Azure Cognitive Services
description: Metin denetleme API 'siyle birlikte kullanmak üzere özel terim listeleri oluşturmak için liste yönetimi API 'sini kullanın.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 828e8ae68286d7c208462d77a31a764427c79637
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755276"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>API konsolundaki Özel terim listeleriyle orta

Azure Content Moderator'daki varsayılan genel terim listesi, içerik moderasyonu ihtiyaçlarının büyük bölümü için yeterlidir. Bununla birlikte, kuruluşunuza özgü terimleri elemek gerekebilir. Örneğin, daha fazla incelemek üzere rakiplerin adlarını etiketlemek isteyebilirsiniz. 

Metin denetleme API 'siyle birlikte kullanmak üzere özel terim listeleri oluşturmak için [liste YÖNETIMI API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) 'sini kullanın. Metin **-ekran** işlemi metninizi küfür için tarar ve ayrıca metni özel ve paylaşılan kara listelerle karşılaştırır.

> [!NOTE]
> Üst sınır, her biri **10.000 terimi aşmamak** kaydıyla **5 listedir**.
>

Aşağıdaki görevleri yapmak için liste yönetimi API 'sini kullanabilirsiniz:
- Liste oluşturma.
- Listeye terimleri ekleme.
- Terimleri listedeki terimlere göre eleme.
- Listeden terim silme.
- Listeyi silme.
- Liste bilgileri düzenleme.
- Listede yapılan değişikliklerin yeni taramaya eklenmesi için dizini yenileyin.

## <a name="use-the-api-console"></a>API konsolunu kullanma

Çevrimiçi konsolda API 'YI test etmeden önce, abonelik anahtarınız olmalıdır. Bu anahtar, **OCP-apim-Subscription-Key** kutusundaki **Ayarlar** sekmesinde bulunur. Daha fazla bilgi için bkz. [Genel Bakış](overview.md).

## <a name="refresh-search-index"></a>Arama dizinini Yenile

Bir terim listesinde değişiklik yaptıktan sonra, gelecekteki taramalara dahil edilecek değişikliklerin dizinini yenilemeniz gerekir. Bu adım, masaüstünüzdeki bir arama altyapısının (etkinse) veya bir Web araması altyapısının, yeni dosya veya sayfaları içerecek şekilde dizinini sürekli olarak yenilediğine benzer.

1. [Liste yönetimi API 'si başvurusunda](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), sol menüsünde, **terim listeleri**' ni seçin ve ardından **Arama dizinini Yenile**' yi seçin. 

   **Liste ara dizini Yenile** sayfası açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

   ![Terim listeleri-arama dizini sayfa bölgesi seçimini Yenile](images/test-drive-region.png)

   **Liste terimi-arama dizinini Yenile** API konsolu açılır.

3. **ListId** kutusuna liste kimliğini girin. Abonelik anahtarınızı girin ve ardından **Gönder**' i seçin.

   ![Terim listeleri API 'SI-arama dizini konsol yanıtı içerik kutusunu Yenile](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Terim listesi oluşturma
1. [Liste YÖNETIMI API başvurusu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)' na gidin. 

   **Listeler-oluştur** sayfası açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

   ![Terim listeleri-sayfa bölgesi seçimi oluşturma](images/test-drive-region.png)

   **Listeler-API konsolu oluşturma terimi** açılır.
 
3. **OCP-apim-Subscription-Key** kutusuna abonelik anahtarınızı girin.

4. **İstek gövdesi** kutusuna **ad** (örneğin, myList) ve **Açıklama**değerlerini girin.

   ![Terim listeleri-konsol Isteği gövdesi adı ve açıklaması oluşturma](images/try-terms-list-create-1.png)

5. Listenize daha açıklayıcı meta veriler atamak için anahtar-değer çifti yer tutucuları kullanın.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Anahtar-değer çiftleri olarak liste meta verileri ekleyin ve gerçek koşullar değildir.
 
6. **Gönder**’i seçin. Listeniz oluşturulur. Yeni liste ile ilişkili olan **kimlik** değerini unutmayın. Diğer terim listesi yönetim işlevleri için bu KIMLIĞE ihtiyacınız vardır.

   ![Terim listeleri-konsol yanıt içeriği oluştur kutusu liste KIMLIĞINI gösterir](images/try-terms-list-create-2.png)
 
7. Koşulları MyList öğesine ekleyin. Sol taraftaki menüde **terim**' ın altında **Terim Ekle**' yi seçin. 

   Terim **ekleme** terimi sayfası açılır. 

8. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

   ![Terim-terim Ekle sayfa bölge seçimi](images/test-drive-region.png)

   Terim **ekleme** API 'si konsolu açılır.
 
9. **ListId** kutusunda, oluşturduğunuz liste kimliğini girin ve **dil**için bir değer seçin. Abonelik anahtarınızı girin ve ardından **Gönder**' i seçin.

   ![Terim-terim konsolu sorgu parametreleri Ekle](images/try-terms-list-create-3.png)
 
10. Terimin listeye eklendiğini doğrulamak için, sol taraftaki menüden **terim**' i seçin ve ardından **tüm terimleri al**' ı seçin. 

    **Terim al tüm terimleri al** API konsolu açılır.

11. **ListId** kutusuna liste kimliği ' ni girin ve ardından abonelik anahtarınızı girin. **Gönder**’i seçin.

12. **Yanıt içeriği** kutusunda, girdiğiniz koşulları doğrulayın.

    ![Terim-tüm terimleri al konsol yanıtı içerik kutusu, girdiğiniz terimleri listeler](images/try-terms-list-create-4.png)
 
13. Birkaç terim daha ekleyin. Artık özel bir terim listesi oluşturduğunuza göre, özel terim listesini kullanarak [bazı metinleri taramayı](try-text-api.md) deneyin. 

## <a name="delete-terms-and-lists"></a>Terimleri ve listeleri silme

Terim veya listeyi silmek basit bir işlemdir. API 'YI kullanarak aşağıdaki görevleri gerçekleştirebilirsiniz:

- Terim silme. (**Terim silme**)
- Listeyi silmeden listedeki tüm terimleri silme. (**Dönem-tüm terimleri Sil**)
- Listeyi ve tüm içeriğini silme. (**Terim listeleri-silme**)

Bu örnek tek bir terimi siler.

1. [Liste yönetimi API 'si başvurusunda](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), sol menüsünde **terim**' i seçin ve ardından **Sil**' i seçin. 

   **-Delete terimi** açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

   ![Terim-sayfa bölgesi seçimini silme](images/test-drive-region.png)

   **Terim silme** API 'si konsolu açılır.
  
3. **ListId** kutusuna bir terim silmek ISTEDIĞINIZ listenin kimliğini girin. Bu KIMLIK, MyList için liste **ayrıntıları** konsolundan döndürülen sayıdır (bizim örneğimizde, **122**). Terimi girin ve bir dil seçin.
 
   ![Terim-konsol sorgu parametrelerini Sil](images/try-terms-list-delete-1.png)

4. Abonelik anahtarınızı girin ve ardından **Gönder**' i seçin.

5. Terimin silindiğini doğrulamak için, **liste koşulları-tüm Konsolu al** ' ı kullanın.

   ![Terim listeleri-tüm konsol yanıtı içerik kutusunu al, terimin silindiğini gösterir](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Liste bilgilerini değiştir

Bir listenin adını ve açıklamasını düzenleyebilir ve meta veri öğeleri ekleyebilirsiniz.

1. [Liste yönetimi API 'si başvurusunda](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), sol menüsünde, **terim listeleri**' ni seçin ve ardından **Güncelleştirme ayrıntıları**' nı seçin. 

   **Liste terimi-Güncelleştirme ayrıntıları** sayfası açılır.

2. **Open API test konsolu**için, konumunuzu en iyi şekilde açıklayan bölgeyi seçin. 

   ![Terim listeleri-Güncelleştirme ayrıntıları sayfası bölge seçimi](images/test-drive-region.png)

   **Liste terimi-Güncelleştirme ayrıntıları** API konsolu açılır.

3. **ListId** kutusuna liste kimliği ' ni girin ve ardından abonelik anahtarınızı girin.

4. **İstek gövdesi** kutusunda, düzenlemelerinizi yapın ve ardından **Gönder**' i seçin.

   ![Terim listeleri-Güncelleştirme ayrıntıları konsolu Istek gövdesi düzenlemeleri](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızla birleştirmek için kodunuzda REST API kullanın veya [.net hızlı](term-lists-quickstart-dotnet.md) başlangıcı ile başlayın.
