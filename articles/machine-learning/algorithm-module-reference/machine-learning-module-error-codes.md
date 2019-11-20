---
title: Modül hatalarında sorun giderme
titleSuffix: Azure Machine Learning
description: Hata kodlarını kullanarak Azure Machine Learning Studio modül özel durumlarının sorunlarını giderme
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b07b1fc14ac7ac72a30f75a098b4aab35f2de713
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497733"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Algoritma & modül başvurusu için özel durumlar ve hata kodları

Azure Machine Learning tasarımcısında modüller kullanarak karşılaşabileceğiniz hata iletileri ve özel durum kodları hakkında bilgi edinin (Önizleme). 

Bu sorunu çözmek için, bu makaledeki hata olup olmadığına bakarak yaygın nedenler hakkında bilgi edinin. Tasarımcıda bir hata iletisinin tam metnini almanın iki yolu vardır:  
 
- Sağ bölmedeki bağlantıyı tıklatın, **Çıkış günlüğünü görüntüleyin**ve en alta kaydırın. Ayrıntılı hata iletisi pencerenin son iki satırı içinde görüntülenir.  
  
- Hatayı içeren modülü seçin ve kırmızı X ' e tıklayın. Yalnızca ilgili hata metni görüntülenir.  
  
Hata iletisi metni yararlı değilse, geri bildirimde bulunarak bağlam ve istenen eklemeler ya da değişiklikler hakkında bize bilgi gönderin.


## <a name="error-0001"></a>Hata 0001  
 Belirli bir veya daha fazla veri kümesi sütunu bulunamazsa özel durum oluşur.  
  
 Bu hatayı bir modül için bir sütun seçimi yapılırsa, ancak seçili sütunlar giriş veri kümesinde yoksa alırsınız. Bu hata, bir sütun adına el ile girdiğiniz veya sütun seçici, işlem hattını çalıştırdığınızda veri kümeniz içinde olmayan önerilen bir sütun sağladıysa meydana gelebilir.  
  
**Çözüm:** Bu özel durumu oluşturan modülün yeniden ziyaret edin ve sütun adının veya adlarının doğru olduğunu ve başvurulan tüm sütunların mevcut olduğunu doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Belirtilen bir veya daha fazla sütun bulunamadı|  
|"{0}" adına veya dizinine sahip sütun bulunamadı|  
|"{0}" adına veya dizinine sahip sütun "{1}" içinde yok|  
 

## <a name="error-0002"></a>Hata 0,0002  
 Bir veya daha fazla parametre ayrıştırılamadıysanız veya belirtilen türden hedef Yöntem türüne göre gerekli bir şekilde dönüştürülemiyorsa özel durum oluşur.  
  
 Bu hata, giriş olarak bir parametre belirttiğinizde ve değer türü beklenen türden farklıysa ve örtük dönüştürme gerçekleştirilemediği zaman Azure Machine Learning oluşur.  
  
**Çözüm:** Modül gereksinimlerini denetleyin ve hangi değer türünün gerekli olduğunu (dize, tamsayı, Çift, vb.) saptayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Parametre ayrıştırılamadı|  
|"{0}" parametresi ayrıştırılamadı|  
|"{0}" parametresi "{1}" olarak ayrıştırılamadı|  
|"{1}" olan "{0}" parametresi "{2}" olarak dönüştürülemedi|  
|"{1}" "{0}" parametre değeri "{2}" iken "{3}" olarak dönüştürülemedi|  
|"{1}" sütunundaki "{0}" değeri "{2}", "{4}" biçiminin kullanımı ile "{3}" olarak dönüştürülemedi|  
  

## <a name="error-0003"></a>Hata 0003  
 Bir veya daha fazla giriş null veya boş olduğunda özel durum oluşur.  
  
 Bu hatayı, bir modüle yönelik herhangi bir giriş veya parametre null ya da boş olduğunda Azure Machine Learning alırsınız.  Bu hata, örneğin, bir parametre için herhangi bir değer yazmadınız meydana gelebilir. Ayrıca, eksik değerlere veya boş bir veri kümesine sahip bir veri kümesi seçerseniz da bu durum oluşabilir.  
  
**Çözünürlüğüne**
 
+ Özel durumu üreten modülü açın ve tüm girişlerin belirtildiğini doğrulayın. Tüm gerekli girişlerin belirtildiğinden emin olun. 
+ Azure depolama 'dan yüklenen verilerin erişilebilir olduğundan ve hesap adının veya anahtarın değişmediğinden emin olun.  
+ Eksik değerler veya null değerleri için giriş verilerini denetleyin.
+ Veri kaynağında bir sorgu kullanılıyorsa, verilerin beklediğinizi biçimde döndürüldüğünden emin olun. 
+ Veri belirtimindeki yazım hatalarını veya diğer değişiklikleri denetleyin.
  
|Özel durum Iletileri|  
|------------------------|  
|Bir veya daha fazla giriş null veya boş|  
|"{0}" girişi null veya boş|  
  

## <a name="error-0004"></a>Hata 0,0004  
 Parametre belirli bir değerden küçük veya ona eşitse özel durum oluşur.  
  
 İletideki parametre, modülün verileri işlemesi için gereken bir sınır değerinin altındaysa, bu hatayı Azure Machine Learning alırsınız.  
  
**Çözüm:** Özel durumu oluşturan modülün yeniden ziyaret edin ve parametreyi belirtilen değerden daha büyük olacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Parametre sınır değerinden büyük olmalıdır.|  
|"{0}" parametresinin değeri {1}daha büyük olmalıdır.|  
|"{0}" parametresi, {2} daha büyük olması gereken "{1}" değerine sahip|  
  


## <a name="error-0005"></a>Hata 0005  
 Parametre belirli bir değerden küçükse özel durum oluşur.  
  
 İletideki parametre, modülün verileri işlemesi için gereken bir sınır değerine eşitse veya eşitse, bu hatayı Azure Machine Learning alırsınız.  
  
**Çözüm:** Özel durumu oluşturan modülü yeniden ziyaret edin ve parametreyi belirtilen değere eşit veya ondan büyük olacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Parametre, sınır değerinden büyük veya buna eşit olmalıdır.|  
|"{0}" parametresinin değeri {1}eşit veya daha büyük olmalıdır.|  
|"{0}" parametresi, {2}büyük veya eşit olması gereken "{1}" değerine sahip.|  
  

## <a name="error-0006"></a>Hata 0006  
 Parametre belirtilen değerden büyük veya bu değere eşitse özel durum oluşur.  
  
 İletideki parametre, modülün verileri işlemesi için gereken sınır değerinden büyük veya bu değere eşitse Azure Machine Learning bu hatayı alırsınız.  
  
**Çözüm:** Özel durumu oluşturan modülün yeniden ziyaret edin ve parametreyi belirtilen değerden daha az olacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Parametre uyumsuzluğu. Parametrelerden biri başka bir değerden küçük olmalıdır.|  
|"{0}" parametresinin değeri "{1}" değerinden küçük olmalıdır.|  
|"{0}" parametresi, {2}daha az olması gereken "{1}" değerine sahip.|  
  

## <a name="error-0007"></a>Hata 0007  
 Parametre belirli bir değerden daha büyükse özel durum oluşur.  
  
 Bu hatayı, modülün özelliklerinde izin verilenden daha büyük bir değer belirtenden Azure Machine Learning alırsınız. Örneğin, desteklenen tarihler aralığının dışında bir veri belirtebilir veya yalnızca üç sütun kullanılabilir olduğunda beş sütun kullanıldığını gösterebilirsiniz. 
 
 Bu hatayı, bir şekilde eşleşmesi gereken iki veri kümesi belirtiyorsanız de görebilirsiniz. Örneğin, sütunları yeniden adlandırıyorsanız ve sütunları dizine göre belirtirseniz, sağladığınız adların sayısı sütun dizini sayısıyla eşleşmelidir. Diğer bir örnek, iki sütun kullanan bir matematik işlemi olabilir, burada sütunlar aynı sayıda satıra sahip olmalıdır. 
  
**Çözünürlüğüne**
 
 + Söz konusu modülü açın ve tüm sayısal özellik ayarlarını gözden geçirin.
 + Herhangi bir parametre değerinin, bu özellik için desteklenen değer aralığı içinde olduğundan emin olun.
 + Modül birden çok giriş alırsa, girişlerin aynı boyutta olduğundan emin olun.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Veri kümesinin veya veri kaynağının değiştirilip değiştirilmediğini denetleyin. Bazen, verilerin önceki bir sürümüyle çalışan bir değer, sütun sayısı, sütun veri türleri veya verilerin boyutu değiştirildikten sonra başarısız olur.  
  
|Özel durum iletileri|  
|------------------------|  
|Parametre uyumsuzluğu. Parametrelerden biri başka bir değerden küçük veya buna eşit olmalıdır.|  
|"{0}" parametresi "{1}" değerinden küçük veya bu değere eşit olmalıdır.|  
|"{0}" parametresi, {2}küçük veya eşit olması gereken "{1}" değerine sahip.|  
  

## <a name="error-0008"></a>Hata 0008  
 Parametre Aralık içinde değilse özel durum oluşur.  
  
 İletideki parametre, modülün verileri işlemesi için gereken sınırların dışındaysa Azure Machine Learning bu hatayı alırsınız.  
  
 Örneğin, farklı sayıda sütuna sahip iki veri kümesini birleştirmek için [satır ekle](add-rows.md) seçeneğini kullanmayı denerseniz bu hata görüntülenir.  
  
**Çözüm:** Özel durumu oluşturan modülün yeniden ziyaret edin ve parametreyi belirtilen aralık dahilinde olacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Parametre değeri belirtilen aralıkta değil.|  
|"{0}" parametresinin değeri Aralık içinde değil.|  
|"{0}" parametresinin değeri [{1}, {2}] aralığında olmalıdır.|  
  

## <a name="error-0009"></a>Hata 0009  
 Azure depolama hesabı adı veya kapsayıcı adı yanlış belirtildiğinde özel durum oluşur.  
  
Bu hata, bir Azure depolama hesabının parametrelerini belirttiğinizde, ancak ad veya parola çözümlenemediğinde Azure Machine Learning tasarımcısında oluşur. Parolalardan veya hesap adlarındaki hatalar birçok nedenden kaynaklanabilir:
 
 + Hesap yanlış türde. Machine Learning Designer ile kullanım için bazı yeni hesap türleri desteklenmez. Ayrıntılar için bkz. [verileri Içeri aktarma](import-data.md) .
 + Yanlış hesap adı girdiniz
 + Hesap artık yok
 + Depolama hesabının parolası yanlış veya değişmiş
 + Kapsayıcı adını belirtmediniz veya kapsayıcı yok
 + Dosya yolunu (Blobun yolu) tam olarak belirtmediniz
   
**Çözünürlüğüne**

Bu tür sorunlar genellikle hesap adını, parolayı veya kapsayıcı yolunu el ile girmeye çalıştığınızda oluşur. [Veri Içeri aktarma](import-data.md) modülü için yeni Sihirbazı kullanmanızı öneririz. Bu, adları bulmanıza ve denetlemeye yardımcı olur.

Ayrıca hesabın, kapsayıcının veya Blobun silinip silinmediğini denetleyin. Hesap adının ve parolanın doğru girildiğini ve kapsayıcının mevcut olduğunu doğrulamak için başka bir Azure depolama yardımcı programı kullanın. 

Bazı yeni hesap türleri Azure Machine Learning tarafından desteklenmez. Örneğin, yeni "sık erişimli" veya "soğuk" Depolama türleri makine öğrenimi için kullanılamaz. Klasik depolama hesapları ve "genel amaçlı" olarak oluşturulan depolama hesapları iyi çalışır.

Bir Blobun yolun tamamı belirtilmişse, yolun **kapsayıcı/blobname**olarak belirtildiğinden ve hem kapsayıcının hem de Blobun hesapta bulunduğundan emin olun.  
  
 Yol önünde eğik çizgi içermemelidir. Örneğin **/Container/blob** yanlış ve **kapsayıcı/blob**olarak girilmelidir.  

  
|Özel durum Iletileri|  
|------------------------|  
|Azure depolama hesabı adı veya kapsayıcı adı yanlış.|  
|Azure depolama hesabı adı "{0}" veya "{1}" kapsayıcı adı yanlış; kapsayıcı/blob biçim kapsayıcısı adı bekleniyordu.|  
  

## <a name="error-0010"></a>Hata 0010  
 Giriş veri kümelerinde eşleşmesi gereken ancak olmayan sütun adları varsa özel durum oluşur.  
  
 İletideki sütun dizininde iki giriş veri kümesi içinde farklı sütun adları varsa Azure Machine Learning bu hatayı alırsınız.  
  
**Çözüm:** Belirtilen sütun dizini için aynı sütun adına sahip olması için [meta verileri Düzenle](edit-metadata.md) veya özgün veri kümesini Değiştir ' i kullanın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Giriş veri kümelerinde karşılık gelen dizine sahip sütunlarda farklı adlar vardır.|  
|Sütun adları, giriş veri kümelerinin ({1} ve {2} sırasıyla) sütun {0} (sıfır tabanlı) için aynı değildir.|  
  

## <a name="error-0011"></a>Hata 0011  
 Geçirilen sütun kümesi bağımsız değişkeni herhangi bir veri kümesi sütunu için uygulanmemişse özel durum oluşur.  
  
 Belirtilen sütun seçimi verilen veri kümesindeki sütunlardan hiçbiriyle eşleşmezse bu hatayı Azure Machine Learning alırsınız.  
  
 Ayrıca, bir sütun seçmediyseniz ve modülün çalışması için en az bir sütun gerekliyse bu hatayı alabilirsiniz.  
  
**Çözüm:** Modüldeki sütun seçimini, veri kümesindeki sütunlara uygulanacak şekilde değiştirin.  
  
 Modül, etiket sütunu gibi belirli bir sütunu seçmenizi gerektiriyorsa, doğru sütunun seçildiğini doğrulayın.  
  
 Uygunsuz sütunlar seçildiyse, bunları kaldırın ve işlem hattını yeniden çalıştırın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Belirtilen sütun kümesi hiçbir veri kümesi sütunu için uygulanmıyor.|  
|Belirtilen "{0}" sütun kümesi herhangi bir veri kümesi sütunu için uygulanmıyor.|  
  

## <a name="error-0012"></a>Hata 0012  
 Geçirilen bağımsız değişkenler kümesiyle sınıf örneği oluşturuoluşturulamadığı takdirde özel durum oluşur.  
  
**Çözüm:** Bu hata Kullanıcı tarafından işlem yapılabilir değildir ve gelecek sürümde kullanım dışı olacaktır.  
  
|Özel durum Iletileri|  
|------------------------|  
|Eğitilen model, önce modeli eğitme.|  
|Eğitilen model ({0}), eğitilen modeli kullanın.|  
  

## <a name="error-0013"></a>Hata 0013  
 Modüle geçirilen Learner geçersiz bir tür ise özel durum oluşur.  
  
 Eğitilen bir model bağlı Puanlama modülüyle uyumsuz olduğunda bu hata oluşur. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->
  
**Çözünürlüğüne**

Eğitim modülü tarafından üretilen öğrenici türünü ve öğrenici için uygun Puanlama modülünü saptayın. 

Model, özelleştirilmiş eğitim modüllerinden herhangi birini kullanarak eğitilolduysa, eğitilen modeli yalnızca ilgili özelleştirilmiş Puanlama modülüne bağlayın. 


