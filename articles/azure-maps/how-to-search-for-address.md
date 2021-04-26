---
title: Azure haritalar arama hizmetlerini kullanarak bir konum arayın
description: Azure haritalar arama hizmeti hakkında bilgi edinin. Bu API kümesini coğrafi kodlama, ters coğrafi kodlama, benzer aramalar ve çapraz açık aramalar için nasıl kullanacağınızı öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: dddf56edf2037d87a28589a59834db32f8d04a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98598358"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Azure haritalar arama hizmetlerini kullanarak bir konum arayın

[Azure haritalar arama hizmeti](/rest/api/maps/search) , geliştiricilerin adresleri, yerleri ve iş dökümlerini ada, kategoriye ve diğer coğrafi bilgilere göre aramasına yardımcı olmak üzere tasarlanan bir dizi yeniden API 'dir. Geleneksel coğrafi kodlamayı desteklemeye ek olarak, hizmetler Ayrıca coğrafi kod adreslerini ve çapraz Streets 'leri de Latitudes ve Longitudes temelinde ters çevirebilirsiniz. Arama tarafından döndürülen Enlem ve boylam değerleri, [yönlendirme](/rest/api/maps/route) ve [Hava durumu](/rest/api/maps/weather) hizmetleri gibi diğer Azure haritalar hizmetlerinde parametre olarak kullanılabilir.


Bu makalede şunları yapmayı öğreneceksiniz:

