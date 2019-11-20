---
title: Filtreler ve dinamik bildirimler | Microsoft Docs
description: Bu konuda, istemci akışı için bir stream'ın belirli bölümlerine kullanabilmesi için filtreler oluşturmayı açıklar. Media Services, bu seçmeli akışı arşivlemek için dinamik bildirimler oluşturuyor.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 1234263fa800a17d0a5c235df54ca2751e3094bb
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "69015861"
---
# <a name="filters-and-dynamic-manifests"></a>Filtreler ve dinamik bildirimler

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Media Services sürümünü seçin:"]
> * [Sürüm 2](media-services-dynamic-manifest-overview.md)
> * [Sürüm 3](../latest/filters-dynamic-manifest-overview.md)

2,17 sürümünden itibaren, Media Services varlıklarınız için filtreler tanımlamanızı sağlar. Bu filtreler, müşterilerinizin şu şekilde işlem yapmasına izin veren sunucu tarafı kurallarıdır: videonun yalnızca bir bölümünü kayıttan oynat (videonun tamamını çalmak yerine) veya yalnızca müşterinizin cihazının işleyebileceği ses ve video çevirilerinin bir alt kümesini belirtin ( varlıkla ilişkili tüm yorumlamaları yerine). Varlıklarınızın bu şekilde filtrelenmesi, müşterinizin, belirtilen filtreye göre video akışı isteği üzerine oluşturulan **dinamik bildirimle**gerçekleştirilir.

Bu konu, filtre kullanmanın müşterilerinizin faydalı olabileceği yaygın senaryoları ve program aracılığıyla filtrelerin nasıl oluşturulduğunu gösteren konuların bağlantılarını anlatmaktadır.

## <a name="overview"></a>Genel Bakış
İçeriğinizi müşterilere sunarken (canlı etkinlik veya isteğe bağlı video akışı), farklı ağ koşulları altında çeşitli cihazlara yüksek kaliteli bir video sunmaktır. Bu hedefe ulaşmak için aşağıdakileri yapın:

