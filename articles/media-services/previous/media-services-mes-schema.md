---
title: Media Encoder Standard şeması | Microsoft Docs
description: Bu makalede, Media Encoder Standard önayarlarının temel aldığı XML şemasının bazı öğeleri ve türleri açıklanmaktadır.
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
ms.openlocfilehash: f82e0c3f76dba05c3404b11e07c7130119ce0b9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015667"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard şeması

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Bu makalede, [Media Encoder Standard önayarlarının](media-services-mes-presets-overview.md) temel aldığı XML şemasının bazı öğeleri ve türleri açıklanmaktadır. Makale, öğelerin ve bunların geçerli değerlerinin açıklamasını verir.  

## <a name="preset-root-element"></a><a name="Preset"></a> Önayar (kök öğe)
Bir kodlama ön ayarını tanımlar.  

### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **Kodlama** |[Kodlama](media-services-mes-schema.md#Encoding) |Kök öğe, giriş kaynaklarının kodlandığını gösterir. |
| **Çıkışlar** |[Çıkışlar](media-services-mes-schema.md#Output) |İstenen çıkış dosyalarının toplanması. |
| **Üzerinde mod**<br/>minOccurs = "0"<br/>Varsayılan = "AutoSize|xs: String|Çıktı video çerçevesinin boyutunu, doldurmayı, pikseli veya en boy oranını görüntüleyin. En uygun değerler şu değerlerden **biri olabilir:** **none**, **AutoSize** (default) veya **Otomatik Sığdır**.<br/><br/>**Hiçbiri**: piksel boyut oranını göz önünde bulundurmadan veya giriş videosunun en boy oranını göstermeksizin çıkış çözünürlüğünü (örneğin, önceden ayarlı **Genişlik** ve **Yükseklik** ) kesin olarak izleyin. Çıkış videosunun, girişle karşılaştırıldığında farklı bir en boy oranına sahip olan [kırpma](media-services-crop-video.md)gibi senaryolarda önerilir. <br/><br/>Otomatik **Boyutlandır**: çıkış çözünürlüğü, önceden ayarlanmış olarak belirtilen pencerenin Içine (genişlik * yükseklik) sığacak. Ancak, kodlayıcı kare (1:1) piksel boyut oranına sahip bir çıkış videosu üretir. Bu nedenle, girişin görüntüleme en boy oranını doldurma olmadan eşleştirmek için çıkış genişliğinin veya çıkış yüksekliğinin üzerine geçersiz kılınabilir. Örneğin, giriş 1920x1080 ise ve kodlama ön ayarı 1280x1280 isterse, ön ayarda bulunan yükseklik değeri geçersiz kılınır ve çıkış, 16:9 girişinin en boy oranını tutan 1280x720 ' de olur. <br/><br/>**Otomatik Sığdır**: gerekirse, çıktı videosunu (harf kutusu veya pillarbox ile), istenen çıkış çözünürlüğünü kabul etmek için doldurur. Bu, çıkışdaki etkin video bölgesinin, girişle aynı en boy oranına sahip olmasını sağlar. Örneğin, girişin 1920x1080 olduğunu ve kodlama önayarının 1280x1280 istediğini varsayalım. Ardından, çıkış videosu 1280x1280 konumunda olacaktır, ancak en boy oranı 16:9 280 ve üst ve alt olarak en fazla boyut olan "etkin video" bir iç 1280x720 dikdörtgeni içerecektir. Başka bir örnek için, giriş 1440x1080 ise ve kodlama ön ayarı 1280x720 isterse, çıktı, 4:3 160 ve sol tarafta en boy oranındaki bir iç dikdörtgen içeren 1280x720 olur. 

### <a name="attributes"></a>Öznitelikler

| Ad | Tür | Description |
| --- | --- | --- |
| **Sürüm**<br/><br/> Gerekli |**xs: Decimal** |Önceden ayarlanmış sürüm. Aşağıdaki kısıtlamalar geçerlidir: xs: fractionDigits value = "1" ve xs: minInclusive value = "1", örneğin **Version = "1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a> Şifreleme
Aşağıdaki öğelerin dizisini içerir:  

### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |R., video için ayarlar. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Sesin AAC kodlama ayarları. |
| **Bmpımage** |[Bmpımage](media-services-mes-schema.md#BmpImage) |BMP resmi ayarları. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |PNG resmi için ayarlar. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Jpg resmi ayarları. |

## <a name="h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs = "0" |**xs: Boolean** |Şu anda yalnızca bir-pass kodlaması desteklenir. |
| **KeyFrameInterval**<br/><br/> minOccurs = "0"<br/><br/> **Varsayılan = "00:00:02"** |**xs: Saat** |Saniye cinsinden ıDR çerçeveleri arasındaki sabit boşluğu belirler. GOP süresi olarak da adlandırılır. Kodlayıcının bu değerden sapıp ayırt edilip edilmeyeceğini denetlemek için bkz. **manzara** . |
| **Manzara algılaması**<br/><br/> minOccurs = "0"<br/><br/> Varsayılan = "false" |**xs: Boolean** |True olarak ayarlanırsa, kodlayıcı videoda sahne değişikliğini algılamaya çalışır ve bir ıDR çerçevesi ekler. |
| **Karmaşıklığını**<br/><br/> minOccurs = "0"<br/><br/> Varsayılan = "dengeli" |**xs: String** |Kodlama hızı ve video kalitesi arasındaki ticareti denetler. Şu değerlerden biri olabilir: **hız**, **dengeli** veya **kalite**<br/><br/> Varsayılan: **dengeli** |
| **SyncMode**<br/><br/> minOccurs = "0" | |Özellik gelecek bir sürümde kullanıma sunulacaktır. |
| **H264Layers**<br/><br/> minOccurs = "0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Çıkış video katmanlarından oluşan koleksiyon. |

### <a name="attributes"></a>Öznitelikler

| Ad | Tür | Description |
| --- | --- | --- |
| **Condition** |**xs: String** | Girişin video numarası yoksa, kodlayıcının tek renkli video izlemesi eklemesini zorlamak isteyebilirsiniz. Bunu yapmak için, Condition = "ınsertblackıce ıfnovideobottomlayeronly" (yalnızca en düşük bit hızına sahip bir video eklemek için) veya Condition = "ınsertblackıce" (tüm çıkış bittarifelerinde video eklemek için) kullanın. Daha fazla bilgi için [Bu](media-services-advanced-encoding-with-mes.md#no_video) makaleye bakın.|

## <a name="h264layers"></a><a name="H264Layers"></a> H264Layers

Varsayılan olarak, yalnızca ses içeren ve video içermeyen kodlayıcıya bir giriş gönderirseniz, çıkış varlığı yalnızca ses verileriyle dosya içerir. Bazı oyuncular bu tür çıkış akışlarını işleyemeyebilir. Kodlayıcının Bu senaryodaki çıkışa bir video izlemesi eklemesini zorlamak için H264Video's **ınsertblackıce ıfnovideo** öznitelik ayarını kullanabilirsiniz. Daha fazla bilgi için [Bu](media-services-advanced-encoding-with-mes.md#no_video) makaleye bakın.
              
### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = "0" maxOccurs = "sınırsız" |[H264Layer](media-services-mes-schema.md#H264Layer) |H264 katmanlı bir koleksiyon. |

## <a name="h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> Video limitleri, [H264 Levels](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tablosunda açıklanan değerlere göre yapılır.  
> 
> 

### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> Varsayılan = "Auto" |**xs: String** |Şu **xs: String** değerlerinden biri olabilir: **Auto**, **Baseline**, **Main**, **High**. |
| **Düzeyde**<br/><br/> minOccurs = "0"<br/><br/> Varsayılan = "Auto" |**xs: String** | |
| **Bit hızı**<br/><br/> minOccurs = "0" |**xs: int** |Bu video katmanı için kullanılan bit hızı, Kbps olarak belirtilir. |
| **Maxbit hızı**<br/><br/> minOccurs = "0" |**xs: int** |Bu video katmanı için kullanılan, Kbps olarak belirtilen maksimum bit hızı. |
| **BufferWindow**<br/><br/> minOccurs = "0"<br/><br/> Varsayılan = "00:00:05" |**xs: Saat** |Video arabelleğinin uzunluğu. |
| **Genişlik**<br/><br/> minOccurs = "0" |**xs: int** |Çıkış video çerçevesinin piksel cinsinden genişliği.<br/><br/> Şu anda, hem genişlik hem de yükseklik belirtmeniz gerekir. Genişlik ve yüksekliğin çift sayı olması gerekir. |
| **Yükseklik**<br/><br/> minOccurs = "0" |**xs: int** |Çıkış video çerçevesinin piksel cinsinden yüksekliği.<br/><br/> Şu anda, hem genişlik hem de yükseklik belirtmeniz gerekir. Genişlik ve yüksekliğin çift sayı olması gerekir.|
| **BFrames 'ler**<br/><br/> minOccurs = "0" |**xs: int** |Başvuru çerçeveleri arasındaki B kare sayısı. |
| **ReferenceFrames**<br/><br/> minOccurs = "0"<br/><br/> Varsayılan = "3" |**xs: int** |Bir GOP içindeki başvuru çerçevelerinin sayısı. |
| **EntropyMode**<br/><br/> minOccurs = "0"<br/><br/> Varsayılan = "Katzü" |**xs: String** |Şu değerlerden biri olabilir: **Catzb** ve **CAVLC**. |
| **Kare hızı değerini**<br/><br/> minOccurs = "0" |Rational Number |Çıkış videosunun kare hızını belirler. Kodlayıcının giriş videosunun kare hızını kullanmasına izin vermek için "0/1" varsayılanını kullanın. İzin verilen değerlerin ortak video kare hızları olması beklenir. Ancak geçerli bir Rational öğesine izin verilir. Örneğin, 1/1 1 fps olur ve geçerli olur.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> -24000/1001 (23,976 fps)<br/><br/> -25/1 (25 fps)<br/><br/>  -30/1 (30 fps)<br/><br/> -30000/1001 (29,97 fps) <br/> <br/>**Göz önünde** Çoklu bit hızlı kodlama için özel bir önayar oluşturuyorsanız, önceden ayarlanmış tüm katmanların aynı kare hızına sahip **olması gerekir** .|
| **Uyartivebframe**<br/><br/> minOccurs = "0" |**xs: Boolean** |Azure Medya kodlayıcıdan kopyalama |
| **Dilimler**<br/><br/> minOccurs = "0"<br/><br/> Varsayılan = "0" |**xs: int** |Bir karenin kaç dilimlerinin bölündüğü belirler. Varsayılan kullanım önerilir. |

## <a name="aacaudio"></a><a name="AACAudio"></a> AACAudio
 Aşağıdaki öğe ve grupların bir dizisini içerir.  

 AAC hakkında daha fazla bilgi için bkz. [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> Varsayılan = "AACLC" |**xs: String** |Şu değerlerden biri olabilir: **AACLC**, **HEAACV1** veya **HEAACV2**. |

### <a name="attributes"></a>Öznitelikler

| Ad | Tür | Description |
| --- | --- | --- |
| **Condition** |**xs: String** |Bir giriş sesi olmadığında, kodlayıcının sessiz ses izi içeren bir varlık üretmesine zorlamak için "ınsertsilenceifnoaudio" değerini belirtin.<br/><br/> Varsayılan olarak, yalnızca video içeren ve ses içermeyen kodlayıcıya bir giriş gönderirseniz, çıkış varlığı yalnızca video verilerini içeren dosyaları içerir. Bazı oyuncular bu tür çıkış akışlarını işleyemeyebilir. Bu ayarı, kodlayıcının Bu senaryodaki çıkışa sessiz ses izi eklemesini zorlamak için kullanabilirsiniz. |

### <a name="groups"></a>Gruplar

| Başvuru | Description |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs = "0" |Her bir profil için ayarlanacak kanal, örnekleme hızı ve bit hızının uygun sayısını öğrenmek için [Audiogroup](media-services-mes-schema.md#AudioGroup) 'un açıklamasına bakın. |

## <a name="audiogroup"></a><a name="AudioGroup"></a> AudioGroup
Her profil için geçerli olan değerlerin ayrıntıları için aşağıdaki "ses codec bileşeni ayrıntıları" tablosuna bakın.  

### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **Kanallar**<br/><br/> minOccurs = "0" |**xs: int** |Kodlanan ses kanalları sayısı. Şunlar geçerli seçenekler şunlardır: 1, 2, 5, 6, 8.<br/><br/> Varsayılan: 2. |
| **SamplingRate**<br/><br/> minOccurs = "0" |**xs: int** |Hz 'de belirtilen ses örnekleme hızı. |
| **Bit hızı**<br/><br/> minOccurs = "0" |**xs: int** |Ses kodlaması sırasında kullanılan bit hızı, kbps cinsinden belirtilir. |

### <a name="audio-codec-details"></a>Ses codec bileşeni ayrıntıları

Ses codec bileşeni|Ayrıntılar  
-----------------|---  
**AACLC** |1:<br/><br/> -11025:8 &lt; = bit hızı &lt; 16<br/><br/> -12000:8 &lt; = bit hızı &lt; 16<br/><br/> -16000:8 &lt; = bit hızı &lt; 32<br/><br/>-22050:24 &lt; = bit hızı &lt; 32<br/><br/> -24000:24 &lt; = bit hızı &lt; 32<br/><br/> -32000:32 &lt; = bit hızı &lt; = 192<br/><br/> -44100:56 &lt; = bit hızı &lt; = 288<br/><br/> -48000:56 &lt; = bit hızı &lt; = 288<br/><br/> -88200:128 &lt; = bit hızı &lt; = 288<br/><br/> -96000:128 &lt; = bit hızı &lt; = 288<br/><br/> 2:<br/><br/> -11025:16 &lt; bit hızı &lt; 24<br/><br/> -12000:16 &lt; bit hızı &lt; 24<br/><br/> -16000:16 &lt; bit hızı &lt; 40<br/><br/> -22050:32 &lt; = bit hızı &lt; 40<br/><br/> -24000:32 &lt; = bit hızı &lt; 40<br/><br/> -32000:40 &lt; = bit hızı &lt; = 384<br/><br/> -44100:96 &lt; = bit hızı &lt; = 576<br/><br/> -48000:96 &lt; = bit hızı &lt; = 576<br/><br/> -88200:256 &lt; = bit hızı &lt; = 576<br/><br/> -96000:256 &lt; = bit hızı &lt; = 576<br/><br/> 5/6:<br/><br/> -32000:160 &lt; = bit hızı &lt; = 896<br/><br/> -44100:240 &lt; = bit hızı &lt; = 1024<br/><br/> -48000:240 &lt; = bit hızı &lt; = 1024<br/><br/> -88200:640 &lt; = bit hızı &lt; = 1024<br/><br/> -96000:640 &lt; = bit hızı &lt; = 1024<br/><br/> 8:<br/><br/> -32000:224 &lt; = bit hızı &lt; = 1024<br/><br/> -44100:384 &lt; = bit hızı &lt; = 1024<br/><br/> -48000:384 &lt; = bit hızı &lt; = 1024<br/><br/> -88200:896 &lt; = bit hızı &lt; = 1024<br/><br/> -96000:896 &lt; = bit hızı &lt; = 1024  
**HEAACV1** |1:<br/><br/> -22050: bit hızı = 8<br/><br/> -24000:8 &lt; = bit hızı &lt; = 10<br/><br/> -32000:12 &lt; = bit hızı &lt; = 64<br/><br/> -44100:20 &lt; = bit hızı &lt; = 64<br/><br/> -48000:20 &lt; = bit hızı &lt; = 64<br/><br/> -88200: bit hızı = 64<br/><br/> 2:<br/><br/> -32000:16 &lt; = bit hızı &lt; = 128<br/><br/> -44100:16 &lt; = bit hızı &lt; = 128<br/><br/> -48000:16 &lt; = bit hızı &lt; = 128<br/><br/> -88200:96 &lt; = bit hızı &lt; = 128<br/><br/> -96000:96 &lt; = bit hızı &lt; = 128<br/><br/> 5/6:<br/><br/> -32000:64 &lt; = bit hızı &lt; = 320<br/><br/> -44100:64 &lt; = bit hızı &lt; = 320<br/><br/> -48000:64 &lt; = bit hızı &lt; = 320<br/><br/> -88200:256 &lt; = bit hızı &lt; = 320<br/><br/> -96000:256 &lt; = bit hızı &lt; = 320<br/><br/> 8:<br/><br/> -32000:96 &lt; = bit hızı &lt; = 448<br/><br/> -44100:96 &lt; = bit hızı &lt; = 448<br/><br/> -48000:96 &lt; = bit hızı &lt; = 448<br/><br/> -88200:384 &lt; = bit hızı &lt; = 448<br/><br/> -96000:384 &lt; = bit hızı &lt; = 448  
**HEAACV2** |2:<br/><br/> -22050:8 &lt; = bit hızı &lt; = 10<br/><br/> -24000:8 &lt; = bit hızı &lt; = 10<br/><br/> -32000:12 &lt; = bit hızı &lt; = 64<br/><br/> -44100:20 &lt; = bit hızı &lt; = 64<br/><br/> -48000:20 &lt; = bit hızı &lt; = 64<br/><br/> -88200:64 &lt; = bit hızı &lt; = 64  
  
## <a name="clip"></a><a name="Clip"></a> Resimler
### <a name="attributes"></a>Öznitelikler

| Ad | Tür | Description |
| --- | --- | --- |
| **StartTime** |**xs: süre** |Bir sununun başlangıç saatini belirtir. StartTime değerinin, giriş videosunun mutlak zaman damgalarına uyması gerekir. Örneğin, giriş videosunun ilk karesinde 12:00:10.000 zaman damgası varsa StartTime, en az 12:00:10.000 veya daha büyük olmalıdır. |
| **Süre** |**xs: süre** |Bir sununun süresini belirtir (örneğin, videodaki bir kaplamanın görünümü). |

## <a name="output"></a><a name="Output"></a> Çıktıların
### <a name="attributes"></a>Öznitelikler

| Ad | Tür | Description |
| --- | --- | --- |
| **Kısaltın** |**xs: String** |Çıktı dosyasının adı.<br/><br/> Çıkış dosyası adlarını oluşturmak için aşağıdaki tabloda açıklanan makroları kullanabilirsiniz. Örnek:<br/><br/> **"Çıktılar": [{"FileName": "{baseName}*{Resolution}*{bit hızı}. mp4", "biçim": {"Type": "MP4Format"}}]** |

### <a name="macros"></a>Makrolar

| Makroya | Description |
| --- | --- |
| **BaseName** |VoD kodlaması yapıyorsanız, {baseName}, giriş varlığının birincil dosyasının AssetFile.Name özelliğinin ilk 32 karakterdir.<br/><br/> Giriş varlığı canlı bir arşividir, {baseName}, sunucu bildirimindeki trackName özniteliklerinden türetilir. "<VideoStream topbit hızı</VideoStream" içinde olduğu gibi, Topbit hızını kullanarak bir alt klip işi gönderiyorsanız \> \> ve çıkış dosyası video içeriyorsa, {baseName}, video katmanının en yüksek bit hızına sahip trackname 'in ilk 32 karakterdir.<br/><br/> Bunun yerine, "<VideoStream * </VideoStream" gibi tüm giriş bit hızlarını kullanarak bir alt klip işi gönderiyorsanız \> \> ve çıkış dosyası video içeriyorsa, {baseName} karşılık gelen video katmanının trackname öğesinin ilk 32 karakterdir. |
| **Bileşeni** |Video için "H264" ve ses için "AAC" ile eşlenir. |
| **Bit hızı** |Çıkış dosyası video ve ses içeriyorsa, hedef video bit hızı veya çıkış dosyası yalnızca ses içeriyorsa ses bit hızını hedefleyin. Kullanılan değer, kbps cinsinden bit hızında olur. |
| **Kanalla** |Dosya ses içeriyorsa ses kanalı sayısı. |
| **Genişliğinde** |Dosyanın video içermesi durumunda, çıkış dosyasında, videonun piksel cinsinden genişliği. |
| **Yükseklik** |Dosya video içeriyorsa, çıkış dosyasındaki piksel cinsinden videonun yüksekliği. |
| **Uzantının** |Çıkış dosyası için "tür" özelliğinden devralır. Çıkış dosyası adı bir uzantıya sahiptir: "MP4", "TS", "jpg", "png" veya "BMP". |
| **INDEKS** |Küçük resim için zorunlu. Yalnızca bir kez bulunmalıdır. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a> Video (karmaşık tür codec 'ten devralır)
### <a name="attributes"></a>Öznitelikler

| Ad | Tür | Description |
| --- | --- | --- |
| **Başlangıç** |**xs: String** | |
| **Adım** |**xs: String** | |
| **Aralık** |**xs: String** | |
| **PreserveResolutionAfterRotation** |**xs: Boolean** |Ayrıntılı açıklama için şu bölüme bakın: [Preserveresolutionafterrotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
**Preserveresolutionafterrotation** bayrağını yüzde cinsinden ifade edilen çözüm değerleriyle birlikte kullanmanız önerilir (Width = "%100%", height = "100%").  

Varsayılan olarak, Media Encoder Standard (MES) önayarlarında kodlama çözümleme ayarları (genişlik, yükseklik) 0 derece dönüşle videoları hedeflemektedir. Örneğin, giriş videonuzun sıfır derecelik dönüşle 1280x720 olması halinde varsayılan ayar, çıktının aynı çözünürlüğe sahip olmasını sağlamaktır.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Giriş videosu sıfır olmayan dönüşle yakalanmışsa (örneğin, dikey bir smartphone veya tablet), varsayılan olarak, giriş videosuna kodlama çözümleme ayarlarını (genişlik, yükseklik) uygular ve ardından döndürme için telafi sağlar. Örneğin, aşağıdaki resme bakın. Önceden ayarlanmış, width = "100%", height = "100%" kullanır ve bu da çıktının 1280 piksel genişliğinde ve 720 piksel yüksekliğinde olmasını gerektirdiğini yorumlar. Videoyu döndürdükten sonra, resmi o pencereye sığacak şekilde küçültür ve sol ve sağ taraftaki ekran kutusu alanlarının önüne döner.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternatif olarak, **Preserveresolutionafterrotation** bayrağını kullanabilir ve bunu "true" olarak ayarlayabilirsiniz (varsayılan değer "false" dır). Önayarlarınızın width = "100%", height = "100%" ve PreserveResolutionAfterRotation değeri "true" olarak ayarlandıysa, 1280 piksel genişliğinde ve 720 piksel yüksekliğinde olan bir giriş videosu, sıfır derecelik dönüşle bir çıkış üretir, ancak 90 piksel genişliğinde ve 720 piksel yüksekliğinde olur. Aşağıdaki resme bakın:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a> FormatGroup (Grup)
### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Öğe

| Ad | Tür | Description |
| --- | --- | --- |
| **Genişlik**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Yükseklik**<br/><br/> minOccurs = "0" |**xs: int** | |

### <a name="attributes"></a>Öznitelikler

| Ad | Tür | Description |
| --- | --- | --- |
| **Condition** |**xs: String** | |

## <a name="pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Öğe

| Ad | Tür | Description |
| --- | --- | --- |
| **Genişlik**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Yükseklik**<br/><br/> minOccurs = "0" |**xs: int** | |

### <a name="attributes"></a>Öznitelikler

| Ad | Tür | Description |
| --- | --- | --- |
| **Condition** |**xs: String** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Öğe

| Ad | Tür | Description |
| --- | --- | --- |
| **Genişlik**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Yükseklik**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Kalite**<br/><br/> minOccurs = "0" |**xs: int** |Geçerli değerler: 1 (en kötü)-100 (en iyi) |

### <a name="attributes"></a>Öznitelikler

| Ad | Tür | Description |
| --- | --- | --- |
| **Condition** |**xs: String** | |

## <a name="pnglayers"></a><a name="PngLayers"></a> Pngkatmanları
### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = "0" maxOccurs = "sınırsız" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a> Bmpkatmanları
### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = "0" maxOccurs = "sınırsız" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a> Jpgkatmanları
### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = "0" maxOccurs = "sınırsız" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> Bmpımage (karmaşık tür videodan devralır)
### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **Pngkatmanları**<br/><br/> minOccurs = "0" |[Pngkatmanları](media-services-mes-schema.md#PngLayers) |PNG katmanları |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (karmaşık tür videodan devralır)
### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **Pngkatmanları**<br/><br/> minOccurs = "0" |[Pngkatmanları](media-services-mes-schema.md#PngLayers) |PNG katmanları |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (karmaşık tür videodan devralır)
### <a name="elements"></a>Öğeler

| Ad | Tür | Description |
| --- | --- | --- |
| **Pngkatmanları**<br/><br/> minOccurs = "0" |[Pngkatmanları](media-services-mes-schema.md#PngLayers) |PNG katmanları |

## <a name="examples"></a>Örnekler
Bu şemaya göre oluşturulan XML önayarlarının örneklerine bakın, bkz. [mes Için görev önayarları (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

