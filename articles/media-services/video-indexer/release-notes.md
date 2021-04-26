---
title: Azure Media Services Video Indexer sürüm notları | Microsoft Docs
description: En son gelişmelerden haberdar olmak için, bu makalede Azure Media Services Video Indexer en son güncelleştirmeleri sunulmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.custom: references_regions
ms.date: 03/30/2021
ms.author: juliako
ms.openlocfilehash: b3602d421718cbd1de3509751491ec6db65b1b01
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532895"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer sürüm notları

>Bu URL 'YI kopyalayarak ve yapıştırarak bu sayfanın ne zaman yeniden ziyaret ettikleridir hakkında bildirim alın: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` RSS akışı okuyucusuna.

En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

* En son yayınlar
* Bilinen sorunlar
* Hata düzeltmeleri
* Kullanım dışı işlevsellik

## <a name="march-2021"></a>Mart 2021

### <a name="audio-analysis"></a>Ses Analizi 

Ses analizi artık farklı fiyat noktasındaki yeni ses özellikleri paketinde bulunabilir. Yeni **temel ses** Analizi ön ayarı yalnızca konuşma dökümünü, çeviri ve biçim çıkış başlıklarını ve alt yazıları ayıklamak için düşük maliyetli bir seçenek sağlar. **Temel ses** ön ayarı faturanızda bir satır ve başlık ve alt başlık biçimlendirmesi için ayrı bir satır dahil olmak üzere iki ayrı ölçü oluşturur. Fiyatlandırma hakkında daha fazla bilgi için [Media Services fiyatlandırma](https://azure.microsoft.com/pricing/details/media-services/) sayfasına bakın.

Yeni eklenen paket,   ->  **temel ses** Önayarı ( **video + ses dizin oluşturma** açılan kutusu altında) seçeneğini belirleyerek dosyanızı dizin oluşturma veya yeniden dizin oluşturma sırasında kullanılabilir.

### <a name="new-developer-portal"></a>Yeni geliştirici portalı 

Yeni bir [Geliştirici portalına](https://api-portal.videoindexer.ai/)sahip video Indexer, yeni video Indexer API 'leri deneyin ve tüm ilgili kaynakları tek bir yerde bulun: [GitHub deposu](https://github.com/Azure-Samples/media-services-video-indexer), [yığın taşması](https://stackoverflow.com/questions/tagged/video-indexer) [video Indexer](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer) , Ilgili blog gönderileri, [video Indexer SSS](faq.md), [Kullanıcı seslerine](https://feedback.azure.com/forums/932041-cognitive-services?category_id=399016) geri bildirimde bulunmak ve Özellikler önermek ve pencere öğesi kod örnekleri ile  [' codepen ' bağlantısı](https://codepen.io/videoindexer) . 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>Öngörüler pencere öğesi için Gelişmiş özelleştirme özellikleri 

SDK artık Video Indexer öngörüleri pencere öğesini kendi hizmetinize eklemek ve stilini ve verilerini özelleştirmek için kullanılabilir. SDK Standart Video Indexer Öngörüler pencere öğesini ve tamamen özelleştirilebilir Öngörüler pencere öğesini destekler. Kod örneği [video Indexer GitHub deposunda](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization)kullanılabilir. Bu gelişmiş özelleştirme özellikleri sayesinde, çözüm geliştiricisi özel stil uygulayabilir ve müşterinin kendi AI verilerini getirebilir ve bunu, Öngörüler pencere öğesinde (Video Indexer içgörüler içeren veya olmayan) sunar. 

### <a name="video-indexer-deployed-in-the-us-north-central--us-west-and-canada-central"></a>ABD Orta Kuzey, ABD Batı ve Kanada Orta dağıtılan Video Indexer 

Artık ABD Orta Kuzey, ABD Batı ve Kanada Orta bölgelerinde Video Indexer ücretli bir hesap oluşturabilirsiniz
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Konuşmayı metne dönüştürme (STT), çeviri ve arama için yeni kaynak dilleri desteği 

Video Indexer artık, Danca (' da-DK '), Norveççe (' NB-NO '), Isveççe (' SV-tr '), Fince (' FI-ı '), Kanada Fransızcası (' fr-CA '), Tay dili (' th-TH ') içinde STT, çeviri ve arama desteği Arapça (' ar-BH ', ' ar-EG ', ' ar-IQ ', ' ar-JO ', ' ar-KW ', ' ar-LB ', ' ar-OM ', ' ar-QA ', ' ar-S ' ve ' ar-SY ') ve Türkçe (' tr-TR '). Bu diller hem API 'de hem de Video Indexer Web sitesinde kullanılabilir. 
 
### <a name="search-by-topic-in-video-indexer-website"></a>Video Indexer Web sitesinde konuya göre ara 

Artık, belirli konularda video aramak için [video Indexer Web sitesi](https://www.videoindexer.ai/account/login) sayfasının en üstündeki arama özelliğini kullanabilirsiniz. 

## <a name="february-2021"></a>Şubat 2021

### <a name="multiple-account-owners"></a>Birden çok hesap sahibi 

Hesap sahibi rolü Video Indexer eklendi. Kullanıcı ekleyebilir, değiştirebilir ve kaldırabilirsiniz; rolünü değiştirin. Hesap paylaşma hakkında daha fazla bilgi için bkz. [kullanıcıları davet etme](invite-users.md).

### <a name="audio-event-detection-public-preview"></a>Ses olayı algılama (Genel Önizleme)

> [!NOTE]
> Bu özellik yalnızca deneme hesaplarında kullanılabilir. 

Video Indexer artık içeriğin konuşma olmayan kesimlerinde şu ses efektlerini algılar: GUNSHOT, cam shaur, alarm, SIREN, patlama, köpek Bark, screater, defghter, kitle yeniden Actions (peylü, clatıcı ve King) ve sessizlik. 

Yeni eklenen ses etkileri özelliği, gelişmiş bir   ->  **Ses** Önayarı (video + ses dizin oluşturma altında) seçeneği belirlenerek dosyanızı dizinlerken kullanılabilir. Standart dizin oluşturma, yalnızca **sessizlik** ve **kitle yeniden eylemini** içerir. 

Önceki ses efektleri modelinde yer alan **hareketli** olay türü, artık **kitle tepki** olay türünün bir parçasını ayıklamıştır.

[Video Indexer](https://www.videoindexer.ai/) Web sitesinde videonuzun **öngörülerini** görmeyi seçtiğinizde, sayfada ses efektleri gösterilir.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Ses olayı algılama":::

### <a name="named-entities-enhancement"></a>Adlandırılmış varlık geliştirmesi  

Ayıklanan kişi ve konum listesi, genel olarak genişletilmiş ve güncelleştirildi. 

Ayrıca, model artık, videoda bir ' Sam ' veya ' Home ' gibi çok büyük olmayan kişiler ve konumlar da içerir. 

## <a name="january-2021"></a>Ocak 2021

### <a name="video-indexer-is-deployed-on-us-government-cloud"></a>Video Indexer ABD kamu bulutunda dağıtılır 

Artık Virginia ve Arizona bölgelerinde ABD kamu bulutunda ücretli Video Indexer bir hesap oluşturabilirsiniz. Video Indexer ücretsiz deneme teklifi, belirtilen bölgede kullanılamıyor. Daha fazla bilgi için Video Indexer belgelerine gidin. 

### <a name="video-indexer-deployed-in-the-india-central-region"></a>Hindistan Orta bölgesinde dağıtılan Video Indexer 

Artık Hindistan Orta bölgesinde Video Indexer ücretli bir hesap oluşturabilirsiniz. 

### <a name="new-dark-mode-for-the-video-indexer-website-experience"></a>Video Indexer Web sitesi deneyimi için yeni koyu mod

Video Indexer Web sitesi deneyimleri artık koyu modda kullanılabilir. Koyu modu etkinleştirmek için ayarlar panelini açın ve **koyu mod** seçeneğinde geçiş yapın. 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Koyu mod ayarı":::

## <a name="december-2020"></a>Aralık 2020

### <a name="video-indexer-deployed-in-the-switzerland-west-and-switzerland-north"></a>İsviçre Batı ve İsviçre Kuzey Video Indexer dağıtılır

Artık İsviçre Batı ve İsviçre Kuzey bölgelerde Video Indexer ücretli bir hesap oluşturabilirsiniz.

## <a name="october-2020"></a>Ekim 2020

### <a name="animated-character-identification-improvements"></a>Animasyonlu karakter tanımlama geliştirmeleri  

Video Indexer, bilişsel hizmetler özel Vision ile tümleştirme yoluyla animasyonlu içerikte karakterlerin algılanmasını, gruplanmasını ve tanınmasını destekler. Bu AI algoritmasına, algılama ve karakterler tanımada önemli bir geliştirme ekledik. bu nedenle, sonuç öngörüleri doğruluğu ve tanımlanan karakterler önemli ölçüde iyileştirilmiştir.

### <a name="planned-video-indexer-website-authenticatication-changes"></a>Planlı Video Indexer Web sitesi kimlik doğrulama değişiklikleri

1 Mart 2021 ' den itibaren, artık Facebook veya LinkedIn kullanarak [video Indexer Web sitesi](https://www.videoindexer.ai/) [Geliştirici Portalında](video-indexer-use-apis.md) oturum açabiliyor ve oturum açamazsınız.

Şu sağlayıcılardan birini kullanarak kaydolabilir ve oturum açabilirsiniz: Azure AD, Microsoft ve Google.

> [!NOTE]
> LinkedIn ve Facebook 'a bağlı Video Indexer hesaplara 1 Mart 2021 ' den sonra erişilemeyecektir. 
> 
> Erişiminiz olacak şekilde, sahip olduğunuz bir Azure AD, Microsoft veya Google e-postasını Video Indexer hesabına [davet](invite-users.md) etmelisiniz. Daha sonra, [davet](invite-users.md)bölümünde açıklandığı gibi desteklenen sağlayıcıların ek bir sahibini ekleyebilirsiniz. <br/>
> Alternatif olarak, ücretli bir hesap oluşturabilir ve verileri geçirebilirsiniz.

## <a name="august-2020"></a>Ağustos 2020

### <a name="mobile-design-for-the-video-indexer-website"></a>Video Indexer Web sitesi için mobil tasarım

Video Indexer Web sitesi deneyimi artık mobil cihazları destekliyor. Kullanıcı deneyimi, mobil ekran boyutunuzu uyarlayarak (özelleştirme Uıof) yanıt veriyor. 

### <a name="accessibility-improvements-and-bug-fixes"></a>Erişilebilirlik geliştirmeleri ve hata düzeltmeleri 

WCAG (Web Içeriği erişilebilirlik yönergeleri) kapsamında, Microsoft erişilebilirlik standartları kapsamında Video Indexer Web sitesi deneyimleri sınıf C ile hizalanır. Klavye gezintisi, programlı erişim ve ekran okuyucu ile ilgili birçok hata ve geliştirmeler çözüldü. 

## <a name="july-2020"></a>Temmuz 2020

### <a name="ga-for-multi-language-identification"></a>Çok dilli tanımlama için GA

Çoklu dil tanımlama, önizleme 'den GA 'ye taşınır ve üretken kullanım için hazırlayın.

"GA Önizleme" geçişi ile ilgili bir fiyatlandırma etkisi yoktur.

### <a name="video-indexer-website-improvements"></a>Video Indexer Web sitesi geliştirmeleri

#### <a name="adjustments-in-the-video-gallery"></a>Video galerisindeki ayarlamalar

Ayrıntılı Öngörüler için ek filtreleme özelliklerine sahip yeni arama çubuğu eklendi. Arama sonuçları da geliştirildi.

Birden çok dosya içeren video arşivini sıralama ve yönetme yeteneğine sahip yeni liste görünümü.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Kolay seçim ve yapılandırma için yeni panel

Kolay seçim ve Kullanıcı Yapılandırması için yan panel eklendi, basit ve hızlı hesap oluşturma ve paylaşmaya izin vererek yapılandırma ayarı da sağlar.

Yan panel, Kullanıcı tercihleri ve yardım için de kullanılır.

## <a name="june-2020"></a>Haziran 2020

### <a name="search-by-topics"></a>Konulara göre ara

Artık belirli konularda (yalnızca API) videoları aramak için arama API 'sini kullanabilirsiniz.

Konular, `textScope` (isteğe bağlı parametre) bir parçası olarak eklenir. Ayrıntılar için bkz. [API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos) .  

### <a name="labels-enhancement"></a>Etiket geliştirmesi

Etiket etiketi yükseltildi ve artık tanımlanabilecek daha fazla görsel etiket içeriyor.

## <a name="may-2020"></a>Mayıs 2020

### <a name="video-indexer-deployed-in-the-east-us"></a>Doğu ABD Video Indexer dağıtıldı

Artık Doğu ABD bölgesinde Video Indexer ücretli bir hesap oluşturabilirsiniz.
 
### <a name="video-indexer-url"></a>Video Indexer URL 'SI

Video Indexer bölgesel bitiş noktalarının hepsi yalnızca www ile başlayacak şekilde birleştirilmiştir. Eylem öğesi gerekli değildir.

Şu andan itibaren, pencere öğeleri ekleme veya Video Indexer Web uygulamalarında oturum açma için mi olduğunu www.videoindexer.ai 'ye ulaşıyorsunuz.

Ayrıca, wus.videoindexer.ai www 'e yeniden yönlendirilir. Daha fazla bilgi, [uygulamalarınızda ekleme video Indexer pencere öğeleri](video-indexer-embed-widgets.md)içinde bulunabilir.

## <a name="april-2020"></a>Nisan 2020

### <a name="new-widget-parameters-capabilities"></a>Yeni pencere öğesi parametreleri özellikleri

**Öngörüler** pencere öğesi yeni parametreler içerir: `language` ve `control` .

**Oynatıcı** pencere öğesi yeni bir `locale` parametreye sahiptir. Hem `locale` hem de `language` parametreleri Player 'ın dilini denetler.

Daha fazla bilgi için [pencere öğesi türleri](video-indexer-embed-widgets.md#widget-types) bölümüne bakın. 

### <a name="new-player-skin"></a>Yeni oyuncu kaplaması

Güncelleştirilmiş tasarımla başlatılan yeni bir oyuncu kaplaması.

### <a name="prepare-for-upcoming-changes"></a>Yaklaşan değişikliklere hazırlanma

* Günümüzde, aşağıdaki API 'Ler bir hesap nesnesi döndürüyor:

    * [Ücretli hesap oluştur](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Hesap al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Hesap al-yetkilendirme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [-Hesaplar-belirteç Ile](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    Hesap nesnesi `Url` [video Indexer Web sitesinin](https://www.videoindexer.ai/)konumunu işaret eden bir alana sahiptir.
Ücretli hesaplar için, `Url` alan şu anda genel Web sitesi yerine bir Iç URL 'yi işaret ediyor.
Önümüzdeki haftalarda bunu değiştirecek ve tüm hesapların [video Indexer Web sitesi](https://www.videoindexer.ai/) URL 'sini döndürecek (deneme ve ücretli).

    İç URL 'Leri kullanmayın, [video Indexer ortak API 'leri](https://api-portal.videoindexer.ai/)kullanmanız gerekir.
* Video Indexer URL 'Leri uygulamalarınıza katıştırdıysanız ve URL 'Ler [video Indexer Web sitesini](https://www.videoindexer.ai/) veya video Indexer API uç noktasını () işaret etmediğinden `https://api.videoindexer.ai` (örneğin, `https://wus2.videoindexer.ai` ), URL 'leri yeniden üretin.

   Bunu şu şekilde yapabilirsiniz:

    * URL 'yi Video Indexer pencere öğesi API 'Lerine işaret eden bir URL ile değiştirme (örneğin, [Öngörüler pencere öğesi](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget))
    * Yeni bir katıştırılmış URL oluşturmak için Video Indexer Web sitesini kullanma:
         
         Videonun sayfasına ulaşmak için **oynat** 'a basın-> **&lt; / &gt; ekleme** düğmesine tıklayın-> URL 'yi uygulamanıza kopyalayın:
   
    Bölgesel URL 'Ler desteklenmez ve önümüzdeki haftalarda engellenecektir.

## <a name="january-2020"></a>Ocak 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Ek diller için özel dil desteği

Video Indexer artık, ve için özel dil modellerini destekler `ar-SY` `en-UK` `en-AU` (yalnızca API).
 
### <a name="delete-account-timeframe-action-update"></a>Hesap zaman çerçevesi eylem güncelleştirmesini Sil

Hesap silme eylemi artık hesabı 48 saat yerine 90 gün içinde siler.
 
### <a name="new-video-indexer-github-repository"></a>Yeni Video Indexer GitHub deposu

Farklı projelere sahip yeni bir GitHub Video Indexer, başlangıç kılavuzlarından ve kod örneklerine artık ulaşılabilir: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger güncelleştirmesi

Tek bir [video Indexer Openapı belirtiminde (Swagger)](https://api-portal.videoindexer.ai/api-details#api=Operations&operation)birleştirilmiş **kimlik doğrulamaları** ve **işlemler** video Indexer. Geliştiriciler [video Indexer Geliştirici Portalında](https://api-portal.videoindexer.ai/)API 'leri bulabilir.

## <a name="december-2019"></a>Aralık 2019

### <a name="update-transcript-with-the-new-api"></a>Yeni API ile dökümü Güncelleştir

[Update-video-Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) API 'sini kullanarak döküm dosyasında belirli bir bölümü güncelleştirin.

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Video Indexer portalından hesap yapılandırmasını onarma

Artık, şu gibi sorunlarla ilgili kendi kendine yardım almak için Media Services bağlantı yapılandırmasını güncelleştirebilirsiniz: 

* Azure Media Services kaynak yanlış
* parola değişiklikleri
* Media Services kaynaklar abonelikler arasında taşındı  

Hesap yapılandırmasını onarmak için Video Indexer portalında ayarlar > hesap sekmesi ' ne gidin (sahip olarak).

### <a name="configure-the-custom-vision-account"></a>Özel Vision hesabını yapılandırma

Video Indexer portalını kullanarak ücretli hesaplarda özel Vision hesabını yapılandırın (daha önce bu yalnızca API tarafından desteklenmektedir). Bunu yapmak için Video Indexer portalında oturum açın, model özelleştirmesi > modelleme > ' ı seçin. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Sahneler, görünümler ve ana kareler – şimdi bir Öngörüler bölmesinde

Sahneler, görünümler ve ana kareler artık daha kolay tüketim ve gezinme için tek bir Öngörüler halinde birleştirilmiştir. İstediğiniz sahneyi seçtiğinizde, hangi görüntüleri ve bu ana kareleri içerdiğini görebilirsiniz. 

### <a name="notification-about-a-long-video-name"></a>Uzun bir video adı hakkında bildirim

Video adı 80 karakterden uzunsa, Video Indexer karşıya yükleme sırasında açıklayıcı bir hata gösterir.

### <a name="streaming-endpoint-is-disabled-notification"></a>Akış uç noktası devre dışı bildirimi

Akış uç noktası devre dışı bırakıldığında, Video Indexer oynatıcı sayfasında açıklayıcı bir hata gösterir.

### <a name="error-handling-improvement"></a>Hata işleme geliştirmesi

Durum kodu 409 şimdi, video etkin bir şekilde dizinlenerek, geçerli yeniden dizin değişikliklerinin yanlışlıkla yanlışlıkla geçersiz kılınmasını engellemek amacıyla video Dizin API 'lerini [yeniden dizinleyerek](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) video [dizini](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) API 'lerinden geri döndürülecek.

## <a name="november-2019"></a>Kasım 2019
 
* Korece özel dil modelleri desteği

    Video Indexer artık hem API hem de portalda Korece () dilinde özel dil modellerini desteklemektedir `ko-KR` . 
* Konuşmayı metne dönüştürme (STT) için desteklenen yeni diller

    Video Indexer API 'Leri artık, Arapça Levantine (ar-SY), Ingiliz Birleşik Krallık (en-GB) ve Ingilizce Avustralya diyalekti (en-AU) içinde STT 'i desteklemektedir.
    
    Video yüklemesi için, zh-, her ikisi de desteklenir, ancak zh-CN önerilir ve daha doğru bir şekilde değişir.
    
## <a name="october-2019"></a>Ekim 2019
 
* Galerideki animasyonlu karakterleri arama

    Animasyonlu karakterleri dizinlerken, artık hesabın video Gale ' i içinde arama yapabilirsiniz. Daha fazla bilgi için bkz. [animasyonlu karakter tanıma](animated-characters-recognition.md).

## <a name="september-2019"></a>Eylül 2019
 
IBC 2019 ' de birden çok terfi duyurusu:
 
* Animasyonlu karakter tanıma (Genel Önizleme)

    Özel Vision ile tümleştirme yoluyla, animasyonlu içerikte grup ad tanıma karakterlerini algılama özelliği. Daha fazla bilgi için bkz. [animasyonlu karakter algılama](animated-characters-recognition.md).
* Çoklu dil tanımlama (Genel Önizleme)

    Ses kanalında birden çok dildeki segmentleri algılayın ve bunlara göre çok dilli bir döküm oluşturun. İlk destek: Ingilizce, Ispanyolca, Almanca ve Fransızca. Daha fazla bilgi için bkz. [Çok dilli içeriği tanımlama ve transkripsiyonunu alma](multi-language-identification-transcription.md).
* Kişiler ve konum için adlandırılmış varlık ayıklama

    Marka, konum ve kişileri, doğal dil işleme (NLP) aracılığıyla konuşma ve görsel metinden ayıklar.
* Düzenleme görüntüsü türü sınıflandırması

    Yakın, orta görüntü, iki görüntü, ınkapılı, dış mek, vb. gibi düzenleme türleri ile anlık görüntü etiketleme. Daha fazla bilgi için bkz. [düzenleme görüntüsü türü algılama](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Konu düğümü Netleştirme geliştirme-artık düzey 2 ' ye sahip
    
    Inse sınırlaması modeli, artık ıPTC sınıflandırmanın daha derin ayrıntı düzeyini destekliyor. [Yeni Azure MEDIA SERVICES AI destekli yeniliklerin](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)tüm ayrıntılarını okuyun.

## <a name="august-2019"></a>Ağustos 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>UK Güney Video Indexer dağıtıldı

Artık UK Güney bölgesinde Video Indexer ücretli bir hesap oluşturabilirsiniz.

### <a name="new-editorial-shot-type-insights-available"></a>Yeni düzenleme resmi türü Öngörüler mevcuttur

Video görüntüleriyle eklenen yeni Etiketler, bunları, içerik oluşturma iş akışında kullanılan yaygın düzenleme tümcecikleriyle tanımlamak için düzenleme "görüntü türleri" sağlar: en çok closeup, wiup, geniş, orta, iki görüntü, dış mek, ınkapılı, sol yüz ve sağ yüz (JSON 'da kullanılabilir).

### <a name="new-people-and-locations-entities-extraction-available"></a>Yeni kişiler ve konumlar varlıkları ayıklama kullanılabilir

Video Indexer, videonun OCR ve dökümünü kullanarak doğal dil işleme (NLP) aracılığıyla adlandırılmış konumları ve kişileri tanımlar. Video Indexer, belirli konumların (örneğin, Eiffel kulede) veya kişilerden (örneğin John tikan) bir videoda nasıl çağrılmakta olduğunu tanımak için makine öğrenimi algoritmasını kullanır.

### <a name="keyframes-extraction-in-native-resolution"></a>Yerel çözünürlükte ana kare ayıklama

Video Indexer tarafından ayıklanan ana kareler, videonun özgün çözünürlüğünde kullanılabilir.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Görüntülerdeki özel yüz modellerini eğitmek için GA

Önizleme modundan aşağı (API ile ve portalda kullanılabilir) taşınan görüntülerden eğitim yüzeyleri.

> [!NOTE]
> "GA Önizleme" geçişi ile ilgili bir fiyatlandırma etkisi yoktur.

### <a name="hide-gallery-toggle-option"></a>Galeri değiştirme seçeneğini gizle

Kullanıcı, portalda Galeri sekmesini gizlemeyi seçebilir (örnekler sekmesini gizlemeye benzer).
 
### <a name="maximum-url-size-increased"></a>En büyük URL boyutu artırıldı

Video dizinleme sırasında 4096 (2048 yerine) URL sorgu dizesi desteği.
 
### <a name="support-for-multi-lingual-projects"></a>Çok dilli projeler için destek

Projeler artık farklı dillerde (yalnızca API) dizin oluşturulmuş videolar temel alınarak oluşturulabilir.

## <a name="july-2019"></a>Temmuz 2019

### <a name="editor-as-a-widget"></a>Pencere öğesi olarak düzenleyici

Video Indexer AI Düzenleyicisi, artık müşteri uygulamalarına katıştırılacak bir pencere öğesi olarak sunulmaktadır.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Özel dil modelini portaldan kapalı açıklamalı altyazı dosyasından Güncelleştir

Müşteriler, portalın Özelleştirme sayfasında dil modelleri için giriş olarak VTT, SRT ve TTML dosya biçimleri sağlayabilir.

## <a name="june-2019"></a>Haziran 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer Japonya Doğu dağıtıldı

Artık Japonya Doğu bölgesinde Video Indexer ücretli bir hesap oluşturabilirsiniz.

### <a name="create-and-repair-account-api-preview"></a>Hesap API 'SI oluşturma ve onarma (Önizleme)

[Azure Media Service bağlantı noktasını veya anahtarını güncelleştirmenizi](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)sağlayan yenı bir API eklendi.

### <a name="improve-error-handling-on-upload"></a>Karşıya yükleme sırasında hata işlemeyi geliştirme 

Temel alınan Azure Media Services hesabının yanlış yapılandırılması durumunda açıklayıcı bir ileti döndürülür.

### <a name="player-timeline-keyframes-preview"></a>Oyuncu zaman çizelgesi ana kare önizlemesi 

Artık Player 'ın zaman çizelgesinde her seferinde bir görüntü önizlemesi görebilirsiniz.

### <a name="editor-semi-select"></a>Düzenleyici yarı seçim

Artık düzenleyicide belirli bir öngörü zaman dilimini seçme sonucu olarak seçilen tüm öngörülerin önizlemesini görebilirsiniz.

## <a name="may-2019"></a>Mayıs 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Özel dil modelini kapalı açıklamalı altyazı dosyasından Güncelleştir

[Özel dil modeli oluşturma](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) ve [özel dil modellerini güncelleştirme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) API 'leri artık dil modellerı için girdi olarak VTT, SRT ve ttml dosya biçimlerini desteklemektedir.

[Update video döküm API 'si](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)çağrılırken, döküm otomatik olarak eklenir. Videoyla ilişkili eğitim modeli de otomatik olarak güncelleştirilir. Dil modellerinizi özelleştirme ve eğitme hakkında daha fazla bilgi için bkz. [video Indexer bir dil modelini özelleştirme](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Yeni karşıdan yükleme TRANSCRIPT biçimleri – TXT ve CSV

Zaten desteklenen (SRT, VTT ve TTML) kapalı açıklamalı alt yazı biçiminin yanı sıra, Video Indexer artık kodu TXT ve CSV biçimlerinde indirmeyi desteklemektedir.

## <a name="next-steps"></a>Sonraki adımlar

[Genel Bakış](video-indexer-overview.md)
