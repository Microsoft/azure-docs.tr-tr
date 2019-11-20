---
title: Azure Media Services kullanarak canlı akışa genel bakış | Microsoft Docs
description: Bu konu, Azure Media Services kullanarak canlı akışa genel bakış sunar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5ab4a6b96df964497e20b2b93c59febb0e24393c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035889"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Media Services kullanarak canlı akışa genel bakış

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Genel Bakış

Azure Media Services ile canlı akış olayları sunarken, aşağıdaki bileşenler genellikle dahil edilir:

* Etkinliği yayınlamak için kullanılan bir kamera.
* Kameradan gelen sinyalleri bir canlı akış hizmetine gönderilen akışlara dönüştüren gerçek zamanlı bir video kodlayıcısı.

    İsteğe bağlı olarak, birden çok, gerçek zamanlı, zaman eşitlenmiş kodlayıcı. Yüksek kullanılabilirlik ve üstün kaliteli bir deneyim gerektiren bazı önemli etkinliklerde, veri kaybı olmadan sorunsuz yük devretme elde etmek için zaman eşitlemeli aktif-aktif yedekli kodlayıcılar kullanılması önerilir.
* Aşağıdakileri yapmanıza olanak sağlayan bir canlı akış hizmeti:

  * çeşitli canlı akış protokollerini (RTMP veya Kesintisiz Akış gibi) kullanarak canlı içerik alma,
  * (isteğe bağlı olarak) akışınızı, bit hızı uyarlamalı akışa kodlama,
  * canlı akışınızı önizleme,
  * alınan içeriği daha sonra akışla aktarmak üzere kaydedip depolama (İsteğe Bağlı Video),
  * içeriği yaygın akış protokolleri (örneğin MPEG DASH, Kesintisiz, HLS) aracılığıyla doğrudan müşterilerinize veya başkalarına dağıtım için bir Content Delivery Network’e (CDN) teslim etme.

**Microsoft Azure Media Services** (AMS) canlı akış içeriğinizi alma, kodlama, önizleme, depolama ve teslim etme olanağı sağlar.

Media Services sayesinde [dinamik paketlemeden](media-services-dynamic-packaging-overview.md)yararlanarak, canlı akışlarınızı, hizmet 'e gönderilen katkı AKıŞıNDAN MPEG Dash, hls ve kesintisiz akış biçimlerinde yayınlanmasını sağlayabilirsiniz. Görüntüleyicilerinizin her türlü HLS, ÇIZGI veya Kesintisiz Akış uyumlu oyuncularla canlı akışı kayıttan yürütebileceği. Bu protokollerden herhangi birine akışını sunmak için Web veya mobil uygulamalarınızda Azure Media Player kullanabilirsiniz.

> [!NOTE]
> 12 Mayıs 2018 ' den itibaren Canlı Kanallar artık RTP/MPEG-2 aktarım akışı alma protokolünü desteklemezler. Lütfen RTP/MPEG-2 ' den RTMP veya parçalanmış MP4 (Kesintisiz Akış) alma protokolleriyle geçiş yapın.

## <a name="streaming-endpoints-channels-programs"></a>Akış uç noktaları, kanallar, programlar

Azure Media Services’de **Kanallar**, **Programlar** ve **Akış Uç Noktaları**; alma biçimlendirme, DVR, güvenlik, ölçeklenebilirlik ve yedeklilik dahil olmak üzere tüm canlı akış işlevlerini idare eder.

**Kanal**, canlı akış içeriğinin işleneceği bir işlem hattını temsil eder. Kanal aşağıdaki yollarla bir canlı girdi akışı alabilir:

