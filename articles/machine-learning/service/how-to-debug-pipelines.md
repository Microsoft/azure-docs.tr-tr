---
title: Makine öğrenimi ardışık düzenleri hata ayıklama ve sorunlarını giderme
titleSuffix: Azure Machine Learning
description: Python için Azure Machine Learning SDK 'da makine öğrenimi ardışık düzenleri hata ayıklayın ve sorun giderin. İşlem hatları geliştirmek için ortak olan ve uzaktan yürütme sırasında ve betiklerinizde hata ayıklamanıza yardımcı olan ipuçları hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 10/03/2019
ms.openlocfilehash: fc19e864f00489d3ebc0162705af864785af0811
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497070"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Makine öğrenimi ardışık düzenleri hata ayıklama ve sorunlarını giderme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, [Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)'da [makine öğrenimi](concept-ml-pipelines.md) işlem hatlarında hata ayıklamayı ve sorun gidermeyi öğreneceksiniz.

Aşağıdaki bölümler, işlem hatları oluştururken ortak olan genel bakışa genel bakış ve bir işlem hattında çalışan kodunuzda hata ayıklama için farklı stratejiler sağlar. İşlem hattını beklenen şekilde çalıştırmak için bir işlem hattı alırken sorun yaşadığınızda aşağıdaki ipuçlarını kullanın. 

## <a name="testing-scripts-locally"></a>Betikleri yerel olarak test etme

Bir işlem hattındaki en yaygın hatalardan biri, ekli bir betiğin (veri temizleme betiği, Puanlama betiği, vb.) amaçlanan gibi çalışmadığı veya uzak işlem bağlamında hata ayıklamanın Azure makinesindeki çalışma alanınızda hata ayıklaması zor olan çalışma zamanı hataları içerdiğinde Eğitim Studio. 

Ardışık düzenleri yerel olarak çalıştırılamaz, ancak komut dosyalarını yerel makinenizde yalıtımlarda çalıştırmak, işlem ve ortam oluşturma işlemini beklemek zorunda olmadığınızdan daha hızlı hata ayıklamanıza olanak tanır. Bunu yapmak için bazı geliştirme işleri gereklidir:

* Verileriniz bir bulut veri deposunda yer alıyorsa, verileri indirmeniz ve betiğiniz için kullanılabilir hale getirmeniz gerekir. Verilerinizin küçük bir örneğini kullanmak çalışma zamanını kesmek ve betik davranışında hızlıca geri bildirim almak için iyi bir yoldur
* Ara işlem hattı adımının benzetimini gerçekleştirmeye çalışıyorsanız, belirli bir betiğin önceki adımdan beklediği nesne türlerini el ile oluşturmanız gerekebilir
* Ayrıca kendi ortamınızı tanımlamanız ve Uzaktan işlem ortamınızda tanımlanan bağımlılıkları çoğaltmanız gerekecektir

Yerel ortamınızda çalıştırmak üzere bir betik kurulumuna sahip olduktan sonra, şu gibi hata ayıklama görevlerinin olması çok daha kolaydır:

* Özel hata ayıklama yapılandırması iliştirme
* Yürütmeyi duraklatma ve nesne durumunu inceleme
* Çalışma zamanına kadar sunulmayacak tür veya mantıksal hataları yakalama

> [!TIP] 
> Betiğinizin beklendiği gibi çalıştığını doğrulayabilmeniz için, bir sonraki adım komut dosyasını birden çok adımla bir işlem hattında çalıştırmayı denemeden önce tek adımlı bir işlem hattında çalıştırıyor.

## <a name="debugging-scripts-from-remote-context"></a>Uzak bağlamdaki betiklerin hatalarını ayıklama

Komut dosyalarını yerel olarak test etmek, bir işlem hattı oluşturmaya başlamadan önce büyük kod parçalarının ve karmaşık mantığın hatalarını ayıklamanın harika bir yoludur. ancak bazı durumlarda gerçek işlem hattı sırasında betiklerde hata ayıklamanız gerekir, özellikle de oluşan davranışı tanılarken işlem hattı adımları arasındaki etkileşim sırasında. Adım betiklerinizde `print()` deyimlerinin serbest bir şekilde kullanılmasını öneririz; Böylece, JavaScript kodunun hatalarını ayıklamanıza benzer şekilde, uzaktan yürütme sırasında nesne durumunu ve beklenen değerleri görebilirsiniz.

