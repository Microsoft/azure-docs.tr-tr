---
title: Giriş meta verileri şemasını Azure Media Services | Microsoft Docs
description: Bu makale, Azure Media Services giriş meta verisi şemasına genel bir bakış sunar.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: eebe1c77c9af6791be94c16f0f792a9e7deb9a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017418"
---
# <a name="input-metadata"></a>Giriş meta verileri

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Bir kodlama işi, üzerinde bazı kodlama görevlerini gerçekleştirmek istediğiniz bir giriş varlığı (veya varlıkları) ile ilişkilendirilir.  Bir görevi tamamladıktan sonra bir çıkış varlığı üretilir.  Çıkış varlığı video, ses, küçük resim, bildirim vb. içerir. Çıktı varlığı, giriş varlığı hakkında meta veriler içeren bir dosya da içerir. Meta veri XML dosyasının adı şu biçimdedir: &lt; asset_id &gt;_metadata.xml (örneğin, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml); burada &lt; Asset_id, &gt; giriş varlığının AssetID değeridir.  

Media Services meta veri oluşturmak için giriş varlıklarını önceden üretemiyor. Giriş meta verileri yalnızca bir Iş içinde bir giriş varlığı işlendiğinde yapıt olarak üretilir. Bu nedenle, bu yapıt çıkış varlığına yazılır. Farklı araçlar, giriş varlıkları ve çıkış varlıkları için meta veriler oluşturmak için kullanılır. Bu nedenle, giriş meta verilerinin, çıkış meta verileri arasında biraz farklı bir şeması vardır.

Meta veri dosyasını incelemek istiyorsanız **SAS** Konumlandırıcı oluşturabilir ve dosyayı yerel bilgisayarınıza indirebilirsiniz. SAS Konumlandırıcı oluşturma ve  [Media Services .NET SDK uzantılarını kullanarak](media-services-dotnet-get-started.md)bir dosyayı indirme hakkında bir örnek bulabilirsiniz.  

Bu makalede, giriş metada ( &lt; asset_id_metadata.xml) temel ALDıĞı XML şemasının öğeleri ve türleri açıklanmaktadır &gt; .  Çıktı varlığı hakkında meta veriler içeren dosya hakkında daha fazla bilgi için bkz. [Çıkış meta verileri](media-services-output-metadata-schema.md).  

