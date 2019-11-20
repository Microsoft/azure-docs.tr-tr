---
title: Çoklu bit hızı akışları oluşturan şirket içi kodlayıcılarla canlı akış-Azure | Microsoft Docs
description: 'Bu konuda, şirket içi kodlayıcıdan çoklu bit hızlı canlı akış alan bir kanalın nasıl ayarlanacağı açıklanır. Akış daha sonra, aşağıdaki uyarlamalı akış protokollerinden birini kullanarak bir veya daha fazla akış uç noktası aracılığıyla istemci kayıttan yürütme uygulamalarına teslim edilebilir: HLS, Kesintisiz Akış, TIRE.'
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a299c050be37d53acd01ddc2db580c4881eeae07
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "69015479"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Şirket içi kodlayıcılardan çoklu bit hızlı canlı akış alan kanallar ile çalışma

> [!NOTE]
> 12 Mayıs 2018 ' den itibaren Canlı Kanallar artık RTP/MPEG-2 aktarım akışı alma protokolünü desteklemezler. Lütfen RTP/MPEG-2 ' den RTMP veya parçalanmış MP4 (Kesintisiz Akış) alma protokolleriyle geçiş yapın.

## <a name="overview"></a>Genel Bakış
Azure Media Services, *Kanal* canlı akış içeriğini işlemek için bir işlem hattını temsil eder. Kanal, canlı giriş akışlarını iki şekilde alır:

* Şirket içi bir Live Encoder, Media Services ile gerçek zamanlı kodlama gerçekleştirmek için etkinleştirilmemiş kanala çoklu bit hızlı RTMP veya Kesintisiz Akış (parçalanmış MP4) akışı gönderir. Alınan akışlar daha fazla işlem yapmadan kanalları geçer. Bu yönteme *doğrudan geçiş*adı verilir. Live Encoder Ayrıca canlı kodlama için etkinleştirilmemiş bir kanala tek bit hızlı bir akış gönderebilir, ancak bunu önermiyoruz. Media Services, akışı isteyen müşterilere teslim eder.

  > [!NOTE]
  > Doğrudan geçiş yöntemi kullanmak, canlı akış yapmanın en ekonomik yoludur.


* Şirket içi bir Live Encoder, aşağıdaki biçimlerden birinde Media Services ile canlı kodlama gerçekleştirmek üzere etkinleştirilen kanala tek bit hızlı bir akış gönderir: RTMP veya Kesintisiz Akış (parçalanmış MP4). Kanal daha sonra, gelen tek bit hızlı akışın çoklu bit hızında (Uyarlamalı) bir video akışına canlı kodlamasını gerçekleştirir. Media Services, akışı isteyen müşterilere teslim eder.

Media Services 2,10 sürümünden başlayarak, bir kanal oluşturduğunuzda, kanalınızın giriş akışını nasıl almasını istediğinizi belirtebilirsiniz. Ayrıca, kanalın akışının gerçek zamanlı kodlamasını gerçekleştirmesini isteyip istemediğinizi belirtebilirsiniz. İki seçeneğiniz vardır:

* **Geçiş**: Çıkış olarak çoklu bit hızına sahip bir akışa (doğrudan geçiş akışı) sahip bir şirket içi Live Encoder kullanmayı planlıyorsanız bu değeri belirtin. Bu durumda, gelen akış herhangi bir kodlama olmadan çıkışa geçer. Bu, 2,10 sürümünden önceki bir kanalın davranışıdır. Bu makale, bu türden kanallarla çalışma hakkında ayrıntılı bilgi sağlar.
* **Live Encoding**: Tek bit hızlı canlı akışınızı çoklu bit hızına sahip bir akışa kodlamak için Media Services kullanmayı planlıyorsanız bu değeri seçin. Canlı kodlama kanalının **çalışır** durumda bırakılması faturalandırma ücretleri doğurur. Ek saatlik ücretlerden kaçınmak için canlı akış olayınız tamamlandıktan sonra çalışan kanallarınızı hemen durdurmanız önerilir. Media Services, akışı isteyen müşterilere teslim eder.

