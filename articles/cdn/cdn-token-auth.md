---
title: Azure CDN varlıkları belirteç kimlik doğrulaması ile güvenli hale getirme | Microsoft Docs
description: Belirteç kimlik doğrulaması, Azure CDN varlıklara erişimi güvenli hale getirmek için kullanmayı öğrenin.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: fa71f472294b91baebc2a6075ddb2b50123e545d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593386"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Azure CDN varlıkları belirteç kimlik doğrulaması ile güvenli hale getirme

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Genel Bakış

Belirteç kimlik doğrulaması varlıklar yetkisiz istemcilerine hizmet veren Azure Content Delivery Network (CDN) önlemek izin veren mekanizmadır. Belirteç kimlik doğrulaması önlemek için genellikle yapıldığı *hotlinking* içeriğinin bir ileti panosu gibi farklı bir Web sitesi varlıklarınızı izni olmaksızın kullanır. Hotlinking içerik teslim maliyetlerinizi üzerinde bir etkisi olabilir. CDN'ye içerik teslim önce CDN belirteci kimlik doğrulamasını etkinleştirerek, CDN uç sunucusu tarafından istekler doğrulanır. 

## <a name="how-it-works"></a>Nasıl çalışır?

Belirteç kimlik doğrulaması istekleri ayrı tutma istek sahibinin hakkında bilgi kodlanmış belirteci bir değer içermesi için istekleri gerektirerek tarafından güvenilen bir site oluşturulur doğrular. Yalnızca kodlanmış bilgileri gereksinimlerini karşılıyorsa içerik bir istek sahibine sunulur; Aksi takdirde istek reddedilir. Bir veya daha fazla aşağıdaki parametreleri kullanarak gereksinimleri ayarlayabilirsiniz:

- Ülke: İzin verme veya reddetme tarafından belirtilen ülkeler/bölgeler kaynaklanan istekler kendi [ülke kodu](/previous-versions/azure/mt761717(v=azure.100)).
- URL: Belirtilen varlık veya yol ile eşleşen istekleri izin verir.
- Ana bilgisayar: İzin ver veya istek üst bilgisinde belirtilen konakların kullanan isteklerini reddedin.
- Başvuran: İzin ver veya belirtilen başvuran isteği reddet.
- IP adresi: Belirli bir IP adresi veya IP alt ağı kaynaklanan istekler izin verir.
- Protokol: İzin ver veya içerik istemek için kullanılan protokolü temel istekleri reddet.
- Süre sonu: Bir bağlantı yalnızca sınırlı bir süre boyunca geçerli olmaya devam ettiğinden emin olmak için bir tarih ve saat dilimini atar.

