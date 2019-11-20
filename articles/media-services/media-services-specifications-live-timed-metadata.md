---
title: Canlı akış 'da zamanlanmış meta verileri Azure Media Services sinyali alma | Microsoft Docs
description: Bu belirtim, Azure Media Services alırken ve akışa alma sırasında zamanlı meta verileri sinyal almaya yönelik yöntemleri özetler. Bu, genel zamanlı olmayan meta veri sinyalleri (ıD3) desteğinin yanı sıra ad ekleme ve splice Condition sinyali için de SCTE-35 sinyali içerir.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: df2a86dd1292f58511765e842ee97daddcff4e3e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102931"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Canlı akışta zamanlanmış meta verileri sinyal alma 

Son Güncelleme Tarihi: 2019-08-22

### <a name="conformance-notation"></a>Uygunluk gösterimi

"", "" Gerekli değildir "," gerekli "," Bu "," Bu "," zorunlu değil "," durum "ve" Isteğe bağlı "anahtar sözcükleri, RFC 2119 ' de açıklandığı gibi yorumlanmalıdır.

## <a name="1-introduction"></a>1. Giriş 

Bir istemci oynatıcısının reklamları veya özel meta veri olaylarını eklemeye işaret etmek için, yayımcılar genellikle videoda yerleşik olarak bulunan zaman aşımına uğramış meta verileri kullanır. Bu senaryoları etkinleştirmek için Media Services, canlı akış kanalının alma noktasındaki zaman aşımına uğramış meta verilerin istemci uygulamasına taşınması için destek sağlar.
Bu belirtim, canlı akış sinyalleri içindeki zaman aşımına uğramış meta veriler için Media Services tarafından desteklenen çeşitli modları özetler.

1. [SCTE-35] [SCTE-35], [SCTE-214-1], [SCTE-214-3] ve [RFC8216] tarafından belirtilen standartlarla uyumlu olan sinyal

2. [SCTE-35] RTMP ad sinyali için eski [Adobe-Primetime] belirtimiyle uyumlu sinyal.
   
3. [Scte-35] **olmayan** ve [ID3v2] veya uygulama geliştiricisi tarafından tanımlanan diğer özel şemaları taşıyabilecek iletiler için genel bir zaman uyumlu meta veri sinyali modu.

## <a name="11-terms-used"></a>1,1 terim kullanıldı

| Terim              | Tanım |
|-------------------|------------|
| Ad kesmesi          | Bir veya daha fazla reklamın teslim edilmek üzere zamanlanabileceği bir konum veya zaman noktası; kullanılabilir ve yerleştirme fırsatına benzer. |
| Ad karar hizmeti| kullanıcıya hangi ad (ler) ve süreler gösterildiğine karar veren dış hizmet. Hizmetler genellikle bir iş ortağı tarafından sağlanır ve bu belgenin kapsamı dışındadır.|
| 'Yu               | Yaklaşan ad kesmenin zaman ve parametrelerinin göstergesi. İpuçlarının bir ad kesmesi için bekleyen bir anahtarı belirtebileceğini, bir ad kesmesi içindeki bir sonraki ad için bekleyen geçiş ve bir ad sonundan Ana içeriğe bekleyen bir geçiş olduğunu unutmayın. |
| Paketleyici          | "Akış uç noktası" Azure Media Services, DASH ve HLS için dinamik paketleme özellikleri sağlar ve medya sektöründe "paketleyicisi" olarak adlandırılır. 
| Sunum süresi | Bir olaya bir görüntüleyiciye sunulan zaman. Bu süre, medya zaman çizelgesinde bir izleyicinin olayı göreceği süreyi temsil eder. Örneğin, bir SCTE-35 splice_info () komut iletisinin sunum süresi splice_time () ' dır. |
| Varış saati      | Bir olay iletisinin ulaştığı zaman. Olay iletileri etkinliğin sunum zamanından önce gönderildiğinden, saat genellikle olayın sunum zamanından farklıdır.                                     |
| Seyrek parça      | sürekli olmayan ve zaman bir üst veya denetim izlemeli eşitlenmiş medya izi.                                                                                                                                    |
| Kaynak            | Azure Medya akışı hizmeti                                                                                                                                                                                                |
| Kanal havuzu      | Azure Medya canlı akış hizmeti                                                                                                                                                                                           |
| HLS               | Apple HTTP Canlı Akışı Protokolü                                                                                                                                                                                               |
| TILDE              | HTTP üzerinden dinamik uyarlamalı akış                                                                                                                                                                                             |
| Kesintisiz            | Kesintisiz Akış Protokolü                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG 2 aktarım akışları                                                                                                                                                                                                         |
| RTMP              | Gerçek zamanlı multimedya Protokolü                                                                                                                                                                                                    |
| uıısbf            | İşaretsiz tamsayı, önce en önemli bit.                                                                                                                                                                                    |

---

## <a name="12-normative-references"></a>1,2 normatif başvurular

Aşağıdaki belgeler, bu metinde başvuru aracılığıyla bu belgenin sağlamasını oluşturan sağlama sağlar. Tüm belgeler standartlar gövdelerinin düzeltmesine tabidir ve okuyucular aşağıda listelenen belgelerin en son sürümlerini uygulama olasılığını araştırmaya teşvik edilir. Okuyucular Ayrıca, başvurulan belgelerin yeni sürümlerinin Azure Media Services için zaman aşımına uğramış meta veri belirtiminin bu sürümü ile uyumlu olabileceğini de anımsatılır.