* akışınızı çoklu bit hızında ([Uyarlamalı bit hızı](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) video akışına kodlayın (kalite ve ağ koşullarından bu işlem yapılır) ve 
* akışınızı farklı protokollere dinamik olarak yeniden paketlemek için Media Services [dinamik paketleme](media-services-dynamic-packaging-overview.md) kullanın (Bu işlem, farklı cihazlarda akışı üstlenir). Media Services, aşağıdaki Uyarlamalı bit hızı akış teknolojilerinin teslimini destekler: HTTP Canlı Akışı (HLS), Kesintisiz Akış ve MPEG DASH. 

### <a name="manifest-files"></a>Bildirim dosyaları
Bir varlığı Uyarlamalı bit hızı akışı için kodlarken bir **bildirim** (çalma listesi) dosyası oluşturulur (dosya, metin tabanlı veya XML tabanlıdır). **Bildirim** dosyası, örneğin: izleme türü (ses, video veya metin), parça adı, başlangıç ve bitiş zamanı, bit hızı (kaliteleri), izleme dilleri, sunu penceresi (Sabit sürenin kayan penceresi), video codec (FourCC) gibi akış meta verilerini içerir. Ayrıca, Player 'ın sonraki parçayı almasına, kullanılabilir bir sonraki yürütülebilir video parçaları ve bunların konumları hakkında bilgi sağlayıp sağlamamasını söyler. Parçalar (veya segmentler), video içeriğinin gerçek "öbekleridir".

Bildirim dosyasına bir örnek aşağıda verilmiştir: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Dinamik bildirimler
İstemcinizin varsayılan varlığın bildirim dosyasında açıklanmasının daha fazla esneklik ihtiyacı olduğunda [senaryolar](media-services-dynamic-manifest-overview.md#scenarios) vardır. Örneğin:

* Cihaza özgü: içeriği kayıttan yürütmek için kullanılan cihaz tarafından desteklenen yalnızca belirtilen çevirileri ve/veya belirtilen dil izlerini sunun ("yorumlama filtrelemesi"). 
* Canlı bir etkinliğin alt klibini ("alt klip filtrelemesi") göstermek için bildirimi küçültün.
* Videonun başlangıcını kırpın ("video kırpma").
* Player 'da ("sunum penceresini ayarlama"), DVR penceresinde sınırlı bir uzunluk sağlamak için sunum penceresini (DVR) ayarlayın.

Bu esnekliği sağlamak için Media Services, önceden tanımlanmış [filtreleri](media-services-dynamic-manifest-overview.md#filters)temel alarak **dinamik bildirimler** sunmaktadır.  Filtreleri tanımladıktan sonra istemcileriniz, videonuzun belirli bir işlemesini veya alt kliplerini akışa almak için bunları kullanabilir. Akış URL 'sinde filtre (ler) belirler. Filtreler, [dinamik paketleme](media-services-dynamic-packaging-overview.md)tarafından desteklenen Uyarlamalı bit hızı akış protokollerine uygulanabilir: HLS, MPEG-DASH ve Kesintisiz Akış. Örneğin:

Filtre içeren MPEG DASH URL 'SI

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

Filtre içeren Kesintisiz Akış URL 'SI

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


İçeriğinizi teslim etme ve akış URL 'Leri oluşturma hakkında daha fazla bilgi için bkz. [Içerik teslimi genel bakış](media-services-deliver-content-overview.md).

> [!NOTE]
> Dinamik bildirimlerin varlığı ve bu varlık için varsayılan bildirimi değiştirmediğini unutmayın. İstemciniz, filtreleri olan veya olmayan bir akış istemeyi tercih edebilir. 
> 
> 

### <a id="filters"></a>Yorsa
İki tür varlık filtresi vardır: 

* Genel filtreler (Azure Media Services hesabındaki herhangi bir varlığa uygulanabilir, hesabın kullanım ömrü vardır) ve 
* Yerel filtreler (yalnızca, oluşturulduktan sonra filtrenin ilişkilendirildiği bir varlığa uygulanabilir, varlık kullanım ömrü vardır). 

Genel ve yerel filtre türleri tam olarak aynı özelliklere sahiptir. İkisi arasındaki temel fark, ne tür bir filme türü daha uygun olan senaryolara yöneliktir. Genel filtreler genellikle, yerel filtrelerin belirli bir varlığı kırpmak için kullanılabileceği cihaz profilleri (işleme filtrelemesi) için uygundur.

## <a id="scenarios"></a>Yaygın senaryolar
Daha önce belirtildiği gibi, İçeriğinizi müşterilere sunarken (canlı etkinlik veya isteğe bağlı video akışı), amacınız, farklı ağ koşulları altında çeşitli cihazlara yüksek kaliteli bir video sunmaktır. Ayrıca, varlıklarınızı filtrelemeyi ve **dinamik bildirimlerin**kullanımını içeren başka gereksinimleriniz de olabilir. Aşağıdaki bölümler, farklı filtreleme senaryolarına kısa bir genel bakış sunar.

* Belirli cihazların işleyebileceği ses ve video yorumlamaları (varlıkla ilişkili tüm yorumlamalar yerine) yalnızca bir alt kümesini belirtin. 
* Videonun yalnızca bir bölümünü kayıttan oynatma (videonun tamamını çalmak yerine).
* DVR sunusu penceresini ayarlayın.

## <a name="rendition-filtering"></a>İşleme filtrelemesi
Varlığınızı birden çok Encoding profiline (H. ıfer taban çizgisi, H. Ida yüksek, AACL, AACH, Dolby Digital Plus) ve birden çok kalitede bit fiyatları olarak kodlamayı seçebilirsiniz. Bununla birlikte, tüm istemci cihazları tüm varlıklarınızın profillerini ve bitoranını destekleyecektir. Örneğin, eski Android cihazlar yalnızca H. bir taban çizgisi + AACL 'yi destekler. Daha yüksek bitfiyatlar, avantajları, boşa harcar bant genişliği ve cihaz hesaplamasını elde eden bir cihaza gönderiliyor. Bu cihaz, yalnızca görüntülenmek üzere ölçeklemek için verilen tüm bilgilerin kodunu çözmelidir.

Dinamik bildirim ile mobil, konsol, HD/SD vb. gibi cihaz profilleri oluşturabilir ve her bir profilin parçası olmasını istediğiniz parçaları ve nitelikleri dahil edebilirsiniz.

![Yorumlama filtreleme örneği][renditions2]

Aşağıdaki örnekte, bir Mezzanine örneğini yedi ISO MP4 'leri video yorumlamasına (180 p-1080p) kodlamak için bir kodlayıcı kullanılmıştır. Kodlanmış varlık, aşağıdaki akış protokollerinden herhangi birine dinamik olarak paketlenebilir: HLS, sorunsuz ve MPEG DASH.  Diyagramın en üstünde, filtre olmadan varlık için HLS bildirimi gösterilir (tüm yedi yorumlamaları içerir).  Sol alt tarafta, "Ott" adlı bir filtrenin uygulandığı HLS bildirimi gösterilmiştir. "Ott" filtresi, en düşük iki kalite düzeyinin yanıtta kaldırılmasına neden olan 1 MB/sn altındaki tüm bit hızlarının kaldırılacağını belirtir. Sağ alt köşede, "mobil" adlı bir filtrenin uygulandığı HLS bildirimi gösterilir. "Mobil" filtresi, çözümlemenin 720 ' den büyük olduğu ve iki 1080p çevirinin kaldırılmasına neden olan çevirileri kaldırmayı belirtir.

![İşleme filtrelemesi][renditions1]

## <a name="removing-language-tracks"></a>Dil izlerini kaldırma
Varlıklarınız Ingilizce, Ispanyolca, Fransızca vb. gibi birden çok ses dili içerebilir. Genellikle, Player SDK yöneticileri varsayılan ses izleme seçimi ve Kullanıcı seçimi başına kullanılabilir ses izlemelerini izler. Bu tür oynatıcı SDK 'Ları geliştirmek zordur, bu, cihaza özgü oynatıcı çerçeveler arasında farklı uygulamalar gerektirir. Ayrıca, bazı platformlarda oynatıcı API 'Leri sınırlıdır ve kullanıcıların varsayılan ses parçasını seçip değiştiremediği ses seçimi özelliğini içermez. Varlık filtreleri sayesinde, yalnızca istenen ses dillerini içeren filtreler oluşturarak davranışı kontrol edebilirsiniz.

![Diller filtrelemeyi izler][language_filter]

## <a name="trimming-start-of-an-asset"></a>Bir varlığın başlangıcını kırpma
Çoğu canlı akış olayında, işleçler gerçek olaydan önce bazı testler çalıştırır. Örneğin, bu, olayın başlangıcından önce aşağıdakine benzer bir kurşun ekler içerebilir: "Program her anda başlayacak". Program arşivlenmek gerekirse, test ve kurşun veriler de arşivlenir ve sunuya dahil edilir. Ancak, bu bilgiler istemcilere gösterilmemelidir. Dinamik bildirimle, bir başlangıç zamanı filtresi oluşturabilir ve istenmeyen verileri bildirimden kaldırabilirsiniz.

![Kırpma başlangıcı][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Canlı arşivden alt Klipler (görünümler) oluşturma
Birçok canlı olay uzun süredir çalışıyor ve Canlı Arşiv birden çok olay içerebilir. Canlı Etkinlik bittikten sonra, yayımcılar canlı Arşivi mantıksal program başlatma ve durdurma dizilerini bölmek isteyebilir. Daha sonra, bu sanal programları canlı Arşivi oluşturmadan bağımsız olarak yayımlayın ve ayrı varlıklar oluşturun (CDNs 'de var olan önbelleğe alınmış parçaların avantajlarından yararlanmaz). Bu tür sanal programlara örnek olarak bir futbol veya basketbol oyunun çeyreği, beyde innings veya herhangi bir spor programının tek olayları verilebilir.

Dinamik bildirimle, başlangıç/bitiş zamanlarını kullanarak filtreler oluşturabilir ve canlı arşivlerinizin en üstünde sanal görünümler oluşturabilirsiniz. 

![Alt klip filtresi][subclip_filter]

Filtrelenmiş varlık:

![Kayak][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Sunum penceresini ayarlama (DVR)
Şu anda Azure Media Services, sürenin 5 dakika-25 saat arasında yapılandırılabileceği dairesel arşiv sunmaktadır. Bildirim filtrelemesi, arşiv üst kısmında medya silinmeden bir sıralı DVR penceresi oluşturmak için kullanılabilir. Yayımcılar 'ın canlı kenarlarla birlikte hareket etmek için sınırlı bir DVR penceresi sağlamak istediği birçok senaryo vardır ve aynı zamanda daha büyük bir arşivleme penceresi saklayın. Bir yayıncı, klipleri vurgulamak için DVR penceresindeki verileri kullanmak isteyebilir veya farklı cihazlar için farklı DVR pencereleri sağlamak isteyebilir. Örneğin, mobil cihazların çoğu büyük DVR pencerelerini işlemez (mobil cihazlar için 2 dakikalık bir DVR penceresine ve Masaüstü istemcileri için bir saate sahip olabilirsiniz).

![DVR penceresi][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff ayarlanıyor (canlı konum)
Bildirim filtrelemesi, canlı bir programın canlı kenarından birkaç saniye kaldırmak için kullanılabilir. Filtreleme, yayımcılar 'in yayını önizleme yayın noktasında izlemesini ve izleyicilerin akışı almadan önce tanıtım ekleme noktaları oluşturmasını sağlar (30 saniye boyunca desteklenir). Yayımcılar daha sonra bu reklamları kendi istemci çerçevelerine alabilir ve tanıtım fırsatından önce bilgileri işleyebilir.

Tanıtım desteğine ek olarak, canlı geri alma ayarı, istemcilerin bir HTTP 404 veya 412 hatası yerine sunucudan parçalar almaya devam edebilmesi için görüntüleyiciler konumunu ayarlamak için kullanılabilir.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Birden çok kuralı tek bir filtrede birleştirme
Birden çok Filtreleme kuralını tek bir filtrede birleştirebilirsiniz. Örnek olarak, canlı bir arşivden SLA 'ları kaldırmak için bir "Aralık kuralı" tanımlayabilir ve ayrıca kullanılabilir bitfiyatları da filtreleyebilirsiniz. Birden çok filtreleme kuralı uygulanırken, nihai sonuç tüm kuralların kesişmesi olur.

![Çoklu kurallar][multiple-rules]

## <a name="create-filters-programmatically"></a>Program aracılığıyla filtre oluşturma
Aşağıdaki makalede, filtrelerle ilgili Media Services varlıklar açıklanmaktadır. Makalede ayrıca program aracılığıyla filtrelerin nasıl oluşturulacağı gösterilmektedir.  

[REST API 'leri ile filtre oluşturun](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Birden çok filtre birleştirme (filtre bileşimi)
Tek bir URL 'de birden çok filtre de birleştirebilirsiniz. 

Aşağıdaki senaryoda filtreleri nasıl birleştirmek isteyebileceğiniz gösterilmektedir:

1. Android veya iPAD gibi mobil cihazlar için video kalitelerinizi filtrelemeniz gerekir (video kalitelerini sınırlamak için). İstenmeyen kaliteleri kaldırmak için, cihaz profilleri için uygun bir genel filtre oluşturacaksınız. Bu makalede daha önce bahsedildiği gibi, genel filtreler, aynı Media Services hesabındaki tüm varlıklarınızda başka bir ilişkilendirme olmadan kullanılabilir. 
2. Ayrıca, bir varlığın başlangıç ve bitiş saatini de kırpmak istiyorsunuz. Bunu başarmak için yerel bir filtre oluşturur ve başlangıç/bitiş saatini ayarlarsınız. 
3. Bu filtrelerin her ikisini de birleştirmek istiyorsunuz (hiçbir birleşim olmadan, filtre kullanımını daha zor hale getiren kırpma filtresine kalite filtrelemesi eklemeniz gerekir).

Filtreleri birleştirmek için, filtre adlarını noktalı virgülle ayrılmış şekilde bildirim/çalma listesi URL 'sine ayarlamanız gerekir. *MyMobileDevice* adlı bir süzgecizin olduğunu ve belirli bir başlangıç saati ayarlamak Için *mystarttime* adlı başka bir filtreniz olduğunu varsayalım. Bunları şöyle birleştirebilirsiniz:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

En fazla üç filtre birleştirebilirsiniz. 

Daha fazla bilgi için [Bu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) bloga bakın.

## <a name="know-issues-and-limitations"></a>Sorunları ve sınırlamaları öğrenin
* Dinamik bildirim GOP sınırları (anahtar çerçeveler) içinde çalışır, bu nedenle kırpma, GOP doğruluğunu içerir. 
* Yerel ve genel filtreler için aynı filtre adı ' nı kullanabilirsiniz. Yerel filtrelerin önceliği daha yüksektir ve genel filtreleri geçersiz kılar.
* Bir filtreyi güncelleştirirseniz akış uç noktasının kuralların yenilenmesi 2 dakika kadar sürebilir. İçerik bazı filtreler kullanılarak (ve proxy 'lerde ve CDN önbelleklerinde önbelleğe alınmışsa) sunulduysa, bu filtrelerin güncelleştirilmesi oynatıcı hatalarıyla sonuçlanabilir. Filtre güncelleştirildikten sonra önbelleğin temizlemeniz önerilir. Bu seçenek mümkün değilse, farklı bir filtre kullanmayı düşünün.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca Bkz.
[Müşterilere Içerik sunma genel bakış](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