Daha fazla bilgi için her bir parametre için ayrıntılı yapılandırma örneklere bakın [belirteci kimlik doğrulamasını ayarlama](#setting-up-token-authentication).

>[!IMPORTANT] 
> Bu hesapta herhangi bir yol için yetkilendirme belirteci etkinleştirilirse, standart Önbellek modu sorgu dizesini önbelleğe alma için kullanılabilen tek moddur. Daha fazla bilgi için bkz. [Sorgu dizeleri içeren Azure CDN önbelleğe alma davranışını kontrol etme](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Başvuru mimarisi

Aşağıdaki iş akışı diyagramı belirteci kimlik doğrulaması CDN web uygulamanızın üzerinde çalışmak için nasıl kullandığını açıklar.

![CDN belirteç kimlik doğrulama iş akışı](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN uç noktasında belirteç Doğrulama mantığı
    
Aşağıdaki akış çizelgesi, CDN uç noktasında belirteci kimlik doğrulaması yapılandırıldığında, istemci istek nasıl Azure CDN doğrular açıklar.

![CDN belirteç Doğrulama mantığı](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Belirteç kimlik doğrulaması ayarlama

1. Gelen [Azure portalında](https://portal.azure.com), CDN profilinize gidin ve ardından seçin **Yönet** ek Portalı'nı başlatmak için.

    ![CDN profili Yönet düğmesi](./media/cdn-token-auth/cdn-manage-btn.png)

2. Üzerine **HTTP büyük**seçin **belirteç kimlik doğrulaması** içinde açılır. Ardından şifreleme anahtarı ve şifreleme parametreleri aşağıdaki gibi ayarlayabilirsiniz:

   1. Bir veya daha fazla şifreleme anahtarları oluşturun. Bir şifreleme anahtarı, büyük küçük harfe duyarlıdır ve herhangi bir birleşimini alfasayısal karakterler içerebilir. Karakterler, boşluklar dahil başka türde izin verilmez. En fazla 250 karakterdir. Şifreleme anahtarlarınızı rastgele, önerilen kullanarak bunları oluşturursunuz emin olmak için [OpenSSL aracı](https://www.openssl.org/). 

      OpenSSL araç sözdizimi aşağıdaki gibidir:

      ```rand -hex <key length>```

      Örneğin:

      ```OpenSSL> rand -hex 32``` 

      Kapalı kalma süresini önlemek için hem birincil hem de bir yedek anahtarı oluşturun. Birincil anahtarınız güncelleştirildiğinde bir yedek anahtarı içeriğinizi kesintisiz erişim sağlar.
    
   2. Bir benzersiz şifreleme anahtarını girin **birincil anahtar** kutusuna ve isteğe bağlı olarak bir yedek anahtarı girin **yedek anahtar** kutusu.

   3. Her anahtarı için şifreleme en düşük sürümü seçin, **şifreleme ve üstünü** listeleyin ve ardından **güncelleştirme**:
      - **V2**: Anahtar, 2.0 ve 3.0 sürümü belirteçler oluşturmak için kullanılabilir gösterir. Yalnızca bir sürüm 3.0 anahtarına bir eski sürüm 2.0 şifreleme anahtarı geçiş, bu seçeneği kullanın.
      - **V3**: (Önerilen) Anahtarı yalnızca 3.0 sürümü belirteçler oluşturmak için kullanılabilir olduğunu gösterir.

      ![CDN belirteci kimlik doğrulaması kurulum anahtarı](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Şifreleme parametreleri ayarlayın ve bir belirteç oluşturmak için şifreleme aracını kullanın. Şifreleme aracıyla izin verebilir veya sona erme saati, ülke/bölge, başvuran, protokolü ve (herhangi bir birleşimi)'deki istemci IP istekleri göre reddet. Sayı ve bir belirteç oluşturmak için birleştirilebilir parametre birleşimi sınır olmamasına rağmen bir belirteç toplam uzunluğu 512 karakterle sınırlıdır. 

      ![CDN şifreleme aracı](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Bir veya daha fazla aşağıdaki şifreleme parametreleri için değerleri girin **şifrelemek aracı** bölümü: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Parametre adı</th> 
      >   <th>Açıklama</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Bir süre sonra belirtecin süresi dolar işaretine atar. Sona erme zamanı engellenir sonra gönderilen istek sayısı. Bu parametre standart UNIX dönem geçen saniye sayısının temel aldığı bir Unix zaman damgası kullanır `1/1/1970 00:00:00 GMT`. (Çevrimiçi araçları arasında Standart Saati ve Unix zamanından dönüştürmek için kullanabilirsiniz.)> 
      >    Örneğin, süresi dolacak şekilde belirteç istiyorsanız `12/31/2016 12:00:00 GMT`, Unix zaman damgası değeri girin `1483185600`. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Belirli bir varlık veya yolu belirteçleri uyumlu hale getirmenizi sağlar. İstek URL'si, belirli bir göreli yol ile başlatmak için erişimi kısıtlar. URL büyük/küçük harfe duyarlıdır. Her yol virgül ile ayırarak birden fazla yol giriş; alanları eklemeyin. Gereksinimlerinize bağlı olarak, farklı erişim düzeyini sağlamak için farklı değerleri ayarlayabilirsiniz.> 
      >    Örneğin, URL için `http://www.mydomain.com/pictures/city/strasbourg.png`, bu istekler için aşağıdaki giriş değerleri izin verilir: 
      >    <ul>
      >       <li>Giriş değeri `/`: Tüm isteklerine izin verilir.</li>
      >       <li>Giriş değeri `/pictures`, aşağıdaki isteklerine izin verilir: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Giriş değeri `/pictures/`: Yalnızca istekleri içeren `/pictures/` yolu izin verilir. Örneğin: `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Giriş değeri `/pictures/city/strasbourg.png`: Bu belirli bir yol ve varlık için yalnızca isteklerine izin verilir.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Yalnızca bir veya daha fazla belirtilen ülkelerden/bölgelerden kökenli isteklerine izin verir. Tüm diğer ülkeler/bölgeler üzerinden kaynaklanan istekler reddedilir. İki harfli kullanın [3166 ISO ülke kodu](/previous-versions/azure/mt761717(v=azure.100)) her bir ülkede ve her biri bir virgül ile ayırın; boşluk eklemeyin. Örneğin, yalnızca Amerika Birleşik Devletleri ve Fransa erişim izin vermek istiyorsanız, girin `US,FR`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Bir veya daha fazla belirtilen ülkelerden/bölgelerden kaynaklanan istekleri reddeder. Tüm diğer ülkeler/bölgeler üzerinden kaynaklanan isteklerine izin verilir. Uygulama ile aynı olduğu <b>ec_country_allow</b> parametresi. Bir ülke kodu hem de mevcutsa <b>ec_country_allow</b> ve <b>ec_country_deny</b> parametreleri <b>ec_country_allow</b> parametresi önceliklidir.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Yalnızca belirtilen başvuran gelen istekleri sağlar. Bir başvuran istenen kaynağa bağlantılı web sayfasının URL'sini tanımlar. Parametre değeri içinde Protokolü dahil değildir.> 
      >    Aşağıdaki tür giriş izin verilir:
      >    <ul>
      >       <li>Bir ana bilgisayar adı veya bir ana bilgisayar adı ve yolu.</li>
      >       <li>Birden çok başvuran. Birden çok başvuran eklemek için her başvuran virgül ile ayırın; bir alanı eklemeyin. Başvuran bir değer belirtebilirsiniz, ancak başvuran bilgileri tarayıcı yapılandırması nedeniyle istek gönderilmez, istek varsayılan olarak reddedilir.</li> 
      >       <li>İstekleri başvuran bilgileri eksik veya boş. Varsayılan olarak, <b>ec_ref_allow</b> parametresi bu tür istekleri engeller. Bu isteklere izin vermek için "eksik" ya da metni girin veya boş bir değer (sonunda virgül kullanarak) girin.</li> 
      >       <li>Alt etki alanları. Alt etki alanlarını izin vermek için bir yıldız işareti girin (\*). Örneğin, tüm alt etki alanlarına izin vermek için `contoso.com`, girin `*.contoso.com`.</li>
      >    </ul> 
      >    Örneğin, gelen istekleri için erişime izin vermek için `www.contoso.com`, altındaki tüm alt etki alanlarıyla `contoso2.com`, başvuranlar, boş veya eksik isteklerle girin `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Belirtilen başvuran istekleri reddeder. Uygulama ile aynı olduğu <b>ec_ref_allow</b> parametresi. Bir başvuran her ikisinde de mevcutsa <b>ec_ref_allow</b> ve <b>ec_ref_deny</b> parametreleri <b>ec_ref_allow</b> parametresi önceliklidir.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Yalnızca belirtilen protokol gelen isteklere izin verir. Geçerli değerler `http`, `https`, veya `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Belirtilen protokole istekleri reddeder. Uygulama ile aynı olduğu <b>ec_proto_allow</b> parametresi. Bir protokol hem de mevcutsa <b>ec_proto_allow</b> ve <b>ec_proto_deny</b> parametreleri <b>ec_proto_allow</b> parametresi önceliklidir.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Belirtilen sahibinin IP adresine erişimi kısıtlar. IPv4 ve IPv6 desteklenir. Bir tek istek IP adresi ya da belirli bir alt ağ ile ilişkili IP adreslerini belirtebilirsiniz. Örneğin, `11.22.33.0/22` IP adresleri için 11.22.32.1 11.22.35.254 gelen isteklere izin verir.</td>
      > </tr>
      > </table>

   5. Şifreleme parametresi değerleri girmeyi bitirdiğinizde (hem birincil hem de bir yedek anahtarı oluşturduysanız) şifrelemek için bir anahtar seçin **için anahtar şifreleme** listesi.
    
   6. Bir şifreleme sürümden seçin **şifreleme sürümünü** listesi: **V2** sürüm 2 için veya **V3** için sürüm 3 (önerilir). 

   7. Seçin **şifrele** belirteci oluşturabilirsiniz.

      Belirteç oluşturulduktan sonra görüntüleneceğini **oluşturulan belirteç** kutusu. Belirteç kullanmak için URL yolundaki dosyanın sonuna bir sorgu dizesi olarak ekler. Örneğin: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. İsteğe bağlı olarak, belirtecin parametreleri görüntüleyebilmek, belirteç şifre çözme aracı ile test edin. Belirteç değeri olarak yapıştırın **belirteç şifre çözme için** kutusu. Şifreleme anahtarını kullanmak için işaretleyin **anahtarın şifresini çözüp** listeleyin ve ardından **şifresini**.

      Belirteç şifresi sonra parametrelerini görüntülenecek **özgün parametreleri** kutusu.

   9. İsteğe bağlı olarak, bir istek reddedildiğinde, döndürülen yanıt kodu türü özelleştirin. Seçin **etkin**, alınan yanıt kodu seçip **yanıt kodu** listesi. **Üst bilgi adı** otomatik olarak ayarlandığından **konumu**. Seçin **Kaydet** yeni yanıt kodu uygulamak için. Belirli yanıt kodlarını da hata sayfanız URL'sini girmeniz gerekir **üstbilgi değeri** kutusu. **403** yanıt kodu (Yasak), varsayılan olarak seçilidir. 

3. Altında **HTTP büyük**seçin **kurallar altyapısı**. Kural altyapısı özelliği geçerli belirteç kimlik doğrulaması özelliği etkinleştirmek ve ek belirteç kimlik doğrulaması ile ilgili özellikleri etkinleştirmek için yollarını tanımlamak için kullanın. Daha fazla bilgi için [kural altyapısı başvurusu](cdn-rules-engine-reference.md).

   1. Mevcut bir kuralı seçin veya belirteci kimlik doğrulaması uygulamak istediğiniz varlık veya yolunu tanımlamak için yeni bir kural oluşturun. 
   2. Bir kural belirteci kimlik doğrulamasını etkinleştirmek için seçin **[belirteç kimlik doğrulaması](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)** gelen **özellikleri** listeleyin ve ardından **etkin**. Seçin **güncelleştirme** kural güncelleştiriyorsanız veya **Ekle** bir kuralı oluşturuyorsanız.
        
      ![Belirteç kimlik doğrulaması etkin örnek CDN kural altyapısı](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Kural altyapısı ek belirteç kimlik doğrulamayla ilgili özellikler de etkinleştirebilirsiniz. Aşağıdaki özelliklerden herhangi birini etkinleştirmek için seçim **özellikleri** listeleyin ve ardından **etkin**.
    
   - **[Belirteç kimlik doğrulama reddi kod](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)** : Bir istek reddedildiğinde kullanıcıya döndürülür yanıtının türünü belirler. Burada ayarlanan kurallar kümesi'nde yanıt kodu geçersiz kılma **reddi özel işleme** belirteç tabanlı kimlik doğrulama sayfasındaki bölümü.

   - **[Belirteç kimlik doğrulaması, URL çalışması Yoksay](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)** : Belirteci doğrulamak için kullanılan URL büyük küçük harfe duyarlı olup olmadığını belirler.

   - **[Parametre auth token](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)** : İstenen URL'yi görüntülenen belirteci kimlik doğrulaması sorgu dizesi parametresi olarak yeniden adlandırır. 
        
     ![Belirteç kimlik doğrulaması ayarları örneği CDN kural altyapısı](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Kaynak kodunda erişerek belirtecinizi özelleştirebilirsiniz [GitHub](https://github.com/VerizonDigital/ectoken).
   Mevcut diller şunlardır:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN özellikleri ve fiyatlandırmayı sağlayıcısı

Özellikler hakkında daha fazla bilgi için bkz. [Azure CDN ürün özellikleri](cdn-features.md). Fiyatlandırma hakkında daha fazla bilgi için bkz. [Content Delivery Network fiyatlandırması](https://azure.microsoft.com/pricing/details/cdn/).
