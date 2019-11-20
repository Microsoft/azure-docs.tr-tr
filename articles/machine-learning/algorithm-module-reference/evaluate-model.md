---
title: 'Modeli değerlendir: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Eğitilen bir modelin doğruluğunu ölçmek için Azure Machine Learning modeli değerlendir modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: ddc6a35d5959a2d1f85c151a6f229223db9ba566
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497917"
---
# <a name="evaluate-model-module"></a>Model modülünü değerlendir

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Eğitilen bir modelin doğruluğunu ölçmek için bu modülü kullanın. Bir modelden oluşturulan puanları içeren bir veri kümesi sağlarsınız ve **modeli değerlendir** modülü bir sektör standardı değerlendirme ölçümleri kümesini hesaplar.
  
 **Değerlendirme modeli** tarafından döndürülen ölçümler, değerlendirdiğiniz modelin türüne bağlıdır:  
  
-   **Sınıflandırma modelleri**    
-   **Regresyon modelleri**    



> [!TIP]
> Model değerlendirmesine yeni çalışıyorsanız, EdX 'in [makine öğrenimi kursu](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) kapsamında Dr. Stephen Elston tarafından video serisini öneririz. 


**Modeli değerlendir** modülünü kullanmanın üç yolu vardır:

+ Eğitim verileriniz üzerinde puanlar oluşturun ve bu puanları temel alarak modeli değerlendirin
+ Modelde puanlar oluşturun, ancak bu puanları ayrılmış bir test kümesinde puanlar ile karşılaştırın
+ Aynı veri kümesini kullanarak iki farklı ancak ilgili modellerin puanlarını karşılaştırın

## <a name="use-the-training-data"></a>Eğitim verilerini kullanma

Bir modeli değerlendirmek için bir giriş sütunları ve puanları kümesi içeren bir veri kümesini bağlamanız gerekir.  Başka veri yoksa, özgün veri kümenizi kullanabilirsiniz.

1. [Puan modelinin puın](./score-model.md) **veri kümesi** çıkışını, **değerlendirme modeli**girişine bağlayın. 
2. **Modelleme modülünü değerlendir** ' e tıklayın ve değerlendirme puanlarını oluşturmak için işlem hattını çalıştırın.

## <a name="use-testing-data"></a>Test verilerini kullan

Machine Learning 'deki yaygın bir senaryo, özgün veri kümelerinizi eğitim ve test veri kümelerini, [bölünmüş](./split-data.md) modül veya [bölüm ve örnek](./partition-and-sample.md) modülünü kullanarak ayıramaktır. 

1. [Puan modelinin puın](score-model.md) **veri kümesi** çıkışını, **değerlendirme modeli**girişine bağlayın. 
2. Test verilerini içeren bölünmüş veri modülünün çıkışını, **modeli değerlendir**'in sağ girişine bağlayın.
2. **Modelleme modülünü değerlendir** ' e tıklayın ve değerlendirme puanlarını oluşturmak Için **Seçileni Çalıştır** ' ı seçin.

## <a name="compare-scores-from-two-models"></a>İki modelden puanları karşılaştırın

Ayrıca, **modeli değerlendirmek**için ikinci bir puan kümesini bağlayabilirsiniz.  Puanlar, bilinen sonuçlara sahip bir paylaşılan değerlendirme kümesi veya aynı veriler için farklı bir modelden bir sonuç kümesi olabilir.

Aynı verilerdeki iki farklı modelden sonuçları kolayca karşılaştırabilmeniz için bu özellik yararlıdır. Ya da, farklı parametrelerle aynı veriler üzerinde bulunan iki farklı çalıştırmaların puanlarını karşılaştırabilirsiniz.

1. [Puan modelinin puın](score-model.md) **veri kümesi** çıkışını, **değerlendirme modeli**girişine bağlayın. 
2. İkinci modelin puan modeli modülünün çıkışını, **modeli değerlendir**' in sağ girişine bağlayın.
3. **Modeli değerlendir**' e sağ tıklayın ve değerlendirme puanlarını oluşturmak Için **Seçileni Çalıştır** ' ı seçin.

## <a name="results"></a>Sonuçlar

**Modeli değerlendir**' i çalıştırdıktan sonra, modüle sağ tıklayın ve sonuçları görmek için **değerlendirme sonuçları** ' nı seçin. Şunları yapabilirsiniz:

+ Diğer araçlarla daha kolay analize yönelik olarak sonuçları bir veri kümesi olarak kaydedin
+ Tasarımcıda görselleştirme oluşturma

Veri kümelerini her iki **değerlendirme modeli**girişi için bağladığınızda, sonuçlar hem veri kümesinin hem de her iki modelin ölçümlerini içerir.
Sol bağlantı noktasına eklenen model veya veriler, önce raporda, ardından veri kümesi için ölçümler veya sağ bağlantı noktasında eklenmiş model tarafından sunulur.  

