---
title: Özel yeteneği ölçeklendirin ve yönetin
titleSuffix: Azure Cognitive Search
description: En yüksek aktarım hızı için özel bir yetenek verimli bir şekilde ölçeklendirmeye yönelik araçları ve teknikleri öğrenin. Özel yetenekler, Azure Bilişsel Arama 'de bir AI zenginleştirme dizin oluşturma işlem hattına ekleyebileceğiniz özel AI modellerini veya mantığını çağırır.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 4fdc222fa20aef6639bf6d5d485f7dcf6b6ca535
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641148"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Özel bir yeteneğin etkin bir şekilde ölçeğini genişletme

Özel yetenekler, belirli bir arabirimi uygulayan Web API 'lardır. Özel bir yetenek, genel olarak adreslenebilir herhangi bir kaynakta uygulanabilir. Özel yetenekler için en yaygın uygulamalar şunlardır:
* Özel mantık becerileri için Azure Işlevleri
* Basit Kapsayıcılı AI becerileri için Azure webapps
* Daha karmaşık veya daha büyük yetenekler için Azure Kubernetes hizmeti.

## <a name="prerequisites"></a>Önkoşullar

+ Özel bir yeteneğin uygulanması gereken giriş/çıkış arabirimine giriş için [özel beceri arabirimini](cognitive-search-custom-skill-interface.md) gözden geçirin.

+ Ortamınızı ayarlayın. Visual Studio Code ve Python uzantılarını kullanarak sunucusuz Azure Işlevini ayarlamak için [Bu öğreticiden uçtan uca](/azure/azure-functions/create-first-function-vs-code-python) başlayabilirsiniz.

## <a name="skillset-configuration"></a>Beceri yapılandırması

Dizin oluşturma işleminin aktarım hızını en üst düzeye çıkarmak için özel bir yetenek yapılandırmak, Beceri, Dizin Oluşturucu yapılandırmalarının ve yeteneğin her belgeyle nasıl ilişkili olduğunu anlamak için gereklidir. Örneğin, bir yeteneğin belge başına çağrılma sayısı ve çağrı başına beklenen süre.

### <a name="skill-settings"></a>Yetenek ayarları

[Özel beceri](cognitive-search-custom-skill-web-api.md) üzerinde aşağıdaki parametreleri ayarlayın.

1. `batchSize`Yeteneğin tek bir çağrısında yeteneğe gönderilen kayıt sayısını yapılandırmak için özel beceri kümesi.

2. `degreeOfParallelism`Dizin oluşturucunun becerinize yapması için eşzamanlı istek sayısını ayarlamak için öğesini ayarlayın.

3. `timeout`Niteliğin geçerli bir Yanıtla yanıt vermesi için yeterince bir değere ayarlayın.

4. `indexer`Tanımında, [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) veri kaynağından okunmalı ve aynı anda zenginleştirilmiş belge sayısına ayarlayın.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

Dizin oluşturucular performansını iyileştirmek için bu değişkenlerin ayarlanması, becerinizi birçok eşzamanlı küçük istekten veya daha az büyük isteklerle daha iyi bir şekilde gerçekleştirip gerçekleştirmediğini belirlemeyi gerektirir. Göz önünde bulundurulması gereken birkaç soru şunlardır:

* Yetenek çağırma kardinalitesi nedir? Beceri her belge için bir kez yürütülür, örneğin bir belge sınıflandırması yeteneği, ya da bu beceri belge başına birden çok kez yürütülecektir bir paragraf sınıflandırma becerisi mi?

* Yetenek toplu iş boyutuna bağlı olarak bir yetenek isteğini dolduracak şekilde veri kaynağından kaç belge okunduğunu ortalama? İdeal olarak, bu Dizin Oluşturucu toplu iş boyutundan daha az olmalıdır. Toplu iş boyutlarıyla 1 ' den büyük olan beceri, birden fazla kaynak belgeden kayıt alabilir. Örneğin, Dizin Oluşturucu toplu iş sayısı 5 ve yetenek toplu iş sayısı 50 ise ve her belge yalnızca beş kayıt oluşturursa, dizin oluşturucunun, birden çok Dizin Oluşturucu toplu işleminde özel bir yetenek isteğini doldurması gerekir.

* Bir Dizin Oluşturucu toplu işleminin oluşturabileceği ortalama istek sayısı, paralellik derecenin derece için en iyi ayarı sunmalıdır. Yeteneği barındıran altyapınız bu eşzamanlılık düzeyini desteklemiyorsa, paralellik derecesini çevirmeyi göz önünde bulundurun. En iyi uygulama olarak, parametrelerinizi parametrelere göre doğrulamak için yapılandırmanız birkaç belgeyle test edin.

