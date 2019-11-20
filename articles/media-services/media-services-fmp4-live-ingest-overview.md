---
title: Azure Media Services parçalanmış MP4 canlı alma belirtimi | Microsoft Docs
description: Bu belirtim, Azure Media Services için parçalanmış MP4 tabanlı canlı akış alımı için protokolü ve biçimi açıklar. Bulut platformu olarak Azure kullanarak canlı olayları akışa almak ve içeriği gerçek zamanlı olarak yayınlamak için Azure Media Services kullanabilirsiniz. Bu belge, yüksek düzeyde yedekli ve sağlam canlı alma mekanizmaları oluşturmaya yönelik en iyi yöntemleri de açıklamaktadır.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 4e1d41216f99a86a1b04ada882dcae0ff34b823b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "69014751"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services parçalanmış MP4 canlı alma belirtimi 

Bu belirtim, Azure Media Services için parçalanmış MP4 tabanlı canlı akış alımı için protokolü ve biçimi açıklar. Media Services, müşterilerin bulut platformu olarak Azure kullanarak canlı olayları ve yayın içeriğini gerçek zamanlı olarak akışa almak için kullanabileceği canlı bir akış hizmeti sağlar. Bu belge, yüksek düzeyde yedekli ve sağlam canlı alma mekanizmaları oluşturmaya yönelik en iyi yöntemleri de açıklamaktadır.

## <a name="1-conformance-notation"></a>1. Uygunluk gösterimi
Bu belgede, RFC 2119 ' de açıklandığı gibi, "", "" olmamalıdır, "" gereklı değildir, "" Not "," "Not", "", "" tavsıye edilmelidir, "", "ve" OPTIONAL "anahtar sözcükleri yorumlanmalıdır.

## <a name="2-service-diagram"></a>2. Hizmet diyagramı
Aşağıdaki diyagramda Media Services içindeki canlı akış hizmetinin üst düzey mimarisi gösterilmektedir:

1. Canlı kodlayıcı, Azure Media Services SDK aracılığıyla oluşturulup sağlanan kanallara canlı akışlar gönderir.
1. İçindeki kanallar, programlar ve akış uç noktaları, alma, biçimlendirme, bulut DVR, güvenlik, ölçeklenebilirlik ve yedeklilik dahil tüm canlı akış işlevlerini idare Media Services.
1. İsteğe bağlı olarak, müşteriler akış uç noktası ve istemci uç noktaları arasında bir Azure Content Delivery Network katmanını dağıtmayı seçebilirler.
1. HTTP uyarlamalı akış protokollerini kullanarak akış uç noktasındaki istemci uç noktaları akışı. Örnek olarak, Microsoft Kesintisiz Akış, HTTP üzerinden dinamik uyarlamalı akış (DASH veya MPEG-DASH) ve Apple HTTP Canlı Akışı (HLS) bulunur.

