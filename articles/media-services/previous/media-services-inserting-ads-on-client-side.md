---
title: İstemci tarafına reklam ekleme | Microsoft Docs
description: Bu konu başlığı altında, istemci tarafında reklamları nasıl ekleyebileceğiniz gösterilmektedir.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 37ee600a2f7d621d3fefb2f70c26b6c29f738ea9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162696"
---
# <a name="inserting-ads-on-the-client-side"></a>İstemci tarafına reklam ekleme
Bu makale, istemci tarafında çeşitli tür reklamları ekleme hakkında bilgiler içerir.

Canlı akış videolarında kapalı açıklamalı altyazı ve ad desteği hakkında daha fazla bilgi için bkz. [desteklenen kapalı açıklamalı altyazı ve ad ekleme standartları](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player şu anda reklamları desteklemiyor.
> 
> 

## <a id="insert_ads_into_media"></a>Medyanıza reklamları ekleme
Azure Media Services, Windows Media platformu: oynatıcı çerçeveleri aracılığıyla ad ekleme desteği sağlar. Windows 8, Silverlight, Windows Phone 8 ve iOS cihazlarda ad desteğine sahip oynatıcı çerçeveleri mevcuttur. Her Player çerçevesi, oynatıcı uygulamasının nasıl uygulanacağını gösteren örnek kod içerir. Medyanıza ekleyebileceğiniz üç farklı tür reklam vardır: listesi.

* **Doğrusal** – ana videoyu duraklatarak tam çerçeve reklamları.
* **Doğrusal** olmayan – ana video olarak görüntülenen kaplama reklamları, genellikle bir logo veya Player içinde yer alan başka bir statik görüntü oynamıştır.
* **Yardımcı** – oyuncu dışında görüntülenen reklamlar.

Reklamlar, ana videonun zaman satırındaki herhangi bir noktaya yerleştirilebilir. Player 'ın ad 'yi oynatacak ve hangi reklamları oynatacak hakkında söylemeniz gerekir. Bu, standart bir XML tabanlı dosyalar kümesi kullanılarak yapılır: video ad hizmeti şablonu (büyük), dijital video birden çok ad çalma listesi (VMAP), medya soyut sıralama şablonu (MAST) ve dijital video oynatıcı ad arabirimi tanımı (VÜCRETLI). Büyük dosyalar hangi reklamların gösterileceğini belirtir. VMAP dosyaları, çeşitli reklamları ne zaman oynatacak ve çok sayıda XML içermektir. MAST dosyaları, büyük bir XML de içerebilen reklamları sıralamanın başka bir yoludur. VÜCRETLI dosyalar, video oynatıcı ile ad veya ad sunucusu arasında bir arabirim tanımlar.

Her Player Çerçevesi farklı şekilde çalışır ve her biri kendi makalesinde ele alınacaktır. Bu makalede, reklamları eklemek için kullanılan temel mekanizmalar açıklanmaktadır. Video oynatıcı uygulamaları bir ad sunucusundan reklam ister. Ad sunucusu çeşitli yollarla yanıt verebilir:

* Büyük bir dosya döndür
* Bir VMAP dosyası (gömülü büyük) döndürün
* Bir MAST dosyası (gömülü büyük) döndürün
* VÜCRETLI reklamları olan büyük bir dosya döndürün

### <a name="using-a-video-ad-service-template-vast-file"></a>Video ad hizmet şablonu (büyük) dosyası kullanma
Büyük bir dosya hangi ad veya reklamları gösterileceğini belirtir. Aşağıdaki XML, doğrusal bir ad için büyük bir dosya örneğidir:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

Doğrusal ad <**doğrusal**> öğesi tarafından açıklanmıştır. Bu, ad süresini, izleme olaylarını, izleme ' yi ve bir dizi **mediafile** öğesini belirtir. İzleme olayları <**Trackingevents**> öğesi içinde belirtilmiştir ve ad sunucusunun ad görüntülenirken oluşan çeşitli olayları izlemesine izin verir. Bu durumda başlangıç, orta nokta, tamam ve Genişlet olayları izlenir. Ad görüntülendiğinde başlangıç olayı oluşur. Orta nokta olayı, ad zaman çizelgesinin en az %50 ' i görüntülenirken meydana gelir. Tüm olay, ad sonuna kadar çalıştırıldığında oluşur. Kullanıcı video oynatıcıyı tam ekran olarak genişlediğinde genişletme olayı oluşur. Clickthroughs, bir <**videotıklamalar**> öğesi Içinde <**tıklama**> öğesi ile belirtilir ve Kullanıcı ad 'ye tıkladığında görüntülenecek bir kaynağın URI 'sini belirtir. Tıklama Izlemesi, <**Videotıklamalar**> öğesi içinde de bir <**clicktracking**> öğesinde belirtilir ve Kullanıcı ad 'ye tıkladığında, oynatıcının istemesi için bir izleme kaynağı belirtir. <**Mediafile**> öğeleri, bir ad 'nin belirli bir kodlamasıyla ilgili bilgileri belirtir. Birden fazla <**mediafile**> öğesi olduğunda, video oynatıcı platformun en iyi kodlamasını seçebilir.

Doğrusal reklamlar belirtilen sırada gösterilebilir. Bunu yapmak için, büyük dosyaya ek `<Ad>` öğeleri ekleyin ve sıra özniteliğini kullanarak sırayı belirtin. Aşağıdaki örnek şunu göstermektedir:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Doğrusal reklamlar `<Creative>` öğesinde da belirtilir. Aşağıdaki örnekte, doğrusal olmayan bir ad tanımlayan bir `<Creative>` öğesi gösterilmektedir.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

**İlişkisel olmayan reklamları**> öğesi, her biri doğrusal olmayan bir ad açıklayan bir veya daha fazla <**Doğrusal**dizi > öğesi içerebilir. <**Doğrusal**olmayan > öğesi, doğrusal olmayan ad için kaynağı belirtir. Kaynak < bir**StaticResource**>, bir <**Iframpaesource**> veya <**HTMLResource**> olabilir. \<**StaticResource**> HTML olmayan bir kaynağı açıklar ve kaynağın nasıl görüntülendiğini belirten bir creativeType özniteliği tanımlar:

Image/gif, Image/JPEG, Image/PNG – kaynak bir HTML <**img**> etiketinde görüntülenir.

Application/x-JavaScript – kaynak, bir HTML <**betik**> etiketinde görüntülenir.

Application/x-Shockwave-Flash: kaynak, bir Flash oynatıcıda görüntülenir.

**Idimeresource** , IFRAME içinde GÖRÜNTÜLENEBILEN bir HTML kaynağını açıklar. **HTMLResource** , bir Web sayfasına EKLENEBILEN bir HTML kodu parçasını tanımlar. **Trackingevents** izleme olaylarını ve olay gerçekleştiğinde Istek yapılacak URI 'yi belirtir. Bu örnekte, Acceptınvisyon ve daraltma olayları izlenir. **İlişkisel olmayan ads** öğesi ve alt öğeleri hakkında daha fazla bilgi için bkz. IAB.net/VAST. **Trackingevents** öğesinin, **Doğrusal** olmayan öğe yerine, **nonmetinsiz** bir öğe içinde bulunduğunu unutmayın.

Yardımcı reklamlar bir `<CompanionAds>` öğesi içinde tanımlanır. `<CompanionAds>` öğesi bir veya daha fazla `<Companion>` öğesi içerebilir. Her `<Companion>` öğesi, bir yardımcı ad açıklar ve bir `<StaticResource>`, `<IFrameResource>`veya `<HTMLResource>` içerebilir ve bu da doğrusal olmayan bir ad ile aynı şekilde belirtilir. Büyük bir dosya birden çok yardımcı reklam içerebilir ve oynatıcı uygulaması görüntülenecek en uygun ad ' ı seçebilir. Büyük hakkında daha fazla bilgi için bkz. çok [3,0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Dijital video çoklu ad çalma listesi (VMAP) dosyası kullanma
Bir VMAP dosyası, ad sonlarının ne zaman gerçekleşeceğini, her kesmenin ne kadar süreceğine, bir kesme içinde kaç reklam gösterileceğini ve bir kesme sırasında hangi tür reklamları görüntülenebileceğini belirtmenizi sağlar. Aşağıda, tek bir ad kesmeyi tanımlayan örnek bir VMAP dosyası verilmiştir:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Bir VMAP dosyası, her biri bir ad kesmesi tanımlayan bir veya daha fazla `<AdBreak>` öğesi içeren `<VMAP>` öğesi ile başlar. Her ad kesmesi bir kesme türü, kesme KIMLIĞI ve zaman boşluğu belirtir. BreakType özniteliği, kesme sırasında yürütülebilecek ad türünü belirtir: doğrusal, doğrusal olmayan veya görüntü. Büyük ve çok yardımcı reklamlar için ads haritasını görüntüleyin. Birden çok ad türü, virgülle ayrılmış bir liste (boşluk olmadan) ile belirtilebilir. Breakıd, ad için isteğe bağlı bir tanıtıcıdır. Timekayması, ad ' ın ne zaman gösterileceğini belirtir. Aşağıdaki yollarla belirtilebilir:

1. Time – hh: mm: SS veya ss: DD: ss. mmm biçiminde; burada aaa milisaniyedir. Bu özniteliğin değeri, video zaman çizelgesinin başından ad kesmenin başlangıcına kadar olan süreyi belirtir.
2. Yüzde: n% biçimindedir; burada n, ad oynatıcının oynatılması için video zaman çizelgesinin yüzdesidir
3. Başlangıç/bitiş – bir ad, video görüntülenmeden önce veya sonra görüntülenecek şekilde belirtir
4. Konum – canlı akış gibi ad sonlarının zamanlaması bilinmiyorsa ad sonlarının sırasını belirtir. Her ad kesmenin sırası, n 'nin bir tamsayı 1 veya daha büyük olduğu #n biçiminde belirtilir. 1 ad 'nin ilk fırsatta yürütülmesi gerektiğini belirtir, 2 ad ikinci fırsatta çalınmalıdır ve bu şekilde devam eder.

`<AdBreak>` öğesi içinde, bir <**Adsource**> öğesi olabilir. <**Adsource**> öğesi aşağıdaki öznitelikleri içerir:

1. ID: ad kaynağı için bir tanımlayıcı belirtir
2. Allowmultipliderleri: ad sonu sırasında birden fazla reklam gösterilip gösterilmeyeceğini belirten bir Boole değeri
3. Izleme yeniden yönlendirmeleri: video yürütücüsünün bir ad yanıtı içindeki yeniden yönlendirmeleri dikkate almalıdır olduğunu belirten isteğe bağlı bir Boolean değeri

<**Adsource**> öğesi, oynatıcı için bir satır içi ad yanıtı veya bir ad yanıtı başvurusu sağlar. Aşağıdaki öğelerden birini içerebilir:

* `<VASTAdData>`, VMAP dosyası içine büyük bir ad yanıtının gömülmesini gösterir
* başka bir sistemden ad yanıtına başvuran bir URI `<AdTagURI>`
* `<CustomAdData>`-çok büyük olmayan yanıtı temsil eden rastgele bir dize

Bu örnekte, büyük bir ad yanıtı içeren bir `<VASTAdData>` öğesiyle bir satır içi ad yanıtı belirtilmiştir. Diğer öğeler hakkında daha fazla bilgi için bkz. [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

**Adbreak**> öğesi bir <**Trackingevents**> öğesi de içerebilir. <**Trackingevents**> öğesi, bir ad sonunun başlangıcını veya sonunu izlemenize veya ad kesmesi sırasında bir hata oluşup oluşmadığını izlemenize olanak sağlar. **Trackingevents**> öğesi, her biri bir izleme olayı ve bir izleme URI 'si belirten bir veya daha fazla <**izleme**> öğesi içeriyor. Olası izleme olayları şunlardır:

1. breakStart: bir ad kesmenin başlangıcını izler
2. breakEnd – bir ad kesmenin tamamlanmasını izleme
3. Hata: ad sonu sırasında oluşan bir hatayı izler

Aşağıdaki örnek, izleme olaylarını belirten bir VMAP dosyasını gösterir

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

**Trackingevents**> öğesi ve alt öğeleri hakkında daha fazla bilgi için bkz http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Medya soyut sıralama şablonu (MAST) dosyası kullanma
Bir MAST dosyası, bir ad görüntülendiğinde tanımlayan Tetikleyicileri belirtmenize olanak tanır. Aşağıda, bir ön alma ad, orta düzey ad ve bir son alma reklamı için Tetikleyiciler içeren bir MAST dosyası verilmiştir.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


Bir MAST dosyası bir **Triggers** öğesi Içeren bir **mast** öğesi ile başlar. `<triggers>` öğesi, bir ad 'nin ne zaman yürütülmesi gerektiğini tanımlayan bir veya daha fazla **tetikleyici** öğesi içeriyor.

**Tetikleyici** öğesi, bir ad 'nin oynatılma başlaması gereken zaman belirten bir **startconditions** öğesi içerir. **Startconditions** öğesi bir veya daha fazla `<condition>` öğesi içeriyor. Her bir `<condition>` true olarak değerlendirilirse, `<condition>` bir **startconditions** veya **endconditions** öğesi içinde olup olmadığına bağlı olarak bir tetikleyici başlatılır veya iptal edilir. Birden çok `<condition>` öğesi olduğunda, örtülü olarak değerlendirilir, true olarak değerlendirilen herhangi bir koşul tetikleyicisinin başlatılmasına neden olur. `<condition>` öğeler iç içe olabilir. Alt `<condition>` öğeleri önceden ayarlı olduğunda, örtülü olarak değerlendirilir ve tetikleyicisinin başlatılması için tüm koşulların doğru olarak değerlendirilmesi gerekir. `<condition>` öğesi, koşulu tanımlayan aşağıdaki öznitelikleri içerir:

1. **tür** – koşulun, etkinliğin veya özelliğin türünü belirtir
2. **ad** : değerlendirme sırasında kullanılacak özelliğin veya etkinliğin adı
3. **değer** : bir özelliğin değerlendirileceği değer
4. **işleç** – değerlendirme sırasında kullanılacak Işlem: EQ (eşittir), NEQ (eşit değildir), GTR (daha büyük), GEQ (büyük veya eşittir), lt (küçüktür), LEQ (küçüktür veya eşittir), mod (mod)

**Endconditions** Ayrıca `<condition>` öğeleri içerir. Bir koşul true olarak değerlendirildiğinde tetikleyici sıfırlanır. `<trigger>` öğesi, bir veya daha fazla `<source>` öğesi içeren bir `<sources>` öğesi de içerir. `<source>` öğeleri, ad yanıtının URI 'sini ve ad yanıtının türünü tanımlar. Bu örnekte, büyük bir yanıta bir URI verilir.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Video oynatıcı kullanma-ad arabirimi tanımı (VÜCRETLI)
VÜCRETLI, yürütülebilir ad birimlerinin bir video oynatıcı ile iletişim kurmasını sağlayan bir API 'dir. Bu, son derece etkileşimli ad deneyimlerine izin verir. Kullanıcı ad ile etkileşime geçebilir ve ad, Görüntüleyici tarafından alınan eylemlere yanıt verebilir. Örneğin, bir ad, kullanıcının daha fazla bilgi veya daha uzun bir sürümü görüntülemesine izin veren düğmeler görüntüleyebilir. Video oynatıcı, VÜCRETLI API 'yi desteklemelidir ve yürütülebilir ad, API 'YI uygulamalıdır. Bir oyuncu bir ad sunucusundan bir ad istediğinde, sunucu, VÜCRETLI ad içeren büyük bir Yanıt ile yanıt verebilir.

Bir yürütülebilir ad, Adobe Flash™ veya bir Web tarayıcısında yürütülebilecek JavaScript gibi bir çalışma zamanı ortamında yürütülmesi gereken kodda oluşturulur. Bir ad sunucusu VÜCRETLI ad içeren büyük bir yanıt döndürdüğünde, `<MediaFile>` öğesindeki apiFramework özniteliğinin değeri "VÜCRETLI" olmalıdır. Bu öznitelik, kapsanan ad 'nin bir VÜCRETLI yürütülebilir ad olduğunu belirtir. Tür özniteliği yürütülebilir dosyanın MIME türüne ayarlanmalıdır (örneğin, "application/x-Shockwave-Flash" veya "application/x-JavaScript"). Aşağıdaki XML kod parçacığında, VÜCRETLI yürütülebilir ad içeren büyük bir yanıttan `<MediaFile>` öğesi gösterilmektedir.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Yürütülebilir bir ad, büyük bir yanıtta `<Linear>` veya `<NonLinear>` öğelerinde `<AdParameters>` öğesi kullanılarak başlatılabilir. `<AdParameters>` öğesi hakkında daha fazla bilgi için bkz. çok [3,0](https://www.iab.net/media/file/VASTv3.0.pdf). VÜCRETLI API hakkında daha fazla bilgi için bkz. [vücretli 2,0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Ad desteğiyle Windows veya Windows Phone 8 oynatıcı uygulama
Microsoft medya platformu: Windows 8 ve Windows Phone 8 için oynatıcı Framework, Framework kullanarak bir video oynatıcı uygulamasının nasıl uygulanacağını gösteren örnek uygulamaların bir koleksiyonunu içerir. Oynatıcı çerçevesini ve [Windows 8 Için Player Framework ve Windows Phone 8 '](https://playerframework.codeplex.com)deki örnekleri indirebilirsiniz.

Microsoft. PlayerFramework. xaml. Samples çözümünü açtığınızda, proje içinde bir dizi klasör görürsünüz. Reklam klasörü, ad desteğiyle bir video oynatıcı oluşturmayla ilgili örnek kodu içerir. Reklam klasörü içinde her biri farklı bir şekilde reklamları nasıl ekleyebileceğiniz gösteren bir dizi XAML/cs dosyası. Aşağıdaki listede her biri açıklanmaktadır:

* Adpod Page. xaml, bir ad Pod 'un nasıl görüntüleneceğini gösterir.
* AdSchedulingPage. xaml, reklamları zamanlamayı gösterir.
* FreeWheelPage. xaml, reklamları zamanlamak için FreeWheel eklentisinin nasıl kullanılacağını gösterir.
* MastPage. xaml, bir MAST dosyası ile reklamları zamanlamayı gösterir.
* ProgrammaticAdPage. xaml, reklamları programlı bir şekilde bir videoya zamanlamayı gösterir.
* ScheduleClipPage. xaml, büyük bir dosya olmadan bir ad zamanlamayı gösterir.
* Vastbir Companionpage. xaml, doğrusal ve yardımcı bir ad eklemenin nasıl yapılacağını gösterir.
* Vastnonbir. xaml, doğrusal olmayan bir ad eklemeyi gösterir.
* VmapPage. xaml, bir VMAP dosyası ile reklamları belirtmeyi gösterir.

Bu örneklerin her biri, Player çerçevesi tarafından tanımlanan MediaPlayer sınıfını kullanır. Çoğu örnek, çeşitli ad yanıt biçimleri için destek ekleyen eklentileri kullanır. ProgrammaticAdPage örneği programlı olarak bir MediaPlayer örneğiyle etkileşime girer.

### <a name="adpodpage-sample"></a>Adpod sayfa örneği
Bu örnek, bir ad ne zaman görüntüleneceğini tanımlamak için AdSchedulerPlugin kullanır. Bu örnekte, bir orta seviye tanıtım, beş saniyeden sonra çalınmak üzere zamanlandı. Ad sunucusundan döndürülen büyük bir dosyada ad Pod (sırayla görüntülenecek bir reklamlar grubu) belirtilir. Büyük dosyanın URI 'SI `<RemoteAdSource>` öğesinde belirtilir.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

AdSchedulerPlugin hakkında daha fazla bilgi için bkz. [Windows 8 ve Windows Phone 8 ' de oynatıcı çerçevesinde reklam](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Bu örnek ayrıca AdSchedulerPlugin kullanır. Üç reklam, bir ön uç ad, orta noktadan bir ad ve bir son alma reklamı zamanlar. Her ad için büyük olan URI bir `<RemoteAdSource>` öğesinde belirtilir.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
Bu örnek, ad içeriğini ve ad zamanlama bilgilerini belirten bir SmartXML dosyasına işaret eden bir URI belirten bir kaynak özniteliği belirten FreeWheelPlugin 'i kullanır.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Bu örnek, bir MAST dosyası kullanmanıza izin veren MastSchedulerPlugin kullanır. Kaynak öznitelik, MAST dosyasının konumunu belirtir.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Bu örnek, program aracılığıyla MediaPlayer ile etkileşime girer. ProgrammaticAdPage. xaml dosyası MediaPlayer 'yi başlatır:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

ProgrammaticAdPage.xaml.cs dosyası bir AdHandlerPlugin oluşturur, bir ad ne zaman görüntüleneceğini belirtmek için bir TimelineMarker ekler ve sonra da büyük bir dosyaya bir URI belirterek RemoteAdSource 'u yükleyen Markerulaşıldı olayı için bir işleyici ekler ve ardından ad yürütülür.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>Scheduleclipsayfası
Bu örnek, ad içeren bir. wmv dosyası belirterek bir orta noktadan ad zamanlamak için AdSchedulerPlugin kullanır.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>Vastdoğrcompanionpage
Bu örnek, bir yardımcı ad ile orta ölçekli bir ad zamanlamak için AdSchedulerPlugin 'in nasıl kullanılacağını gösterir. `<RemoteAdSource>` öğesi, büyük dosyanın konumunu belirtir.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>Vastdoğrnondoğruz sayfası
Bu örnek, doğrusal ve doğrusal olmayan bir ad zamanlamak için AdSchedulerPlugin kullanır. Büyük dosya konumu `<RemoteAdSource>` öğesiyle belirtilir.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
Bu örnek, VMAP dosyası kullanarak reklamları zamanlamak için VmapSchedulerPlugin kullanır. VMAP dosyasının URI 'SI `<VmapSchedulerPlugin>` öğesinin kaynak özniteliğinde belirtilir.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Ad desteğiyle iOS video oynatıcı uygulama
Microsoft medya platformu: iOS için oynatıcı çatısı, Framework kullanarak bir video oynatıcı uygulamasının nasıl uygulanacağını gösteren örnek uygulamalar koleksiyonunu içerir. Player çerçevesini ve [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework)'ten örnekleri indirebilirsiniz. GitHub sayfasında, oynatıcı çerçevesiyle ilgili ek bilgiler içeren bir wiki bağlantısı ve Player örneğine giriş: [Azure Media Player wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>VMAP ile reklamları zamanlama
Aşağıdaki örnek, bir VMAP dosyası kullanılarak reklamları zamanlamayı gösterir.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Büyük ölçekli reklamları zamanlama
Aşağıdaki örnek, bir geç bağlamanın büyük bir ad olarak nasıl planlanalınacağını göstermektedir.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   Aşağıdaki örnek, bir erken bağlamanın büyük ad ile nasıl planlanalınacağını göstermektedir.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

Aşağıdaki örnek, kaba bir kesme düzenlemesi (RCE) kullanılarak nasıl ad ekleneceğini gösterir

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

Aşağıdaki örnek, bir ad Pod zamanlamasını gösterir.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Aşağıdaki örnek, yapışkan olmayan bir orta noktadan ad 'nin nasıl zamanlanıp planlanalınacağını göstermektedir. Yapışkan olmayan bir ad, görüntüleyicinin yaptığı herhangi bir arama ne olursa olsun yalnızca bir kez oynatılır.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Aşağıdaki örnek, bir yapışkan MIN ad zamanlamasını gösterir. Her zaman, video zaman çizelgesinde belirtilen noktaya ulaşıldığında bir yapışkan ad görüntülenir.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Aşağıdaki örnek, bir son alma reklamı zamanlamasını gösterir.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Aşağıdaki örnek, bir ön alma ad zamanlamasını gösterir.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Aşağıdaki örnek, bir orta seviye kaplama ad zamanlamasının nasıl planlanarak göstermektedir.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirim sağlayın
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca Bkz.
[Video oynatıcı uygulamaları geliştirme](media-services-develop-video-players.md)