[Şema kodunu](media-services-input-metadata-schema.md#code) Bu makalenin sonunda bir [XML örneği](media-services-input-metadata-schema.md#xml) bulabilirsiniz.  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a> AssetFiles öğesi (kök öğe)
Kodlama işi için [Assetdosya öğesi](media-services-input-metadata-schema.md#AssetFile)öğeleri koleksiyonu içerir.  

Bu makalenin sonundaki bir XML örneğine bakın: [XML örneği](media-services-input-metadata-schema.md#xml).  

| Ad | Açıklama |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = "1" maxOccurs = "sınırsız" |Tek bir alt öğe. Daha fazla bilgi için bkz. [Assetfile öğesi](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile öğesi
 Bir varlık dosyasını tanımlayan öznitelikleri ve öğeleri içerir.  

 Bu makalenin sonundaki bir XML örneğine bakın: [XML örneği](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Öznitelikler
| Ad | Tür | Açıklama |
| --- | --- | --- |
| **Ad**<br /><br /> Gerekli |**xs: String** |Varlık dosya adı. |
| **Boyut**<br /><br /> Gerekli |**xs: Long** |Varlık dosyasının bayt cinsinden boyutu. |
| **Süre**<br /><br /> Gerekli |**xs: süre** |İçerik kayıttan yürütme süresi. Örnek: Duration = "PT25M 37.757 S". |
| **NumberOfStreams**<br /><br /> Gerekli |**xs: int** |Varlık dosyasındaki akış sayısı. |
| **FormatNames**<br /><br /> Gerekli |**xs: String** |Biçim adları. |
| **FormatVerboseNames**<br /><br /> Gerekli |**xs: String** |Ayrıntı adlarını biçimlendirin. |
| **StartTime** |**xs: süre** |İçerik başlangıç saati. Örnek: StartTime = "PT 2.669 S". |
| **Overallbit hızı** |**xs: int** |Bir varlık dosyasının KB/sn cinsinden ortalama bit hızı. |

> [!NOTE]
> Aşağıdaki dört alt öğe bir dizide yer almalıdır.  
> 
> 

### <a name="child-elements"></a>Alt öğeleri
| Ad | Tür | Description |
| --- | --- | --- |
| **Programlar**<br /><br /> minOccurs = "0" | |Varlık dosyası MPEG-TS biçiminde olduğunda tüm [Programlar öğesi](media-services-input-metadata-schema.md#Programs) koleksiyonu. |
| **Videoizler**<br /><br /> minOccurs = "0" | |Her fiziksel varlık dosyası sıfır veya daha fazla video, araya eklemeli bir şekilde uygun bir kapsayıcı biçimine sahip olabilir. Bu öğe, varlık dosyasının parçası olan tüm [video izlemelerinin](media-services-input-metadata-schema.md#VideoTracks) bir koleksiyonunu içerir. |
| **Ses Izleri**<br /><br /> minOccurs = "0" | |Her fiziksel varlık dosyası, uygun bir kapsayıcı biçiminde araya eklemeli sıfır veya daha fazla ses parçası içerebilir. Bu öğe, varlık dosyasının parçası olan tüm [Ses izlemelerinin](media-services-input-metadata-schema.md#AudioTracks) bir koleksiyonunu içerir. |
| **Meta veri**<br /><br /> minOccurs = "0" maxOccurs = "sınırsız" |[Meta veri türü](media-services-input-metadata-schema.md#MetadataType) |Varlık dosyasının meta verileri key\value dizeleri olarak temsil edilir. Örnek:<br /><br /> **&lt;Metadata anahtarı = "Language" Value = "ENG"/&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a> TrackType
Bu makalenin sonundaki bir XML örneğine bakın: [XML örneği](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Öznitelikler
| Ad | Tür | Description |
| --- | --- | --- |
| **Numarasını**<br /><br /> Gerekli |**xs: int** |Bu sesin veya video izlemenin sıfır tabanlı dizini.<br /><br /> Bu, bir MP4 dosyasında kullanılan TrackID olması gerekmez. |
| **Bileşeni** |**xs: String** |Video izleme codec bileşeni dizesi. |
| **CodecLongName** |**xs: String** |Ses veya video izleme codec bileşeni uzun adı. |
| **Temeli**<br /><br /> Gerekli |**xs: String** |Zaman tabanı. Örnek: zaman temeli = "1/48000" |
| **NumberOfFrames** |**xs: int** |Çerçeve sayısı (video parçaları için mevcut). |
| **StartTime** |**xs: süre** |Başlangıç saatini izleyin. Örnek: StartTime = "PT 2.669 S" |
| **Süre** |**xs: süre** |Süreyi izleyin. Örnek: Duration = "PTSampleFormat d 37.757 S". |

> [!NOTE]
> Aşağıdaki iki alt öğe bir dizide yer almalıdır.  
> 
> 

### <a name="child-elements"></a>Alt öğeleri
| Ad | Tür | Description |
| --- | --- | --- |
| **Çıkarma**<br /><br /> minOccurs = "0" maxOccurs = "1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Sunum bilgilerini içerir (örneğin, belirli bir ses izlemenin görsel olarak Engelli görüntüleyiciler için olup olmadığı). |
| **Meta veri**<br /><br /> minOccurs = "0" maxOccurs = "sınırsız" |[Meta veri türü](media-services-input-metadata-schema.md#MetadataType) |Çeşitli bilgileri tutmak için kullanılabilen genel anahtar/değer dizeleri. Örneğin, Key = "Language" ve value = "ENG". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a> AudioTrackType (TrackType öğesinden devralır)
 **Audiotracktype** , [tracktype](media-services-input-metadata-schema.md#TrackType)'dan devralan küresel bir karmaşık türdür.  

 Tür, varlık dosyasındaki belirli bir ses parçasını temsil eder.  

 Bu makalenin sonundaki bir XML örneğine bakın: [XML örneği](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Öznitelikler
| Ad | Tür | Description |
| --- | --- | --- |
| **SampleFormat** |**xs: String** |Örnek biçim. |
| **ChannelLayout** |**xs: String** |Kanal düzeni. |
| **Kanallar**<br /><br /> Gerekli |**xs: int** |Ses kanalı sayısı (0 veya daha fazla). |
| **SamplingRate**<br /><br /> Gerekli |**xs: int** |Örnek/sn veya Hz 'teki ses örnekleme oranı. |
| **Bit hızı** |**xs: int** |Varlık dosyasından hesaplanan bit/saniye cinsinden ortalama ses bit hızı. Yalnızca öğesel akış yükü sayılır ve paketleme ek yükü bu sayıma dahil değildir. |
| **BitsPerSample** |**xs: int** |WFormatTag biçim türü için örnek başına bit sayısı. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a> VideoTrackType (TrackType öğesinden devralır)
**Videotracktype** , [tracktype](media-services-input-metadata-schema.md#TrackType)'dan devralan küresel bir karmaşık türdür.  

Tür, varlık dosyasında belirli bir video parçasını temsil eder.  

Bu makalenin sonundaki bir XML örneğine bakın: [XML örneği](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Öznitelikler
| Ad | Tür | Description |
| --- | --- | --- |
| **FourCC**<br /><br /> Gerekli |**xs: String** |Video codec FourCC kodu. |
| **Profil** |**xs: String** |Video izleme profili. |
| **Düzeyde** |**xs: String** |Video izlemenin düzeyi. |
| **PixelFormat 'a dönüştürme** |**xs: String** |Video izlemenin piksel biçimi. |
| **Genişlik**<br /><br /> Gerekli |**xs: int** |Piksel cinsinden kodlanmış video genişliği. |
| **Yükseklik**<br /><br /> Gerekli |**xs: int** |Piksel cinsinden kodlanmış video yüksekliği. |
| **DisplayAspectRatioNumerator**<br /><br /> Gerekli |**xs: Double** |Video görüntüleme en boy oranı payı. |
| **Displayaspectratiopayda**<br /><br /> Gerekli |**xs: Double** |Video görüntüleme en boy oranı paydası. |
| **Displayaspectratiopayda**<br /><br /> Gerekli |**xs: Double** |Video örneği en boy oranı payı. |
| **SampleAspectRatioNumerator** |**xs: Double** |Video örneği en boy oranı payı. |
| **SampleAspectRatioNumerator** |**xs: Double** |Video örneği en boy oranı paydası. |
| **Kare hızı değerini**<br /><br /> Gerekli |**xs: Decimal** |.3F biçiminde ölçülen video kare hızı. |
| **Bit hızı** |**xs: int** |Varlık dosyasından hesaplanan saniye başına kilobit cinsinden ortalama video bit hızı. Yalnızca öğesel akış yükü sayılır ve paketleme ek yükü dahil değildir. |
| **MaxGOPBitrate** |**xs: int** |Bu video izlemesi için saniyedeki maksimum GOP Ortalama bit hızı, saniye başına kilobit cinsinden. |
| **HasBFrames** |**xs: int** |Video parça sayısı B kare. |

## <a name="metadatatype"></a><a name="MetadataType"></a> Meta veri türü
**Metadatatype** , bir varlık dosyasının meta verilerini anahtar/değer dizeleri olarak açıklayan küresel bir karmaşık türdür. Örneğin, Key = "Language" ve value = "ENG".  

Bu makalenin sonundaki bir XML örneğine bakın: [XML örneği](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Öznitelikler
| Ad | Tür | Description |
| --- | --- | --- |
| **anahtar**<br /><br /> Gerekli |**xs: String** |Anahtar/değer çiftindeki anahtar. |
| **değer**<br /><br /> Gerekli |**xs: String** |Anahtar/değer çiftindeki değer. |

## <a name="programtype"></a><a name="ProgramType"></a> ProgramType
**Programtype** , bir programı tanımlayan küresel bir karmaşık türdür.  

### <a name="attributes"></a>Öznitelikler
| Ad | Tür | Description |
| --- | --- | --- |
| **Program kimliği**<br /><br /> Gerekli |**xs: int** |Program kimliği |
| **NumberOfPrograms**<br /><br /> Gerekli |**xs: int** |Program sayısı. |
| **Pmtpıd**<br /><br /> Gerekli |**xs: int** |Program eşleme tabloları (PMTs) programlar hakkında bilgi içerir.  Daha fazla bilgi için bkz. [DEVRESEL_ÖDEME](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Gerekli |**xs: int** |Kod çözücü tarafından kullanılır. Daha fazla bilgi için bkz. [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: Long** |Sunum zaman damgası başlatılıyor. |
| **EndPTS** |**xs: Long** |Sunum zaman damgasını bitiriliyor. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a> StreamDispositionType
**Streamdispositiontype** , akışı tanımlayan küresel bir karmaşık türdür.  

Bu makalenin sonundaki bir XML örneğine bakın: [XML örneği](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Öznitelikler
| Ad | Tür | Description |
| --- | --- | --- |
| **Varsayılanını**<br /><br /> Gerekli |**xs: int** |Bu özniteliği varsayılan sunum olduğunu göstermek için 1 olarak ayarlayın. |
| **Dub**<br /><br /> Gerekli |**xs: int** |Bu özniteliği, bu işlemi, dublaed sunum olarak belirtmek için 1 olarak ayarlayın. |
| **Özgün**<br /><br /> Gerekli |**xs: int** |Bu özniteliği orijinal sunum olduğunu göstermek için 1 olarak ayarlayın. |
| **Yorum**<br /><br /> Gerekli |**xs: int** |Bu özniteliği, bu izlemenin yorum içerdiğini belirtmek için 1 olarak ayarlayın. |
| **Mü**<br /><br /> Gerekli |**xs: int** |Bu özniteliği, bu izlemenin şarkı sözlerini içerdiğini belirtmek için 1 olarak ayarlayın. |
| **Karaoke**<br /><br /> Gerekli |**xs: int** |Bu özniteliği, Karaoke izlemesini (arka plan müziği, Vocal yok) temsil ettiğini belirtmek için 1 olarak ayarlayın. |
| **Zorlamak**<br /><br /> Gerekli |**xs: int** |Bu özniteliği zorlanan sunum olduğunu göstermek için 1 olarak ayarlayın. |
| **Duyun**<br /><br /> Gerekli |**xs: int** |Bu özniteliği, duymakta olan kişiler için bu izi göstermek üzere 1 olarak ayarlayın. |
| **Görselleştirme**<br /><br /> Gerekli |**xs: int** |Bu özniteliği, bu izlemenin görsel olarak zayıfladığını belirtmek için 1 olarak ayarlayın. |
| **Temizleme etkileri**<br /><br /> Gerekli |**xs: int** |Bu özniteliğin temiz etkileri olduğunu göstermek için bu özniteliği 1 olarak ayarlayın. |
| **Attachedpıc**<br /><br /> Gerekli |**xs: int** |Bu özniteliği, bu izlemenin resim olduğunu belirtmek için 1 olarak ayarlayın. |

## <a name="programs-element"></a><a name="Programs"></a> Programs öğesi
Birden çok **Program** öğesini tutan sarmalayıcı öğesi.  

### <a name="child-elements"></a>Alt öğeleri
| Ad | Tür | Description |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs = "0" maxOccurs = "sınırsız" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |MPEG-TS biçimindeki varlık dosyaları için varlık dosyasındaki programlarla ilgili bilgiler içerir. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> Videoiz öğesi
 Birden çok **Videotrack** öğesi tutan sarmalayıcı öğesi.  

 Bu makalenin sonundaki bir XML örneğine bakın: [XML örneği](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Alt öğeleri
| Ad | Tür | Description |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs = "0" maxOccurs = "sınırsız" |[VideoTrackType (TrackType öğesinden devralır)](media-services-input-metadata-schema.md#VideoTrackType) |Varlık dosyasındaki video izlemelerinin bilgilerini içerir. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> Audioiz öğesi
 Birden çok **Audiotrack** öğesi tutan sarmalayıcı öğesi.  

 Bu makalenin sonundaki bir XML örneğine bakın: [XML örneği](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>öğeler
| Ad | Tür | Description |
| --- | --- | --- |
| **Ses Izi**<br /><br /> minOccurs = "0" maxOccurs = "sınırsız" |[AudioTrackType (TrackType öğesinden devralır)](media-services-input-metadata-schema.md#AudioTrackType) |Varlık dosyasındaki ses izlemelerinin bilgilerini içerir. |

## <a name="schema-code"></a><a name="code"></a> Şema kodu
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            elementFormDefault="qualified">  

  <xs:complexType name="MetadataType">  
    <xs:attribute name="key"   type="xs:string" use="required"/>  
    <xs:attribute name="value" type="xs:string" use="required"/>  
  </xs:complexType>  

  <xs:complexType name="ProgramType">  
    <xs:attribute name="ProgramId" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Program Id</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Number of programs</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PmtPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pmt pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PcrPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pcr pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="StartPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>start pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="EndPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>end pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="StreamDispositionType">  
    <xs:attribute name="Default"          type="xs:int" use="required" />  
    <xs:attribute name="Dub"              type="xs:int" use="required" />  
    <xs:attribute name="Original"         type="xs:int" use="required" />  
    <xs:attribute name="Comment"          type="xs:int" use="required" />  
    <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
    <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
    <xs:attribute name="Forced"           type="xs:int" use="required" />  
    <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
    <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
    <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
    <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
  </xs:complexType>  

  <xs:complexType name="TrackType" abstract="true">  
    <xs:sequence>  
      <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
      <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
    </xs:sequence>  
    <xs:attribute name="Id" use="required">  
      <xs:annotation>  
        <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="Codec" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec string</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="CodecLongName" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec long name</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="TimeBase"  type="xs:string" use="required">  
      <xs:annotation>  
        <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfFrames">  
      <xs:annotation>  
        <xs:documentation>number of frames</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="StartTime" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="Duration" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="VideoTrackType">  
    <xs:annotation>  
      <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="FourCC" type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>video codec FourCC code</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Profile" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>profile</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Level" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>level</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PixelFormat" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>Video track's pixel format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Width" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video width in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Height" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video height in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioNumerator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioDenominator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="FrameRate" use="required">  
          <xs:annotation>  
            <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:decimal">  
              <xs:minInclusive value="0"/>  
              <xs:fractionDigits value="3"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="MaxGOPBitrate">  
          <xs:annotation>  
            <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="HasBFrames" type="xs:int">  
          <xs:annotation>  
            <xs:documentation>video track number of B frames</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:complexType name="AudioTrackType">  
    <xs:annotation>  
      <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="SampleFormat"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>sample format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="ChannelLayout"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>channel layout</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Channels" use="required">  
          <xs:annotation>  
            <xs:documentation>number of audio channels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SamplingRate" use="required">  
          <xs:annotation>  
            <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="BitsPerSample">  
          <xs:annotation>  
            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:element name="AssetFiles">  
    <xs:annotation>  
      <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
    </xs:annotation>  
    <xs:complexType>  
      <xs:sequence>  
        <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
          <xs:annotation>  
            <xs:documentation>asset file</xs:documentation>  
          </xs:annotation>  
          <xs:complexType>  
            <xs:sequence>  
              <xs:element name="Programs" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
            </xs:sequence>  
            <xs:attribute name="Name" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>the media asset file name</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Size" use="required">  
              <xs:annotation>  
                <xs:documentation>size of file in bytes</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:long">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Duration" type="xs:duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
              <xs:annotation>  
                <xs:documentation>number of streams in asset file</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatNames" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format verbose names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="StartTime" type="xs:duration">  
              <xs:annotation>  
                <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="OverallBitRate">  
              <xs:annotation>  
                <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:complexType>  
        </xs:element>  
      </xs:sequence>  
    </xs:complexType>  
  </xs:element>  
</xs:schema>  
```


## <a name="xml-example"></a><a name="xml"></a> XML örneği
Aşağıda, giriş meta verileri dosyasına bir örnek verilmiştir.  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
  <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
    <VideoTracks>  
      <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
      </VideoTrack>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
      </AudioTrack>  
    </AudioTracks>  
    <Metadata key="major_brand" value="mp42" />  
    <Metadata key="minor_version" value="0" />  
    <Metadata key="compatible_brands" value="mp42mp41" />  
    <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
    <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

