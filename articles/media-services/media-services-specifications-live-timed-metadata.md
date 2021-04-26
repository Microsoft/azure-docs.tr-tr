---
title: Canlı akış 'da zamanlanmış meta verileri Azure Media Services sinyali verme
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
ms.openlocfilehash: f826ee9ef3c9fff0b721a9c79d3c12e0adbd5f7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336403"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Canlı akışta zamanlanmış meta verileri sinyal alma 

Son güncelleme tarihi: 2019-08-22

### <a name="conformance-notation"></a>Uygunluk gösterimi

"", "" Gerekli değildir "," gerekli "," Bu "," Bu "," zorunlu değil "," durum "ve" Isteğe bağlı "anahtar sözcükleri, RFC 2119 ' de açıklandığı gibi yorumlanmalıdır.

## <a name="1-introduction"></a>1. Giriş 

Bir istemci oynatıcısının reklamları veya özel meta veri olaylarını eklemeye işaret etmek için, yayımcılar genellikle videoda yerleşik olarak bulunan zaman aşımına uğramış meta verileri kullanır. Bu senaryoları etkinleştirmek için Media Services, canlı akış kanalının alma noktasındaki zaman aşımına uğramış meta verilerin istemci uygulamasına taşınması için destek sağlar.
Bu belirtim, canlı akış sinyalleri içindeki zaman aşımına uğramış meta veriler için Media Services tarafından desteklenen çeşitli modları özetler.

1. [SCTE-35] [SCTE-35], [SCTE-214-1], [SCTE-214-3] ve [RFC8216] tarafından belirtilen standartlarla uyumlu olan sinyal

2. [SCTE-35] RTMP ad sinyali için eski [Adobe-Primetime] belirtimiyle uyumlu sinyal.
   
3. [Scte-35] **olmayan** ve [ID3v2] veya uygulama geliştiricisi tarafından tanımlanan diğer özel şemaları taşıyabilecek iletiler için genel bir zaman uyumlu meta veri sinyali modu.

## <a name="11-terms-used"></a>1,1 terim kullanıldı

| Süre                | Tanım                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ad kesmesi            | Bir veya daha fazla reklamın teslim edilmek üzere zamanlanabileceği bir konum veya zaman noktası; kullanılabilir ve yerleştirme fırsatına benzer.                                                                                                                     |
| Ad karar hizmeti | kullanıcıya hangi ad (ler) ve süreler gösterildiğine karar veren dış hizmet. Hizmetler genellikle bir iş ortağı tarafından sağlanır ve bu belgenin kapsamı dışındadır.                                                                    |
| 'Yu                 | Yaklaşan ad kesmenin zaman ve parametrelerinin göstergesi. İpuçlarının bir ad kesmesi için bekleyen bir anahtarı belirtebileceğini, bir ad kesmesi içindeki bir sonraki ad için bekleyen geçiş ve bir ad sonundan Ana içeriğe bekleyen bir geçiş olduğunu unutmayın.           |
| Paketleyici            | "Akış uç noktası" Azure Media Services, DASH ve HLS için dinamik paketleme özellikleri sağlar ve medya sektöründe "paketleyicisi" olarak adlandırılır.                                                                              |
| Sunum süresi   | Bir olaya bir görüntüleyiciye sunulan zaman. Bu süre, medya zaman çizelgesinde bir izleyicinin olayı göreceği süreyi temsil eder. Örneğin, bir SCTE-35 splice_info () komut iletisinin sunum süresi splice_time (). |
| Varış saati        | Bir olay iletisinin ulaştığı zaman. Olay iletileri etkinliğin sunum zamanından önce gönderildiğinden, saat genellikle olayın sunum zamanından farklıdır.                                                    |
| Seyrek parça        | sürekli olmayan ve zaman bir üst veya denetim izlemeli eşitlenmiş medya izi.                                                                                                                                                  |
| Kaynak              | Azure Medya akışı hizmeti                                                                                                                                                                                                             |
| Kanal havuzu        | Azure Medya canlı akış hizmeti                                                                                                                                                                                                        |
| HLS                 | Apple HTTP Canlı Akışı Protokolü                                                                                                                                                                                                            |
| TILDE                | HTTP üzerinden dinamik uyarlamalı akış                                                                                                                                                                                                          |
| Kesintisiz              | Kesintisiz Akış Protokolü                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG 2 aktarım akışları                                                                                                                                                                                                                      |
| RTMP                | Real-Time multimedya Protokolü                                                                                                                                                                                                                 |
| uıısbf              | İşaretsiz tamsayı, önce en önemli bit.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1,2 normatif başvurular

Aşağıdaki belgeler, bu metinde başvuru aracılığıyla bu belgenin sağlamasını oluşturan sağlama sağlar. Tüm belgeler standartlar gövdelerinin düzeltmesine tabidir ve okuyucular aşağıda listelenen belgelerin en son sürümlerini uygulama olasılığını araştırmaya teşvik edilir. Okuyucular Ayrıca, başvurulan belgelerin yeni sürümlerinin Azure Media Services için zaman aşımına uğramış meta veri belirtiminin bu sürümü ile uyumlu olabileceğini de anımsatılır.