> [!NOTE]
> Bu makalede, gerçek zamanlı kodlama gerçekleştirmek için etkinleştirilmemiş kanalların öznitelikleri açıklanmaktadır. Canlı kodlama gerçekleştirmeye etkinleştirilen kanallarla çalışma hakkında daha fazla bilgi için bkz. [Azure Media Services kullanarak canlı akış çoklu bit hızına sahip akışlar oluşturma](media-services-manage-live-encoder-enabled-channels.md).
>
>Şirket içi kodlayıcılar hakkında daha fazla bilgi için, bkz. [Şirket içi kodlayıcılar Için önerilen](media-services-recommended-encoders.md).

Aşağıdaki diyagramda çoklu bit hızlı RTMP veya parçalanmış MP4 (Kesintisiz Akış) akışları çıkış olarak eklemek için şirket içi bir yerleşik Kodlayıcı kullanan bir canlı akış iş akışı temsil etmektedir.

![Canlı iş akışı][live-overview]

## <a id="scenario"></a>Ortak canlı akış senaryosu
Aşağıdaki adımlarda ortak canlı akış uygulamaları oluşturmayla ilgili görevler açıklanır.

1. Bilgisayara bir video kamera bağlayın. Çoklu bit hızlı RTMP veya parçalanmış MP4 (Kesintisiz Akış) akışına çıkış olarak sahip olan bir şirket içi canlı kodlayıcı başlatın ve yapılandırın. Daha fazla bilgi için bkz. [Azure Media Services RTMP Desteği ve Gerçek Zamanlı Kodlayıcılar](https://go.microsoft.com/fwlink/?LinkId=532824).

    Bu adımı, kanalınızı oluşturduktan sonra da gerçekleştirebilirsiniz.
2. Bir kanal oluşturup başlatın.

3. Kanal alma URL 'sini alın.

    Live Encoder, akışı kanala göndermek için alma URL 'sini kullanır.
4. Kanal önizleme URL 'sini alın.

    Kanalınızın canlı akışı düzgün şekilde aldığını doğrulamak için bu URL’yi kullanın.
5. Bir program oluşturun.

    Azure portal kullandığınızda, bir program oluşturmak bir varlık da oluşturur.

    .NET SDK veya REST kullandığınızda bir varlık oluşturmanız ve bir program oluştururken bu varlığın kullanılması gerektiğini belirtmeniz gerekir.
6. Programla ilişkili varlığı yayımlayın.   

    >[!NOTE]
    >Azure Media Services hesabınız oluşturulduğunda hesabınıza **durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumunda olması gerekir.

7. Akışı ve arşivlemeyi başlatmaya hazırsanız programı başlatın.

8. İsteğe bağlı olarak, gerçek zamanlı kodlayıcıya bir reklam başlatması bildirilebilir. Reklam, çıktı akışına eklenir.

9. Olay için akışı ve arşivlemeyi durdurmak istediğinizde programı durdurun.

10. Programı silin (ve isteğe bağlı olarak varlığı silin).     

## <a id="channel"></a>Bir kanalın açıklaması ve ilgili bileşenleri
### <a id="channel_input"></a>Kanal girişi (alma) konfigürasyonları
#### <a id="ingest_protocols"></a>Alma akış protokolü
Media Services, çoklu bit hızlı parçalanmış MP4 ve çoklu bit hızlı RTMP 'yi akış protokolleri olarak kullanarak canlı akışların kullanılmasını destekler. RTMP içe akış protokolü seçildiğinde, kanal için iki alma (giriş) uç noktası oluşturulur:

* **BIRINCIL URL**: Kanalın birincil RTMP alma uç noktasının tam URL 'sini belirtir.
* **IKINCIL URL** (isteğe bağlı): Kanalın ikincil RTMP alma uç noktasının tam URL 'sini belirtir.

Özellikle aşağıdaki senaryolar için, Alım akışınızı (Ayrıca kodlayıcı yük devretmesi ve hataya dayanıklılık) dayanıklılık ve hataya dayanıklılık düzeyini artırmak istiyorsanız ikincil URL 'YI kullanın:

- Tek Kodlayıcı hem birincil hem de ikincil URL 'Lere çift gönderiliyor:

    Bu senaryonun ana amacı, ağ dalgalanmalarına ve jiciler için daha fazla esneklik sağlamaktır. Bazı RTMP kodlayıcıları ağ bağlantısı kesilme iyi işlemez. Bir ağ bağlantısı kesildiğinde, bir kodlayıcı kodlamayı durdurabilir ve sonra bir yeniden bağlantı gerçekleştiğinde arabelleğe alınmış verileri göndermeyebilir. Bu, süreksizlik ve veri kaybına neden olur. Azure tarafında hatalı ağ veya bakım nedeniyle ağ bağlantısı kesiliyor olabilir. Birincil/ikincil URL 'Ler ağ sorunlarını azaltır ve denetimli bir yükseltme işlemi sağlar. Zamanlanan bir ağ bağlantısı kesildiğinde, Media Services birincil ve ikincil bağlantıları yönetir ve ikisi arasında gecikmeli bir bağlantı kesmeyi sağlar. Daha sonra kodlayıcılara veri göndermeyi ve yeniden bağlanmayı zamanla. Kesilme sırası rastgele olabilir, ancak birincil/ikincil veya ikincil/birincil URL 'Ler arasında her zaman bir gecikme olur. Bu senaryoda, kodlayıcı hala tek hata noktasıdır.

- Her kodlayıcı ayrılmış bir noktaya itimciyle birden çok kodlayıcı:

    Bu senaryo hem Kodlayıcı hem de ıngests yedekliliği sağlar. Bu senaryoda, encoder1 birincil URL 'ye gönderim ve encoder2 ikincil URL 'ye gönderim. Kodlayıcı başarısız olduğunda, diğer kodlayıcı veri göndermeye devam edebilir. Media Services, birincil ve ikincil URL 'Lerin bağlantısını aynı anda kesmediği için veri artıklığı korunabilir. Bu senaryo, kodlayıcılara zaman eşitlendiği ve tam olarak aynı verileri sağlayan varsayılmaktadır.  

- Hem birincil hem de ikincil URL 'Lere birden çok kodlayıcıitme:

    Bu senaryoda, her iki kodlayıcıda verileri birincil ve ikincil URL 'lere gönderir. Bu, en iyi güvenilirlik ve hataya dayanıklılık ve veri artıklığı sağlar. Bu senaryo, bir kodlayıcı çalışmayı durdursa bile, her iki kodlayıcı hatasını kabul edebilir ve bağlantıyı keser. Kodlayıcılara zaman eşitlendiğinde ve tam olarak aynı verileri sağlayan varsayılmaktadır.  

RTMP Live kodlayıcılar hakkında bilgi için bkz. [Azure MEDIA SERVICES RTMP desteği ve canlı kodlayıcılar](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Alma URL 'Leri (uç noktalar)
Kanal, canlı kodlayıcıda belirttiğiniz bir giriş uç noktası (alma URL 'SI) sağlar. böylece kodlayıcı, akışları kanallarınıza gönderebilir.   

Kanalı oluştururken alma URL 'Lerini alabilirsiniz. Bu URL 'Leri alabilmeniz için kanalın **çalışır** durumda olması gerekmez. Verileri kanala göndermeye başlamaya hazırsanız, kanal **çalışıyor** durumunda olmalıdır. Kanal veri almaya başladıktan sonra, önizleme URL 'SI aracılığıyla akışınızı önizleyebilirsiniz.

Bir SSL bağlantısı üzerinden parçalanmış MP4 (Kesintisiz Akış) canlı akışını geri almaya yönelik bir seçeneğiniz vardır. SSL üzerinden almak için alma URL 'sini HTTPS olarak güncelleştirdiğinizden emin olun. Şu anda, SSL üzerinden RTMP 'yi içe geçirilemez.

#### <a id="keyframe_interval"></a>Ana kare aralığı
Çoklu bit hızı akışı oluşturmak için şirket içi bir Live Encoder kullandığınızda, ana kare aralığı, söz konusu dış kodlayıcı tarafından kullanılan resim grubunun (GOP) süresini belirtir. Kanal bu gelen akışı aldıktan sonra, aşağıdaki biçimlerden birinde canlı akışınızı istemci kayıttan yürütme uygulamalarına gönderebilirsiniz: Kesintisiz Akış, HTTP üzerinden dinamik uyarlamalı akış (DASH) ve HTTP Canlı Akışı (HLS). Canlı akış yaparken, HLS her zaman dinamik olarak paketlenmiştir. Varsayılan olarak, Media Services, canlı kodlayıcıdan alınan ana kare aralığına göre HLS segmenti paketleme oranını (kesim başına parçalar) otomatik olarak hesaplar.

Aşağıdaki tabloda, segment süresinin nasıl hesaplandığı gösterilmektedir:

| Ana kare aralığı | HLS segmenti paketleme oranı (FragmentsPerSegment) | Örnek |
| --- | --- | --- |
| 3 saniyeden küçük veya buna eşit |3:1 |KeyFrameInterval (veya GOP) 2 saniyedir, varsayılan HLS segmenti paketleme oranı 3 ' e kadar 1 ' dir. Bu, 6 saniyelik bir HLS segmenti oluşturur. |
| 3-5 saniye |2:1 |KeyFrameInterval (veya GOP) 4 saniyedir, varsayılan HLS segmenti paketleme oranı 2 ile 1 arasındadır. Bu, 8 saniyelik bir HLS segmenti oluşturur. |
| 5 saniyeden fazla |1:1 |KeyFrameInterval (veya GOP) 6 saniyedir, varsayılan HLS segmenti paketleme oranı 1 ile 1 arasındadır. Bu, 6 saniyelik bir HLS segmenti oluşturur. |

Kanalın çıkışını yapılandırarak ve ChannelOutputHls üzerinde FragmentsPerSegment ayarlayarak, kesim başına parça oranını değiştirebilirsiniz.

Ayrıca, ChannelInput üzerinde keyFrameInterval özelliğini ayarlayarak ana kare aralığı değerini değiştirebilirsiniz. KeyFrameInterval 'ı açıkça ayarlarsanız, HLS segmenti paketleme oranı FragmentsPerSegment, daha önce açıklanan kurallar aracılığıyla hesaplanır.  

Hem keyFrameInterval hem de FragmentsPerSegment ayarlarsanız, Media Services ayarladığınız değerleri kullanır.

#### <a name="allowed-ip-addresses"></a>İzin verilen IP adresleri
Bu kanala video yayımlamasına izin verilen IP adreslerini tanımlayabilirsiniz. İzin verilen bir IP adresi aşağıdakilerden biri olarak belirtilebilir:

* Tek bir IP adresi (örneğin, 10.0.0.1)
* IP adresi ve CıDR alt ağ maskesi kullanan bir IP aralığı (örneğin, 10.0.0.1/22)
* Bir IP adresi ve noktalı ondalık alt ağ maskesi kullanan bir IP aralığı (örneğin, 10.0.0.1 (255.255.252.0))

Hiçbir IP adresi belirtilmemişse ve kural tanımı yoksa, hiçbir IP adresine izin verilmez. Tüm IP adreslerine izin vermek için, bir kural oluşturun ve 0.0.0.0/0 olarak ayarlayın.

### <a name="channel-preview"></a>Kanal önizlemesi
#### <a name="preview-urls"></a>URL 'Leri Önizle
Kanallar, daha fazla işlem ve teslim yapmadan önce akışınızı önizlemek ve doğrulamak için kullandığınız bir önizleme uç noktası (önizleme URL 'SI) sağlar.

Kanalı oluştururken önizleme URL 'sini alabilirsiniz. URL 'YI alabilmeniz için kanalın **çalışıyor** durumunda olması gerekmez. Kanal veri almaya başladıktan sonra, akışınızı önizleyebilirsiniz.

Şu anda önizleme akışı, belirtilen giriş türünden bağımsız olarak yalnızca parçalanmış MP4 (Kesintisiz Akış) biçiminde teslim edilebilir. Kesintisiz akışı test etmek için [kesintisiz akış sistem durumu İzleyicisi](https://playready.directtaps.net/smoothstreaming/) oyuncusu kullanabilirsiniz. Akışınızı görüntülemek için Azure portal barındırılan bir oynatıcı da kullanabilirsiniz.

#### <a name="allowed-ip-addresses"></a>İzin verilen IP adresleri
Önizleme uç noktasına bağlanmasına izin verilen IP adreslerini tanımlayabilirsiniz. Hiçbir IP adresi belirtilmemişse, herhangi bir IP adresine izin verilir. İzin verilen bir IP adresi aşağıdakilerden biri olarak belirtilebilir:

* Tek bir IP adresi (örneğin, 10.0.0.1)
* IP adresi ve CıDR alt ağ maskesi kullanan bir IP aralığı (örneğin, 10.0.0.1/22)
* Bir IP adresi ve noktalı ondalık alt ağ maskesi kullanan bir IP aralığı (örneğin, 10.0.0.1 (255.255.252.0))

### <a name="channel-output"></a>Kanal çıkışı
Kanal çıktısı hakkında daha fazla bilgi için, bkz. [ana kare aralığı](#keyframe_interval) bölümü.

### <a name="channel-managed-programs"></a>Kanal tarafından yönetilen programlar
Kanal, canlı bir akışta parçaların yayımlanmasını ve depolanmasını denetlemek için kullanabileceğiniz programlarla ilişkilendirilir. Kanalları programları yönetir. Kanal ve program ilişkisi, bir kanalın sabit bir içerik akışına ve bir programın bu kanalda zaman aşımına uğramış bir olay kapsamına sahip olduğu geleneksel medyaya benzerdir.

Program için kaydedilen içeriği kaç saat tutmak istediğinizi **Arşiv Penceresi** uzunluğunu ayarlayarak belirleyebilirsiniz. Bu değer en az 5 dakika, en çok 25 saat olarak ayarlanabilir. Arşiv penceresi uzunluğu, istemcilerin geçerli canlı konumdan zaman içinde arayabilen en fazla sayıda süreyi de belirler. Olaylar belirtilen süre miktarından uzun sürebilir, ancak pencere uzunluğunun gerisine düşen içerik sürekli olarak atılır. Bu özelliğin bu değeri, istemci bildiriminin ne kadar uzayabileceğini de belirler.

Her program, akan içeriği depolayan bir varlıkla ilişkilendirilir. Bir varlık, Azure depolama hesabındaki bir Blok Blobu kapsayıcısına eşlenir ve varlık içindeki dosyalar bu kapsayıcıda blob olarak depolanır. Müşterilerinizin akışı görüntülemesi için programı yayımlamak üzere, ilişkili varlık için bir OnDemand Bulucu oluşturmanız gerekir. Bu bulucuyu, istemcilerinize sağlayabilmeniz için bir akış URL 'SI oluşturmak için kullanabilirsiniz.

Kanal, en fazla üç eşzamanlı çalışan programı destekler, böylece aynı gelen akış için birden fazla arşiv oluşturabilirsiniz. Gerektiğinde, bir olayın farklı parçalarını yayımlayabilir ve arşivleyebilirsiniz. Örneğin, iş gereksinimizin bir programın 6 saati arşivlemesini, ancak yalnızca son 10 dakikayı yayınladığınızı düşünün. Bunu yapmak için, eşzamanlı olarak çalışan iki program oluşturmanız gerekir. Bir program, etkinliğin altı saatini arşivlemek için ayarlanmıştır, ancak program yayımlanmaz. Diğer program 10 dakika için arşiv olarak ayarlanır ve bu program yayımlanır.

Yeni olaylar için mevcut programları yeniden kullanmamalısınız. Bunun yerine, her olay için yeni bir program oluşturun. Akışı ve arşivlemeyi başlatmaya hazırsanız programı başlatın. Olay için akışı ve arşivlemeyi durdurmak istediğinizde programı durdurun.

Arşivlenmiş içeriği silmek için programı durdurup silin ve ardından ilişkili varlığı silin. Bir program tarafından kullanılıyorsa varlık silinemez. Önce programın silinmesi gerekir.

Programı durdurup sildikten sonra bile, kullanıcılar, varlığı silinceye kadar arşivlenmiş içeriğinizi isteğe bağlı bir video olarak akışa alabilir. Arşivlenmiş içeriği sürdürmek istiyorsanız ancak akış için kullanılabilir değilse, akış bulucuyu silin.

## <a id="states"></a>Kanal durumları ve faturalandırma
Bir kanalın geçerli durumu için olası değerler şunlardır:

* **Durduruldu**: Bu, kanalın oluşturulduktan sonraki ilk durumudur. Bu durumda, kanal özellikleri güncelleştirilebilir ama akışa izin verilmez.
* **Başlangıç**: Kanal başlatılıyor. Bu durum süresince güncelleştirmelere veya akışa izin verilmez. Bir hata oluşursa, kanal **durdurulmuş** duruma geri döner.
* **Çalıştırma**: Kanal Canlı akışları işleyebilir.
* **Durduruluyor**: Kanal durduruluyor. Bu durum süresince güncelleştirmelere veya akışa izin verilmez.
* **Silme**: Kanal siliniyor. Bu durum süresince güncelleştirmelere veya akışa izin verilmez.

Aşağıdaki tabloda, kanal durumlarının faturalandırma modu ile nasıl eşleştiği gösterilir.

| Kanal durumu | Portal kullanıcı arabirimi göstergeleri | Faturalandırılmış mı? |
| --- | --- | --- |
| **Şunlar** |**Şunlar** |Hayır (geçici durum) |
| **Çalıştıran** |**Hazırlanıyor** (çalışan program yok)<p><p>veya<p>**Akış** (en az bir çalışan program) |Evet |
| **Durdurulamadı** |**Durdurulamadı** |Hayır (geçici durum) |
| **Durdurulacağını** |**Durdurulacağını** |Hayır |

## <a id="cc_and_ads"></a>Kapalı açıklamalı altyazı ve ad ekleme
Aşağıdaki tabloda kapalı açıklamalı altyazı ve ad ekleme için desteklenen standartlar gösterilmektedir.

| Standart | Notlar |
| --- | --- |
| CEA-708 ve EA-608 (708/608) |CEA-708 ve EIA-608, Birleşik Devletler ve Kanada için kapalı açıklamalı alt yazı standartlarıdır.<p><p>Şu anda, resim yazısı yalnızca kodlanmış giriş akışında taşınmışsa desteklenir. Media Services gönderilen kodlanmış akışta 608 veya 708 açıklamalı altyazı ekleyebilen canlı Medya Kodlayıcısı kullanmanız gerekir. Media Services, ekli açıklamalı alt yazıların içeriğini görüntüleyicilerinize sunar. |
| TTML içinde. ismt (Kesintisiz Akış metin parçaları) |Media Services dinamik paketleme, istemcilerinizin aşağıdaki biçimlerden herhangi birine içerik akışını sağlar: DASH, HLS veya Kesintisiz Akış. Ancak,. ismt (Kesintisiz Akış metin parçaları) içindeki açıklamalı alt yazılar ile parçalanmış MP4 (Kesintisiz Akış) aldıysanız, akışı yalnızca Kesintisiz Akış istemcilerine teslim edebilirsiniz. |
| SCTE-35 |SCTE-35, reklam ekleme işlemini işaret etmek için kullanılan dijital bir sinyal sistemidir. Aşağı akış alıcıları, ayrılan süre için tanıtımı akışa almak için sinyali kullanır. SCTE-35, giriş akışında seyrek bir izleme olarak gönderilmelidir.<p><p>Şu anda, ad sinyallerini taşıyan tek desteklenen giriş akışı biçimi parçalanmış MP4 (Kesintisiz Akış). Yalnızca desteklenen çıkış biçimi de Kesintisiz Akış. |

## <a id="considerations"></a>Konuları
Bir kanala çoklu bit hızında akış göndermek için şirket içi bir Live Encoder kullandığınızda aşağıdaki kısıtlamalar uygulanır:

* Alma noktalarına veri göndermek için yeterli boş Internet bağlantısına sahip olduğunuzdan emin olun.
* İkincil alma URL 'SI kullanmanın ek bant genişliği olması gerekir.
* Gelen çoklu bit hızı akışı, en fazla 10 video kalite düzeyine (katman) ve en fazla 5 ses parçası olabilir.
* Video kalite seviyelerinin herhangi biri için en yüksek ortalama bit hızı 10 MB/sn olmalıdır.
* Tüm video ve ses akışlarının Ortalama bit hızlarının toplamı 25 MB/sn olmalıdır.
* Kanal veya ilişkili programları çalışırken giriş protokolünü değiştiremezsiniz. Farklı protokollere ihtiyacınız varsa her bir giriş protokolü için farklı bir kanal oluşturmalısınız.
* Kanalınıza tek bir bit hızı aktarabilirsiniz. Ancak kanal akışı işlemediğinden, istemci uygulamaları tek bir bit hızı akışı da alacaktır. (Bu seçeneği önermiyoruz.)

Kanallarla ve ilgili bileşenlerle çalışma ile ilgili başka konular aşağıda verilmiştir:

* Gerçek zamanlı kodlayıcıyı her yeniden yapılandırdığınızda, kanalda **Reset** yöntemini çağırın. Kanalı sıfırlamadan önce programı durdurmanız gerekir. Kanalı sıfırladıktan sonra programı yeniden başlatın.

  > [!NOTE]
  > Programı yeniden başlattığınızda, bunu yeni bir varlıkla ilişkilendirmeniz ve yeni bir bulucu oluşturmanız gerekir. 
  
* Kanal, yalnızca **çalışır** durumda olduğunda ve kanaldaki tüm programlar durdurulduğunda durdurulabilir.
* Varsayılan olarak, Media Services hesabınıza yalnızca beş kanal ekleyebilirsiniz. Daha fazla bilgi için bkz. [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md).
* Yalnızca kanalınızın **çalışır** durumda olması durumunda faturalandırılırsınız. Daha fazla bilgi için bkz. [Kanal durumları ve faturalama](media-services-live-streaming-with-onprem-encoders.md#states) bölümü.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Geri Bildirim
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>İlgili konular
[Şirket içi kodlayıcılar önerilir](media-services-recommended-encoders.md)

[Azure Media Services parçalanmış MP4 yaşamlarını alma belirtimi](../media-services-fmp4-live-ingest-overview.md)

[Azure Media Services genel bakış ve genel senaryolar](media-services-overview.md)

[Media Services kavramlar](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
