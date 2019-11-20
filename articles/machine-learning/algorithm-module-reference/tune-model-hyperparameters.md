---
title: Model Hiper Parametrelerini Ayarlama
titleSuffix: Azure Machine Learning service
description: En uygun parametre ayarlarını belirleyebilmek üzere bir modelde bir parametre tarama işlemi gerçekleştirmek için Azure Machine Learning hizmetinde ayarlama modeli hiper parametreleri modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 834907528e4062de583197cf91e98372b96451d7
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131230"
---
# <a name="tune-model-hyperparameters"></a>Model Hiper Parametrelerini Ayarlama

Bu makalede, Azure Machine Learning tasarımcısında ayarlama modeli hiper parametreleri modülünün nasıl kullanılacağı açıklanmaktadır (Önizleme). Amaç, bir makine öğrenimi modeli için en uygun hiper parametreleri belirlemektir. Modül farklı ayarların birleşimlerini kullanarak birden çok modeli oluşturur ve sınar. Ayarların birleşimlerini almak için ölçümleri tüm modeller üzerinde karşılaştırır. 

Terms *parametresi* ve *hyperparameter* kafa karıştırıcı olabilir. Modelin *parametreleri* Özellikler bölmesinde ayarladığınız şeydir. Temelde, bu modül belirtilen parametre ayarları üzerinde bir *parametre süpürme* gerçekleştirir. Her bir karar ağacı, veri kümesi veya regresyon yöntemi için farklı olabilecek, en uygun _hiper parametre_kümesini öğrenir. En iyi yapılandırmayı bulma işlemi bazen *ayarlama*olarak adlandırılır. 

Modül, bir modelin en iyi ayarlarını bulmak için aşağıdaki yöntemi destekler: *Tümleşik tren ve ayarlama.* Bu yöntemde, kullanılacak bir parametre kümesi yapılandırırsınız. Daha sonra modülün birden çok kombinasyonu üzerinde yineleme yapmasına izin verin. Modül, "en iyi" modeli bulana kadar doğruluğu ölçer. En öğrenner modülleri sayesinde, eğitim süreci sırasında hangi parametrelerin değiştirilmesi gerektiğini ve ne sabit kalması gerektiğini seçebilirsiniz.

Ayarlama işleminin ne kadar süreyle çalışmasını istediğinize bağlı olarak, tüm birleşimleri tamamen tüketireceğinize karar verebilirsiniz. Ya da parametre kılavuzunun bir kılavuzunu oluşturarak ve parametre kılavuzunun rastgele bir alt kümesini test ederek süreci kısaltabilirsiniz.

Bu yöntem, yeniden kullanım için kaydedebilmeniz gereken eğitilen bir model oluşturur.  

> [!TIP] 
> İlgili bir görevi yapabilirsiniz. Ayarlamayı başlamadan önce, en yüksek bilgi değerine sahip sütunları veya değişkenleri belirlemekte özellik seçimini uygulayın.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Ayarlama modeli hiper parametrelerini yapılandırma  

Bir makine öğrenimi modeli için en uygun hiper parametreleri öğrenirken işlem hatları önemli ölçüde kullanılır.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Parametre süpürme kullanarak model eğitme  

Bu bölümde, model hiper parametreleri ayarla modülünü kullanarak bir modeli gösteren temel bir parametre süpürme gerçekleştirme açıklanmaktadır.

1.  Tasarımcı 'daki işlem hattınızı ayarlama modeli hiper parametreleri modülünü ekleyin.

2.  Eğitimli olmayan bir modeli en soldaki girişe bağlayın. 

3. **Parametre aralığına**bir **çöp modu oluştur** seçeneğini ayarlayın. Parametre süpür'da kullanılacak bir değer aralığı belirtmek için **Aralık Oluşturucu** kullanın.  

    Neredeyse tüm sınıflandırma ve regresyon modülleri tümleşik bir parametre süpürmesini destekler. Bir parametre aralığı yapılandırmayı desteklemeyen öğrenenler için, yalnızca kullanılabilir parametre değerlerini test edebilirsiniz.

    Bir veya daha fazla parametre için değeri el ile ayarlayabilir ve ardından kalan parametrelerin üzerinde tarama yapabilirsiniz. Bu işlem biraz zaman kazandırabilir.

4.  Eğitim için kullanmak istediğiniz veri kümesini ekleyin ve bunu ayarlama modeli hiper parametrelerinin orta girişine bağlayın.  

    İsteğe bağlı olarak, etiketli bir veri kümeniz varsa, bunu en sağdaki giriş bağlantı noktasına (**Isteğe bağlı doğrulama veri kümesi**) bağlayabilirsiniz. Bu, eğitim ve ayarlama sırasında doğruluğu ölçmenize olanak tanır.

