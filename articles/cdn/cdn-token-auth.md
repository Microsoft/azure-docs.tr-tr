---
title: Belirteç kimlik doğrulamasıyla Azure CDN varlıkların güvenliğini sağlama | Microsoft Docs
description: Azure CDN varlıklarınıza erişim sağlamak için belirteç kimlik doğrulamasını nasıl kullanacağınızı öğrenin.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 21ef06f37e6840df08b1477f9c0ff24f6e15d1a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95978052"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Belirteç kimlik doğrulamasıyla Azure CDN varlıkların güvenliğini sağlama

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Genel Bakış

Belirteç kimlik doğrulaması, Azure Content Delivery Network (CDN) 'in varlık dışı istemcilere hizmet etmelerini engellemenizi sağlayan bir mekanizmadır. Belirteç kimlik doğrulaması genellikle, bir ileti panosu gibi farklı bir Web sitesinin varlıklarınızı izin olmadan kullandığı, içeriğin *anında bağlanmasını* engellemek için yapılır. Hotlink, içerik teslim maliyetlerinizi etkileyebilir. CDN 'de belirteç kimlik doğrulamasını etkinleştirerek, CDN 'nin içeriği teslim etmeden önce CDN Edge sunucusu tarafından kimlik doğrulaması yapılır. 

## <a name="how-it-works"></a>Nasıl çalışır?

Belirteç kimlik doğrulaması, isteklerin istek sahibi hakkında kodlanmış bilgileri tutan bir belirteç değeri içermesini gerektirerek güvenilen bir site tarafından oluşturulduğunu doğrular. İçerik, yalnızca kodlanan bilgiler gereksinimleri karşılıyorsa istek sahibine sunulur; Aksi takdirde, istekler reddedilir. Aşağıdaki parametrelerden bir veya daha fazlasını kullanarak gereksinimleri ayarlayabilirsiniz:

- Ülke/bölge: [ülke/bölge kodları](/previous-versions/azure/mt761717(v=azure.100))tarafından belirtilen ülkelerden/bölgelerden kaynaklanan Isteklere izin ver veya Reddet.
- URL: yalnızca belirtilen varlık veya yol ile eşleşen isteklere Izin ver.
- Ana bilgisayar: istek üstbilgisinde belirtilen konakları kullanan isteklere Izin ver veya Reddet istekleri.
- Başvuran: belirtilen başvuran için istekleri Izin ver veya Reddet.
- IP adresi: yalnızca belirli bir IP adresinden veya IP alt ağından kaynaklanan isteklere Izin verir.
- Protokol: içeriği istemek için kullanılan protokole göre isteklere Izin ver veya Reddet.
- Sona erme saati: bir bağlantının yalnızca sınırlı bir süre için geçerli kalmasını sağlamak için bir tarih ve zaman aralığı atayın.

