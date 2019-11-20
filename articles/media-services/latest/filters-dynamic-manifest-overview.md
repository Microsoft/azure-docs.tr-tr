---
title: Filtreler ve dinamik bildirimleri Azure Media Services | Microsoft Docs
description: Bu konuda, istemci akışı için bir stream'ın belirli bölümlerine kullanabilmesi için filtreler oluşturmayı açıklar. Media Services, bu seçmeli akış elde etmek için olan dinamik bildirimler oluşturur.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: dc9f59894da071e956283591cf7206bc371650b7
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991440"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>Dinamik Paketleyiciyi kullanarak bildirimleri önceden filtreleme

Cihazlara bit hızı uyarlamalı akış içeriği sunarken, genellikle belirli cihaz yeteneklerini veya kullanılabilir ağ bant genişliğini hedeflemek için bir bildirimin birden çok sürümünü yayımlamanız gerekir. [Dinamik Paketleyici](dynamic-packaging-overview.md) , birden çok kopya oluşturma gereksinimini ortadan kaldırmak için belirli codec bileşenleri, çözünürlükler, bitoranlar ve ses izleme birleşimlerini filtreleyebilen filtreler belirtmenize olanak tanır. Yalnızca hedef cihazlarınıza (iOS, Android, SmartTV ya da tarayıcılar) ve ağ özelliklerine (yüksek bant genişliği, mobil veya düşük bant genişlikli senaryolar) yapılandırılmış belirli bir filtre kümesiyle yeni bir URL yayımlamanız gerekir. Bu durumda, istemciler, içerik akışını sorgu dizesi aracılığıyla işleyebilir (kullanılabilir [varlık filtrelerini veya hesap filtrelerini](filters-concept.md)belirterek) ve bir akışın belirli bölümlerini akışa almak için filtreleri kullanabilirsiniz.

Bazı teslim senaryoları, bir müşterinin belirli izlemelere erişegerektirmediğinden emin olmanızı gerektirir. Örneğin, belirli bir abone katmanında HD izleri içeren bir bildirim yayımlamak istemeyebilirsiniz. Ya da, belirli Uyarlamalı bit hızı (ABR) izlerini kaldırmak isteyebilirsiniz. Bu, ek İzlemelerden yararlanmayan belirli bir cihaza teslim maliyetini azaltır. Bu durumda, önceden oluşturulmuş filtrelerin listesini oluşturma sırasında [akış bulucuyu](streaming-locators-concept.md) ilişkilendirebilirsiniz. Bu durumda, istemciler içeriğin akışını, **akış Bulucu**tarafından tanımlanır.