* Daha küçük bir belge örneği ile test etmek için, niteliğin yürütme süresini, belge alt kümesini işlemek için geçen sürenin genel zamanına göre değerlendirin. Dizin oluşturucunuz bir toplu iş oluşturuyor veya becerinizden yanıt beklerken daha fazla zaman harcasın mı? 

* Paralellik için yukarı akış etkilerini göz önünde bulundurun. Özel bir beceriye giriş önceki bir beceriye ait bir çıktıyse, Beceri 'deki tüm yetenekler gecikme süresini en aza indirmek için etkin şekilde ölçeklendirildi mi?

## <a name="error-handling-in-the-custom-skill"></a>Özel yeteneğin hata işleme

Beceri başarıyla tamamlandığında özel yetenekler HTTP 200 başarılı bir durum kodu döndürmelidir. Bir toplu işteki bir veya daha fazla kayıt hatalara neden olursa, çok durum kodu 207 ' i döndürmeyi göz önünde bulundurun. Kayıt için hata veya uyarı listesi uygun iletiyi içermelidir.

Toplu işteki herhangi bir öğe, ilgili belgenin başarısız olmasına neden olur. Belgenin başarılı olması gerekiyorsa bir uyarı döndürün.

299 üzerinden herhangi bir durum kodu bir hata olarak değerlendirilir ve tüm enzenginler başarısız bir belgeye neden olur. 

### <a name="common-error-messages"></a>Genel hata iletileri

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Beceri üzerinde zaman aşımı parametresini daha uzun bir yürütme süresine izin verecek şekilde ayarlayın.

* `Could not execute skill because Web Api skill response is invalid.` Özel beceri yanıtı biçiminde bir ileti döndürmedi beceriye işaret eder. Bu, yeteneğin yakalanamayan bir özel durumun sonucu olabilir.

* `Could not execute skill because the Web Api request failed.` Büyük olasılıkla yetkilendirme hataları veya özel durumlar nedeniyle oluşur.

* `Could not execute skill.` Yaygın olarak, yetenek yanıtının belge hiyerarşisinde var olan bir özellikle eşlenmesinden kaynaklanır.

## <a name="testing-custom-skills"></a>Özel becerileri test etme

Özel becerinizi doğrulamak için bir REST API istemcisiyle test ederek başlayın:

* Bu beceri, istekler ve yanıtlar için özel beceri arabirimini uygular

* Beceri, MIME türü ile geçerli bir JSON döndürüyor `application/JSON`

* Geçerli bir HTTP durum kodu döndürür

Becerinizi beceri 'ye eklemek için bir [hata ayıklama oturumu](cognitive-search-debug-session.md) oluşturun ve geçerli bir zenginleştirme ürettiğinden emin olun. Bir hata ayıklama oturumu yeteneğin performansını ayarlamanıza izin vermediğinden, yeteneğin geçerli değerlerle yapılandırılmasını sağlamanıza ve beklenen zenginleştirilmiş nesneleri döndürmenize olanak tanır.

## <a name="best-practices"></a>En iyi uygulamalar

* Yetenekler, daha büyük yükleri kabul edebilir ve döndürebilir, JSON 'u döndürürken 150 MB veya daha az olan yanıtı sınırlamayı düşünün.

* Her bir veri kaynağı toplu işinin, becerinize yönelik bir tam yük üretdiğinden emin olmak için Dizin Oluşturucu ve beceri üzerinde toplu iş boyutunu ayarlamayı düşünün.

* Uzun süre çalışan görevler için, dizin oluşturucunun belgeleri eşzamanlı olarak işlerken hata oluşturmaz emin olmak için zaman aşımını yeterince yüksek bir değere ayarlayın.

* Niteliğin beklediği yük modelini oluşturmak için Dizin Oluşturucu toplu iş boyutunu, yetenek toplu iş boyutunu ve benzersiz paralellik derecesini iyileştirin, daha az büyük istekler veya çok sayıda küçük istek.

* Verilerin değişkenlik sonucu olarak belirli isteklerin sürekli olarak başarısız olduğu senaryolara sahip olduğunuz için ayrıntılı hata günlükleri ile özel becerileri izleyin.


## <a name="next-steps"></a>Sonraki adımlar
Tebrikler! Özel Beceri, Dizin oluşturucudaki aktarım hızını en üst düzeye çıkarmak için hemen ölçeklendirilir. 

+ [Güç becerileri: özel yeteneklerin bir deposu](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Bir AI zenginleştirme ardışık düzenine özel yetenek ekleme](cognitive-search-custom-skill-interface.md)
+ [Azure Machine Learning yetenek ekleme](./cognitive-search-aml-skill.md)
+ [Değişiklikleri test etmek için hata ayıklama oturumlarını kullanın](./cognitive-search-debug-session.md)