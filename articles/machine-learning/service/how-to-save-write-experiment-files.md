---
title: '& Yazma deneme dosyalarının kaydedileceği yer'
titleSuffix: Azure Machine Learning
description: Deneme girdi dosyalarınızın nereye kaydedileceğini ve depolama sınırlaması hatalarını ve deneme gecikmesini engellemek için çıkış dosyalarının nereye yazılacağını öğrenin.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 41b2602e57d295cfd7e475f4b3aa5657bd4e24d7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489597"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Azure Machine Learning denemeleri için dosya kaydetme ve yazma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, giriş dosyalarının nereye kaydedileceğini ve depolama sınırı hatalarını ve deneme gecikmesini engellemek için denemeleri 'dan çıktı dosyalarının nereye yazılacağını öğrenirsiniz.

Bir [işlem hedefi](how-to-set-up-training-targets.md)üzerinde eğitim çalıştırmaları başlatıldığında, bunlar dış ortamlardan yalıtılmıştır. Bu tasarımın amacı, denemenin reproducibility ve taşınabilirliği sağlamaktır. Aynı betiği iki kez çalıştırırsanız aynı veya başka bir işlem hedefinde aynı sonuçları alırsınız. Bu tasarımla, işlem hedeflerini durum bilgisiz hesaplama kaynakları olarak, her biri tamamlandıktan sonra çalışmakta olan işlere benzeşim olmadan kabul edebilirsiniz.

## <a name="where-to-save-input-files"></a>Giriş dosyalarının kaydedileceği yer

Bir işlem hedefi veya yerel makinenizde bir deneme başlamadan önce, kodunuzun çalışması gereken bağımlılık dosyaları ve veri dosyaları gibi gerekli dosyaların bu işlem hedefi için kullanılabilir olduğundan emin olmanız gerekir.

Azure Machine Learning, tüm betik klasörünü hedef işlem bağlamına kopyalayarak ve ardından bir anlık görüntü alan eğitim betikleri çalıştırır. Deneme anlık görüntüleri için depolama sınırı 300 MB ve/veya 2000 dosyalarıdır.

Bu nedenle şunları öneririz:

* **Dosyalarınızı Azure Machine Learning [veri deposunda](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)depolama.** Bu, deneme süresi sorunlarını önler ve uzaktan bir işlem hedefinden verilere erişmenin avantajlarından yararlanır, bu da kimlik doğrulama ve bağlama Azure Machine Learning tarafından yönetilir. Kaynak dizininiz olarak bir veri deposu belirtme ve [verileri veri depoları makalesindeki erişim verilerine](how-to-access-data.md) yükleme hakkında daha fazla bilgi edinin.

* **Yalnızca birkaç veri dosyası ve bağımlılık betiklerine Ihtiyacınız varsa ve bir veri deposu kullanamaz,** dosyaları eğitim betiğinizle aynı klasör dizinine yerleştirin. Bu klasörü doğrudan eğitim betiğinizdeki `source_directory` olarak veya eğitim betiğinizi çağıran kodda belirtin.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Deneme anlık görüntülerinin depolama sınırları

Denemeleri için Azure Machine Learning, çalıştırmayı yapılandırırken önerdiğiniz dizine göre kodunuzun deneme anlık görüntüsünü otomatik olarak yapar. Bunun toplam 300 MB ve/veya 2000 dosya sınırı vardır. Bu sınırı aşarsanız aşağıdaki hatayı görürsünüz:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Bu hatayı çözmek için, denemenizin dosyalarını bir veri deposunda depolayın. Bir veri deposu kullanamıyoruz, aşağıdaki tabloda olası alternatif çözümler sunulmaktadır.

Deneme&nbsp;açıklaması|Depolama sınırı çözümü
---|---
2000 'den az dosya & veri deposu kullanamaz| Anlık görüntü boyutu sınırını geçersiz kıl <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Bu işlem, dosyaların sayısına ve boyutuna bağlı olarak birkaç dakika sürebilir.
Belirli komut dosyası dizini kullanılmalıdır| Kaynak kodunun parçası olmayan denemenizin anlık görüntüsünden dosyaları dışlamak için `.amlignore` bir dosya oluşturun. Dosya adlarını `.amlignore` dosyasına ekleyin ve eğitim betiğinizle aynı dizine yerleştirin. `.amlignore` dosyası, bir `.gitignore` dosyası ile aynı [söz dizimini ve desenleri](https://git-scm.com/docs/gitignore) kullanır.
İşlem hattı|Her adım için farklı bir alt dizin kullanın
Jupyter notebooks| `.amlignore` bir dosya oluşturun veya not defterinizi yeni, boş bir alt dizine taşıyın ve kodunuzu yeniden çalıştırın.

## <a name="where-to-write-files"></a>Dosyaların yazılacağı yer

Eğitim denemeleri yalıtımı nedeniyle, çalışma sırasında oluşan dosyalardaki değişiklikler ortamınızın dışında kalıcı hale gelmez. Betiğinizin yerel olarak işlem yapmasını değiştirirse, değişiklikler sonraki denemenizin çalıştırılması için kalıcı olmaz ve istemci makinesine otomatik olarak geri yayılmaz. Bu nedenle, ilk deneme sırasında yapılan değişiklikler çalışmaz ve ikinciden etkilenmemelidir.

Değişiklikleri yazarken, bir Azure Machine Learning veri deposuna dosya yazılmasını öneririz. Bkz. [veri mağazalarınızın verilerine erişme](how-to-access-data.md).

Bir veri deposu gerekmiyorsa, dosyaları `./outputs` ve/veya `./logs` klasörüne yazın.

>[!Important]
> İki klasör, *Çıkış* ve *günlük*, Azure Machine Learning özel bir işleme alır. Eğitim sırasında, dosyaları`./outputs` ve`./logs` klasörlerine yazdığınızda dosyalar otomatik olarak çalıştırma geçmişinize yüklenir, böylece çalıştırma tamamlandıktan sonra bunlara erişebilirsiniz.

* **Durum iletileri veya Puanlama sonuçları gibi çıktılar için** dosyaları `./outputs` klasöre yazın. bu nedenle, çalışma geçmişinde yapıtlar olarak kalıcı hale getirilir. İçerik çalıştırma geçmişine yüklendiğinde gecikme sürebileceği için, bu klasöre yazılan dosyaların sayısı ve boyutu hakkında mindazın. Gecikme bir sorun oluşturacaksa, dosyaları bir veri deposuna yazmak önerilir.

* **Yazılan dosyayı çalıştırma geçmişinde Günlükler olarak kaydetmek için** dosyaları `./logs` klasöre yazın. Günlükler gerçek zamanlı olarak karşıya yüklenir, bu nedenle bu yöntem uzak bir çalıştırmada canlı güncelleştirmeleri akışa uygundur.

## <a name="next-steps"></a>Sonraki adımlar

* [Veri mağazalarınızın verilerine erişme](how-to-access-data.md)hakkında daha fazla bilgi edinin.

* [Eğitim hedeflerini ayarlama](how-to-set-up-training-targets.md)hakkında daha fazla bilgi edinin.
