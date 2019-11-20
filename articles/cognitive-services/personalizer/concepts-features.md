---
title: 'Özellikler: eylem ve bağlam kişiselleştirici'
titleSuffix: Azure Cognitive Services
description: Kişiselleştirici, daha iyi derecelendirme önerileri sağlamak için özellikleri, Eylemler ve bağlam hakkındaki bilgileri kullanır. Özellikler çok genel veya bir öğeye özgü olabilir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 9a7599cd71c087201b54c594954a6fff377b3e45
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490771"
---
# <a name="features-are-information-about-actions-and-context"></a>Özellikler, Eylemler ve bağlamla ilgili bilgiler

Kişiselleştirici hizmeti, uygulamanızın belirli bir bağlamdaki kullanıcılara ne göstermesi gerektiğini öğrenerek işe yarar.

Kişiselleştirici, en iyi **eylemi**seçmek için **geçerli bağlamla** ilgili bilgiler olan **özellikleri**kullanır. Özellikler, daha yüksek ödüller elde etmek için kişiselleştirmenize yardımcı olabilecek tüm bilgileri temsil eder. Özellikler çok genel veya bir öğeye özgü olabilir. 

Örneğin, şu **özelliklere** sahip olabilirsiniz:

* `Sports_Shopper`gibi _Kullanıcı_ . Bu, tek bir kullanıcı KIMLIĞI olmamalıdır. 
* Bir video `Documentary`, bir `Movie`veya `TV Series`ya da bir perakende öğesinin mağazada kullanılabilir olup olmadığı gibi _içerikler_ .
* Haftanın günün saati gibi _geçerli_ süre.

Kişiselleştirici, Eylemler ve bağlam için göndereceğiniz özellikleri etkilemez, sınırlamaz veya düzelmez:

* Bazı eylemler için bazı özellikler gönderebilirsiniz, bunlar yoksa diğerleri için değildir. Örneğin, TV serisinde filmlerin olmayan öznitelikleri olabilir.
* Bazı özelliklerinizin yalnızca birkaç kez kullanılabilir olması olabilir. Örneğin, bir mobil uygulama bir Web sayfasından daha fazla bilgi sağlayabilir. 
* Zamanla, bağlam ve eylemler hakkında özellikler ekleyebilir ve kaldırabilirsiniz. Kişiselleştirici, kullanılabilir bilgilerden öğrenmeye devam ediyor.
* Bağlam için en az bir özellik olmalıdır. Kişiselleştirici boş bir bağlamı desteklemiyor. Her seferinde yalnızca sabit bir bağlam gönderirseniz, kişiselleştirici yalnızca eylemlerdeki özelliklerle ilgili olarak, derecelendiriciye yönelik eylemi seçer.
* Kategorik özellikler için olası değerleri tanımlamanız gerekmez ve sayısal değerler için aralıkları önceden tanımlamanız gerekmez.

## <a name="supported-feature-types"></a>Desteklenen özellik türleri

Kişiselleştirici dize, sayısal ve Boole türlerinin özelliklerini destekler.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Özellik türü seçimi, kişiselleştirici içinde Machine Learning nasıl etkiler

* **Dizeler**: dize türleri için, her anahtar ve değerin birleşimi, kişiselleştirici makine öğrenimi modelinde yeni ağırlıklar oluşturur. 
* **Sayısal**: sayı, kişiselleştirme sonucunu orantılı bir şekilde etkiliyorsa sayısal değerleri kullanmanız gerekir. Bu, çok senaryoya bağımlıdır. Basitleştirilmiş bir örnekte, örneğin, bir perakende deneyimini kişiselleştirmek için, 2 veya 3 pelleri olan kişilerin kişiselleştirme sonucunu iki kez etkilemesini veya 1 Evcil hayvan 'yı büyük ölçüde etkilemesini isteyebileceğiniz için sayısal olan bir özellik olabilir. Sayısal birimleri temel alan, ancak anlamı yaş, sıcaklık veya kişi yüksekliği gibi doğrusal olmayan, en iyi dize olarak kodlanan ve özellik kalitesi genellikle aralıklar kullanılarak iyileştirilen özelliklerdir. Örneğin, Yaş "Age" olarak kodlanıp: "0-5", "Age": "6-10", vb.
* "False" değeri ile gönderilen **Boole** değerleri, hiç gönderilmemiş gibi davranır.

