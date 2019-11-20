---
title: Çoklu bit hızına sahip akışlar oluşturmak için Azure Media Services kullanarak canlı akış | Microsoft Docs
description: 'Bu konu başlığı altında, şirket içi kodlayıcıdan tek bir bit hızı canlı akış alan ve sonra Media Services ile Uyarlamalı bit hızında akışa canlı kodlama gerçekleştiren bir kanalın nasıl ayarlanacağı açıklanır. Akış daha sonra, aşağıdaki uyarlamalı akış protokollerinden birini kullanarak bir veya daha fazla akış uç noktası aracılığıyla istemci kayıttan yürütme uygulamalarına teslim edilebilir: HLS, kesintisiz akış, MPEG DASH.'
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 4131e9b0ec057c16516f5a656debcf7053c2c1fe
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598313"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Azure Media Services aracılığıyla canlı akış gerçekleştirerek çoklu bit hızına sahip akışlar oluşturma

> [!NOTE]
> 12 Mayıs 2018 ' den itibaren Canlı Kanallar artık RTP/MPEG-2 aktarım akışı alma protokolünü desteklemezler. Lütfen RTP/MPEG-2 ' den RTMP veya parçalanmış MP4 (Kesintisiz Akış) alma protokolleriyle geçiş yapın.

## <a name="overview"></a>Genel Bakış
Azure Media Services (AMS) ' de **Kanal** , canlı akış içeriğini işlemek için bir işlem hattını temsil eder. **Kanal** , canlı giriş akışlarını iki şekilde alır:

* Şirket içi bir Live Encoder, aşağıdaki biçimlerden birinde Media Services ile canlı kodlama gerçekleştirmek için etkinleştirilen kanala tek bit hızlı bir akış gönderir: RTMP veya Kesintisiz Akış (parçalanmış MP4). Ardından Kanal, gelen tek bit hızlı akışın çoklu bit hızlı (uyarlamalı) bir video akışına gerçek zamanlı kodlanmasını gerçekleştirir. İstendiğinde, Media Services akışı müşterilere teslim eder.
* Şirket içi bir Live Encoder, AMS ile gerçek zamanlı kodlama gerçekleştirmek için etkinleştirilmemiş kanala çoklu bit hızlı **RTMP** veya **KESINTISIZ AKıŞ** (parçalanmış MP4) gönderir. Alınan akışlar, başka bir işlem yapılmadan **Kanal**s üzerinden geçer. Bu yönteme **doğrudan geçiş**adı verilir. Çoklu bit hızı Kesintisiz Akış: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco ve elete çıkış yapan aşağıdaki canlı kodlayıcıları kullanabilirsiniz. Aşağıdaki Live kodlayıcılar çıktıyı RTMP: Adobe Flash Media Live Encoder (FMLE), Telestream kablolü, haivision, teradek ve karmaşık Aster kodlayıcıları.  Gerçek zamanlı bir kodlayıcı, gerçek zamanlı kodlama için etkinleştirilmemiş bir kanala tek bit hızlı bir akış da gönderebilir, ancak bu işlem önerilmez. İstendiğinde, Media Services akışı müşterilere teslim eder.

  > [!NOTE]
  > Doğrudan geçiş yöntemi kullanmak, canlı akış yapmanın en ekonomik yoludur.
  > 
  > 

Media Services 2,10 sürümü ile başlayarak, bir kanal oluşturduğunuzda, kanalınızın giriş akışını almasını istediğiniz şekilde ve kanalın, akışın canlı kodlamasını gerçekleştirmesini isteyip istemediğinizi belirtebilirsiniz. İki seçeneğiniz vardır:

* **Hiçbiri** – çoklu bit hızında akış (doğrudan geçiş akışı) çıktısını alacak bir şirket içi Live Encoder kullanmayı planlıyorsanız bu değeri belirtin. Bu durumda, gelen akış herhangi bir kodlama olmadan çıkışa geçirilir. Bu, 2,10 sürümünden önceki bir kanalın davranışıdır.  Bu türden kanallarla çalışma hakkında daha ayrıntılı bilgi için bkz. [çoklu bit hızı akışları oluşturan şirket içi kodlayıcılarla canlı akış](media-services-live-streaming-with-onprem-encoders.md).
* **Standart** – tek bit hızı canlı akışınızı çoklu bit hızına sahip akışa kodlamak için Media Services kullanmayı planlıyorsanız bu değeri seçin. Canlı kodlama için bir faturalandırma etkisi olduğunu ve "çalışıyor" durumundaki canlı bir kodlama kanalını terk ettiğini unutmayın.  Ek saatlik ücretlerden kaçınmak için canlı akış olayınız tamamlandıktan sonra çalışan kanallarınızı hemen durdurmanız önerilir.

