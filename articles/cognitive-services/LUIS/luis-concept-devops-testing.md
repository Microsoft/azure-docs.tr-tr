---
title: LUSıS uygulamaları için DevOps test ediliyor
description: Language Understanding (LUSıS) uygulamanızı DevOps ortamında test etme.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788460"
---
# <a name="testing-for-luis-devops"></a>LUSıS DevOps için test etme

Language Understanding (LUU) uygulaması geliştiren yazılım mühendisleri, bu yönergeleri izleyerek [kaynak denetimi](luis-concept-devops-sourcecontrol.md), [Otomatik derlemeler](luis-concept-devops-automation.md), [Test](luis-concept-devops-testing.md)ve [Sürüm Yönetimi](luis-concept-devops-automation.md#release-management) etrafında DevOps uygulamaları uygulayabilir.

Çevik yazılım geliştirme yöntemleri ' nde, test, kaliteli yazılım oluştururken bir integral rol oynar. Bir LUO uygulamasında yapılan her önemli değişiklik, geliştiricinin uygulamada oluşturduğu yeni işlevleri test etmek üzere tasarlanan testler ile birlikte kullanılmalıdır. Bu testler, LUSıS uygulamanızın kaynağıyla birlikte kaynak kod deponuzda işaretlenir `.lu` . Uygulama testleri karşılıyorsa değişikliğin uygulanması tamamlanmıştır.

Testler [CI/CD iş akışlarının](luis-concept-devops-automation.md)kritik bir parçasıdır. Bir LUSıS uygulamasında yapılan değişiklikler bir çekme isteğinde (PR) önerilmişse veya değişiklikler ana dalınızla birleştirildikten sonra, bu durumda, güncelleştirmelerin herhangi bir gerileme olmadığını doğrulamak için CI iş akışlarının testleri çalıştırmaları gerekir.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Birim testi ve toplu Işlem testi yapma

Sürekli Tümleştirme iş akışlarında gerçekleştirmeniz gereken bir LUO uygulaması için iki farklı test türü vardır:

- **Birim testleri** -lusıs uygulamanızın önemli işlevlerini doğrulayan görece basit testler. Belirli bir test ututi için beklenen amaç ve beklenen varlıklar döndürüldüğünde bir birim testi geçer. Test çalıştırmasının başarıyla tamamlanabilmesi için tüm birim testlerinin başarılı olması gerekir.  
Bu tür bir test, [luın portalında](https://www.luis.ai/)yapabileceğiniz [etkileşimli teste](./luis-concept-test.md) benzerdir.

- **Toplu iş testleri** -Batch testi, performansını ölçmek için geçerli eğitilen modelinize yönelik kapsamlı bir sınamadır. Birim testlerinin aksine toplu işlem testi başarılı olmaz | başarısız test. Toplu iş testi ile beklendiğinde, her testin beklenen amacı ve beklenen varlıkları döndürmesi gerekir. Bunun yerine, bir Batch testi uygulamanızdaki her bir amaç ve varlığın doğruluğunu görüntülemenize ve iyileştirmeler yaparken zaman içinde karşılaştırmanıza yardımcı olur.  
Bu tür bir test, LUO portalında etkileşimli olarak gerçekleştirebileceğiniz [Batch testi](./luis-how-to-batch-test.md) ile aynıdır.

Projenizin başından birim testi kullanabilirsiniz. Toplu iş testi, LUSıS uygulamanızın şemasını geliştirdikten ve doğruluğunu iyileştirmek için çalışmanız durumunda yalnızca bir değerden oluşur.

Hem birim testleri hem de toplu sınamalar için, test dıklarınızın eğitim dıklarından ayrı tutulduğundan emin olun. Üzerinde eğitecağınız verileri test ederseniz, uygulamanın test verilerine çok fazla dahil olduğu konusunda yanlış izlenimi alırsınız. Testlerin ne kadar iyi hale kullanıldığını test etmek için, modelin, model tarafından görülmeyen bir değer olması gerekir.

### <a name="writing-tests"></a>Testleri yazma

Bir test kümesi yazdığınızda, tanımlamanız gereken her bir test için:

* Test söylenişi
* Beklenen amaç
* Beklenen varlıklar.

Bir test grubunu JSON biçimli bir dosyada tanımlamak için LUO [Batch dosyası sözdizimini](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities) kullanın. Örnek:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

NLU gibi bazı test araçları [. DevOps](https://github.com/microsoft/NLU.DevOps) Ayrıca Luaşağı biçimli test dosyalarını destekler.

#### <a name="designing-unit-tests"></a>Birim testlerini tasarlama

Birim testleri, LUSıS uygulamanızın temel işlevselliğini test etmek için tasarlanmalıdır. Uygulama geliştirmelerinizin her yinelemede veya Sprint 'de, bu yinelemede uyguladığınız anahtar işlevselliğinin doğru şekilde çalıştığını doğrulamak için yeterli sayıda test yazmanız gerekir.

Her birim testinde, belirli bir test utterliği için şunları yapabilirsiniz:

* Doğru amacın döndürüldüğünü test edin
* ' Anahtar ' varlıklarının, çözümünüz için kritik olan ' ın döndürüldüğünden test ediliyor.
* Amaç ve varlıkların [tahmin puanının](./luis-concept-prediction-score.md) tanımladığınız eşiği aştığını test edin. Örneğin, amaç için tahmin puanı 0,75 ' ı aşarsa yalnızca bir testin geçtiğini göz önünde bulundurmaya karar verebilirsiniz.

Birim testlerinde, anahtar varlıklarınızın tahmin yanıtında döndürüldüğünü test etmek ve hatalı pozitif sonuçları yoksaymak için bu iyi bir fikirdir. *Hatalı pozitif* durumlar, tahmin yanıtında bulunan ancak testiniz için beklenen sonuçlarda tanımlanmayan varlıklardır. Yanlış pozitifleri yoksayarak, birim testlerini yazmak daha az bir işlem yapar, ancak çözümünüz için anahtar olan verilerin bir tahmin yanıtında döndürüldüğünden teste odaklanmanıza odaklanmaya devam edersiniz.

> [!TIP]
> [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) Aracı, tüm lusıs test ihtiyaçlarınızı destekler. `compare` [Birim testi modunda](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) kullanıldığında, tüm testlerin başarılı olduğunu ve beklenen sonuçlarda etiketlenmemiş varlıkların yanlış pozitif sonuçlarını yoksaymasını sağlayacak komut.

#### <a name="designing-batch-tests"></a>Batch testlerini tasarlama

Batch test kümeleri, tüm amaçlar genelinde ve LUSıS uygulamanızdaki tüm varlıklarda test etmek için tasarlanan çok sayıda test çalışması içermelidir. Batch test kümesi tanımlama hakkında bilgi için bkz. [LUA portalında Batch test etme](./luis-how-to-batch-test.md) .

### <a name="running-tests"></a>Testleri çalıştırma

Lua portalı etkileşimli test konusunda yardımcı olacak özellikler sunar:

* [**Etkileşimli test**](./luis-concept-test.md) , örnek bir değer göndermenize ve lu, tanınan amaçlardan ve varlıklardan yanıt almanıza olanak sağlar. Testin başarısını görsel inceleme ile doğrularsınız.

* [**Toplu işlem**](./luis-how-to-batch-test.md) testi, etkin eğitilen sürümünüzü, tahmin doğruluğunu ölçecek şekilde doğrulamak için giriş olarak bir toplu işlem test dosyası kullanır. Batch testi, etkin sürümünüzde her bir amaç ve varlığın doğruluğunu görüntülemenize yardımcı olur ve sonuçları grafik ile görüntüler.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Otomatikleştirilmiş derleme iş akışında testleri çalıştırma

Lub portalındaki etkileşimli test özellikleri faydalıdır, ancak DevOps için bir CI/CD iş akışında gerçekleştirilen otomatikleştirilmiş test bazı gereksinimleri getirir:

* Test araçları, bir yapı sunucusundaki bir iş akışı adımında çalıştırılmalıdır. Bu, araçların komut satırında çalıştırılabilmesi gerektiği anlamına gelir.
* Test araçları bir uç noktaya karşı bir test grubunu yürütebilmelidir ve beklenen sonuçları gerçek sonuçlara göre otomatik olarak doğrulayabilmelidir.
* Testler başarısız olursa, test araçlarının iş akışını durdurmak için bir durum kodu döndürmesi ve "derlemeyi başarısız olması" gerekir.

LUSıS, bu özellikleri sunan bir komut satırı aracı veya üst düzey bir API sunmaz. [NLU kullanmanız önerilir. ](https://github.com/microsoft/NLU.DevOps)Testleri çalıştırmak ve sonuçları doğrulamak Için DevOps Aracı, hem komut satırında hem de BIR CI/CD iş akışı içinde otomatik test sırasında.

LUO portalında kullanılabilen test özellikleri yayımlanmış bir uç nokta gerektirmez ve LUSıS yazma yeteneklerinin bir parçasıdır. Otomatikleştirilmiş derleme iş akışında test uygularken, NLU gibi test araçları için bir uç noktaya test etmek üzere LUSıS uygulama sürümünü yayımlamanız gerekir. DevOps, sınama kapsamında tahmin istekleri gönderebilir.

> [!TIP]
> * Kendi test çözümünüzü uygulamadıysanız ve bir uç noktaya test alma işlemleri göndermek için kod yazıyorsanız, LUSıS yazma anahtarını kullanıyorsanız, izin verilen işlem hızının 5 TPS ile sınırlı olduğunu unutmayın. Gönderme oranını kısıtlama ya da bunun yerine bir tahmin anahtarı kullanın.
> * Bir uç noktaya test sorguları gönderirken, `log=false` tahmin isteğinizin sorgu dizesinde kullanmayı unutmayın. Bu, test dıklarınızın LUIN tarafından günlüğe kaydedilmeyeceğini ve LUSıS [etkin öğrenme](./luis-concept-review-endpoint-utterances.md) özelliği tarafından sunulan uç nokta uttersları gözden geçirme listesinde sonlandırılmasını ve sonuç olarak, uygulamanızın eğitim mühendislerine yanlışlıkla eklenmemesini sağlar.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Birim testlerini komut satırında ve CI/CD iş akışlarında çalıştırma

NLU 'yı kullanabilirsiniz [. ](https://github.com/microsoft/NLU.DevOps) Komut satırında testleri çalıştırmak Için DevOps paketi:

* NLU kullanın. Testleri bir test dosyasından bir uç noktaya göndermek ve gerçek tahmin sonuçlarını bir dosyada yakalamak için DevOps [test komutu](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) .
* NLU kullanın. Giriş testi dosyasında tanımlanan beklenen sonuçlarla gerçek sonuçları karşılaştırmak için DevOps [Compare komutu](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) . `compare`Komut NUnit test çıktısı oluşturur ve bayrağın kullanımı ile [birim testi modunda](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) kullanıldığında `--unit-test` , tüm testlerin başarılı olacağını onaylar.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Komut satırında ve CI/CD iş akışlarında toplu testleri çalıştırma

NLU 'yı de kullanabilirsiniz. Komut satırında Batch testlerini çalıştırmak için DevOps paketi.

* NLU kullanın. Testleri bir test dosyasından bir uç noktaya göndermek ve gerçek tahmin sonuçlarını birim testleriyle aynı bir dosyada yakalamak için DevOps [test komutu](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) .
* NLU kullanın. Uygulamanızın performansını ölçmek için [performans testi modundaki](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) DevOps [Compare komutu](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) , uygulamanızın performansını temel bir performans karşılaştırmasına karşı karşılaştırabilir, örneğin, en son işlemeden ana veya geçerli sürüme kadar olan sonuçlar. Performans testi modunda `compare` komut, JSON biçiminde NUnit test çıktısı ve [Batch test sonuçları](./luis-glossary.md#batch-test) üretir.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUSıS belirleyici olmayan eğitim ve test üzerindeki etkisi

LUSıS, amaç gibi bir modele eğitim yaparken, hem pozitif veri ister. bu modelin kullanımına *yönelik geçerli olmayan* model ve negatif veri verilerine yönelik uygulamayı eğmede sağladığınız etiketli eğitim dıklarınız. Eğitim sırasında, Lua, diğer modeller için sağladığınız tüm pozitif verilerden bir modelin negatif verilerini oluşturur, ancak bazı durumlarda bir veri dengesizliği üretebilirler. Bu dengesizliği önlemek için, LUSıS daha iyi bir dengeli eğitim kümesi, geliştirilmiş model performansı ve daha hızlı eğitim süresi için en iyi hale getirilmiş bir değer olan negatif verilerin bir alt kümesini örnekliyor.

Bu belirleyici olmayan eğitimin sonucu, [farklı eğitim oturumları arasında](./luis-concept-prediction-score.md), genellikle [tahmin puanının](./luis-concept-prediction-score.md) yüksek olmadığı amaçlar ve/veya varlıklar arasında biraz farklı bir tahmin yanıtı elde edebilir.

Test amacıyla oluşturmakta olduğunuz LUO uygulama sürümleri için belirleyici olmayan eğitimi devre dışı bırakmak istiyorsanız, ayarı olarak ayarlanmış olan [Sürüm Ayarları API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) 'sini kullanın `UseAllTrainingData` `true` .

## <a name="next-steps"></a>Sonraki adımlar

* [CI/CD iş akışlarını uygulama](luis-concept-devops-automation.md) hakkında bilgi edinin
* [GitHub Ile lusıs Için DevOps 'u nasıl uygulayacağınızı](luis-how-to-devops-with-github.md) öğrenin