|Model türü|Eğitim modülü| Puanlama modülü|
|----|----|----|
|herhangi bir sınıflandırıcı|[Modeli eğitme](train-model.md) |[Puan modeli](score-model.md)|
|herhangi bir regresyon modeli|[Modeli eğitme](train-model.md) |[Puan modeli](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->
  
|Özel durum Iletileri|  
|------------------------|  
|Geçersiz tür öğrenici geçildi.|  
|Learner "{0}" türü geçersiz.|  


## <a name="error-0014"></a>Hata 0014  
 Sütun benzersiz değerlerinin sayısı izin verilenden fazlaysa özel durum oluşur.  
  
 Bu hata, bir sütun çok sayıda benzersiz değer içerdiğinde oluşur.  Örneğin, bir sütunun kategorik veriler olarak işleneceğini belirtirseniz, ancak sütunda işlemin tamamlanmasına izin vermek için çok sayıda benzersiz değer varsa, bu hatayı görebilirsiniz. Ayrıca, iki girişte benzersiz değer sayısı arasında uyuşmazlık varsa bu hatayı görebilirsiniz.   
  
**Çözünürlüğüne**

Hatayı oluşturan modülü açın ve giriş olarak kullanılan sütunları tespit edin. Bazı modüller için, veri kümesi girişine sağ tıklayıp, benzersiz değer sayısı ve bunların dağılımı dahil olmak üzere tek tek sütunlarda istatistik almak için **Görselleştir** ' i seçebilirsiniz.

Gruplandırma veya kategorilere ayırma için kullanmayı düşündüğünüz sütunlarda, sütunlardaki benzersiz değerlerin sayısını azaltmak için gerekli adımları uygulayın. Sütunun veri türüne bağlı olarak farklı şekillerde azaltabilirsiniz. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Senaryoünüzle eşleşen bir çözüm bulunamıyor mu? Hatayı oluşturan modülün adını ve sütunun veri türünü ve kardinalitesini içeren bu konu hakkında geri bildirim sağlayabilirsiniz. Yaygın senaryolar için daha hedeflenmiş sorun giderme adımları sağlamak üzere bu bilgileri kullanacağız.   
  
|Özel durum Iletileri|  
|------------------------|  
|Sütun benzersiz değerlerinin sayısı izin verilenden fazla.|  
|Sütundaki benzersiz değer sayısı: "{0}", {1}demet sayısını aşıyor.|  
  

## <a name="error-0015"></a>Hata 0015  
 Veritabanı bağlantısı başarısız olursa özel durum oluşur.  
  
 Yanlış bir SQL hesabı adı, parola, veritabanı sunucusu veya veritabanı adı veya veritabanı ya da sunucu sorunları nedeniyle veritabanıyla bağlantı kurulamazsa bu hatayı alırsınız.  
  
**Çözüm:** Hesap adı, parola, veritabanı sunucusu ve veritabanının doğru girildiğini ve belirtilen hesabın doğru izin düzeyine sahip olduğunu doğrulayın. Veritabanına Şu anda erişilebildiğini doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Veritabanı bağlantısı kurulurken hata oluştu.|  
|Veritabanı bağlantısı kurulurken hata oluştu: {0}.|  
  


## <a name="error-0016"></a>Hata 0016  
 Modüle geçirilen giriş veri kümelerinin uyumlu sütun türleri olması, ancak bunu olmaması durumunda özel durum oluşur.  
  
 İki veya daha fazla veri kümesine geçirilen sütunların türleri birbirleriyle uyumlu değilse, bu hatayı Azure Machine Learning alırsınız.  
  
**Çözüm:** [Meta verileri düzenleme](edit-metadata.md) veya özgün giriş veri kümesini değiştirme<!--, or use [Convert to Dataset](convert-to-dataset.md)--> sütun türlerinin uyumlu olduğundan emin olmak için.  
  
|Özel durum Iletileri|  
|------------------------|  
|Giriş veri kümelerinde karşılık gelen dizine sahip sütunlarda uyumsuz türler vardır.|  
|{0} ve {1} sütunları uyumsuzdur.|  
|Sütun öğesi türleri, giriş veri kümelerinin ({1} ve {2} sırasıyla) sütun {0} (sıfır tabanlı) için uyumlu değildir.|  
  

## <a name="error-0017"></a>Hata 0017  
 Seçili bir sütun, geçerli modül tarafından desteklenmeyen bir veri türü kullanıyorsa özel durum oluşur.  
  
 Örneğin, sütun seçiminiz, modül tarafından işlenemediği bir veri türü olan bir sütun içeriyorsa (örneğin, bir matematik işleminin dize sütunu veya bir kategorik özellik sütununun olduğu puan sütunu), bu hatayı Azure Machine Learning alabilirsiniz. Gerekli.  
  
**Çözünürlüğüne**
 1. Sorun olan sütunu belirler.
 2. Modülün gereksinimlerini gözden geçirin.
 3. Bir sütunu gereksinimlere uygun hale getirmek için değiştirin. Bir sütuna ve denediğiniz dönüştürmeye bağlı olarak, değişiklikler yapmak için aşağıdaki modüllerden birkaçını kullanmanız gerekebilir:
    + Sütunların veri türünü değiştirmek veya sütun kullanımını özellikten sayısal olarak, kategorik olmayan kategorilere ayrılmamış olarak değiştirmek için [meta verileri Düzenle](edit-metadata.md) ' yi kullanın.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Son çare olarak, özgün giriş veri kümesini değiştirmeniz gerekebilir.

> [!TIP]
> Senaryoünüzle eşleşen bir çözüm bulunamıyor mu? Hatayı oluşturan modülün adını ve sütunun veri türünü ve kardinalitesini içeren bu konu hakkında geri bildirim sağlayabilirsiniz. Yaygın senaryolar için daha hedeflenmiş sorun giderme adımları sağlamak üzere bu bilgileri kullanacağız. 
  
|Özel durum Iletileri|  
|------------------------|  
|Geçerli türdeki sütun işlenemiyor. Tür modül tarafından desteklenmiyor.|  
|{0}türünde sütun işlenemiyor. Tür modül tarafından desteklenmiyor.|  
|{0}türündeki "{1}" sütunu işlenemiyor. Tür modül tarafından desteklenmiyor.|  
|{0}türündeki "{1}" sütunu işlenemiyor. Tür modül tarafından desteklenmiyor. Parametre adı: {2}|  
  

## <a name="error-0018"></a>Hata 0018  
 Giriş veri kümesi geçerli değilse özel durum oluşur.  
  
**Çözüm:** Azure Machine Learning bu hata birçok bağlamda görünebilir, bu nedenle tek bir çözüm yoktur. Genellikle hata, bir modüle giriş olarak girilen verilerin yanlış sayıda sütuna sahip olduğunu veya veri türünün modül gereksinimleriyle eşleşmediğinden emin olduğunu gösterir. Örneğin:  
  
-   Modül bir etiket sütunu gerektiriyor, ancak hiçbir sütun etiket olarak işaretlenmemiş veya henüz bir etiket sütunu seçmediniz.  
  
-   Modül, verilerin kategorik olmasını gerektirir, ancak verileriniz sayısal olmalıdır.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Verilerin biçimi yanlış.  
  
-   İçeri aktarılan veriler geçersiz karakterler, hatalı değerler veya Aralık değerleri içeriyor.  
-   Sütun boş veya çok fazla eksik değer içeriyor.  
  
 Gereksinimleri ve verilerinizin nasıl olabileceğini öğrenmek için, veri kümesini girdi olarak kullanan modülün yardım konusunu gözden geçirin.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Özel durum Iletileri|  
|------------------------|  
|Veri kümesi geçerli değil.|  
|{0} geçersiz veri içeriyor.|  
|{0} ve {1} tutarlı bir sütun temelinde olmalıdır.|  
  

## <a name="error-0019"></a>Hata 0019  
 Sütunun sıralanmış değerleri içermesi bekleniyorsa, özel durum oluşur, ancak bunu yapmaz.  
  
 Belirtilen sütun değerleri sıra dışı ise bu hatayı Azure Machine Learning alırsınız.  
  
**Çözüm:** Giriş veri kümesini el ile değiştirerek sütun değerlerini sıralayın ve modülü yeniden çalıştırın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Sütundaki değerler sıralanmaz.|  
|"{0}" sütunundaki değerler sıralanmaz.|  
|"{1}" veri kümesinin "{0}" sütunundaki değerler sıralanmaz.|  
  

## <a name="error-0020"></a>Hata 0020  
 Modüle geçirilen bazı veri kümelerinde sütun sayısı çok küçük olduğunda özel durum oluşur.  
  
 Bu hatayı, bir modül için yeterli sütun seçilmemişse Azure Machine Learning alırsınız.  
  
**Çözüm:** Modülü yeniden ziyaret edin ve sütun seçicinin doğru sayıda sütun seçildiğinden emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Giriş veri kümesindeki sütun sayısı izin verilen en küçük değerden küçük.|  
|Giriş veri kümesindeki sütun sayısı, izin verilen en az {0} sütundan daha az.|  
|"{0}" giriş veri kümesindeki sütun sayısı izin verilen en az {1} sütundan az.|

## <a name="error-0021"></a>Hata 0021  
 Modüle geçirilen bazı veri kümelerinde satır sayısı çok küçük olduğunda özel durum oluşur.  
  
 Bu hata, veri kümesinde belirtilen işlemi gerçekleştirmek için yeterli miktarda satır olmadığında Azure Machine Learning görülür. Örneğin, giriş veri kümesi boşsa veya en az sayıda satırın geçerli olması gereken bir işlem gerçekleştirmeye çalışıyorsanız bu hatayı görebilirsiniz. Bu gibi işlemler, istatistiksel yöntemlere, belirli bir tür bindürme ve saymayla öğrenlerine göre gruplandırma veya sınıflandırmayı içerebilir (ancak bunlarla sınırlı değildir).  
  
**Çözünürlüğüne**
 
 + Hatayı döndüren modülü açın ve giriş veri kümesini ve modül özelliklerini denetleyin. 
 + Giriş veri kümesinin boş olmadığını ve modül yardımı 'nda açıklanan gereksinimleri karşılayacak yeterli veri satırı olduğunu doğrulayın.  
 + Verileriniz bir dış kaynaktan yüklenirse, veri kaynağının kullanılabilir olduğundan ve veri tanımında içeri aktarma işleminin daha az satır almasını sağlayan bir hata veya değişiklik olmadığından emin olun.
 + Modülün veri türünü veya Temizleme, bölme veya ekleme işlemleri gibi değer sayısını etkileyebilecek bir işlem gerçekleştiriyorsanız, döndürülen satır sayısını öğrenmek için bu işlemlerin çıkışlarını kontrol edin.  



## <a name="error-0022"></a>Hata 0022  
 Giriş veri kümesindeki Seçili sütunların sayısı beklenen sayıya eşit değilse özel durum oluşur.  
  
 Azure Machine Learning içinde bu hata, aşağı akış modülü veya işlemi belirli sayıda sütun veya giriş gerektirdiğinde ve çok az veya çok fazla sayıda sütun veya giriş sağladıysanız oluşabilir. Örneğin:  
  
-   Tek etiketli bir sütun veya anahtar sütunu ve yanlışlıkla birden çok sütun seçtiniz.  
  
-   Sütunları yeniden adlandırdınız, ancak sütundan daha fazla veya daha az ad sağladınız.  
  
-   Kaynak veya hedefteki sütun sayısı değişmiş veya modül tarafından kullanılan sütun sayısıyla eşleşmiyor.  
  
-   Girişler için virgülle ayrılmış bir değerler listesi sağladınız, ancak değer sayısı eşleşmiyor veya birden çok giriş desteklenmiyor.  
  
**Çözüm:** Doğru sayıda sütun seçildiğinden emin olmak için modülü yeniden ziyaret edin ve sütun seçimini denetleyin. Yukarı akış modüllerinin çıkışlarını ve aşağı akış işlemlerinin gereksinimlerini doğrulayın.  
  
 Birden çok sütun (sütun dizini, tüm özellikler, tüm sayısal öğeler vb.) seçebileceğiniz sütun seçim seçeneklerinden birini kullandıysanız, seçimin döndürdüğü sütunların tam sayısını doğrulayın.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Yukarı akış sütunlarının sayısının veya türünün değiştirilmediğini doğrulayın.  
  
 Bir modeli eğitme için bir öneri veri kümesi kullanıyorsanız, öneren Kullanıcı öğesi çiftlerine veya Kullanıcı-öğe derecelendirerleriyle ilgili olarak sınırlı sayıda sütun beklediğini unutmayın. Model eğitimi yapmadan veya öneri veri kümelerini bölmeden önce ek sütunları kaldırın. Daha fazla bilgi için bkz. [verileri bölme](split-data.md).  
  
|Özel durum Iletileri|  
|------------------------|  
|Giriş veri kümesindeki Seçili sütunların sayısı beklenen sayıya eşit değil.|  
|Giriş veri kümesindeki Seçili sütunların sayısı {0}eşit değil.|  
|"{0}" sütun seçim deseninin, giriş veri kümesinde {1}eşit değildir.|  
|"{0}" sütun seçim deseninin giriş veri kümesinde seçilmiş {1} sütun (ler) sağlaması bekleniyordu, ancak {2} sütunları/öğeleri sağlanmalıdır.|  



## <a name="error-0023"></a>Hata 0023  
 Giriş veri kümesinin hedef sütunu geçerli eğitmen modülü için geçerli değilse özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, hedef sütun (modül parametrelerinde seçildiği gibi) geçerli veri türünde değilse, eksik tüm değerler içermediğinde veya beklendiği gibi kategorilendiğinde oluşur.  
  
**Çözüm:** Etiket/hedef sütununun içeriğini incelemek için modül girişini yeniden ziyaret edin. Tüm eksik değerleri içermediğinden emin olun. Modül hedef sütunun kategorik olmasını bekliyorsanız, hedef sütununda birden fazla farklı değer bulunduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Giriş veri kümesinde desteklenmeyen hedef sütun yok.|  
|Giriş veri kümesinde desteklenmeyen "{0}" hedef sütunu yok.|  
|Giriş veri kümesinde, {1}türünde Learner için desteklenmeyen "{0}" hedef sütunu yok.|  
 

## <a name="error-0024"></a>Hata 0024  
DataSet bir etiket sütunu içermiyorsa özel durum oluşur.  

 Azure Machine Learning bu hata, modül bir etiket sütunu gerektirdiğinde ve DataSet 'in bir etiket sütunu olmadığında oluşur. Örneğin, bir puanlanmış veri kümesinin değerlendirilmesi genellikle işlem doğruluğu ölçümlerinin bir etiket sütununun olmasını gerektirir.  
 
Ayrıca, bir etiket sütununun veri kümesinde mevcut olması, ancak Azure Machine Learning tarafından doğru algılanmadığını da ortaya çıkabilir.
  
**Çözünürlüğüne**

+ Hatayı oluşturan modülü açın ve bir etiket sütununun mevcut olup olmadığını saptayın. Sütunun adı veya veri türü, tahmin etmeye çalıştığınız tek bir sonuç (veya bağımlı değişken) içerdiği sürece önemi yoktur. Hangi sütunun etikete sahip olduğundan emin değilseniz, *Class* veya *target*gibi genel bir adı arayın. 
+  Veri kümesi bir etiket sütunu içermiyorsa, etiket sütununun açık olarak veya yanlışlıkla yukarı akış kaldırılmış olması mümkündür. Ayrıca, veri kümesi bir yukarı akış Puanlama modülünün çıkışı değildir.
+ Sütunu etiket sütunu olarak açıkça işaretlemek için, [meta veri düzenleme](edit-metadata.md) modülünü ekleyin ve veri kümesini bağlayın. Yalnızca etiket sütununu seçin ve **alanlar** açılan listesinden **etiket** ' i seçin. 
+ Etiket olarak yanlış sütun seçilirse, sütundaki meta verileri düzeltebilmeniz için alanlardan **etiketi temizle** ' yi seçebilirsiniz. 
  
|Özel durum Iletileri|  
|------------------------|  
|Veri kümesinde etiket sütunu yok.|  
|"{0}" içinde etiket sütunu yok.|  
  

## <a name="error-0025"></a>Hata 0025  
 Veri kümesinde bir puan sütunu yoksa özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, değerlendir modeli girişi geçerli puan sütunları içermiyorsa oluşur. Örneğin, Kullanıcı, doğru eğitilen bir modelle puanlanmadan önce bir veri kümesini değerlendirmeye çalışır veya puan sütunu açıkça yukarı akış olarak bırakılmıştı. Bu özel durum, iki veri kümelerinde puan sütunları uyumsuzsa da oluşur. Örneğin, doğrusal bir gerileme algılayıcısı 'nın doğruluğunu bir ikili sınıflandırıcıdan karşılaştırmaya çalışıyor olabilirsiniz.  
  
**Çözüm:** Girdiyi değerlendir modeline yeniden ziyaret edin ve bir veya daha fazla puan sütunu içerip içermediğinden inceleyin. Aksi takdirde, veri kümesi puanlanmadı veya bir yukarı akış modülünde puan sütunları bırakılmıştı.  
  
|Özel durum Iletileri|  
|------------------------|  
|Veri kümesinde puan sütunu yok.|  
|"{0}" içinde puan sütunu yok.|  
|"{0}" içinde "{1}" tarafından üretilen puan sütunu yok. Doğru öğrenner türünü kullanarak veri kümesini puan edin.|  
  

## <a name="error-0026"></a>Hata 0026  
 Aynı ada sahip sütunlara izin verilmiyorsa özel durum oluşur.  
  
 Aynı ada sahip birden çok sütun varsa Azure Machine Learning bu hata oluşur. Bu hatayı alabileceğiniz bir yol, veri kümesinin bir başlık satırına sahip olmaması ve sütun adlarının otomatik olarak atanabileceği bir yoldur: Col0, Sütun1, vb.  
  
**Çözüm:** Sütunlar aynı ada sahip ise, giriş veri kümesi ve modülü arasına bir [veri düzenleme](edit-metadata.md) modülü ekleyin. Yeni adları **Yeni sütun adları** metin kutusuna yazarak yeniden adlandırılacak sütunları seçmek Için [meta verileri Düzenle](edit-metadata.md) ' de sütun seçiciyi kullanın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Bağımsız değişkenlerde eşit sütun adları belirtilmiştir. Modül tarafından eşit sütun adlarına izin verilmiyor.|  
|"{0}" ve "{1}" bağımsız değişkenlerine eşit sütun adlarına izin verilmez. Farklı adlar belirtin.|  
  

## <a name="error-0027"></a>Hata 0027  
 İki nesnenin aynı boyutta olması, ancak bunların olmaması durumunda özel durum oluşur.  
  
 Bu, Azure Machine Learning yaygın olarak karşılaşılan bir hatadır ve birçok koşuldan kaynaklanıyor olabilir.  
  
**Çözüm:** Belirli bir çözüm yoktur. Ancak, aşağıdakiler gibi koşulları kontrol edebilirsiniz:  
  
-   Sütunları yeniden adlandırıyorsanız her bir listenin (girdi sütunları ve yeni adların listesi) aynı sayıda öğeye sahip olduğundan emin olun.  
  
-   İki veri kümesini birleştiriyorsanız veya birleştirirken aynı şemaya sahip olduklarından emin olun.  
  
-   Birden çok sütunu olan iki veri kümesini birleştiriyorsanız, anahtar sütunlarının aynı veri türüne sahip olduğundan emin olun ve **seçimdeki yinelenen öğelere Izin ver ve sütun sırasını koru**seçeneğini belirleyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Geçirilen nesnelerin boyutu tutarsız.|  
|"{0}" boyutu "{1}" boyutuyla tutarsız.|  
  

## <a name="error-0028"></a>Hata 0028  
 Sütun kümesi yinelenen sütun adları içerdiğinde ve buna izin verilmediği durumlarda özel durum oluşur.  
  
 Azure Machine Learning bu hata, sütun adları çoğaltıldığında oluşur; Yani, benzersiz değildir.  
  
**Çözüm:** Herhangi bir sütun aynı ada sahip ise, giriş veri kümesi ve hatayı ortaya geçiren modül arasında bir [düzenleme meta verileri](edit-metadata.md) örneği ekleyin. Yeniden adlandırılacak sütunları seçmek için [meta verileri Düzenle](edit-metadata.md) ' de sütun seçiciyi kullanın ve yeni **sütun** adları metin kutusuna yeni sütun adlarını yazın. Birden çok sütunu yeniden adlandırıyorsanız, **Yeni sütun adlarına** yazdığınız değerlerin benzersiz olduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Sütun kümesi yinelenen sütun adları içeriyor.|  
|"{0}" adı yineleniyor.|  
|"{0}" adı "{1}" içinde yineleniyor.|  
  

## <a name="error-0029"></a>Hata 0029  
 Geçersiz URI geçirildiğinde özel durum oluşur.  
  
 Geçersiz URI geçirildiğinde Azure Machine Learning bu hata oluşur.  Aşağıdaki koşullardan herhangi biri doğru olduğunda bu hatayı alırsınız:, veya.  
  
-   Okuma veya yazma için Azure Blob depolama için belirtilen genel veya SAS URI 'SI bir hata içeriyor.  
  
-   SAS için zaman penceresinin süresi doldu.  
  
-   HTTP kaynağı aracılığıyla Web URL 'SI bir dosyayı veya geri döngü URI 'sini temsil eder.  
  
-   HTTP üzerinden Web URL 'SI hatalı biçimlendirilmiş bir URL içerir.  
  
-   URL, uzak kaynak tarafından çözümlenemiyor.  
  
**Çözüm:** Modülü yeniden ziyaret edin ve URI 'nin biçimini doğrulayın. Veri kaynağı HTTP aracılığıyla bir Web URL 'SI ise, hedeflenen kaynağın bir dosya ya da bir geri döngü URI 'SI (localhost) olmadığını doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Geçersiz URI geçirildi.|  
  

## <a name="error-0030"></a>Hata 0030  
 Bir dosyanın indirileceği durumlarda özel durum oluşur.  
  
 Azure Machine Learning içindeki bu özel durum, bir dosyanın indirileceği zaman oluşur. Üç (3) yeniden deneme denemesinden sonra bir HTTP kaynağından okuma girişimi başarısız olduğunda bu özel durumu alırsınız.  
  
**Çözüm:** HTTP kaynağına ait URI 'nin doğru olduğundan ve sitenin Şu anda Internet üzerinden erişilebilir olduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Dosya indirilemiyor.|  
|Dosya indirilirken hata oluştu: {0}.|  
  

## <a name="error-0031"></a>Hata 0031  
 Sütun kümesindeki sütun sayısı gerekenden küçükse özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, seçilen sütun sayısı gerekenden küçükse oluşur.  Gerekli en az sayıda sütun seçilmezse bu hatayı alırsınız.  
  
**Çözüm:** Sütun **seçiciyi**kullanarak sütun seçimine ek sütunlar ekleyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Sütun kümesindeki sütun sayısı gerekenden küçük.|  
|{0} sütun (ler) i belirtilmelidir. Belirtilen sütunların gerçek sayısı {1}.|  

## <a name="error-0032"></a>Hata 0032  
 Bağımsız değişken bir sayı değilse özel durum oluşur.  
  
 Bağımsız değişken bir Double veya NaN ise Azure Machine Learning bu hatayı alırsınız.  
  
**Çözüm:** Belirtilen bağımsız değişkeni geçerli bir değer kullanacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Bağımsız değişken bir sayı değil.|  
|"{0}" bir sayı değil.|  
  

## <a name="error-0033"></a>Hata 0033  
 Bağımsız değişken sonsuzluk ise özel durum oluşur.  
  
 Bağımsız değişken sonsuz ise Azure Machine Learning bu hata oluşur. Bağımsız değişken `double.NegativeInfinity` ya da `double.PositiveInfinity`ise bu hatayı alırsınız.  
  
**Çözüm:** Belirtilen bağımsız değişkeni geçerli bir değer olacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Bağımsız değişken sonlu olmalıdır.|  
|"{0}" sınırlı değildir.|  
  

## <a name="error-0034"></a>Hata 0034  
 Belirli bir Kullanıcı öğesi çifti için birden çok derecelendirme varsa özel durum oluşur.  
  
 Bir Kullanıcı öğesi çiftinin birden fazla derecelendirme değeri varsa Azure Machine Learning bu hata öneriye göre oluşur.  
  
**Çözüm:** Kullanıcı öğesi çiftinin yalnızca bir derecelendirme değeri içerdiğinden emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Veri kümesindeki değer (ler) için birden fazla derecelendirme var.|  
|Kullanıcı {0} için birden çok derecelendirme ve öğe derecelendirmesi tahmin verileri tablosu {1}.|  
  

## <a name="error-0035"></a>Hata 0035  
 Belirli bir kullanıcı veya öğe için hiçbir özellik sağlanmazsa özel durum oluşur.  
  
 Azure Machine Learning bu hata, Puanlama için bir öneri modeli kullanmaya çalışıyordum, ancak özellik vektörü bulunamıyor.  
  
**Çözünürlüğüne**

Matchbox öneren, öğe özellikleri veya Kullanıcı özellikleri kullanılırken karşılanması gereken belirli gereksinimlere sahiptir.  Bu hata, giriş olarak verdiğiniz bir kullanıcı veya öğe için bir özellik vektörünün eksik olduğunu gösterir.  Her Kullanıcı veya öğe için verilerde bir özellik vektörünün kullanılabilir olduğundan emin olmanız gerekir.  
  
 Örneğin, kullanıcının yaşı, konumu veya geliri gibi özellikleri kullanarak bir öneri modeli eğitiliyor, ancak şimdi eğitim sırasında görülmemiş yeni kullanıcılar için puanlar oluşturmak istiyoryseniz, bazı eşdeğer Özellikler (ör. yaş, konum) sağlamanız gerekir. gelir değerleri) yeni kullanıcılar için uygun tahminleri yapmak üzere. 
 
 Bu kullanıcılar için herhangi bir özellik yoksa, uygun özellikleri oluşturmak için özellik Mühendisliği ' nı düşünün.  Örneğin, bireysel kullanıcı yaşı veya gelir değerleriniz yoksa, bir Kullanıcı grubu için kullanılacak yaklaşık değerler oluşturabilirsiniz. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Çözüm sizin için geçerli değil mi? Bu makaleyle ilgili geri bildirimde bulunmak ve söz konusu senaryo hakkında, modül ve sütundaki satır sayısı dahil olmak üzere bir bilgi sağlamak için hoş geldiniz. Gelecekte daha ayrıntılı sorun giderme adımları sağlamak için bu bilgileri kullanacağız.
   
