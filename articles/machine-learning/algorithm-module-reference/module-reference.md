---
title: Algoritma ve modül başvurusu
description: Azure Machine Learning tasarımcısında kullanılabilen modüller hakkında bilgi edinin (Önizleme)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 11/11/2019
ms.openlocfilehash: 21577936f2487313683ac763b0c6c661c6c1f958
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039328"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Azure Machine Learning Tasarımcısı için algoritma & modül başvurusu

Bu başvuru içeriği, Azure Machine Learning tasarımcısında bulunan her makine öğrenimi algoritması ve modülleriyle ilgili teknik arka plan sağlar (Önizleme).

Her modül, gerekli girişler verildiğinde bağımsız olarak çalışabilen ve makine öğrenimi görevi gerçekleştiren bir kod kümesini temsil eder. Bir modül belirli bir algoritma içerebilir veya eksik değer değiştirme veya istatistiksel analiz gibi makine öğrenimi için önemli bir görev gerçekleştirebilir.

> [!TIP]
> Tasarımcıda herhangi bir işlem hattında, belirli bir modülle ilgili bilgi edinebilirsiniz. Modülünü seçin ve ardından **hızlı yardım** bölmesinde **daha fazla yardım** bağlantısını seçin.

## <a name="modules"></a>Modules

Modüller işlevlere göre düzenlenmiştir:

| İşlev | Açıklama | Modül |
| --- |--- | ---- |
| Veri girişi ve çıkışı | Verileri bulut kaynaklarından ardışık düzene taşıyın. Sonuçları veya ara verilerinizi Azure depolama 'ya, bir SQL veritabanına veya Hive 'ye, işlem hattı çalıştırırken veya işlem hatları arasında veri alışverişi yapmak için bulut depolama alanını kullanarak yazın.  | [Verileri içeri aktar](import-data.md) <br/> [Verileri el Ile girin](enter-data-manually.md) <br/>[Verileri dışarı aktar](export-data.md) |
| Veri dönüştürme | Makine öğrenimi için benzersiz olan, verileri normalleştirime veya binme, Boyut azaltma ve çeşitli dosya biçimleri arasında veri dönüştürme gibi işlemler.| [Sütun Ekle](add-columns.md) <br/> [Satır Ekle](add-rows.md) <br/> [Eksik verileri temizle](clean-missing-data.md) <br/> [CSV 'ye Dönüştür](convert-to-csv.md) <br/> [Veri kümesine Dönüştür](convert-to-dataset.md) <br/> [Meta verileri Düzenle](edit-metadata.md) <br/> [Verileri birleştirin](join-data.md) <br/> [Verileri Normalleştir](normalize-data.md) <br/> [Yinelenen satırları kaldır](remove-duplicate-rows.md) <br/> [Sütun dönüştürmeyi Seç](select-columns-transform.md) <br/> [Veri kümesindeki sütunları seçme](select-columns-in-dataset.md) |
| Örnekleme | Makine öğrenimi modellerini eğmek ve test etmek için verilerinizi bir veya daha fazla alt küme alt kümelerine ayırın.  | [Çapraz doğrulama modeli](cross-validate-model.md) <br/> [Bölüm ve örnek](partition-and-sample.md) <br/> [SMOTE](smote.md) <br/> [Verileri Böl](split-data.md) |
| Özellik seçimi | Analitik model oluştururken kullanılacak ilgili, yararlı özelliklerin bir alt kümesini seçin. | [Filtre tabanlı özellik seçimi](filter-based-feature-selection.md) <br/> [Permütasyon özelliği önem derecesi](permutation-feature-importance.md) |
| Python ve R | Python ve R 'yi ardışık düzen ile bütünleştirmek için kodu yazın ve bir modüle ekleyin. | [Python modeli oluşturma](create-python-model.md) <br/> [Python betiğini Yürüt](execute-python-script.md)   <br/>  [R betiğini Yürüt](execute-r-script.md)
| Metin Analizi | Yapılandırılmış ve yapılandırılmamış metinle çalışmak için özelleştirilmiş hesaplama araçları sağlar. | [Metinden N gram özelliklerini Ayıkla](extract-n-gram-features-from-text.md) <br/> [Özellik karma](feature-hashing.md) <br/> [Ön işleme metni](preprocess-text.md) |
|  | **Makine öğrenimi algoritmaları**: | |
| Sınıflandırma | Bir sınıfı tahmin edin.  İkili (iki sınıf) veya birden çok sınıf algoritmalarından birini seçin.| [Birden çok Lass karar ormanı](multiclass-decision-forest.md) <br/> [Birden çok sınıf tarafından artırılmış karar ağacı](multiclass-boosted-decision-tree.md) <br/> [Birden çok Lass Lojistik gerileme](multiclass-logistic-regression.md)  <br/> [Birden çok Lass sinir ağı](multiclass-neural-network.md) <br/> [Tek bir ve birden çok Lass](one-vs-all-multiclass.md) <br/>  [İki sınıf Lojistik gerileme](two-class-logistic-regression.md)  <br/>[İki sınıflı ortalama Perceptron](two-class-averaged-perceptron.md) <br/> [İki sınıf önceden artırılmış karar ağacı](two-class-boosted-decision-tree.md)  <br/> [İki sınıf karar ormanı](two-class-decision-forest.md)  <br/> [İki sınıf sinir ağı](two-class-neural-network.md) <br/> [İki sınıf desteği vektör makinesi](two-class-support-vector-machine.md) | 
| Lenmesi | Verileri birlikte gruplandırın.| [K-kümelemenin anlamı](k-means-clustering.md)
| Regresyon | Bir değer tahmin edin. | [Artırılmış karar ağacı gerileme](boosted-decision-tree-regression.md) <br/> [Karar ormanı gerileme](decision-forest-regression.md) <br/> [Doğrusal regresyon](linear-regression.md)  <br/> [Sinir ağ gerileme](neural-network-regression.md)  <br/> |
| Öneren | Öneri modelleri oluşturun. | [Öneren değerlendir](evaluate-recommender.md) <br/> [Skor SVD öneren](score-svd-recommender.md) <br/> [SVD öneren eğitme](train-SVD-recommender.md) |
|  | **Model oluşturma ve değerlendirme**: | |
| Eğitim   | Algoritmayı kullanarak verileri çalıştırın. | [Modeli eğitme](train-model.md)  <br/> [Kümeleme modelini eğitme](train-clustering-model.md) <br/>  [Model hiper parametrelerini ayarla](tune-model-hyperparameters.md) |
| Modeli Değerlendirme | Eğitilen modelin doğruluğunu ölçün. |  [Modeli değerlendir](evaluate-model.md) |
| Puan | Az önce eğitilen modelden tahminlerden yararlanın. | [Dönüştürmeyi Uygula](apply-transformation.md)<br/>[Kümelere veri atama](assign-data-to-clusters.md) <br/>[Puan modeli](score-model.md) |
| İstatistiksel İşlevler | Veri bilimi ile ilgili çok çeşitli istatistiksel yöntemler sağlar. | [Matematik Işlemini Uygula](apply-math-operation.md) <br/> [Verileri özetleme](summarize-data.md)|

## <a name="error-messages"></a>Hata iletileri

Azure Machine Learning tasarımcısında modüller kullanarak karşılaşabileceğiniz [hata iletileri ve özel durum kodları](machine-learning-module-error-codes.md) hakkında bilgi edinin.
