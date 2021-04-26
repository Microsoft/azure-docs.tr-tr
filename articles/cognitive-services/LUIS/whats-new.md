---
title: Yenilikler-Language Understanding (LUSıS)
description: Bu makale, Azure bilişsel hizmetler Language Understanding API hakkındaki haberlerle düzenli olarak güncelleştirilir.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: f6c6f2ba20a67cd39d4142ffadddc803927c936c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227067"
---
# <a name="whats-new-in-language-understanding"></a>Language Understanding yenilikleri

Hizmette nelerin yeni olduğunu öğrenin. Bu öğeler sürüm notlarını, Videoları, blog gönderilerini ve diğer bilgi türlerini içerir. Hizmette güncel kalmasını sağlamak için bu sayfaya yer işareti ekleyin.

## <a name="release-notes"></a>Sürüm notları

### <a name="april-2021"></a>2021 Nisan

* İsviçre Kuzey [yazma bölgesi](luis-reference-regions.md#publishing-to-europe).

### <a name="january-2021"></a>Ocak 2021

* V3 tahmin API 'si artık [Bing yazım denetımı API](luis-tutorial-bing-spellcheck.md)'sini destekliyor.
* Bölgesel portallar (au.luis.ai ve eu.luis.ai) tek bir portalda ve URL 'de birleştirildi. Bu portallardan birini kullanıyorsanız, luis.ai 'e otomatik olarak yeniden yönlendirilirsiniz.

### <a name="december-2020"></a>Aralık 2020

* [Lusıs yazma kaynağına geçiş](luis-migration-authoring.md) yapmak IÇIN tüm luya kullanıcıları gereklidir
* REST API kullanarak toplu testler göndermenize ve amaç ve varlıklarınız için doğruluk sonuçları elde etmeniz için yeni [değerlendirme uç noktaları](luis-how-to-batch-test.md#batch-testing-using-the-rest-api) . V 3.0-Preview LUSıS uç noktasından başlayarak kullanılabilir.

### <a name="june-2020"></a>Haziran 2020

* [Preview 3,0 yazma](luis-migration-authoring-entities.md) 'Sının
    * Sürüm 3.2.0-Önizleme. 3- [.net-NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Sürüm 4.0.0-Önizleme. 3- [js-NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* LUIS ile DevOps yöntemlerini uygulama
    * Kavramlar
        * [LUSıS için DevOps uygulamaları](luis-concept-devops-sourcecontrol.md)
        * [LUSıS DevOps için sürekli tümleştirme ve sürekli teslim iş akışları](luis-concept-devops-automation.md)
        * [LUSıS DevOps için test etme](luis-concept-devops-testing.md)
    * Nasıl yapılır
        * [GitHub eylemlerini kullanarak LUSıS uygulama geliştirmeye DevOps uygulama](luis-how-to-devops-with-github.md)
    * [Komple kod GitHub deposu](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Mayıs 2020-/sürümü

* **Genel kullanıma açık** (GA):
    * [Language Understanding kapsayıcı](luis-container-howto.md)
    * Önizleme portalı [geçerli portala](https://www.luis.ai)yükseltildi, [önceki](https://previous.luis.ai) Portal hala kullanılabilir
    * Yeni makine öğrenimi varlık oluşturma ve etiketleme deneyimi
    * Bileşik ve basit varlıklardan makine öğrenimi varlıklarına [yükseltme işlemi](migrate-from-composite-entity.md)
    * Sözcük çeşitlemelerini normalleştirime desteğini [ayarlama](how-to-application-settings-portal.md)
* Önizleme yazma API 'SI değişiklikleri
    * İç içe makine öğrenimi varlıkları için uygulama şeması 7. x
    * [Gerekli özelliğe geçiş](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Geliştiriciler için yeni kaynaklar
    * [Sürekli tümleştirme araçları](developer-reference-resource.md#continuous-integration-tools)
    * Workshop- [lusıs kullanarak _doğal Language Understanding_ (NLU) için en iyi yöntemleri öğrenin](developer-reference-resource.md#workshops)
* [Müşteri tarafından yönetilen anahtarlar](./encrypt-data-at-rest.md) -halde kullandığınız tüm verileri kendi anahtarınızı kullanarak şifreleyin
* [AI Show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (video)-lusıs 'deki yeni özelliklere bakın



### <a name="march-2020"></a>Mart 2020

* TLS 1,2, bu hizmete yönelik tüm HTTP istekleri için de zorlanır. Daha fazla bilgi için bkz. Azure bilişsel [Hizmetler güvenliği](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4 Kasım 2019-Menite

* Vısıs [ve Azure bilişsel hizmetler kullanan video gelişmiş doğal Language Understanding (NLU) modelleri | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Geliştirilmiş geliştirici verimliliği
    * [Tahmin uç noktası v3](luis-migration-api-v3.md)'nin genel kullanılabilirliği.
    * `.lu`([Luaşağı](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) biçimindeki uygulamaları içeri ve dışarı aktarma özelliği. Bu, etkin bir CI/CD işleminin yolunu ele alır.
* Dil genişletmesi
    * [Arapça ve Hintçe](luis-language-support.md) genel önizlemede.
* Önceden oluşturulmuş modeller
    * [Önceden oluşturulmuş etki alanları](luis-reference-prebuilt-domains.md) artık genel kullanıma sunuldu (GA)
    * Japonca [önceden oluşturulmuş varlıklar](luis-reference-prebuilt-entities.md#japanese-entity-support) -yaş, para birimi, sayı ve yüzde v3 'de desteklenmez.
    * İtalyanca [önceden oluşturulmuş varlıklar](luis-reference-prebuilt-entities.md#italian-entity-support) -yaş, para birimi, boyut, sayı ve yüzde çözünürlüğü v2 'den değiştirilmiştir.
* [Preview.Luis.ai Portal](https://preview.luis.ai) 'da geliştirilmiş Kullanıcı deneyimi-karmaşık modellerin oluşturulmasını ve hata ayıklamasını etkinleştirmek için etiketleme deneyimidir. Önizleme portalı öğreticilerini deneyin:
    * [Yalnızca amaçlar](tutorial-intents-only.md)
    * [Birleştirilebilen makine öğrenimi varlığı](tutorial-machine-learned-entity.md)
* Gelişmiş dil özelliklerini anlama-daha az çaba ile [Gelişmiş dil modelleri oluşturma](luis-concept-entity-types.md) .
* Model düzeyinde makine öğrenimi özelliklerini tanımlayın ve modellerin diğer modellere sinyal olarak kullanılmasını etkinleştirin. Örneğin, varlıkları amaçlar ve diğer varlıklara özellikler olarak kullanma.
* Yeni, genişletilmiş [sınırlar](luis-limits.md) -tümcecik listeleri ve toplam ifadeler için yüksek maksimum sınır, özellik sınırları olarak yeni model
* Derin hiyerarşi yapısı biçimindeki metinden bilgi ayıklayın ve konuşma uygulamalarını daha güçlü hale getirir.

    ![makine öğrenimi varlık resmi](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 Eylül 2019

* Azure yazma kaynağı- [Şimdi geçirin](luis-migration-authoring.md).
    * Azure kaynağı başına 500 uygulama
    * Uygulama başına 100 sürüm
* Önceden oluşturulmuş varlıklar için Türkçe desteği
* DatetimeV2 için İtalyanca desteği

### <a name="july-23-2019"></a>23 Temmuz 2019

* [Tanıyıcıları-metin](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) ile 1.2.3 güncelleştirme
    * Italyanca ' de yaş, sıcaklık, boyut ve para birimi tanıyıcıları.
    * Tatil tabanlı tarihleri doğru şekilde hesaplamak için Ingilizce olarak tatil tanıma geliştirme.
    * Tarih ve saat olmayan varlıkların yanlış pozitiflerini azaltmak için Fransız Tarih/saatteki geliştirmeler.
    * Ingilizce gün içinde takvim/okul/mali yıl ve kısaltmalar için destek.
    * Çince ve Japonca için, iyileştirilmiş PhoneNumber Tanıma.
    * Ingilizce olarak NumberRange desteği geliştirildi.
    * Performans geliştirmeleri.

### <a name="june-24-2019"></a>24 Haziran 2019

* İleri, önceki ve son gibi sıralamayı desteklemek için [OrdinalV2 önceden oluşturulmuş varlık](luis-reference-prebuilt-ordinal-v2.md) . Yalnızca İngilizce kültürü.

### <a name="may-6-2019---build-conference"></a>6 Mayıs 2019-/sürüm Konferansı

Aşağıdaki özellikler Build 2019 Konferansı 'nda yayımlanmıştır:

* [V3 API geçiş kılavuzu önizlemesi](luis-migration-api-v3.md)
* [Geliştirilmiş analiz panosu](luis-how-to-use-dashboard.md)
* [Geliştirilmiş önceden oluşturulmuş etki alanları](luis-reference-prebuilt-domains.md)
* [Dinamik liste varlıkları](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Dış varlıklar](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Bloglar

[Bot Altyapısı](https://blog.botframework.com/)

## <a name="videos"></a>Videolar

### <a name="2019-ignite-videos"></a>2019 Ignite videoları

[LUVE Azure bilişsel hizmetler kullanan gelişmiş doğal Language Understanding (NLU) modeller | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 derleme videoları

[Azure konuşma AI kullanarak işinizi bir sonraki nesil için ölçeklendirin](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Hizmet güncelleştirmeleri

[Bilişsel hizmetler için Azure Update duyuruları](https://azure.microsoft.com/updates/?product=cognitive-services)