Örneğin, aşağıdaki görüntü, aynı veriler üzerinde oluşturulmuş, ancak farklı parametrelerle oluşturulan iki kümeleme modelinden sonuçların karşılaştırmasını temsil eder.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Bu bir kümeleme modeli olduğundan, değerlendirme sonuçları, puanları iki gerileme modelinden karşılaştırmakla veya iki sınıflandırma modelinin karşılaştırılmasından farklı olur. Ancak, genel sunum aynıdır. 

## <a name="metrics"></a>Ölçümler

Bu bölümde, **modeli değerlendir**ile kullanılmak üzere desteklenen belirli model türleri için döndürülen ölçümler açıklanmaktadır:

+ [sınıflandırma modelleri](#bkmk_classification)
+ [regresyon modelleri](#bkmk_regression)

###  <a name="bkmk_classification"></a>Sınıflandırma modelleriyle ilgili ölçümler

Sınıflandırma modelleri değerlendirilirken aşağıdaki ölçümler raporlanır. Modelleri karşılaştırırsanız, bunlar değerlendirme için seçtiğiniz ölçüm tarafından derecelendirilir.  
  
-   **Doğruluk** , toplam durum sonuçlarının gerçek sonuçlarının oranı olarak bir sınıflandırma modelinin iyiyliğini ölçer.  
  
-   **Duyarlık** , tüm pozitif sonuçlar üzerinde doğru sonuçların oranındaki orandır.  
  
-   **Geri çağırma** , modelin döndürdüğü tüm doğru sonuçların kesiri.  
  
-   **F puanı** ağırlıklı duyarlık ortalaması olarak hesaplanır ve ideal F puanı değerinin 1 olduğu 0 ile 1 arasında geri çağırır.  
  
-   **AUC** , y ekseninde gerçek pozitifler ve x ekseninde hatalı pozitif sonuçlar ile çizilen eğrinin altındaki alanı ölçer. Bu ölçüm, farklı türlerin modellerini karşılaştırmanızı sağlayan tek bir sayı sağladığından yararlıdır.  
  
- **Ortalama günlük kaybı** , yanlış sonuçlar için ceza Puanını ifade etmek için kullanılan tek bir puandır. Bu, iki olasılık dağıtımı arasındaki fark olarak hesaplanır; gerçek bir değer ve modeldeki bir tane.  
  
- **Eğitim günlük kaybı** , sınıflandırıcının rastgele bir tahmine göre faydalanmasını temsil eden tek bir puandır. Günlük kayıp, çıktılarının çıkış yaptığı olasılıkların, etiketlerde bilinen değerler (zemin dışı) ile karşılaştırılmasıyla, modelinizin unkliğini ölçer. Modelin günlük kaybını bir bütün olarak en aza indirmek istiyorsunuz.

##  <a name="bkmk_regression"></a>Regresyon modelleriyle ilgili ölçümler
 
Regresyon modelleri için döndürülen ölçümler genellikle hata miktarını tahmin etmek için tasarlanmıştır.  Gözlemlenen ve tahmin edilen değerler arasındaki fark küçük olduğunda, bir model veriye uyacak şekilde değerlendirilir. Ancak, fazlalıklar (tahmin edilen herhangi bir nokta ve ilgili gerçek değer arasındaki fark), modelde potansiyel sapma hakkında çok daha fazla bilgi verebilir.  
  
 Gerileme modellerini değerlendirmek için aşağıdaki ölçümler raporlanır. Modelleri karşılaştırdığınızda, değerlendirme için seçtiğiniz ölçüm tarafından derecelendirilir.  
  
- **Ortalama mutlak hata (MAE)** , tahminleri gerçek sonuçlar için nasıl kapamaları gerektiğini ölçer; Bu nedenle, daha düşük bir puan daha iyidir.  
  
- **Kök ortalama kare hatası (rmo)** modeldeki hatayı özetleyen tek bir değer oluşturur. Fark karelerini alıp, ölçüm, daha fazla tahmin ve tahmin düzeyi arasındaki farkı dikkate vermez.  
  
- **Göreli mutlak hata (rampası)** , beklenen ve gerçek değerler arasındaki göreli mutlak farktır; göreli olarak, ortalama farkı aritmetik ortalama tarafından bölünür.  
  
- **Göreli kare hatası (RSE)** , gerçek değerlerin toplam kare şeklinde hataya göre öngörülen değerlerin toplam kare halinde hata sayısını normalleştirir.  
  
- **Ortalama sıfır bir hata (MZOE)** , tahmine uygun olup olmadığını gösterir.  Diğer bir deyişle: `x!=y``ZeroOneLoss(x,y) = 1`; Aksi takdirde `0`.
  
- Genellikle R<sup>2</sup>olarak anılan **belirleme katsayısı**, modelin tahmine dayalı gücünü 0 ile 1 arasında bir değer olarak temsil eder. Sıfır, modelin rastgele olduğu anlamına gelir (hiçbir şey açıklar); 1, mükemmel bir uyum olduğu anlamına gelir. Ancak, düşük değerler tamamen normal olabilir ve yüksek değerler şüpheli olduğundan, R<sup>2</sup> değerlerini yorumlamak için dikkatli bir değer kullanılmalıdır.
  

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 