Mevcut olmayan özellikler istekten atlanmalıdır. Model eğitimi yaparken, bir null değeri olan ve bir "null" değeri ile işlenen özellikler göndermekten kaçının.

## <a name="categorize-features-with-namespaces"></a>Özellikleri ad alanları ile kategorilere ayırma

Kişiselleştirici, ad alanları halinde düzenlenmiş özellikleri alır. Uygulamanızda, ad alanları kullanılıyorsa ve bunların ne olması gerektiğini belirlersiniz. Ad alanları, benzer bir konuyla ilgili özellikleri veya belirli bir kaynaktan gelen özellikleri gruplandırmak için kullanılır.

Uygulamalar tarafından kullanılan özellik ad alanı örnekleri aşağıda verilmiştir:

* User_Profile_from_CRM
* Zaman
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* Userdeviceınfo
* Hava durumu
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Özellik ad alanlarını, geçerli JSON anahtarları oldukları sürece kendi kurallarınızı izleyerek adlandırın. Ad alanları, özellikleri farklı kümeler halinde düzenlemek ve benzer adlarla özellikleri ortadan kaldırmak için kullanılır. Ad alanlarını, özellik adlarına eklenen bir ' önek ' olarak düşünebilirsiniz. Ad alanları iç içe olamaz.


Aşağıdaki JSON 'da, `user`, `state`ve `device` Özellik ad uzaylardır. 

> [!Note]
> Şu anda UTF-8 tabanlı özellik ad alanları için adların kullanılması ve farklı harfler ile başlaması önemle önerilir. Örneğin, `user`, `state`ve `device` `u`, `s`ve `d`ile başlayın. Şu anda aynı ilk karakterlerle ad alanları olması makine öğreniminde kullanılan dizinlerde çakışmaları ortaya çıkmasına neden olabilir.

JSON nesneleri, iç içe geçmiş JSON nesnelerini ve basit özellik/değerleri içerebilir. Bir dizi yalnızca dizi öğeleri sayı ise dahil edilebilir. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Ad alanları için karakter kümelerinde kısıtlamalar

Ad alanını adlandırırken kullandığınız dize bazı kısıtlamalara uymalıdır: 
* Unicode olamaz.
* Ad alanı adları için < 256 kodlu bazı yazdırılabilir sembolleri kullanabilirsiniz. 
* Sembolleri < 32 (yazdırılamaz), 32 (boşluk), 58 (iki nokta), 124 (pipe) ve 126 – 140 kodlarla kullanamazsınız.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Özellik kümelerini kişiselleştirici için nasıl daha etkili hale getirme

İyi bir özellik kümesi, kişiselleştirmenin, en yüksek ödül sağlayacak eylemi nasıl önleyeceğinizi öğrenmenize yardımcı olur. 

Aşağıdaki önerileri takip eden, kişiselleştirici derecelendirme API 'sine Özellikler göndermeyi göz önünde bulundurun:

* Sürücü kişiselleştirmesi için yeterli özellik vardır. İçeriğin daha kesin bir şekilde hedeflenmiş olması gerekir, daha fazla özellik gereklidir.

* Çeşitli *siteler*için yeterli özellikler vardır. Birçok öğe birkaç demette gruplandırılmışsa özellik *yoğun* olur. Örneğin, binlerce video "Long" (5 dakikadan fazla) ve "Short" (5 dakikalık bir süre altında) olarak sınıflandırılabilirler. Bu *çok yoğun* bir özelliktir. Diğer taraftan, aynı değere sahip olan binlerce öğe "title" adlı bir özniteliğe sahip olabilir, bu da neredeyse hiçbir şekilde bir öğeden diğerine benzer. Bu çok yoğun olmayan veya *seyrek* bir özelliktir.  

