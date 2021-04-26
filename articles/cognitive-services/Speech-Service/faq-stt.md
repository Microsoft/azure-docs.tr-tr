---
title: Konuşmayı metne sık sorulan sorular
titleSuffix: Azure Cognitive Services
description: Konuşmayı metin hizmeti hakkında sık sorulan soruların yanıtlarını alın.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: panosper
ms.openlocfilehash: f71fd01d45604dff843ad6eba62561937366a125
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382343"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Konuşmayı metne sık sorulan sorular

Bu SSS 'de sorularınıza yanıt bulamazsanız, [diğer destek seçeneklerine](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)göz atın.

## <a name="general"></a>Genel

**S: bir taban çizgisi modeliyle özel bir konuşma ve metin modeli arasındaki fark nedir?**

Y **: temel bir model**, Microsoft 'a ait veriler kullanılarak eğitilmiştir ve bulutta zaten dağıtılır. Bir modeli, belirli çevresel gürültü veya dile sahip belirli bir ortama daha iyi uyacak şekilde uyarlamak için özel bir model kullanabilirsiniz. Fabrika zeminleri, otomobiller veya gürültülü Streets bir uyarlanan model gerektirir. Bioloji, fizik, radiolojisi, ürün adları ve özel kısaltmalar gibi konular, bir uyarlanan dil modeli gerektirir. Özel bir model eğitemeniz durumunda, özel hüküm ve tümceciklerin tanınmasını geliştirmek için ilgili metinle başlamanız gerekir.

**S: bir temel modeli kullanmak istersem nereden başlayabilirim?**