`70_driver_log.txt` günlük dosyası şunları içerir: 

* Betiğinizin yürütülmesi sırasında tüm yazdırılmış deyimler
* Betik için yığın izlemesi 

Portalda bu ve diğer günlük dosyalarını bulmak için, önce çalışma alanınızdaki işlem hattına tıklayın.

![Portalda işlem hatları sayfası](./media/how-to-debug-pipelines/pipeline-1.png)

Ardışık düzen üst öğesine gidin.

![Ardışık düzen üst çalıştırması](./media/how-to-debug-pipelines/pipeline-2.png)

Belirli bir adım için çalıştırma KIMLIĞI ' ne tıklayın.

![Portalda işlem hatları sayfası](./media/how-to-debug-pipelines/pipeline-3.png)

**Günlükler** sekmesine gidin. Diğer Günlükler, ortam görüntüsü oluşturma işlemi ve adım hazırlama betikleriniz hakkında bilgiler içerir.

![Portalda işlem hatları sayfası](./media/how-to-debug-pipelines/pipeline-4.png)

> [!TIP]
> Yayınlanan işlem *hatları* için çalıştırmalar, portalda çalışma alanınızdaki işlem **hatları** sekmesinde bulunabilir. *Yayımlanamayan işlem hatları* için çalıştırmalar, **denemeleri**içinde bulunabilir.

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

Aşağıdaki tabloda, potansiyel çözümlerle birlikte işlem hattı geliştirme sırasında yaygın sorunlar yer almaktadır.

| Sorun | Olası çözüm |
|--|--|
| `PipelineData` dizinine veri geçirilemez | Komut dosyasında, işlem hattının adım çıkış verilerini beklediği yere karşılık gelen bir dizin oluşturduğunuzdan emin olun. Çoğu durumda, bir giriş bağımsız değişkeni çıkış dizinini tanımlar ve ardından dizini açıkça oluşturursunuz. Çıkış dizinini oluşturmak için `os.makedirs(args.output_dir, exist_ok=True)` kullanın. Bu tasarım modelini gösteren bir Puanlama betiği örneği için [öğreticiye](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) bakın. |
| Bağımlılık hataları | Komut dosyalarını yerel olarak geliştirdiyseniz, ancak işlem hattındaki uzak bir işlem üzerinde çalışırken bağımlılık sorunları bulursanız, işlem ortamı bağımlılıklarınızın ve sürümlerinin test ortamınızla eşleştiğinden emin olun. |
| İşlem hedefleri ile belirsiz hatalar | İşlem hedeflerini silme ve yeniden oluşturma işlemi, işlem hedefleriyle ilgili bazı sorunları çözebilir. |
| İşlem hattı adımları yeniden kullanma | Adım yeniden kullanım varsayılan olarak etkindir, ancak işlem hattı adımında devre dışı bırakılmadığınızdan emin olun. Yeniden kullanım devre dışıysa, adımdaki `allow_reuse` parametresi `False`olarak ayarlanır. |
| İşlem hattı gereksiz yere yeniden çalıştırılıyor | Adımların yalnızca temel alınan verileri veya betikleri değiştiğinde yeniden çalıştırılması için dizinlerinizi her bir adımla ayırın. Birden çok adım için aynı kaynak dizinini kullanırsanız, gereksiz yeniden çalıştırma işlemleri yaşayabilirsiniz. İşlem hattı adım nesnesindeki `source_directory` parametresini kullanarak bu adım için yalıtılmış dizininizi işaret edin ve birden çok adım için aynı `source_directory` yolunu kullanmadığınız emin olun. |

## <a name="next-steps"></a>Sonraki adımlar

* [Azureml işlem hatları-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) paketi ve [azureml-işlem hatları-adımlar](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) PAKETIYLE ilgili yardım için SDK başvurusuna bakın.

* Toplu Puanlama için işlem hatlarını kullanma hakkında [Gelişmiş öğreticiyi](tutorial-pipeline-batch-scoring-classification.md) izleyin.