[Akış Konumlandırıcı](filters-concept.md#associating-filters-with-streaming-locator) + istemci URL 'de belirttiği ek cihaza özgü filtreler için filtre belirterek filtrelemeyi birleştirebilirsiniz. Bu, meta veri veya olay akışları, ses dilleri veya açıklayıcı ses parçaları gibi ek parçaları kısıtlamak için yararlı olabilir. 

Bu işlem, akışlarınızda farklı filtreler belirtmektir, hedef cihazlarınızda birden çok kullanım örneği senaryosunu hedeflemek için güçlü bir **dinamik bildirim** işleme çözümü sağlar. Bu konuda, **dinamik bildirimleriyle** ilgili kavramlar açıklanmakta ve bu özelliği kullanmak isteyebileceğiniz senaryolara örnek verilmiştir.

> [!NOTE]
> Dinamik bildirimler varlığı ve bu varlık için varsayılan bildirimi değiştirmez. 
> 

##  <a name="overview-of-manifests"></a>Bildirimlere genel bakış

Azure Media Services, HLS, MPEG DASH ve Kesintisiz Akış protokollerini destekler. [Dinamik paketleme](dynamic-packaging-overview.md)kapsamında, akış istemci bildirimleri (HLS ana çalma LISTESI, Dash medya sunusu AÇıKLAMASı [MPD] ve kesintisiz akış), URL 'deki biçim seçicisine göre dinamik olarak oluşturulur. Bkz. [ortak isteğe bağlı iş akışında](dynamic-packaging-overview.md#delivery-protocols)teslim protokolleri. 

### <a name="get-and-examine-manifest-files"></a>Bildirim dosyalarını al ve incele

Akışlarınızın hangi izlemelerinizin (canlı veya video isteğe bağlı [VOD]) dinamik olarak oluşturulan bir bildirime eklenmesi gereken filtre izleme özelliği koşullarını bir liste olarak belirtirsiniz. İzlemelerin özelliklerini almak ve incelemek için öncelikle Kesintisiz Akış bildirimini yüklemeniz gerekir.

[.NET öğreticisi Ile karşıya yükleme, kodlama ve akış dosyaları](stream-files-tutorial-with-api.md#get-streaming-urls) , .net Ile akış URL 'lerinin nasıl oluşturulacağını gösterir. Uygulamayı çalıştırırsanız, URL 'Lerden biri Kesintisiz Akış bildirimine işaret eder: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> URL 'YI kopyalayıp bir tarayıcının adres çubuğuna yapıştırın. Dosya indirilecek. Bunu istediğiniz bir metin düzenleyicisinde açabilirsiniz.

REST örneği için bkz. [rest ile dosyaları karşıya yükleme, kodlama ve akış](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Video akışının bit hızını izleme

Video akışının bit hızını izlemek için [Azure Media Player Demo sayfasını](https://aka.ms/azuremediaplayer) kullanabilirsiniz. Tanıtım sayfası tanılama sekmesinde Tanılama bilgilerini görüntüler:

![Azure Media Player tanılama][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Örnekler: Sorgu dizesinde filtre içeren URL 'Ler

ABR akış protokollerine filtre uygulayabilirsiniz: HLS, MPEG-DASH ve Kesintisiz Akış. Aşağıdaki tabloda, filtre içeren URL 'lerin bazı örnekleri gösterilmektedir:

|Protocol|Örnek|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Kesintisiz Akış|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>İşleme filtrelemesi

Varlığınızı birden çok Encoding profiline (H. ıfer taban çizgisi, H. Ida yüksek, AACL, AACH, Dolby Digital Plus) ve birden çok kalitede bit fiyatları olarak kodlamayı seçebilirsiniz. Bununla birlikte, tüm istemci cihazları tüm varlıklarınızın profillerini ve bitoranını destekleyecektir. Örneğin, eski Android cihazları yalnızca H. bir taban çizgisi + AACL 'yi destekler. Bir cihaza daha yüksek bitoranlar göndererek, daha fazla bit genişliği ve cihaz hesaplama avantajları elde edemiyor. Bu tür bir cihaz, yalnızca görüntülenmek üzere ölçeklemek için verilen tüm bilgilerin kodunu çözmelidir.

Dinamik bildirim ile cihaz profilleri oluşturabilir (mobil, konsol veya HD/SD gibi) ve her bir profilin parçası olmasını istediğiniz parçaları ve nitelikleri dahil edebilirsiniz. Bu, yorumlama filtrelemesi olarak adlandırılır. Aşağıdaki diyagramda bir örneği gösterilmektedir.

![Yorumlama filtreleme örneği][renditions2]

Aşağıdaki örnekte, bir Mezzanine örneğini yedi ISO MP4 'leri video yorumlamasına (180 p-1080p) kodlamak için bir kodlayıcı kullanılmıştır. Kodlanmış varlık, aşağıdaki akış protokollerinden herhangi birine [dinamik olarak paketlenebilir](dynamic-packaging-overview.md) : HLS, MPEG DASH ve sorunsuz. 

Aşağıdaki diyagramın en üst kısmında, filtre olmadan varlık için HLS bildirimi gösterilmektedir. (Tüm yedi çevirileri içerir.)  Sol alt köşedeki diyagramda, "Ott" adlı bir filtrenin uygulandığı bir HLS bildirimi gösterilmektedir. "Ott" filtresi, tüm bit hızlarının 1 MB/sn 'tan kaldırılmasını belirtir. bu nedenle, doğru iki kalite düzeyi yanıtta çıkarılır. Sağ alt köşedeki diyagramda, "mobil" adlı bir filtrenin uygulandığı HLS bildirimi gösterilmektedir. "Mobil" filtresi, çözümlemenin 720p 'den büyük olduğu ve iki 1080p çevirinin çıkarılmasından dolayı yapılan yorumlamaları kaldırma işlemini belirtir.

![İşleme filtrelemesi][renditions1]

## <a name="removing-language-tracks"></a>Dil izlerini kaldırma
Varlıklarınız Ingilizce, Ispanyolca, Fransızca vb. gibi birden çok ses dili içerebilir. Genellikle, Player SDK yöneticileri varsayılan ses izleme seçimi ve Kullanıcı seçimi başına kullanılabilir ses izlemelerini izler.

Bu tür oynatıcı SDK 'Ları, cihaza özgü oynatıcı çerçeveleri arasında farklı uygulamalar gerektirdiğinden zorlayıcı hale gelir. Ayrıca, bazı platformlarda oynatıcı API 'Leri sınırlıdır ve kullanıcıların varsayılan ses parçasını seçmediği veya değiştiremediği ses seçimi özelliğini içermez. Varlık filtreleri sayesinde, yalnızca istenen ses dillerini içeren filtreler oluşturarak davranışı kontrol edebilirsiniz.

![Dil izlemelerinin filtrelenmesi][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Bir varlığın başlangıcını kırpma

Çoğu canlı akış olayında, işleçler gerçek olaydan önce bazı testler çalıştırır. Örneğin, bu, olayın başlangıcından önce aşağıdakine benzer bir kurşun ekler içerebilir: "Program her anda başlayacak." 

Program arşivlenmek gerekirse, test ve kurşun veriler de arşivlenir ve sunuya dahil edilir. Ancak, bu bilgiler istemcilere gösterilmemelidir. Dinamik bildirimle, bir başlangıç zamanı filtresi oluşturabilir ve istenmeyen verileri bildirimden kaldırabilirsiniz.

![Kırpma başlangıcı][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Canlı arşivden alt Klipler (görünümler) oluşturma

Birçok canlı olay uzun süredir çalışıyor ve Canlı Arşiv birden çok olay içerebilir. Canlı Etkinlik bittikten sonra, yayımcılar canlı Arşivi mantıksal program başlatma ve durdurma dizilerini bölmek isteyebilir. 

Bu sanal programları, canlı Arşivi oluşturmadan ve ayrı varlıklar oluşturmamadan (CDNs 'de var olan önbelleğe alınmış parçaların avantajını elde etmez) ayrı olarak yayımlayabilirsiniz. Bu tür sanal programlara örnek olarak bir futbol veya basketbol oyunun çeyreği, beyde innings veya herhangi bir spor programının tek olayları verilebilir.

Dinamik bildirimle, başlangıç/bitiş zamanlarını kullanarak filtreler oluşturabilir ve canlı arşivlerinizin en üstünde sanal görünümler oluşturabilirsiniz. 

![Alt klip filtresi][subclip_filter]

Filtrelenmiş varlık aşağıda verilmiştir:

![Kayak][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Sunu penceresini ayarlama (DVR)

Şu anda Azure Media Services, sürenin 1 dakika-25 saat arasında yapılandırılabileceği dairesel arşiv sunmaktadır. Bildirim filtrelemesi, arşiv üst kısmında medya silinmeden bir sıralı DVR penceresi oluşturmak için kullanılabilir. Yayımcılar 'ın canlı kenarlarla birlikte hareket etmek için sınırlı bir DVR penceresi sağlamak istediği birçok senaryo vardır ve aynı zamanda daha büyük bir arşivleme penceresi saklayın. Bir yayıncı, klipleri vurgulamak için DVR penceresindeki verileri kullanmak isteyebilir veya farklı cihazlar için farklı DVR pencereleri sağlamak isteyebilir. Örneğin, mobil cihazların çoğu büyük DVR pencerelerini işlemez (mobil cihazlar için 2 dakikalık bir DVR penceresine ve Masaüstü istemcileri için bir saate sahip olabilirsiniz).

![DVR penceresi][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff ayarlanıyor (canlı konum)

Bildirim filtrelemesi, canlı bir programın canlı kenarından birkaç saniye kaldırmak için kullanılabilir. Filtreleme, yayımcılar 'in yayını önizleme yayın noktasında izlemesini ve izleyicilerin akışı almadan önce tanıtım ekleme noktaları oluşturmasını sağlar (30 saniyeden sonra desteklenir). Yayımcılar daha sonra bu reklamları kendi istemci çerçevelerine göndererek tanıtım fırsatından önce bilgileri alıp işleyebilirler.

Tanıtım desteğine ek olarak, canlı geri ödeme ayarı, istemcilerin, istemcileri ft + canlı uca vurduklarında, hala sunucudan parçalar alabilecekleri şekilde ayarlamak için kullanılabilir. Bu şekilde, istemciler HTTP 404 veya 412 hatası almaz.

![Canlı geri dönme için filtrele][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Birden çok kuralı tek bir filtrede birleştirme

Birden çok Filtreleme kuralını tek bir filtrede birleştirebilirsiniz. Örnek olarak, canlı bir arşivden SLA 'ları kaldırmak için bir "Aralık kuralı" tanımlayabilir ve ayrıca kullanılabilir bitfiyatları da filtreleyebilirsiniz. Birden çok filtreleme kuralı uygularken, nihai sonuç tüm kuralların kesişimine göre yapılır.

![Birden çok filtreleme kuralı][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Birden çok filtre birleştirme (filtre bileşimi)

Tek bir URL 'de birden çok filtre de birleştirebilirsiniz. Aşağıdaki senaryoda filtreleri nasıl birleştirmek isteyebileceğiniz gösterilmektedir:

1. Android veya iPad gibi mobil cihazlar için video kalitelerinizi filtrelemeniz gerekir (video kalitelerini sınırlamak için). İstenmeyen kaliteleri kaldırmak için cihaz profilleri için uygun bir hesap filtresi oluşturacaksınız. Tüm varlıklarınız için hesap filtrelerini, daha fazla ilişkilendirme yapmadan aynı Media Services hesabı altında kullanabilirsiniz.
1. Ayrıca, bir varlığın başlangıç ve bitiş saatini de kırpmak istiyorsunuz. Bunu başarmak için bir varlık filtresi oluşturacak ve başlangıç/bitiş saatini ayarlayacaksınız. 
1. Bu filtrelerin her ikisini de birleştirmek istiyorsunuz. Birleşim olmadan, filtre kullanımını daha zor hale getirmek için kırpma filtresine kalite filtresi eklemeniz gerekir.


Filtreleri birleştirmek için, filtre adlarını, noktalı virgülle ayrılmış biçimde bildirim/çalma listesi URL 'sine ayarlamanız gerekir. Kalitelerini filtreleyerek *MyMobileDevice* adlı bir filtreniz olduğunu ve belirli bir başlangıç saati ayarlamak Için *mystarttime* adlı başka bir filtre olduğunu varsayalım. En fazla üç filtre birleştirebilirsiniz. 

Daha fazla bilgi için [Bu blog gönderisine](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)bakın.

## <a name="considerations-and-limitations"></a>Önemli noktalar ve sınırlamalar

- **Forceendtimestamp**, **Presentationwindowduration**ve **livebackoffduration** değerleri bir VOD filtresi için ayarlanmamalıdır. Yalnızca canlı filtre senaryolarında kullanılır. 
-  Dinamik bir bildirim GOP sınırları (anahtar çerçeveler) içinde çalışır, bu nedenle kırpılacak GOP doğruluğu vardır.
-  Hesap ve varlık filtreleri için aynı filtre adını kullanabilirsiniz. Varlık filtrelerinin önceliği daha yüksektir ve hesap filtrelerini geçersiz kılar.
- Bir filtreyi güncelleştirirseniz akış uç noktasının kuralların yenilenmesi 2 dakika kadar sürebilir. İçeriği karşılamak için filtreler kullandıysanız (ve içeriği proxy 'lerde ve CDN önbelleklerinde önbelleğe alırsanız), bu filtrelerin güncelleştirilmesi oynatıcı hatalara neden olabilir. Filtreyi güncelleştirdikten sonra Önbelleği temizlemeniz önerilir. Bu seçenek mümkün değilse, farklı bir filtre kullanmayı göz önünde bulundurun.
- Müşterilerin bildirimi el ile indirmesi ve tam başlangıç zamanı damgası ve zaman ölçeğini ayrıştırması gerekir.
    
    - Bir varlık içindeki izlemelerin özelliklerini öğrenmek için [bildirim dosyasını alın ve inceleyin](#get-and-examine-manifest-files).
    - Varlık filtresi zaman damgası özelliklerini ayarlamak için formül: <br/>StartTimestamp = &lt;beklenen bildirimde&gt; +  başlangıç zamanısaniye&gt; cinsinden filtre başlangıç zamanı * zaman ölçeği&lt;

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde, programlı olarak filtrelerin nasıl oluşturulacağı gösterilmektedir:  

- [REST API 'Leri ile filtre oluşturma](filters-dynamic-manifest-rest-howto.md)
- [.NET ile filtre oluşturma](filters-dynamic-manifest-dotnet-howto.md)
- [CLı ile filtre oluşturma](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