Yüksek yoğunluklu özelliklerin olması, kişiselleştirmeye en geç öğrenmeyi bir öğeden diğerine kadar ortadan kaldırmanıza yardımcı olur. Ancak, yalnızca birkaç özellik varsa ve bunlar çok yoğun ise, kişiselleştirici, içeriği yalnızca birkaç demetle tam olarak hedeflemek üzere çalışır.

### <a name="improve-feature-sets"></a>Özellik kümelerini geliştirme 

Çevrimdışı bir değerlendirme gerçekleştirerek Kullanıcı davranışını çözümleyin. Bu, ne kadar çok daha fazla katkıda bulunanlara göre olumlu bir ölçüde daha fazla katkı olduğunu görmek için geçmiş verileri görmenizi sağlar. Hangi özelliklerin yardımcı olduğunu görebilirsiniz ve daha da çok daha fazla bilgi edinmek için kişiselleştirmeye gönderilmek üzere daha iyi özellikler bulabilirsiniz.

Aşağıdaki bölümler, Kişiselleştiriciye gönderilen özellikleri iyileştirmeye yönelik yaygın uygulamalardan oluşur.

#### <a name="make-features-more-dense"></a>Özellikleri daha yoğun hale getirme

Özellik kümelerinizi daha büyük ve daha fazla veya daha az yoğun hale getirmek üzere düzenleyerek geliştirmek mümkündür.

Örneğin, ikincisine doğru bir zaman damgası çok seyrek bir özelliktir. Süreleri "sabah", "Orta gün", "öğleden sonra" vb. sınıflandırarak daha yoğun (etkili) hale getirilebilir.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Extrapotildi Information ile özellik kümelerini Genişlet

Ayrıca, zaten sahip olduğunuz bilgilerden türetilebilen keşfedilmemiş öznitelikleri düşünerek daha fazla özellik elde edebilirsiniz. Örneğin, kurgusal bir film listesi kişiselleştirmesi sırasında, bir hafta içi iş gününe ait farklı bir çalışma gününe ait farklı bir davranış olabilir mi? Saat, "hafta sonu" veya "hafta içi" özniteliğine sahip olacak şekilde genişletilebilir. Ulusal kültürel Uluslararası tatiller, belirli film türlerine göre ilgilensin mi? Örneğin, "Cadılar Bayramı" özniteliği ilgili olduğu yerlerde faydalıdır. Rainy Hava durumu, birçok kişiye ait bir film seçimine önemli bir etkiye sahip olabilir mi? Zaman ve yerde, bir hava durumu hizmeti bu bilgileri sağlayabilir ve ek bir özellik olarak ekleyebilirsiniz. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Yapay zeka ve bilişsel hizmetler ile özellik kümelerini genişletme

Yapay zeka ve çalıştırmaya hazırlanma bilişsel hizmetler, Kişiselleştiriciye çok güçlü bir ekleme olabilir. 

Yapay zeka hizmetlerini kullanarak öğelerinizi ön işlemden yararlanarak, kişiselleştirmeyle ilgili olabilecek olası bilgileri otomatik olarak ayıklayabilirsiniz.

Örneğin:

* Sahne öğelerini, metni, yaklaşımı ve diğer birçok özniteliği ayıklamak için [video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) aracılığıyla bir film dosyası çalıştırabilirsiniz. Bu öznitelikler daha sonra, özgün öğe meta verilerinde bulunmayan özellikleri yansıtacak şekilde daha yoğun hale getirilebilir. 
* Görüntüler, nesne algılama, yaklaşım, yüz ve benzeri işlemler aracılığıyla çalıştırılabilir.
* Metindeki bilgiler, varlıklar ayıklanarak, yaklaşım, Bing bilgi Graf ile varlıkları genişleterek, vb. genişletilebilir.

