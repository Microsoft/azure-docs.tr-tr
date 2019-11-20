---
title: Çağrı merkezi dökümü-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşmayı metne yönelik yaygın bir senaryo, etkileşimli sesli yanıt (ıVR) gibi çeşitli sistemlerden gelen büyük miktarda telefon verilerinin dökümünü yaptırıyorsanız. Konuşma hizmetini ve birleştirilmiş konuşma modelini kullanarak bir işletme, ses yakalama sistemleriyle yüksek kaliteli bir döküm alabilir.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a2025eb611a394cf4b67c05a4019ccf03bcadf9b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075857"
---
# <a name="speech-service-for-telephony-data"></a>Telefon verileri için konuşma hizmeti

Landlines, cep telefonları ve radyolar aracılığıyla oluşturulan telefon verileri genellikle düşük kalitede ve okuma-metin dönüştürmesiyle ilgili zorluk yaratan 8 KHz aralığında daha dar bant oluşturur. Bu telefon verilerini bir insan tarafından anlaşılması zor olduğu durumlarda bile, konuşma hizmeti Excel 'den en son konuşma tanıma modelleri. Bu modeller büyük hacimlerdeki telefon verileriyle eğitilir ve gürültülü ortamlarda bile en iyi pazar tanıma doğruluğunu sağlar.

Konuşmayı metne dönüştürme için yaygın bir senaryo, etkileşimli sesli yanıt (ıVR) gibi çeşitli sistemlerden gelmiş olabilecek büyük miktarda telefon verilerinin dökümünü yaptırıyorsanız. Bu sistemlerin sağladığı ses, bir stereo veya mono olabilir ve sinyal üzerinde çok az bir POST işlemi yapılmayan ham olabilir. Konuşma hizmetini ve birleştirilmiş konuşma modelini kullanarak bir işletme, ses yakalamak için kullanılan sistemleri yüksek kaliteli çapraz bir şekilde alabilir.

Telefon verileri, müşterilerinizin ihtiyaçlarını daha iyi anlamak, yeni pazarlama fırsatlarını belirlemek veya çağrı merkezi aracılarının performansını değerlendirmek için kullanılabilir. Veriler yapıldıktan sonra, bir işletme, geliştirilmiş telemetri, anahtar tümceleri tanımlama veya müşteri yaklaşımını çözümleme gibi amaçlar için çıktıyı kullanabilir.

Bu sayfada özetlenen teknolojiler, hem gerçek zamanlı hem de toplu işlem modunda çeşitli destek çağrı işleme hizmetleri için dahili olarak Microsoft tarafından kullanılır.

Konuşma hizmeti 'nin sunduğu bazı teknolojileri ve ilgili özellikleri gözden geçirelim.

> [!IMPORTANT]
> Konuşma hizmeti Birleşik modeli, farklı verilerle eğitilmiş ve dikte etme ile telefon analizine kadar birçok senaryoya tek modelli bir çözüm sunmaktadır.

## <a name="azure-technology-for-call-centers"></a>Çağrı merkezleri için Azure teknolojisi

Konuşma hizmeti özelliklerinin işlevsel yönlerinin ötesinde, birincil amacı – çağrı merkezine uygulandığında müşteri deneyimini geliştirmaktır. Bu şekilde üç şifresiz etki alanı mevcuttur:

- Çağrıdan sonra çağrı kayıtlarının toplu işlemesi olan çağrı sonrası analizler.
- Gerçek zamanlı analizler, bu, çağrı gerçekleştiği sırada çeşitli Öngörüler ayıklamaya yönelik ses sinyalinin işlenmesiyle (önemli bir kullanım durumu olması halinde).
- Sesli yardımcılar (robotlar), müşterinin aracı katılımı olmadan müşterinin sorununu çözme veya aracı yardımcı olmak üzere yapay zeka (AI) protokollerinin uygulaması olan bir girişimle ilgili iletişim kutusunu ortaya sürüş.