|Özel durum Iletileri|  
|------------------------|  
|Gerekli bir kullanıcı veya öğe için hiçbir özellik belirtilmedi.|  
|{0} için özellikler gerekli ancak sağlanmadı.|  
  

## <a name="error-0036"></a>Hata 0036  
 Belirli bir kullanıcı veya öğe için birden fazla özellik vektörü sağlanmışsa özel durum oluşur.  
  
 Bir özellik vektörü birden çok kez tanımlanmışsa Azure Machine Learning bu hata oluşur.  
  
**Çözüm:** Özellik vektörünün birden çok kez tanımlandığından emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Bir kullanıcı veya öğe için yinelenen özellik tanımı.|  
|{0}için yinelenen özellik tanımı.|  
  

## <a name="error-0037"></a>Hata 0037  
 Birden çok etiket sütunu belirtilirse ve yalnızca birine izin veriliyorsa özel durum oluşur.  
  
 Yeni etiket sütunu olarak birden fazla sütun seçilirse Azure Machine Learning bu hata oluşur. En denetimli öğrenme algoritmaları, hedef veya etiket olarak işaretlenmesi için tek bir sütun gerektirir.  
  
**Çözüm:** Yeni etiket sütunu olarak tek bir sütun seçtiğinizden emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Birden çok etiket sütunu belirtildi.|  
  

## <a name="error-0038"></a>Hata 0038  
 Beklenen öğe sayısı tam değer olmalıdır, ancak bu durum değildir.  
  
 Azure Machine Learning içindeki bu hata, beklenen öğe sayısı tam değer olmalıdır, ancak bu durum değildir.  Öğe sayısı beklenen geçerli değere eşit değilse bu hatayı alırsınız.  
  
**Çözüm:** Girişi doğru sayıda öğe olacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Öğe sayısı geçerli değil.|  
|"{0}" içindeki öğe sayısı geçerli değil.|  
|"{0}" içindeki öğe sayısı, geçerli {1} öğe sayısına eşit değil.|  
  

## <a name="error-0039"></a>Hata 0039  
 İşlem başarısız olursa özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, bir iç işlem tamamlanamıyor oluşur.  
  
**Çözüm:** Bu hata birçok koşuldan kaynaklanır ve belirli bir çözüm yoktur.  
 Aşağıdaki tabloda bu hatayla ilgili genel iletiler yer almaktadır ve bu durum, koşulun belirli bir açıklamasıyla izlenir. 
 
 Kullanılabilir ayrıntı yoksa, [geri bildirim gönderin](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) ve hatayı ve ilgili koşulları oluşturan modüller hakkında bilgi sağlayın.
  
|Özel durum Iletileri|  
|------------------------|  
|İşlem başarısız oldu.|  
|İşlem tamamlanırken hata oluştu: {0}.|  
  

## <a name="error-0040"></a>Hata 0040  
 Kullanım dışı bir modül çağrılırken özel durum oluştu.  
  
 Azure Machine Learning bu hata, kullanım dışı bir modül çağrılırken üretilir.  
  
**Çözüm:** Kullanım dışı bırakılan modülü desteklenen bir modülle değiştirin. Bunun yerine hangi modülün kullanılacağı hakkında bilgi için bkz. Modül çıkış günlüğü.  
  
|Özel durum Iletileri|  
|------------------------|  
|Kullanım dışı modüle erişme.|  
|"{0}" modülü kullanım dışıdır. Bunun yerine "{1}" modülünü kullanın.|  
 

## <a name="error-0041"></a>Hata 0041  
 Kullanım dışı bir modül çağrılırken özel durum oluştu.  
  
 Azure Machine Learning bu hata, kullanım dışı bir modül çağrılırken üretilir.  
  
**Çözüm:** Kullanım dışı bırakılan modülü desteklenen bir küme ile değiştirin. Bu bilgiler modül çıkış günlüğünde görünür olmalıdır.  
  
|Özel durum Iletileri|  
|------------------------|  
|Kullanım dışı modüle erişme.|  
|"{0}" modülü kullanım dışıdır. İstenen işlevsellik için "{1}" modüllerini kullanın.|  
 

## <a name="error-0042"></a>Hata 0042  
 Sütunu başka bir türe dönüştürmek mümkün olmadığında özel durum oluşur.  
  
 Azure Machine Learning bu hata, sütunu belirtilen türe dönüştürmek mümkün olmadığında oluşur.  Bu hata, bir modül DateTime, Text, kayan noktalı sayı veya tamsayı gibi belirli bir veri türünü gerektiriyorsa, ancak mevcut bir sütunu gerekli türe dönüştürmek mümkün değilse bu hatayı alırsınız.  
 
Örneğin, bir sütun seçip bir matematik işleminde kullanılmak üzere sayısal bir veri türüne dönüştürmeyi deneyebilir ve sütun geçersiz veri içeriyorsa bu hatayı alabilirsiniz. 

Diğer bir nedenden dolayı, kayan noktalı sayılar veya çok sayıda benzersiz değer içeren bir sütunu kategorik bir sütun olarak kullanmayı denerseniz bu hatayı alabilirsiniz. 
  
**Çözünürlüğüne**

+ Hatayı oluşturan modülün yardım sayfasını açın ve veri türü gereksinimlerini doğrulayın.
+ Giriş veri kümesindeki sütunların veri türlerini gözden geçirin.
+ Bu durumda, şema-daha az veri kaynağı olarak adlandırılan verileri inceleyin.
+ Eksik değerler veya istenen veri türüne dönüştürmeyi engelleyebilen özel karakterler için veri kümesini denetleyin. 
    + Sayısal veri türleri tutarlı olmalıdır: Örneğin, tamsayılar sütunundaki kayan nokta numaralarını denetleyin.
    + Bir sayı sütunundaki metin dizelerini veya NA değerleri arayın. 
    + Boole değerleri, gerekli veri türüne bağlı olarak uygun bir temsilde dönüştürülebilir.
    + Unicode olmayan karakterler, sekme karakterleri veya denetim karakterleri için metin sütunlarını inceleyin
    + Model oluşturma hatalarından kaçınmak için DateTime verileri tutarlı olmalıdır, ancak temizlik birçok biçime kadar karmaşık olabilir. Kullanmayı düşünün <!--the [Execute R Script](execute-r-script.md) or -->Temizleme işlemini gerçekleştirmek için [Python betik modüllerini yürütün](execute-python-script.md) .  