5.  Model hiper parametrelerinin ayarla ' nın **Özellikler** bölmesinde, **parametre swemodu**için bir değer seçin. Bu seçenek parametrelerin nasıl seçili olduğunu denetler.

    - **Tüm ızgara**: Bu seçeneği belirlediğinizde, modül, sistem tarafından önceden tanımlanmış bir kılavuz üzerinde döngü gerçekleştirerek farklı birleşimler deneyebilir ve en iyi öğrenme kimliğini belirler. Bu seçenek, en iyi parametre ayarlarının ne olabileceğini bilmiyorsanız ve tüm olası değer birleşimlerini denemek istediğinizde faydalıdır.

    - **Rastgele tarama**: Bu seçeneği belirlediğinizde modül, sistem tarafından tanımlanan bir aralıktaki parametre değerlerini rastgele seçer. Modülün yürütmesini istediğiniz en fazla çalıştırma sayısını belirtmeniz gerekir. Bu seçenek, tercih ettiğiniz ölçümleri kullanarak model performansını artırmak, ancak bilgi işlem kaynaklarını korumak istediğinizde yararlıdır.    

6.  **Etiket sütunu**için, tek bir etiket sütunu seçmek üzere sütun seçiciyi açın.

7.  Çalıştırma sayısını seçin:

    1. **Rastgele tarama sırasında en fazla çalışma sayısı**: rastgele bir tarama seçerseniz, parametre değerlerinin rastgele bir birleşimini kullanarak modelin kaç kez eğitilmek gerektiğini belirtebilirsiniz.

    2. **Rastgele kılavuzda en fazla çalışma sayısı**: Bu seçenek ayrıca parametre değerlerinin rastgele bir örneklemede yineleme sayısını denetler, ancak değerler belirtilen aralıktan rastgele oluşturulmaz. Bunun yerine, modül tüm olası parametre değerleri birleşimlerinin bir matrisini oluşturur. Ardından, matris üzerinde rastgele bir örnekleme gerçekleştirir. Bu yöntem, bölgesel fazla örnekleme veya yetersiz örnekleme konusunda daha etkilidir ve daha az açıktır.

8.  **Sıralama**için, modelleri derecelendirerek kullanılacak tek bir ölçüm seçin.

    Bir parametre süpürme çalıştırdığınızda modül, model türü için geçerli tüm ölçümleri hesaplar ve bunları **tarama sonuçları** raporunda döndürür. Modül, regresyon ve sınıflandırma modelleri için ayrı ölçümler kullanır.

    Ancak, seçtiğiniz ölçüm modellerin derecelendirme şeklini belirler. Yalnızca, seçili ölçüm tarafından derecelendirilen en üst model, Puanlama için kullanılacak eğitilen bir model olarak çıktı.

9.  **Rastgele çekirdek**için, parametre süpürme başlatılırken kullanılacak bir sayı girin. 

10. İşlem hattını çalıştırma.

## <a name="results-of-hyperparameter-tuning"></a>Hiper parametre ayarlama sonuçları

Eğitim tamamlandığında:

+ En iyi modelin doğruluk ölçümleri kümesini görüntülemek için modüle sağ tıklayın, **tarama sonuçları**' nı seçin ve ardından **Görselleştir**' i seçin.

    Çıktı, model türü için uygulanan tüm doğruluk ölçümlerini içerir, ancak sıralama için seçtiğiniz ölçüm hangi modelin "en iyi" kabul edileceğini belirler.

+ Ayarlama işlemini yinelemek zorunda kalmadan, Puanlama için modeli başka işlem hatlarında kullanmak için model çıktısına sağ tıklayın ve **eğitilen model olarak kaydet**' i seçin. 


## <a name="technical-notes"></a>Teknik notlar

Bu bölüm uygulama ayrıntılarını ve ipuçlarını içerir.

### <a name="how-a-parameter-sweep-works"></a>Bir parametre süpürme nasıl kullanılır

Bir parametre süpürme ayarladığınızda, aramanızın kapsamını tanımlarsınız. Arama, rastgele seçilmiş sınırlı sayıda parametre kullanabilir. Ya da tanımladığınız bir parametre alanı üzerinde kapsamlı bir arama olabilir.

