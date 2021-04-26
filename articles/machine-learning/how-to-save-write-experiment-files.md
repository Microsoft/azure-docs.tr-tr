---
title: '& yazma deneme dosyalarının kaydedileceği yer'
titleSuffix: Azure Machine Learning
description: Depolama sınırlaması hatalarını ve deneme gecikmesini engellemek için giriş ve çıkış dosyalarınızın nereye kaydedileceğini öğrenin.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 59955b291ce706a77d0dd5ab052809fe725166d9
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387896"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Azure Machine Learning denemeleri için dosya kaydetme ve yazma


Bu makalede, giriş dosyalarının nereye kaydedileceğini ve depolama sınırı hatalarını ve deneme gecikmesini engellemek için denemeleri 'dan çıktı dosyalarının nereye yazılacağını öğrenirsiniz.

Bir [işlem hedefi](concept-compute-target.md)üzerinde eğitim çalıştırmaları başlatıldığında, bunlar dış ortamlardan yalıtılmıştır. Bu tasarımın amacı, denemenin reproducibility ve taşınabilirliği sağlamaktır. Aynı betiği iki kez çalıştırırsanız aynı veya başka bir işlem hedefinde aynı sonuçları alırsınız. Bu tasarımla, işlem hedeflerini durum bilgisiz hesaplama kaynakları olarak, her biri tamamlandıktan sonra çalışmakta olan işlere benzeşim olmadan kabul edebilirsiniz.

## <a name="where-to-save-input-files"></a>Giriş dosyalarının kaydedileceği yer

Bir işlem hedefi veya yerel makinenizde bir deneme başlamadan önce, kodunuzun çalışması gereken bağımlılık dosyaları ve veri dosyaları gibi gerekli dosyaların bu işlem hedefi için kullanılabilir olduğundan emin olmanız gerekir.

Azure Machine Learning, tüm kaynak dizinini kopyalayarak eğitim betikleri çalıştırır. Karşıya yüklemek istemediğiniz gizli verileriniz varsa, bir [. Ignore dosyası](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) kullanın veya kaynak dizine eklemeyin. Bunun yerine, veri [deposu](/python/api/azureml-core/azureml.data)kullanarak verilerinize erişin.

Deneme anlık görüntülerinin depolama sınırı 300 MB ve/veya 2000 dosyadır.

Bu nedenle şunları öneririz:

* **Dosyalarınızı Azure Machine Learning [veri kümesinde](/python/api/azureml-core/azureml.data)depolama.** Bu, deneme süresi sorunlarını önler ve uzaktan bir işlem hedefinden verilere erişmenin avantajlarından yararlanır, bu da kimlik doğrulama ve bağlama Azure Machine Learning tarafından yönetilir. Veri kümesi [ile](how-to-train-with-datasets.md)eğitim betiğinizdeki giriş veri kaynağınız olarak veri kümesini belirtme hakkında daha fazla bilgi edinin.

* **Yalnızca birkaç veri dosyası ve bağımlılık betiklerine Ihtiyacınız varsa ve bir veri deposu kullanamaz,** dosyaları eğitim betiğinizle aynı klasör dizinine yerleştirin. Bu klasörü `source_directory` doğrudan eğitim betiğinizdeki veya eğitim betiğinizi çağıran kodda belirtin.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Deneme anlık görüntülerinin depolama sınırları

Denemeleri için Azure Machine Learning, çalıştırmayı yapılandırırken önerdiğiniz dizine göre kodunuzun deneme anlık görüntüsünü otomatik olarak yapar. Bunun toplam 300 MB ve/veya 2000 dosya sınırı vardır. Bu sınırı aşarsanız aşağıdaki hatayı görürsünüz:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Bu hatayı çözmek için, denemenizin dosyalarını bir veri deposunda depolayın. Bir veri deposu kullanamıyoruz, aşağıdaki tabloda olası alternatif çözümler sunulmaktadır.

Deneme &nbsp; açıklaması|Depolama sınırı çözümü
---|---
2000 'den az dosya & veri deposu kullanamaz| Anlık görüntü boyutu sınırını geçersiz kıl <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Bu işlem, dosyaların sayısına ve boyutuna bağlı olarak birkaç dakika sürebilir.
Belirli komut dosyası dizini kullanılmalıdır| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
İşlem Hattı|Her adım için farklı bir alt dizin kullanın
Jupyter Notebooks| Bir `.amlignore` dosya oluşturun veya not defterinizi yeni, boş bir alt dizine taşıyın ve kodunuzu yeniden çalıştırın.

## <a name="where-to-write-files"></a>Dosyaların yazılacağı yer

Eğitim denemeleri yalıtımı nedeniyle, çalışma sırasında oluşan dosyalardaki değişiklikler ortamınızın dışında kalıcı hale gelmez. Betiğinizin yerel olarak işlem yapmasını değiştirirse, değişiklikler sonraki denemenizin çalıştırılması için kalıcı olmaz ve istemci makinesine otomatik olarak geri yayılmaz. Bu nedenle, ilk deneme sırasında yapılan değişiklikler çalışmaz ve ikinciden etkilenmemelidir.

Değişiklikleri yazarken, [Outputfiledatasetconfig nesnesine](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)sahip bir Azure Machine Learning veri kümesi aracılığıyla depolama alanına dosya yazılmasını öneririz. Bkz. [OutputFileDatasetConfig oluşturma](how-to-train-with-datasets.md#where-to-write-training-output).

Aksi takdirde, dosyaları `./outputs` ve/veya klasörüne yazın `./logs` .

>[!Important]
> İki klasör, *Çıkış* ve *günlük*, Azure Machine Learning özel bir işleme alır. Eğitim sırasında, dosyaları `./outputs` ve `./logs` klasörlere yazdığınızda, dosyalar çalışma geçmişinize otomatik olarak yüklenir, böylece çalıştırma tamamlandıktan sonra bunlara erişebilirsiniz.

* **Durum iletileri veya Puanlama sonuçları gibi bir çıktı için,** dosyaları `./outputs` klasöre yazın ve bu nedenle çalışma geçmişinde yapıtlar olarak kalıcı hale getirilir. İçerik çalıştırma geçmişine yüklendiğinde gecikme sürebileceği için, bu klasöre yazılan dosyaların sayısı ve boyutu hakkında mindazın. Gecikme bir sorun oluşturacaksa, dosyaları bir veri deposuna yazmak önerilir.

* **Yazılan dosyayı çalıştırma geçmişinde Günlükler olarak kaydetmek için** dosyaları `./logs` klasöre yazın. Günlükler gerçek zamanlı olarak karşıya yüklenir, bu nedenle bu yöntem uzak bir çalıştırmada canlı güncelleştirmeleri akışa uygundur.

## <a name="next-steps"></a>Sonraki adımlar

* [Depolama alanından verilere erişme](how-to-access-data.md)hakkında daha fazla bilgi edinin.

* [Model eğitimi ve dağıtımı için işlem hedefleri oluşturma](how-to-create-attach-compute-studio.md) hakkında daha fazla bilgi edinin