![alma akışı][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream biçimi – ISO 14496-12 parçalanmış MP4
Bu belgede ele alınan canlı akış için Tel biçimi [ISO-14496-12] tabanlıdır. İsteğe bağlı video dosyaları ve canlı akış alımı için parçalı MP4 biçimi ve uzantılarının ayrıntılı bir açıklaması için, bkz. [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Canlı alma biçimi tanımları
Aşağıdaki listede, Azure Media Services içine canlı alma için uygulanan özel biçim tanımları açıklanmaktadır:

1. **Ftyp**, **Live Server bildirim kutusu**ve **Moov** kutuları her istekle (http post) birlikte gönderilmelidir. Bu kutular akışın başlangıcında ve kodlayıcının akış alma işlemini devam ettirmeye her zaman gönderilmesi gerekir. Daha fazla bilgi için [1] içindeki bölüm 6 bölümüne bakın.
1. [1] içindeki bölüm 3.3.2, canlı alma için **Streammanifestbox** adlı isteğe bağlı bir kutu tanımlar. Azure Yük dengeleyicinin yönlendirme mantığı nedeniyle, bu kutunun kullanılması kullanım dışıdır. Media Services, Box ' a geri geldiğinde bulunmamalıdır. Bu kutu varsa Media Services sessizce yoksayar.
1. [1] içinde 3.2.3.2 içinde tanımlanan **TrackFragmentExtendedHeaderBox** kutusu her parça IÇIN mevcut olmalıdır.
1. **TrackFragmentExtendedHeaderBox** kutusunun sürüm 2 ' nin birden fazla veri merkezinde özdeş URL 'leri olan medya kesimleri oluşturmak IÇIN kullanılması gerekir. Parça dizini alanı, Apple HLS ve dizin tabanlı MPEG-DASH gibi dizin tabanlı akış biçimlerinin çapraz Datacenter yük devretmesi için GEREKLIDIR. Platformlar arası yük devretmeyi etkinleştirmek için, parça dizininin birden çok kodlayıcıda eşitlenmesi ve arka arkaya gelen her medya parçası için Kodlayıcı yeniden başlatmaları veya hatalarda bile 1 ile artması gerekır.
1. [1] içindeki bölüm 3.3.6, kanala (EOS) akışın son akışını göstermek için canlı Alım sonunda gönderilebilecek **MovieFragmentRandomAccessBox** (**mfra**) adlı bir kutu tanımlar. Media Services alma mantığı nedeniyle, EOS kullanımı kullanım dışıdır ve canlı alma için **mfra** kutusu gönderilmemelidir. Gönderildiyse Media Services sessizce yoksayar. Alma noktasının durumunu sıfırlamak için [Kanal sıfırlamayı](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels)kullanmanızı öneririz. Ayrıca, bir sunuyu ve akışı sonlandırmak için [Program durdur](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) ' un kullanılmasını öneririz.
1. İstemci bildirimlerinin boyutunu azaltmak için MP4 parça süresi sabit olmalıdır. Sabit bir MP4 parça süresi Ayrıca, yineleme etiketlerinin kullanımıyla istemci indirme buluşsal yöntemlerini geliştirir. Süre, tamsayı olmayan kare hızları için dengede dalgalanmaya BAŞLAYABILIR.
1. MP4 parça süresi yaklaşık 2 ila 6 saniye arasında olmalıdır.
1. MP4 parça zaman damgaları ve dizinler `fragment_ absolute_ time` (TrackFragmentExtendedHeaderBox `fragment_index`ve) artan sırada gelmiş olmalıdır. Yinelenen parçaların Media Services dayanıklı olmasına karşın, parçaları medya zaman çizelgesine göre yeniden sıralamak sınırlı olabilir.

## <a name="4-protocol-format--http"></a>4. Protokol biçimi – HTTP
Media Services için ISO parçalanmış MP4 tabanlı canlı alma, parçalanmış MP4 biçiminde paketlenmiş kodlanmış medya verilerini hizmetine iletmek için standart bir uzun süreli HTTP POST isteği kullanır. Her HTTP GÖNDERISI, üst bilgi kutularından (**ftyp**, **Live Server bildirim kutusu**ve **Moov** kutularından) başlayarak, eksiksiz bir parçalanmış MP4 Bitstream ("Stream") gönderir ve bir dizi parça ile devam eder (**Moof** ve  **mdat** kutuları). HTTP POST isteğinin URL sözdizimi için [1] içindeki 9,2 bölümüne bakın. GÖNDERI URL 'sine bir örnek: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Gereksinimler
Ayrıntılı gereksinimler şunlardır:

1. Kodlayıcı, aynı alma URL 'sini kullanarak boş bir "Body" (sıfır içerik uzunluğu) ile bir HTTP POST isteği göndererek yayını başlatmalıdır. Bu, kodlayıcının canlı alma uç noktasının geçerli olup olmadığını ve gerekli herhangi bir kimlik doğrulaması ya da başka koşullar varsa hızlı bir şekilde tespit etmenize yardımcı olabilir. HTTP protokolü başına, sunucu, POST gövdesi dahil olmak üzere tüm istek için bir HTTP yanıtı geri gönderemez. Canlı bir etkinliğin uzun süre çalışan doğası göz önüne alındığında, bu adım olmadan kodlayıcı tüm verileri göndermeyi bitirene kadar herhangi bir hata algılayamayabilir.
1. Kodlayıcı (1) nedeniyle herhangi bir hata veya kimlik doğrulama sorunlarını ele ALMALıDıR. (1) bir 200 yanıtıyla başarılı olursa devam edin.
1. Kodlayıcı parçalanmış MP4 akışı ile yeni bir HTTP POST isteği BAŞLATMALıDıR. Yük, üst bilgi kutuları ve ardından parçalar tarafından başlamalıdır. **Ftyp**, **Live Server bildirim kutusu**ve **Moov** kutularının (Bu sırada), önceki istek akışın sonundan önce sonlandırıldığı için her istekle birlikte gönderilmesi gerektiğini unutmayın. 
1. Canlı etkinliğin tüm içerik uzunluğunu tahmin etmek imkansız olduğundan, kodlayıcı karşıya yükleme için öbekli aktarım kodlaması kullanmalıdır.
1. Olay üzerindeyken, son parçayı gönderdikten sonra kodlayıcı, öbekli aktarım kodlama ileti sırasını düzgün bir şekilde sonlandırmalıdır (çoğu HTTP istemci yığınları otomatik olarak işler). Kodlayıcı hizmetin Son Yanıt kodunu döndürmesini bekleyip bağlantıyı sonlandıramalıdır. 
1. Kodlayıcı Media Services ' de canlı alma `Events()` için, [1] içinde 9,2 bölümünde açıklandığı gibi bir ad kullanmamalıdır.
1. HTTP POST isteği akışın sonundan önce bir TCP hatasıyla sonlandığında veya zaman aşımına uğrarsa, kodlayıcı yeni bir bağlantı kullanarak yeni bir POST isteği VERMELIDIR ve önceki gereksinimleri izlemelidir. Ayrıca, kodlayıcı akıştaki her parça için önceki iki MP4 parçalarını yeniden göndermesi ve medya zaman çizelgesinde sürekliliği bildirmeden devam ETMELIDIR. Her bir parça için son iki MP4 parçasının yeniden kesilmesi, veri kaybı olmamasını sağlar. Diğer bir deyişle, bir akış hem bir ses hem de video izlemesi içeriyorsa ve geçerli POST isteği başarısız olursa, kodlayıcı, daha önce başarıyla gönderilen ve video için son iki parçayı ve video için son iki parçayı yeniden göndermesi ve yeniden göndermesi gerekir veri kaybı olmadığından emin olmak için daha önce başarıyla gönderilen izleme. Kodlayıcı, yeniden bağlandığında daha sonra sona erecek olan medya parçalarının "ileri" bir arabelleğini KORUMALıDıR.

## <a name="5-timescale"></a>5. Timescale
[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) , **yumuşak streamingmedia** (Section 2.2.2.1), **streamelement** (Section 2.2.2.3), **StreamFragmentElement** (Bölüm 2.2.2.6) ve **livesmil** (Bölüm 2.2.7.3.1) için zaman ölçeğinin kullanımını açıklar. Zaman ölçeği değeri yoksa, kullanılan varsayılan değer 10.000.000 ' dir (10 MHz). Kesintisiz Akış biçim belirtimi diğer zaman ölçeği değerlerinin kullanımını engelmese de, çoğu Kodlayıcı uygulaması bu varsayılan değeri (10 MHz) kullanarak Kesintisiz Akış alma verileri oluşturur. [Azure Medya dinamik paketleme](media-services-dynamic-packaging-overview.md) özelliği nedeniyle, video akışları için 90-khz zaman ölçeğini ve ses akışları Için 44,1 khz veya 48,1 kHz kullanmanızı öneririz. Farklı akışlar için farklı zaman ölçeği değerleri kullanılıyorsa, akış düzeyi zaman ölçeğinin gönderilmesi gerekır. Daha fazla bilgi için bkz. [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. "Stream" tanımı
Stream, canlı bir sunu oluşturmaya, akış yük devretmesini işlemeye ve artıklık senaryolarına yönelik olarak dinamik alma işlemindeki temel işlem birimidir. Akış, tek bir izleme veya birden çok parça içerebilen bir benzersiz, parçalanmış MP4 Bitstream olarak tanımlanır. Tam canlı bir sunum, canlı kodlayıcıların yapılandırmasına bağlı olarak bir veya daha fazla akış içerebilir. Aşağıdaki örneklerde, tam canlı bir sunum oluşturmak için akışları kullanmanın çeşitli seçenekleri gösterilmektedir.

**Örnek:** 

Müşteri, aşağıdaki ses/video bitoranını içeren canlı bir akış sunumu oluşturmak istiyor:

Video – 3000 Kbps, 1500 Kbps, 750 kbps

Ses – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Seçenek 1: Bir akıştaki tüm parçalar
Bu seçenekte, tek bir kodlayıcı tüm ses/video izlerini üretir ve bunları bir parçalanmış MP4 Bitstream olarak paketler. Parçalanmış MP4 Bitstream daha sonra tek bir HTTP POST bağlantısı aracılığıyla gönderilir. Bu örnekte, bu canlı sunu için yalnızca bir akış vardır.

![Akışlar-tek parça][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Seçenek 2: Her parça ayrı bir akışta
Bu seçenekte, kodlayıcı her bir bir parça MP4 Bitstream 'e bir izleme koyar ve ardından ayrı HTTP bağlantıları üzerinden tüm akışları nakleder. Bu, tek bir kodlayıcı veya birden çok kodlayıcıyla yapılabilir. Canlı alma, bu canlı sunumu dört akışdan oluşan şekilde görür.

![Akışlar ayrı parçalar][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Seçenek 3: En düşük bit hızı video izleme ile ses izlemeyi tek bir akışa paketleyin
Bu seçenekte, müşteri ses parçasını bir parça MP4 Bitstream 'de en düşük bit hızında video izlemesine göre paketleyip, diğer iki video parçasını ayrı akışlar olarak bırakır. 

![Akışlar-ses ve video parçaları][image4]

### <a name="summary"></a>Özet
Bu örnek için tüm olası Alım seçeneklerinin kapsamlı bir listesi değildir. Aslında, her türlü parçayı akışlara gruplama, canlı alma tarafından desteklenir. Müşteriler ve kodlayıcı satıcıları, mühendislik karmaşıklığı, kodlayıcı kapasitesi ve artıklık ve yük devretme konuları temelinde kendi uygulamalarını seçebilirler. Ancak çoğu durumda, canlı sununun tamamı için yalnızca bir ses izi vardır. Bu nedenle, ses parçasını içeren alma akışının sistem durumunu sağlamak önemlidir. Bu, genellikle ses izlemenin kendi akışına yerleştirilmesi (seçenek 2 ' de olduğu gibi) veya en düşük bit hızında video izlemesine (seçenek 3 ' te olduğu gibi) paketlemeye neden olur. Ayrıca, daha iyi artıklık ve hata toleransı için aynı ses parçasını iki farklı akışlarda (yedek ses parçalarıyla 2. seçenek) gönderir veya en düşük bit hızında video izlemelerinin en az ikisi ile ses izini paketlerler (3. seçenek, en az iki ile paketlenmiş ses ile). video akışları) Media Services canlı alma için önemle önerilir.

## <a name="7-service-failover"></a>7. Hizmet yük devretmesi
Canlı akış doğası göz önüne alındığında, hizmetin kullanılabilirliğini sağlamak için iyi yük devretme desteği önemlidir. Media Services, ağ hataları, sunucu hataları ve depolama sorunları dahil olmak üzere çeşitli hata türlerini işlemek için tasarlanmıştır. Live Encoder tarafında doğru yük devretme mantığı ile birlikte kullanıldığında, müşteriler buluttan son derece güvenilir bir canlı akış hizmeti elde edebilir.

Bu bölümde, hizmet yük devretme senaryolarını tartıştık. Bu durumda, hata hizmetin içinde bir yerde gerçekleşir ve kendi kendine bir ağ hatası olarak bildirim ister. Hizmet yük devretmesini işlemek için Kodlayıcı uygulamasına yönelik bazı öneriler aşağıda verilmiştir:

1. TCP bağlantısını kurmak için 10 saniyelik bir zaman aşımı kullanın. Bağlantıyı kurma girişimi 10 saniyeden uzun sürerse, işlemi iptal edip yeniden deneyin. 
1. HTTP istek iletisi öbeklerini göndermek için kısa bir zaman aşımı kullanın. Hedef MP4 parça süresi N saniye ise, N ila 2 N saniye arasında bir gönderme zaman aşımı kullanın; Örneğin, MP4 parça süresi 6 saniyedir, 6 ile 12 saniye arasında bir zaman aşımı süresi kullanın. Zaman aşımı oluşursa, bağlantıyı sıfırlayın, yeni bir bağlantı açın ve yeni bağlantıda akış alma işlemini devam ettirir. 
1. Başarılı ve hizmete tamamen gönderilen her bir parça için son iki parçaya sahip sıralı bir arabellek saklayın.  Akışa ait HTTP POST isteği, akışın sonundan önce sonlandırılırsa veya zaman aşımına uğrarsa, yeni bir bağlantı açın ve başka bir HTTP POST isteği başlatın, Stream üst bilgilerini yeniden gönderin, her bir parça için son iki parçayı yeniden gönderin ve d 'ye giriş yapmadan akışı sürdürür medya zaman çizelgesinde ıscontinuity. Bu, veri kaybı olasılığını azaltır.
1. Bir TCP hatası oluştuktan sonra kodlayıcının bağlantı kurmaya veya akışı sürdürmesine yönelik yeniden deneme sayısını kısıtlayamayacağını öneririz.
1. Bir TCP hatasından sonra:
  
    a. Geçerli bağlantı kapatılmalıdır ve yeni bir HTTP POST isteği için yeni bir bağlantı oluşturulması gerekır.

    b. Yeni HTTP GÖNDERI URL 'SI, ilk GÖNDERI URL 'siyle aynı OLMALıDıR.
  
    c. Yeni HTTP POST, ilk POSTADAKI akış üst bilgileriyle aynı olan akış üstbilgilerini (**ftyp**, **Live Server manifest Box**ve **Moov** kutularý) içermelidir.
  
    d. Her bir parça için gönderilen son iki parça yeniden gönderilmesi ve akış, medya zaman çizelgesinde süreksizlik olmadan sürdürülmelidir. MP4 parça zaman damgaları, HTTP POST istekleri arasında bile sürekli olarak artmalıdır.
1. Veriler MP4 parça süresiyle bir hızda gönderilmezse, kodlayıcının HTTP POST isteğini sonlandırılması gerekır.  Veri gönderebilen bir HTTP POST isteği, bir hizmet güncelleştirmesi olayında kodlayıcının kodlayıcıyla hızlı bir şekilde bağlantısını kesmesinin engellenmesine Media Services engel olabilir. Bu nedenle, seyrek parça (ad sinyali) için HTTP POST ' un kısa süreli olması gerekır ve bu, seyrek parça gönderilir bitmez sonlandırılıyor.

## <a name="8-encoder-failover"></a>8. Kodlayıcı yük devretme
Kodlayıcı yük devretmesi, uçtan uca canlı akış teslimi için giderilmesi gereken ikinci yük devretme senaryosu türüdür. Bu senaryoda, hata durumu kodlayıcı tarafında oluşur. 

![Kodlayıcı yük devretme][image5]

Aşağıdaki beklentiler, kodlayıcı yük devretmesi gerçekleştiğinde canlı alma uç noktasından geçerlidir:

1. Diyagramda gösterildiği gibi, akışa devam etmek için yeni bir kodlayıcı örneği oluşturulmalıdır (kesik çizgili, 3000k video akışı).
1. Yeni kodlayıcının, başarısız örnek olarak HTTP POST istekleri için aynı URL 'YI kullanması gerekır.
1. Yeni kodlayıcı 'nın POST isteği, başarısız örnekle aynı parçalanmış MP4 üstbilgi kutularını içermelidir.
1. Yeni kodlayıcı aynı canlı sununun diğer tüm çalışan kodlayıcılarıyla düzgün şekilde eşitlenmelidir ve bu da hizalanmış parça sınırlarıyla eşitlenmiş ses/video örnekleri oluşturur.
1. Yeni akış, önceki akışa göre anlamsal olarak eşdeğer olmalıdır ve üst bilgi ve parça düzeylerinde değiştirilebilir olmalıdır.
1. Yeni kodlayıcı veri kaybını en aza indirmeye çalışır. `fragment_absolute_time` Ve`fragment_index` medya parçaları, kodlayıcının en son durdurulma noktasından artmalıdır. `fragment_absolute_time` Ve`fragment_index` sürekli olarak artmalıdır, ancak gerekirse, süreksizlik bir şekilde tanıtılmaya izin verilir. Media Services, zaten aldığı ve işlediği parçaları yoksayar; bu nedenle, parçaları yeniden gönderme tarafında, medya zaman çizelgesinde süreksizlik tanıtıldığından hata vermek daha iyidir. 

## <a name="9-encoder-redundancy"></a>9. Kodlayıcı yedekliği
Daha yüksek kullanılabilirlik ve deneyim kalitesi gerektiren belirli kritik canlı etkinlikler için, veri kaybı olmadan sorunsuz yük devretme elde etmek üzere etkin-etkin gereksiz kodlayıcıları kullanmanızı öneririz.

![Kodlayıcı yedekliği][image6]

Bu diyagramda gösterildiği gibi, iki kodlayıcıdır grubu, her akışın iki kopyasını canlı hizmete aynı anda gönderir. Media Services, akış KIMLIĞI ve parça zaman damgasına göre yinelenen parçaları filtreleyebileceğinden bu kurulum desteklenir. Ortaya çıkan canlı akış ve arşiv, iki kaynaktan en iyi olası toplama olan tüm akışların tek bir kopyasıdır. Örneğin, kuramsal çok büyük bir durumda, tek bir kodlayıcı olduğu sürece (aynı anda aynı olmak zorunda değildir), her bir akış için belirli bir zamanda çalışan gerçek akış, veri kaybı olmadan sürekli olur. 

Bu senaryonun gereksinimleri, "kodlayıcı yük devretmesi" gibi gereksinimlerle neredeyse aynıdır ve ikinci kodlayıcıların birincil kodlayıcılarla aynı anda çalıştığı özel durumdur.

## <a name="10-service-redundancy"></a>10. Hizmet yedekliliği
Yüksek oranda yedekli küresel dağıtım için bazen bölgesel olağanüstü durumları işlemek için çapraz bölge yedeğine sahip olmanız gerekir. Müşteriler, "kodlayıcı artıklığı" topolojisine genişleterek, ikinci kodlayıcılarla bağlantılı farklı bir bölgede yedekli bir hizmet dağıtımına sahip olmak için seçim yapabilir. Müşteriler Ayrıca, istemci trafiğini sorunsuz bir şekilde yönlendirmek için iki hizmet dağıtımı önünde küresel bir Traffic Manager dağıtmak üzere bir Content Delivery Network sağlayıcısıyla birlikte çalışabilir. Kodlayıcıların gereksinimleri "kodlayıcı artıklığı" durumu ile aynıdır. Tek özel durum, ikinci kodlayıcıkümesinin farklı bir canlı alma uç noktasına işaret etmek için gereklidir. Aşağıdaki diyagramda bu kurulum gösterilmektedir:

![hizmet yedekliliği][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Özel Alım biçimleri türleri
Bu bölümde, belirli senaryoları işlemek için tasarlanan özel türdeki canlı Alım biçimleri açıklanmaktadır.

### <a name="sparse-track"></a>Seyrek parça
Zengin bir istemci deneyimiyle canlı bir akış sunumu sunarken, genellikle ana medya verileriyle zaman eşitlenmiş olayları veya bant içi sinyalleri iletmek gereklidir. Bu, dinamik canlı ad eklemesi örneğidir. Bu tür bir olay sinyali, seyrek yapısı nedeniyle normal ses/video akışlarından farklıdır. Diğer bir deyişle, sinyal verileri genellikle sürekli gerçekleşmez ve zaman aralığı tahmin edilebilir. Seyrek parça kavramı, bant içi sinyal verilerini almak ve yayınlamak için tasarlanmıştır.

Aşağıdaki adımlar, seyrek parça izlemek için önerilen bir uygulama olarak verilmiştir:

1. Ses/video parçaları olmadan yalnızca seyrek izler içeren ayrı bir parçalanmış MP4 Bitstream oluşturun.
1. **Live Server bildirim kutusunda** , [1] Içinde 6 bölümünde tanımlandığı gibi, üst izlemenin adını belirtmek Için *parenttrackname* parametresini kullanın. Daha fazla bilgi için bkz. [1] içinde Bölüm 4.2.1.2.1.2.
1. **Canlı sunucu bildirim kutusunda**, **bildirimini estoutput** **true**olarak ayarlanmalıdır.
1. Sinyal olayının seyrek doğası göz önüne alındığında, şunları öneririz:
   
    a. Canlı etkinliğin başlangıcında, kodlayıcı ilk başlık kutularını hizmete gönderir ve bu da hizmetin, istemci bildiriminde seyrek parçaya kaydolmasıyla sonuçlanır.
   
    b. Kodlayıcı, veriler gönderilmediği zaman HTTP POST isteğini sonlandıramalıdır. Veri gönderebilen uzun süre çalışan bir HTTP GÖNDERISI, bir hizmet güncelleştirmesi veya sunucu yeniden başlatması durumunda Media Services kodlayıcıdan hızlı bir şekilde bağlantı kesmeyi engelleyebilir. Bu durumlarda, yuva üzerindeki bir alma işleminde medya sunucusu geçici olarak engellenir.
   
    c. Sinyal verilerinin kullanılamadığı süre boyunca, kodlayıcı HTTP POST isteğini kapatmalıdır. POST isteği etkin olsa da, kodlayıcı veri göndermelidir.

    d. Seyrek parçalar gönderilirken, kodlayıcı varsa açık bir içerik uzunluğu üst bilgisi ayarlayabilir.

    e. Yeni bir bağlantıyla seyrek parçalar gönderirken kodlayıcı, başlık kutularından ve ardından yeni parçalar tarafından gönderilmeye başlamalıdır. Bu, yük devretme işleminin arada olduğu ve yeni seyrek bağlantının daha önce seyrek izlemeyi görmeyen yeni bir sunucuya kurulduğu durumlar içindir.

    f. İstemci için eşit veya daha büyük bir zaman damgası değeri olan karşılık gelen üst izleme parçası olduğunda, seyrek izleme parçası istemci için kullanılabilir hale gelir. Örneğin, seyrek parça bir t = 1000 zaman damgasına sahipse, istemci "video" (üst parça adının "video" olduğu varsayıldığında) parça zaman damgası 1000 veya daha yüksek olduğunda, bu, seyrek parça t = 1000 ' i indirebilir. Gerçek sinyalin, sunum zaman çizelgesinde belirlenen amaçla farklı bir konum için kullanılabileceğini unutmayın. Bu örnekte, t = 1000 ' in seyrek parçası bir XML yüküne sahip olabilir. Bu, birkaç saniye sonra bir konuma ad eklemek için olan bir XML yüküne sahiptir.

    g. Seyrek izleme parçalarının yükü, senaryoya bağlı olarak farklı biçimlerde (XML, metin veya ikili gibi) olabilir.

### <a name="redundant-audio-track"></a>Yedekli ses izi
Tipik bir HTTP uyarlamalı akış senaryosunda (örneğin, Kesintisiz Akış veya DASH), genellikle sununun tamamında yalnızca bir ses parçası vardır. İstemcinin hata koşullarında seçmesi için birden çok kalite düzeyine sahip olan video izlemelerinden farklı olarak, ses parçasını içeren akışın alımı bozulur ve ses izi tek bir hata noktası olabilir. 

Bu sorunu çözmek için Media Services, yedekli ses izlemelerinin canlı alımını destekler. Fikir, aynı ses parçasının farklı akışlarda birden çok kez gönderilecedir. Hizmet, istemci bildiriminde yalnızca ses parçasını bir kez kaydederse, birincil ses kanalında sorunlar varsa ses parçalarını almak için yedek olarak yedekli ses izlerini kullanabilir. Gereksiz ses parçalarını almak için, kodlayıcının şunları yapması gerekir:

1. Aynı ses parçasını birden çok parça MP4 bitstreams içinde oluşturun. Yedekli ses parçaları, aynı parça zaman damgalarına sahip anlamsal olarak eşdeğer OLMALıDıR ve üst bilgi ve parça düzeylerinde değiştirilebilir olmalıdır.
1. Canlı sunucu bildirimindeki "ses" girişinin ([1] içindeki bölüm 6) tüm yedekli ses parçaları için aynı olduğundan emin olun.

Gereksiz ses parçaları için aşağıdaki uygulama önerilir:

1. Her benzersiz ses parçasını bir akışta tek başına gönderin. Ayrıca, ikinci akışın yalnızca HTTP POST URL 'sindeki tanımlayıcıdan farklı olduğu bu ses izi akışlarının her biri için yedekli bir akış gönderin: {Protocol}: gt {Server Address}/{yayımlama noktası yolu}/Streams ({Identifier}).
1. İki en düşük video bitoranını göndermek için ayrı akışlar kullanın. Bu akışların her biri aynı zamanda her benzersiz ses izlemenin bir kopyasını içermelidir. Örneğin, birden çok dil desteklenmiş olduğunda, bu akışlar her dil için ses parçaları içermelidir.
1. (1) ve (2) içinde bahsedilen gereksiz akışları kodlamak ve göndermek için ayrı sunucu (Kodlayıcı) örnekleri kullanın. 

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