|Standart  |Tanım  |
|---------|---------|
|[Adobe-Primetime] | [Primetime dijital program ekleme sinyal belirtimi 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-Flash-AS] | [FLASH ActionScript dil başvurusu](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| [AMF0]            | ["Eylem Iletisi biçimi AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [DASH-IF-IOP]     | DASH sektör Forumu birlikte çalışma kılavuzu v 4,2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | HTTP Canlı Akışı için zamanlanmış meta veriler-[https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [CMAF-ID3]         | [Ortak medya uygulaması biçiminde zamanlanmış meta veriler (CMAF)](https://aomediacodec.github.io/av1-id3/)
| [ID3v2]           | ID3 etiket sürümü 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12: Bölüm 12 ISO temel medya dosyası biçimi, Onthedition 2012-07-15  |
| [MPEGDASH]        | Bilgi teknolojisi-HTTP üzerinden dinamik uyarlamalı akış (DASH)--1. Bölüm: Medya sunusu açıklaması ve kesim biçimleri. 2014 Mayıs. Yayımladığı. 'DEKI https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | Bilgi teknolojisi--multimedya uygulama biçimi (MPEG-A)--Bölüm 19: Bölünmüş medya için ortak medya uygulaması biçimi (CMAF). Ocak 2018. Yayımladığı. 'DEKI https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Bilgi teknolojisi--MPEG Sistemleri Teknolojileri--Bölüm 7: ISO tabanlı medya dosyası biçim dosyalarında ortak şifreleme. 2016 Şubat. Yayımladığı. 'DEKI https://www.iso.org/standard/68042.html |
| [MS-SSTR]         | ["Microsoft Kesintisiz Akış Protokolü", 15 Mayıs 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [MS-SSTR-ınest]  | [Azure Media Services parçalanmış MP4 canlı alma belirtimi](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | R. Pantos, Ed.; W. Mayıs. HTTP Canlı Akışı. Ağustos 2017. Amaçlı. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| [RFC4648]         |Base16, Base32 ve Base64 veri kodlamaları-[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| RTMP            |["Adobe 'un gerçek zamanlı mesajlaşma Protokolü", 21 Aralık 2012](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35: 2019-kablolu için dijital program ekleme Cueiletisi- https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 – IP tabanlı kablo hizmetleri için MPEG DASH Bölüm 1: MPD kısıtlamaları ve uzantıları |
| [SCTE-214-3]      | IP tabanlı kablo hizmetleri için SCTE 214-3 2015 MPEG DASH Bölüm 3: TIRE/FF profili |
| [SCTE-224]        | SCTE 224 2018r1 – olay zamanlama ve bildirim arabirimi |
| [SCTE-250]        | Olay ve sinyal yönetimi API 'SI (ESAM) |

---


## <a name="2-timed-metadata-ingest"></a>2. Zamanlanmış meta veri alma

Azure Media Services hem [RTMP] Kesintisiz Akış hem de [MS-SSTR-Ingest] protokolleri için gerçek zamanlı bant içi meta verileri destekler. Gerçek zamanlı meta veriler, özel olayları tanımlamak için kullanılabilir (JSON, Ikili, XML), Ayrıca, bir yayın akışındaki ad sinyali için ıD3 veya SCTE-35 gibi endüstri tanımlı biçimler kullanabilirsiniz. 

Bu makalede, Azure Media Services için desteklenen alma protokollerini kullanarak özel zamanlı meta veri sinyalleri gönderme ayrıntıları sağlanmaktadır. Makalede ayrıca, HLS, DASH ve Kesintisiz Akış bildirimlerinin, zaman uyumlu olmayan meta veri sinyalleriyle nasıl ilişkili olduğu ve içerik CMAF (MP4 parçaları) veya HLS için aktarım akışı (TS) kesimleri kullanılarak bantta nasıl taşınacağı açıklanmaktadır. 

Zaman aşımına uğramış meta veriler için genel kullanım örneği senaryoları şunlardır:

 - Canlı bir olayda veya doğrusal yayında ad sonlarının tetiklenmesi için SCTE-35 ad sinyalleri
 - Bir istemci uygulamasında (tarayıcı, iOS veya Android) olayları tetikleyesağlayan özel ıD3 meta verileri
 - İstemci uygulamasında olayları tetiklemek için özel tanımlanmış JSON, Ikili veya XML meta verileri
 - Live Encoder, IP kamerası veya drone telemetri telemetrisi
 - Hareket, yüz algılama vb. gibi bir IP kamerasından gelen olaylar.
 - Bir eylem kamerasından, drbir cihazdan veya taşınan cihazdan coğrafi konum bilgileri
 - Şarkı şarkı sözleri
 - Doğrusal canlı bir akışta program sınırları
 - Canlı bir akışta görüntülenecek görüntüler veya genişletilmiş meta veriler
 - Spor puanları veya oyun saati bilgileri
 - Tarayıcıda videonun yanında görüntülenecek etkileşimli reklam paketleri
 - Testler veya yoklamalar
  
Azure Media Services canlı olaylar ve Paketleyici, bu zaman aşımına uğramış meta veri sinyallerini alabilir ve bunları, HLS ve DASH gibi standartlara dayalı protokoller kullanarak istemci uygulamalarına ulaşan meta veri akışına dönüştürür.


## <a name="21-rtmp-timed-metadata"></a>2,1 RTMP zamanlı meta veriler

[RTMP] Protokolü, özel meta veriler ve SCTE-35 ad sinyalleri dahil çeşitli senaryolar için zamanlanmış meta veri sinyallerinin gönderilmesine izin verir. 

Reklam sinyalleri (işaret iletileri) [RTMP] akışı içine katıştırılmış [AMF0] Cue iletileri olarak gönderilir. İpucu iletileri gerçek olay veya [SCTE35] ad splice Signal gerçekleşmesi için bir süre önce gönderilebilir. Bu senaryoyu desteklemek için, olayın gerçek sunum zaman damgası, işaret iletisi içinde gönderilir. Daha fazla bilgi için bkz. [AMF0].

Aşağıdaki [AMF0] komutları, RTMP alma için Azure Media Services tarafından desteklenir:

- **Onuserdataevent** -özel meta veriler veya [ID3v2] için zamanlanmış meta veriler için kullanılır
- **Onadcue** -canlı akıştaki tanıtım yerleşimi fırsatının sinyali için öncelikli olarak kullanılır. İki ipucu biçimi desteklenir, basit bir mod ve "SCTE-35" modu. 
- **onCuePoint** -Live Encoder gibi belirli şirket içi donanım kodlayıcıları tarafından [SCTE35] ileti sinyali için desteklenir. 
  

Aşağıdaki tablolarda, Media Services hem "basit" hem de [SCTE35] ileti modlarını alacak olan AMF ileti yükünün biçimi açıklanmaktadır.

[AMF0] iletisinin adı aynı türden birden çok olay akışını ayırt etmek için kullanılabilir.  Hem [SCTE-35] iletileri hem de "basit" modu için, AMF iletisinin adı [Adobe-Primetime] belirtiminde gerekli olduğu gibi "onAdCue" olmalıdır.  Aşağıda listelenmeyen tüm alanlar alma sırasında Azure Media Services yok sayılır.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>"onUserDataEvent" kullanarak özel meta verilerle 2.1.1 RTMP

Yukarı akış kodlayıcısından, IP kameranıza, drone 'dan veya RTMP protokolünü kullanarak cihazdan özel meta veri akışları sağlamak istiyorsanız, "onUserDataEvent" [AMF0] veri iletisi komut türünü kullanın.

**"Onuserdataevent"** veri iletisi komutu, Media Services tarafından yakalanıp bant içi dosya biçiminde paketlenmesi ve HLS, DASH ve kesintisiz akış için bildirim almak üzere aşağıdaki tanım ile bir ileti yükü taşımalıdır.
Her 0,5 saniye (500ms) veya canlı akışla ilgili kararlılık sorunlarından daha sık, zaman aşımına uğramamış veri iletileri göndermeniz önerilir. Çerçeve düzeyinde meta veriler sağlamanız gerekiyorsa, her ileti birden çok kareden meta verileri toplayabilir. Çoklu bit hızı akışları gönderiyorsanız, meta verileri tek bir bit hızında sağlamanız önerilir, bu da yalnızca bant genişliğini azaltmak ve video/ses işlemeyle ilgili girişimlerden kaçınmak için gerekir. 

**"Onuserdataevent"** için yük BIR [MPEGDASH] EVENTSTREAM xml biçim iletisi olmalıdır. Bu, HLS veya DASH protokolleri üzerinden sunulan CMAF [MPEGCMAF] içeriği için bantta ' EMSG ' yükleri içinde taşınılabilen özel tanımlı şemaları kolayca geçirmeye olanak sağlar. Her DASH olay akışı iletisi, URN ileti düzeni tanımlayıcısı olarak işlev gören ve iletinin yükünü tanımlayan bir IBir tek düzen içerir. [Scte-35 https://aomedia.org/emsg/ID3 ] için "" [ID3v2] veya **urn: scte: scte35:2013: bin** gibi bazı şemalar, birlikte çalışabilirlik için sektör yarışma göre standartlaştırılmıştır. Herhangi bir uygulama sağlayıcısı, denetleyedikleri URL 'YI (sahip etki alanı) kullanarak kendi özel düzenlerini tanımlayabilir ve tercih ettikleri URL 'de bir belirtim sağlayabilir. Bir oyuncunun tanımlı düzen için bir işleyicisi varsa, bu, yükü ve Protokolü anlaması gereken tek bileşendir.

[MPEG-DASH] EventStream XML yükünün şeması olarak tanımlanır (DASH ISO-ıEC-23009-1-3. sürüm). Şu anda yalnızca "EventStream" başına bir "EventType" değeri desteklendiğini unutmayın. **Eventstream**'de birden fazla olay sağlanıyorsa yalnızca ilk **olay** öğesi işlenir.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>ID3 şema KIMLIĞI ve Base64 kodlamalı veri yüküne sahip örnek XML olay akışı.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Özel şema KIMLIĞI ve Base64 kodlamalı ikili verileri içeren örnek olay akışı  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Özel şema KIMLIĞI ve özel JSON ile örnek olay akışı  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Yerleşik desteklenen şema KIMLIĞI URI 'Leri
| Şema KIMLIĞI URI 'SI                 |  Açıklama                                             |
|-------------------------------|----------------------------------------------------------|
| https:\//aomedia.org/EMSG/ID3   | [ID3v2] meta verilerinin CMAF ile uyumlu [MPEGCMAF] parçalanmış MP4 içinde zaman aşımına uğramış bir meta veriler olarak nasıl taşınabilir açıklar. Daha fazla bilgi için bkz [. ortak medya uygulaması biçimindeki (CMAF) zaman aşımına uğrayan meta veriler](https://aomediacodec.github.io/av1-id3/) |

### <a name="event-processing-and-manifest-signaling"></a>Olay işleme ve bildirim sinyali verme

Geçerli bir **"Onuserdataevent"** olayının alınması sırasında, Azure Media Services EventStreamType ([MPEGDASH] içinde tanımlanmıştır) ile eşleşen GEÇERLI bir XML yükünü ARAYACAKTıR, XML yükünü ayrıştırır ve bır [MPEGCMAF] mp4 Fragment ' EMSG ' sürüm 1 kutusuna dönüştürür Canlı arşivdeki depolama ve Media Services Paketleyiciyi aktarma.   Paketleyici, canlı akıştaki ' EMSG ' kutusunu algılar ve şu şekilde görünür:

- (a) "dinamik olarak paketleyin" HLS istemcilerinin HLS için zaman uyumsuz meta veri belirtimi [HLS-TMD] veya
- (b) hmaf parçaları içinde HLS veya DASH aracılığıyla teslim etmek için ile geçiş yapın veya 
- (c) Kesintisiz Akış [MS-SSTR] aracılığıyla teslim için bir seyrek izleme sinyaline dönüştürün.

HLS için bant içi ' EMSG ' biçimine ek olarak, DASH (MPD) ve Kesintisiz Akış için bildirimler, bant içi olay akışlarına (Kesintisiz Akış aynı zamanda seyrek akış izleme olarak da bilinir) bir başvuru içerir. 

Bireysel olaylar veya veri yükleri doğrudan HLS, kesik çizgi veya düzgün bildirimlerde çıktı DEĞILDIR. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>OnUserDataEvent olayları için ek bilgilendirici kısıtlamalar ve varsayılanlar

- EventStream öğesinde zaman ölçeği ayarlanmamışsa, varsayılan olarak RTMP 1 kHz zaman ölçeği kullanılır
- OnUserDataEvent iletisinin teslimi her 500 MS Max ile sınırlıdır. Olayları daha sık gönderirseniz, bant genişliğini ve canlı akışın kararlılığını etkileyebilir

## <a name="212-rtmp-ad-cue-signaling-with-oncuepoint"></a>"onCuePoint" ile 2.1.2 'yi RTMP ad ipucu sinyali

Azure Media Services, canlı akıştaki çeşitli gerçek zamanlı eşitlenmiş meta verileri göstermek için kullanılabilecek çeşitli [AMF0] ileti türlerini dinleyebilir ve yanıtlayabilir.  [Adobe-Primetime] belirtimi, "Simple" ve "SCTE-35" modu olarak adlandırılan iki işaret türünü tanımlar. "Basit" modu için Media Services, "basit mod" sinyali için tanımlanan tabloyla eşleşen bir yük kullanarak "onAdCue" adlı tek bir AMF ipucu iletisini destekler.  

Aşağıdaki bölümde, HLS, DASH ve Microsoft Kesintisiz Akış istemci bildirimine yapılacak temel bir "spliceOut" ad sinyalini işaret etmek için kullanılabilen RTMP "basit" mod "yükü gösterilmektedir. Bu, müşterinin karmaşık bir SCTE-35 tabanlı ad sinyal dağıtımı veya ekleme sistemine sahip olmadığı ve bir API aracılığıyla işaret iletisinde göndermek için temel bir şirket içi kodlayıcı kullandığı senaryolarda çok yararlı olur. Genellikle şirket içi kodlayıcı, bu sinyali tetiklemek için REST tabanlı bir API 'yi destekleyecektir. Bu, videoya bir ıDR çerçevesi ekleyerek ve yeni bir GOP başlatarak video akışını da "splice-Condition" olarak destekler.

## <a name="213--rtmp-ad-cue-signaling-with-oncuepoint---simple-mode"></a>"onCuePoint"-basit mod ile 2.1.3 RTMP ad ipucu sinyali

| Alan Adı | Alan türü | Gerekli mi? | Açıklamaları                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 'yu        | Dize     | Gerekli | Olay iletisi.  Basit bir mod splice belirlemek için "SpliceOut" olacaktır.                                              |
| id         | Dize     | Gerekli | Splice veya segment tanımlayan benzersiz bir tanımlayıcı. İletinin bu örneğini tanımlar                            |
| duration   | Number     | Gerekli | Splice 'ın süresi. Birimler kesirli saniyedir.                                                                |
| elapsed    | Number     | İsteğe Bağlı | Sinyal, içindeki ayarlamayı desteklemek için yinelendiğinde, bu alan, splice başladıktan sonra geçen sunum süresinin miktarı olacaktır. Birimler kesirli saniyedir. Basit mod kullanılırken bu değer, splice 'ın orijinal süresini aşmamalıdır.                                                  |
| time       | Number     | Gerekli | , Sunum süresi içinde, splice 'ın saati olacaktır. Birimler kesirli saniyedir.                                     |

---
 
## <a name="214-rtmp-ad-cue-signaling-with-oncuepoint---scte-35-mode"></a>"onCuePoint"-SCTE-35 modu ile 2.1.4 RTMP ad ipucu sinyali

Tam SCTE-35 yük iletisinin HLS veya DASH bildirimine taşınmasını gerektiren daha gelişmiş bir yayın üretim iş akışıyla çalışırken, [Adobe-Primetime] belirtiminin "SCTE-35 modunu" kullanmak en iyisidir.  Bu mod, şirket içi bir Live Encoder 'a doğrudan gönderilen bant içi SCTE-35 sinyallerini destekler ve daha sonra [Adobe-Primetime] belirtiminde belirtilen "SCTE-35 modunu" kullanarak, bu sinyalleri RTMP akışına kodluyor. 

Genellikle SCTE-35 iletileri yalnızca şirket içi Kodlayıcıdaki MPEG-2 aktarım akışı (TS) girişlerinde görünebilir. SCTE-35 ' i içeren bir aktarım akış alma işlemini yapılandırma ve Adobe SCTE-35 modunda RTMP 'ye geçiş için etkinleştirme hakkında ayrıntılı bilgi edinmek için Kodlayıcı üreticinize danışın.

Bu senaryoda, **"Onadcue"** [AMF0] ileti türü kullanılarak şirket içi kodlayıcıdan aşağıdaki yükün gönderilmesi gerekir.

| Alan Adı | Alan türü | Gerekli mi? | Açıklamaları                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 'yu        | Dize     | Gerekli | Olay iletisi.  [SCTE-35] iletileri için, iletilerin HLS, kesintisiz ve Dash istemcilerine gönderilmesi için Base64 kodlamalı [RFC4648] ikili splice_info_section () olması gerekır.                                              |
| type       | Dize     | Gerekli | İleti düzenini tanımlayan bir URN veya URL. [SCTE-35] iletilerinde, iletilerin HLS, sorunsuz ve Dash istemcilerine gönderilmesi için [Adobe-Primetime] ile uyumlu olması **gerekir** . Bu, **"scte35"** olmalıdır. İsteğe bağlı olarak, [SCTE-35] iletisini bildirmek için URN "urn: scte: scte35:2013: bin" de kullanılabilir. |
| id         | Dize     | Gerekli | Splice veya segment tanımlayan benzersiz bir tanımlayıcı. İletinin bu örneğini tanımlar.  Denk semantiklere sahip iletiler aynı değere sahip olacaktır.|
| duration   | Number     | Gerekli | Biliniyorsa olay veya ad splice-segment süresi. Bilinmiyorsa, değer 0 olmalıdır .                                                                 |
| elapsed    | Number     | İsteğe Bağlı | ' İ ayarlamak için [SCTE-35] ad sinyali yinelendiğinde, bu alan, splice başladıktan sonra geçen sunum süresi kadar olacaktır. Birimler kesirli saniyedir. [SCTE-35] modunda, bu değer, splice veya segment için belirtilen özgün süreyi aşabilirler.                                                  |
| time       | Number     | Gerekli | Etkinliğin veya ad splice 'ın sunum süresi.  Sunum süresi ve süresi, [ISO-14496-12] Ek I içinde tanımlandığı şekilde, 1 veya 2 türündeki akış erişim noktalarıyla (SAP) hizalı **olmalıdır** . HLS çıkışı için saat ve süre, kesim sınırlarıyla hizalı **olmalıdır** . Aynı olay akışı içindeki farklı olay iletilerinin sunum süresi ve süresi çakışmamalıdır. Birimler kesirli saniyedir.

---
## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>On Live için 2.1.5 RTMP ad sinyali "onCuePoint" ile

Dinamik şirket içi kodlayıcı, RTMP sinyalinde ad işaretçilerini destekler. Azure Media Services Şu anda yalnızca RTMP için "onCuePoint" ad Işaret türünü destekliyor.  Bu, "**ad_markers**" öğesini "onCuePoint" olarak ayarlayarak, elete Media Live Encoder ayarları veya API 'SINDEKI Adobe RTMP grup ayarlarından etkinleştirilebilir.  Ayrıntılar için lütfen bkz. canlı belgelere bakın. RTMP grubunda bu özelliğin etkinleştirilmesi, Azure Media Services tarafından işlenecek olan Adobe RTMP çıktılarına SCTE-35 sinyallerini geçirecek.

"OnCuePoint" ileti türü, [Adobe-Flash-AS] içinde tanımlanmıştır ve, Elelılive RTMP çıktısından gönderildiğinde aşağıdaki yük yapısına sahiptir.


| Özellik  |Açıklama  |
|---------|---------|
|  name     | Ad, elete Live tarafından '**scte35**' olmalıdır. |
|time     |  Zaman çizelgesi sırasında, bir video dosyasında işaret noktasının gerçekleştiği saniye cinsinden süre |
| type     | İşaret noktası türü "**Event**" olarak ayarlanmalıdır. |
| parameters | Kimliği ve süresi de dahil olmak üzere SCTE-35 iletisindeki bilgileri içeren, ilişkilendirilebilir ad/değer çifti dizeleri dizisi. Bu değerler Azure Media Services tarafından ayrıştırılır ve bildirim dekorasyonu etiketine dahildir.  |


Bu ad işaretçisi modu kullanıldığında, HLS bildirim çıktısı Adobe "Simple" moduna benzerdir. 

### <a name="216-cancellation-and-updates"></a>2.1.6 iptali ve güncelleştirmeleri

Aynı sunum süresi ve KIMLIĞIYLE birden çok ileti gönderilerek iletiler iptal edilebilir veya güncelleştirilir. Sunum süresi ve KIMLIĞI, olayı benzersiz şekilde tanımlar ve ön alma kısıtlamalarını karşılayan belirli bir sunum saati için alınan son ileti, üzerinde işlem yapılan bir iletidir. Güncelleştirilmiş olay, daha önce alınan iletilerin yerini alır. Ön rulme kısıtlaması dört saniyedir. Sunum zamanından önce en az dört saniye alınan iletiler tarihinde işlem görür.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2 parçalanmış MP4 alma (Kesintisiz Akış)

Canlı akış alma hakkında gereksinimler için [MS-SSTR-Ingest] bölümüne bakın. Aşağıdaki bölümler, zamanlı sunum meta verilerinin alımı ile ilgili ayrıntıları sağlar.  Zamanlanmış sunum meta verileri, hem canlı sunucu bildirim kutusunda (bkz. MS-SSTR) hem de film kutusu 'nda (' Moov ') tanımlanmış bir seyrek iz olarak alınır.  

Her seyrek parça bir film parçası kutusundan (' Moof ') ve medya Data Box (' mdat ') oluşur; burada ' mdat ' kutusu ikili iletidir.

Reklamların çerçeveye doğru eklenmesini sağlamak için, kodlayıcının, bu parçayı, destesi 'nin eklenmesi gereken sunum sırasında bölünmesi gerekır.  Yeni oluşturulan bir ıDR çerçevesiyle başlayan yeni bir parça oluşturulmalıdır veya [ISO-14496-12] Ek I içinde tanımlandığı gibi 1 veya 2 türünde akış erişim noktaları (SAP) ile başlar. Bu, Azure Medya paketleyicisi 'nin bir HLS bildirimini ve yeni dönemin çerçeveye doğru katlanan sunum süresinde başladığı bir TIRE çok dönem bildirimini düzgün bir şekilde oluşturmasını sağlar.

### <a name="221-live-server-manifest-box"></a>2.2.1 canlı sunucu bildirim kutusu

Seyrek parça, canlı sunucu bildirim kutusunda bir  **\<\> TextStream** girişi ile bildirilmelidir ve aşağıdaki özniteliklerin ayarlanmış olması **gerekir** :

| **Öznitelik adı** | **Alan türü** | **Gerekli?** | **Açıklama**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Gerekli      | Bilinmeyen, değişken bit hızına sahip bir izleme belirten "0" olmalıdır.                                                                                                                                                                                                 |
| parentTrackName    | Dize         | Gerekli      | Seyrek izleme zaman kodlarının zaman ölçeği hizalı olduğu üst izlemenin adı olmalıdır. Üst izleme seyrek bir izleme olamaz.                                                                                                                    |
| Bildirimli Estoutput     | Boole değeri        | Gerekli      | Seyrek izlemenin sorunsuz istemci bildiriminde gömüleceğini göstermek için "true" olmalıdır.                                                                                                                                                               |
| Alt tür            | Dize         | Gerekli      | "DATA" dört karakter kodu **olmalıdır** .                                                                                                                                                                                                                        |
| Şemadaki             | Dize         | Gerekli      | İleti düzenini tanımlayan bir URN veya URL **olmalıdır** . [SCTE-35] iletileri için, iletilerin HLS 'ye gönderilmesi, sorunsuz ve Dash istemcilerine [SCTE-35] ile uyum sağlamak için bu "urn: scte: scte35:2013: bin" olması **gerekir** . |
| trackName          | Dize         | Gerekli      | Seyrek izlemenin adı **olmalıdır** . TrackName birden çok olay akışını aynı şemayla ayırt etmek için kullanılabilir. Her benzersiz olay akışı benzersiz bir izleme adına sahip **olmalıdır** .                                                                           |
| timescale          | Number         | İsteğe Bağlı      | Üst izlemenin zaman ölçeği olması **gerekir** .                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>2.2.2 film kutusu

Film kutusu (' Moov '), canlı sunucu bildirim kutusunu, seyrek bir izleme için akış üstbilgisinin bir parçası olarak izler.

' Moov ' kutusu, şu kısıtlamalara sahıp [ıso-14496-12] içinde tanımlanan bir **trackheaderbox (' tkhd ')** kutusu içermelidir:

| **Alan adı** | **Alan türü**          | **Gerekli?** | **Açıklama**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 64-bit işaretsiz tamsayı | Gerekli      | , İzleme kutusunda sıfır örnek olduğundan ve izleme kutusundaki örneklerin toplam süresi 0 olduğundan 0 olmalıdır. |

---

' Moov ' kutusu, şu kısıtlamalara sahıp [ıso-14496-12] içinde tanımlanan bir **handlerbox (' hdlr ')** içermelidir:

| **Alan adı** | **Alan türü**          | **Gerekli?** | **Açıklama**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32-bit işaretsiz tamsayı | Gerekli      | ' Meta ' olmalıdır. |

---

' Stsd ' kutusu, [ıso-14496-12] içinde tanımlanan kodlama adına sahip bir MetaDataSampleEntry kutusu içermelidir.  Örneğin, SCTE-35 iletileri için kodlama adı ' scte ' olmalıdır.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 film parçası kutusu ve medya Data Box

Seyrek parça parçaları bir film parçası kutusundan (' Moof ') ve medya Data Box (' mdat ') oluşur.

> [!NOTE]
> Reklamların çerçeveye doğru eklenmesini sağlamak için, kodlayıcının, bu parçayı, destesi 'nin eklenmesi gereken sunum sırasında bölünmesi gerekır.  Yeni oluşturulan bir ıDR çerçevesiyle başlayan yeni bir parça oluşturulmalıdır veya [ISO-14496-12] Ek I içinde tanımlandığı gibi 1 veya 2 türünde akış erişim noktaları (SAP) ile başlar
> 

MovieFragmentBox (' Moof ') kutusu, Şu alanlarla [MS-SSTR] içinde tanımlanan bir **TrackFragmentExtendedHeaderBox (' UUID ')** kutusu içermelidir:

| **Alan adı**         | **Alan türü**          | **Gerekli?** | **Açıklama**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64-bit işaretsiz tamsayı | Gerekli      | Olayın varış zamanı olmalıdır. Bu değer, iletiyi üst izleme ile hizalar.   |
| fragment_duration      | 64-bit işaretsiz tamsayı | Gerekli      | Olayın süresi **olmalıdır** . Sürenin bilinmiyor olduğunu göstermek için süre sıfır olabilir. |

---


MediaDataBox (' mdat ') kutusu aşağıdaki biçimde **olmalıdır** :

| **Alan adı**          | **Alan türü**                   | **Gerekli?** | **Açıklama**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32-bit işaretsiz tamsayı (uıısbf) | Gerekli      | ' Mdat ' kutusunun içindekilerin biçimini belirler. Tanınmayan sürümler yoksayılacak. Şu anda desteklenen tek sürüm 1 ' dir.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bit işaretsiz tamsayı (uıısbf) | Gerekli      | İletinin bu örneğini tanımlar. Denk semantiklere sahip iletiler aynı değere sahip olacaktır; diğer bir deyişle, aynı kimliğe sahip herhangi bir olay iletisi kutusunu işlemek yeterlidir.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bit işaretsiz tamsayı (uıısbf) | Gerekli      | TrackFragmentExtendedHeaderBox içinde belirtilen fragment_absolute_time toplamı ve presentation_time_delta, etkinliğin sunum süresi olmalıdır. Sunum süresi ve süresi, [ISO-14496-12] Ek I içinde tanımlandığı şekilde, 1 veya 2 türündeki akış erişim noktalarıyla (SAP) hizalı **olmalıdır** . HLS çıkışı için saat ve süre, kesim sınırlarıyla hizalı **olmalıdır** . Aynı olay akışı içindeki farklı olay iletilerinin sunum süresi ve süresi çakışmamalıdır. |
| message                 | bayt dizisi                       | Gerekli      | Olay iletisi. [SCTE-35] iletileri için, ileti ikili splice_info_section (). [SCTE-35] iletileri için, iletilerin HLS, kesintisiz ve Dash istemcilerine [scte-35] ile uyumlu olarak gönderilmesi için bu splice_info_section () olmalıdır. [SCTE-35] iletileri için, ikili splice_info_section () ' mdat ' kutusunun yüküyle, Base64 kodlamalı **değildir** .                                                            |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 iptali ve güncelleştirmeleri

Aynı sunum süresi ve KIMLIĞIYLE birden çok ileti gönderilerek iletiler iptal edilebilir veya güncelleştirilir.  Sunum süresi ve KIMLIĞI, olayı benzersiz şekilde tanımlar. Ön alma kısıtlamalarını karşılayan belirli bir sunum saati için alınan son ileti, üzerinde işlem yapılan iletidir. Güncelleştirilmiş ileti, daha önce alınan iletilerin yerini alır.  Ön rulme kısıtlaması dört saniyedir. Sunum zamanından önce en az dört saniye alınan iletiler tarihinde işlem görür. 


## <a name="3-timed-metadata-delivery"></a>3 zamanlanmış meta veri teslimi

Olay akışı verileri Media Services opaktır. Media Services yalnızca içe uç nokta ve istemci uç noktası arasında üç bilgi parçasını geçirir. Aşağıdaki özellikler istemciye [SCTE-35] ve/veya istemcinin akış protokolü ile uyumlu olarak dağıtılır:

1.  Scheme: ileti düzenini tanımlayan bir URN veya URL.
2.  Sunum süresi: medya zaman çizelgesindeki etkinliğin sunum süresi.
3.  Duration: olayın süresi.
4.  ID: olay için isteğe bağlı benzersiz bir tanımlayıcı.
5.  İleti – olay verileri.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 Microsoft Kesintisiz Akış bildirimi  

Seyrek ileti izlemeyi biçimlendirme hakkında ayrıntılı bilgi için bkz. seyrek parça işleme [MS-SSTR]. [SCTE35] iletileri için Kesintisiz Akış Base64 kodlamalı splice_info_section () bir seyrek parçaya çıktı.
StreamIndex bir "DATA" alt türüne sahip **olmalıdır** ve CustomAttributes Name = " Schema" ve value = "urn: scte: scte35:2013: bin" olan bir özniteliği içermelidir.

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Base64 kodlamalı [SCTE35] splice_info_section () gösteren kesintisiz Istemci bildirimi örneği
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3,2 Apple HLS bildirim dekorasyonu

Azure Media Services, canlı veya isteğe bağlı bir olay sırasında ad kullanılabilir bilgilerini sinyal etmek için aşağıdaki HLS bildirim etiketlerini destekler. 

- Apple HLS 'de tanımlandığı şekilde EXT-X-DATERANGE [RFC8216]
- [Adobe-Primetime] içinde tanımlanan EXT-X-CUE, bu mod "eski" olarak kabul edilir. Müşteriler mümkün olduğunda EXT-X-DATERANGE etiketini benimsemelidir.

Her etikete yönelik veri çıktısı, kullanılan sinyal moduna göre değişir. Örneğin, Adobe Simple modundaki RTMP alma, tam SCTE-35 base64 kodlu yükü içermez.

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 Apple HLS with Adobe Primetime EXT-X-DATERANGE (önerilir)

Apple HTTP Canlı Akışı [RFC8216] belirtimi [SCTE-35] ileti sinyaline izin verir. İletiler, "SCTE-35 ' i n-X-DATERANGE 'a" Mapping "başlıklı bir EXT-X-DATERANGE etiketi başına [RFC8216] bölümü içine eklenir  İstemci uygulama katmanı, M3U çalma listesini ayrıştırabilirler ve M3U etiketlerini işleyebilir ya da Apple Player Çerçevesi aracılığıyla olayları alabilir.  

Azure Media Services (sürüm 3 API) ' de **Önerilen** YAKLAŞıM [RFC8216] öğesini izlemek ve bildirimde bulunan [SCTE35] ad kullanımı için ext-X_DATERANGE etiketini kullanmaktır.

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS with Adobe Primetime EXT-X-CUE (eski)

Ayrıca, [Adobe-Primetime] "SCTE-35 modu" içinde tanımlanan şekilde EXT-X-CUE etiketini kullanan Azure Media Services (sürüm 2 ve 3 API) içinde sunulan bir "eski" uygulama de mevcuttur. Bu modda, Azure Media Services EXT-X-CUE etiketine Base64 kodlamalı [SCTE-35] splice_info_section () eklenir.  

"Eski" EXT-X-Cue etiketi aşağıda belirtildiği gibi tanımlanmıştır ve ayrıca [Adobe-Primetime] belirtiminde başvurulan 1.5 olabilir. Bu, yalnızca gerektiğinde eski SCTE35 sinyali için kullanılmalıdır, aksi takdirde önerilen etiket [RFC8216] içinde EXT-X-DATERANGE olarak tanımlanır. 

| **Öznitelik adı** | **Tür**                      | **Gerekli?**                             | **Açıklama**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 'YU                | alıntılanmış dize                 | Gerekli                                  | [RFC4648] bölümünde açıklandığı gibi Base64 kodlamalı dize olarak kodlanmış ileti. [SCTE-35] iletileri için bu, Base64 kodlamalı splice_info_section ().                                                                                                |
| TÜR               | alıntılanmış dize                 | Gerekli                                  | İleti düzenini tanımlayan bir URN veya URL. [SCTE-35] iletileri için, tür "scte35" özel değerini alır.                                                                                                                                |
| id                 | alıntılanmış dize                 | Gerekli                                  | Olay için benzersiz bir tanımlayıcı. İleti yapıldığında KIMLIK belirtilmezse Azure Media Services benzersiz bir kimlik oluşturulur.                                                                                                                                          |
| SÜRENIN           | ondalık kayan nokta sayısı | Gerekli                                  | Etkinliğin süresi. Bilinmiyorsa, değer 0 olmalıdır . Birimler, saniyeler içinde yapılır.                                                                                                                                                                                           |
| GEÇEN SÜRE            | ondalık kayan nokta sayısı | İsteğe bağlı, ancak kayan pencere için gerekli | Bir kayan sunu penceresini desteklemek için sinyal tekrarlandığında, bu alan, olayın başlamasından bu yana geçen sunum süresinin miktarı **olmalıdır** . Birimler kesirli saniyedir. Bu değer, splice veya segment için belirtilen özgün süreyi aşabilirler. |
| TIME               | ondalık kayan nokta sayısı | Gerekli                                  | Etkinliğin sunum süresi. Birimler kesirli saniyedir.                                                                                                                                                                                                                    |


HLS Player uygulama katmanı, türü ileti biçimini tanımlamak, iletinin kodunu çözmek, gerekli zaman dönüşümlerini uygulamak ve olayı işlemek için kullanır.  Olaylar, olay zaman damgasına göre üst izlemenin kesim çalma listesinde zaman içinde eşitlenir.  Bunlar, en yakın kesim (#EXTINF etiketi) öncesine eklenir.

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>"eski" Adobe Primetime EXT-X-CUE kullanarak 3.2.3 HLS segment çalma listesi örneği

Aşağıdaki örnekte, Adobe Primetime EXT-X-CUE etiketini kullanarak HLS bildirim dekorasyonu gösterilmektedir.  "CUE" parametresi, bu sinyalin Adobe SCTE-35 sinyal modunda RTMP 'yi kullanarak geldiğini veya Kesintisiz Akış SCTE-35 sinyal modu aracılığıyla geldiğini gösteren tam base64 kodlu SCTE-35 splice_info yükünü içerir. 

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>"eski" Adobe Primetime EXT-X-CUE için 3.2.4 HLS Ileti Işleme

Olaylar, her video ve ses parçasının kesim çalma listesinde sinyal edilir. EXT-X-CUE etiketinin konumu, her zaman ilk HLS segmentinden (bir ayırma veya kesim başlangıcı için) ya da zaman ve süre özniteliklerinin başvurduğu son HLS segmentinden (bir splice veya segment ucunda) hemen sonra olmalıdır [Adobe Primetime] tarafından gerektirilir.

Bir kayan sunu penceresi etkinleştirildiğinde, bir X-CUE etiketinin, her zaman segment çalma listesinde her zaman tam olarak açıklanabileceği kadar çok tekrarlanması **gerekir** ve geçen öznitelik, [Adobe Primetime] tarafından istenen şekilde, splice veya segment etkin.

Bir kayan sunu penceresi etkinleştirildiğinde, başvurduğu medya süresi kayan sunu penceresinden çıkarıldığında, EXT-X-CUE etiketleri kesim listesinden kaldırılır.

## <a name="33-dash-manifest-decoration-mpd"></a>3,3 DASH bildirim dekorasyonu (MPD)

[MPEGDASH] olayları bildirmek için üç yol sağlar:

1.  MPD EventStream 'de işaret eden olaylar
2.  Olay Ileti kutusu (' EMSG ') kullanılarak bant içinde işaret eden olaylar
3.  Hem 1 hem de 2 birleşimi

MPD EventStream 'de işaret edilen olaylar, VOD akışı için yararlıdır, çünkü istemciler, MPD indirildikten hemen sonra tüm olaylara erişebilirler. Ayrıca, aşağı akış SSAI satıcısının çok süreli bir MPD bildiriminden sinyalleri ayrıştırmak ve ad içeriğini dinamik olarak eklemesi gereken SSAı sinyali için de kullanışlıdır.  Bant içi (' EMSG ') çözümü, istemcilerin MPD 'yı yeniden indirmesi gerekmeyen canlı akış için yararlıdır veya istemci ile kaynak arasında SSAı bildirim işleme yoktur. 

DASH için Azure Media Services varsayılan davranış hem MPD EventStream içinde hem de olay Ileti kutusu (' EMSG ') kullanılarak bantta sinyaldir.

[RTMP] veya [MS-SSTR-Ingest] üzerinden alınan işaret iletileri, bant içi ' EMSG ' kutuları ve/veya-MPD EventStreams kullanılarak DASH olaylarına eşlenir. 

DASH için bant içi SCTE-35 sinyali, [SCTE-214-3] içinde tanımlanan tanım ve gereksinimleri ve ayrıca [DASH-IF-ıOP] Bölüm 13.12.2 (' SCTE35 Events ') içinde izler. 

Bant içi [SCTE-35] satır için, olay Ileti kutusu (' EMSG '), "urn: scte: scte35:2013: bin" öğesini kullanır. MPD bildirim dekorasyonu için EventStream derleme kodu "urn: scte: scte35:2014: XML + bin" kullanır.  Bu biçim, alma sırasında ulaşan tam SCTE-35 iletisinin ikili base64 kodlamalı bir çıkışını içeren olayının bir XML gösterimidir. 

[Scte-35] sec 6.7.4 (MPD) ve [SCTE-214-3] sec 7.3.2 (SCTE 35 Cue iletilerinin her biri) için 214-1 DASH içindeki/mative başvuru tanımları

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) EventStream sinyali

Olaylardaki bildirim (MPD) dekorasyonu, Period öğesinde görünen EventStream öğesi kullanılarak, MPD 'de bildirilecektir. Kullanılan ' urn: scte: scte35:2014: XML + bin "kullanılan değer kimliği.

> [!NOTE]
> Breçekimi amaçları için [SCTE-35], tamamen ayrıştırılmış bir işaret iletisinin sonuna alternatif olarak, sinyal. Binary öğesi (Signal. Spliceınfosection öğesi yerine) içinde Base64 kodlamalı bölümün kullanılmasına izin verir.
> Azure Media Services, MPD bildiriminde sinyal vermek için bu ' XML + bin ' yaklaşımını kullanır.
> Bu Ayrıca, [DASH-IF-ıOP]- [' ın ' ad ekleme olay akışları ' başlıklı bölümünde, IOP Kılavuzu '](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams) nu görmek için de önerilen yöntemdir
> 

EventStream öğesi aşağıdaki özniteliklere sahiptir:

| **Öznitelik adı** | **Tür**                | **Gerekli?** | **Açıklama**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | dize                  | Gerekli      | İleti düzenini tanımlar. Düzen, canlı sunucu bildirim kutusundaki Scheme özniteliğinin değerine ayarlanır. Değer, ileti düzenini TANıMLAYAN bir urn veya URL olmalıdır; Hizmetin, MPD 'de kısaltma için şu anda yalnızca "XML + bin" desteklediği için desteklenen çıkış dosyası sayısı "urn: scte: scte35:2014: XML + bin" ([SCTE-214-1] sec 6.7.4 (MPD) olmalıdır.  |
| value              | dize                  | İsteğe Bağlı      | İletinin semantiğini özelleştirmek için düzenin sahipleri tarafından kullanılan ek bir dize değeri. Birden çok olay akışını aynı şemayla ayırt etmek için, değerin olay akışı adı ([MS-SSTR-Ingest] için trackName veya [RTMP] ınest için AMF ileti adı) olarak ayarlanması **gerekir** . |
| Timescale          | 32-bit işaretsiz tamsayı | Gerekli      | Zaman ölçeği, saniye başına saat cinsinden.                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>3.3.2 örnek MPEG DASH bildirimi (MPD) EventStream kullanarak SCTE-35 sinyali

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> PresentationTime 'ın, iletinin varış süresi değil, dönem başlangıç zamanına göre değil, [SCTE-35] olayının sunum zamanı olduğunu unutmayın.
> [MPEGDASH], Event@presentationTime "dönemin başlangıcına göre etkinliğin sunum süresini belirtir.
> Sunum süresinin saniye cinsinden değeri, bu özniteliğin değerinin ve EventStream@timescale öznitelik değerinin bölümüdür.
> Yoksa, sunum zamanının değeri 0 ' dır.


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.3 MPEG DASH bant Içi olay Ileti kutusu sinyali

Bant içi olay akışı, MPD 'ın uyarlama kümesi düzeyinde bir InbandEventStream öğesi olmasını gerektirir.  Bu öğe, olay Ileti kutusunda (' EMSG ') da görüntülenen zorunlu bir ıiduri özniteliği ve isteğe bağlı zaman ölçeği özniteliği vardır.  MPD 'da tanımlanmayan düzen tanımlayıcılarına sahip olay iletisi kutuları bulunmamalıdır.

Bant içi [SCTE-35] satıral için, sinyaller , ' ' urn: scte: scte35:2013: bin "olarak kullanılmalıdır.
[SCTE-35]-bant içi iletilerin normative tanımları, [SCTE-214-3] sec 7.3.2 (SCTE 35 Cue iletilerinin satır sayısı) içinde tanımlanmıştır.

Aşağıdaki ayrıntılar, istemcinin [SCTE-214-3] ile uyumlu olması için ' EMSG ' içinde beklediği belirli değerleri özetler.

| **Alan adı**          | **Alan türü**          | **Gerekli?** | **Açıklama**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | dize                  | Gerekli      | İleti düzenini tanımlar. Düzen, canlı sunucu bildirim kutusundaki Scheme özniteliğinin değerine ayarlanır. Değer, ileti düzenini tanımlayan bir URN **olmalıdır** . [SCTE-35] iletileri için bu, [scte-214-3] ile uyumlu olması için "urn: scte: scte35:2013: bin" olmalıdır |
| Value                   | dize                  | Gerekli      | İletinin semantiğini özelleştirmek için düzenin sahipleri tarafından kullanılan ek bir dize değeri. Birden çok olay akışını aynı şemayla ayırt etmek için, bu değer olay akışının adına ayarlanır (düz alma için trackName veya RTMP alma için AMF ileti adı).                                                                  |
| Timescale               | 32-bit işaretsiz tamsayı | Gerekli      | ' EMSG ' kutusu içindeki süreler ve süre alanlarının saniye başına düşen zaman ölçeği.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32-bit işaretsiz tamsayı | Gerekli      | Bu kesimdeki, etkinliğin sunum süresinin ve en erken sunum zamanının medya sunumu zaman aralığı. Sunum süresi ve süresi, [ISO-14496-12] Ek I içinde tanımlandığı şekilde, 1 veya 2 türündeki akış erişim noktalarıyla (SAP) hizalı **olmalıdır** .                                                                                            |
| event_duration          | 32-bit işaretsiz tamsayı | Gerekli      | Olayın süresi veya 0xFFFFFFFF bilinmeyen bir süreyi belirtecek şekilde.                                                                                                                                                                                                                                                                                          |
| Id                      | 32-bit işaretsiz tamsayı | Gerekli      | İletinin bu örneğini tanımlar. Denk semantiklere sahip iletiler aynı değere sahip olacaktır. İleti yapıldığında KIMLIK belirtilmezse Azure Media Services benzersiz bir kimlik oluşturulur.                                                                                                                                                    |
| Message_data            | bayt dizisi              | Gerekli      | Olay iletisi. [SCTE-35] iletileri için ileti verisi, [SCTE-214-3] ile uyumlu ikili splice_info_section () olur |

### <a name="334-dash-message-handling"></a>3.3.4 DASH Ileti Işleme

Olaylar, hem video hem de ses parçaları için ' EMSG ' kutusu içinde bant içinde sinyal alınır.  Sinyal, presentation_time_delta 15 saniye veya daha az olan tüm kesim istekleri için oluşur. 

Bir kayan sunu penceresi etkinleştirildiğinde, olay iletisinin süresi ve süresinin toplamı, bildirimdeki medya verilerinin süresinden daha az olduğunda, MPD 'den olay iletileri kaldırılır.  Diğer bir deyişle, başvurdukları medya zamanı, kayan sunu penceresinden alındığında olay iletileri bildirimden kaldırılır.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. Çözücü satıcıları için SCTE-35 alma Uygulama Kılavuzu

Aşağıdaki kılavuzlar, bir kodlayıcı satıcısının bu belirtim uygulamasını etkileyebilecek yaygın sorunlardır.  Aşağıdaki yönergeler, bu şartın başkaları için uygulanmasını kolaylaştırmak amacıyla gerçek dünya ortağı geri bildirimlerine göre toplanmıştı. 

[SCTE-35] iletiler, [MS-SSTR-Ingest] için " **urn: scte: scte35:2013: bin"** düzenini ve [RTMP] alma için **"scte35"** türünü kullanarak ikili biçimde alınır. MPEG-2 aktarım akışı sunum zaman damgalarını (PTS) temel alan [SCTE-35] zamanlamalarının dönüştürülmesini kolaylaştırmak için, olay sunumu süresi (pts_time + pts_adjustment) ve medya zaman çizelgesi arasında bir eşleme sağlanır ( Düz alma için fragment_absolute_time alanı ve RTMP alma için saat alanı. 33 bit PTS değeri yaklaşık her 26,5 saatte bir yapıldığından eşleme gereklidir.

Kesintisiz Akış alma [MS-SSTR-alma], medya Data Box (' mdat ') [scte-35] içinde tanımlanan **splice_info_section ()** içermesi **gerekir** . 

RTMP alma için AMF iletisinin Cue özniteliği [SCTE-35] içinde tanımlanan Base64 kodlamalı **splice_info_section ()** olarak ayarlanır.  

İletiler yukarıda açıklanan biçimde olduğunda, yukarıda tanımlanan şekilde HLS, kesintisiz ve DASH istemcilerine gönderilir.  

Uygulamanızı Azure Media Services platformu ile sınarken, bir kodlama LiveEvent üzerinde teste geçmeden önce lütfen önce "doğrudan geçişli" LiveEvent ile testi başlatın.

---

## <a name="change-history"></a>Değişiklik geçmişi

| Date     | Değişiklikler                                                                            |
|----------|------------------------------------------------------------------------------------|
| 07/2/19  | SCTE35 desteği için RTMP alma düzenlendi, elete Live için RTMP "onCuePoint" eklendi | 
| 08/22/19 | Özel meta veriler için RTMP 'ye OnUserDataEvent eklemek üzere güncelleştirildi                         |

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını görüntüleyin.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