| Standart          | Tanım                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime dijital program ekleme sinyal belirtimi 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [FLASH ActionScript dil başvurusu](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Eylem Iletisi biçimi AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-ıOP]     | DASH sektör Forumu birlikte çalışma kılavuzu v 4,2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | HTTP Canlı Akışı için zamanlanmış meta veriler- [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ıD3]        | [Ortak medya uygulaması biçiminde zamanlanmış meta veriler (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 etiket sürümü 2.4.0  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/ıEC 14496-12: Bölüm 12 ISO taban medya dosyası biçimi, Onthedition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Bilgi teknolojisi-HTTP üzerinden dinamik uyarlamalı akış (DASH)--1. Bölüm: medya sunusu açıklaması ve kesim biçimleri. 2014 Mayıs. Yayımladığı. 'DEKI https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Bilgi teknolojisi--multimedya uygulama biçimi (MPEG-A)--Bölüm 19: kesimli medya için ortak medya uygulaması biçimi (CMAF). Ocak 2018. Yayımladığı. 'DEKI https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Bilgi teknolojisi--MPEG sistemleri teknolojileri--6. Bölüm: ISO tabanlı medya dosyası biçim dosyalarında ortak şifreleme. 2016 Şubat. Yayımladığı. 'DEKI https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Kesintisiz Akış Protokolü", 15 Mayıs 2014](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-ınest]  | [Azure Media Services parçalanmış MP4 canlı alma belirtimi](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | Sağ. Pantos, Ed.; W. Mayıs. HTTP Canlı Akışı. Ağustos 2017. Amaçlı. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Base16, Base32 ve Base64 veri kodlamaları- [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| RTMP            | ["Adobe 'un Real-Time mesajlaşma Protokolü", 21 Aralık 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35:2019-kablo için dijital program ekleme Cueiletisi- https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – IP-Based kablo hizmetleri için MPEG DASH 1. Bölüm: MPD kısıtlamaları ve uzantıları                                                                                                                 |
| [SCTE-214-3]      | IP-Based kablo hizmetleri için SCTE 214-3 2015 MPEG DASH 3. Bölüm: DASH/FF profili                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – olay zamanlama ve bildirim arabirimi                                                                                                                                                  |
| [SCTE-250]        | Olay ve sinyal yönetimi API 'SI (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. zamanlanmış meta veri alma

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
  
Canlı etkinlikler ve Paketleyici Azure Media Services, bu zaman aşımına uğramış meta veri sinyallerini alabilir ve bunları, HLS ve DASH gibi standartlara dayalı protokoller kullanarak istemci uygulamalarına ulaşan meta veri akışına dönüştürebilirsiniz.


## <a name="21-rtmp-timed-metadata"></a>2,1 RTMP zamanlı meta veriler

[RTMP] Protokolü, özel meta veriler ve SCTE-35 ad sinyalleri dahil çeşitli senaryolar için zamanlanmış meta veri sinyallerinin gönderilmesine izin verir. 

Reklam sinyalleri (işaret iletileri) [RTMP] akışı içine katıştırılmış [AMF0] Cue iletileri olarak gönderilir. İpucu iletileri gerçek olay veya [SCTE35] ad splice Signal gerçekleşmesi için bir süre önce gönderilebilir. Bu senaryoyu desteklemek için, olayın gerçek sunum zaman damgası, işaret iletisi içinde gönderilir. Daha fazla bilgi için bkz. [AMF0].

Aşağıdaki [AMF0] komutları, RTMP alma için Azure Media Services tarafından desteklenir:

- **Onuserdataevent** -özel meta veriler veya [ID3v2] için zamanlanmış meta veriler için kullanılır
- **Onadcue** -canlı akıştaki tanıtım yerleşimi fırsatının sinyali için öncelikli olarak kullanılır. İki ipucu biçimi desteklenir, basit bir mod ve "SCTE-35" modu. 
- **onCuePoint** -Live Encoder gibi belirli şirket içi donanım kodlayıcıları tarafından [SCTE35] ileti sinyali için desteklenir. 
  

Aşağıdaki tabloda, Media Services hem "basit" hem de [SCTE35] ileti modlarını alacak olan AMF ileti yükünün biçimi açıklanmaktadır.

[AMF0] iletisinin adı aynı türden birden çok olay akışını ayırt etmek için kullanılabilir.  Hem [SCTE-35] iletileri hem de "basit" modu için, AMF iletisinin adı [Adobe-Primetime] belirtiminde gerekli olduğu gibi "onAdCue" olmalıdır.  Aşağıda listelenmeyen tüm alanlar alma sırasında Azure Media Services yok sayılır.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>"onUserDataEvent" kullanarak özel meta verilerle 2.1.1 RTMP

Yukarı akış kodlayıcısından, IP kameranıza, drone 'dan veya RTMP protokolünü kullanarak cihazdan özel meta veri akışları sağlamak istiyorsanız, "onUserDataEvent" [AMF0] veri iletisi komut türünü kullanın.

**"Onuserdataevent"** veri iletisi komutu, Media Services tarafından yakalanıp bant içi dosya biçiminde paketlenmesi ve HLS, DASH ve kesintisiz akış için bildirim almak üzere aşağıdaki tanım ile bir ileti yükü taşımalıdır.
Her 0,5 saniye (500ms) veya canlı akışla ilgili kararlılık sorunlarından daha sık, zaman aşımına uğramamış veri iletileri göndermeniz önerilir. Çerçeve düzeyinde meta veriler sağlamanız gerekiyorsa, her ileti birden çok kareden meta verileri toplayabilir. Çoklu bit hızı akışları gönderiyorsanız, meta verileri tek bir bit hızında sağlamanız önerilir, bu da yalnızca bant genişliğini azaltmak ve video/ses işlemeyle ilgili girişimlerden kaçınmak için gerekir. 

**"Onuserdataevent"** için yük BIR [MPEGDASH] EVENTSTREAM xml biçim iletisi olmalıdır. Bu, HLS veya DASH protokolleri üzerinden sunulan CMAF [MPEGCMAF] içeriği için bantta ' EMSG ' yükleri içinde taşınılabilen özel tanımlı şemaları kolayca geçirmeye olanak sağlar. Her DASH olay akışı iletisi, URN ileti düzeni tanımlayıcısı olarak işlev gören ve iletinin yükünü tanımlayan bir IBir tek düzen içerir. https://aomedia.org/emsg/ID3[Scte-35] için "" [ID3v2] veya **urn: scte: scte35:2013: bin** gibi bazı şemalar, birlikte çalışabilirlik için sektör yarışma göre standartlaştırılmıştır. Herhangi bir uygulama sağlayıcısı, denetleyedikleri URL 'YI (sahip etki alanı) kullanarak kendi özel düzenlerini tanımlayabilir ve tercih ettikleri URL 'de bir belirtim sağlayabilir. Bir oyuncunun tanımlı düzen için bir işleyicisi varsa, bu, yükü ve Protokolü anlaması gereken tek bileşendir.

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
| Şema KIMLIĞI URI 'SI                 | Description                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https: \/ /aomedia.org/EMSG/ID3 | [ID3v2] meta verilerinin CMAF ile uyumlu [MPEGCMAF] parçalanmış MP4 içinde zaman aşımına uğramış bir meta veriler olarak nasıl taşınabilir açıklar. Daha fazla bilgi için bkz [. ortak medya uygulaması biçimindeki (CMAF) zaman aşımına uğrayan meta veriler](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Olay işleme ve bildirim sinyali verme

Geçerli bir **"Onuserdataevent"** olayının alınması sırasında, Azure Media Services EventStreamType ([MPEGDASH] içinde tanımlanmıştır) ile eşleşen GEÇERLI bir XML yükünü ARAYACAKTıR, XML yükünü ayrıştırır ve canlı arşiv ve Media Services paketleyicisi ile iletiminde depolama için BIR [MPEGCMAF] mp4 Fragment ' EMSG ' sürüm 1 kutusuna dönüştürür.   Paketleyici, canlı akıştaki ' EMSG ' kutusunu algılar ve şu şekilde görünür:

- (a) "dinamik olarak paketleyin" HLS istemcilerinin HLS için zaman uyumsuz meta veri belirtimi [HLS-TMD] veya
- (b) hmaf parçaları içinde HLS veya DASH aracılığıyla teslim etmek için ile geçiş yapın veya 
- (c) Kesintisiz Akış [MS-SSTR] aracılığıyla teslim için bir seyrek izleme sinyaline dönüştürün.

HLS için bant içi ' EMSG ' biçimine ek olarak, DASH (MPD) ve Kesintisiz Akış için bildirimler, bant içi olay akışlarına (Kesintisiz Akış aynı zamanda seyrek akış izleme olarak da bilinir) bir başvuru içerir.

Bireysel olaylar veya veri yükleri doğrudan HLS, kesik çizgi veya düzgün bildirimlerde çıktı DEĞILDIR. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>OnUserDataEvent olayları için ek bilgilendirici kısıtlamalar ve varsayılanlar

- EventStream öğesinde zaman ölçeği ayarlanmamışsa, varsayılan olarak RTMP 1 kHz zaman ölçeği kullanılır
- OnUserDataEvent iletisinin teslimi her 500 MS Max ile sınırlıdır. Olayları daha sık gönderirseniz, bant genişliğini ve canlı akışın kararlılığını etkileyebilir

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>"onAdCue" ile 2.1.2 'yi RTMP ad ipucu sinyali

Azure Media Services, canlı akıştaki çeşitli gerçek zamanlı eşitlenmiş meta verileri göstermek için kullanılabilecek çeşitli [AMF0] ileti türlerini dinleyebilir ve yanıtlayabilir.  [Adobe-Primetime] belirtimi, "Simple" ve "SCTE-35" modu olarak adlandırılan iki işaret türünü tanımlar. "Basit" modu için Media Services, "basit mod" sinyali için tanımlanan tabloyla eşleşen bir yük kullanarak "onAdCue" adlı tek bir AMF ipucu iletisini destekler.  

Aşağıdaki bölümde, HLS, DASH ve Microsoft Kesintisiz Akış istemci bildirimine yapılacak temel bir "spliceOut" ad sinyalini işaret etmek için kullanılabilen RTMP "basit" mod "yükü gösterilmektedir. Bu, müşterinin karmaşık bir SCTE-35 tabanlı ad sinyal dağıtımı veya ekleme sistemine sahip olmadığı ve bir API aracılığıyla işaret iletisinde göndermek için temel bir şirket içi kodlayıcı kullandığı senaryolarda çok yararlı olur. Genellikle şirket içi kodlayıcı, bu sinyalin tetiklenmesi için REST tabanlı bir API 'yi destekleyecektir. Bu, videoya bir ıDR çerçevesi ekleyerek ve yeni bir GOP başlatarak video akışını da "splice-Condition" olarak destekler.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>"onAdCue" ile 2.1.3 RTMP ad ipucu sinyali-basit mod

| Alan Adı | Alan Türü | Gerekli mi? | Tanımlarını                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| tür       | Dize     | Gerekli  | Olay iletisi.  Basit bir mod splice belirlemek için "SpliceOut" olacaktır.                                                                                                                                                                                                         |
| kimlik         | Dize     | Gerekli  | Splice veya segment tanımlayan benzersiz bir tanımlayıcı. İletinin bu örneğini tanımlar                                                                                                                                                                                       |
| süre   | Sayı     | Gerekli  | Splice 'ın süresi. Birimler kesirli saniyedir.                                                                                                                                                                                                                           |
| elapsed    | Sayı     | İsteğe Bağlı  | Sinyal, içindeki ayarlamayı desteklemek için yinelendiğinde, bu alan, splice başladıktan sonra geçen sunum süresinin miktarı olacaktır. Birimler kesirli saniyedir. Basit mod kullanılırken bu değer, splice 'ın orijinal süresini aşmamalıdır. |
| time       | Sayı     | Gerekli  | , Sunum süresi içinde, splice 'ın saati olacaktır. Birimler kesirli saniyedir.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Adobe RTMP basit modu kullanılırken örnek MPEG DASH bildirim çıkışı

Bkz [. Adobe Simple modunu kullanarak örnek 3.3.2.1 MPEG Dash. mpd EventStream](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Bkz. [tek dönem ve Adobe Simple modundaki örnek 3.3.3.1 Dash bildirimi ](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Adobe RTMP basit modu kullanılırken örnek HLS bildirim çıkışı

Bkz. [Adobe Simple Mode ve ext-X-Cue etiketini kullanarak örnek 3.2.2 HLS bildirimi](#322-apple-hls-with-adobe-primetime-ext-x-cue)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>"onAdCue"-SCTE-35 modu ile 2.1.4 RTMP ad ipucu sinyali

Tam SCTE-35 yük iletisinin HLS veya DASH bildirimine taşınmasını gerektiren daha gelişmiş bir yayın üretim iş akışıyla çalışırken, [Adobe-Primetime] belirtiminin "SCTE-35 modunu" kullanmak en iyisidir.  Bu mod, şirket içi bir Live Encoder 'a doğrudan gönderilen bant içi SCTE-35 sinyallerini destekler ve daha sonra [Adobe-Primetime] belirtiminde belirtilen "SCTE-35 modunu" kullanarak, bu sinyalleri RTMP akışına kodluyor. 

Genellikle SCTE-35 iletileri yalnızca şirket içi Kodlayıcıdaki MPEG-2 aktarım akışı (TS) girişlerinde görünebilir. SCTE-35 ' i içeren bir aktarım akış alma işlemini yapılandırma ve Adobe SCTE-35 modunda RTMP 'ye geçiş için etkinleştirme hakkında ayrıntılı bilgi edinmek için Kodlayıcı üreticinize danışın.

Bu senaryoda, **"Onadcue"** [AMF0] ileti türü kullanılarak şirket içi kodlayıcıdan aşağıdaki yükün gönderilmesi gerekir.

| Alan Adı | Alan Türü | Gerekli mi? | Tanımlarını                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 'yu        | Dize     | Gerekli  | Olay iletisi.  [SCTE-35] iletileri için, iletilerin HLS, kesintisiz ve Dash istemcilerine gönderilmesi için Base64 kodlamalı [RFC4648] ikili splice_info_section () olması gerekır.                                                                                                                                                                                                                               |
| tür       | Dize     | Gerekli  | İleti düzenini tanımlayan bir URN veya URL. [SCTE-35] iletilerinde, iletilerin HLS, sorunsuz ve Dash istemcilerine gönderilmesi için [Adobe-Primetime] ile uyumlu olması **gerekir** . Bu, **"scte35"** olmalıdır. İsteğe bağlı olarak, [SCTE-35] iletisini bildirmek için URN "urn: scte: scte35:2013: bin" de kullanılabilir.                                                                                                        |
| kimlik         | Dize     | Gerekli  | Splice veya segment tanımlayan benzersiz bir tanımlayıcı. İletinin bu örneğini tanımlar.  Denk semantiklere sahip iletiler aynı değere sahip olacaktır.                                                                                                                                                                                                                                                       |
| süre   | Sayı     | Gerekli  | Biliniyorsa olay veya ad splice-segment süresi. Bilinmiyorsa **, değer 0 olmalıdır.**                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Sayı     | İsteğe Bağlı  | ' İ ayarlamak için [SCTE-35] ad sinyali yinelendiğinde, bu alan, splice başladıktan sonra geçen sunum süresi kadar olacaktır. Birimler kesirli saniyedir. [SCTE-35] modunda, bu değer, splice veya segment için belirtilen özgün süreyi aşabilirler.                                                                                                                   |
| time       | Sayı     | Gerekli  | Etkinliğin veya ad splice 'ın sunum süresi.  Sunum süresi ve süresi, [ISO-14496-12] Ek I içinde tanımlandığı şekilde, 1 veya 2 türündeki akış erişim noktalarıyla (SAP) hizalı **olmalıdır** . HLS çıkışı için saat ve süre, kesim sınırlarıyla hizalı **olmalıdır** . Aynı olay akışı içindeki farklı olay iletilerinin sunum süresi ve süresi çakışmamalıdır. Birimler kesirli saniyedir. |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>SCTE-35 mod sinyaliyle örnek HLS manifest. M3U8
Bkz. [SCTE-35 Ile Bölüm 3.2.1.1 örnek HLS bildirimi](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>On Live için 2.1.5 RTMP ad sinyali "onCuePoint" ile

Dinamik şirket içi kodlayıcı, RTMP sinyalinde ad işaretçilerini destekler. Azure Media Services Şu anda yalnızca RTMP için "onCuePoint" ad Işaret türünü destekliyor.  Bu, "**ad_markers**" öğesini "onCuePoint" olarak ayarlayarak, elete Media Live Encoder ayarları veya API 'SINDEKI Adobe RTMP grup ayarlarından etkinleştirilebilir.  Ayrıntılar için lütfen bkz. canlı belgelere bakın. RTMP grubunda bu özelliğin etkinleştirilmesi, Azure Media Services tarafından işlenecek olan Adobe RTMP çıktılarına SCTE-35 sinyallerini geçirecek.

"OnCuePoint" ileti türü, [Adobe-Flash-AS] içinde tanımlanmıştır ve, Elelılive RTMP çıktısından gönderildiğinde aşağıdaki yük yapısına sahiptir.


| Özellik   | Açıklama                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | Ad, elete Live tarafından '**scte35**' olmalıdır.                                                                                                                                                                              |
| time       | Zaman çizelgesi sırasında, bir video dosyasında işaret noktasının gerçekleştiği saniye cinsinden süre                                                                                                                                           |
| tür       | İşaret noktası türü "**Event**" olarak ayarlanmalıdır.                                                                                                                                                                             |
| parameters | Kimliği ve süresi de dahil olmak üzere SCTE-35 iletisindeki bilgileri içeren ad/değer çifti dizelerinin ilişkilendirilebilir dizisi. Bu değerler Azure Media Services tarafından ayrıştırılır ve bildirim dekorasyonu etiketine dahildir. |


Bu ad işaretçisi modu kullanıldığında, HLS bildirim çıktısı Adobe "Simple" moduna benzerdir.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Örnek MPEG DASH MPD, tek dönem, Adobe basit mod sinyalleri

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Örnek HLS çalma listesi, Adobe basit mod, EXT-X-CUE etiketini (kesilen "...") kullanarak bildirir breçekimi için)

Aşağıdaki örnek, Adobe "Simple" mod sinyallerini ve eski [Adobe Primetime] EXT-X-CUE etiketini kullanarak bir RTMP alma akışı için Media Services dinamik paketleyicisi çıktısını gösterir.  

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...
```

### <a name="216-cancellation-and-updates"></a>2.1.6 iptali ve güncelleştirmeleri

Aynı sunum süresi ve KIMLIĞIYLE birden çok ileti gönderilerek iletiler iptal edilebilir veya güncelleştirilir. Sunum süresi ve KIMLIĞI, olayı benzersiz şekilde tanımlar ve ön alma kısıtlamalarını karşılayan belirli bir sunum saati için alınan son ileti, üzerinde işlem yapılan bir iletidir. Güncelleştirilmiş olay, daha önce alınan iletilerin yerini alır. Ön rulme kısıtlaması dört saniyedir. Sunum zamanından önce en az dört saniye alınan iletiler tarihinde işlem görür.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2 parçalanmış MP4 alma (Kesintisiz Akış)

Canlı akış alma hakkında gereksinimler için [MS-SSTR-Ingest] bölümüne bakın. Aşağıdaki bölümler, zamanlı sunum meta verilerinin alımı ile ilgili ayrıntıları sağlar.  Zamanlanmış sunum meta verileri, hem canlı sunucu bildirim kutusunda (bkz. MS-SSTR) hem de film kutusu 'nda (' Moov ') tanımlanmış bir seyrek iz olarak alınır.  

Her seyrek parça bir film parçası kutusundan (' Moof ') ve medya Data Box (' mdat ') oluşur; burada ' mdat ' kutusu ikili iletidir.

Reklamların çerçeveye doğru eklenmesini sağlamak için, kodlayıcının, bu parçayı, destesi 'nin eklenmesi gereken sunum sırasında bölünmesi gerekır.  Yeni oluşturulan bir ıDR çerçevesiyle başlayan yeni bir parça oluşturulmalıdır veya [ISO-14496-12] Ek I içinde tanımlandığı gibi 1 veya 2 türünde akış erişim noktaları (SAP) ile başlar.
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>2.2.1 canlı sunucu bildirim kutusu

Seyrek **izleme,** canlı sunucu bildirim kutusunda bir girdiyle bildirilmelidir **\<textstream\>** ve aşağıdaki özniteliklerin ayarlanmış olması **gerekir** :

| **Öznitelik adı** | **Alan türü** | **Gerekli mi?** | **Açıklama**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Sayı         | Gerekli      | Bilinmeyen, değişken bit hızına sahip bir izleme belirten "0 **" olmalıdır.**                                                                                                                                                          |
| parentTrackName    | Dize         | Gerekli      | Seyrek izleme zaman kodlarının zaman ölçeği hizalı olduğu üst izlemenin **adı olmalıdır.** Üst izleme seyrek bir izleme olamaz.                                                                             |
| Bildirimli Estoutput     | Boole        | Gerekli      | Seyrek izlemenin sorunsuz istemci bildiriminde gömüleceğini göstermek için "true **" olmalıdır.**                                                                                                                        |
| Subtype            | Dize         | Gerekli      | "DATA" dört karakter kodu **olmalıdır** .                                                                                                                                                                                  |
| Düzen             | Dize         | Gerekli      | İleti düzenini tanımlayan bir URN veya URL **olmalıdır** . [SCTE-35] iletileri için, iletilerin HLS 'ye gönderilmesi, sorunsuz ve Dash istemcilerine [SCTE-35] ile uyum sağlamak için bu "urn: scte: scte35:2013: bin" olması **gerekir** . |
| trackName          | Dize         | Gerekli      | Seyrek izlemenin adı **olmalıdır** . TrackName birden çok olay akışını aynı şemayla ayırt etmek için kullanılabilir. Her benzersiz olay akışı benzersiz bir izleme adına sahip **olmalıdır** .                                |
| zaman          | Sayı         | İsteğe Bağlı      | Üst izlemenin zaman ölçeği olması **gerekir** .                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 film kutusu

Film kutusu (' Moov '), canlı sunucu bildirim kutusunu, seyrek bir izleme için akış üstbilgisinin bir parçası olarak izler.

' Moov **' kutusu,** şu kısıtlamalara sahıp [ıso-14496-12] içinde tanımlanan bir **trackheaderbox (' tkhd ')** kutusu içermelidir:

| **Alan adı** | **Alan türü**          | **Gerekli mi?** | **Açıklama**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| süre       | 64-bit işaretsiz tamsayı | Gerekli      | , İzleme kutusunda sıfır örnek olduğundan ve izleme kutusundaki örneklerin toplam süresi 0 olduğundan **0 olmalıdır.** |

---

' Moov **' kutusu,** şu kısıtlamalara sahıp [ıso-14496-12] içinde tanımlanan bir **handlerbox (' hdlr ')** içermelidir:

| **Alan adı** | **Alan türü**          | **Gerekli mi?** | **Açıklama**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32-bit işaretsiz tamsayı | Gerekli      | ' Meta **' olmalıdır.** |

---

' Stsd ' kutusu, [ISO-14496-12] içinde tanımlanan kodlama adına sahip bir MetaDataSampleEntry **kutusu içermelidir.**  Örneğin, SCTE-35 iletileri için kodlama adı ' scte **' olmalıdır.**

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 film parçası kutusu ve medya Data Box

Seyrek parça parçaları bir film parçası kutusundan (' Moof ') ve medya Data Box (' mdat ') oluşur.

> [!NOTE]
> Reklamların çerçeveye doğru eklenmesini sağlamak için, kodlayıcının, bu parçayı, destesi 'nin eklenmesi gereken sunum sırasında bölünmesi gerekır.  Yeni oluşturulan bir ıDR çerçevesiyle başlayan yeni bir parça oluşturulmalıdır veya [ISO-14496-12] Ek I içinde tanımlandığı gibi 1 veya 2 türünde akış erişim noktaları (SAP) ile başlar
> 

MovieFragmentBox (' Moof ') kutusu, Şu alanlarla [MS-SSTR] içinde tanımlanan bir **TrackFragmentExtendedHeaderBox (' UUID ')** **kutusu içermelidir:**

| **Alan adı**         | **Alan türü**          | **Gerekli mi?** | **Açıklama**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64-bit işaretsiz tamsayı | Gerekli      | Olayın varış **zamanı olmalıdır.** Bu değer, iletiyi üst izleme ile hizalar.           |
| fragment_duration      | 64-bit işaretsiz tamsayı | Gerekli      | Olayın süresi **olmalıdır** . Sürenin bilinmiyor olduğunu göstermek için süre sıfır olabilir. |

---


MediaDataBox (' mdat ') kutusu aşağıdaki biçimde **olmalıdır** :

| **Alan adı**          | **Alan türü**                   | **Gerekli mi?** | **Açıklama**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sürüm                 | 32-bit işaretsiz tamsayı (uıısbf) | Gerekli      | ' Mdat ' kutusunun içindekilerin biçimini belirler. Tanınmayan sürümler yoksayılacak. Şu anda desteklenen tek sürüm 1 ' dir.                                                                                                                                                                                                                                                                                                                                                                      |
| kimlik                      | 32-bit işaretsiz tamsayı (uıısbf) | Gerekli      | İletinin bu örneğini tanımlar. Denk semantiklere sahip iletiler aynı değere sahip olacaktır; diğer bir deyişle, aynı kimliğe sahip herhangi bir olay iletisi kutusunu işlemek yeterlidir.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bit işaretsiz tamsayı (uıısbf) | Gerekli      | TrackFragmentExtendedHeaderBox içinde belirtilen fragment_absolute_time toplamı ve presentation_time_delta, etkinliğin sunum süresi **olmalıdır** . Sunum süresi ve süresi, [ISO-14496-12] Ek I içinde tanımlandığı şekilde, 1 veya 2 türündeki akış erişim noktalarıyla (SAP) hizalı **olmalıdır** . HLS çıkışı için saat ve süre, kesim sınırlarıyla hizalı **olmalıdır** . Aynı olay akışı içindeki farklı olay iletilerinin sunum süresi ve süresi **çakışmamalıdır.** |
| message                 | bayt dizisi                       | Gerekli      | Olay iletisi. [SCTE-35] iletileri için, ileti ikili splice_info_section (). [SCTE-35] iletileri için, iletilerin HLS, kesintisiz ve Dash istemcilerine [SCTE-35] ile uyumlu olarak gönderilmesi için splice_info_section () olması **gerekir** . [SCTE-35] iletileri için, ikili splice_info_section () ' mdat ' kutusunun yüküyle ve Base64 kodlamalı **değildir** .                                                                                                                     |

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

Seyrek ileti izlemeyi biçimlendirme hakkında ayrıntılı bilgi için bkz. seyrek parça işleme [MS-SSTR]. [SCTE35] iletileri için Kesintisiz Akış Base64 kodlamalı splice_info_section () bir seyrek parçaya çıkış olur.
StreamIndex bir "DATA" alt türüne sahip **olmalıdır** ve CustomAttributes Name = "Schema" ve value = "urn: scte: scte35:2013: bin" olan bir **özniteliği içermelidir.**

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Base64 kodlamalı [SCTE35] splice_info_section () gösteren kesintisiz Istemci bildirimi örneği
```xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="32-apple-hls-manifest-decoration"></a>3,2 Apple HLS bildirim dekorasyonu

Azure Media Services, canlı veya isteğe bağlı bir olay sırasında ad kullanılabilir bilgilerini sinyal etmek için aşağıdaki HLS bildirim etiketlerini destekler. 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- [Adobe Primetime] içinde tanımlandığı şekilde EXT-X-CUE
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

Her etikete yönelik veri çıktısı, kullanılan sinyal moduna göre değişir. Örneğin, Adobe Simple modundaki RTMP alma, tam SCTE-35 base64 kodlu yükü içermez.

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1 örnek HLS manifest. M3U8 EXT-X-CUE sinyallerini gösteren 35

Media Services dinamik Paketleyiciyi izleyen aşağıdaki örnek HLS bildirim çıktısı, SCTE35 modunda [Adobe Primetime] için EXT-X-CUE etiketini gösterir. 

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Apple HLS with Adobe Primetime EXT-X-CUE

Media Services (sürüm 2 ve 3 API), [Adobe-Primetime] "SCTE-35 modu" içinde tanımlanan şekilde EXT-X-CUE etiketinin çıkışını destekler. Bu modda, Azure Media Services EXT-X-CUE etiketine Base64 kodlamalı [SCTE-35] splice_info_section () eklenir.  

"Eski" EXT-X-Cue etiketi aşağıda belirtildiği gibi tanımlanmıştır ve ayrıca [Adobe-Primetime] belirtiminde başvurulan 1.5 olabilir. Bu, yalnızca gerektiğinde eski SCTE35 sinyali için kullanılmalıdır, aksi takdirde önerilen etiket [RFC8216] içinde EXT-X-DATERANGE olarak tanımlanır. 

| **Öznitelik adı** | **Tür**                      | **Gerekli mi?**                             | **Açıklama**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 'YU                | alıntılanmış dize                 | Gerekli                                  | [RFC4648] bölümünde açıklandığı gibi Base64 kodlamalı dize olarak kodlanmış ileti. [SCTE-35] iletileri için bu, Base64 kodlamalı splice_info_section ().                                                                                                                                      |
| TÜR               | alıntılanmış dize                 | Gerekli                                  | İleti düzenini tanımlayan bir URN veya URL. [SCTE-35] iletileri için, tür "scte35" özel değerini alır.                                                                                                                                                                          |
| ID                 | alıntılanmış dize                 | Gerekli                                  | Olay için benzersiz bir tanımlayıcı. İleti yapıldığında KIMLIK belirtilmezse Azure Media Services benzersiz bir kimlik oluşturulur.                                                                                                                                              |
| DURATION           | ondalık kayan nokta sayısı | Gerekli                                  | Etkinliğin süresi. Bilinmiyorsa **, değer 0 olmalıdır.** Birimler, saniyeler içinde yapılır.                                                                                                                                                                                           |
| GEÇECEK            | ondalık kayan nokta sayısı | İsteğe bağlı, ancak kayan pencere için gerekli | Bir kayan sunu penceresini desteklemek için sinyal tekrarlandığında, bu alan, olayın başlamasından bu yana geçen sunum süresinin miktarı **olmalıdır** . Birimler kesirli saniyedir. Bu değer, splice veya segment için belirtilen özgün süreyi aşabilirler. |
| TIME               | ondalık kayan nokta sayısı | Gerekli                                  | Etkinliğin sunum süresi. Birimler kesirli saniyedir.                                                                                                                                                                                                                        |

HLS Player uygulama katmanı, türü ileti biçimini tanımlamak, iletinin kodunu çözmek, gerekli zaman dönüşümlerini uygulamak ve olayı işlemek için kullanır.  Olaylar, olay zaman damgasına göre üst izlemenin kesim çalma listesinde zaman içinde eşitlenir.  Bunlar, en yakın kesim (#EXTINF etiketi) öncesine eklenir.


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>Adobe Primetime EXT-X-CUE kullanarak 3.2.3 HLS. M3U8 manifest örneği

Aşağıdaki örnekte, Adobe Primetime EXT-X-CUE etiketini kullanarak HLS bildirim dekorasyonu gösterilmektedir.  "CUE" parametresi yalnızca tür ve Duration özelliklerini içerir. Bu, bu, Adobe "basit" mod sinyali kullanan bir RTMP kaynağı olduğu anlamına gelir.  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>Adobe Primetime EXT-X-CUE için 3.2.4 HLS Ileti Işleme

Olaylar, her video ve ses parçasının kesim çalma listesinde sinyal edilir. EXT-X-CUE etiketinin konumu, her zaman ilk HLS segmentinden (bir ayırma veya kesim başlangıcı için) ya da zaman ve süre özniteliklerinin başvurduğu en son HLS segmentinden (Splice ın veya segment bitişi için) hemen hemen sonra ([Adobe-Primetime] için gereklidir **) olmalıdır.**

Bir kayan sunu penceresi etkinleştirildiğinde, bir X-CUE etiketinin, her zaman segment çalma listesinde her zaman tam olarak açıklanabileceği kadar çok tekrarlanması **gerekir** ve [Adobe-Primetime] tarafından istenen şekilde, splice veya segment 'in etkin olduğu süre miktarını göstermek için **geçen öznitelik kullanılmalıdır** .

Bir kayan sunu penceresi etkinleştirildiğinde, başvurduğu medya süresi kayan sunu penceresinden çıkarıldığında, EXT-X-CUE etiketleri kesim listesinden kaldırılır.

## <a name="33-dash-manifest-decoration-mpd"></a>3,3 DASH bildirim dekorasyonu (MPD)

[MPEGDASH] olayları bildirmek için üç yol sağlar:

1.  MPD EventStream 'de işaret eden olaylar
2.  Olay Ileti kutusu (' EMSG ') kullanılarak bant içinde işaret eden olaylar
3.  Hem 1 hem de 2 birleşimi

MPD EventStream 'de işaret edilen olaylar, VOD akışı için yararlıdır, çünkü istemciler, MPD indirildikten hemen sonra tüm olaylara erişebilirler. Ayrıca, aşağı akış SSAI satıcısının, MPD bildiriminden sinyalleri ayrıştırmak ve ad içeriğini dinamik olarak eklemesi gereken SSAı sinyali için de kullanışlıdır.  Bant içi (' EMSG ') çözümü, istemcilerin MPD 'yı yeniden indirmesi gerekmeyen canlı akış için yararlıdır veya istemci ile kaynak arasında SSAı bildirim işleme yoktur. 

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

| **Öznitelik adı** | **Tür**                | **Gerekli mi?** | **Açıklama**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | string                  | Gerekli      | İleti düzenini tanımlar. Düzen, canlı sunucu bildirim kutusundaki Scheme özniteliğinin değerine ayarlanır. Değer, ileti düzenini tanımlayan bir URN veya **URL olmalıdır;** Hizmetin, MPD 'de kısaltma için şu anda yalnızca "XML + bin" desteklediği için desteklenen çıkış dosyası sayısı "urn: scte: scte35:2014: XML + bin" ([SCTE-214-1] sec 6.7.4 (MPD) olmalıdır. |
| değer              | string                  | İsteğe Bağlı      | İletinin semantiğini özelleştirmek için düzenin sahipleri tarafından kullanılan ek bir dize değeri. Birden çok olay akışını aynı şemayla ayırt etmek için, değerin olay akışı adı ([MS-SSTR-Ingest] için trackName veya [RTMP] ınest için AMF ileti adı) olarak ayarlanması **gerekir** .                                                                         |
| Timescale          | 32-bit işaretsiz tamsayı | Gerekli      | Zaman ölçeği, saniye başına saat cinsinden.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>MPEG DASH için 3.3.2 örnek olay akışları

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 örnek MPEG DASH. mpd bildirim, Adobe Simple modunu kullanarak RTMP akışı sinyali

Aşağıdaki örnek, Adobe "basit" mod sinyalli bir RTMP akışı için Media Services dinamik Paketleyiciyi kullanarak bir alıntı EventStream gösterir.

```xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 örnek MPEG DASH. mpd bildirim sinyali Adobe SCTE-35 modunu kullanarak bir RTMP akışı

Aşağıdaki örnek, Adobe SCTE-35 modu sinyali kullanan bir RTMP akışı için Media Services dinamik paketlemesiyle bir alıntı EventStream gösterir.

[SCTE-214-1] başına XML + bin stili sinyal kullanan örnek EventStream öğesi

```xml

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
```



> [!IMPORTANT]
> PresentationTime 'ın, iletinin varış süresi değil, dönem başlangıç zamanına göre değil, [SCTE-35] olayının sunum zamanı olduğunu unutmayın.
> [MPEGDASH], Event@presentationTime "dönemin başlangıcına göre etkinliğin sunum süresini belirtir.
> Sunum süresinin saniye cinsinden değeri, bu özniteliğin değerinin ve öznitelik değerinin bölümüdür EventStream@timescale .
> Yoksa, sunum zamanının değeri 0 ' dır.


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 örnek MPEG DASH bildirimi (MPD), tek dönem, EventStream, Adobe Simple mod sinyalleri kullanılarak

Aşağıdaki örnek, Adobe "basit" mod ad sinyal yöntemi kullanılarak bir kaynak RTMP akışı için Media Services dinamik Paketleyiciyi gösteren çıktıyı gösterir. Çıktı, "urn: com: Adobe: DPI: Simple: 2015" olarak ayarlanmış ve değer özelliği "simplesignal" olarak ayarlanmış bir EventStream 'i gösteren tek bir nokta bildirimidir.
Her basit sinyal @presentationTime , @duration @id gelen basit sinyallere göre doldurulmuş,, ve özelliklerine sahip bir olay öğesinde sağlanır.

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH bant Içi olay Ileti kutusu sinyali

Bant içi olay akışı, MPD 'ın uyarlama kümesi düzeyinde bir InbandEventStream öğesi olmasını gerektirir.  Bu öğe, olay Ileti kutusunda (' EMSG ') da görüntülenen zorunlu bir ıiduri özniteliği ve isteğe bağlı zaman ölçeği özniteliği vardır.  MPD 'da tanımlanmayan düzen tanımlayıcılarına sahip olay iletisi **kutuları bulunmamalıdır.**

Bant içi [SCTE-35] satıral için, sinyaller, ' ' urn: scte: scte35:2013: bin " **olarak kullanılmalıdır.**
[SCTE-35]-bant içi iletilerin normative tanımları, [SCTE-214-3] sec 7.3.2 (SCTE 35 Cue iletilerinin satır sayısı) içinde tanımlanmıştır.

Aşağıdaki ayrıntılar, istemcinin [SCTE-214-3] ile uyumlu olması için ' EMSG ' içinde beklediği belirli değerleri özetler.

| **Alan adı**          | **Alan türü**          | **Gerekli mi?** | **Açıklama**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | string                  | Gerekli      | İleti düzenini tanımlar. Düzen, canlı sunucu bildirim kutusundaki Scheme özniteliğinin değerine ayarlanır. Değer, ileti düzenini tanımlayan bir URN **olmalıdır** . [SCTE-35] iletileri için bu, [SCTE-214-3] ile **uyumlu olması için** "urn: scte: scte35:2013: bin" olmalıdır          |
| Değer                   | string                  | Gerekli      | İletinin semantiğini özelleştirmek için düzenin sahipleri tarafından kullanılan ek bir dize değeri. Birden çok olay akışını aynı şemayla ayırt etmek için, bu değer olay akışının adına ayarlanır (düz alma için trackName veya RTMP alma için AMF ileti adı). |
| Timescale               | 32-bit işaretsiz tamsayı | Gerekli      | ' EMSG ' kutusu içindeki süreler ve süre alanlarının saniye başına düşen zaman ölçeği.                                                                                                                                                                                                            |
| Presentation_time_delta | 32-bit işaretsiz tamsayı | Gerekli      | Bu kesimdeki, etkinliğin sunum süresinin ve en erken sunum zamanının medya sunumu zaman aralığı. Sunum süresi ve süresi, [ISO-14496-12] Ek I içinde tanımlandığı şekilde, 1 veya 2 türündeki akış erişim noktalarıyla (SAP) hizalı **olmalıdır** .                                  |
| event_duration          | 32-bit işaretsiz tamsayı | Gerekli      | Olayın süresi veya 0xFFFFFFFF bilinmeyen bir süreyi belirtecek şekilde.                                                                                                                                                                                                                              |
| Id                      | 32-bit işaretsiz tamsayı | Gerekli      | İletinin bu örneğini tanımlar. Denk semantiklere sahip iletiler aynı değere sahip olacaktır. İleti yapıldığında KIMLIK belirtilmezse Azure Media Services benzersiz bir kimlik oluşturulur.                                                                                        |
| Message_data            | bayt dizisi              | Gerekli      | Olay iletisi. [SCTE-35] iletileri için ileti verisi, [SCTE-214-3] ile uyumlu olan ikili splice_info_section ()                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Adobe Simple modu için örnek InBandEvenStream varlığı
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>3.3.5 DASH Ileti Işleme

Olaylar, hem video hem de ses parçaları için ' EMSG ' kutusu içinde bant içinde sinyal alınır.  Sinyal, presentation_time_delta 15 saniye veya daha az olan tüm kesim istekleri için oluşur. 

Bir kayan sunu penceresi etkinleştirildiğinde, olay iletisinin süresi ve süresinin toplamı, bildirimdeki medya verilerinin süresinden daha az olduğunda, MPD 'den olay iletileri kaldırılır.  Diğer bir deyişle, başvurdukları medya zamanı, kayan sunu penceresinden alındığında olay iletileri bildirimden kaldırılır.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 kodlayıcı için uygulama kılavuzlarından yararlanın

Aşağıdaki kılavuzlar, bir kodlayıcı satıcısının bu belirtim uygulamasını etkileyebilecek yaygın sorunlardır.  Aşağıdaki yönergeler, bu şartın başkaları için uygulanmasını kolaylaştırmak amacıyla gerçek dünya ortağı geri bildirimlerine göre toplanmıştı. 

[SCTE-35] iletiler, [MS-SSTR-Ingest] için " **urn: scte: scte35:2013: bin"** düzenini ve [RTMP] alma için **"scte35"** türünü kullanarak ikili biçimde alınır. MPEG-2 aktarım akışı sunum zaman damgalarını (PTS) temel alan [SCTE-35] zamanlamalarının dönüştürülmesini kolaylaştırmak için, (splice_time ()) PTS (pts_time + pts_adjustment) ve medya zaman çizelgesi arasında bir eşleme, olay sunumu süresi (düz alma için fragment_absolute_time alanı ve RTMP alma için saat alanı) tarafından sağlanır. 33 bit PTS değeri yaklaşık her 26,5 saatte bir yapıldığından eşleme gereklidir.

Kesintisiz Akış alma [MS-SSTR-alma], medya Data Box (' mdat ') [scte-35] içinde tanımlanan **splice_info_section ()** içermesi **gerekir** . 

RTMP alma için AMF iletisinin Cue özniteliği, [SCTE-35] içinde tanımlanan Base64 kodlamalı **splice_info_section ()** olarak ayarlanır.  


İletiler yukarıda açıklanan biçimde olduğunda, yukarıda tanımlanan şekilde HLS, kesintisiz ve DASH istemcilerine gönderilir.  

Uygulamanızı Azure Media Services platformu ile sınarken, bir kodlama LiveEvent üzerinde teste geçmeden önce lütfen önce "doğrudan geçişli" LiveEvent ile testi başlatın.

---

## <a name="change-history"></a>Değişiklik Geçmişi

| Tarih     | Değişiklikler                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Eklenmiş RTMP alma desteği, elete Live için RTMP "onCuePoint" eklendi                                            |
| 08/22/19 | Özel meta veriler için RTMP 'ye OnUserDataEvent eklemek üzere güncelleştirildi                                                          |
| 1/08/20  | RTMP Simple ve RTMP SCTE35 modunda hata düzeltildi. "OnCuePoint" iken "onAdCue" olarak değiştirildi. Basit mod tablosu güncelleştirildi. |
| 08/4/20  | Üretim hizmetindeki uygulamayla eşleşecek şekilde DAVTERANGE etiketi için destek kaldırıldı.    |

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını görüntüleyin.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