* Adres [Arama API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)'sini kullanarak bir adresin (geocode adres konumu) enlem ve boylam koordinatlarını isteyin.
* [Benzer arama API](/rest/api/maps/search/getsearchfuzzy)'sini kullanarak bir adres veya ilgi noktası (POI) arayın.
* Koordinat konumunu cadde adresine çevirmek için [ters adres araması](/rest/api/maps/search/getsearchaddressreverse) yapın.
* Koordinat konumunu, [arama adresi geriye yönelik arama API 'sini](/rest/api/maps/search/getsearchaddressreversecrossstreet)kullanarak, bir insan genelindeki bir merkezden karşılıklı olarak çevirin.  Genellikle, bu, bir cihazdan veya varlıktan GPS akışı alan uygulamaları izlemek ve koordinat nerede bulunduğunu bildirmek için gereklidir.

## <a name="prerequisites"></a>Önkoşullar

1. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
2. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Bu öğretici [Postman](https://www.postman.com/) uygulamasını kullanır, ancak farklı bir API geliştirme ortamı seçebilirsiniz.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Bir adres (coğrafi kodlama) için Enlem ve Boylam iste

Bu örnekte, bir adresi Enlem ve Boylam koordinatlarına dönüştürmek için Azure Maps [arama adresi al API](/rest/api/maps/search/getsearchaddress) 'sini kullanacağız. Bu işleme *coğrafi kodlama* da denir. Bu, koordinatları döndürmenin yanı sıra, cadde, posta kodu, municipsellik ve ülke/bölge bilgileri gibi ayrıntılı adres özelliklerini de döndürür.

>[!TIP]
>Geocode için bir adres kümesine sahipseniz, tek bir API çağrısında bir toplu sorgu göndermek için [arama adresini gönder Batch API](/rest/api/maps/search/postsearchaddressbatch) 'sini kullanabilirsiniz.

1. Postman uygulamasını açın. Postman uygulamasının üst kısmında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **koleksiyon**' ı seçin.  Koleksiyonu adlandırın ve **Oluştur** düğmesini seçin. Bu belgenin geri kalan örnekleri için bu koleksiyonu kullanacaksınız.

2. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki adımda oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

3. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istekte, belirli bir adres için arama yapıyoruz: `400 Braod St, Seattle, WA 98109` . Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Mavi **Gönder** düğmesine tıklayın. Yanıt gövdesinde tek bir konum için veriler yer alacak.

5. Şimdi birden fazla olası konumu olan bir adres arayacağız. **Params** bölümünde `query` anahtarı olarak değiştirin `400 Broad, Seattle` . Mavi **Gönder** düğmesine tıklayın.

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Adres ara":::

6. Sonra, `query` anahtarı olarak ayarlamayı deneyin `400 Broa` .

7. **Gönder** düğmesine tıklayın. Artık yanıtın birden çok ülkeden gelen yanıtları içerdiğini görebilirsiniz. Kullanıcılara yönelik ilgili alana yönelik sonuçları elde etmek için, her zaman istek için mümkün olduğunca fazla konum ayrıntısı ekleyin.

## <a name="using-fuzzy-search-api"></a>Benzer arama API 'sini kullanma

Azure Maps [benzer arama API 'si](/rest/api/maps/search/getsearchfuzzy) , standart tek satırlı ve serbest form aramalarını destekler. Azure haritalar arama isteği için Kullanıcı giriş türünü bilmiyorsanız, benzer API aramasını kullanmanızı öneririz.  Sorgu girişi tam veya kısmi bir adres olabilir. Ayrıca, POI, POı kategorisi veya marka adı gibi bir Ilgi noktası (POı) belirteci de olabilir. Ayrıca, arama sonuçlarınızın uygunluğunu artırmak için, sorgu sonuçları bir koordinat konumu ve yarıçapı tarafından kısıtlanabilir veya bir sınırlayıcı kutu tanımlayarak kısıtlanabilir.

>[!TIP]
>Çoğu arama, performansı elde etmek ve olağandışı sonuçları azaltmak için varsayılan olarak Maxbelirsizlik Zylevel = 1 ' i sorgular. Veya parametrelerini kullanarak belirsizlik düzeylerini ayarlayabilirsiniz `maxFuzzyLevel` `minFuzzyLevel` . Hakkında daha fazla bilgi `maxFuzzyLevel` ve tüm isteğe bağlı parametrelerin tamamen listesi için bkz. [benzer arama URI parametreleri](/rest/api/maps/search/getsearchfuzzy#uri-parameters)

### <a name="search-for-an-address-using-fuzzy-search"></a>Benzer arama kullanarak adres arayın

Bu örnekte, tüm dünyayı aramak için belirsiz arama kullanacağız `pizza` .  Ardından, belirli bir ülkenin kapsamını nasıl arayabileceksiniz. Son olarak, belirli bir alanda arama kapsamını ve döndürülen sonuçların sayısını sınırlamak için koordinat konumunu ve yarıçapını nasıl kullanacağınızı göstereceğiz.

>[!IMPORTANT]
>Kullanıcılar için ilgili alana sonuçları elde etmek için, her zaman mümkün olduğunca fazla konum ayrıntısı ekleyin. Daha fazla bilgi edinmek için bkz. [arama Için En Iyi uygulamalar](how-to-use-best-practices-for-search.md#geobiased-search-results).

1. Postman uygulamasını açın, **Yeni**' ye tıklayın ve **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki bölümde oluşturduğunuz veya yeni bir tane oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >URL yolundaki _JSON_ özniteliği yanıt biçimini belirler. Bu makalede, kullanım kolaylığı ve okunabilirlik için JSON kullanılır. Desteklenen diğer yanıt biçimlerini bulmak için `format` [URI parametresi başvuru belgelerindeki](/rest/api/maps/search/getsearchfuzzy#uri-parameters)parametre tanımına bakın.

3. **Gönder** ' e tıklayın ve yanıt gövdesini gözden geçirin.

    "Pizza" için belirsiz sorgu dizesi, hem "pizza" hem de "Restoran" kategorilerinde 10 [ilgi çekici sonuç](/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) noktası döndürdü. Her sonuç, konum için sokak adresi, enlem ve boylam değerleri, görüntüleme bağlantı noktası ve giriş noktaları gibi ayrıntıları içerir. Sonuçlar artık bu sorgu için farklılaştırmıştır ve herhangi bir başvuru konumuna bağlı değildir.
  
    Bir sonraki adımda, `countrySet` yalnızca uygulamanızın kapsama ihtiyacı olan ülkeleri/bölgeleri belirtmek için parametresini kullanacağız. Desteklenen ülkelerin/bölgelerin tüm listesi için bkz. [arama kapsamı](./geocoding-coverage.md).

4. Varsayılan davranış, büyük olasılıkla gereksiz sonuçlar döndüren dünyanın tamamında arama yapmak için kullanılır. Daha sonra, pizza yalnızca Birleşik Devletler arayacağız. `countrySet`Anahtarı **params** bölümüne ekleyin ve değerini olarak ayarlayın `US` . `countrySet`Anahtarın olarak ayarlanması, `US` sonuçları Birleşik Devletler ile bağlanacaktır.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Birleşik Devletler için pizza araması yapın":::

    Sonuçlar artık ülke kodu ile sınırlıdır ve sorgu Birleşik Devletler.

5. Daha da hedeflenmiş bir arama sağlamak için, bir Lat./lonın kapsamında arama yapabilirsiniz. Koordinat çifti. Bu örnekte, Lat./Lonu kullanacağız. Seattle Space Iğne 'nin. Sonuçları yalnızca 400 ölçüm yarıçapı içinde döndürmek istiyoruz, `radius` parametresini ekleyeceğiz. Ayrıca, `limit` sonuçları en yakın beş pizza yerleştir ile sınırlamak için parametresini ekleyeceğiz.

    **Params** bölümünde aşağıdaki anahtar/değer çiftlerini ekleyin:

     | Anahtar | Değer |
    |-----|------------|
    | [ | 47,620525 |
    | Lon | -122,349274 |
    | La | 400 |
    | limit | 5|

6. **Gönder**’e tıklayın. Yanıt, Seattle Space Iğne 'ye yakın olan pizza restoranlar için sonuçlar içerir.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Ters adres arama kullanarak sokak adresi arama

Azure haritalar [arama adresi al ters API 'si]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , koordinatları insanlar tarafından okunabilir sokak adreslerine çevirir. Bu API genellikle GPS akışlarını kullanan uygulamalar için kullanılır ve belirli koordinat noktalarında adresler bulmayı ister.

>[!IMPORTANT]
>Kullanıcılar için ilgili alana sonuçları elde etmek için, her zaman mümkün olduğunca fazla konum ayrıntısı ekleyin. Daha fazla bilgi edinmek için bkz. [arama Için En Iyi uygulamalar](how-to-use-best-practices-for-search.md#geobiased-search-results).

>[!TIP]
>Geriye doğru coğrafi koda sahip bir koordinat konumları kümesine sahipseniz, tek bir API çağrısında bir toplu sorgu göndermek için [arama sonrası geri çevirme adresi toplu API 'sini](/rest/api/maps/search/postsearchaddressreversebatch) kullanabilirsiniz.

Bu örnekte, kullanılabilir isteğe bağlı parametrelerin birkaçını kullanarak geriye doğru aramalar yapacağız. İsteğe bağlı parametrelerin tam listesi için bkz. [ters arama parametreleri](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

1. Postman uygulamasında **Yeni**' ye tıklayın ve **istek**' ı seçin. İstek için bir **istek adı** girin. İlk bölümde oluşturduğunuz veya yeni bir tane oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin. İstek aşağıdaki URL gibi görünmelidir:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. **Gönder**' e tıklayın ve yanıt gövdesini gözden geçirin. Bir sorgu sonucu görmeniz gerekir. Yanıt, Safeco alanı hakkındaki anahtar adres bilgilerini içerir.
  
4. Şimdi aşağıdaki anahtar/değer çiftlerini **params** bölümüne ekleyeceğiz:

    | Anahtar | Değer | Döndürülenler
    |-----|------------|------|
    | sayı | 1 |Yanıt, cadde (sol/sağ) yanı sıra sayı için de bir konum konumu içerebilir.|
    | returnSpeedLimit | true | Adresteki hız sınırını döndürür.|
    | returnRoadUse | true | Adreste yol kullanım türlerini döndürür. Tüm olası yol kullanım türleri için bkz. [yol kullanım türleri](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).|
    | returnMatchType | true| Eşleşme türünü döndürür. Olası tüm değerler için bkz. [ters adres arama sonuçları](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Ters arama.":::

5. **Gönder**' e tıklayın ve yanıt gövdesini gözden geçirin.

6. Sonra `entityType` anahtarı ekleyecek ve değerini olarak ayarlayacağız `Municipality` . `entityType`Bu anahtar, `returnMatchType` önceki adımda anahtarı geçersiz kılar. Ayrıca `returnSpeedLimit` `returnRoadUse` , municipsellik hakkında bilgi istediğimiz için de öğesini kaldırdık.  Tüm olası varlık türleri için bkz. [varlık türleri](/rest/api/maps/search/getsearchaddressreverse#entitytype).

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Ters entityType 'ı arayın.":::

7. **Gönder**’e tıklayın. Sonuçları 5. adımda döndürülen sonuçlarla karşılaştırın.  İstenen varlık türü artık olduğundan `municipality` , yanıt, sokak adresi bilgisini içermez. Ayrıca, döndürülen `geometryId` Azure haritaları aracılığıyla sınır poligon [arama Çokgen API 'sini](/rest/api/maps/search/getsearchpolygon)almak için de kullanılır.

>[!TIP]
>Bu parametrelerle ilgili daha fazla bilgi edinmek ve diğerleri hakkında bilgi edinmek için, [geriye doğru arama parametreleri bölümüne](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)bakın.

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Ters adres çapraz arama kullanarak çapraz cadde arama

Bu örnekte, bir adresin koordinatları temelinde bir çapraz cadde arayacağız.

1. Postman uygulamasında **Yeni**' ye tıklayın ve **istek**' ı seçin. İstek için bir **istek adı** girin. İlk bölümde oluşturduğunuz veya yeni bir tane oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin. İstek aşağıdaki URL gibi görünmelidir:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Çapraz cadde ara.":::
  
3. **Gönder**' e tıklayın ve yanıt gövdesini gözden geçirin. Yanıtın bir değeri içerdiğini fark edeceksiniz `crossStreet` `South Atlantic Street` .

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure haritalar Arama Hizmeti REST API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Azure haritalar Arama Hizmeti En Iyi Yöntemler](how-to-use-best-practices-for-search.md)