Bir Batch senaryosu uygulamasının tipik bir mimari diyagramı, çağrı merkezi döküm mimarisi ![aşağıdaki resimde gösterilmiştir](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Konuşma analizi teknoloji bileşenleri

Etki alanı, çağrı sonrası veya gerçek zamanlı olup olmadığı için Azure, müşteri deneyimini geliştirmek için bir çok sayıda ve gelişen teknoloji sunar.

### <a name="speech-to-text-stt"></a>Konuşmayı metne dönüştürme (STT)

[Konuşmayı metne](speech-to-text.md) dönüştürme işlemi, herhangi bir çağrı merkezi çözümündeki en son aranan özelliktir. Aşağı akış analizi işlemlerinin birçoğu, metnin sonuna kadar önemli olduğundan, hata oranı (_wer_) en önemli öneme sahiptir. Çağrı merkezi 'ndeki önemli güçlüklerden biri, çağrı merkezi 'nde (örneğin, arka planda konuşuyor diğer aracılar) yaygın olarak kullanılan ve gerçek telefon sinyalinin en düşük kalitesindeki, çok sayıda dil yerel ayarı ve diapahı 'nin yaygın olduğu gürültü. WER, belirli bir yerel ayar için akustik ve dil modellerinin eğitilme açısından yüksek bir şekilde bağıntılı olduğundan, modeli yerel ayarınızdaki şekilde özelleştirebilme özelliği önemli olur. En son birleştirilmiş sürüm 4. x modellerimiz, hem döküm doğruluğu hem de gecikme süresi çözümüdür. En fazla binlerce saatlik akustik veri ve milyarlarca çok sayıda bilgi işlem ile eğitilen, Birleşik modeller pazardaki en doğru modellerdir ve bu da çağrı merkezi verilerini alın.

### <a name="sentiment"></a>Yaklaşım

Ölçmek, arama merkezi alanına uygulandığında konuşma analizinin en önemli alanlarından biridir. [Toplu Iş dökümü API](batch-transcription.md) 'imiz, utterance başına yaklaşım analizi sunar. Hem aracılarınız hem de müşteri için çağrının yaklaşımını tespit etmek üzere çağrı dökümünü bir parçası olarak elde edilen değerler kümesini toplayabilirsiniz.

### <a name="silence-non-talk"></a>Sessizlik (konuş olmayan)

Bir destek çağrısının yüzde 35 ' unun, konuşmasız bir süre arayabiliriz. Konuşmadan sorumlu olmayan bazı senaryolar: bir müşteriyle önceki servis talebi geçmişini arayan aracılar, müşterilerin masaüstüne erişmesine ve işlevleri gerçekleştirmesine izin veren araçları kullanan aracılar, müşteriler bir aktarım için bekleyen beklemeye bakıyor ve bu şekilde devam eder. Bu tür senaryolar etrafında ve çağrıda nerede gerçekleştikleri önemli müşteri sensitivities sayısı olduğu için sessizlik bir çağrıda gerçekleştiği zaman ölçmesi son derece önemlidir.

### <a name="translation"></a>Çeviri

Bazı şirketler, dağıtım yöneticilerinin müşterilerinin dünya çapında deneyimlerini anlayabilmesi için yabancı dil destek çağrılarından çevrilmiş yazılı betikler sağlamaya devam eder. [Çeviri](translation.md) olanaklarımız unsurpassed. Çok sayıda yerel ayar için ses-ses veya ses-metin çevirisi yapabilirsiniz.

### <a name="text-to-speech"></a>Metin Okuma

[Metinden konuşmaya](text-to-speech.md) , müşterilerle etkileşime geçen botları uygulamakta önemli bir alandır. Tipik bir yol, müşterinin konuştuğu, sesin metne göre çözümlenme, amaç için analiz olduğu, tanınan amaca göre bir yanıt elde edilmesi ve bir varlığın müşteriye veya bir sensörle bir ses yanıtı olduğu üret. Kuşkusuz bunların hepsi hızlı bir şekilde gerçekleşmelidir; bu nedenle düşük gecikme süresi, bu sistemlerin başarısı için önemli bir bileşendir.

Uçtan uca gecikme süresi, [konuşmadan metne](speech-to-text.md), [Luo](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [bot Framework](https://dev.botframework.com/), [metin okuma](text-to-speech.md)gibi çeşitli teknolojiler için önemli ölçüde düşüktür.

Yeni seslerimiz de insan seslerinden ayırt edilemez. Kendi seslerimizi kullanarak botumuzu benzersiz kişiliğine aktarabilirsiniz.

### <a name="search"></a>Search

Analize ait başka bir zımba, belirli bir olay veya deneyimin gerçekleştiği etkileşimleri belirlemektir. Bu genellikle iki yaklaşımdan biriyle yapılır; kullanıcının bir tümceciği ve sistem yanıt verdiğini veya bir analistdeki bir senaryoyu tanımlayan mantıksal deyimler kümesi oluşturabileceğiniz daha yapılandırılmış bir sorgu ya da her çağrının bu sorgu kümesine göre dizine alınması için bir ad hoc arama. İyi bir arama örneği, ubititous uyumluluk deyimidir "Bu çağrı kalite amacıyla kaydedilecek... ". Birçok şirket, aracıların gerçekten kaydedilmeden önce bu vazgeçme belgesi müşterilere bu bildirimi sağladığından emin olmak ister. Çoğu analiz sistemi, sorgu/arama algoritmalarıyla bulunan davranışları eğilim özelliğine sahiptir ve bu eğilimleri raporlamak, son olarak bir analiz sisteminin en önemli işlevlerinden biridir. Bilişsel [Hizmetler dizini](https://azure.microsoft.com/services/cognitive-services/directory/search/) sayesinde uçtan uca çözümünüz, dizin oluşturma ve arama özellikleri ile önemli ölçüde iyileştirilen olabilir.

### <a name="key-phrase-extraction"></a>Anahtar İfade Ayıklama

Bu alan, daha zorlu analitik uygulamalardan biridir ve bunlardan biri AI ve makine öğrenimi uygulamasından yararlanırken. Bu örnekte birincil senaryo müşteri amacını çıkarmaktır. Müşteri neden çağrılıyor? Müşteri sorunu nedir? Müşterinin neden negatif bir deneyimi var? [Metin analizi](https://azure.microsoft.com/services/cognitive-services/text-analytics/) hizmetimiz, bu önemli anahtar kelimeleri veya tümceleri ayıklamanıza yönelik uçtan uca çözümünüzü hızlı bir şekilde yükseltmek için kutudan çıkan bir analiz kümesi sağlar.

Artık, toplu işleme ve konuşma tanıma için gerçek zamanlı işlem hatlarına biraz daha ayrıntılı bir göz atalım.

## <a name="batch-transcription-of-call-center-data"></a>Çağrı merkezi verilerinin toplu olarak dökümünü alma

Toplu ses dökümü için [toplu Iş dökümü API](batch-transcription.md)'sini geliştirdik. Toplu Iş dökümü API 'SI, büyük miktarlarda ses verisi zaman uyumsuz olacak şekilde geliştirilmiştir. Çözüm çağrı merkezi verileriyle ilgili olarak, çözümümüzü bu sayfalarımdayına dayalıdır:

- **Doğruluk** -dördüncü nesil Birleşik modellerle unsurpassed döküm kalitesi sunuyoruz.
- **Gecikme süresi** -toplu döküm yaparken, bu işlemleri hızlı bir şekilde yapmanız gerektiğini anladık. [Toplu Iş dökümü API 'si](batch-transcription.md) aracılığıyla başlatılan döküm işleri hemen sıraya alınır ve iş çalışmaya başladıktan sonra gerçek zamanlı dökümden daha hızlı gerçekleştirilir.
- **Güvenlik** -çağrıların hassas verileri içerebileceğini anladık. Güvenlik, en yüksek önceliklerimizden biri olduğundan emin olur. Hizmetimiz ISO, SOC, HIPAA, PCI sertifikaları aldı.

Çağrı merkezleri günlük olarak büyük hacimli ses verileri oluşturur. İşiniz telefon verilerini Azure depolama gibi merkezi bir konumda depoluyorsa, [toplu Iş dökümü API](batch-transcription.md) 'sini kullanarak zaman uyumsuz istek yapabilir ve bu istekleri alabilirsiniz.

Tipik bir çözüm bu hizmetleri kullanır:

- Konuşma hizmeti konuşmayı metne eklemek için kullanılır. Toplu Iş dökümü API 'sini kullanmak için konuşma hizmeti için standart bir abonelik (S0) gereklidir. Ücretsiz abonelikler (F0) çalışmayacak.
- [Azure depolama](https://azure.microsoft.com/services/storage/) , telefon verilerini depolamak Için ve toplu iş dökümü API 'si tarafından döndürülen döküm dosyaları için kullanılır. Bu depolama hesabı, özellikle yeni dosyaların eklendiği zaman için bildirimleri kullanmalıdır. Bu bildirimler, döküm işleminin tetiklenmesi için kullanılır.
- [Azure işlevleri](https://docs.microsoft.com/azure/azure-functions/) , her kayıt için paylaşılan erişim IMZALARı (SAS) URI 'si oluşturmak için KULLANıLıR ve http post isteğini bir döküm başlatacak şekilde tetikler. Ayrıca, Azure Işlevleri, toplu Iş dökümü API 'sini kullanarak döküm alma ve silme istekleri oluşturmak için kullanılır.
- [Web kancaları](webhooks.md) , döküm tamamlandığında bildirim almak için kullanılır.

Dahili olarak, Microsoft Müşteri çağrılarını toplu Iş modunda desteklemek için yukarıdaki teknolojileri kullanıyoruz.
Batch mimarisi ![](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Çağrı merkezi verileri için gerçek zamanlı döküm

Bazı işletmeler, konuşmaları gerçek zamanlı olarak almak için gereklidir. Gerçek zamanlı döküm, önemli kelimeleri belirlemek, görüşmeyi izlemek, erişilebilirliği geliştirmek veya yerel olmayan müşteriler ve aracılar için çeviriler sağlamak üzere, konuşma açısından ilgili içerik ve kaynakları aramak için kullanılabilir lerinin.

Gerçek zamanlı döküm gerektiren senaryolar için [konuşma SDK 'sını](speech-sdk.md)kullanmanızı öneririz. Şu anda, konuşmadan metne [20 ' den fazla dilde](language-support.md)erişilebilir ve SDK, C++ C#, Java, Python, Node. js, amaç-C ve JavaScript ' te kullanılabilir. Örnek, [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk)'daki her dilde kullanılabilir. En son haberler ve güncelleştirmeler için bkz. [sürüm notları](releasenotes.md).

Dahili olarak, aşağıdaki diyagramda gösterildiği gibi gerçek zamanlı Microsoft Müşteri çağrılarını çözümlemek için yukarıdaki teknolojileri kullanıyoruz.

![Batch mimarisi](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>IRS üzerinde bir kelime

Konuşma hizmeti, [konuşma SDK 'sı](speech-sdk.md) veya [REST API](rest-apis.md)kullanılarak herhangi bir çözüme kolayca tümleştirilebilir. Ancak, çağrı merkezi dökümü ek teknolojiler gerektirebilir. Genellikle, bir ıVR sistemi ve Azure arasında bir bağlantı gereklidir. Bu tür bileşenleri sunmuyoruz, ancak bir ıVR bağlantısının ne olduğunu bir açıklama bulabilirsiniz.

Birçok IR veya telefon hizmet ürünü (Genesys veya AudioCodes gibi), bir Azure hizmetine gelen ve giden ses geçişini etkinleştirmek için yararlanılabilir olabilecek tümleştirme özellikleri sunar. Temel olarak, özel bir Azure hizmeti telefon araması oturumlarını (çağrı başlatma veya çağrı bitişi gibi) tanımlamak için belirli bir arabirim sağlayabilir ve konuşma hizmeti ile kullanılan gelen akış sesini almak için bir WebSocket API 'SI sunar. Konuşma ve bot Framework ile bağlantılar gibi giden yanıtlar, Microsoft 'un metin okuma hizmeti ile birlikte bulunabilir ve kayıttan yürütme için ıVR 'ye döndürülür.

Diğer senaryo, oturum başlatma Protokolü (SIP) ile doğrudan tümleştirmedir. Azure hizmeti bir SIP sunucusuna bağlanır, bu nedenle, konuşma ve metinden konuşmaya aşamalar için kullanılan bir gelen akış ve giden akış elde edilir. Bir SIP sunucusuna bağlanmak için, ozeki SDK gibi ticari yazılım teklifleri veya ses çağrıları için bu tür senaryoyu destekleyecek şekilde tasarlanan [takımlar çağıran ve toplantılar API 'si](/graph/api/resources/communications-api-overview) (Şu anda beta sürümünde) bulunur.

## <a name="customize-existing-experiences"></a>Mevcut deneyimleri özelleştirin

 Konuşma hizmeti yerleşik modellerle iyi şekilde çalışacaktır. Bununla birlikte, ürününüzün veya ortamınızın deneyimini daha fazla özelleştirmek ve ayarlamak isteyebilirsiniz. Özelleştirme seçenekleri, akustik model ayarlamalarından markanızla ilgili benzersiz ses yazı tiplerine kadar değişir. Özel bir model oluşturduktan sonra, onu gerçek zamanlı veya toplu modda konuşma hizmeti özelliklerinden herhangi biriyle kullanabilirsiniz.

| Konuşma hizmeti | Model | Açıklama |
| -------------- | ----- | ----------- |
| Konuşmayı metne dönüştürme | [Akustik model](how-to-customize-acoustic-models.md) | Her biri belirli kayıt koşullarına sahip bir otomobil veya bir fabrika katında gibi belirli ortamlarda kullanılan uygulamalar, araçlar veya cihazlar için özel bir akustik model oluşturun. Bu örneklere, vurgulu konuşma, belirli arka plan gürültüler veya kayıt için belirli bir mikrofon kullanımı dahildir. |
|                | [Dil modeli](how-to-customize-language-model.md) | Sektöre özgü sözlük ve dilbilgisinde tıp terminolojisi ya da It jarggibi bir tür dökümü geliştirmek için özel bir dil modeli oluşturun. |
|                | [Söyleniş modeli](how-to-customize-pronunciation.md) | Özel bir telaffuz modeliyle, fonetik biçimini tanımlayabilir ve bir sözcük veya terim için görüntüleyebilirsiniz. Ürün adları veya kısaltmalar gibi özelleştirilmiş koşullarını işlemek için kullanışlıdır. Kullanmaya başlamak için tek yapmanız gereken basit bir `.txt` dosyası olan bir telaffuz dosyasıdır. |
| Metin okuma | [Ses tipi](how-to-customize-voice-font.md) | Özel ses yazı tipleri, markanız için tanınabilir, tek bir tür sesi oluşturmanızı sağlar. Başlamak için yalnızca az miktarda veri alır. Sağladığınız daha fazla veri, ses yazı tipi daha doğal ve insan gibi bir şekilde gönderilir. |

## <a name="sample-code"></a>Örnek kod

Örnek kod, her konuşma hizmeti özelliği için GitHub 'da kullanılabilir. Bu örnekler, bir dosya veya akıştan ses okuma, sürekli ve tek kararlı bir tanıma ve özel modellerle çalışma gibi yaygın senaryoları kapsar. SDK ve REST örneklerini görüntülemek için bu bağlantıları kullanın:

- [Konuşmadan metne ve konuşma çevirisi örnekleri (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Toplu iş dökümü örnekleri (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Metinden konuşmaya örnekleri (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Başvuru belgeleri

- [Konuşma SDK'sı](speech-sdk-reference.md)
- [Konuşma cihazları SDK 'Sı](speech-devices-sdk.md)
- [REST API: konuşmayı metne dönüştürme](rest-speech-to-text.md)
- [REST API: metinden konuşmaya](rest-text-to-speech.md)
- [REST API: toplu Iş dökümü ve özelleştirme](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Ücretsiz bir konuşma hizmeti abonelik anahtarı alın](get-started.md)