Daha fazla bilgi için, [belirteç kimlik doğrulamasını ayarlama](#setting-up-token-authentication)içindeki her parametre için ayrıntılı yapılandırma örneklerine bakın.

>[!IMPORTANT] 
> Bu hesapta herhangi bir yol için belirteç yetkilendirmesi etkinleştirilmişse, sorgu dizesi önbelleğe alma için kullanılabilecek tek mod standart önbellek modu olur. Daha fazla bilgi için bkz. [Sorgu dizeleri içeren Azure CDN önbelleğe alma davranışını kontrol etme](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Başvuru mimarisi

Aşağıdaki iş akışı diyagramı, CDN 'nin Web uygulamanızla birlikte çalışmak için belirteç kimlik doğrulamasını nasıl kullandığını açıklar.

![CDN belirteci kimlik doğrulama iş akışı](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN uç noktasındaki belirteç doğrulama mantığı
    
Aşağıdaki akış çizelgesi, CDN uç noktasında belirteç kimlik doğrulaması yapılandırıldığında Azure CDN istemci isteğini nasıl doğrulayacağını açıklar.

![CDN belirteci doğrulama mantığı](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Belirteç kimlik doğrulamasını ayarlama

1. [Azure Portal](https://portal.azure.com), CDN profilinize gidin ve ardından **Yönet** ' i seçerek ek portalı başlatın.

    ![CDN profili Yönet düğmesi](./media/cdn-token-auth/cdn-manage-btn.png)

2. **Http büyük** üzerine gelin, ardından açılır menü kutusunda **belirteç kimlik doğrulaması** ' nı seçin. Daha sonra şifreleme anahtarını ve şifreleme parametrelerini aşağıdaki gibi ayarlayabilirsiniz:

   1. Bir veya daha fazla şifreleme anahtarı oluşturun. Şifreleme anahtarı büyük/küçük harfe duyarlıdır ve alfasayısal karakterlerin herhangi bir birleşimini içerebilir. Boşluk da dahil olmak üzere diğer karakter türlerine izin verilmez. En fazla 250 karakter uzunluğunda olur. Şifreleme anahtarlarınızın rastgele olduğundan emin olmak için, [OpenSSL aracını](https://www.openssl.org/)kullanarak bunları oluşturmanız önerilir. 

      OpenSSL aracı aşağıdaki sözdizimine sahiptir:

      ```rand -hex <key length>```

      Örnek:

      ```OpenSSL> rand -hex 32``` 

      Kapalı kalma süresini önlemek için hem birincil hem de bir yedekleme anahtarı oluşturun. Bir yedekleme anahtarı, birincil anahtarınız güncelleştirilirken içeriğinize kesintisiz erişim sağlar.
    
   2. **Birincil anahtar** kutusuna benzersiz bir şifreleme anahtarı girin ve isteğe bağlı olarak **yedekleme anahtarı** kutusuna bir yedekleme anahtarı girin.

   3. **En düşük şifreleme sürümü** listesinden her anahtar için en düşük şifreleme sürümünü seçin ve ardından **Güncelleştir**' i seçin:
      - **V2**: anahtarın sürüm 2,0 ve 3,0 belirteçleri oluşturmak için kullanılabileceğini gösterir. Bu seçeneği yalnızca eski sürüm 2,0 şifreleme anahtarından sürüm 3,0 anahtarına geçiş yapıyorsanız kullanın.
      - **V3**: (önerilir), anahtarın yalnızca sürüm 3,0 belirteçleri oluşturmak Için kullanılabileceğini gösterir.

      ![CDN belirteci kimlik doğrulama kurulum anahtarı](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Şifreleme parametrelerini ayarlamak ve bir belirteç oluşturmak için şifreleme aracını kullanın. Şifreleme Aracı ile, süresi sona erme saati, ülke/bölge, başvuran, protokol ve istemci IP 'si (herhangi bir bileşimde) temelinde istekleri izin verebilir veya reddedebilirsiniz. Belirteç oluşturmak için birleştirilebilecek parametrelerin sayısı ve birleşimine yönelik bir sınır olmasa da, belirtecin toplam uzunluğu 512 karakterle sınırlıdır. 

      ![CDN şifreleme aracı](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Şifreleme **Aracı** bölümünde aşağıdaki şifreleme parametrelerinden bir veya daha fazlası için değerler girin: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Parametre adı</th> 
      >   <th>Açıklama</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Belirtecin süresi dolduktan sonra belirtece bir süre sonu süresi atar. Süre sonu zamanından sonra gönderilen istekler reddedilir. Bu parametre, standart Unix dönemi sonrasındaki saniye sayısına bağlı olarak bir UNIX zaman damgası kullanır `1/1/1970 00:00:00 GMT` . (Standart saat ve UNIX saati arasında dönüştürme yapmak için çevrimiçi araçları kullanabilirsiniz.)> 
      >    Örneğin, belirtecin süre içinde dolacağını istiyorsanız, `12/31/2016 12:00:00 GMT` Unix zaman damgası değerini girin `1483185600` . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Belirteçleri belirli bir varlığa veya yola uyarlamanızı sağlar. URL 'SI belirli bir göreli yol ile başlayan isteklere erişimi kısıtlar. URL 'Ler büyük/küçük harfe duyarlıdır. Her yolu virgülle ayırarak birden çok yol girin; boşluk eklemeyin. Gereksinimlerinize bağlı olarak, farklı erişim düzeyi sağlamak için farklı değerler ayarlayabilirsiniz.> 
      >    Örneğin, URL için `http://www.mydomain.com/pictures/city/strasbourg.png` , bu isteklere aşağıdaki giriş değerleri için izin verilir: 
      >    <ul>
      >       <li>Giriş değeri `/` : tüm isteklere izin verilir.</li>
      >       <li>Giriş değeri `/pictures` , aşağıdaki isteklere izin verilir: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Giriş değeri `/pictures/` : yalnızca yolu içeren isteklere `/pictures/` izin verilir. Örneğin, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Giriş değeri `/pictures/city/strasbourg.png` : yalnızca bu belirli yol ve varlık için isteklere izin verilir.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Yalnızca bir veya daha fazla belirtilen ülkede/bölgeden kaynaklı isteklere izin verir. Diğer tüm ülkelerden/bölgelerden kaynaklanan istekler reddedilir. Her ülke/bölge için iki harfli [ıso 3166 ülke/bölge kodu](/previous-versions/azure/mt761717(v=azure.100)) kullanın ve her birini virgülle ayırın; boşluk eklemeyin. Örneğin, yalnızca Birleşik Devletler ve Fransa 'dan erişime izin vermek istiyorsanız, girin `US,FR` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Belirtilen bir veya daha fazla ülke/bölgeden kaynaklanan istekleri reddeder. Diğer tüm ülkelerden/bölgelerden kaynaklı isteklere izin verilir. Uygulama <b>ec_country_allow</b> parametresiyle aynıdır. Hem <b>ec_country_allow</b> hem de <b>ec_country_deny</b> parametrelerinde bir ülke/bölge kodu varsa <b>ec_country_allow</b> parametresi önceliklidir.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Yalnızca belirtilen başvuran isteklere izin verir. Başvuran, istenen kaynakla bağlantılı web sayfasının URL 'sini tanımlar. Protokolü parametre değerine eklemeyin.> 
      >    Aşağıdaki giriş türlerine izin verilir:
      >    <ul>
      >       <li>Ana bilgisayar adı veya ana bilgisayar adı ve yolu.</li>
      >       <li>Birden çok başvuranlar. Birden çok başvura eklemek için, her bir başvuran virgülle ayırın; boşluk eklemeyin. Bir başvuran değeri belirtirseniz, ancak tarayıcı yapılandırması nedeniyle istek içinde başvuran bilgileri gönderilmezse, istek varsayılan olarak reddedilir.</li> 
      >       <li>Eksik veya boş başvuran bilgileri olan istekler. Varsayılan olarak, <b>ec_ref_allow</b> parametresi bu istek türlerini engeller. Bu isteklere izin vermek için, "eksik" metnini girin veya boş bir değer girin (sondaki virgül kullanarak).</li> 
      >       <li>Alt. Alt etki alanlarına izin vermek için bir yıldız işareti ( \* ) girin. Örneğin, tüm alt etki alanlarına izin vermek için `contoso.com` , girin `*.contoso.com` .</li>
      >    </ul> 
      >    Örneğin, içindeki isteklerin `www.contoso.com` , içindeki tüm alt etki alanları `contoso2.com` ve boş veya eksik başvuranlar içeren istekler için erişime izin vermek için girin `www.contoso.com,*.contoso.com,missing` .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Belirtilen başvuran istekleri reddeder. Uygulama <b>ec_ref_allow</b> parametresiyle aynıdır. Hem <b>ec_ref_allow</b> hem de <b>ec_ref_deny</b> parametrelerinde bir başvuran varsa <b>ec_ref_allow</b> parametresi önceliklidir.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Yalnızca belirtilen protokoldeki isteklere izin verir. Geçerli değerler `http` , `https` , veya `http,https` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Belirtilen protokolden gelen istekleri reddeder. Uygulama <b>ec_proto_allow</b> parametresiyle aynıdır. Bir protokol hem <b>ec_proto_allow</b> hem de <b>ec_proto_deny</b> parametrelerinde mevcutsa <b>ec_proto_allow</b> parametresi önceliklidir.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Belirtilen istek sahibinin IP adresine erişimi kısıtlar. Hem ıPV4 hem de ıPV6 desteklenir. Tek bir istek IP adresi veya belirli bir alt ağla ilişkili IP adresleri belirtebilirsiniz. Örneğin, `11.22.33.0/22` 11.22.32.1 IP adreslerinden gelen isteklerin 11.22.35.254 'e erişmesine izin verir.</td>
      > </tr>
      > </table>

   5. Şifreleme parametre değerlerini girmeyi bitirdikten sonra, **şifrelemek Için anahtardan** bir anahtar seçin (hem birincil hem de bir yedek anahtar oluşturduysanız).
    
   6. **Şifreleme sürümü** listesinden bir şifreleme sürümü seçin: sürüm 2 için **v2** veya sürüm 3 için **v3** (önerilir). 

   7. Belirteci oluşturmak için **şifreleyin** ' ı seçin.

      Belirteç oluşturulduktan sonra, **üretilen belirteç** kutusunda görüntülenir. Belirteci kullanmak için, URL yolunuzda dosyanın sonuna bir sorgu dizesi olarak ekleyin. Örneğin, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. İsteğe bağlı olarak, belirtecinizin parametrelerini görüntüleyebilmeniz için, şifre çözme aracıyla belirtecinizi test edin. Belirteç değerini **şifre çözme** kutusuna yapıştırın. **Şifresini çözmek Için anahtardan** kullanılacak şifreleme anahtarını seçin ve ardından **şifre çöz**' ü seçin.

      Belirtecin şifresi çözülmediğinde, parametreleri **özgün parametreler** kutusunda görüntülenir.

   9. İsteğe bağlı olarak, bir istek reddedildiğinde döndürülen yanıt kodu türünü özelleştirin. **Etkin**' i seçin, ardından **Yanıt** kodu listesinden yanıt kodunu seçin. **Üstbilgi adı** otomatik olarak **konum** olarak ayarlanır. Yeni yanıt kodunu uygulamak için **Kaydet** ' i seçin. Belirli yanıt kodları için **üstbilgi değeri** kutusuna hata sayfanızın URL 'sini de girmeniz gerekir. **403** yanıt kodu (yasak) varsayılan olarak seçilidir. 

3. **Http büyük** altında **Rules Engine**' i seçin. Özelliği uygulamak için yollar tanımlamak, belirteç kimlik doğrulaması özelliğini etkinleştirmek ve belirteç kimlik doğrulaması ile ilgili ek özellikleri etkinleştirmek için Rules altyapısını kullanın. Daha fazla bilgi için bkz. [kural altyapısı başvurusu](./cdn-verizon-premium-rules-engine-reference.md).

   1. Belirteç kimlik doğrulaması uygulamak istediğiniz varlığı veya yolu tanımlamak için mevcut bir kuralı seçin veya yeni bir kural oluşturun. 
   2. Bir kuralda belirteç kimlik doğrulamasını etkinleştirmek için, **Özellikler** listesinden **[belirteç](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm)** kimlik doğrulaması ' nı seçin ve ardından **etkin**' i seçin. Bir kuralı **güncelleştiriyorsanız veya bir kural oluşturuyorsanız,** **Güncelleştir** ' i seçin.
        
      ![CDN kuralları altyapı belirteci kimlik doğrulama etkinleştirme örneği](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Kurallar altyapısında, ek belirteç kimlik doğrulaması ile ilgili özellikleri de etkinleştirebilirsiniz. Aşağıdaki özelliklerden herhangi birini etkinleştirmek için, **Özellikler** listesinden seçin ve ardından **etkin**' i seçin.
    
   - **[Belirteç kimlik doğrulaması reddi kodu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm)**: bir istek reddedildiğinde kullanıcıya döndürülen yanıtın türünü belirler. Burada ayarlanan kurallar, belirteç tabanlı kimlik doğrulama sayfasındaki **özel olarak reddetme** bölümünde ayarlanan yanıt kodunu geçersiz kılar.

   - **[Belirteç kimlik doğrulaması URL 'Sini yoksay durum](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm)**: belirteci doğrulamak IÇIN kullanılan URL 'nin büyük/küçük harfe duyarlı olup olmadığını belirler.

   - **[Belirteç kimlik doğrulaması parametresi](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm)**: istenen URL 'de görünen belirteç kimlik doğrulama sorgu dizesi parametresini yeniden adlandırır. 
        
     ![CDN kuralları altyapı belirteci kimlik doğrulama ayarları örneği](./media/cdn-token-auth/cdn-rules-engine2.png)

5. [GitHub](https://github.com/VerizonDigital/ectoken)'da kaynak koduna erişerek belirtecinizi özelleştirebilirsiniz.
   Kullanılabilir diller şunları içerir:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN özellikleri ve sağlayıcı fiyatlandırması

Özellikler hakkında bilgi için bkz. [Azure CDN ürün özellikleri](cdn-features.md). Fiyatlandırma hakkında bilgi için bkz. [Content Delivery Network fiyatlandırması](https://azure.microsoft.com/pricing/details/cdn/).