Gibi diğer birçok Azure bilişsel [hizmeti](https://www.microsoft.com/cognitive-services)kullanabilirsiniz.

* [Varlık bağlama](../entitylinking/home.md)
* [Metin Analizi](../text-analytics/overview.md)
* [Duygu tanıma](../emotion/home.md)
* [Görüntü İşleme](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Eylemler bir seçenek listesini temsil eder

Her eylem:

* _Olay_ kimliği vardır. Zaten bir olay KIMLIĞINIZ varsa, bunu göndermeniz gerekir. Bir olay KIMLIĞINIZ yoksa, bir tane göndermeyin, kişiselleştirici sizin için bir tane oluşturur ve bu dosyayı sıralama isteğinin yanıtında döndürür. KIMLIK, Kullanıcı değil, sıralama olayı ile ilişkilendirilir. Bir KIMLIK oluşturursanız, bir GUID en iyi şekilde kullanılır. 
* Özelliklerin bir listesini içerir.
* Özellikler listesi büyük (yüzlerce) olabilir, ancak bir yandan elde etmeyen özellikleri kaldırmak için özellik verimliliğini değerlendirmeyi öneririz. 
* **Eylemlerdeki** özellikler, kişiselleştirici tarafından kullanılan **bağlamdaki** özelliklerle bağıntılı olabilir veya olmayabilir.
* Eylemler için özellikler bazı eylemlerde bulunabilir ve başkaları tarafından kullanılamaz. 
* Belirli bir eylem KIMLIĞI için özellikler bir gün kullanılabilir, ancak daha sonra kullanılamaz hale gelir. 

Kararlı özellik kümeleri olduğunda, kişiselleştirici 'ın makine öğrenimi algoritmaları daha iyi gerçekleştirilir, ancak özellik kümesi zaman içinde değişirse derecelendirme çağrıları başarısız olur.

İşlemleri derecelendirme sırasında 50 ' den fazla eylem gönderme. Bunlar her seferinde aynı 50 eylem olabilir, ya da değişebilir. Örneğin, bir e-ticaret uygulaması için 10.000 öğe kataloğuna ait bir Ürün kataloğunuz varsa, bir müşterinin en iyi 40 ' i belirleyebilmek için bir öneri veya filtreleme motoru kullanabilir ve en iyi şekilde ilgilenmek için kişiselleştirici kullanabilirsiniz (örneğin, , Kullanıcı, geçerli bağlam için sepete eklenecektir).


### <a name="examples-of-actions"></a>Eylem örnekleri

Derecelendirme API 'sine göndereceğiniz eylemler, kişiselleştirmeye çalıştığınız işlemlere göre değişir.

İşte bazı örnekler:

|Amaç|Eylem|
|--|--|
|Bir haber web sitesinde vurgulanan makaleyi kişiselleştirin.|Her eylem olası bir haber makalelidir.|
|Web sitesinde ad yerleşimini iyileştirin.|Her eylem, reklamlar için bir düzen (örneğin, en üstte, küçük görüntüler, büyük görüntüler) oluşturmak için bir düzen veya kurallar olacaktır.|
|Bir alışveriş web sitesinde önerilen öğelerin kişiselleştirilmiş sıralamasını görüntüleyin.|Her eylem, belirli bir üründür.|
|Belirli bir fotoğrafta uygulanacak filtreler gibi kullanıcı arabirimi öğelerini önerin.|Her eylem farklı bir filtre olabilir.|
|Kullanıcı amacını açıklamak veya bir eylem önermek için bir sohbet bot 'un yanıtını seçin.|Her eylem, yanıtın nasıl yorumlanacağı konusunda bir seçenektir.|
|Arama sonuçları listesinin en üstünde neyin gösterileceğini seçin|Her eylem, en çok birkaç arama sonuçlarından biridir.|


### <a name="examples-of-features-for-actions"></a>Eylemlere yönelik özellik örnekleri

Eylemler için özelliklerin iyi örnekleri aşağıda verilmiştir. Bunlar her bir uygulamaya göre değişir.

* Eylemlerin özelliklerine sahip özellikler. Örneğin, bu bir film ya da TV serisi mi?
* Kullanıcıların geçmişte bu eylemle nasıl etkileşim albileceğini gösteren Özellikler. Örneğin, bu film çoğu zaman demografik A veya B 'deki kişiler tarafından görülebildiğinden, genellikle birden fazla kez yürütüyordur.
* Kullanıcının eylemleri nasıl *gördüğü* özellikleriyle ilgili özellikler. Örneğin, küçük resimde gösterilen filmin posterleri yüz, araba veya landscapes içeriyor mu?

### <a name="load-actions-from-the-client-application"></a>İstemci uygulamasından eylemleri yükleme

Eylemlerden özellikler genellikle içerik yönetim sistemlerinden, kataloglarından ve öneren sistemlerinden gelebilir. Uygulamanız, sahip olduğunuz ilgili veritabanlarından ve sistemlerden eylemlerle ilgili bilgileri yüklemekten sorumludur. Eylemleriniz, performans üzerinde gereksiz bir etkiye sahip olmak üzere değişiklik veya yüklenmeme durumunda bu bilgileri önbelleğe almak için uygulamanıza Logic ekleyebilirsiniz.

### <a name="prevent-actions-from-being-ranked"></a>Eylemlerin derecelendirmasını önleme

Bazı durumlarda, kullanıcılara görüntülenmesini istemediğiniz eylemler vardır. Bir eylemin en üst olarak derecelendirmasını önlemenin en iyi yolu, bunu ilk yerde bulunan işlem API 'sine eylem listesine dahil etmez.

Bazı durumlarda, yalnızca bir derecelendirme API çağrısının sonuç _eylemi_ bir kullanıcıya gösteriliyorsa, iş mantığınızdaki daha sonra belirlenebilir. Bu gibi durumlarda, _etkin olmayan olayları_kullanmanız gerekir.

## <a name="json-format-for-actions"></a>Eylemler için JSON biçimi

Derece çağrılırken, aralarından seçim yapabileceğiniz birden çok eylem gönderirsiniz:

JSON nesneleri, iç içe geçmiş JSON nesnelerini ve basit özellik/değerleri içerebilir. Bir dizi yalnızca dizi öğeleri sayı ise dahil edilebilir. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Bağlam bilgileri örnekleri

_Bağlam_ bilgisi her bir uygulamaya ve kullanım örneğine bağlıdır, ancak genellikle aşağıdakiler gibi bilgiler içerebilir:

* Kullanıcı hakkındaki demografik ve profil bilgileri.
* Kullanıcı Aracısı gibi HTTP başlıklarından ayıklanan veya IP adreslerine göre ters coğrafi aramalar gibi HTTP bilgilerinden türetilmiş bilgiler.
* Geçerli saat hakkında, haftanın günü, hafta sonu veya Not, sabah veya öğleden sonra tatil sezi vb. gibi bilgiler.
* Konum, taşıma veya pil düzeyi gibi mobil uygulamalardan ayıklanan bilgiler.
* Bu kullanıcının en çok görüntüledikleri film tarzları gibi Kullanıcı davranışının geçmiş toplamaları.

Uygulamanız, sahip olduğunuz ilgili veritabanlarından, sensörlerden ve sistemlerden bağlam hakkındaki bilgileri yüklemekten sorumludur. Bağlam bilgileriniz değişmezse, bu bilgileri derecelendirme API 'sine göndermeden önce önbelleğe almak için uygulamanıza Logic ekleyebilirsiniz.

## <a name="json-format-for-context"></a>Bağlam için JSON biçimi 

Bağlam, derecelendirme API 'sine gönderilen bir JSON nesnesi olarak ifade edilir:

JSON nesneleri, iç içe geçmiş JSON nesnelerini ve basit özellik/değerleri içerebilir. Bir dizi yalnızca dizi öğeleri sayı ise dahil edilebilir. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Pekiştirmeye dayalı öğrenme](concepts-reinforcement-learning.md) 