+ Gerekirse, sütunun başarıyla dönüştürülebilmesi için giriş veri kümesindeki değerleri değiştirin. Değişiklik, atma, kesme veya yuvarlama işlemleri, aykırı değerleri eleme veya eksik değerlerin imputation içerebilir. Machine Learning 'de bazı yaygın veri dönüştürme senaryoları için aşağıdaki makalelere bakın:
    + [Eksik verileri temizle](clean-missing-data.md)
    + [Verileri Normalleştir](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Geçici olarak çözümlenmiyor veya sizin için geçerli değil misiniz? Bu makaleyle ilgili geri bildirimde bulunmak ve bu makaleye ait modül ve sütunun veri türü dahil olmak üzere senaryo hakkında bilgi sağlamak için hoş geldiniz. Gelecekte daha ayrıntılı sorun giderme adımları sağlamak için bu bilgileri kullanacağız.  
  
|Özel durum Iletileri|  
|------------------------|  
|Dönüştürmeye izin verilmiyor.|  
|{0} türündeki sütun {1}türünde bir sütuna dönüştürülemedi.|  
|{0} türündeki "{2}" sütunu {1}türünde bir sütuna dönüştürülemedi.|  
|{0} türündeki "{2}" sütunu {1}türündeki "{3}" sütununa dönüştürülemedi.|  
  

## <a name="error-0043"></a>Hata 0043  
 Öğe türü açıkça eşit olarak uygulamadığı zaman özel durum oluşur.  
  
 Azure Machine Learning bu hata kullanılmıyor ve kullanım dışı bırakılacak.  
  
**Çözüm:** Seçim.  
  
|Özel durum Iletileri|  
|------------------------|  
|Erişilebilir açık yöntem bulunamadı.|  
|{1}türündeki "{0}\\" \\sütun değerleri karşılaştırılamıyor. Erişilebilir açık yöntem bulunamadı.|  


## <a name="error-0044"></a>Hata 0044  
 Mevcut değerlerden sütun öğe türü türetilemiyor olması mümkün olmadığında özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, bir veri kümesindeki sütun veya sütunların türünü çıkarsanmak mümkün olmadığında oluşur. Bu genellikle farklı öğe türlerine sahip iki veya daha fazla veri kümesini birleştirirken oluşur. Azure Machine Learning, bilgi kaybı olmadan bir sütun veya sütundaki tüm değerleri temsil edebilecek ortak bir tür belirleyemediğini bu hatayı oluşturur.  
  
**Çözüm:** Her iki veri kümesinin da birleştirileceği belirli bir sütundaki tüm değerlerin aynı türde (sayısal, Boole, kategorik, dize, tarih, vb.) olduğundan emin olun veya aynı türe zorlanverilebilir.  
  
|Özel durum Iletileri|  
|------------------------|  
|Sütunun öğe türü türetilemiyor.|  
|"{0}" sütunu için öğe türü türetilemez--tüm öğeler null başvurulardır.|  
|"{1}" veri kümesinin "{0}" sütunu için öğe türü türetilemez--tüm öğeler null başvurulardır.|  
  

## <a name="error-0045"></a>Hata 0045  
 Kaynakta karışık öğe türleri nedeniyle bir sütun oluşturulması mümkün olmadığında özel durum oluşur.  
  
 Azure Machine Learning bu hata, birleştirilmekte olan iki veri kümesinin öğe türleri farklı olduğunda üretilir.  
  
**Çözüm:** Her iki veri kümesinin da birleştirileceği belirli bir sütundaki tüm değerlerin aynı türde olduğundan emin olun (sayısal, Boolean, kategorik, dize, tarih, vb.).  
  
|Özel durum Iletileri|  
|------------------------|  
|Karışık öğe türleri olan sütun oluşturulamıyor.|  
|Karışık öğe türlerindeki KIMLIĞI "{0}" olan sütun oluşturulamıyor: \ n\tType of Data [{1}, {0}] {2}\N\tveri türü [{3}, {0}] {4}.|  
  

## <a name="error-0046"></a>Hata 0046  
 Belirtilen yolda dizin oluşturmak mümkün olmadığında özel durum oluşur.  
  
 Azure Machine Learning bu hata, belirtilen yolda bir dizin oluşturmak mümkün olmadığında oluşur. Hive sorgusu için çıkış dizinine giden yolun herhangi bir bölümü yanlış veya erişilemez durumda olduğunda bu hatayı alırsınız.  
  
**Çözüm:** Modülü yeniden ziyaret edin ve dizin yolunun doğru biçimlendirildiğinden ve geçerli kimlik bilgileriyle erişilebilir olduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Geçerli bir çıkış dizini belirtin.|  
|Dizin: {0} oluşturulamıyor. Geçerli bir yol belirtin.|  
  

## <a name="error-0047"></a>Hata 0047  
 Modüle geçirilen bazı veri kümelerinde Özellik sütunlarının sayısı çok küçük olduğunda özel durum oluşur.  
  
 Azure Machine Learning bu hata, eğitimin giriş veri kümesi, algoritma için gereken en az sütun sayısını içermiyorsa oluşur. Genellikle veri kümesi boştur ya da yalnızca eğitim sütunları içerir.  
  
**Çözüm:** Etiket sütunundan ayrı bir veya daha fazla sütun olduğundan emin olmak için giriş veri kümesini yeniden ziyaret edin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Giriş veri kümesindeki Özellik sütunlarının sayısı izin verilen en küçük değerden küçük.|  
|Giriş veri kümesindeki Özellik sütunlarının sayısı izin verilen en az {0} sütundan daha az.|  
|"{0}" giriş veri kümesindeki Özellik sütunlarının sayısı izin verilen en az {1} sütundan az.|  
  

## <a name="error-0048"></a>Hata 0048  
 Bir dosya açmak mümkün olmadığı durumlarda özel durum oluşur.  
  
 Azure Machine Learning bu hata, bir dosyayı okuma veya yazma için açmak mümkün olmadığında oluşur. Bu nedenlerden dolayı bu hatayı alabilirsiniz:  
  
-   Kapsayıcı veya dosya (blob) yok  
  
-   Dosya veya kapsayıcının erişim düzeyi dosyaya erişmenize izin vermiyor  
  
-   Dosya okuma için çok büyük ya da yanlış biçimde  
  
**Çözüm:** Modülü ve okumaya çalıştığınız dosyayı yeniden ziyaret edin.  
  
 Kapsayıcının ve dosyanın adlarının doğru olduğundan emin olun.  
  
 Dosyaya erişim izninizin olduğunu doğrulamak için klasik Azure portalını veya bir Azure depolama aracını kullanın.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Özel durum Iletileri|  
|------------------------|  
|Dosya açılamıyor.|  
|Dosya açılırken hata oluştu: {0}.|  


## <a name="error-0049"></a>Hata 0049  
 Bir dosya ayrıştırılmaya mümkün olmadığı durumlarda özel durum oluşur.  
  
 Azure Machine Learning bu hata, bir dosyayı ayrıştırmaya mümkün olmadığında oluşur. [Verileri Içeri aktarma](import-data.md) modülünde seçilen dosya biçimi dosyanın gerçek biçimiyle eşleşmezse veya dosya tanınmayan bir karakter içeriyorsa bu hatayı alırsınız.  
  
**Çözüm:** Modülü yeniden ziyaret edin ve dosyanın biçimiyle eşleşmiyorsa dosya biçimi seçimini düzeltin. Mümkünse, geçersiz karakter içermediğinden emin olmak için dosyayı inceleyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Dosya ayrıştırılamadı.|  
|Dosya ayrıştırılırken hata oluştu: {0}.|  
  

## <a name="error-0050"></a>Hata 0050  
 Giriş ve çıkış dosyalarının aynısı olması durumunda özel durum oluşur.  
  
**Çözüm:** Azure Machine Learning bu hata kullanılmıyor ve kullanım dışı bırakılacak.  
  
|Özel durum Iletileri|  
|------------------------|  
|Giriş ve çıkış için belirtilen dosyalar aynı olamaz.|


## <a name="error-0051"></a>Hata 0051  
 Birkaç çıkış dosyası aynı olduğunda özel durum oluşur.  
  
**Çözüm:** Azure Machine Learning bu hata kullanılmıyor ve kullanım dışı bırakılacak.  
  
|Özel durum Iletileri|  
|------------------------|  
|Çıktılar için belirtilen dosyalar aynı olamaz.|


## <a name="error-0052"></a>Hata 0052  
 Azure depolama hesabı anahtarı yanlış belirtilmişse özel durum oluşur.  
  
 Azure depolama hesabına erişmek için kullanılan anahtar yanlış ise Azure Machine Learning bu hata oluşur. Örneğin, Azure depolama anahtarı kopyalanırken ve yapıştırıldığında kesilmişse veya yanlış anahtar kullanılmışsa bu hatayı görebilirsiniz.  
  
 Bir Azure depolama hesabı için anahtar alma hakkında daha fazla bilgi için bkz. [depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)oluşturma.  
  
**Çözüm:** Modülü yeniden ziyaret edin ve Azure depolama anahtarının hesap için doğru olduğunu doğrulayın; gerekirse, anahtarı yeniden klasik Azure portalından kopyalayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Azure depolama hesabı anahtarı yanlış.|  
  

## <a name="error-0053"></a>Hata 0053  
 Özel durum, Matchbox önerileri için Kullanıcı özelliği veya öğe olmadığında oluşur.  
  
 Azure Machine Learning bu hata, bir özellik vektörü bulunamadığında üretilir.  
  
**Çözüm:** Giriş veri kümesinde bir özellik vektörünün bulunduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Kullanıcı özellikleri veya/ve öğeleri gereklidir ancak sağlanmadı.|  

## <a name="error-0054"></a>Hata 0054  
 İşlemin tamamlanabilmesi için sütunda çok az sayıda farklı değer varsa özel durum oluşur.  
  
**Çözüm:** Azure Machine Learning bu hata kullanılmıyor ve kullanım dışı bırakılacak.  
  
|Özel durum Iletileri|  
|------------------------|  
|Veri, işlemi gerçekleştirmek için belirtilen sütunda çok az sayıda farklı değere sahip.|  
|Veri, işlemi gerçekleştirmek için belirtilen sütunda çok az sayıda farklı değere sahip. Gerekli en az öğe {0}.|  
|Verilerin işlemi tamamlaması için "{1}" sütununda çok az sayıda farklı değer vardır. Gerekli en az öğe {0}.|  
  

## <a name="error-0055"></a>Hata 0055  
 Kullanım dışı bir modül çağrılırken özel durum oluştu.  Azure Machine Learning bu hata, kullanım dışı bırakılmış bir modül çağırmaya çalışırsanız görüntülenir.
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Kullanım dışı modüle erişme.|  
|"{0}" modülü kullanım dışıdır.|  

## <a name="error-0056"></a>Hata 0056  
 İşlem için seçtiğiniz sütunlar gereksinimleri ihlal ederse özel durum oluşur.  
  
 Azure Machine Learning bu hata, sütunun belirli bir veri türünde olmasını gerektiren bir işlem için sütunlar seçerken oluşur. 
 
 Bu hata, sütun doğru veri türünde olduğunda da gerçekleşebilir, ancak kullandığınız modül sütunun bir özellik, etiket ya da kategorik sütun olarak işaretlenmesini gerektirir.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Çözünürlüğüne**
  
1.  Şu anda seçili olan sütunların veri türünü gözden geçirin. 

2. Seçili sütunların kategorik, etiket veya özellik sütunları olup olmadığını yoklar.  
  
3.  Veri türü veya sütun kullanımı için belirli gereksinimler olup olmadığını öğrenmek için, sütun seçimini yaptığınız modülün yardım konusunu gözden geçirin.  
  
3.  Bu işlem süresince sütun türünü değiştirmek için [meta verileri Düzenle](edit-metadata.md) ' ye kullanın. Aşağı akış işlemleri için [gerekliyse, sütun](edit-metadata.md)türünü özgün değerine geri değiştirdiğinizden emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Seçilen bir veya daha fazla sütun izin verilen bir kategoride değildi.|  
|"{0}" adlı sütun izin verilen bir kategoride değil.|  
  

## <a name="error-0057"></a>Hata 0057  
 Zaten var olan bir dosya veya blob oluşturulmaya çalışılırken özel durum oluştu.  
  
 Bu özel durum, Azure Blob depolama alanına Azure Machine Learning bir işlem hattının sonuçlarını kaydetmek için [verileri dışarı aktar](export-data.md) modülünü veya başka bir modülü kullandığınızda oluşur, ancak zaten var olan bir dosya ya da blob oluşturmayı deneyin.   
  
**Çözünürlüğüne**
 
 Bu hatayı yalnızca, daha önce **Azure Blob depolama yazma modu** özelliğini **hata**olarak ayarlarsanız alırsınız. Tasarıma göre, zaten var olan bir bloba veri kümesi yazmayı denerseniz, bu modül bir hata oluşturur.
 
 - Modül özelliklerini açın ve **Azure Blob depolama yazma modu** özelliğini **üzerine yazacak**şekilde değiştirin.
 - Alternatif olarak, farklı bir hedef Blobun veya dosyanın adını yazabilir ve mevcut olmayan bir blob belirtdiğinizden emin olabilirsiniz.  
  
|Özel durum Iletileri|  
|------------------------|  
|Dosya veya blob zaten var.|  
|"{0}" dosyası veya blobu zaten var.|  
  

## <a name="error-0058"></a>Hata 0058  
 Veri kümesi beklenen etiket sütununu içermiyorsa Azure Machine Learning bu hata oluşur.  
  
 Bu özel durum, belirtilen etiket sütunu öğrenner tarafından beklenen veriler veya veri türüyle eşleşmiyorsa ya da yanlış değerlere sahip olduğunda da gerçekleşebilir. Örneğin, bu özel durum, ikili bir sınıflandırıcının eğitiminde gerçek değerli bir etiket sütunu kullanılırken üretilir.  
  
**Çözüm:** Çözüm, kullanmakta olduğunuz öğrenme veya eğitime ve veri kümenizdeki sütunların veri türleri üzerinde değişir. İlk olarak, Machine Learning algoritmasının veya eğitim modülünün gereksinimlerini doğrulayın.  
  
 Giriş veri kümesini yeniden ziyaret edin. Etiket olarak değerlendirildiğini düşündüğünüz sütunun oluşturmakta olduğunuz model için doğru veri türüne sahip olduğunu doğrulayın.  
  
 Eksik değerler için girişleri denetleyin ve gerekirse bunları kaldırın veya değiştirin.  
  
 Gerekirse, [verileri Düzenle](edit-metadata.md) modülünü ekleyin ve etiket sütununun etiket olarak işaretlendiğinden emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Etiket sütunu beklenen şekilde değil|  
|Etiket sütunu "{0}" içinde beklendiği gibi değil.|  
|"{1}" içinde "{0}" etiket sütunu beklenmiyor.|  
  

## <a name="error-0059"></a>Hata 0059  
 Sütun seçicisinde belirtilen bir sütun dizini ayrıştırılamıyorsa özel durum oluşur.  
  
 Azure Machine Learning bu hata, sütun seçici kullanılırken belirtilen bir sütun dizini ayrıştırılamadığından oluşur.  Sütun dizini ayrıştırılmamış geçersiz bir biçimde olduğunda bu hatayı alırsınız.  
  
**Çözüm:** Sütun dizinini geçerli bir dizin değeri kullanacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Belirtilen bir veya daha fazla sütun dizini veya dizin aralığı ayrıştırılamadı.|  
|Sütun dizini veya "{0}" aralığı ayrıştırılamadı.|  
  

## <a name="error-0060"></a>Hata 0060  
 Bir sütun seçicide Aralık dışı bir sütun aralığı belirtildiğinde özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, sütun seçicide Aralık dışı bir sütun aralığı belirtildiğinde oluşur. Sütun seçicideki sütun aralığı, veri kümesindeki sütunlara karşılık gelmiyorsa bu hatayı alırsınız.  
  
**Çözüm:** Sütun seçicisindeki sütun aralığını, veri kümesindeki sütunlara uyacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Geçersiz veya Aralık dışı sütun dizini aralığı belirtildi.|  
|"{0}" sütun aralığı geçersiz veya Aralık dışında.|  
  

## <a name="error-0061"></a>Hata 0061  
 Tablodaki farklı sayıda sütuna sahip bir DataTable 'a satır eklenmeye çalışılırken özel durum oluşur.  
  
 Azure Machine Learning bu hata, veri kümesinden farklı sayıda sütuna sahip bir veri kümesine bir satır eklemeye çalıştığınızda oluşur.  Veri kümesine eklenen satırın giriş veri kümesinden farklı sayıda sütuna sahip olması durumunda bu hatayı alırsınız.  Sütun sayısı farklıysa satır veri kümesine eklenemiyor.  
  
**Çözüm:** Girdi veri kümesini, eklenen satırla aynı sayıda sütuna sahip olacak şekilde değiştirin veya eklenen satırı, veri kümesiyle aynı sayıda sütuna sahip olacak şekilde değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Tüm tablolar aynı sayıda sütuna sahip olmalıdır.|  
  

## <a name="error-0062"></a>Hata 0062  
 Farklı Learner türleriyle iki modeli karşılaştırmaya çalışırken özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, iki farklı puanlı veri kümesi için değerlendirme ölçümleri karşılaştırılabir zaman üretilemez. Bu durumda, iki puanlanmış veri kümesini oluşturmak için kullanılan modellerin verimliliğini karşılaştırmak mümkün değildir.  
  
**Çözüm:** Puanlanmış sonuçların aynı türde makine öğrenimi modeli (ikili sınıflandırma, regresyon, çok sınıf sınıflandırması, öneri, kümeleme, anomali algılama vb.) tarafından üretildiğini doğrulayın. Karşılaştırmanız gereken tüm modeller aynı Learner türüne sahip olmalıdır.  
  
|Özel durum Iletileri|  
|------------------------|  
|Tüm modeller aynı Learner türüne sahip olmalıdır.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning designer, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the pipeline to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the pipeline.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to pipelines in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Özel durum Iletileri|  
|------------------------|  
|R betiği değerlendirmesi sırasında hata oluştu.|  
|R betiği değerlendirmesi sırasında şu hata oluştu: r----------hata iletisinin----------başlangıcını {0}-----------hata iletisinin sonu-----------|  
|"{1}" R betiğinin değerlendirmesi sırasında şu hata oluştu: r----------{0} hata iletisinin----------başlangıcı----------------------hata iletisinin sonu|  
  


## <a name="error-0064"></a>Hata 0064  
 Azure depolama hesabı adı veya depolama anahtarı yanlış belirtilmişse özel durum oluşur.  
  
 Azure depolama hesabı adı veya depolama anahtarı yanlış belirtilmişse Azure Machine Learning bu hata oluşur. Depolama hesabı için yanlış hesap adı veya parola girerseniz bu hatayı alırsınız. Hesap adını veya parolayı el ile girdiğinizde bu durum oluşabilir. Hesap silinmişse da bu durum oluşabilir.  
  
**Çözüm:** Hesap adının ve parolanın doğru girildiğini ve hesabın mevcut olduğunu doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Azure depolama hesabı adı veya depolama anahtarı yanlış.|  
|Hesap adı için Azure depolama hesabı adı "{0}" veya depolama anahtarı yanlış.|  
  

## <a name="error-0065"></a>Hata 0065  
 Azure Blob adı yanlış belirtilmişse özel durum oluşur.  
  
 Azure Blob adı yanlış belirtilmişse Azure Machine Learning bu hata oluşur.  Şu durumlarda hata alırsınız:  
  
-   Blob belirtilen kapsayıcıda bulunamıyor.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Biçim Excel veya kodlama ile CSV olduğunda, [Içeri aktarma verileri](import-data.md) isteği içindeki kaynak olarak yalnızca kapsayıcı belirtildi; bir kapsayıcı içindeki tüm Blobların içeriklerinin içeriğine bu formatlarda izin verilmez.  
  
-   SAS URI 'SI geçerli bir Blobun adını içermiyor.  
  
**Çözüm:** Özel durumu oluşturan modülün yeniden ziyaret edin. Belirtilen Blobun depolama hesabındaki kapsayıcıda var olduğunu ve bu izinlerin blobu görmenizi sağlar. Kodlama biçimleriyle Excel veya CSV kullanıyorsanız, girişin **ContainerName/filename** biçiminde olduğunu doğrulayın. SAS URI 'sinin geçerli bir Blobun adını içerdiğini doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Azure Depolama Blobu yanlış.|  
|Azure Depolama Blobu adı "{0}" yanlış|  
  

## <a name="error-0066"></a>Hata 0066  
 Bir kaynak Azure Blobuna yüklenemediğinde özel durum oluşur.  
  
 Azure Machine Learning bu hata, bir kaynak Azure Blobuna yüklenemediğinde oluşur.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Her ikisi de giriş dosyasını içeren hesapla aynı Azure depolama hesabına kaydedilir.  
  
**Çözüm:** Modülü yeniden ziyaret edin. Azure hesap adının, depolama anahtarının ve kapsayıcının doğru olduğundan ve hesabın kapsayıcıya yazma izni olduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Kaynak Azure depolama 'ya yüklenemedi.|  
|"{0}" dosyası Azure depolama 'ya {1}olarak yüklenemedi.|  
  

## <a name="error-0067"></a>Hata 0067  
 Bir veri kümesinde beklenenden farklı sayıda sütun varsa özel durum oluşur.  
  
 Bir veri kümesinde beklenenden farklı sayıda sütun varsa Azure Machine Learning bu hata oluşur.  Veri kümesindeki sütun sayısı modülün yürütme sırasında beklediği sütun sayısından farklı olduğunda bu hatayı alırsınız.  
  
**Çözüm:** Giriş veri kümesini veya parametrelerini değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|DataTable 'da beklenmeyen sayıda sütun.|  
|"{0}" sütunları bekleniyordu ancak bunun yerine "{1}" sütunları bulundu.|  
  

## <a name="error-0068"></a>Hata 0068  
 Belirtilen Hive betiği doğru değilse özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, Hive QL betiğinde sözdizimi hataları varsa veya Hive yorumlayıcı sorgu veya betiği yürütürken bir hatayla karşılaştığında oluşur.  
  
**Çözünürlüğüne**

Hive 'den alınan hata iletisi normalde hata günlüğünde geri bildirilir, böylece belirli hataya göre işlem gerçekleştirebilirsiniz. 

+ Modülü açın ve hataları Sorgula ' yı inceleyin.  
+ Sorgunun, Hadoop kümenizin Hive konsolunda oturum açarak ve sorguyu çalıştırarak Azure Machine Learning dışında düzgün çalıştığını doğrulayın.  
+ Yürütülebilir deyimleri ve açıklamaları tek bir satırda karıştırın tersine, Hive betiğinizdeki açıklamaları ayrı bir satıra yerleştirmeyi deneyin.  

### <a name="resources"></a>Kaynaklar

Makine öğrenimi için Hive sorgularıyla ilgili yardım için aşağıdaki makalelere bakın:

+ [Azure Blob depolamadan Hive tabloları oluşturma ve veri yükleme](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Hive sorgularıyla tablolardaki verileri araştırma](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Hive sorguları kullanarak bir Hadoop kümesindeki veriler için özellikler oluşturma](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [SQL kullanıcıları için Hive sayfası (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Özel durum Iletileri|  
|------------------------|  
|Hive betiği yanlış.|  
|Hive betiği {0} doğru değil.|  
  

## <a name="error-0069"></a>Hata 0069  
 Belirtilen SQL betiği doğru değilse özel durum oluşur.  
  
 Bu Azure Machine Learning hatası, belirtilen SQL betiği söz dizimi sorunları içeriyorsa veya betikte belirtilen sütunlar veya tablo geçerli değilse oluşur. 
 
 SQL Altyapısı sorgu veya betiği yürütürken herhangi bir hatayla karşılaştığında bu hatayı alırsınız. SQL hata iletisi normalde hata günlüğünde geri bildirilir, böylece belirli hataya göre işlem gerçekleştirebilirsiniz.  
  
**Çözüm:** Modülü yeniden ziyaret edin ve hatalar için SQL sorgusunu inceleyin.  
  
 Sorgunun, doğrudan veritabanı sunucusunda oturum açarak ve sorguyu çalıştırarak Azure ML dışında düzgün çalıştığını doğrulayın.  
  
 Modül özel durumu tarafından bildirilen bir SQL tarafından oluşturulan ileti varsa, bildirilen hataya göre işlem yapın. Örneğin, hata iletileri bazen olası hataya özgü yönergeler içerir:
+ *Böyle bir sütun veya eksik veritabanı değil*, bir sütun adı yanlış yazmış olabileceğiniz. Sütun adının doğru olduğundan eminseniz, sütun tanımlayıcısını kapsamak için köşeli ayraç veya tırnak işaretleri kullanmayı deneyin.
+ Belirtilen anahtar sözcükten önce bir sözdizimi hatası olabileceğini belirten *\<SQL anahtar sözcük\>yakınında SQL Logic Error*

  
|Özel durum Iletileri|  
|------------------------|  
|SQL betiği yanlış.|  
|"{0}" SQL sorgusu doğru değil.|  
|"{0}" SQL sorgusu doğru değil: {1}|  
  

## <a name="error-0070"></a>Hata 0070  
 Mevcut olmayan Azure tablosuna erişmeye çalışırken özel durum oluştu.  
  
 Mevcut olmayan bir Azure tablosuna erişmeye çalıştığınızda Azure Machine Learning bu hata oluşur. Azure depolama 'da bir tablo belirtirseniz, Azure Tablo depolama alanından okurken veya yazarken bu hatayı alırsınız. Bu durum, istenen tablonun adını yanlış yazdığınızda veya hedef adı ile depolama türü arasında bir uyuşmazlık olması durumunda gerçekleşebilir. Örneğin, bir tablodan okumayı amaçlıyorsanız, ancak bunun yerine bir Blobun adını girdiniz.  
  
**Çözüm:** Tablo adının doğru olduğunu doğrulamak için modülü yeniden ziyaret edin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Azure tablosu yok.|  
|"{0}" Azure tablosu yok.|  
  
## <a name="error-0071"></a>Hata 0071  
 Girilen kimlik bilgileri yanlışsa özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, belirtilen kimlik bilgileri yanlışsa oluşur.  
  
 Modül bir HDInsight kümesine bağlanamıyorsa da bu hatayı alabilirsiniz.  
  
**Çözüm:** Modülün girdilerini gözden geçirin ve hesap adını ve parolayı doğrulayın.  
  
 Hataya neden olabilecek aşağıdaki sorunları kontrol edin:  
  
-   Veri kümesinin şeması hedef DataTable 'ın şemasıyla eşleşmiyor.  
  
-   Sütun adları eksik veya yanlış yazılmış  
  
-   Geçersiz karakterleri olan sütun adlarına sahip bir tabloya yazıyor. Genellikle bu tür sütun adlarını köşeli ayraç içine alabilirsiniz, ancak bu işe çalışmazsa sütun adlarını yalnızca harfler ve alt çizgiler (_) kullanacak şekilde düzenleyin  
  
-   Yazmaya çalıştığınız dizeler tek tırnak işaretleri içeriyor  
  
 Bir HDInsight kümesine bağlanmaya çalışıyorsanız, hedef kümeye sağlanan kimlik bilgileriyle erişilebildiğini doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Yanlış kimlik bilgileri geçirildi.|  
|Yanlış Kullanıcı adı "{0}" veya parola geçti|  
  

## <a name="error-0072"></a>Hata 0072  
 Bağlantı zaman aşımı durumunda özel durum oluşur.  
  
 Azure Machine Learning bir bağlantı zaman aşımına uğrarsa bu hata oluşur. Bu hatayı, yavaş internet bağlantısı gibi veri kaynağı veya hedefle ilgili bağlantı sorunları varsa ya da veri kümesi büyükse ve/veya veride okunan SQL sorgusu karmaşık işlem yaptığında alırsınız.  
  
**Çözüm:** Azure Storage veya Internet ile yavaş bağlantılarla ilgili şu anda sorun olup olmadığını belirleme.  
  
|Özel durum Iletileri|  
|------------------------|  
|Bağlantı zaman aşımı oluştu.|  
  

## <a name="error-0073"></a>Hata 0073  
 Bir sütun başka bir türe dönüştürülürken hata oluşursa özel durum oluşur.  
  
 Azure Machine Learning bu hata, sütunu başka bir türe dönüştürmek mümkün olmadığında oluşur.  Bir modül belirli bir tür gerektiriyorsa ve sütunu yeni türe dönüştürmek mümkün değilse bu hatayı alırsınız.  
  
**Çözüm:** Sütunun iç özel duruma göre dönüştürülebilmesi için giriş veri kümesini değiştirin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Sütun dönüştürülemedi.|  
|Sütun {0}dönüştürülemedi.|  
  

## <a name="error-0074"></a>Hata 0074  
 [Düzenleme meta verileri](edit-metadata.md) seyrek bir sütunu kategorik 'e dönüştürmeye çalıştığında özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, [düzenleme meta verileri](edit-metadata.md) seyrek bir sütunu kategorik olarak dönüştürmeye çalıştığında oluşur.  Bu hatayı, seyrek sütunları, **kategorik yap** seçeneğiyle kategorik 'e dönüştürmeye çalışırken alacaksınız.  Azure Machine Learning seyrek categorik dizileri desteklemediğinden modül başarısız olur.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Özel durum Iletileri|  
|------------------------|  
|Seyrek sütunlar kategorik olarak dönüştürülemez.|  
  

## <a name="error-0075"></a>Hata 0075  
Bir veri kümesini satışlama sırasında geçersiz bir binme işlevi kullanıldığında özel durum oluşur.  
  
Azure Machine Learning içinde bu hata, desteklenmeyen bir yöntemi kullanarak verileri bölmeye çalışırken veya parametre birleşimleri geçersiz olduğunda oluşur.  
  
**Çözünürlüğüne**

Bu olay için hata işleme, daha fazla sayıda daha fazla özelleştirme yönteminin özelleştirilmesine izin veren Azure Machine Learning önceki bir sürümünde kullanıma sunulmuştur. Şu anda tüm binme yöntemleri, bir açılan listeden seçim üzerine dayalıdır, bu nedenle Teknik olarak bu hatayı almak için artık mümkün olmayacaktır.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Özel durum Iletileri|  
|------------------------|  
|Geçersiz binme işlevi kullanıldı.|  
  

## <a name="error-0077"></a>Hata 0077  
 Bilinmeyen BLOB dosya yazma modu geçirildiğinde özel durum oluşur.  
  
 Bir BLOB dosya hedefi veya kaynağı için belirtimlerde geçersiz bir bağımsız değişken geçirilmediğinde Azure Machine Learning bu hata oluşur.  
  
**Çözüm:** Azure Blob depolama alanına ve bu sunucudan veri içeri ve dışarı aktarılan neredeyse tüm modüllerde, yazma modunu denetleyen parametre değerleri bir açılan liste kullanılarak atanır; Bu nedenle, geçersiz bir değer geçirmek mümkün değildir ve bu hata görüntülenmemelidir. Bu hata sonraki bir sürümde kullanım dışı olacaktır.  
  
|Özel durum Iletileri|  
|------------------------|  
|Desteklenmeyen blob yazma modu.|  
|Desteklenmeyen blob yazma modu: {0}.|  
  

## <a name="error-0078"></a>Hata 0078  
 [Içeri aktarma verileri](import-data.md) için http seçeneği yeniden yönlendirmeyi belirten bir 3xx durum kodu aldığında özel durum oluşur.  
  
 Azure Machine Learning bu hata, [Içeri aktarma verileri](import-data.md) için http seçeneği yeniden yönlendirmeyi belirten bir 3xx (301, 302, 304, vb.) durum kodu aldığında oluşur. Tarayıcıyı başka bir sayfaya yönlendiren bir HTTP kaynağına bağlanmaya çalışırsanız bu hatayı alırsınız. Güvenlik nedenleriyle, Web sitelerinin Azure Machine Learning için veri kaynakları olarak yeniden yönlendirilmesi izin verilmez.  
  
**Çözüm:** Web sitesi güvenilir bir Web sitesi ise, yeniden yönlendirilen URL 'YI doğrudan girin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Http yeniden yönlendirmesine izin verilmiyor|  
  

## <a name="error-0079"></a>Hata 0079  
 Azure depolama kapsayıcısı adı yanlış belirtilmişse özel durum oluşur.  
  
 Azure depolama kapsayıcısı adı yanlış belirtilmişse Azure Machine Learning bu hata oluşur. Bu hatayı, Azure Blob depolama alanına yazarken **kapsayıcı ile başlayan blob 'un yolunu** kullanarak hem kapsayıcıyı hem de blob (dosya) adını belirttebilmeniz durumunda alırsınız.  
  
**Çözüm:** [Verileri dışarı aktar](export-data.md) modülünü yeniden ziyaret edin ve belirtilen blob yolunun hem kapsayıcıyı hem de dosya adını kapsayıcı **/filename**biçiminde içerdiğini doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Azure depolama kapsayıcısı adı yanlış.|  
|Azure depolama kapsayıcısı adı "{0}" yanlış; kapsayıcı/blob biçim kapsayıcısı adı bekleniyordu.|  
  

## <a name="error-0080"></a>Hata 0080  
 Özel durum, tüm değerleri eksik olan sütuna modül tarafından izin verilmediği zaman oluşur.  
  
 Azure Machine Learning bu hata, modül tarafından tüketilen bir veya daha fazla sütun eksik tüm değerleri içerdiğinde üretilir. Örneğin, bir modül her sütun için toplama istatistiklerini hesapladıysanız, veri içermeyen bir sütun üzerinde çalışamaz. Bu gibi durumlarda, modül yürütmesi bu özel durumla durdurulur.  
  
**Çözüm:** Giriş veri kümesini yeniden ziyaret edin ve tüm eksik değerleri içeren sütunları kaldırın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Tüm değerleri eksik olan sütunlara izin verilmez.|  
|Sütun {0} tüm değerler eksik.|  
  

## <a name="error-0081"></a>Hata 0081  
 En az bir seyrek özellik sütunu içeren giriş veri kümesindeki Özellik sütunlarının sayısına eşitse, PCA modülünde özel durum oluşur.  
  
 Azure Machine Learning bu hata, aşağıdaki koşullar karşılanıyorsa üretilir: (a) giriş veri kümesinde en az bir seyrek sütun ve (b) istenen en son boyut sayısı, giriş boyutlarının sayısıyla aynıdır.  
  
**Çözüm:** Çıkışdaki boyut sayısını, girişte bulunan boyut sayısından az olacak şekilde azaltmayı göz önünde bulundurun. Bu, PCA uygulamalarında tipik bir davranıştır.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Özel durum Iletileri|  
|------------------------|  
|Seyrek Özellik sütunları içeren veri kümesi için, azaltma için boyut sayısı Özellik sütunlarının sayısından az olmalıdır.|  
 

## <a name="error-0082"></a>Hata 0082  
 Bir modelin serisi başarıyla kaldırıldığında özel durum oluşur.  
  
 Azure Machine Learning bu hata, kaydedilmiş bir makine öğrenimi modeli veya dönüştürmesi, bir son değişikliğin sonucu olarak Azure Machine Learning çalışma zamanının daha yeni bir sürümü tarafından yüklenemediğinde oluşur.  
  
**Çözüm:** Modeli veya dönüştürmeyi üreten eğitim ardışık düzeni yeniden çalıştırılmalıdır ve model ya da dönüşüm yeniden kaydedilmelidir.  
  
|Özel durum Iletileri|  
|------------------------|  
|Büyük olasılıkla eski bir serileştirme biçimiyle serileştirildiği için model seri durumdan çıkarılamadı. Modeli yeniden eğitme ve yeniden kaydedin.|  
  

## <a name="error-0083"></a>Hata 0083  
 Eğitim için kullanılan veri kümesi, Learner somut türü için kullanılmıyorsa özel durum oluşur.  
  
 Azure Machine Learning bu hata, veri kümesi eğitilen öğrenici ile uyumsuz olduğunda üretilir. Örneğin, veri kümesi her satırda en az bir eksik değer içerebilir ve sonuç olarak veri kümesinin tamamı eğitim sırasında atlanır. Diğer durumlarda, anomali algılama gibi bazı makine öğrenimi algoritmaları etiketlerin mevcut olmasını beklemez ve veri kümesinde Etiketler mevcutsa bu özel durumu oluşturabilir.  
  
**Çözüm:** Giriş veri kümesi için gereksinimleri denetlemek için kullanılan öğrenimlerin belgelerine başvurun. Tüm gerekli sütunların mevcut olduğunu görmek için sütunları inceleyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Eğitim için kullanılan veri kümesi geçersiz.|  
|{0} eğitim için geçersiz veri içeriyor.|  
|{0} eğitim için geçersiz veri içeriyor. Learner türü: {1}.|  
  

## <a name="error-0084"></a>Hata 0084  
 Bir R betikten üretilen puanlar değerlendirildiğinde özel durum oluşur. Bu şu anda desteklenmiyor.  
  
 Azure Machine Learning içinde bu hata, puanları içeren bir R betiğinin çıkışıyla bir modeli değerlendirmek için modüllerden birini kullanmayı denerseniz oluşur.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|R tarafından üretilen puanları değerlendirmek Şu anda desteklenmiyor.|  
  

## <a name="error-0085"></a>Hata 0085  
 Betik değerlendirmesi hata vererek başarısız olduğunda özel durum oluşur.  
  
 Azure Machine Learning bu hata, sözdizimi hataları içeren özel betik çalıştırırken oluşur.  
  
**Çözüm:** Kodunuzu bir dış düzenleyicide gözden geçirin ve hata olup olmadığını denetleyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Betik değerlendirmesi sırasında hata oluştu.|  
|Betik değerlendirmesi sırasında aşağıdaki hata oluştu, daha fazla bilgi için çıkış günlüğünü görüntüleyin: {0} yorumlayıcı----------hata iletisinin başlangıcını--------------------yorumlayıcısından {0} hata iletisinin sonuna {1}----------|  
  

## <a name="error-0086"></a>Hata 0086  
 Bir sayım dönüştürmesi geçersiz olduğunda özel durum oluşur.  
  
 Azure Machine Learning bu hata, bir Count tablosuna dayalı bir dönüşüm seçtiğinizde oluşur, ancak seçilen dönüşüm geçerli verilerle veya yeni sayım tablosu ile uyumsuzdur.  
  
**Çözüm:** Modül, dönüştürmeyi iki farklı biçimde oluşturan sayıları ve kuralları kaydetmeyi destekler. Sayım tablolarını birleştiriyorsanız, birleştirmek istediğiniz her iki tablonun de aynı biçimde kullanılacağını doğrulayın.  
  
Genel olarak, sayı tabanlı bir dönüşüm yalnızca dönüştürmenin ilk olarak oluşturulduğu veri kümesiyle aynı şemaya sahip olan veri kümelerine uygulanabilir.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Özel durum Iletileri|  
|------------------------|  
|Geçersiz sayım dönüşümü belirtildi.|  
|'{0}' giriş bağlantı noktasındaki sayım dönüştürmesi geçersiz.|  
|'{0}' giriş bağlantı noktasındaki sayım dönüştürmesi, '{1}' giriş bağlantı noktasındaki sayım dönüşümüyle birleştirilemez. Eşleşenleri saymak için kullanılan meta verileri doğrulamak için işaretleyin.|  
  

## <a name="error-0087"></a>Hata 0087  
 Count modülleri ile öğrenme için geçersiz bir sayma tablosu türü belirtildiğinde özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, var olan bir Count tablosunu içeri aktarmaya çalıştığınızda oluşur, ancak tablo geçerli verilerle veya yeni sayım tablosu ile uyumsuzdur.  
  
**Çözüm:** Dönüştürmeyi oluşturan sayıları ve kuralları kaydetmek için farklı biçimler vardır. Count tablolarını birleştiriyorsanız, her ikisinin de aynı biçimi kullandığınızı doğrulayın.  
  
 Genellikle, bir sayma tabanlı dönüşüm yalnızca dönüştürmenin ilk olarak oluşturulduğu veri kümesiyle aynı şemaya sahip olan veri kümelerine uygulanabilir.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Hata 0088  
 Sayı modülleri ile öğrenme için geçersiz bir sayma türü belirtildiğinde özel durum oluşur.  
  
 Azure Machine Learning ' de bu hata, sayı tabanlı farklılaştırma için desteklenenden farklı bir sayma yöntemi kullanmaya çalıştığınızda oluşur.  
  
**Çözüm:** Genel olarak, sayım yöntemleri bir açılan listeden seçilir, bu nedenle bu hatayı görmezsiniz.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Özel durum Iletileri|  
|------------------------|  
|Geçersiz sayım türü belirtildi.|  
|Belirtilen '{0}' sayım türü geçerli bir sayım türü değil.|  
  

## <a name="error-0089"></a>Hata 0089  
 Belirtilen sınıf sayısı, sayım için kullanılan bir veri kümesindeki gerçek sınıf sayısından az olduğunda özel durum oluşur.  
  
 Bir Count tablosu oluştururken Azure Machine Learning bu hata oluşur ve etiket sütunu modül parametrelerinde belirtenden farklı sayıda sınıf içerdiğinde oluşur.  
  
**Çözüm:** Veri kümenizi denetleyin ve etiket sütununda tam olarak kaç farklı değer (olası sınıf) olduğunu öğrenin. Count tablosunu oluşturduğunuzda, en az bu sayıda sınıfı belirtmeniz gerekir.  
  
 Count tablosu, kullanılabilir sınıfların sayısını otomatik olarak belirleyemiyor.  
  
 Count tablosunu oluşturduğunuzda, 0 veya etiket sütunundaki gerçek sınıf sayısından daha küçük bir sayı belirtemezsiniz.  
  
|Özel durum Iletileri|  
|------------------------|  
|Sınıfların sayısı yanlış. Parametre bölmesinde belirttiğiniz sınıf sayısının etiket sütunundaki sınıf sayısından büyük veya ona eşit olduğundan emin olun.|  
|Belirtilen sınıf sayısı '{0}' ve bu değer, saymak için kullanılan veri kümesindeki '{1}' Etiket değerinden büyük değil. Parametre bölmesinde belirttiğiniz sınıf sayısının etiket sütunundaki sınıf sayısından büyük veya ona eşit olduğundan emin olun.|  
  

## <a name="error-0090"></a>Hata 0090  
 Hive tablo oluşturma başarısız olduğunda özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, verileri [dışarı aktarma](export-data.md) veya bir HDInsight kümesine kaydetmek için başka bir seçenek kullandığınızda oluşur ve belirtilen Hive tablosu oluşturulenemez.  
  
**Çözüm:** Kümeyle ilişkili Azure depolama hesabı adını denetleyin ve modül özelliklerinde aynı hesabı kullandığınızdan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Hive tablosu oluşturulamadı. Bir HDInsight kümesi için, kümeyle ilişkili Azure depolama hesabı adının modül parametresi aracılığıyla geçirilen ile aynı olduğundan emin olun.|  
|"{0}" Hive tablosu oluşturulamadı. Bir HDInsight kümesi için, kümeyle ilişkili Azure depolama hesabı adının modül parametresi aracılığıyla geçirilen ile aynı olduğundan emin olun.|  
|"{0}" Hive tablosu oluşturulamadı. HDInsight kümesi için, kümeyle ilişkili Azure depolama hesabı adının "{1}" olduğundan emin olun.|  
 

## <a name="error-0100"></a>Hata 0100  
 Özel bir modül için desteklenmeyen bir dil belirtildiğinde özel durum oluşur.  
  
 Bu Azure Machine Learning hata, özel bir modül oluşturulurken ve özel bir modül XML tanım dosyasındaki **Language** öğesinin Name özelliğinin geçersiz bir değere sahip olduğu durumlarda oluşur. Şu anda bu özellik için geçerli olan tek değer `R`. Örneğin:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Çözüm:** Özel modül XML tanım dosyasındaki **Language** öğesinin name özelliğinin `R`olarak ayarlandığını doğrulayın. Dosyayı kaydedin, özel modül ZIP paketini güncelleştirin ve özel modülü yeniden eklemeyi deneyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Desteklenmeyen özel modül dili belirtildi|  
  

## <a name="error-0101"></a>Hata 0101  
 Tüm bağlantı noktası ve parametre kimlikleri benzersiz olmalıdır.  
  
 Azure Machine Learning bu hata, bir veya daha fazla bağlantı noktası ya da parametreye özel bir modül XML tanım dosyasında aynı KIMLIK değeri atandığında oluşur.  
  
**Çözüm:** Tüm bağlantı noktaları ve parametreler genelinde KIMLIK değerlerinin benzersiz olduğunu denetleyin. XML dosyasını kaydedin, özel modül ZIP paketini güncelleştirin ve özel modülü yeniden eklemeyi deneyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Bir modülün tüm bağlantı noktası ve parametre kimlikleri benzersiz olmalıdır|  
|'{0}' modülünün yinelenen bağlantı noktası/bağımsız değişken kimlikleri vardır. Tüm bağlantı noktası/bağımsız değişken kimlikleri bir modül için benzersiz olmalıdır.|  
  

## <a name="error-0102"></a>Hata 0102  
 Bir ZIP dosyası ayıklanamıyor olduğunda oluşturulur.  
  
 Azure Machine Learning içindeki bu hata,. zip uzantısına sahip bir daraltılmış paketi içeri aktarırken, ancak paket bir ZIP dosyası olmadığında veya dosya desteklenen bir ZIP biçimi kullanmıyorsa oluşur.  
  
**Çözüm:** Seçili dosyanın geçerli bir. zip dosyası olduğundan ve desteklenen sıkıştırma algoritmalarından birini kullanarak sıkıştırıldığından emin olun.  
  
 Veri kümelerini sıkıştırılmış biçimde aktarırken bu hatayı alırsanız, içerilen tüm dosyaların desteklenen dosya biçimlerinden birini kullandığınızı ve Unicode biçiminde olduğunu doğrulayın.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 İstenen dosyaları yeni bir sıkıştırılmış daraltılmış klasöre açmayı deneyin ve özel modülü yeniden eklemeyi deneyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Verilen ZIP dosyası doğru biçimde değil|  


## <a name="error-0103"></a>Hata 0103  
 Bir ZIP dosyası herhangi bir. xml dosyası içermiyorsa oluşturulur  
  
 Azure Machine Learning içindeki bu hata, özel modül ZIP paketi herhangi bir modül tanımı (. xml) dosyası içermiyorsa oluşur. Bu dosyaların, ZIP paketinin kökünde bulunması gerekir (örneğin, bir alt klasör içinde değil.)  
  
**Çözüm:** Bir veya daha fazla XML modül tanımı dosyasının, disk sürücünüzdeki geçici bir klasöre çıkartarak ZIP paketinin kök klasöründe olduğunu doğrulayın. Herhangi bir XML dosyası, ZIP paketini ayıkladığınız klasörde doğrudan olmalıdır. ZIP paketini oluştururken, zip paketi içinde, zip üzerinde seçtiğiniz klasörle aynı ada sahip bir alt klasör oluşturacak şekilde ZIP paketini oluştururken emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Verilen ZIP dosyası herhangi bir modül tanım dosyası içermiyor (. xml dosyası)|  


## <a name="error-0104"></a>Hata 0104  
 Modül tanımı dosyası bulunamayan bir betiğe başvurduğunda oluşturulur  
  
 Bu Azure Machine Learning hata, özel bir modül XML tanım dosyası, ZIP paketinde bulunmayan **Language** öğesindeki bir betik dosyasına başvurduğunda oluşur. Betik dosyası yolu, **Language** öğesinin **SourceFile** özelliğinde tanımlanmıştır. Kaynak dosyanın yolu, ZIP paketinin köküne göredir (modül XML tanım dosyalarıyla aynı konum). Betik dosyası bir alt klasörce ise, betik dosyasının göreli yolu belirtilmelidir. Örneğin, tüm betikler ZIP paketi içindeki bir **myscripts** klasöründe depolanıyorsa, **Language** öğesinin bu yolu aşağıdaki gibi **SourceFile** özelliğine eklemesi gerekir. Örneğin:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Çözüm:** Özel modül XML tanımının **Language** öğesindeki **SourceFile** özelliğinin değerinin doğru olduğundan ve kaynak dosyanın ZIP paketindeki doğru göreli yolda bulunduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Başvurulan R betiği dosyası yok.|  
|Başvurulan R betik dosyası '{0}' bulunamıyor. Dosya için göreli yolun, tanımlar konumundan doğru olduğundan emin olun.|  


## <a name="error-0105"></a>Hata 0105  
 Bu hata bir modül tanımı dosyası desteklenmeyen bir parametre türü içerdiğinde görüntülenir  
  
 Azure Machine Learning içindeki bu hata, özel bir modül xml tanımı oluşturduğunuzda ve bir parametre ya da tanımda bir bağımsız değişkenin türü desteklenen bir türle eşleşmediği zaman üretilir.  
  
**Çözüm:** Özel modül XML tanım dosyasındaki herhangi bir **bağımsız değişken** öğesinin type özelliğinin desteklenen bir tür olduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Desteklenmeyen parametre türü.|  
|Desteklenmeyen parametre türü '{0}' belirtildi.|  


## <a name="error-0106"></a>Hata 0106  
 Modül tanımı dosyası desteklenmeyen bir giriş türünü tanımlıyorsa oluşturulur  
  
 Azure Machine Learning içindeki bu hata, özel bir modül XML tanımındaki bir giriş bağlantı noktasının türü desteklenen bir türle eşleşmediği zaman üretilir.  
  
**Çözüm:** Özel modül XML tanım dosyasındaki bir giriş öğesinin type özelliğinin desteklenen bir tür olduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Desteklenmeyen giriş türü.|  
|Desteklenmeyen giriş türü '{0}' belirtildi.|  


## <a name="error-0107"></a>Hata 0107  
 Modül tanımı dosyası desteklenmeyen bir çıkış türünü tanımlıyorsa oluşturulur  
  
 Azure Machine Learning içindeki bu hata, özel bir modül XML tanımındaki bir çıkış bağlantı noktasının türü desteklenen bir türle eşleşmediği zaman üretilir.  
  
**Çözüm:** Özel modül XML tanım dosyasındaki bir output öğesinin type özelliğinin desteklenen bir tür olduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Desteklenmeyen çıkış türü.|  
|Desteklenmeyen çıkış türü '{0}' belirtildi.|  


## <a name="error-0108"></a>Hata 0108  
 Bir modül tanımı dosyası desteklenenden daha fazla giriş veya çıkış bağlantı noktası tanımlıyorsa oluşturulur  
  
 Azure Machine Learning bu hata, özel bir modül XML tanımında çok fazla giriş veya çıkış bağlantı noktası tanımlandığında üretilir.  
  
**Çözüm:** Özel modül XML tanımında tanımlanan maksimum giriş ve çıkış bağlantı noktası sayısının en fazla desteklenen bağlantı noktası sayısını aşmadığından emin olur.  
  
|Özel durum Iletileri|  
|------------------------|  
|Desteklenen giriş veya çıkış bağlantı noktası sayısı aşıldı.|  
|Desteklenen '{0}' bağlantı noktası sayısı aşıldı. İzin verilen en fazla '{0}' bağlantı noktası sayısı '{1}'.| 

## <a name="error-0109"></a>Hata 0109  
 Bir modül tanımı dosyası bir sütun seçiciyi yanlış bir şekilde tanımladığında oluşturulur  
  
 Azure Machine Learning bu hata, bir sütun seçici bağımsız değişkeninin sözdizimi özel bir modül XML tanımında hata içerdiğinde üretilir.  
  
**Çözüm:** Bu hata, bir sütun seçici bağımsız değişkeninin sözdizimi özel bir modül XML tanımında hata içerdiğinde üretilir.  
  
|Özel durum Iletileri|  
|------------------------|  
|Sütun seçici için desteklenmeyen sözdizimi.|  
  

## <a name="error-0110"></a>Hata 0110  
 Modül tanımı dosyası mevcut olmayan bir giriş bağlantı noktası KIMLIĞINE başvuruda bulunan bir sütun seçici tanımlıyorsa oluşturulur  
  
 Azure Machine Learning içindeki bu hata, ColumnPicker türünde bir bağımsız değişken öğesinin Properties öğesi içindeki *PortID* özelliği bir giriş bağlantı noktasının kimlik değeri ile eşleşmediği zaman üretilir.  
  
**Çözüm:** PortID özelliğinin özel modül XML tanımında tanımlanan bir giriş bağlantı noktasının KIMLIK değeri ile eşleştiğinden emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Sütun seçici mevcut olmayan bir giriş bağlantı noktası KIMLIĞINE başvuruyor.|  
|Sütun seçici, mevcut olmayan bir '{0}' giriş bağlantı noktası KIMLIĞINE başvuruyor.|  
  

## <a name="error-0111"></a>Hata 0111  
 Modül tanımı dosyası geçersiz bir özellik tanımlıyorsa oluşturulur  
  
 Azure Machine Learning içindeki bu hata, özel modül XML tanımındaki bir öğeye geçersiz bir özellik atandığında üretilir.  
  
**Çözüm:** Özelliğin özel modül öğesi tarafından desteklendiğinden emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Özellik tanımı geçersiz.|  
|Özellik tanımı '{0}' geçersiz.|  
  

## <a name="error-0112"></a>Hata 0112  
 Modül tanımı dosyası ayrıştırılamadığınızda oluşturulur  
  
 Bu Azure Machine Learning hata, XML biçiminde bir hata olduğunda oluşturulur ve özel modül XML tanımının geçerli bir XML dosyası olarak ayrıştırılmasını önler.  
  
**Çözüm:** Her öğenin doğru şekilde açıldığından ve kapatıldığından emin olun. XML biçimlendirmesinde hata olmadığından emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Modül tanım dosyası ayrıştırılamıyor.|  
|'{0}' modül tanımı dosyası ayrıştırılamıyor.|  
  

## <a name="error-0113"></a>Hata 0113  
 Bir modül tanımı dosyası hatalar içerdiğinde oluşturulur.  
  
 Bu Azure Machine Learning hata, özel modül XML tanım dosyası ayrıştırıla, ancak özel modüller tarafından desteklenmeyen öğelerin tanımı gibi hatalar içerdiğinde üretilir.  
  
**Çözüm:** Özel modül tanımı dosyasının özel modüller tarafından desteklenen öğeleri ve özellikleri tanımladığından emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Modül tanımı dosyası hatalar içeriyor.|  
|'{0}' modül tanımı dosyası hatalar içeriyor.|  
|'{0}' modül tanımı dosyası hatalar içeriyor. {1}|  
  

## <a name="error-0114"></a>Hata 0114  
 Özel bir modül oluşturulamazsa oluşturulur.  
  
 Azure Machine Learning içindeki bu hata, özel bir modül oluşturma işlemi başarısız olduğunda üretilir. Özel modül eklenirken bir veya daha fazla özel modülle ilgili hatayla karşılaşıldığında bu durum oluşur. Bu hata iletisi içinde ek hatalar raporlanır.  
  
**Çözüm:** Bu özel durum iletisi içinde bildirilen hataları çözün.  
  
|Özel durum Iletileri|  
|------------------------|  
|Özel modül derlenemedi.|  
|Özel modül derlemeleri hata (ler) ile başarısız oldu: {0}|  
  

## <a name="error-0115"></a>Hata 0115  
 Özel bir modül varsayılan betiği desteklenmeyen bir uzantıya sahip olduğunda oluşturulur.  
  
 Azure Machine Learning içindeki bu hata, bilinmeyen bir dosya adı uzantısı kullanan özel bir modül için bir komut dosyası sağladığınızda oluşur.  
  
**Çözüm:** Özel modülde yer alan herhangi bir betik dosyasının dosya biçimi ve dosya adı uzantısını doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Varsayılan betik için desteklenmeyen uzantı.|  
|Varsayılan betik için desteklenmeyen dosya uzantısı {0}.|  
  

## <a name="error-0121"></a>Hata 0121  
 Tablo yazılamaz olduğundan SQL yazma işlemleri başarısız olduğunda oluşturulur  
  
 Azure Machine Learning bu hata, sonuçları SQL veritabanındaki bir tabloya kaydetmek için [verileri dışarı aktarma](export-data.md) modülünü kullandığınızda oluşturulur ve tablo öğesine yazılamaz. Genellikle, [veri verme](export-data.md) modülünün SQL Server örnekle bir bağlantı kurmasından, ancak daha sonra tabloya Azure ML veri kümesinin içeriğini yazmadığında bu hatayı görürsünüz.  
  
**Çözünürlüğüne**
 - [Veri dışarı aktarma](export-data.md) modülünün Özellikler bölmesini açın ve veritabanı ve tablo adlarının doğru girildiğini doğrulayın. 
 - Dışarı aktardığınız veri kümesinin şemasını gözden geçirin ve verilerin hedef tabloyla uyumlu olduğundan emin olun.
 - Kullanıcı adı ve parolasıyla ilişkili SQL oturum açma 'nın tabloya yazma izinleri olduğunu doğrulayın. 
 - Özel durum SQL Server 'den ek hata bilgileri içeriyorsa, düzeltmeler yapmak için bu bilgileri kullanın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Sunucuya bağlanıldı, tabloya yazılamıyor.|  
|SQL tablosuna yazılamıyor: {0}|  


## <a name="error-0122"></a>Hata 0122  
 Birden çok ağırlık sütunu belirtilirse ve yalnızca birine izin veriliyorsa özel durum oluşur.  
  
 Azure Machine Learning içindeki bu hata, ağırlık sütunları olarak çok fazla sütun seçildiği zaman oluşur.  
  
**Çözüm:** Giriş veri kümesini ve meta verilerini gözden geçirin. Yalnızca bir sütunun ağırlık içerdiğinden emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Birden çok ağırlık sütunu belirtildi.|  


## <a name="error-0123"></a>Hata 0123  
 Etiket sütunu için vektör sütunu belirtilmişse özel durum oluşur.  
  
 Etiket sütunu olarak bir Vector kullanırsanız Azure Machine Learning bu hata oluşur.  
  
**Çözüm:** Gerekirse sütunun veri biçimini değiştirin veya farklı bir sütun seçin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Vektör sütunu etiket sütunu olarak belirtilir.|  


## <a name="error-0124"></a>Hata 0124  
 Sayısal olmayan sütunlar ağırlık sütunu olacak şekilde belirtilmişse özel durum oluşur.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Sayısal olmayan sütun, ağırlık sütunu olarak belirtilir.|  
  


## <a name="error-0125"></a>Hata 0125  
 Birden çok veri kümesi için şema eşleşmediği zaman oluşturulur.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Veri kümesi şeması eşleşmiyor.|  


## <a name="error-0126"></a>Hata 0126  
 Kullanıcı Azure ML 'de desteklenmeyen bir SQL etki alanı belirtiyorsa özel durum oluşur.  
  
 Bu hata, Kullanıcı Azure Machine Learning desteklenmeyen bir SQL etki alanı belirttiğinde üretilir. Bu hatayı, beyaz listede olmayan bir etki alanındaki bir veritabanı sunucusuna bağlanmaya çalışıyorsanız alırsınız. Şu anda, izin verilen SQL etki alanları şunlardır: ". database.windows.net", ". cloudapp.net" veya ". database.secure.windows.net". Yani, sunucunun Azure 'da bir Azure SQL Server veya bir sanal makinede bulunan bir sunucu olması gerekir.  
  
**Çözüm:** Modülü yeniden ziyaret edin. SQL veritabanı sunucusunun kabul edilen etki alanlarından birine ait olduğunu doğrulayın:  
  
-   . database.windows.net  
  
-   . cloudapp.net  
  
-   . database.secure.windows.net  
  
|Özel durum Iletileri|  
|------------------------|  
|Desteklenmeyen SQL etki alanı.|  
|SQL etki alanı {0} Şu anda Azure ML 'de desteklenmiyor|  
  

## <a name="error-0127"></a>Hata 0127  
 Resim piksel boyutu izin verilen sınırı aşıyor  
  
 Bu hata, sınıflandırma için bir görüntü veri kümesinden görüntüleri okuyorsanız ve görüntülerin modelin işleyebileceğinden daha büyük olması durumunda oluşur.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Özel durum Iletileri|  
|------------------------|  
|Resim piksel boyutu izin verilen sınırı aşıyor.|  
|'{0}' dosyasındaki resim piksel boyutu izin verilen sınırı aşıyor: '{1}'|  


## <a name="error-0128"></a>Hata 0128  
 Kategorik sütunlar için koşullu olasılıkların sayısı sınırı aşıyor.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Kategorik sütunlar için koşullu olasılıkların sayısı sınırı aşıyor.|  
|Kategorik sütunlar için koşullu olasılıkların sayısı sınırı aşıyor. '{0}' ve '{1}' sütunları sorunlu çiftidir.|  


## <a name="error-0129"></a>Hata 0129  
 Veri kümesindeki sütun sayısı izin verilen sınırı aşıyor.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Veri kümesindeki sütun sayısı izin verilen sınırı aşıyor.|  
|'{0}' içindeki veri kümesindeki sütun sayısı izin verilen sınırı aşıyor. '|  
|'{0}' içindeki veri kümesindeki sütun sayısı izin verilen '{1}' sınırını aşıyor. '|  
|'{0}' içindeki veri kümesindeki sütun sayısı '{2}' için izin verilen '{1}' sınırını aşıyor. '|  
## <a name="error-0130"></a>Hata 0130  
 Eğitim veri kümesindeki tüm satırlar eksik değerler içerdiğinde özel durum oluşur.  
  
 Bu, eğitim veri kümesindeki bir sütun boş olduğunda oluşur.  
  
**Çözüm:** Eksik olan tüm değerleri içeren sütunları kaldırmak için [eksik veri silme](clean-missing-data.md) modülünü kullanın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Eğitim veri kümesindeki tüm satırlar eksik değerler içeriyor.  Eksik değerleri kaldırmak için eksik verileri temizleme modülünü kullanmayı düşünün.|  
 

## <a name="error-0131"></a>Hata 0131  
 ZIP dosyasındaki bir veya daha fazla veri kümesinin sıkıştırılamadı ve doğru şekilde kaydedilmediğinde özel durum oluşur  
  
 Bu hata, bir ZIP dosyasındaki bir veya daha fazla veri kümesinin sıkıştırılamadı ve doğru okunamazsa üretilir. ZIP dosyasının kendisi veya içindeki dosyalardan biri bozuk olduğu veya bir dosyayı paketten açmaya çalışırken bir sistem hatası olduğundan, açma işlemi başarısız olursa bu hatayı alırsınız.  
  
**Çözüm:** Nasıl ilerleyeceğini öğrenmek için hata iletisinde sunulan ayrıntıları kullanın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Daraltılmış veri kümelerini karşıya yükleme başarısız|  
|Daraltılmış veri kümesi {0} şu iletiyle başarısız oldu: {1}|  
|Daraltılmış veri kümesi {0} şu iletiyle {1} bir özel durumla başarısız oldu: {2}|  
  

## <a name="error-0132"></a>Hata 0132  
 Açmak için dosya adı belirtilmedi; ZIP dosyasında birden çok dosya bulundu.  
  
 Bu hata, açma için dosya adı belirtilmediğinde üretilir; ZIP dosyasında birden çok dosya bulundu. . Zip dosyası birden fazla sıkıştırılmış dosya içeriyorsa, ancak modülün **özellik** bölmesinde **veri kümesindeki** ayıklama için bir dosya belirtmediyseniz bu hatayı alırsınız. Şu anda, modül her çalıştırıldığında yalnızca bir dosya ayıklanabilir.  
  
**Çözüm:** Hata iletisi,. zip dosyasında bulunan dosyaların bir listesini sağlar. İstenen dosyanın adını kopyalayın ve metin kutusunun **paketini açmak Için veri kümesine** yapıştırın.  
  
|Özel durum Iletileri|  
|------------------------|  
|ZIP dosyası birden çok dosya içeriyor; Genişletilecek dosyayı belirtmeniz gerekir.|  
|Dosya birden fazla dosya içeriyor. Genişletilecek dosyayı belirtin. Şu dosyalar bulundu: {0}|  
  

## <a name="error-0133"></a>Hata 0133  
 Belirtilen dosya ZIP dosyasında bulunamadı  
  
 Bu hata, **özellik** bölmesinin **paketini açmak için veri kümesine** girilen dosya adı,. zip dosyasında bulunan herhangi bir dosyanın adı ile eşleşmediği zaman üretilir. Bu hatanın en yaygın nedenleri bir yazma hatasıdır veya genişletilecek dosya için yanlış arşiv dosyası arıyor.  
  
**Çözüm:** Modülü yeniden ziyaret edin. Açmak istediğiniz dosyanın adı, bulunan dosya listesinde görünürse, dosya adını kopyalayın ve özellik kutusunun **paketini açmak Için veri kümesine** yapıştırın. Listede istenen dosya adını görmüyorsanız, istenen dosya için doğru. zip dosyasına ve doğru ada sahip olduğunuzdan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Belirtilen dosya, zip dosyası için tamsayı bulunamadı.|  
|Belirtilen dosya bulunamadı. Şu dosya (lar) bulundu: {0}|  
  

## <a name="error-0134"></a>Hata 0134
Etiket sütunu eksik olduğunda veya etiketli satırlara yetecek kadar özel durum oluşur.  
  
Bu hata, modül bir etiket sütunu gerektirdiğinde, ancak sütun seçimine bir tane eklemezseniz veya etiket sütununda çok fazla sayıda değer bulunmadığından oluşur.

Bu hata, önceki bir işlem, bir aşağı akış işleminde yeterli sayıda satır olduğu için veri kümesini değiştirdiğinde de oluşabilir. Örneğin, bir veri kümesini değerlere göre bölmek için **bölüm ve örnek** modülünde bir ifade kullandığınızı varsayalım. İfadeniz için eşleşme bulunmazsa, bölümden kaynaklanan veri kümelerinden biri boş olur.

Çözüm: 

 Sütun seçimine bir etiket sütunu eklerseniz, ancak tanınmazsa, etiket sütunu olarak işaretlemek için [meta verileri Düzenle](edit-metadata.md) modülünü kullanın.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Daha sonra, etiket sütununda eksik değerleri olan satırları kaldırmak için [eksik veri silme](clean-missing-data.md) modülünü kullanabilirsiniz. 

 Giriş veri kümelerinizi denetleyerek, geçerli veriler içerdiğinden emin olun ve işlemin gereksinimlerini karşılamak için yeterli sayıda satır vardır. Birçok algoritma, en az sayıda veri satırı gerektiriyorsa, ancak veriler yalnızca birkaç satır ya da yalnızca bir üst bilgi içeriyorsa bir hata mesajı oluşturur.
  
|Özel durum Iletileri|
|------------------------|
|Etiket sütunu eksik olduğunda veya etiketli satırlara yetecek kadar özel durum oluşur.|  
|Etiket sütunu eksik veya {0} etiketli satırlardan daha az olduğunda özel durum oluşur|  
  

## <a name="error-0135"></a>Hata 0135  
 Yalnızca centroıd tabanlı küme desteklenir.  
  
**Çözüm:** Kümeyi başlatmak için centroıd kullanmayan özel bir kümeleme algoritmasını temel alan bir kümeleme modeli değerlendirmeye çalıştığınızda bu hata iletisiyle karşılaşabilirsiniz.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Özel durum Iletileri|  
|------------------------|  
|Yalnızca centroıd tabanlı küme desteklenir.|  
  

## <a name="error-0136"></a>Hata 0136  
 Dosya adı döndürülmedi; Sonuç olarak dosyası işlenemiyor.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Dosya adı döndürülmedi; Sonuç olarak dosyası işlenemiyor.|  
  

## <a name="error-0137"></a>Hata 0137  
 Azure Storage SDK, okuma veya yazma sırasında tablo özellikleri ve veri kümesi sütunları arasında dönüştürme hatasıyla karşılaştı.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Azure Tablo Depolama özelliği ve veri kümesi sütunu arasında dönüştürme hatası.|  
|Azure Tablo Depolama özelliği ve veri kümesi sütunu arasında dönüştürme hatası. Ek bilgiler: {0}|  

## <a name="error-0138"></a>Hata 0138  
 Bellek tükendi, modülün çalıştırılması tamamlanamıyor. Veri kümesinin altörnekleme sorunu hafifetmenize yardımcı olabilir.  
  
 Bu hata, çalıştıran modül Azure kapsayıcısında kullanılabilir olandan daha fazla bellek gerektirdiğinde oluşur. Büyük bir veri kümesiyle çalışıyorsanız ve geçerli işlem belleğe sığmayacak olması durumunda bu durum oluşabilir.  
  
**Çözüm:** Büyük bir veri kümesini okumaya çalışıyorsanız ve işlem tamamlanamıyor, veri kümesinin altörnekleme yardımcı olabilir.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Özel durum Iletileri|  
|------------------------|  
|Bellek tükendi, modülün çalıştırılması tamamlanamıyor.|  
  

## <a name="error-0139"></a>Hata 0139  
 Bir sütunu başka bir türe dönüştürmek mümkün olmadığında özel durum oluşur.  
  
 Azure Machine Learning bu hata, bir sütunu farklı bir veri türüne dönüştürmeye çalıştığınızda oluşur, ancak bu tür geçerli işlem ya da modül tarafından desteklenmez.  
  
 Hata ayrıca bir modül geçerli modülün gereksinimlerini karşılamak üzere dolaylı olarak veri dönüştürmeye çalıştığında da görünebilir, ancak dönüştürme mümkün değildir.  
  
**Çözünürlüğüne**

1. Giriş verilerinizi gözden geçirin ve kullanmak istediğiniz sütunun tam veri türünü ve hatayı üreten sütunun veri türünü saptayın. Bazen veri türünün doğru olduğunu düşünebilirsiniz, ancak bir yukarı akış işleminin veri türünü veya bir sütunun kullanımını değiştirmekte olduğunu fark edebilirsiniz. Sütun meta verilerini özgün durumuna sıfırlamak için [meta verileri Düzenle](edit-metadata.md) modülünü kullanın. 
2. Belirtilen işlem için gereksinimleri doğrulamak üzere modül yardım sayfasına bakın. Hangi veri türlerinin geçerli modül tarafından desteklendiğini ve hangi değer aralığının desteklendiğini belirleme. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Sütunu dönüştürme veya farklı bir veri türüne dönüştürme mümkün olup olmadığını düşünün. Aşağıdaki modüller, verileri değiştirmek için önemli esneklik ve güç sağlar: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Python betiğini yürütün](execute-python-script.md).  

> [!NOTE]
> Hala çalışmıyor mu? Daha iyi sorun giderme kılavuzu geliştirmemize yardımcı olması için sorunla ilgili ek geri bildirim sağlamayı düşünün. Yalnızca bu sayfada geri bildirimde bulunun ve hatayı oluşturan modülün adını ve başarısız olan veri türü dönüştürmeyi belirtin.
  
|Özel durum Iletileri|  
|------------------------|  
|Dönüştürmeye izin verilmiyor.|  
|Dönüştürülemedi: {0}.|  
|Dönüştürülemedi: {0}, satır {1}.|  
|{0} türündeki sütun, satır {2}{1} türündeki sütuna dönüştürülemedi.|  
|{0} türündeki "{2}" sütunu, satır {3}{1} türünde bir sütuna dönüştürülemedi.|  
|{0} türündeki "{2}" sütunu, satır {4}{1} türündeki "{3}" sütununa dönüştürülemedi.| 

## <a name="error-0140"></a>Hata 0140  
 Geçirilen sütun kümesi bağımsız değişkeni etiket sütunu dışında başka sütunlar içermiyorsa özel durum oluşur.  
  
 Bu hata, özellikler dahil olmak üzere birden çok sütun gerektiren bir modüle veri kümesi bağladıysanız, ancak yalnızca etiket sütununu sağladıysanız oluşur.  
  
**Çözüm:** Veri kümesine eklemek için en az bir özellik sütunu seçin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Belirtilen sütun kümesi etiket sütunu dışında başka sütunlar içermiyor.|  
  

## <a name="error-0141"></a>Hata 0141  
 Kategorik ve dize sütunlarındaki seçili sayısal sütunların ve benzersiz değerlerin sayısı çok küçük olduğunda özel durum oluşur.  
  
 Azure Machine Learning bu hata, Seçili sütunda işlemi gerçekleştirmek için yeterli sayıda benzersiz değer olmadığında oluşur.  
  
**Çözüm:** Bazı işlemler, özellik ve kategorik sütunlarda istatistiksel işlemler gerçekleştirir ve yeterince değer yoksa, işlem başarısız olabilir veya geçersiz bir sonuç döndürebilir. Özellik ve etiket sütunlarında kaç değer bulunduğunu görmek için veri kümenizi denetleyin ve gerçekleştirmeye çalıştığınız işlemin istatistiksel olarak geçerli olup olmadığını saptayın.  
  
 Kaynak veri kümesi geçerliyse, bazı yukarı akış veri işleme veya meta veri işleminin verileri değiştirilip değiştirilmediğini denetleyebilir ve bazı değerleri kaldırmış olabilirsiniz.  
  
 Yukarı akış işlemleri bölme, örnekleme veya yeniden örnekleme içeriyorsa, çıkışların beklenen satır ve değer sayısını içerdiğini doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Seçilen sayısal sütunların sayısı ve kategorik ve dize sütunlarındaki benzersiz değerler çok küçük.|  
|Seçilen sayısal sütunların ve kategorik ve dize sütunlarındaki benzersiz değerlerin toplam sayısı (Şu anda {0}) en az {1}|  
  

## <a name="error-0142"></a>Hata 0142  
 Sistem kimlik doğrulaması için sertifika yükleyemediğinde özel durum oluşur.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Sertifika yüklenemiyor.|  
|Sertifika {0} yüklenemiyor. Parmak izi {1}.|  
  

## <a name="error-0143"></a>Hata 0143  
 GitHub 'dan olması beklenen Kullanıcı tarafından sağlanmış URL ayrıştırılamıyor.  
  
 Azure Machine Learning içinde bu hata geçersiz bir URL belirttiğinizde ve modül geçerli bir GitHub URL 'SI gerektirdiğinde oluşur.  
  
**Çözüm:** URL 'nin geçerli bir GitHub deposuna başvurduğundan emin olun. Diğer site türleri desteklenmez.  
  
|Özel durum Iletileri|  
|------------------------|  
|URL github.com 'ten değil.|  
|URL, github.com öğesinden değil: {0}|  

## <a name="error-0144"></a>Hata 0144  
 Kullanıcı tarafından belirtilen GitHub URL 'sinde beklenen bölüm eksik.  
  
 Azure Machine Learning içindeki bu hata, geçersiz bir URL biçimi kullanarak bir GitHub dosya kaynağı belirttiğinizde oluşur.  
  
**Çözüm:** GitHub deposunun URL 'sinin geçerli olduğunu ve \blob\ veya \tree\\ile bitip bitmediğini denetleyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|GitHub URL 'SI ayrıştırılamıyor.|  
|GitHub URL 'SI ayrıştırılamıyor (depo adından sonra ' \blob\\' veya ' \tree\\' bekleniyor): {0}|  

## <a name="error-0145"></a>Hata 0145  
 Çoğaltma dizini bazı nedenlerle oluşturulamıyor.  
  
 Azure Machine Learning bu hata, modül belirtilen dizini oluşturamazsa oluşur.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Çoğaltma dizini oluşturulamıyor.|  
  

## <a name="error-0146"></a>Hata 0146  
 Kullanıcı dosyalarının sıkıştırması yerel dizine geri yüklendiğinde, Birleşik yol çok uzun olabilir.  
  
 Azure Machine Learning içindeki bu hata, dosyaları ayıkladığınızda, ancak sıkıştırılmış olmayan bazı dosya adları çok uzun olduğunda oluşur.  
  
**Çözüm:** Dosya adlarını, birleştirilmiş yolun ve dosya adının 248 karakterden uzun olmadığı şekilde düzenleyin.  
  
|Özel durum Iletileri|  
|------------------------|  
|Çoğaltma yolu 248 karakterden uzun, betik adını veya yolu kısaltın.|  

## <a name="error-0147"></a>Hata 0147  
 Bazı nedenlerle GitHub 'dan bir şey indirilemedi  
  
 Azure Machine Learning içindeki bu hata, GitHub 'dan belirtilen dosyaları okuyaveya indiremediğinde oluşur.  
  
**Çözüm:** Sorun geçici olabilir; dosyalara başka bir zamanda erişmeyi deneyebilirsiniz. Veya gerekli izinlere sahip olduğunuzu ve kaynağın geçerli olduğunu doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|GitHub erişim hatası.|  
|GitHub erişim hatası. {0}|  
  

## <a name="error-0148"></a>Hata 0148  
 Veri ayıklanırken veya dizin oluştururken yetkisiz erişim sorunları.  
  
 Azure Machine Learning bu hata, bir dizin oluşturmaya veya depolama alanından veri okumaya çalışırken, ancak gerekli izinlere sahip olmadığında oluşur.  
  
**Çözünürlüğüne**
  
|Özel durum Iletileri|  
|------------------------|  
|Veriler ayıklanırken yetkisiz erişim özel durumu oluştu.|  
  

## <a name="error-0149"></a>Hata 0149  
 Kullanıcı dosyası GitHub paketi içinde yok.  
  
 Azure Machine Learning bu hata, belirtilen dosya bulunamadığında oluşur.  
  
Çözüm: 
  
|Özel durum Iletileri|  
|------------------------|  
|GitHub dosyası bulunamadı.|  
|GitHub dosyası bulunamadı.: {0}|  
  

## <a name="error-0150"></a>Hata 0150  
 Kullanıcı paketinden gelen betikler, büyük olasılıkla GitHub dosyaları ile çakışmadan dolayı sıkıştırıldı.  
  
 Bu Azure Machine Learning hatası, genellikle aynı ada sahip mevcut bir dosya olduğunda bir komut dosyası ayıklanamaz oluşur.  
  
Çözüm:
  
|Özel durum Iletileri|  
|------------------------|  
|Paket açılamıyor; GitHub dosyaları ile olası ad çakışması.|  
  

## <a name="error-0151"></a>Hata 0151  
 Bulut depolama alanına yazılırken bir hata oluştu. URL 'YI denetleyin.  
  
 Azure Machine Learning bu hata, modül bulut depolama alanına veri yazmaya çalıştığında, ancak URL kullanılamıyor ya da geçersiz olduğunda oluşur.  
  
Çözüm: URL 'YI denetleyin ve yazılabilir olduğunu doğrulayın.  
  
|Özel durum Iletileri|  
|------------------------|  
|Bulut depolamaya yazma hatası (muhtemelen hatalı URL).|  
|Bulut depolama alanına yazılırken hata oluştu: {0}. URL 'yi denetleyin.|  
  
## <a name="error-0152"></a>Hata 0152  
 Azure bulut türü modül bağlamında yanlış belirtildi.  
  
|Özel durum iletileri|  
|------------------------|  
|Hatalı Azure bulut türü|  
|Hatalı Azure bulut türü: {0}|  
  
## <a name="error-0153"></a>Hata 0153  
 Belirtilen depolama uç noktası geçersiz.  
  
|Özel durum Iletileri|  
|------------------------|  
|Hatalı Azure bulut türü|  
|Hatalı depolama uç noktası: {0}|  

## <a name="error-0154"></a>Hata 0154  
 Belirtilen sunucu adı çözümlenemedi  
  
|Özel durum Iletileri|  
|------------------------|  
|Belirtilen sunucu adı çözümlenemedi|  
|Belirtilen sunucu {0}. documents.azure.com çözümlenemedi|

## <a name="error-0155"></a>Hata 0155  
 DocDb Istemcisi bir özel durum oluşturdu  
  
|Özel durum Iletileri|  
|------------------------|  
|DocDb Istemcisi bir özel durum oluşturdu|  
|DocDb Istemcisi: {0}|

## <a name="error-0156"></a>Hata 0156  
 HCatalog sunucusu için hatalı yanıt.  
  
|Özel durum Iletileri|  
|------------------------|  
|HCatalog sunucusu için hatalı yanıt. Tüm hizmetlerin çalışıp çalışmadığını denetleyin.|  
|HCatalog sunucusu için hatalı yanıt. Tüm hizmetlerin çalışıp çalışmadığını denetleyin. Hata ayrıntıları: {0}|

## <a name="error-0157"></a>Hata 0157  
 Tutarsız veya farklı belge şemaları nedeniyle Azure Cosmos DB okunurken bir hata oluştu. Okuyucu tüm belgelerin aynı şemaya sahip olmasını gerektirir.  
  
|Özel durum Iletileri|  
|------------------------|  
|Farklı şemalarda bulunan belgeler algılandı. Tüm belgelerin aynı şemaya sahip olduğundan emin olun|

## <a name="error-1000"></a>Hata 1000  
İç kitaplık özel durumu.  
  
Bu hata, işlenmemiş iç altyapı hatalarını yakalamak için sağlanır. Bu nedenle, hatayı oluşturan modüle bağlı olarak bu hatanın nedeni farklı olabilir.  
  
Daha fazla yardım almak için, hataya eşlik eden ayrıntılı iletiyi, giriş olarak kullanılan veriler de dahil olmak üzere senaryonun açıklamasıyla birlikte Azure Machine Learning forumuna nakletmeniz önerilir. Bu geri bildirim, hataları önceliklendirmemize ve daha fazla iş için en önemli sorunları belirlemenize yardımcı olur.  
  
|Özel durum Iletileri|  
|------------------------|  
|Kitaplık özel durumu.|  
|Kitaplık özel durumu: {0}|  
|{0} Kitaplığı özel durumu: {1}|  
