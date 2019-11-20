---
title: Azure haritalar arama hizmetini kullanarak bir adres bulma | Microsoft Docs
description: Azure haritalar arama hizmetini kullanarak bir adres arama hakkında bilgi edinin
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9ce6459dd65c75c6fcff5591d4e4667e4b0c75fa
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928486"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Azure haritalar arama hizmetini kullanarak bir adres bulma

Haritalar arama hizmeti, geliştiricilerin adresleri, yerleri, ilgi çekici noktaları, iş listelerini ve diğer coğrafi bilgileri aramasını sağlamak için tasarlanan bir dizi yeniden API 'dir. Hizmet belirli bir adres, çapraz cadde, coğrafi özellik veya ilgi noktası (POı) için bir Enlem/Boylam atar. Arama tarafından döndürülen Enlem ve boylam değerleri, Yönlendirme ve trafik akışı gibi diğer haritalar hizmetlerinde parametre olarak kullanılabilir.

Bu makalede şunları öğreneceksiniz:

* [Benzer arama API 'sini](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) kullanarak adres arayın
* Özellikler ve koordinatlarla birlikte bir adres arayın
* Sokak adresini aramak için [ters adres araması](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) yapın
* [Arama adresi ters açık çapraz API 'yi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet) kullanarak çapraz cadde arama

## <a name="prerequisites"></a>Önkoşullar

Haritalar hizmeti API 'Lerine herhangi bir çağrı yapmak için bir haritalar hesabı ve anahtarı gereklidir. Hesap oluşturma hakkında daha fazla bilgi için hesabı [Yönet](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) bölümündeki yönergeleri izleyin ve hesabınız için birincil bir abonelik anahtarı almak üzere [birincil anahtar al](./tutorial-search-location.md#getkey) bölümündeki adımları izleyin.

Bu makale, REST çağrıları oluşturmak için [Postman uygulamasını](https://www.getpostman.com/apps) kullanır. Tercih ettiğiniz herhangi bir API geliştirme ortamını kullanabilirsiniz.

## <a name="using-fuzzy-search"></a>Benzer arama kullanma

Arama hizmeti için varsayılan API, benzer bir [aramadır](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) ve Kullanıcı girişlerinizin bir arama sorgusu için ne olduğunu bilmediğinizde yararlı olur. API, POı aramasını ve coğrafi kodlamayı kurallı bir ' tek satırlık arama ' olarak birleştirir. Örneğin, API herhangi bir adres veya POı belirteci birleşiminin girişlerini işleyebilir. Ayrıca, bağlamsal bir konum (Lat./Lonla de ağırlıklı olabilir. Çift), bir koordinat ve yarıçap tarafından tam olarak kısıtlanmış veya herhangi bir coğrafi olarak izleme bağlantı noktası olmadan daha genel olarak yürütülürler.

Çoğu arama, performansı elde etmek ve olağandışı sonuçları azaltmak için `maxFuzzyLevel=1` varsayılan olarak sorgular. Bu varsayılan, sorgu parametresi `maxFuzzyLevel=2` veya `3`geçirerek istek başına gerektiğinde geçersiz kılınabilir.

### <a name="search-for-an-address-using-fuzzy-search"></a>Benzer arama kullanarak adres arayın

1. Postman uygulamasını açın, yeni ' ye tıklayın. Yeni oluştur ve **Isteği al**' ı seçin. **Benzer arama**Için bir istek adı girin, kaydedilecek bir koleksiyon veya klasör seçin ve **Kaydet**' e tıklayın.

2. Oluşturucu sekmesinde http **Al** yöntemini SEÇIN ve API uç noktanız IÇIN istek URL 'sini girin.

    ![Benzer arama](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametre | Önerilen değer |
    |---------------|------------------------------------------------|
    | HTTP yöntemi | GET |
    | İstek URL'si | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Yetkilendirme | Kimlik doğrulama yok |

    URL yolundaki **JSON** özniteliği yanıt biçimini belirler. Kullanım kolaylığı ve okunabilirlik için bu makale boyunca JSON kullanıyorsunuz. Kullanılabilir yanıt biçimlerini, [haritalar işlev API 'si başvurusunun](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) **arama için belirsiz** tanımında bulabilirsiniz.

3. **Parametreler**' e tıklayın ve istek URL 'sinde sorgu veya yol parametreleri olarak kullanılacak aşağıdaki anahtar/değer çiftlerini girin:

    ![Benzer arama](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Anahtar | Değer |
    |------------------|-------------------------|
    | api sürümü | 1.0 |
    | abonelik-anahtar | Azure haritalar anahtarınızı \<\> |
    | sorgu | Pizza |

4. **Gönder** ' e tıklayın ve yanıt gövdesini gözden geçirin.

    "Pizza" öğesinin belirsiz sorgu dizesi, "pizza" ve "Restoran" gibi kategoriler içeren 10 [ilgi çekici sonuç](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) sonucu döndürdü. Her sonuç, konum için bir sokak adresi, Enlem/Boylam değerleri, Görünüm bağlantı noktası ve giriş noktaları döndürür.
  
    Sonuçlar, belirli bir başvuru konumuna bağlı değil bu sorgu için farklılaştırmıştır. Yalnızca uygulamanızın kapsama ihtiyacı olan ülkeleri/bölgeleri belirtmek için **ülke kümesi** parametresini kullanabilirsiniz. varsayılan davranış tüm dünyayı aramak, ancak gereksiz sonuçlara yol açabilir.

5. Aşağıdaki anahtar/değer çiftini **params** bölümüne ekleyin ve **Gönder**' e tıklayın:

    | Anahtar | Değer |
    |------------------|-------------------------|
    | countrySet | US |
  
    Sonuçlar artık ülke kodu ile sınırlıdır ve sorgu Birleşik Devletler.
  
    Bir konum için sonuçlar sağlamak üzere bir ilgi alanını sorgulayabilir ve benzer arama hizmeti çağrıdaki döndürülen Enlem ve boylam değerlerini kullanabilirsiniz. Bu durumda, Seattle Space Iğne 'nin konumunu döndürmek ve Lat 'yi kullanbilmeniz için arama hizmetini kullandınız. Lon. aramayı yönlendirmek için değerler.
  
6. Params ' de, aşağıdaki anahtar/değer çiftlerini girin ve **Gönder**' e tıklayın:

    ![Benzer arama](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Anahtar | Değer |
    |-----|------------|
    | lat | 47,620525 |
    | Lon | -122,349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Adres özelliklerini ve koordinatları ara

Arama adresi API 'sine tamamen veya kısmi bir adres geçirebilir ve municipsellik veya alt bölme gibi ayrıntılı adres özelliklerini ve enlem ve Boylam ' nin konumsal değerlerini içeren bir yanıt alabilirsiniz.

1. Postman 'da, **Isteği al** | **yeni istek** ' a tıklayın ve **Adres aramasını**adlandırın.
2. Oluşturucu sekmesinde http **Al** metodunu SEÇIN, API uç noktanız IÇIN istek URL 'sini girin ve varsa bir Yetkilendirme Protokolü seçin.

    ![Adres arama](./media/how-to-search-for-address/address_search_url.png)
  
    | Parametre | Önerilen değer |
    |---------------|------------------------------------------------|
    | HTTP yöntemi | GET |
    | İstek URL'si | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Yetkilendirme | Kimlik doğrulama yok |

3. **Parametreler**' e tıklayın ve istek URL 'sinde sorgu veya yol parametreleri olarak kullanılacak aşağıdaki anahtar/değer çiftlerini girin:
  
    ![Adres arama](./media/how-to-search-for-address/address_search_params.png)
  
    | Anahtar | Değer |
    |------------------|-------------------------|
    | api sürümü | 1.0 |
    | abonelik-anahtar | Azure haritalar anahtarınızı \<\> |
    | sorgu | 400 geniş St, Seattle, WA 98109 |
  
4. **Gönder** ' e tıklayın ve yanıt gövdesini gözden geçirin.
  
    Bu durumda, tüm adres sorgusu belirttiniz ve yanıt gövdesinde tek bir sonuç alacaksınız.
  
5. Params ' de sorgu dizesini aşağıdaki değere düzenleyin:
    ```plaintext
        400 Broad, Seattle
    ```

6. Aşağıdaki anahtar/değer çiftini **params** bölümüne ekleyin ve **Gönder**' e tıklayın:

    | Anahtar | Değer |
    |-----|------------|
    | typeahead | true |

    **Typeahead** bayrağı, adres araması API 'sine sorguyu kısmi giriş olarak ele almasını ve bir tahmine dayalı değerler dizisi döndürmesini söyler.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Ters adres arama kullanarak sokak adresi arama

1. Postman 'da, **Isteği al** | **yeni istek** ' a tıklayın ve **ters adres aramasını**adlandırın.

2. Oluşturucu sekmesinde http **Al** yöntemini SEÇIN ve API uç noktanız IÇIN istek URL 'sini girin.
  
    ![Ters adres arama URL 'SI](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametre | Önerilen değer |
    |---------------|------------------------------------------------|
    | HTTP yöntemi | GET |
    | İstek URL'si | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Yetkilendirme | Kimlik doğrulama yok |
  
3. **Parametreler**' e tıklayın ve istek URL 'sinde sorgu veya yol parametreleri olarak kullanılacak aşağıdaki anahtar/değer çiftlerini girin:
  
    ![Ters adres arama parametreleri](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Anahtar | Değer |
    |------------------|-------------------------|
    | api sürümü | 1.0 |
    | abonelik-anahtar | Azure haritalar anahtarınızı \<\> |
    | sorgu | 47.591180,-122,332700 |
  
4. **Gönder** ' e tıklayın ve yanıt gövdesini gözden geçirin.

    Yanıt, Safeco alanı hakkındaki anahtar adres bilgilerini içerir.
  
5. Aşağıdaki anahtar/değer çiftini **params** bölümüne ekleyin ve **Gönder**' e tıklayın:

    | Anahtar | Değer |
    |-----|------------|
    | number | true |

    [Sayı](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) sorgu parametresi istekle birlikte gönderilirse, yanıt cadde (sol/sağ) ve ayrıca bu numara için de bir konum konumu içerebilir.
  
6. Aşağıdaki anahtar/değer çiftini **params** bölümüne ekleyin ve **Gönder**' e tıklayın:

    | Anahtar | Değer |
    |-----|------------|
    | returnSpeedLimit | true |
  
    [Returnspeedlimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) sorgu parametresi ayarlandığında, gönderilen hız sınırının yanıtı döndürülür.

7. Aşağıdaki anahtar/değer çiftini **params** bölümüne ekleyin ve **Gönder**' e tıklayın:

    | Anahtar | Değer |
    |-----|------------|
    | returnRoadUse | true |

    [Returnroaduse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) sorgu parametresi ayarlandığında yanıt, ters coğrafi olarak açık coğrafi kodlar için yol kullanım dizisini döndürür.

8. Aşağıdaki anahtar/değer çiftini **params** bölümüne ekleyin ve **Gönder**' e tıklayın:

    | Anahtar | Değer |
    |-----|------------|
    | yol kullanımı | true |

    Ters coğrafi kod sorgusunu, [roaduse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) sorgu parametresini kullanarak, belirli bir yol kullanımı türüyle kısıtlayabilirsiniz.
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Ters adres çapraz arama kullanarak çapraz cadde arama

1. Postman 'da, **Isteği al** | **yeni istek** ' a tıklayın ve **çapraz adres çapraz aramasını ters adrese**adlandırın.

2. Oluşturucu sekmesinde http **Al** yöntemini SEÇIN ve API uç noktanız IÇIN istek URL 'sini girin.
  
    ![Ters adres çapraz arama](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametre | Önerilen değer |
    |---------------|------------------------------------------------|
    | HTTP yöntemi | GET |
    | İstek URL'si | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Yetkilendirme | Kimlik doğrulama yok |
  
3. **Parametreler**' e tıklayın ve istek URL 'sinde sorgu veya yol parametreleri olarak kullanılacak aşağıdaki anahtar/değer çiftlerini girin:
  
    | Anahtar | Değer |
    |------------------|-------------------------|
    | api sürümü | 1.0 |
    | abonelik-anahtar | Azure haritalar anahtarınızı \<\> |
    | sorgu | 47.591180,-122,332700 |
  
4. **Gönder** ' e tıklayın ve yanıt gövdesini gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure haritalar arama hizmeti](https://docs.microsoft.com/rest/api/maps/search) API 'si belgelerini inceleyin.
