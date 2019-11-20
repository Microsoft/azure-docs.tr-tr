---
title: Modelinizde hata ayıklama
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasik) içinde model eğitme ve model modülleri Puanlama tarafından oluşturulan hatalarda hata ayıklama.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 494141cc580d80ff1d025228406d53f9788b5d97
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837749"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio modelinizde hata ayıklama (klasik)

Bir model çalıştırırken aşağıdaki hatalarla karşılaşabilirsiniz:

* [model eğitme][train-model] modülü bir hata üretir 
* [puan modeli][score-model] modülü hatalı sonuçlar oluşturuyor 

Bu makalede bu hataların olası nedenleri açıklanmaktadır.


## <a name="train-model-module-produces-an-error"></a>Model modeli eğitimi bir hata üretir

![image1](./media/debug-models/train_model-1.png)

[Model eğitme][train-model] modülü iki giriş bekler:

1. Azure Machine Learning Studio (klasik) tarafından sunulan modeller koleksiyonundan makine öğrenimi modelinin türü.
2. Tahmin edilecek değişkeni belirten belirtilen bir etiket sütunuyla eğitim verileri (diğer sütunların özellikler olduğu varsayılır).

Bu modül aşağıdaki durumlarda bir hata oluşturabilir:

1. Etiket sütunu yanlış belirtildi. Bu durum, etiket olarak birden fazla sütun seçilirse veya yanlış bir sütun dizini seçilirse meydana gelebilir. Örneğin, ikinci durum, yalnızca 25 sütun içeren bir giriş veri kümesiyle 30 ' un bir sütun dizini kullanılıyorsa geçerlidir.

2. Veri kümesi hiçbir özellik sütunu içermiyor. Örneğin, giriş veri kümesinde etiket sütunu olarak işaretlenen yalnızca bir sütun varsa, modelin oluşturulacağı bir özellik olmaz. Bu durumda, [model eğitimi][train-model] modülü bir hata üretir.

3. Giriş veri kümesi (Özellikler veya etiket) bir değer olarak sonsuz değerini içerir.

## <a name="score-model-module-produces-incorrect-results"></a>Puan modeli modülü hatalı sonuçlar oluşturuyor

![image2](./media/debug-models/train_test-2.png)

Denetimli öğrenme için tipik bir eğitim/test denemesinde, [bölünmüş veri][split] modülü özgün veri kümesini iki parçaya böler: bir bölüm modeli eğmek için, bir bölüm ise eğitilen modelin ne kadar iyi performans olduğunu öğrenmek için kullanılır. Daha sonra eğitilen model, test verilerini Puanlama için kullanılır ve sonrasında, bu, modelin doğruluğunu belirleyen sonuçlar değerlendirilir.

[Puan modeli][score-model] modülü iki giriş gerektirir:

1. Eğitim [modeli][train-model] modülünden eğitilen bir model çıkışı.
2. Modeli eğitmek için kullanılan veri kümesinden farklı bir Puanlama veri kümesi.

Deneme başarılı olsa bile, [puan modeli][score-model] modülü hatalı sonuçlar üretir. Birkaç senaryo bu sorunun oluşmasına neden olabilir:

1. Belirtilen etiket kategorik ise ve bir regresyon modeli veri üzerinde eğitilmiş ise, [puan modeli][score-model] modülü tarafından yanlış bir çıktı oluşturulur. Bunun nedeni, gerileme sürekli bir yanıt değişkeni gerektirmesidir. Bu durumda, bir sınıflandırma modeli kullanmak daha uygun olacaktır. 

2. Benzer şekilde, bir sınıflandırma modeli, etiket sütununda kayan nokta numaraları bulunan bir veri kümesi üzerinde eğitiliyorsa, istenmeyen sonuçlara neden olabilir. Bunun nedeni, sınıflandırmanın yalnızca sonlu ve küçük bir sınıf kümesi üzerinde yer alan değerlere izin veren ayrık bir yanıt değişkeni gerektirmesidir.

3. Puanlama veri kümesi, modeli eğitmek için kullanılan tüm özellikleri içermiyorsa, [puan modeli][score-model] bir hata üretir.

4. Puanlama veri kümesindeki bir satır, özelliklerinin herhangi biri için eksik bir değer veya sonsuz bir değer içeriyorsa, [puan modeli][score-model] söz konusu satıra karşılık gelen bir çıktı oluşturmaz.

5. [Puan modeli][score-model] , Puanlama veri kümesindeki tüm satırlarda aynı çıktıları üretebilir. Bu durum, örneğin yaprak düğüm başına minimum örnek sayısı kullanılabilir eğitim örnekleri sayısından daha fazla olacak şekilde seçilirse karar ormanları kullanılarak sınıflandırma denenmeye çalışıldığında meydana gelebilir.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