**A**: ilk olarak bir [abonelik anahtarı](overview.md#try-the-speech-service-for-free)alın. Önceden dağıtılan taban çizgisi modellerine REST çağrıları yapmak isterseniz [REST API 'lerine](./overview.md#reference-docs)bakın. WebSockets kullanmak istiyorsanız [SDK 'yı indirin](speech-sdk.md).

**S: her zaman özel bir konuşma modeli mi kullanmam gerekir?**

Y **: Hayır**. Uygulamanız genel, günlük dil kullanıyorsa, bir modeli özelleştirmeniz gerekmez. Uygulamanız çok az veya arka plan gürültüsünü olmayan bir ortamda kullanılıyorsa, bir modeli özelleştirmeniz gerekmez.

Portalda temel ve özelleştirilmiş modeller dağıtabilir ve ardından bunlarla aynı şekilde doğruluk testleri çalıştırabilirsiniz. Bu özelliği, bir taban çizgisi modelinin doğruluğunu özel bir modele göre ölçmek için kullanabilirsiniz.

**S: veri kümesi veya modelim için işleme tamamlandığında nasıl öğrenebilirim?**

Y **: Şu** anda, tablodaki modelin veya veri kümesinin durumu bilinmenin tek yoludur. İşlem tamamlandığında durum **başarılı** olur.

**S: birden fazla model oluşturabilir miyim?**

Y: koleksiyonunuzda sahip olduğunuz model sayısında sınır yoktur.

**S: bir hata yaptık. Nasıl yaparım? veri içeri aktarma veya model oluşturma işlemini iptal ediyor mu?**

Y **: Şu anda, bir akustik** veya dil uyarlama işlemini geri alamazsınız. İçeri aktarılan verileri ve modelleri, Terminal durumundayken silebilirsiniz.

**S: ayrıntılı çıkış biçimiyle her bir tümcecik için birkaç sonuç alıyorum. Hangi birini kullanmalıyım?**

**A**: başka bir sonuç ("N-en iyi") daha yüksek bir güven değerine sahip olsa bile her zaman ilk sonucu alın. Konuşma hizmeti ilk sonucu en iyi şekilde değerlendirir. Konuşma tanınmazsa boş bir dize de olabilir.

Diğer sonuçlar büyük olasılıkla daha kötüdir ve tam büyük harfe ve noktalama uygulanmış olabilir. Bu sonuçlar, kullanıcılara bir listeden düzeltmeler çekme veya yanlış tanınan komutları işleme seçeneğini verme gibi özel senaryolarda faydalıdır.

**S: neden farklı temel modeller var?**

Y **: konuşma** hizmetinde birden fazla temel model arasından seçim yapabilirsiniz. Her model adı, eklendiği tarihi içerir. Özel bir model eğitimi başlattığınızda en iyi doğruluğu elde etmek için en son modeli kullanın. Yeni bir model kullanılabilir hale geldiğinde, daha eski temel modeller hala bir süre için kullanılabilir. Devre dışı bırakılana kadar çalıştığınız modeli kullanmaya devam edebilirsiniz (bkz. [model ve uç nokta yaşam döngüsü](./how-to-custom-speech-model-and-endpoint-lifecycle.md)). Daha iyi doğruluk için en son temel modele geçmeniz hala önerilir.

**S: var olan modelmi güncelleştirebilir (model yığınlama)?**

Y **: varolan** bir modeli güncelleştiremezsiniz. Bir çözüm olarak, eski veri kümesini yeni veri kümesiyle ve readapt ile birleştirin.

Eski veri kümesi ve yeni veri kümesi tek bir. zip dosyasında (akustik veriler için) veya bir. txt dosyasında (dil verileri için) birleştirilmelidir. Uyarlama tamamlandığında yeni, güncelleştirilmiş modelin yeni bir uç nokta almak için yeniden dağıtılması gerekir

**S: bir temel modelin yeni bir sürümü kullanılabilir olduğunda, dağıtımım otomatik olarak güncelleştirilir mi?**

**A**: dağıtımlar otomatik olarak güncellenmeyecektir.

Bir modeli uyardıysanız ve dağıttıysanız, bu dağıtım olduğu gibi kalır. Dağıtılmış modelin yetkisini alabilir ve temel modelin daha yeni sürümünü kullanarak readapt ve daha iyi doğruluk için yeniden dağıtabilirsiniz.

Temel modeller ve özel modeller bir süre sonra kullanımdan kaldırılacaktır (bkz. [model ve uç nokta yaşam döngüsü](./how-to-custom-speech-model-and-endpoint-lifecycle.md)).

**S: modelimi indirebilir ve yerel olarak çalıştırabilir miyim?**

Y **: özel bir modeli bir** [Docker kapsayıcısında](speech-container-howto.md?tabs=cstt)yerel olarak çalıştırabilirsiniz.

**S: veri kümelerimi, modellerimi ve dağıtımlarımı başka bir bölgeye veya aboneliğe kopyalayabilir veya taşıyabilir miyim?**

Y **: özel bir modeli** başka bir bölgeye veya aboneliğe kopyalamak için [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) kullanabilirsiniz. Veri kümeleri veya dağıtımlar kopyalanamıyor. Bir veri kümesini başka bir abonelikte içeri aktarabilir ve model kopyalarını kullanarak uç noktalar oluşturabilirsiniz.

**S: isteklerim günlüğe kaydediliyor mi?**

**A**: varsayılan olarak istekler günlüğe kaydedilmez (ses veya döküm değil). Gerekirse, [özel bir uç nokta oluşturduğunuzda](how-to-custom-speech-train-model.md#deploy-a-custom-model) *Bu uç nokta seçeneğinde günlük içeriği* ' ni seçebilirsiniz. Ayrıca, özel bir uç nokta oluşturmadan, [konuşma SDK 'sında](how-to-use-logging.md) istek başına temelinde ses günlüğünü de etkinleştirebilirsiniz. Her iki durumda da isteklerin ses ve tanıma sonuçları, güvenli depolama alanında depolanır. Microsoft 'a ait depolama kullanan abonelikler için, bunlar 30 gün boyunca kullanılabilir olacaktır.

*Bu uç noktanın etkin olan günlük içeriğiyle* özel bir uç nokta kullanırsanız, konuşma Studio 'daki dağıtım sayfasında günlüğe kaydedilen dosyaları dışarı aktarabilirsiniz. Ses günlüğü 'nün SDK aracılığıyla etkinleştirilmesi durumunda dosyalara erişmek için [API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModelLogs) 'yi çağırın.

**S: isteklerim kısıtlandı mı?**

Y **: bkz**. [konuşma Hizmetleri kotaları ve limitleri](speech-services-quotas-and-limits.md).

**S: çift kanallı ses için nasıl ücret ödersiniz?**

Y: her **bir** kanalı ayrı ayrı gönderirseniz (her kanal kendi dosyasında), her bir dosyanın süresi üzerinden ücretlendirilirsiniz. Her bir kanalla çoğullanmış tek bir dosya gönderirseniz, tek dosya süresince ücretlendirilirsiniz. Fiyatlandırma hakkında ayrıntılı bilgi için lütfen Azure bilişsel [Hizmetler fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)bakın.

> [!IMPORTANT]
> Özel konuşma hizmetini kullanmanızı engelleyen başka gizlilik endişeleriniz varsa, destek kanallarından birine başvurun.

## <a name="increasing-concurrency"></a>Eşzamanlılık artırma
Bkz. [konuşma Hizmetleri kotaları ve sınırları](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Verileri içeri aktarma

**S: bir veri kümesinin boyutuyla ilgili sınır nedir ve neden sınırla?**

Y **: sınır**, http karşıya yüklemesi için bir dosyanın boyutu kısıtlamasından kaynaklanır. Gerçek sınır için [konuşma Hizmetleri kotaları ve limitlerini](speech-services-quotas-and-limits.md) inceleyin. Verilerinizi birden çok veri kümesine bölebilir ve tüm bunları seçerek modeli eğitebilirsiniz.

**S: daha büyük bir metin dosyasını karşıya yükleyebilmem için metin dosyalarımı ZIP yapabilir miyim?**

Y **: Hayır**. Şu anda yalnızca sıkıştırılmamış metin dosyalarına izin verilir.

**S: veri raporu, başarısız olan bildirimler olduğunu söylüyor. Sorun nedir?**

**A**: bir dosyadaki çıkışların yüzde 100 ' unun karşıya yüklenememesi bir sorun değildir. Bir akustik veya dil veri kümesindeki (örneğin, yüzde 95 ' den fazla) en büyük çoğunluğunun başarıyla içeri aktarılmışsa, veri kümesi kullanılabilir olabilir. Ancak, neden başarısız olduğunu anlamaya ve sorunları nasıl giderebileceğinizi öneririz. Biçimlendirme hataları gibi yaygın sorunların çoğu düzeltilme kolaydır.

## <a name="creating-an-acoustic-model"></a>Akustik model oluşturma

**S: ne kadar akustik verilere ihtiyacım var?**

Y **: 30** dakika ile bir saat ve akustik veri arasında başlangıç yapmanız önerilir.

**S: hangi verileri toplamalıyım?**

Y **: uygulama** senaryosuna yakın olan verileri toplayın ve mümkün olduğunca büyük küçük harf kullanın. Veri toplama, hedef uygulamayla ve kullanıcılarla cihaz ya da cihaz, ortam ve tür hoparlörleriyle eşleşmelidir. Genel olarak, verileri mümkün olduğunca geniş bir hoparlörden toplayabilirsiniz.

**S: akustik verileri nasıl toplamalıyım?**

Y **: tek başına bir veri** toplama uygulaması oluşturabilir veya raf dışı ses kaydı yazılımını kullanabilirsiniz. Ayrıca, uygulamanızın ses verilerini günlüğe kaydettiği ve ardından verileri kullandığı bir sürümünü de oluşturabilirsiniz.

**S: verileri kendi kendinize uyarlama konusunda bir işlem yapmam gerekir mi?**

Y **: Evet**. Kendiniz düzenleyebilir veya profesyonel bir döküm hizmeti kullanabilirsiniz. Bazı kullanıcılar profesyonel onayları tercih eder ve diğerleri bu kişilerin kendi kendilerini kullanmasını ister.

**S: ses verileriyle özel bir modeli eğmek için ne kadar sürer?**

Y: bir modeli ses verileriyle eğitmek **uzun bir işlem** olabilir. Veri miktarına bağlı olarak, özel bir model oluşturmak birkaç gün sürebilir. Bir hafta içinde bitirilemez servis, eğitim işlemini durdurabilir ve modeli başarısız olarak rapor edebilir.

Eğitim için adanmış donanımın kullanılabildiği [bölgelerden](custom-speech-overview.md#set-up-your-azure-account) birini kullanın. Konuşma hizmeti, bu bölgelerde eğitim için en fazla 20 saatlik ses kullanacaktır. Diğer bölgelerde, yalnızca 8 saate kadar sürer.

Genel olarak, hizmet adanmış donanıma sahip bölgelerde günde yaklaşık 10 saat ses verisi işler. Bu, diğer bölgelerde günde yalnızca 1 saat ses verisi işleyebilir. [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)kullanarak, tamamen eğitilen modeli başka bir bölgeye kopyalayabilirsiniz. Yalnızca metin ile eğitim çok daha hızlıdır ve genellikle dakikalar içinde tamamlanır.

Bazı temel modeller ses verileriyle özelleştirilemez. Bu hizmet için, bakım için yalnızca eğitim ve ses verilerini yok Sayın metnini kullanır. Daha sonra eğitim daha hızlı tamamlanır ve sonuçlar yalnızca metin ile eğitimle aynı olacaktır. Ses verileriyle eğitimi destekleyen temel modellerin listesi için bkz. [dil desteği](language-support.md#speech-to-text) .

## <a name="accuracy-testing"></a>Doğruluk testi

**S: sözcük hata oranı nedir (WER) ve nasıl hesaplanır?**

Y **: wer**, konuşma tanıma için değerlendirme ölçümdür. WER, ekleme, silme ve değiştirmeler içeren toplam hata sayısı olarak sayılır, bu da başvuru dökümünde toplam sözcük sayısına bölünür. Daha fazla bilgi için bkz. [doğruluğu değerlendirme özel konuşma tanıma](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy).

**S: doğruluk testinin sonuçlarının iyi olup olmadığını Nasıl yaparım?.**

Y **: sonuçlar**, taban çizgisi modeliyle özelleştirdiğiniz model arasında bir karşılaştırma gösterir. Özelleştirmeyi bir süre için temel modeli oluşturmak üzere hedefleyebilirsiniz.

**S: bir geliştirme olup olmadığını görmek için bir temel modelin WER özelliğini Nasıl yaparım?.**

Y **: çevrimdışı** test sonuçları özel modelin taban çizgisi doğruluğunu ve taban çizgisinin iyileştirmesini gösterir.

## <a name="creating-a-language-model"></a>Dil modeli oluşturma

**S: ne kadar metin verisi karşıya yüklenemem gerekir?**

Y **: uygulamanızda** kullanılan sözlük ve tümceciklerin başlangıç dili modellerinden farklı olmasına bağlıdır. Tüm yeni kelimeler, bu sözcüklerin kullanımının mümkün olduğu sayıda örnek sağlamak yararlı olur. Uygulamanızda kullanılan yaygın ifadeler için, dil verilerinde ifadeler de dahil olmak üzere, sisteme bu koşulları da dinleyebildiğini söylediğinden de yararlı olur. Dil veri kümesinde en az 100 ve genellikle birkaç yüz ya da daha fazla utlik olması yaygındır. Ayrıca, bazı sorgu türlerinin diğerlerinden daha yaygın olması bekleniyorsa, ortak sorguların birden çok kopyasını veri kümesine ekleyebilirsiniz.

**S: yalnızca bir sözcük listesini karşıya yükleyebilir miyim?**

Y **: sözcüklerin bir listesini** karşıya yüklemek, sözcükleri sözlüğüne ekler, ancak sözcüklerin tipik olarak nasıl kullanıldığını sisteme öğretmez. Tam veya kısmi bir şekilde (kullanıcıların söyledikleri nesnelerin cümleler veya tümceleri), dil modelinin yeni kelimeleri ve nasıl kullanıldığını öğrenebilirsiniz. Özel dil modeli yalnızca sisteme yeni sözcükler eklemek için değil, aynı zamanda uygulamanız için bilinen sözcüklerin olasılığını ayarlamak için de uygundur. Tam dıklarýn sağlanması sistemin daha iyi öğrenilmesine yardımcı olur.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Kiracı modeli (Microsoft 365 verilerle Özel Konuşma Tanıma)

**S: kiracı modeline hangi bilgiler dahildir ve nasıl oluşturulur?**

Y **:** Kiracı modeli, kuruluşunuzdaki herkes tarafından görülemeyen [ortak grup](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) e-postaları ve belgeleri kullanılarak oluşturulmuştur.

**S: kiracı modeli tarafından hangi konuşma deneyimleri geliştirildi?**

Y **:** Kiracı modeli etkinleştirildiğinde, oluşturulup yayımlandığında, konuşma hizmeti kullanılarak oluşturulan tüm kurumsal uygulamalarda tanımayı geliştirmek için kullanılır; Bu Ayrıca, kuruluşa üyelik belirten bir Kullanıcı Azure AD belirteci de iletir.

Dikte ve PowerPoint resim yazısı gibi Microsoft 365 yerleşik konuşma deneyimleri, konuşma hizmeti uygulamalarınız için bir kiracı modeli oluşturduğunuzda değiştirilmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Sorun giderme](troubleshooting.md)
- [Sürüm notları](releasenotes.md)