---
title: 'Permütasyon özelliği önem derecesi: modül başvurusu'
titleSuffix: Azure Machine Learning service
description: Eğitilen bir model ve test veri kümesi verildiğinde, özellik değişkenlerinin permütasyon özelliği önem puanlarını hesaplamak için Azure Machine Learning hizmetinde permütasyon özelliği önem derecesi ' ni nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ddd631b809d4a0635107069f48281db4c0a2e7e0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837547"
---
# <a name="permutation-feature-importance"></a>Permütasyon özelliği önem derecesi

Bu makalede, veri kümeniz için bir özellik önem puanlarının bir kümesini hesaplamak üzere Azure Machine Learning Tasarımcısı 'nda (Önizleme) permütasyon özelliği önem derecesi 'nin nasıl kullanılacağı açıklanır. Bu puanları, bir modelde kullanılacak en iyi özellikleri belirlemenize yardımcı olması için kullanabilirsiniz.

Bu modülde, özellik değerleri tek seferde bir sütun olacak şekilde rastgele karıştırılır. Modelin performansı, ve sonrasında ölçülür. Performansı ölçmek için Standart ölçülerden birini seçebilirsiniz.

Modülün döndürdüğü puanlar, permütasyon sonrasında eğitilen bir modelin performansındaki *değişikliği* temsil eder. Önemli özellikler genellikle karışık süreç için daha duyarlıdır ve bu nedenle daha yüksek önem derecesine neden olur. 

Bu makalede, makine öğrenimi: [permütasyon özelliğinin önem derecesine](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)sahip olan permütasyon özelliği, teorik özelliği ve uygulamaları hakkında genel bir bakış sunulmaktadır.  

## <a name="how-to-use-permutation-feature-importance"></a>Permütasyon özelliği önem derecesi kullanma

Bir özellik puanları kümesi oluşturmak, zaten eğitilen bir modelin yanı sıra bir test veri kümesi olmasını gerektirir.  

1.  İşlem hattınızı permütasyon özelliği önem derecesi ' ni ekleyin. Bu modülü **Özellik seçimi** kategorisinde bulabilirsiniz. 

2.  Eğitilen bir modeli sol girişe bağlayın. Model, regresyon modeli veya sınıflandırma modeli olmalıdır.  

3.  Doğru girişte bir veri kümesini bağlayın. Tercihen, modeli eğitmek için kullandığınız veri kümesinden farklı bir tane seçin. Bu veri kümesi, eğitilen modeli temel alan Puanlama için kullanılır. Ayrıca, özellik değerleri değiştirildikten sonra modeli değerlendirmek için de kullanılır.  

4.  **Rastgele çekirdek**için, rasgeleleştirme için çekirdek olarak kullanılacak bir değer girin. 0 (varsayılan) belirtirseniz, sistem saatine göre bir sayı oluşturulur.

     Çekirdek değer isteğe bağlıdır, ancak aynı işlem hattının reproducibility üzerinde çalışmasını istiyorsanız bir değer sağlamalısınız.  

5.  **Performansı ölçmeye yönelik ölçüm**için, permütasyon sonrasında model kalitesi hesaplanırken kullanılacak tek bir ölçüm seçin.  

     Azure Machine Learning tasarımcı, bir sınıflandırma veya regresyon modeli değerlendiriyor olmanıza bağlı olarak aşağıdaki ölçümleri destekler:  

    -   **Classification** (Sınıflandırma)

        Doğruluk, duyarlık, geri çekme, ortalama günlük kaybı  

    -   **Regresyon**

        Precision, geri çek, ortalama mutlak hata, kök ortalama kare hatası, göreli mutlak hata, göreli kare hatası, belirleme katsayısı  

     Bu değerlendirme ölçümlerinin ve nasıl hesaplandıkları hakkında daha ayrıntılı bir açıklama için bkz. [modeli değerlendir](evaluate-model.md).  

6.  İşlem hattını çalıştırma.  

7.  Modül, özellik sütunlarının bir listesini ve bunlarla ilişkili puanları çıktı olarak verir. Liste puanlarını azalan sırada derecelendirilir.  


##  <a name="technical-notes"></a>Teknik notlar

Permütasyon özelliği önem derecesi, her bir özellik sütununun değerlerini rastgele değiştirerek, tek seferde bir sütundan oluşur. Daha sonra modeli değerlendirir. 

Modülün sağladığı ransler, [filtre tabanlı özellik seçiminden](filter-based-feature-selection.md)aldığınız verilerden genellikle farklıdır. Filtre tabanlı özellik seçimi, bir model oluşturulmadan *önce* puanları hesaplar. 

Farkın nedeni, permütasyon özelliğinin önem derecesi bir özellik ile hedef değer arasındaki ilişkilendirmeyi ölçmez. Bunun yerine, her bir özelliğin modelden tahminlerde ne kadar etkinin olduğunu yakalar.
  
## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 