+ **Rastgele tarama**: Bu seçenek, bir dizi yinelemenin sayısını kullanarak bir modeli ister. 

  Yinelemek için bir Aralık değeri belirtirsiniz ve modül bu değerlerin rastgele seçilmiş bir alt kümesini kullanır. Değerler değiştirme ile seçilir, yani önceden rastgele seçilmiş olan sayıların kullanılabilir sayı havuzundan kaldırılmadığı anlamına gelir. Bu nedenle, seçilen herhangi bir değer tüm geçişlerde aynı kalır.  

+ **Tüm ızgara**: kılavuzun tamamını kullanma seçeneği, her birleşimin test edileceği anlamına gelir. Bu seçenek en kapsamlı seçenektir, ancak en çok bir süre gerektirir. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Eğitimin uzunluğunu ve karmaşıklığını denetleme

Birçok ayar kombinasyonu üzerinde yineleme zaman alabilir, bu nedenle modül işlemi kısıtlamak için çeşitli yollar sağlar:

+ Bir modeli test etmek için kullanılan yineleme sayısını sınırlayın.
+ Parametre alanını sınırlandırın.
+ Hem yineleme sayısını hem de parametre alanını sınırlandırın.

Belirli bir veri kümesi ve model için en verimli eğitim yöntemini belirleme ayarlarına sahip işlem hattı yapmanızı öneririz.

### <a name="choosing-an-evaluation-metric"></a>Değerlendirme ölçümü seçme

Testin sonunda, model, ölçüm sonuçlarını gözden geçirebilmeniz için her modelin doğruluğunu içeren bir rapor sunar:

- Tüm ikili sınıflandırma modelleri için tek bir ölçüm kümesi kullanılır.
- Doğruluk, tüm çok sınıf sınıflandırma modelleri için kullanılır.
- Regresyon modelleri için farklı bir ölçüm kümesi kullanılır. 

Ancak eğitim sırasında, ayarlama işlemi sırasında oluşturulan modelleri derecelendirerek kullanılacak *tek* bir ölçüm seçmeniz gerekir. En iyi ölçümün iş sorununuza ve hatalı pozitif sonuçlar ve yanlış negatifler maliyetlerine bağlı olarak değiştiğini fark edebilirsiniz.

#### <a name="metrics-used-for-binary-classification"></a>İkili sınıflandırma için kullanılan ölçümler

-   **Doğruluk** , toplam durum için gerçek sonuçların orandır.  

-   **Duyarlık** , pozitif sonuçlara yönelik doğru sonuçların orandır.  

-   **Hatırlayın** , tüm sonuçlar üzerinde tüm doğru sonuçların kesiri olur.  

-   **F puanı** duyarlık ve geri çekmeyi dengeleyen bir ölçüdür.  

-   **AUC** , x ekseninde yanlış pozitif değerler çizilse ve y ekseninde doğru pozitif değerler çizilse de eğri altındaki alanı temsil eden bir değerdir.  

-   **Ortalama günlük kaybı** iki olasılık dağıtımı arasındaki farktır: gerçek bir, ve modeldeki bir.  

#### <a name="metrics-used-for-regression"></a>Gerileme için kullanılan ölçümler

-   **Mutlak ortalama hata** modeldeki tüm hataların ortalamasını alır, burada *hata* tahmin edilen değerin gerçek değerden uzaklığı anlamına gelir. Genellikle *Mae*olarak kısaltılır.  

-   **Ortalanan kare hatasının kökü** , hataların karelerinin ortalamasını ölçer ve bu değerin kökünü alır. Genellikle *rmo*olarak kısaltılır.  

-   **Göreli mutlak hata** , hatayı doğru değerin yüzdesi olarak gösterir.  

-   **Göreli kare** oluşan hata, tahmin edilen değerlerin toplam kare dışı hatası ile ayırarak toplam kare içinde hatayı normalleştirir.  

-   **Belirleme katsayısı** , verilerin modele ne kadar iyi uyduğunu gösteren tek bir sayıdır. Bir değeri, modelin verilerle tam olarak eşleştiği anlamına gelir. Sıfır değeri, verilerin rastgele olması veya başka türlü modele sığamayacak olması anlamına gelir. Genellikle *r<sup>2</sup>* , *r<sup>2</sup>* veya *r-kare*olarak adlandırılır.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Parametre süpürme desteği olmayan modüller

Azure Machine Learning neredeyse tüm öğrenenler, tümleşik bir parametre süpürme ile çapraz doğrulamayı destekler, bu da işlem hattının parametrelerini seçmenizi sağlar. Learner, bir değer aralığı ayarlamayı desteklemiyorsa, bunu çapraz doğrulamada kullanmaya devam edebilirsiniz. Bu durumda, tarama için izin verilen değerler aralığı seçilidir. 


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 