* Şirket içi bir gerçek zamanlı kodlayıcı, çoklu bit hızına sahip **RTMP** veya **Kesintisiz Akışı** (parçalanmış MP4) **doğrudan geçiş** teslimi için yapılandırılmış Kanala gönderir. **Doğrudan geçiş** teslimi, alınan akışların herhangi başka bir işlemeye uğramadan **Kanallardan** geçmesidir. Çoklu bit hızı Kesintisiz Akış çıkış yapan aşağıdaki canlı kodlayıcıları kullanabilirsiniz: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco ve elete. Aşağıdaki Live kodlayıcılar çıkış RTMP: Adobe Flash Media Live Encoder (FMLE), Telestream Kablolü, Haivision, Teradek ve karmaşık Aster transcoders.  Gerçek zamanlı bir kodlayıcı, gerçek zamanlı kodlama için etkinleştirilmemiş bir kanala tek bit hızlı bir akış da gönderebilir, ancak bu işlem önerilmez. İstendiğinde, Media Services akışı müşterilere teslim eder.

  > [!NOTE]
  > Uzun bir dönem içerisinde birden çok etkinlik gerçekleştirecekseniz ve zaten şirket içi kodlayıcılara yatırım yaptıysanız, doğrudan geçiş yöntemini kullanmak canlı akış yapmanın en ekonomik yoludur. [Fiyatlandırma](https://azure.microsoft.com/pricing/details/media-services/) detaylarına bakın.
  > 
  > 
* Şirket içi bir Live Encoder, aşağıdaki biçimlerden birinde Media Services ile canlı kodlama gerçekleştirmek üzere etkinleştirilen kanala tek bit hızlı bir akış gönderir: RTMP veya Kesintisiz Akış (parçalanmış MP4). RTMP çıktısına sahip aşağıdaki canlı kodlayıcılara bu türdeki kanallarla birlikte çalışmak üzere bilinmektedir: Telestream kablolu dönüştürme, FMLE. Ardından Kanal, gelen tek bit hızlı akışın çoklu bit hızlı (uyarlamalı) bir video akışına gerçek zamanlı kodlanmasını gerçekleştirir. İstendiğinde, Media Services akışı müşterilere teslim eder.

Media Services 2,10 sürümü ile başlayarak, bir kanal oluşturduğunuzda, kanalınızın giriş akışını almasını istediğiniz şekilde ve kanalın, akışın canlı kodlamasını gerçekleştirmesini isteyip istemediğinizi belirtebilirsiniz. İki seçeneğiniz vardır:

* **Hiçbiri** (doğrudan geçiş) – çoklu bit hızında akış (doğrudan geçiş akışı) çıktısını alacak bir şirket içi Live Encoder kullanmayı planlıyorsanız bu değeri belirtin. Bu durumda, gelen akış herhangi bir kodlama olmadan çıkışa geçirilir. Bu, 2,10 sürümünden önceki bir kanalın davranışıdır.  
* **Standart** – tek bit hızı canlı akışınızı çoklu bit hızına sahip akışa kodlamak için Media Services kullanmayı planlıyorsanız bu değeri seçin. Bu yöntem, sık olmayan olaylar için ölçeği hızla ölçeklendirmeye yönelik daha ekonomik bir yöntemdir. Canlı kodlama için bir faturalandırma etkisi olduğunu ve "çalışıyor" durumundaki canlı bir kodlama kanalını terk ettiğini unutmayın.  Ek saatlik ücretlerden kaçınmak için canlı akış olayınız tamamlandıktan sonra çalışan kanallarınızı hemen durdurmanız önerilir.

## <a name="comparison-of-channel-types"></a>Kanal türlerinin karşılaştırması

Aşağıdaki tabloda, Media Services desteklenen iki kanal türünü Karşılaştırma Kılavuzu sunulmaktadır

| Özellik | Doğrudan geçiş kanalı | Standart Kanal |
| --- | --- | --- |
| Tek bit hızı girişi, bulutta birden çok bit hızında kodlanır |Hayır |Evet |
| Maksimum çözünürlük, katman sayısı |1080p, 8 katman, 60 + fps |720p, 6 katman, 30 fps |
| Giriş protokolleri |RTMP, Kesintisiz Akış |RTMP, Kesintisiz Akış |
| Fiyat |[Fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/media-services/) bakın ve "canlı video" sekmesine tıklayın |[Fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/media-services/) bakın |
| En fazla çalışma süresi |7/24 |8 saat |
| SLA ekleme desteği |Hayır |Evet |
| Ad sinyali için destek |Hayır |Evet |
| Geçiş CEA 608/708 açıklamalı alt yazılar |Evet |Evet |
| Tekdüzen olmayan giriş GOPs desteği |Evet |Hayır – giriş sabit 2sec GOPs olmalıdır |
| Değişken çerçeve hızı girişi desteği |Evet |Hayır – giriş sabit kare oranı olmalıdır.<br/>Küçük çeşitlemeler, örneğin, yüksek hareket sahneleri sırasında toleranslı olarak dağıtılır. Ancak kodlayıcı 10 kare/sn 'ye bırakamıyor. |
| Giriş akışı kaybedildiği zaman kanalların otomatik olarak kaybolması |Hayır |12 saat sonra, çalışan bir program yoksa |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Şirket içi kodlayıcılardan çoklu bit hızlı canlı akış alan Kanallar ile çalışma (doğrudan geçiş)

Aşağıdaki diyagramda, AMS platformunun **doğrudan geçiş** iş akışında rol oynayan başlıca parçaları gösterilmektedir.

![Canlı iş akışı](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Daha fazla bilgi için bkz. [Şirket İçi Kodlayıcılardan Çoklu Bit Hızlı Canlı Akış Alan Kanallar ile Çalışma](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Azure Media Services ile gerçek zamanlı kodlama gerçekleştirmek için etkinleştirilmiş Kanallar ile çalışma

Aşağıdaki diyagramda, AMS platformunun bir Kanalın, Media Services ile kodlama gerçekleştirmek için etkinleştirildiği Canlı Akış iş akışında rol oynayan başlıca parçaları gösterilmektedir.

![Canlı iş akışı](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Daha fazla bilgi için bkz. [Azure Media Services ile Gerçek Zamanlı Kodlama Gerçekleştirmek İçin Etkinleştirilmiş Kanallar ile Çalışma](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Bir kanalın açıklaması ve ilgili bileşenleri

### <a name="channel"></a>Kanal

Media Services, [Kanal](https://docs.microsoft.com/rest/api/media/operations/channel)s 'ler canlı akış içeriğini işlemeden sorumludur. Kanal, daha sonra canlı bir transcoder için sağladığınız bir giriş uç noktası (alma URL 'SI) sağlar. Kanal Canlı geçiş aşamasından canlı giriş akışları alır ve bir veya daha fazla StreamingEndpoints aracılığıyla akış için kullanılabilir hale getirir. Kanallar Ayrıca, daha fazla işlem ve teslim yapmadan önce akışınızı önizlemek ve doğrulamak için kullandığınız bir önizleme uç noktası (önizleme URL 'SI) sağlar.

Kanalı oluştururken alma URL 'sini ve önizleme URL 'sini alabilirsiniz. Bu URL 'Leri almak için kanalın başlatılmış durumda olması gerekmez. Canlı bir transleyici 'den kanala veri göndermeye başlamak için, kanalın başlatılmış olması gerekir. Canlı dönüştürücü verileri almaya başladıktan sonra, akışınızı önizleyebilirsiniz.

Her Media Services hesap birden çok kanal, birden çok program ve birden çok StreamingEndpoints içerebilir. Bant genişliği ve güvenlik ihtiyaçlarına bağlı olarak, StreamingEndpoint Hizmetleri bir veya daha fazla kanala ayrılabilir. Herhangi bir Streammingendpoint herhangi bir kanaldan çekme yapabilir.

Bir kanal oluştururken, izin verilen IP adreslerini aşağıdaki biçimlerden birinde belirtebilirsiniz: 4 sayıdan oluşan IPv4 adresi, CıDR adres aralığı.

### <a name="program"></a>Program
Bir [Program](https://docs.microsoft.com/rest/api/media/operations/program) canlı bir akışta parçaların yayımlanmasını ve depolanmasını denetlemenize olanak sağlar. Kanallar, Programları yönetir. Kanal ve Program arasındaki ilişki, kanalın sürekli bir içerik akışının bulunduğu ve programın bu kanalda zamanlanmış bir olayı kapsadığı geleneksel medyadaki ilişkiye benzer.
**ArchiveWindowLength** özelliğini ayarlayarak program için kaydedilen içeriği bekletmek istediğiniz saat sayısını belirtebilirsiniz. Bu değer en az 5 dakika, en çok 25 saat olarak ayarlanabilir.

ArchiveWindowLength Ayrıca, istemcilerin geçerli canlı konumdan zaman içinde arayamayacak maksimum süreyi belirler. Olaylar belirtilen süre miktarından uzun sürebilir, ancak pencere uzunluğunun gerisine düşen içerik sürekli olarak atılır. Bu özelliğin değeri, istemci bildirimlerinin ne kadar büyüyeceğini de belirler.

Her program bir Varlık ile ilişkilidir. Programı yayımlamak için ilişkili varlık için bir bulucu oluşturmanız gerekir. Bu bulucuya sahip olmak, istemcilerinize sağlayabileceğiniz bir akış URL’si oluşturmanıza olanak tanır.

Bir kanal eşzamanlı çalışan üçe kadar olayı destekler, böylece aynı gelen akışın birden fazla arşivini oluşturabilirsiniz. Bu özellik, gerektiğinde bir olayın farklı kısımlarını yayımlamanıza ve arşivlemenize olanak tanır. Örneğin, iş gereksiniminiz bir programın 6 saatini arşivlemek ancak son 10 dakikasını yayınlamak olabilir. Bunu yapmak için, eşzamanlı olarak çalışan iki program oluşturmanız gerekir. Bir program olayı 6 saat arşivlemek için ayarlanır ancak program yayımlanmaz. Diğer program 10 dakika arşivlenecek şekilde ve bu program yayımlanır.

## <a name="billing-implications"></a>Faturalandırma etkileri
Bir kanal, API aracılığıyla "çalışıyor" durumuna geçiş yaptığı anda faturalandırmaya başlar.  

Aşağıdaki tabloda, kanal durumlarının API ve Azure portal fatura durumlarıyla nasıl eşlenme gösterilmektedir. Durumların API ve Portal UX arasında biraz farklı olduğunu unutmayın. Kanal, API aracılığıyla "çalışıyor" durumunda veya Azure portal "Ready" veya "streaming" durumunda olduğunda faturalandırma etkin olur.

Kanalın daha fazla faturalandırmasını engellemek için, API 'yi API aracılığıyla veya Azure portal durdurmanız gerekir.
Kanal ile işiniz bittiğinde kanallarınızı durdurmaktan siz sorumlusunuz. Kanalın durdurulamaması, faturalandırmaya devam edilmesine neden olur.

> [!NOTE]
> Standart kanallarla çalışırken, AMS, giriş akışı kaybolduktan sonra hala "çalışıyor" durumunda 12 saat içinde olan herhangi bir kanalı otomatik olarak kanalıdır ve çalışan bir program yoktur. Ancak, kanalın "çalışıyor" durumunda olduğu süre için faturalandırılırsınız.
>
>

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
| Başlatılıyor |Başlatılıyor |Hayır (geçici durum) |
| Çalışıyor |Hazır (çalışan program yok)<br/>veya<br/>Akış (en az bir program çalışıyor) |EVET |
| Durduruluyor |Durduruluyor |Hayır (geçici durum) |
| Durduruldu |Durduruldu |Hayır |

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>İlgili konular
[Azure Media Services parçalanmış MP4 canlı alma belirtimi](../media-services-fmp4-live-ingest-overview.md)

[Azure Media Services Live Encoding gerçekleştirmek üzere etkinleştirilen kanallarla çalışma](media-services-manage-live-encoder-enabled-channels.md)

[Şirket İçi Kodlayıcılardan Çoklu Bit Hızlı Canlı Akış Alan Kanallar ile Çalışma](media-services-live-streaming-with-onprem-encoders.md)

[Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md).  

[Media Services kavramlar](media-services-concepts.md)