> [!NOTE]
> Bu konu, canlı kodlama (**Standart** kodlama türü) gerçekleştirmeye etkinleştirilen kanalların özniteliklerini açıklar. Canlı kodlama gerçekleştirmek için etkinleştirilmemiş kanallarla çalışma hakkında daha fazla bilgi için bkz. [çoklu bit hızı akışları oluşturan şirket içi kodlayıcılarla canlı akış](media-services-live-streaming-with-onprem-encoders.md).
> 
> [Konular](media-services-manage-live-encoder-enabled-channels.md#Considerations) bölümünü gözden geçirdiğinizden emin olun.
> 
> 

## <a name="billing-implications"></a>Faturalandırma etkileri
Canlı bir kodlama kanalı, API aracılığıyla "çalışıyor" durumuna geçiş yaptığı sürece faturalandırmaya başlar.   Ayrıca, Azure portal veya Azure Media Services Gezgini aracında (https://aka.ms/amse).

Aşağıdaki tabloda, kanal durumlarının API ve Azure portal fatura durumlarıyla nasıl eşlenme gösterilmektedir. Durumlar, API ve Portal UX arasında biraz farklıdır. Kanal, API aracılığıyla "çalışıyor" durumunda veya Azure portal "Ready" veya "streaming" durumunda olduğunda faturalandırma etkin olur.
Kanalın daha fazla faturalandırmasını engellemek için, API 'yi API aracılığıyla veya Azure portal durdurmanız gerekir.
Canlı kodlama kanalı ile işiniz bittiğinde kanallarınızı durdurmaktan siz sorumlusunuz.  Bir kodlama kanalının durdurulamaması, faturalandırmaya devam edilmesine neden olur.

### <a id="states"></a>Kanal durumları ve bunların faturalama moduyla nasıl eşlendikleri
Kanalın geçerli durumu. Olası değerler şunlardır:

* **Durduruldu**. Bu, oluşturulduktan sonra kanalın ilk durumudur (portalda otomatik başlatma seçili değilse). Bu durumda faturalandırma yapılmaz. Bu durumda, Kanal özellikleri güncelleştirilebilir ama akışa izin verilmez.
* **Başlatılıyor**. Kanal başlatılıyor. Bu durumda faturalandırma yapılmaz. Bu durum süresince güncelleştirmelere veya akışa izin verilmez. Bir sorun oluşursa Kanal, Durduruldu durumuna döndürülür.
* **Çalışıyor**. Kanal canlı akışları işleyebilir. Artık fatura kullanımı. Daha fazla faturalandırma yapılmasını engellemek için kanalı durdurmanız gerekir. 
* **Durduruluyor**. Kanal durduruluyor. Bu geçici durumda faturalandırma yapılmaz. Bu durum süresince güncelleştirmelere veya akışa izin verilmez.
* **Siliniyor**. Kanal siliniyor. Bu geçici durumda faturalandırma yapılmaz. Bu durum süresince güncelleştirmelere veya akışa izin verilmez.

Aşağıdaki tabloda, Kanal durumlarının faturalandırma modu ile nasıl eşleştiği gösterilir. 

| Kanal durumu | Portal Arabirimi Göstergeleri | BT faturalandırma mı? |
| --- | --- | --- |
| Başlangıç |Başlangıç |Hayır (geçici durum) |
| Çalışıyor |Hazır (çalışan program yok)<br/>or<br/>Akış (en az bir program çalışıyor) |YES |
| Durduruluyor |Durduruluyor |Hayır (geçici durum) |
| Durdurulan |Durdurulan |Hayır |

### <a name="automatic-shut-off-for-unused-channels"></a>Kullanılmayan kanallar için otomatik kapatma
25 Ocak 2016 tarihinden itibaren, uzun bir süre boyunca kullanılmayan bir durumda çalıştıktan sonra bir kanalı otomatik olarak durduran bir güncelleştirmeyi (canlı kodlama etkin olan) Media Services. Bu, etkin bir programı olmayan ve uzun bir süre için bir giriş katkısı akışı almamış olan kanallar için geçerlidir.

Kullanılmayan bir dönem için eşik, en az 12 saat, ancak değişikliğe tabidir.

## <a name="live-encoding-workflow"></a>Live Encoding Iş akışı
Aşağıdaki diyagramda, bir kanalın aşağıdaki protokollerden birinde tek bir bit hızı akışı aldığı canlı bir akış iş akışı temsil etmektedir: RTMP veya Kesintisiz Akış; daha sonra akışı çoklu bit hızına sahip bir akışa kodluyor. 

![Canlı iş akışı][live-overview]

## <a id="scenario"></a>Ortak canlı akış senaryosu
Yaygın canlı akış uygulamaları oluşturmak için gerekli olan genel adımlar aşağıdadır.

> [!NOTE]
> Canlı bir etkinlik için önerilen en uzun süre şu anda 8 saattir.
>
> Canlı kodlama için bir faturalandırma etkisi vardır ve "çalışıyor" durumundaki bir canlı kodlama kanalını, saatlik faturalandırma ücretlerine tabi kalabileceğini unutmayın. Ek saatlik ücretlerden kaçınmak için canlı akış olayınız tamamlandıktan sonra çalışan kanallarınızı hemen durdurmanız önerilir. 

1. Bilgisayara bir video kamera bağlayın. Aşağıdaki protokollerden birinde **tek** bit hızlı bir akış çıktısı sağlayan şirket içi bir Live Encoder başlatın ve yapılandırın: RTMP veya kesintisiz akış. 

    Bu adım, Kanalınızı oluşturduktan sonra da gerçekleştirilebilir.
2. Bir Kanal oluşturup başlatın. 
3. Kanal alma URL’sini alın. 

    Alma URL’si gerçek zamanlı kodlayıcı tarafından akışı Kanala göndermek için kullanılır.
4. Kanal önizleme URL’sini alın. 

    Kanalınızın canlı akışı düzgün şekilde aldığını doğrulamak için bu URL’yi kullanın.
5. Bir program oluşturun. 

    Azure portal kullanılırken, bir program oluşturmak bir varlık da oluşturur. 

    .NET SDK veya REST kullanırken bir varlık oluşturmanız ve bir program oluştururken bu varlığı kullanmayı belirtmeniz gerekir. 
6. Programla ilişkili varlığı yayımlayın.   

    >[!NOTE]
    >AMS hesabınız oluşturulduğunda hesabınıza **Durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumunda olması gerekir. 

7. Akışı ve arşivlemeyi başlatmaya hazır olduğunuzda programı başlatın.
8. İsteğe bağlı olarak, gerçek zamanlı kodlayıcıya bir reklam başlatması bildirilebilir. Reklam, çıktı akışına eklenir.
9. Olay için akışı ve arşivlemeyi durdurmak istediğinizde programı durdurun.
10. Programı silin (ve isteğe bağlı olarak varlığı da silin).   

> [!NOTE]
> Live Encoding kanalını durdurmayı unutmamak çok önemlidir. Gerçek zamanlı kodlama için saatlik faturalandırma etkisi olduğunu ve "çalışıyor" durumundaki canlı bir kodlama kanalının ayrıldığının fatura ücretlerine tabi olacağını unutmayın.  Ek saatlik ücretlerden kaçınmak için canlı akış olayınız tamamlandıktan sonra çalışan kanallarınızı hemen durdurmanız önerilir. 
> 
> 

## <a id="channel"></a>Kanalın giriş (ınest) konfigürasyonları
### <a id="Ingest_Protocols"></a>Alma akış protokolü
**Kodlayıcı türü** **Standart**olarak ayarlandıysa, geçerli seçenekler şunlardır:

* Tek bit hızı **RTMP**
* Tek bit hızlı **PARÇALANMıŞ MP4** (kesintisiz akış)

#### <a id="single_bitrate_RTMP"></a>Tek bit hızı RTMP
Dikkat edilmesi gerekenler:

* Gelen akış çoklu bit hızlı video içeremez
* Video akışı, 15 Mbps 'Lik bir ortalama bit hızında olmalıdır
* Ses akışı, 1 MB/sn 'Lik bir ortalama bit hızına sahip olmalıdır
* Desteklenen codec bileşenleri aşağıda verilmiştir:
* MPEG-4 AVC/H. bir video
* Temel, ana, yüksek profil (8 bit 4:2:0)
* High 10 profili (10 bit 4:2:0)
* Yüksek 422 profili (10 bit 4:2:2)
* MPEG-2 AAC-LC sesi
* Mono, stereo, surround (5,1, 7,1)
* 44,1 kHz örnekleme hızı
* MPEG-2 stili ADTS paketleme
* Önerilen kodlayıcılar şunları içerir:
* Telestream kablolu dönüştürme
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Tek bit hızlı Parçalanmış MP4 (Kesintisiz Akış)
Genellikle kullanım örneği:

Açık internet üzerinden giriş akışını yakın bir Azure veri merkezine göndermek için, Envivio Technologies, Ericsson, Ateme gibi satıcılardan şirket içi canlı kodlayıcıları kullanın.

Dikkat edilmesi gerekenler:

[Tek bit hızı RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP)ile aynıdır.

#### <a name="other-considerations"></a>Diğer konular
* Kanal veya ilişkili programları çalışıyorken giriş protokolünü değiştiremezsiniz. Farklı protokollere ihtiyacınız varsa her bir giriş protokolü için farklı bir kanal oluşturmalısınız.
* Gelen video akışı için en yüksek çözünürlük 1920x1080 ve en fazla 60 alan/saniye, titreşimli ise 30 kare/saniye olur.

### <a name="ingest-urls-endpoints"></a>Alma URL 'Leri (uç noktalar)
Kanal, canlı kodlayıcıda belirttiğiniz bir giriş uç noktası (alma URL 'SI) sağlar. böylece kodlayıcı, akışları kanallarınıza gönderebilir.

Bir kanal oluşturduğunuzda alma URL 'Lerini alabilirsiniz. Bu URL 'Leri almak için kanalın **çalışır** durumda olması gerekmez. Verileri kanala göndermeye başlamak için hazırsanız, **çalışma** durumunda olmalıdır. Kanal veri almaya başladıktan sonra, önizleme URL 'SI aracılığıyla akışınızı önizleyebilirsiniz.

Bir SSL bağlantısı üzerinden parçalanmış MP4 (Kesintisiz Akış) canlı akışını geri almaya yönelik bir seçeneğiniz vardır. SSL üzerinden almak için alma URL 'sini HTTPS olarak güncelleştirdiğinizden emin olun. Şu anda, AMS özel etki alanları ile SSL 'yi desteklemez.  

### <a name="allowed-ip-addresses"></a>İzin verilen IP adresleri
Bu kanala video yayımlamasına izin verilen IP adreslerini tanımlayabilirsiniz. İzin verilen IP adresleri tek bir IP adresi (örneğin, ' 10.0.0.1 '), bir IP adresi ve CıDR alt ağ maskesi kullanarak bir IP aralığı (örneğin, ' 10.0.0.1/22 ') veya bir IP adresi ve noktalı ondalık alt ağ maskesi kullanarak bir IP aralığı (örneğin,) olarak belirtilebilir. , ' 10.0.0.1 (255.255.252.0) ').

Herhangi bir IP adresi belirtilmezse ve bir kural tanımı yoksa hiçbir IP adresine izin verilmez. Tüm IP adreslerine izin vermek için, bir kural oluşturun ve 0.0.0.0/0 olarak ayarlayın.

## <a name="channel-preview"></a>Kanal önizlemesi
### <a name="preview-urls"></a>URL 'Leri Önizle
Kanallar, daha fazla işlem ve teslim yapmadan önce akışınızı önizlemek ve doğrulamak için kullandığınız bir önizleme uç noktası (önizleme URL 'SI) sağlar.

Kanalı oluştururken önizleme URL 'sini alabilirsiniz. URL 'YI almak için kanalın **çalışıyor** durumunda olması gerekmez.

Kanal verileri geri almaya başladıktan sonra, akışınızı önizleyebilirsiniz.

> [!NOTE]
> Şu anda önizleme akışı, belirtilen giriş türünden bağımsız olarak yalnızca parçalanmış MP4 (Kesintisiz Akış) biçiminde teslim edilebilir.  Akışınızı görüntülemek için Azure portal barındırılan bir oynatıcı kullanabilirsiniz.
> 
> 

### <a name="allowed-ip-addresses"></a>İzin verilen IP adresleri
Önizleme uç noktasına bağlanmasına izin verilen IP adreslerini tanımlayabilirsiniz. Hiçbir IP adresi belirtilmemişse, herhangi bir IP adresi izin verilmez. İzin verilen IP adresleri tek bir IP adresi (örneğin, ' 10.0.0.1 '), bir IP adresi ve CıDR alt ağ maskesi kullanarak bir IP aralığı (örneğin, ' 10.0.0.1/22 ') veya bir IP adresi ve noktalı ondalık alt ağ maskesi kullanarak bir IP aralığı (örneğin,) olarak belirtilebilir. , ' 10.0.0.1 (255.255.252.0) ').

## <a name="live-encoding-settings"></a>Canlı kodlama ayarları
Bu bölümde, kanalın **kodlama türü** **Standart**olarak ayarlandığında, kanal içindeki canlı kodlayıcı için ayarların nasıl ayarlanacağı açıklanmaktadır.

> [!NOTE]
> Katkı akışınız yalnızca tek bir ses izi içerebilir: birden çok ses parçasını almak şu anda desteklenmiyor. [Şirket içi canlı](media-services-live-streaming-with-onprem-encoders.md)kodlama ile gerçek zamanlı kodlama yaparken, birden çok ses parçası içeren kesintisiz akış protokolüne bir katkı akışı gönderebilirsiniz.
> 
> 

### <a name="ad-marker-source"></a>Ad işaretçisi kaynağı
Ad işaretçileri sinyallerinin kaynağını belirtebilirsiniz. Varsayılan değer **API**'dir ve bu, kanalın içindeki canlı kodlayıcının zaman uyumsuz bir **ad işaretçisi API 'sini**dinlemesi gerektiğini gösterir.

### <a name="cea-708-closed-captions"></a>CEA 708 kapalı açıklamalı altyazı
Live Encoder 'ın gelen videoda gömülü olan CEA 708 açıklamalı alt yazı verilerini yok saymasını belirten isteğe bağlı bir bayrak. Bayrak false (varsayılan) olarak ayarlandığında, kodlayıcı çıkış video akışlarına CEA 708 verilerini algılayıp yeniden ekleyecektir.

#### <a name="index"></a>Dizin
Tek bir program aktarım akışı (SPTS) içinde göndermeniz önerilir. Giriş akışı birden çok program içeriyorsa, kanal içindeki canlı kodlayıcı, girişte program eşleme tablosunu (DEVRESEL_ÖDEME) ayrıştırır, MPEG-2 AAC ADTS veya AC-3 Sistem-A veya AC-3 sistem-B veya MPEG-2 özel PES veya MPEG-1 akış türü adına sahip girdileri tanımlar Ses veya MPEG-2 ses ve bu, DEVRESEL_ÖDEME olarak belirtilen sırayla düzenler. Sıfır tabanlı dizin daha sonra bu düzenlemede n. girişi seçmek için kullanılır.

#### <a name="language"></a>Dil
Ses akışının, ENG gibi ISO 639-2 ile uyumlu dil tanımlayıcısı. Yoksa, varsayılan değer (tanımsız) olur.

### <a id="preset"></a>Sistem ön ayarı
Bu kanalda bulunan Live Encoder tarafından kullanılacak ön ayarı belirtir. Şu anda, izin verilen tek değer **Default720p** ' dir (varsayılan).

**Default720p** , videoyu aşağıdaki 6 katmana kodlayacaktır.

#### <a name="output-video-stream"></a>Çıkış video akışı

| Bit hızı | Genişlik | Yükseklik | MaxFPS | Profil | Çıkış akışı adı |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Yüksek |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Yüksek |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Yüksek |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Yüksek |Video_512x288_850kbps |
| 550 |384 |216 |30 |Yüksek |Video_384x216_550kbps |
| 200 |340 |192 |30 |Yüksek |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Çıkış ses akışı

Ses, 128 KB/sn 'de stereo AAC-LC ve 48 kHz örnekleme hızı olarak kodlanır.

## <a name="signaling-advertisements"></a>Sinyal duyuruları
Kanalınızda Live Encoding etkinleştirildiğinde, işlem hattınızda videoyu işleyen bir bileşen vardır ve onu işleyebilir. Giden Uyarlamalı bit hızı akışına SLA ve/veya tanıtımlar eklemek için kanala işaret edebilirsiniz. SLA 'lar, giriş canlı akışını belirli durumlarda (örneğin, ticari bir kesme sırasında) kapsamak için kullanabileceğiniz görüntülerdir. Reklam sinyalleri, video yürütücüsünün uygun zamanda bir tanıtıma geçiş yapmak gibi özel bir eylem (örneğin,) almak için giden akışa gömmekte olduğunuz zaman eşitlenmiş sinyallerdir. Bu amaçla kullanılan SCTE-35 sinyal mekanizmasına genel bakış için bu [bloga](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) bakın. Aşağıda, canlı olaylarınız için uygulayabileceğiniz tipik bir senaryo verilmiştir.

1. Görüntüleyicilerinizin olay başlamadan önce olay ÖNCESI bir görüntü almasını sağlayın.
2. İzleyicilerinizin olay bittikten sonra olay SONRASı görüntü almasını sağlayın.
3. Olay sırasında bir sorun varsa izleyicilerinizin hata-olay görüntüsü almasını sağlayın (örneğin, stadim 'de güç kesintisi).
4. Ticari bir kesme sırasında canlı olay akışını gizlemek için bir AD sonu görüntüsü gönderin.

Aşağıda, reklamları sinyal alırken ayarlayabileceğiniz özellikler verilmiştir. 

### <a name="duration"></a>Süre
Ticari kesmenin saniye cinsinden süresi. Bu, ticari kesmeyi başlatmak için sıfır olmayan pozitif bir değer olmalıdır. Bir ticari bir kesme devam ederken ve zaman içindeki ticari kesmeyi eşleştirirken süre sıfıra ayarlandığında, bu kesme iptal edilir.

### <a name="cueid"></a>Cuıd
Uygun eylemleri gerçekleştirmek üzere aşağı akış uygulaması tarafından kullanılacak ticari kesmenin benzersiz KIMLIĞI. Pozitif bir tamsayı olması gerekir. Bu değeri herhangi bir rastgele pozitif tamsayı olarak ayarlayabilir veya Işaret kimliklerini izlemek için bir yukarı akış sistemi kullanabilirsiniz. API aracılığıyla göndermeden önce herhangi bir kimliği pozitif tamsayılarla normalleştirmek için unutmayın.

### <a name="show-slate"></a>Kurşun rengi göster
İsteğe bağlı. Canlı Kodlayıcısı, ticari bir kesme sırasında [varsayılan kurşun](media-services-manage-live-encoder-enabled-channels.md#default_slate) görüntüye geçiş yapmak ve gelen video akışını gizlemek için bildirir. Ses, tablet sırasında da sessize. Varsayılan değer **false**'dur. 

Kullanılan görüntü, kanal oluşturma sırasında varsayılan kurşun varlık kimliği özelliği ile belirtilen bir değer olacaktır. Ekran görüntüsü boyutunu sığdırmak için kurşun kurşun rengi uzatılır. 

## <a name="insert-slate--images"></a>Kurşun görüntüleri Ekle
Kanal içindeki canlı kodlayıcı bir tablet görüntüsüne geçiş yapmak için sinyal verebilir. Ayrıca, devam eden bir kurşun ekranda sona erdirmek için de sinyal alınabilir. 

Live Encoder, bir tablet görüntüsüne geçiş yapmak ve örneğin bir ad kesmesi sırasında belirli durumlarda gelen video sinyalini gizlemek için yapılandırılabilir. Böyle bir kurşun değer yapılandırılmadıysa, bu ad kesmesi sırasında giriş videosu maskelenmez.

### <a name="duration"></a>Süre
Saniye cinsinden kurşun süre. Bu, kurşun rengi başlatmak için sıfır olmayan pozitif bir değer olmalıdır. Bir devam eden kurşun değer varsa ve sıfır süresi belirtilmişse, bu durumda bu, devam eden kurşun işlem sonlandırılır.

### <a name="insert-slate-on-ad-marker"></a>Ad işaretleyicisi üzerine kurşun ekler
True olarak ayarlandığında, bu ayar, Live Encoder 'ı bir ad kesmesi sırasında bir tablet görüntüsü eklemek üzere yapılandırır. Varsayılan değer true 'dur. 

### <a id="default_slate"></a>Varsayılan kurşun varlık kimliği

İsteğe bağlı. Tablet görüntüsünü içeren Media Services varlığının varlık kimliğini belirtir. Varsayılan değer null. 


> [!NOTE] 
> Kanalı oluşturmadan önce, aşağıdaki kısıtlamalara sahip kurşun kurşun görüntünün ayrılmış bir varlık olarak yüklenmesi gerekir (Bu varlık içinde başka hiçbir dosya olması gerekmez). Bu görüntü yalnızca, Live Encoder bir ad kesmesi nedeniyle bir kurşun ya da bir kurşun değer işareti eklemek için açıkça işaret edildiğinde kullanılır. Şu anda, Live Encoder ' giriş sinyali kayıp ' durumuna girdiğinde özel görüntü kullanma seçeneği yoktur. Bu özellik için [buradan](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel)oy verebilirsiniz.

* En fazla 1920x1080 çözüm içinde.
* En fazla 3 MB boyutunda.
* Dosya adının bir *. jpg uzantısı olmalıdır.
* Görüntü, söz konusu varlığın tek Assetdosyası olarak bir varlığa yüklenmelidir ve bu Assetdosyası birincil dosya olarak işaretlenmelidir. Varlık depolama şifreli olamaz.

**Varsayılan kurşun Işlem varlığı kimliği** belirtilmemişse ve **ad işaretçisi üzerine kurşun ekleme** değeri **true**olarak ayarlanırsa, giriş video akışını gizlemek için varsayılan bir Azure Media Services görüntüsü kullanılır. Ses, tablet sırasında da sessize. 

## <a name="channels-programs"></a>Kanalın programları
Kanal, bir canlı akıştaki segmentlerin yayımlanması ve depolanmasını denetlemenizi sağlayan programlarla ilişkilidir. Kanallar, Programları yönetir. Kanal ve program ilişkisi, bir kanalın sabit bir içerik akışına ve bir programın bu kanalda zaman aşımına uğramış bir olay kapsamına sahip olduğu geleneksel medyaya çok benzer.

Program için kaydedilen içeriği kaç saat tutmak istediğinizi **Arşiv Penceresi** uzunluğunu ayarlayarak belirleyebilirsiniz. Bu değer en az 5 dakika, en çok 25 saat olarak ayarlanabilir. Arşiv penceresi uzunluğu, istemcilerin geçerli canlı konumdan zaman içinde arayabilen en fazla sayıda süreyi de belirler. Olaylar belirtilen süre miktarından uzun sürebilir, ancak pencere uzunluğunun gerisine düşen içerik sürekli olarak atılır. Bu özelliğin bu değeri, istemci bildiriminin ne kadar uzayabileceğini de belirler.

Her program, akan içeriği depolayan bir varlıkla ilişkilendirilir. Bir varlık, Azure depolama hesabındaki bir Blok Blobu kapsayıcısına eşlenir ve varlık içindeki dosyalar bu kapsayıcıda blob olarak depolanır. Müşterilerinizin akışı görebilmesi için programı yayımlamak üzere ilgili varlık için bir OnDemand Bulucu oluşturmanız gerekir. Bu bulucuya sahip olmak, istemcilerinize sağlayabileceğiniz bir akış URL’si oluşturmanıza olanak tanır.

Bir kanal aynı anda çalışan üç programın aynısını destekler, böylece aynı gelen akış için birden fazla arşiv oluşturabilirsiniz. Bu özellik, gerektiğinde bir olayın farklı kısımlarını yayımlamanıza ve arşivlemenize olanak tanır. Örneğin, iş gereksiniminiz bir programın 6 saatini arşivlemek ancak son 10 dakikasını yayınlamak olabilir. Bunu yapmak için, eşzamanlı olarak çalışan iki program oluşturmanız gerekir. Bir program olayı 6 saat arşivlemek için ayarlanır ancak program yayımlanmaz. Diğer program 10 dakika arşivlenecek şekilde ve bu program yayımlanır.

Yeni olaylar için mevcut programları yeniden kullanmamalısınız. Bunun yerine, canlı akış uygulamaları programlama bölümünde açıklandığı gibi her olay için yeni bir program oluşturun ve başlatın.

Akışı ve arşivlemeyi başlatmaya hazır olduğunuzda programı başlatın. Olay için akışı ve arşivlemeyi durdurmak istediğinizde programı durdurun. 

Arşivlenen içeriği silmek için, programı durdurup silin ve ardından ilişkili varlığı silin. Bir program tarafından kullanılıyorsa varlık silinemez; önce programın silinmesi gerekir. 

Programı durdurup sildikten sonra bile, varlığı silmediğiniz sürece kullanıcılar, arşivlenen içeriğinizin isteğe bağlı video olarak akışını gerçekleştirebilir.

Arşivlenen içeriği tutmak istiyor ancak bu içeriğin akış için kullanılmasını istemiyorsanız, akış bulucuyu silin.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Canlı akışın küçük resim önizlemesi alınıyor
Live Encoding etkinleştirildiğinde, artık kanala ulaşan canlı akışın önizlemesini alabilirsiniz. Bu, canlı akışınızın gerçekten kanala ulaşılıp ulaşmadığını kontrol etmek için değerli bir araç olabilir. 

## <a id="states"></a>Kanal durumları ve durumların faturalandırma moduna nasıl eşlenir
Kanalın geçerli durumu. Olası değerler şunlardır:

* **Durduruldu**. Bu, Kanalın oluşturulduktan sonraki ilk durumudur. Bu durumda, Kanal özellikleri güncelleştirilebilir ama akışa izin verilmez.
* **Başlatılıyor**. Kanal başlatılıyor. Bu durum süresince güncelleştirmelere veya akışa izin verilmez. Bir sorun oluşursa Kanal, Durduruldu durumuna döndürülür.
* **Çalışıyor**. Kanal canlı akışları işleyebilir.
* **Durduruluyor**. Kanal durduruluyor. Bu durum süresince güncelleştirmelere veya akışa izin verilmez.
* **Siliniyor**. Kanal siliniyor. Bu durum süresince güncelleştirmelere veya akışa izin verilmez.

Aşağıdaki tabloda, Kanal durumlarının faturalandırma modu ile nasıl eşleştiği gösterilir. 

| Kanal durumu | Portal Arabirimi Göstergeleri | Faturalandırılmış mı? |
| --- | --- | --- |
| Başlangıç |Başlangıç |Hayır (geçici durum) |
| Çalışıyor |Hazır (çalışan program yok)<br/>or<br/>Akış (en az bir program çalışıyor) |Yes |
| Durduruluyor |Durduruluyor |Hayır (geçici durum) |
| Durdurulan |Durdurulan |Hayır |

> [!NOTE]
> Şu anda kanal başlangıç ortalaması yaklaşık 2 dakikadır, ancak her zaman 20 + dakikaya kadar sürebilir. Kanal sıfırlamaları 5 dakikaya kadar sürebilir.
> 
> 

## <a id="Considerations"></a>Konuları
* **Standart** kodlama türünde bir kanal giriş kaynağı/katkı akışı kaybı yaşadığında, kaynak videoyu/sesi bir hata kurşun ekranı ve sessizlik ile değiştirerek bunu dengeleyerek dengeler. Kanal, giriş/katkı akışı sürdürülene kadar bir tablet görüntüsü almaya devam edecektir. Canlı bir kanalın, 2 saatten uzun süre boyunca böyle bir durumda kalmamalıdır. Bu noktanın ötesinde, giriş yeniden bağlantı üzerindeki kanalın davranışı garanti edilmez, bu durum sıfırlama komutuna yanıt olarak değildir. Kanalı durdurmanız, silmeniz ve yeni bir tane oluşturmanız gerekir.
* Kanal veya ilişkili programları çalışıyorken giriş protokolünü değiştiremezsiniz. Farklı protokollere ihtiyacınız varsa her bir giriş protokolü için farklı bir kanal oluşturmalısınız.
* Gerçek zamanlı kodlayıcıyı her yeniden yapılandırdığınızda, kanalda **Reset** yöntemini çağırın. Kanalı sıfırlamadan önce programı durdurmanız gerekir. Kanalı sıfırladıktan sonra programı yeniden başlatın.
* Kanal yalnızca çalışır durumda olduğunda durdurulabilir ve kanaldaki tüm programlar durdurulur.
* Varsayılan olarak, Media Services hesabınıza yalnızca 5 Kanal ekleyebilirsiniz. Bu, tüm yeni hesaplarda geçici bir kotadır. Daha fazla bilgi için bkz. [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md).
* Kanal veya ilişkili programları çalışıyorken giriş protokolünü değiştiremezsiniz. Farklı protokollere ihtiyacınız varsa her bir giriş protokolü için farklı bir kanal oluşturmalısınız.
* Yalnızca kanalınızın **çalışır** durumda olması durumunda faturalandırılırsınız. Daha fazla bilgi için [Bu](media-services-manage-live-encoder-enabled-channels.md#states) bölüme bakın.
* Canlı bir etkinlik için önerilen en uzun süre şu anda 8 saattir. 
* **Çalışma** durumunda içerik akışı yapmak istediğiniz akış uç noktasına sahip olduğunuzdan emin olun.
* Kodlama önceden kümesi, 30 fps 'nin "Maks. kare hızı" kavramını kullanır. Bu nedenle, giriş 60fps/59.94 i ise, giriş çerçeveleri 30/29.97 fps 'e bırakılır/devre dışı bırakılır. Giriş 50 fps/50i ise, giriş çerçeveleri 25 fps 'e bırakılır/devre dışı bırakılır. Giriş 25 fps ise, çıkış 25 fps 'de kalır.
* Bittiğinde KANALLARıNıZı DURDURMAYı unutmayın. Bunu yapmazsanız faturalandırma devam eder.

## <a name="known-issues"></a>Bilinen Sorunlar
* Kanal başlangıç saati, ortalama 2 dakikalık bir süredir geliştirilmiştir, ancak artan talebe göre en fazla 20 + dakika sürer.
* Kurşun görüntüleri [burada](media-services-manage-live-encoder-enabled-channels.md#default_slate)açıklanan kısıtlamalara uymalıdır. 1920x1080 'den daha büyük bir varsayılan kurşun ile bir kanal oluşturmaya çalışırsanız, istek sonunda hata olur.
* Bir daha.... akışı bitirdiğinizde KANALLARıNıZı DURDURMAYı unutmayın. Bunu yapmazsanız faturalandırma devam eder.

## <a name="need-help"></a>Yardım mı gerekiyor?

[Yeni destek isteğine](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) giderek bir destek bileti açabilirsiniz

## <a name="next-step"></a>Sonraki adım

Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirim sağlayın
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>İlgili konular
[Azure Media Services ile canlı akış olayları sunma](media-services-overview.md)

[Portal ile Uyarlamalı bit hızlı bir akışa, bir tek bit hızında canlı kodlama gerçekleştiren kanallar oluşturun](media-services-portal-creating-live-encoder-enabled-channel.md)

[.NET SDK ile bir tek bit hızı ile Uyarlamalı bit hızında akışa canlı kodlama gerçekleştiren kanallar oluşturun](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[REST API ile kanalları yönetme](https://docs.microsoft.com/rest/api/media/operations/channel)

[Media Services kavramlar](media-services-concepts.md)

[Azure Media Services parçalanmış MP4 canlı alma belirtimi](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

