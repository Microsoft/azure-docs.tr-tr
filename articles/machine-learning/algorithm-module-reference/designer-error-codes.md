---
title: Tasarımcı modülü hatalarında sorun giderme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning tasarımcısında otomatikleştirilmiş modül hata kodlarını nasıl okuyabileceğinizi ve giderebileceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 11/25/2020
ms.openlocfilehash: b917e3fc93c59de85c5236c18e31d7bbc9d891f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98065482"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>Tasarımcı için özel durumlar ve hata kodları

Bu makalede, Machine Learning işlem hatlarınızı gidermenize yardımcı olmak üzere Azure Machine Learning tasarımcısında hata iletileri ve özel durum kodları açıklanmaktadır.

Aşağıdaki adımları izleyerek tasarımcıda hata iletisini bulabilirsiniz:  

- Başarısız modülünü seçin, **çıktılar + Günlükler** sekmesine gidin, ayrıntılı günlüğü, **70_driver_log.txt** dosyasında, **azureml-logs** kategorisi altında bulabilirsiniz.

- Ayrıntılı modül hatası için **module_statistics** kategorisi altındaki error_info.jskontrol edebilirsiniz.

Tasarımcı 'daki modül hata kodları aşağıda verilmiştir.

## <a name="error-0001"></a>Hata 0001  
 Belirli bir veya daha fazla veri kümesi sütunu bulunamazsa özel durum oluşur.  

 Bu hatayı bir modül için bir sütun seçimi yapılırsa, ancak seçili sütunlar giriş veri kümesinde yoksa alırsınız. Bu hata, bir sütun adına el ile girdiğiniz veya sütun seçici, işlem hattını çalıştırdığınızda veri kümeniz içinde olmayan önerilen bir sütun sağladıysa meydana gelebilir.  

**Çözüm:** Bu özel durumu oluşturan modülün yeniden ziyaret edin ve sütun adının veya adlarının doğru olduğunu ve başvurulan tüm sütunların mevcut olduğunu doğrulayın.  

|Özel durum Iletileri|
|------------------------|
|Belirtilen bir veya daha fazla sütun bulunamadı.|
|"{Column_id}" adına veya dizinine sahip sütun bulunamadı.|
|"{Arg_name_missing_column}" içinde "{column_id}" adına veya dizinine sahip sütun yok.|
|"{Column_id}" adına veya dizinine sahip sütun "{arg_name_missing_column}" içinde yok, ancak "{arg_name_has_column}" içinde var.|
|"{Column_names}" adına veya dizinine sahip sütunlar bulunamadı.|
|"{Column_names}" adına veya dizinine sahip sütunlar "{arg_name_missing_column}" içinde yok.|
|"{Column_names}" adına veya dizinine sahip sütunlar "{arg_name_missing_column}" içinde yok, ancak "{arg_name_has_column}" içinde var.|


## <a name="error-0002"></a>Hata 0,0002  
 Bir veya daha fazla parametre ayrıştırılamadıysanız veya belirtilen türden hedef Yöntem türüne göre gerekli bir şekilde dönüştürülemiyorsa özel durum oluşur.  

 Bu hata, giriş olarak bir parametre belirttiğinizde ve değer türü beklenen türden farklıysa ve örtük dönüştürme gerçekleştirilemediği zaman Azure Machine Learning oluşur.  

**Çözüm:** Modül gereksinimlerini denetleyin ve hangi değer türünün gerekli olduğunu (dize, tamsayı, Çift, vb.) saptayın.  

|Özel durum Iletileri|
|------------------------|
|Parametre ayrıştırılamadı.|
|"{Arg_name_or_column}" parametresi ayrıştırılamadı.|
|"{Arg_name_or_column}" parametresi "{to_type}" olarak dönüştürülemedi.|
|"{From_type}" olan "{arg_name_or_column}" parametresi "{to_type}" olarak dönüştürülemedi.|
|"{Arg_name_or_column}" "{arg_value}" parametre değeri "{from_type}" iken "{to_type}" olarak dönüştürülemedi.|
|"{Arg_name_or_column from_type}" sütunundaki "{arg_value}" değeri, belirtilen "{FMT}" biçiminin kullanımıyla "{to_type}" olarak dönüştürülemedi.|


## <a name="error-0003"></a>Hata 0003  
 Bir veya daha fazla giriş null veya boş olduğunda özel durum oluşur.  

 Bu hatayı, bir modüle yönelik herhangi bir giriş veya parametre null ya da boş olduğunda Azure Machine Learning alırsınız.  Bu hata, örneğin, bir parametre için herhangi bir değer yazmadınız meydana gelebilir. Ayrıca, eksik değerlere veya boş bir veri kümesine sahip bir veri kümesi seçerseniz da bu durum oluşabilir.  

**Çözüm:**

+ Özel durumu üreten modülü açın ve tüm girişlerin belirtildiğini doğrulayın. Tüm gerekli girişlerin belirtildiğinden emin olun. 
+ Azure depolama 'dan yüklenen verilerin erişilebilir olduğundan ve hesap adının veya anahtarın değişmediğinden emin olun.  
+ Eksik değerler veya null değerleri için giriş verilerini denetleyin.
+ Veri kaynağında bir sorgu kullanılıyorsa, verilerin beklediğinizi biçimde döndürüldüğünden emin olun. 
+ Veri belirtimindeki yazım hatalarını veya diğer değişiklikleri denetleyin.
  
|Özel durum Iletileri|
|------------------------|
|Bir veya daha fazla giriş null veya boş.|
|"{Name}" girişi null veya boş.|


## <a name="error-0004"></a>Hata 0,0004  
 Parametre belirli bir değerden küçük veya ona eşitse özel durum oluşur.  

 İletideki parametre, modülün verileri işlemesi için gereken bir sınır değerinin altındaysa, bu hatayı Azure Machine Learning alırsınız.  

**Çözüm:** Özel durumu oluşturan modülün yeniden ziyaret edin ve parametreyi belirtilen değerden daha büyük olacak şekilde değiştirin.  

|Özel durum Iletileri|
|------------------------|
|Parametre sınır değerinden büyük olmalıdır.|
|"{Arg_name}" parametresinin değeri {lower_boundary} değerinden büyük olmalıdır.|
|"{Arg_name}" parametresi, {lower_boundary} değerinden büyük olması gereken "{actual_value}" değerine sahip.|


## <a name="error-0005"></a>Hata 0005  
 Parametre belirli bir değerden küçükse özel durum oluşur.  

 İletideki parametre, modülün verileri işlemesi için gereken bir sınır değerine eşitse veya eşitse, bu hatayı Azure Machine Learning alırsınız.  

**Çözüm:** Özel durumu oluşturan modülü yeniden ziyaret edin ve parametreyi belirtilen değere eşit veya ondan büyük olacak şekilde değiştirin.  

|Özel durum Iletileri|
|------------------------|
|Parametre, sınır değerinden büyük veya buna eşit olmalıdır.|
|"{Arg_name}" parametresinin değeri {lower_boundary} değerinden büyük veya buna eşit olmalıdır.|
|"{Arg_name}" parametresi, {lower_boundary} değerinden büyük veya buna eşit olması gereken "{Value}" değerine sahip.|


## <a name="error-0006"></a>Hata 0006  
 Parametre belirtilen değerden büyük veya bu değere eşitse özel durum oluşur.  

 İletideki parametre, modülün verileri işlemesi için gereken sınır değerinden büyük veya bu değere eşitse Azure Machine Learning bu hatayı alırsınız.  

**Çözüm:** Özel durumu oluşturan modülün yeniden ziyaret edin ve parametreyi belirtilen değerden daha az olacak şekilde değiştirin.  

|Özel durum Iletileri|
|------------------------|
|Parametre uyumsuzluğu. Parametrelerden biri başka bir değerden küçük olmalıdır.|
|"{Arg_name}" parametresinin değeri "{upper_boundary_parameter_name}" değerinden küçük olmalıdır.|
|"{Arg_name}" parametresi, {upper_boundary_parameter_name} değerinden az olması gereken "{Value}" değerine sahip.|


## <a name="error-0007"></a>Hata 0007  
 Parametre belirli bir değerden daha büyükse özel durum oluşur.  

 Bu hatayı, modülün özelliklerinde izin verilenden daha büyük bir değer belirtenden Azure Machine Learning alırsınız. Örneğin, desteklenen tarihler aralığının dışında bir veri belirtebilir veya yalnızca üç sütun kullanılabilir olduğunda beş sütun kullanıldığını gösterebilirsiniz. 

 Bu hatayı, bir şekilde eşleşmesi gereken iki veri kümesi belirtiyorsanız de görebilirsiniz. Örneğin, sütunları yeniden adlandırıyorsanız ve sütunları dizine göre belirtirseniz, sağladığınız adların sayısı sütun dizini sayısıyla eşleşmelidir. Diğer bir örnek, iki sütun kullanan bir matematik işlemi olabilir, burada sütunlar aynı sayıda satıra sahip olmalıdır. 

**Çözüm:**

 + Söz konusu modülü açın ve tüm sayısal özellik ayarlarını gözden geçirin.
 + Herhangi bir parametre değerinin, bu özellik için desteklenen değer aralığı içinde olduğundan emin olun.
 + Modül birden çok giriş alırsa, girişlerin aynı boyutta olduğundan emin olun.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Veri kümesinin veya veri kaynağının değiştirilip değiştirilmediğini denetleyin. Bazen, verilerin önceki bir sürümüyle çalışan bir değer, sütun sayısı, sütun veri türleri veya verilerin boyutu değiştirildikten sonra başarısız olur.  

|Özel durum iletileri|
|------------------------|
|Parametre uyumsuzluğu. Parametrelerden biri başka bir değerden küçük veya buna eşit olmalıdır.|
|"{Arg_name}" parametresinin değeri "{upper_boundary_parameter_name}" değerinden küçük veya bu değere eşit olmalıdır.|
|"{Arg_name}" parametresi, {upper_boundary} değerinden küçük veya buna eşit olması gereken "{actual_value}" değerine sahip.|
|"{Arg_name}" parametresinin "{actual_value} parametresi," {upper_boundary_parameter_name} "değerinden {upper_boundary} değerinden küçük veya bu değere eşit olmalıdır.|
|"{Arg_name}" parametresi {actual_value} değeri {upper_boundary_meaning} değeri {upper_boundary} değerinden küçük veya buna eşit olmalıdır.|


## <a name="error-0008"></a>Hata 0008  
 Parametre Aralık içinde değilse özel durum oluşur.  

 İletideki parametre, modülün verileri işlemesi için gereken sınırların dışındaysa Azure Machine Learning bu hatayı alırsınız.  

 Örneğin, farklı sayıda sütuna sahip iki veri kümesini birleştirmek için [satır ekle](add-rows.md) seçeneğini kullanmayı denerseniz bu hata görüntülenir.  

**Çözüm:** Özel durumu oluşturan modülün yeniden ziyaret edin ve parametreyi belirtilen aralık dahilinde olacak şekilde değiştirin.  

|Özel durum Iletileri|
|------------------------|
|Parametre değeri belirtilen aralıkta değil.|
|"{Arg_name}" parametresinin değeri Aralık içinde değil.|
|"{Arg_name}" parametresinin değeri [{lower_boundary}, {upper_boundary}] aralığında olmalıdır.|
|"{Arg_name}" parametresinin değeri Aralık içinde değil. yüzden|


## <a name="error-0009"></a>Hata 0009  
 Azure depolama hesabı adı veya kapsayıcı adı yanlış belirtildiğinde özel durum oluşur.  

Bu hata, bir Azure depolama hesabının parametrelerini belirttiğinizde, ancak ad veya parola çözümlenemediğinde Azure Machine Learning tasarımcısında oluşur. Parolalardan veya hesap adlarındaki hatalar birçok nedenden kaynaklanabilir:

 + Hesap yanlış türde. Machine Learning Designer ile kullanım için bazı yeni hesap türleri desteklenmez. Ayrıntılar için bkz. [verileri Içeri aktarma](import-data.md) .
 + Yanlış hesap adı girdiniz
 + Hesap artık yok
 + Depolama hesabının parolası yanlış veya değişmiş
 + Kapsayıcı adını belirtmediniz veya kapsayıcı yok
 + Dosya yolunu (Blobun yolu) tam olarak belirtmediniz
   

**Çözüm:**

Bu tür sorunlar genellikle hesap adını, parolayı veya kapsayıcı yolunu el ile girmeye çalıştığınızda oluşur. [Veri Içeri aktarma](import-data.md) modülü için yeni Sihirbazı kullanmanızı öneririz. Bu, adları bulmanıza ve denetlemeye yardımcı olur.

Ayrıca hesabın, kapsayıcının veya Blobun silinip silinmediğini denetleyin. Hesap adının ve parolanın doğru girildiğini ve kapsayıcının mevcut olduğunu doğrulamak için başka bir Azure depolama yardımcı programı kullanın. 

Bazı yeni hesap türleri Azure Machine Learning tarafından desteklenmez. Örneğin, yeni "sık erişimli" veya "soğuk" Depolama türleri makine öğrenimi için kullanılamaz. Klasik depolama hesapları ve "genel amaçlı" olarak oluşturulan depolama hesapları iyi çalışır.

Bir Blobun yolun tamamı belirtilmişse, yolun **kapsayıcı/blobname** olarak belirtildiğinden ve hem kapsayıcının hem de Blobun hesapta bulunduğundan emin olun.  

 Yol önünde eğik çizgi içermemelidir. Örneğin **/Container/blob** yanlış ve **kapsayıcı/blob** olarak girilmelidir.  


|Özel durum Iletileri|
|------------------------|
|Azure depolama hesabı adı veya kapsayıcı adı yanlış.|
|"{Account_name}" adlı Azure depolama hesabı veya "{container_name}" kapsayıcı adı yanlış; kapsayıcı/blob biçim kapsayıcısı adı bekleniyordu.|


## <a name="error-0010"></a>Hata 0010  
 Giriş veri kümelerinde eşleşmesi gereken ancak olmayan sütun adları varsa özel durum oluşur.  

 İletideki sütun dizininde iki giriş veri kümesi içinde farklı sütun adları varsa Azure Machine Learning bu hatayı alırsınız.  

**Çözüm:** Belirtilen sütun dizini için aynı sütun adına sahip olması için [meta verileri Düzenle](edit-metadata.md) veya özgün veri kümesini Değiştir ' i kullanın.  

|Özel durum Iletileri|
|------------------------|
|Giriş veri kümelerinde karşılık gelen dizine sahip sütunlarda farklı adlar vardır.|
|Sütun adları, giriş veri kümelerinin ({DataSet1} ve {DataSet2} sırasıyla) {col_index} (sıfır tabanlı) sütunu için aynı değil.|


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
|Belirtilen "{column_set}" sütun kümesi herhangi bir veri kümesi sütunu için uygulanmıyor.|


## <a name="error-0012"></a>Hata 0012  
 Geçirilen bağımsız değişkenler kümesiyle sınıf örneği oluşturuoluşturulamadığı takdirde özel durum oluşur.  

**Çözüm:** Bu hata Kullanıcı tarafından işlem yapılabilir değildir ve gelecek sürümde kullanım dışı olacaktır.  

|Özel durum Iletileri|
|------------------------|
|Eğitilen model, lütfen önce modeli eğitme.|
|Eğitilen model ({arg_name}), eğitilen modeli kullanın.|


## <a name="error-0013"></a>Hata 0013  
 Modüle geçirilen Learner geçersiz bir tür ise özel durum oluşur.  

 Eğitilen bir model bağlı Puanlama modülüyle uyumsuz olduğunda bu hata oluşur. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Çözüm:**

Eğitim modülü tarafından üretilen öğrenici türünü ve öğrenici için uygun Puanlama modülünü saptayın. 

Model, özelleştirilmiş eğitim modüllerinden herhangi birini kullanarak eğitilolduysa, eğitilen modeli yalnızca ilgili özelleştirilmiş Puanlama modülüne bağlayın. 


|Model türü|Eğitim modülü| Puanlama modülü|
|----|----|----|
|herhangi bir sınıflandırıcı|[Model Eğitme](train-model.md) |[Puanlama Modeli](score-model.md)|
|herhangi bir regresyon modeli|[Model Eğitme](train-model.md) |[Puanlama Modeli](score-model.md)|

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
|Learner "{arg_name}" geçersiz türe sahip.|
|"{Arg_name}" öğrenner "{learner_type}" türü geçersiz.|
|Geçersiz tür öğrenici geçildi. Özel durum iletisi: {exception_message}|


## <a name="error-0014"></a>Hata 0014  
 Sütun benzersiz değerlerinin sayısı izin verilenden fazlaysa özel durum oluşur.  

 Bir sütun, KIMLIK sütunu veya metin sütunu gibi çok sayıda benzersiz değer içerdiğinde bu hata oluşur. Bir sütunun kategorik veriler olarak işleneceğini belirtirseniz ancak işlemin tamamlanmasına izin vermek için sütunda çok sayıda benzersiz değer varsa, bu hatayı görebilirsiniz. Ayrıca, iki girişte benzersiz değer sayısı arasında uyuşmazlık varsa bu hatayı görebilirsiniz.   

Aşağıdaki koşulların **her ikisi de** varsa, benzersiz değerlerin hatası izin verilenden fazla olur:

- Bir sütunun %97 ' inden fazlası benzersiz değerlerdir, bu da neredeyse tüm kategorilerin birbirinden farklı olduğu anlamına gelir.
- Bir sütunda 1000 ' den fazla benzersiz değer vardır.

**Çözüm:**

Hatayı oluşturan modülü açın ve giriş olarak kullanılan sütunları tespit edin. Bazı modüller için, veri kümesi girişine sağ tıklayıp, benzersiz değer sayısı ve bunların dağılımı dahil olmak üzere tek tek sütunlarda istatistik almak için **Görselleştir** ' i seçebilirsiniz.

Gruplandırma veya kategorilere ayırma için kullanmayı düşündüğünüz sütunlarda, sütunlardaki benzersiz değerlerin sayısını azaltmak için gerekli adımları uygulayın. Sütunun veri türüne bağlı olarak farklı şekillerde azaltabilirsiniz. 

Model eğitimi sırasında anlamlı Özellikler bulunmayan KIMLIK sütunları için, bu sütunu **Temizle özelliği** olarak Işaretlemek üzere [meta verileri Düzenle](../algorithm-module-reference/edit-metadata.md) ' yi kullanabilirsiniz ve model eğitimi sırasında kullanılmaz. 

Metin sütunlarında, metin sütunlarını önceden işlemek için [özellik karma özelliğini](../algorithm-module-reference/feature-hashing.md) kullanabilir veya [metin modülünden N-gram özelliklerini ayıklayabilirsiniz](../algorithm-module-reference/extract-n-gram-features-from-text.md) .
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Senaryoünüzle eşleşen bir çözüm bulunamıyor mu? Hatayı oluşturan modülün adını ve sütunun veri türünü ve kardinalitesini içeren bu konu hakkında geri bildirim sağlayabilirsiniz. Yaygın senaryolar için daha hedeflenmiş sorun giderme adımları sağlamak üzere bu bilgileri kullanacağız.   

|Özel durum Iletileri|
|------------------------|
|Sütun benzersiz değerlerinin miktarı izin verilenden daha büyük.|
|"{Column_name}" sütunundaki benzersiz değer sayısı izin verilenden büyük.|
|Sütundaki benzersiz değer sayısı: "{column_name}", {limit} demet sayısını aşıyor.|


## <a name="error-0015"></a>Hata 0015  
 Veritabanı bağlantısı başarısız olursa özel durum oluşur.  

 Yanlış bir SQL hesabı adı, parola, veritabanı sunucusu veya veritabanı adı veya veritabanı ya da sunucu sorunları nedeniyle veritabanıyla bağlantı kurulamazsa bu hatayı alırsınız.  

**Çözüm:** Hesap adı, parola, veritabanı sunucusu ve veritabanının doğru girildiğini ve belirtilen hesabın doğru izin düzeyine sahip olduğunu doğrulayın. Veritabanına Şu anda erişilebildiğini doğrulayın.  

|Özel durum Iletileri|
|------------------------|
|Veritabanı bağlantısı kurulurken hata oluştu.|
|Veritabanı bağlantısı kurulurken hata oluştu: {connection_str}.|


## <a name="error-0016"></a>Hata 0016  
 Modüle geçirilen giriş veri kümelerinin uyumlu sütun türleri olması, ancak bunu olmaması durumunda özel durum oluşur.  

 İki veya daha fazla veri kümesine geçirilen sütunların türleri birbirleriyle uyumlu değilse, bu hatayı Azure Machine Learning alırsınız.  

**Çözüm:** [Meta verileri düzenleme](edit-metadata.md) veya özgün giriş veri kümesini değiştirme<!--, or use [Convert to Dataset](convert-to-dataset.md)--> sütun türlerinin uyumlu olduğundan emin olmak için.  

|Özel durum Iletileri|
|------------------------|
|Giriş veri kümelerinde karşılık gelen dizine sahip sütunlarda uyumsuz türler vardır.|
|' {First_col_names} ' sütunları eğitme ve test verileri arasında uyumsuz.|
|' {First_col_names} ' ve ' {second_col_names} ' sütunları uyumsuz.|
|Sütun öğesi türleri, giriş veri kümelerinin ({first_dataset_names} ve {second_dataset_names} sırasıyla) ' {first_col_names} ' sütunu (sıfır tabanlı) için uyumlu değil.|


## <a name="error-0017"></a>Hata 0017  
 Seçili bir sütun, geçerli modül tarafından desteklenmeyen bir veri türü kullanıyorsa özel durum oluşur.  

 Örneğin, sütun seçiminiz, bir Math işleminin dize sütunu veya bir categorik özellik sütununun gerekli olduğu bir puan sütunu gibi, modül tarafından işlenemediği bir veri türüne sahip bir sütun içeriyorsa Azure Machine Learning bu hatayı alabilirsiniz.  

**Çözüm:**
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
|{Col_type} türünde sütun işlenemiyor. Tür modül tarafından desteklenmiyor.|
|{Col_type} türündeki "{col_name}" sütunu işlenemiyor. Tür modül tarafından desteklenmiyor.|
|{Col_type} türündeki "{col_name}" sütunu işlenemiyor. Tür modül tarafından desteklenmiyor. Parametre adı: {arg_name}.|


## <a name="error-0018"></a>Hata 0018  
 Giriş veri kümesi geçerli değilse özel durum oluşur.  

**Çözüm:** Azure Machine Learning bu hata birçok bağlamda görünebilir, bu nedenle tek bir çözüm yoktur. Genellikle hata, bir modüle giriş olarak girilen verilerin yanlış sayıda sütuna sahip olduğunu veya veri türünün modül gereksinimleriyle eşleşmediğinden emin olduğunu gösterir. Örnek:  

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
|{DataSet1} geçersiz veri içeriyor.|
|{DataSet1} ve {DataSet2} tutarlı bir columnwise olmalıdır.|
|{DataSet1} geçersiz veriler içeriyor, {Reason}.|
|{DataSet1}, {invalid_data_category} içeriyor. {troubleshoot_hint}|
|{DataSet1} geçerli değil, {Reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>Hata 0019  
 Sütunun sıralanmış değerleri içermesi bekleniyorsa, özel durum oluşur, ancak bunu yapmaz.  

 Belirtilen sütun değerleri sıra dışı ise bu hatayı Azure Machine Learning alırsınız.  

**Çözüm:** Giriş veri kümesini el ile değiştirerek sütun değerlerini sıralayın ve modülü yeniden çalıştırın.  

|Özel durum Iletileri|
|------------------------|
|Sütundaki değerler sıralanmaz.|
|"{Col_index}" sütunundaki değerler sıralanmaz.|
|"{DataSet}" veri kümesinin "{col_index}" sütunundaki değerler sıralanmaz.|
|"{Arg_name}" bağımsız değişkenindeki değerler "{sorting_order}" sırasında sıralanmıyor.|


## <a name="error-0020"></a>Hata 0020  
 Modüle geçirilen bazı veri kümelerinde sütun sayısı çok küçük olduğunda özel durum oluşur.  

 Bu hatayı, bir modül için yeterli sütun seçilmemişse Azure Machine Learning alırsınız.  

**Çözüm:** Modülü yeniden ziyaret edin ve sütun seçicinin doğru sayıda sütun seçildiğinden emin olun.  

|Özel durum Iletileri|
|------------------------|
|Giriş veri kümesindeki sütun sayısı izin verilen en küçük değerden küçük.|
|"{Arg_name}" giriş veri kümesindeki sütun sayısı izin verilen en küçük değerden küçük.|
|Giriş veri kümesindeki sütun sayısı, izin verilen en az {required_columns_count} sütundan daha az.|
|"{Arg_name}" giriş veri kümesindeki sütun sayısı, izin verilen en az {required_columns_count} sütundan daha az.|


## <a name="error-0021"></a>Hata 0021  
 Modüle geçirilen bazı veri kümelerinde satır sayısı çok küçük olduğunda özel durum oluşur.  

 Bu hata, veri kümesinde belirtilen işlemi gerçekleştirmek için yeterli miktarda satır olmadığında Azure Machine Learning görülür. Örneğin, giriş veri kümesi boşsa veya en az sayıda satırın geçerli olması gereken bir işlem gerçekleştirmeye çalışıyorsanız bu hatayı görebilirsiniz. Bu gibi işlemler, istatistiksel yöntemlere, belirli bir tür bindürme ve saymayla öğrenlerine göre gruplandırma veya sınıflandırmayı içerebilir (ancak bunlarla sınırlı değildir).  

**Çözüm:**

 + Hatayı döndüren modülü açın ve giriş veri kümesini ve modül özelliklerini denetleyin. 
 + Giriş veri kümesinin boş olmadığını ve modül yardımı 'nda açıklanan gereksinimleri karşılayacak yeterli veri satırı olduğunu doğrulayın.  
 + Verileriniz bir dış kaynaktan yüklenirse, veri kaynağının kullanılabilir olduğundan ve veri tanımında içeri aktarma işleminin daha az satır almasını sağlayan bir hata veya değişiklik olmadığından emin olun.
 + Modülün veri türünü veya Temizleme, bölme veya ekleme işlemleri gibi değer sayısını etkileyebilecek bir işlem gerçekleştiriyorsanız, döndürülen satır sayısını öğrenmek için bu işlemlerin çıkışlarını kontrol edin.  

|Özel durum Iletileri|
|------------------------|
|Giriş veri kümesindeki satır sayısı izin verilen en küçük değerden küçük.|
|Giriş veri kümesindeki satır sayısı, izin verilen en az {required_rows_count} satırdan daha az.|
|Giriş veri kümesindeki satır sayısı, izin verilen en az {required_rows_count} satırdan daha az. yüzden|
|"{Arg_name}" giriş veri kümesindeki satır sayısı, izin verilen en az {required_rows_count} satırdan küçük.|
|"{Arg_name}" giriş veri kümesindeki satır sayısı {actual_rows_count}, izin verilen en az {required_rows_count} satırdan az.|
|"{Arg_name}" giriş veri kümesindeki "{row_type}" satır sayısı {actual_rows_count}, izin verilen en az {required_rows_count} satırdan küçük.|


## <a name="error-0022"></a>Hata 0022  
 Giriş veri kümesindeki Seçili sütunların sayısı beklenen sayıya eşit değilse özel durum oluşur.  

 Azure Machine Learning içinde bu hata, aşağı akış modülü veya işlemi belirli sayıda sütun veya giriş gerektirdiğinde ve çok az veya çok fazla sayıda sütun veya giriş sağladıysanız oluşabilir. Örnek:  

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
|Giriş veri kümesindeki Seçili sütunların sayısı {expected_col_count} değerine eşit değil.|
|"{Selection_pattern_friendly_name}" sütun seçim deseninin, giriş veri kümesinde {expected_col_count} öğesine eşit değil, seçili sütun sayısı sağlar.|
|"{Selection_pattern_friendly_name}" sütun seçimi deseninin giriş veri kümesinde seçili {expected_col_count} sütun sağlaması bekleniyor, ancak {selected_col_count} sütun (ler) i gerçekten sağlanmıştır.|


## <a name="error-0023"></a>Hata 0023  

Giriş veri kümesinin hedef sütunu geçerli eğitmen modülü için geçerli değilse özel durum oluşur.  

Azure Machine Learning içindeki bu hata, hedef sütun (modül parametrelerinde seçildiği gibi) geçerli veri türünde değilse, eksik tüm değerler içermediğinde veya beklendiği gibi kategorilendiğinde oluşur.  

**Çözüm:** Etiket/hedef sütununun içeriğini incelemek için modül girişini yeniden ziyaret edin. Tüm eksik değerleri içermediğinden emin olun. Modül hedef sütunun kategorik olmasını bekliyorsanız, hedef sütununda birden fazla farklı değer bulunduğundan emin olun.  

|Özel durum Iletileri|
|------------------------|
|Giriş veri kümesinde desteklenmeyen hedef sütun yok.|
|Giriş veri kümesinde desteklenmeyen "{column_index}" hedef sütunu yok.|
|Giriş veri kümesinde {learner_type} türünde Learner için desteklenmeyen "{column_index}" hedef sütunu yok.|


## <a name="error-0024"></a>Hata 0024  
DataSet bir etiket sütunu içermiyorsa özel durum oluşur.  

 Azure Machine Learning bu hata, modül bir etiket sütunu gerektirdiğinde ve DataSet 'in bir etiket sütunu olmadığında oluşur. Örneğin, bir puanlanmış veri kümesinin değerlendirilmesi genellikle işlem doğruluğu ölçümlerinin bir etiket sütununun olmasını gerektirir.  

Ayrıca, bir etiket sütununun veri kümesinde mevcut olması, ancak Azure Machine Learning tarafından doğru algılanmadığını da ortaya çıkabilir.

**Çözüm:**

+ Hatayı oluşturan modülü açın ve bir etiket sütununun mevcut olup olmadığını saptayın. Sütunun adı veya veri türü, tahmin etmeye çalıştığınız tek bir sonuç (veya bağımlı değişken) içerdiği sürece önemi yoktur. Hangi sütunun etikete sahip olduğundan emin değilseniz,  *Class* veya *target* gibi genel bir adı arayın. 
+  Veri kümesi bir etiket sütunu içermiyorsa, etiket sütununun açık olarak veya yanlışlıkla yukarı akış kaldırılmış olması mümkündür. Ayrıca, veri kümesi bir yukarı akış Puanlama modülünün çıkışı değildir.
+ Sütunu etiket sütunu olarak açıkça işaretlemek için, [meta veri düzenleme](edit-metadata.md) modülünü ekleyin ve veri kümesini bağlayın. Yalnızca etiket sütununu seçin ve **alanlar** açılan listesinden **etiket** ' i seçin. 
+ Etiket olarak yanlış sütun seçilirse, sütundaki meta verileri düzeltebilmeniz için alanlardan **etiketi temizle** ' yi  seçebilirsiniz. 
  
|Özel durum Iletileri|
|------------------------|
|Veri kümesinde etiket sütunu yok.|
|"{Dataset_name}" içinde etiket sütunu yok.|


## <a name="error-0025"></a>Hata 0025  
 Veri kümesinde bir puan sütunu yoksa özel durum oluşur.  

 Azure Machine Learning içindeki bu hata, değerlendir modeli girişi geçerli puan sütunları içermiyorsa oluşur. Örneğin, Kullanıcı, doğru eğitilen bir modelle puanlanmadan önce bir veri kümesini değerlendirmeye çalışır veya puan sütunu açıkça yukarı akış olarak bırakılmıştı. Bu özel durum, iki veri kümelerinde puan sütunları uyumsuzsa da oluşur. Örneğin, bir ikili sınıflandırıcı ile doğrusal bir gerileme doğruluğunu karşılaştırmaya çalışıyor olabilirsiniz.  

**Çözüm:** Girdiyi değerlendir modeline yeniden ziyaret edin ve bir veya daha fazla puan sütunu içerip içermediğinden inceleyin. Aksi takdirde, veri kümesi puanlanmadı veya bir yukarı akış modülünde puan sütunları bırakılmıştı.  

|Özel durum Iletileri|
|------------------------|
|Veri kümesinde puan sütunu yok.|
|"{Dataset_name}" içinde puan sütunu yok.|
|"{Learner_type}" tarafından üretilen "{dataset_name}" içinde puan sütunu yok. Doğru öğrenner türünü kullanarak veri kümesini puan edin.|


## <a name="error-0026"></a>Hata 0026  
 Aynı ada sahip sütunlara izin verilmiyorsa özel durum oluşur.  

 Aynı ada sahip birden çok sütun varsa Azure Machine Learning bu hata oluşur. Bu hatayı alabileceğiniz bir yol, veri kümesinin bir başlık satırına sahip olmaması ve sütun adlarının otomatik olarak atanabileceği bir yoldur: Col0, Sütun1, vb.  

**Çözüm:** Sütunlar aynı ada sahip ise, giriş veri kümesi ve modülü arasına bir [veri düzenleme](edit-metadata.md) modülü ekleyin. Yeni adları **Yeni sütun adları** metin kutusuna yazarak yeniden adlandırılacak sütunları seçmek Için [meta verileri Düzenle](edit-metadata.md) ' de sütun seçiciyi kullanın.  

|Özel durum Iletileri|
|------------------------|
|Bağımsız değişkenlerde eşit sütun adları belirtilmiştir. Modül tarafından eşit sütun adlarına izin verilmiyor.|
|"{Arg_name_1}" ve "{arg_name_2}" bağımsız değişkenlerinde eşit sütun adlarına izin verilmez. Lütfen farklı adlar belirtin.|


## <a name="error-0027"></a>Hata 0027  
 İki nesnenin aynı boyutta olması, ancak bunların olmaması durumunda özel durum oluşur.  

 Bu, Azure Machine Learning yaygın olarak karşılaşılan bir hatadır ve birçok koşuldan kaynaklanıyor olabilir.  

**Çözüm:** Belirli bir çözüm yoktur. Ancak, aşağıdakiler gibi koşulları kontrol edebilirsiniz:  

-   Sütunları yeniden adlandırıyorsanız her bir listenin (girdi sütunları ve yeni adların listesi) aynı sayıda öğeye sahip olduğundan emin olun.  
  
-   İki veri kümesini birleştiriyorsanız veya birleştirirken aynı şemaya sahip olduklarından emin olun.  
  
-   Birden çok sütunu olan iki veri kümesini birleştiriyorsanız, anahtar sütunlarının aynı veri türüne sahip olduğundan emin olun ve **seçimdeki yinelenen öğelere Izin ver ve sütun sırasını koru** seçeneğini belirleyin.  

|Özel durum Iletileri|
|------------------------|
|Geçirilen nesnelerin boyutu tutarsız.|
|"{Friendly_name1}" boyutu "{friendly_name2}" boyutuyla tutarsız.|


## <a name="error-0028"></a>Hata 0028  
 Sütun kümesi yinelenen sütun adları içerdiğinde ve buna izin verilmediği durumlarda özel durum oluşur.  

 Azure Machine Learning bu hata, sütun adları çoğaltıldığında oluşur; Yani, benzersiz değildir.  

**Çözüm:** Herhangi bir sütun aynı ada sahip ise, giriş veri kümesi ve hatayı ortaya geçiren modül arasında bir [düzenleme meta verileri](edit-metadata.md) örneği ekleyin. Yeniden adlandırılacak sütunları seçmek için [meta verileri Düzenle](edit-metadata.md) ' de sütun seçiciyi kullanın ve yeni **sütun** adları metin kutusuna yeni sütun adlarını yazın. Birden çok sütunu yeniden adlandırıyorsanız, **Yeni sütun adlarına** yazdığınız değerlerin benzersiz olduğundan emin olun.  

|Özel durum Iletileri|
|------------------------|
|Sütun kümesi yinelenen sütun adları içeriyor.|
|"{Duplicated_name}" adı yineleniyor.|
|"{Duplicated_name}" adı "{arg_name}" içinde yineleniyor.|
|"{Duplicated_name}" adı yineleniyor. Ayrıntılar: {details}|


## <a name="error-0029"></a>Hata 0029  
 Geçersiz URI geçirildiğinde özel durum oluşur.  

 Geçersiz URI geçirildiğinde Azure Machine Learning bu hata oluşur.  Aşağıdaki koşullardan herhangi biri doğru olduğunda bu hatayı alırsınız:  

-   Okuma veya yazma için Azure Blob depolama için belirtilen genel veya SAS URI 'SI bir hata içeriyor.  
  
-   SAS için zaman penceresinin süresi doldu.  
  
-   HTTP kaynağı aracılığıyla Web URL 'SI bir dosyayı veya geri döngü URI 'sini temsil eder.  
  
-   HTTP üzerinden Web URL 'SI hatalı biçimlendirilmiş bir URL içerir.  
  
-   URL, uzak kaynak tarafından çözümlenemiyor.  

**Çözüm:** Modülü yeniden ziyaret edin ve URI 'nin biçimini doğrulayın. Veri kaynağı HTTP aracılığıyla bir Web URL 'SI ise, hedeflenen kaynağın bir dosya ya da bir geri döngü URI 'SI (localhost) olmadığını doğrulayın.  

|Özel durum Iletileri|
|------------------------|
|Geçersiz URI geçirildi.|
|"{İnvalid_url}" URI 'Si geçersiz.|


## <a name="error-0030"></a>Hata 0030  
 Bir dosyanın indirileceği durumlarda özel durum oluşur.  

 Azure Machine Learning içindeki bu özel durum, bir dosyanın indirileceği zaman oluşur. Üç (3) yeniden deneme denemesinden sonra bir HTTP kaynağından okuma girişimi başarısız olduğunda bu özel durumu alırsınız.  

**Çözüm:** HTTP kaynağına ait URI 'nin doğru olduğundan ve sitenin Şu anda Internet üzerinden erişilebilir olduğundan emin olun.  

|Özel durum Iletileri|
|------------------------|
|Dosya indirilemiyor.|
|Dosya indirilirken hata oluştu: {file_url}.|


## <a name="error-0031"></a>Hata 0031  
 Sütun kümesindeki sütun sayısı gerekenden küçükse özel durum oluşur.  

 Azure Machine Learning içindeki bu hata, seçilen sütun sayısı gerekenden küçükse oluşur.  Gerekli en az sayıda sütun seçilmezse bu hatayı alırsınız.  

**Çözüm:** Sütun **seçiciyi** kullanarak sütun seçimine ek sütunlar ekleyin.  

|Özel durum Iletileri|
|------------------------|
|Sütun kümesindeki sütun sayısı gerekenden küçük.|
|"{Arg_name}" giriş bağımsız değişkeni için en az {required_columns_count} sütun belirtilmelidir.|
|"{Arg_name}" giriş bağımsız değişkeni için en az {required_columns_count} sütun belirtilmelidir. Belirtilen sütunların gerçek sayısı {input_columns_count}.|


## <a name="error-0032"></a>Hata 0032  
 Bağımsız değişken bir sayı değilse özel durum oluşur.  

 Bağımsız değişken bir Double veya NaN ise Azure Machine Learning bu hatayı alırsınız.  

**Çözüm:** Belirtilen bağımsız değişkeni geçerli bir değer kullanacak şekilde değiştirin.  

|Özel durum Iletileri|
|------------------------|
|Bağımsız değişken bir sayı değil.|
|"{arg_name}" bir sayı değil.|


## <a name="error-0033"></a>Hata 0033  
 Bağımsız değişken sonsuzluk ise özel durum oluşur.  

 Bağımsız değişken sonsuz ise Azure Machine Learning bu hata oluşur. Bağımsız değişken ya da ise bu hatayı alırsınız `double.NegativeInfinity` `double.PositiveInfinity` .  

**Çözüm:** Belirtilen bağımsız değişkeni geçerli bir değer olacak şekilde değiştirin.  

|Özel durum Iletileri|
|------------------------|
|Bağımsız değişken sonlu olmalıdır.|
|"{arg_name}" sınırlı değil.|
|"{Column_name}" sütunu sonsuz değerler içeriyor.|


## <a name="error-0034"></a>Hata 0034  
 Belirli bir Kullanıcı öğesi çifti için birden çok derecelendirme varsa özel durum oluşur.  

 Bir Kullanıcı öğesi çiftinin birden fazla derecelendirme değeri varsa Azure Machine Learning bu hata öneriye göre oluşur.  

**Çözüm:** Kullanıcı öğesi çiftinin yalnızca bir derecelendirme değeri içerdiğinden emin olun.  

|Özel durum Iletileri|
|------------------------|
|Veri kümesindeki değer (ler) için birden fazla derecelendirme var.|
|Derecelendirme tahmin verileri tablosunda {User} kullanıcısı ve {Item} öğesi için birden çok derecelendirme.|
|{DataSet} içindeki {User} kullanıcısı ve {Item} öğesi için birden çok derecelendirme.|


## <a name="error-0035"></a>Hata 0035  
 Belirli bir kullanıcı veya öğe için hiçbir özellik sağlanmazsa özel durum oluşur.  

 Azure Machine Learning bu hata, Puanlama için bir öneri modeli kullanmaya çalışıyordum, ancak özellik vektörü bulunamıyor.  

**Çözüm:**

Matchbox öneren, öğe özellikleri veya Kullanıcı özellikleri kullanılırken karşılanması gereken belirli gereksinimlere sahiptir.  Bu hata, giriş olarak verdiğiniz bir kullanıcı veya öğe için bir özellik vektörünün eksik olduğunu gösterir. Her Kullanıcı veya öğe için verilerde bir özellik vektörünün kullanılabildiğinden emin olun.  

 Örneğin, kullanıcının yaşı, konumu veya geliri gibi özellikleri kullanarak bir öneri modeli eğitiliyor, ancak şimdi eğitim sırasında görülmemiş yeni kullanıcılar için puanlar oluşturmak istiyorsam, bu kullanıcılara uygun tahminleri yapabilmek için yeni kullanıcılar için bazı eşdeğer Özellikler (ör. yaş, konum ve gelir değerleri) sağlamanız gerekir. 

 Bu kullanıcılar için herhangi bir özellik yoksa, uygun özellikleri oluşturmak için özellik Mühendisliği ' nı düşünün.  Örneğin, bireysel kullanıcı yaşı veya gelir değerleriniz yoksa, bir Kullanıcı grubu için kullanılacak yaklaşık değerler oluşturabilirsiniz. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > Çözüm sizin için geçerli değil mi? Bu makaleyle ilgili geri bildirimde bulunmak ve söz konusu senaryo hakkında, modül ve sütundaki satır sayısı dahil olmak üzere bir bilgi sağlamak için hoş geldiniz. Gelecekte daha ayrıntılı sorun giderme adımları sağlamak için bu bilgileri kullanacağız.

|Özel durum Iletileri|
|------------------------|
|Gerekli bir kullanıcı veya öğe için hiçbir özellik belirtilmedi.|
|{Required_feature_name} için özellikler gerekli ancak sağlanmadı.|


## <a name="error-0036"></a>Hata 0036  
 Belirli bir kullanıcı veya öğe için birden fazla özellik vektörü sağlanmışsa özel durum oluşur.  

 Bir özellik vektörü birden çok kez tanımlanmışsa Azure Machine Learning bu hata oluşur.  

**Çözüm:** Özellik vektörünün birden çok kez tanımlandığından emin olun.  

|Özel durum Iletileri|
|------------------------|
|Bir kullanıcı veya öğe için yinelenen özellik tanımı.|


## <a name="error-0037"></a>Hata 0037  
 Birden çok etiket sütunu belirtilirse ve yalnızca birine izin veriliyorsa özel durum oluşur.  

 Yeni etiket sütunu olarak birden fazla sütun seçilirse Azure Machine Learning bu hata oluşur. En denetimli öğrenme algoritmaları, hedef veya etiket olarak işaretlenmesi için tek bir sütun gerektirir.  

**Çözüm:** Yeni etiket sütunu olarak tek bir sütun seçtiğinizden emin olun.  

|Özel durum Iletileri|
|------------------------|
|Birden çok etiket sütunu belirtildi.|
|"{Dataset_name}" içinde birden çok etiket sütunu belirtildi.|


## <a name="error-0039"></a>Hata 0039  
 İşlem başarısız olursa özel durum oluşur.  

 Azure Machine Learning içindeki bu hata, bir iç işlem tamamlanamıyor oluşur.  

**Çözüm:** Bu hata birçok koşuldan kaynaklanır ve belirli bir çözüm yoktur.  
 Aşağıdaki tabloda bu hatayla ilgili genel iletiler yer almaktadır ve bu durum, koşulun belirli bir açıklamasıyla izlenir. 

 Ayrıntı yoksa, [Microsoft Q geri bildirim göndermek Için bir soru sayfası&](/answers/topics/azure-machine-learning-studio-classic.html) ve hatayı ve ilgili koşulları oluşturan modüller hakkında bilgi sağlar.

|Özel durum Iletileri|
|------------------------|
|İşlem başarısız oldu.|
|İşlem tamamlanırken hata oluştu: "{failed_operation}".|
|İşlem tamamlanırken hata oluştu: "{failed_operation}". Neden: "{Reason}".|


## <a name="error-0042"></a>Hata 0042  
 Sütunu başka bir türe dönüştürmek mümkün olmadığında özel durum oluşur.  

 Azure Machine Learning bu hata, sütunu belirtilen türe dönüştürmek mümkün olmadığında oluşur.  Bu hata, bir modül DateTime, Text, kayan noktalı sayı veya tamsayı gibi belirli bir veri türünü gerektiriyorsa, ancak mevcut bir sütunu gerekli türe dönüştürmek mümkün değilse bu hatayı alırsınız.  

Örneğin, bir sütun seçip bir matematik işleminde kullanılmak üzere sayısal bir veri türüne dönüştürmeyi deneyebilir ve sütun geçersiz veri içeriyorsa bu hatayı alabilirsiniz. 

Diğer bir nedenden dolayı, kayan noktalı sayılar veya çok sayıda benzersiz değer içeren bir sütunu kategorik bir sütun olarak kullanmayı denerseniz bu hatayı alabilirsiniz. 

**Çözüm:**

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
    + [Eksik Verileri Temizleme](clean-missing-data.md)
    + [Verileri Normalleştirme](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> Geçici olarak çözümlenmiyor veya sizin için geçerli değil misiniz? Bu makaleyle ilgili geri bildirimde bulunmak ve bu makaleye ait modül ve sütunun veri türü dahil olmak üzere senaryo hakkında bilgi sağlamak için hoş geldiniz. Gelecekte daha ayrıntılı sorun giderme adımları sağlamak için bu bilgileri kullanacağız.  

|Özel durum Iletileri|
|------------------------|
|Dönüştürmeye izin verilmiyor.|
|{Type1} türündeki sütun {type2} türünde bir sütuna dönüştürülemedi.|
|{Type1} türündeki "{col_name1}" sütunu {type2} türünde bir sütuna dönüştürülemedi.|
|{Type1} türündeki "{col_name1}" sütunu {type2} türündeki "{col_name2}" sütununa dönüştürülemedi.|


## <a name="error-0044"></a>Hata 0044  
 Mevcut değerlerden sütun öğe türü türetilemiyor olması mümkün olmadığında özel durum oluşur.  

 Azure Machine Learning içindeki bu hata, bir veri kümesindeki sütun veya sütunların türünü çıkarsanmak mümkün olmadığında oluşur. Bu genellikle farklı öğe türlerine sahip iki veya daha fazla veri kümesini birleştirirken oluşur. Azure Machine Learning, bilgi kaybı olmadan bir sütun veya sütundaki tüm değerleri temsil edebilecek ortak bir tür belirleyemediğini bu hatayı oluşturur.  

**Çözüm:** Her iki veri kümesinin da birleştirileceği belirli bir sütundaki tüm değerlerin aynı türde (sayısal, Boole, kategorik, dize, tarih, vb.) olduğundan emin olun veya aynı türe zorlanverilebilir.  

|Özel durum Iletileri|
|------------------------|
|Sütunun öğe türü türetilemiyor.|
|"{Column_name}" sütunu için öğe türü türetilemez--tüm öğeler null başvurulardır.|
|"{Dataset_name}" veri kümesinin "{column_name}" sütunu için öğe türü türetilemez--tüm öğeler null başvurulardır.|


## <a name="error-0045"></a>Hata 0045  
 Kaynakta karışık öğe türleri nedeniyle bir sütun oluşturulması mümkün olmadığında özel durum oluşur.  

 Azure Machine Learning bu hata, birleştirilmekte olan iki veri kümesinin öğe türleri farklı olduğunda üretilir.  

**Çözüm:** Her iki veri kümesinin da birleştirileceği belirli bir sütundaki tüm değerlerin aynı türde olduğundan emin olun (sayısal, Boolean, kategorik, dize, tarih, vb.).  

|Özel durum Iletileri|
|------------------------|
|Karışık öğe türleri olan sütun oluşturulamıyor.|
|Karışık öğe türlerindeki "{column_id}" kimlikli sütun oluşturulamıyor:<br />[{Row_1}, {column_id}] veri türü "{type_1}". <br />[{Row_2}, {column_id}] veri türü "{type_2}".|
|Karışık öğe türlerindeki "{column_id}" kimlikli sütun oluşturulamıyor:<br />{Chunk_id_1} öbekteki tür "{type_1}". <br />{Chunk_id_2} öbekteki tür "{type_2}", {1} öbek boyutuyla: {chunk_size}.|


## <a name="error-0046"></a>Hata 0046  
 Belirtilen yolda dizin oluşturmak mümkün olmadığında özel durum oluşur.  

 Azure Machine Learning bu hata, belirtilen yolda bir dizin oluşturmak mümkün olmadığında oluşur. Hive sorgusu için çıkış dizinine giden yolun herhangi bir bölümü yanlış veya erişilemez durumda olduğunda bu hatayı alırsınız.  

**Çözüm:** Modülü yeniden ziyaret edin ve dizin yolunun doğru biçimlendirildiğinden ve geçerli kimlik bilgileriyle erişilebilir olduğundan emin olun.  

|Özel durum Iletileri|
|------------------------|
|Lütfen geçerli bir çıkış dizini belirtin.|
|Dizin: {Path} oluşturulamıyor. Lütfen geçerli bir yol belirtin.|


## <a name="error-0047"></a>Hata 0047  
 Modüle geçirilen bazı veri kümelerinde Özellik sütunlarının sayısı çok küçük olduğunda özel durum oluşur.  

 Azure Machine Learning bu hata, eğitimin giriş veri kümesi, algoritma için gereken en az sütun sayısını içermiyorsa oluşur. Genellikle veri kümesi boştur ya da yalnızca eğitim sütunları içerir.  

**Çözüm:** Etiket sütunundan bir veya daha fazla ek sütun olduğundan emin olmak için giriş veri kümesini yeniden ziyaret edin.  

|Özel durum Iletileri|
|------------------------|
|Giriş veri kümesindeki Özellik sütunlarının sayısı izin verilen en küçük değerden küçük.|
|Giriş veri kümesindeki Özellik sütunlarının sayısı, izin verilen en az {required_columns_count} sütundan daha az.|
|"{Arg_name}" giriş veri kümesindeki Özellik sütunlarının sayısı izin verilen en az {required_columns_count} sütundan küçük.|


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
|Dosya açılırken hata oluştu: {file_name}.|
|Dosya açılırken hata oluştu: {file_name}. Depolama özel durum iletisi: {Exception}.|


## <a name="error-0049"></a>Hata 0049  
 Bir dosya ayrıştırılmaya mümkün olmadığı durumlarda özel durum oluşur.  

 Azure Machine Learning bu hata, bir dosyayı ayrıştırmaya mümkün olmadığında oluşur. [Verileri Içeri aktarma](import-data.md) modülünde seçilen dosya biçimi dosyanın gerçek biçimiyle eşleşmezse veya dosya tanınmayan bir karakter içeriyorsa bu hatayı alırsınız.  

**Çözüm:** Modülü yeniden ziyaret edin ve dosyanın biçimiyle eşleşmiyorsa dosya biçimi seçimini düzeltin. Mümkünse, geçersiz karakter içermediğinden emin olmak için dosyayı inceleyin.  

|Özel durum Iletileri|
|------------------------|
|Dosya ayrıştırılamadı.|
|{File_format} dosyası ayrıştırılırken hata oluştu.|
|{File_format} dosyası ayrıştırılırken hata oluştu: {file_name}.|
|{File_format} dosyası ayrıştırılırken hata oluştu. Neden: {failure_reason}.|
|{File_format} dosyası ayrıştırılırken hata oluştu: {file_name}. Neden: {failure_reason}.|


## <a name="error-0052"></a>Hata 0052  
 Azure depolama hesabı anahtarı yanlış belirtilmişse özel durum oluşur.  

 Azure depolama hesabına erişmek için kullanılan anahtar yanlış ise Azure Machine Learning bu hata oluşur. Örneğin, Azure depolama anahtarı kopyalanırken ve yapıştırıldığında kesilmişse veya yanlış anahtar kullanılmışsa bu hatayı görebilirsiniz.  

 Bir Azure depolama hesabı için anahtar alma hakkında daha fazla bilgi için bkz. [depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden](../../storage/common/storage-account-create.md)oluşturma.  

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


## <a name="error-0056"></a>Hata 0056  
 İşlem için seçtiğiniz sütunlar gereksinimleri ihlal ederse özel durum oluşur.  

 Azure Machine Learning bu hata, sütunun belirli bir veri türünde olmasını gerektiren bir işlem için sütunlar seçerken oluşur. 

 Bu hata, sütun doğru veri türünde olduğunda da gerçekleşebilir, ancak kullandığınız modül sütunun bir özellik, etiket ya da kategorik sütun olarak işaretlenmesini gerektirir.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Çözüm:**

1.  Şu anda seçili olan sütunların veri türünü gözden geçirin. 

2. Seçili sütunların kategorik, etiket veya özellik sütunları olup olmadığını yoklar.  
  
3.  Veri türü veya sütun kullanımı için belirli gereksinimler olup olmadığını öğrenmek için, sütun seçimini yaptığınız modülün yardım konusunu gözden geçirin.  
  
3.  Bu işlem süresince sütun türünü değiştirmek için [meta verileri Düzenle](edit-metadata.md) ' ye kullanın. Aşağı akış işlemleri için [gerekliyse, sütun](edit-metadata.md)türünü özgün değerine geri değiştirdiğinizden emin olun.  

|Özel durum Iletileri|
|------------------------|
|Seçilen bir veya daha fazla sütun izin verilen bir kategoride değildi.|
|"{Col_name}" adlı sütun izin verilen bir kategoride değil.|


## <a name="error-0057"></a>Hata 0057  
 Zaten var olan bir dosya veya blob oluşturulmaya çalışılırken özel durum oluştu.  

 Bu özel durum, Azure Blob depolama alanına Azure Machine Learning bir işlem hattının sonuçlarını kaydetmek için [verileri dışarı aktar](export-data.md) modülünü veya başka bir modülü kullandığınızda oluşur, ancak zaten var olan bir dosya ya da blob oluşturmayı deneyin.   

**Çözüm:**

 Bu hatayı yalnızca, daha önce **Azure Blob depolama yazma modu** özelliğini **hata** olarak ayarlarsanız alırsınız. Tasarıma göre, zaten var olan bir bloba veri kümesi yazmayı denerseniz, bu modül bir hata oluşturur.

 - Modül özelliklerini açın ve **Azure Blob depolama yazma modu** özelliğini **üzerine yazacak** şekilde değiştirin.
 - Alternatif olarak, farklı bir hedef Blobun veya dosyanın adını yazabilir ve mevcut olmayan bir blob belirtdiğinizden emin olabilirsiniz.  

|Özel durum Iletileri|
|------------------------|
|Dosya veya blob zaten var.|
|"{File_path}" dosyası veya blobu zaten var.|


## <a name="error-0058"></a>Hata 0058  
 Veri kümesi beklenen etiket sütununu içermiyorsa Azure Machine Learning bu hata oluşur.  

 Bu özel durum, belirtilen etiket sütunu öğrenner tarafından beklenen veriler veya veri türüyle eşleşmiyorsa ya da yanlış değerlere sahip olduğunda da gerçekleşebilir. Örneğin, bu özel durum, ikili bir sınıflandırıcının eğitiminde gerçek değerli bir etiket sütunu kullanılırken üretilir.  

**Çözüm:** Çözüm, kullanmakta olduğunuz öğrenme veya eğitime ve veri kümenizdeki sütunların veri türleri üzerinde değişir. İlk olarak, Machine Learning algoritmasının veya eğitim modülünün gereksinimlerini doğrulayın.  

 Giriş veri kümesini yeniden ziyaret edin. Etiket olarak değerlendirildiğini düşündüğünüz sütunun oluşturmakta olduğunuz model için doğru veri türüne sahip olduğunu doğrulayın.  

 Eksik değerler için girişleri denetleyin ve gerekirse bunları kaldırın veya değiştirin.  

 Gerekirse, [verileri Düzenle](edit-metadata.md) modülünü ekleyin ve etiket sütununun etiket olarak işaretlendiğinden emin olun.  

|Özel durum Iletileri|
|------------------------|
|Etiket sütun değerleri ve puanlanmış etiket sütun değerleri karşılaştırılabilir değildir.|
|Etiket sütunu, "{dataset_name}" içinde beklenen şekilde değil.|
|Etiket sütunu, "{dataset_name}", {Reason} içinde beklenen şekilde değil.|
|"{Dataset_name}" içinde "{column_name}" etiket sütunu beklenmiyor.|
|"{Dataset_name}", {Reason} içinde "{column_name}" etiket sütunu beklenmiyor.|


## <a name="error-0059"></a>Hata 0059  
 Sütun seçicisinde belirtilen bir sütun dizini ayrıştırılamıyorsa özel durum oluşur.  

 Azure Machine Learning bu hata, sütun seçici kullanılırken belirtilen bir sütun dizini ayrıştırılamadığından oluşur.  Sütun dizini ayrıştırılmamış geçersiz bir biçimde olduğunda bu hatayı alırsınız.  

**Çözüm:** Sütun dizinini geçerli bir dizin değeri kullanacak şekilde değiştirin.  

|Özel durum Iletileri|
|------------------------|
|Belirtilen bir veya daha fazla sütun dizini veya dizin aralığı ayrıştırılamadı.|
|"{Column_index_or_range}" sütun dizini veya aralığı ayrıştırılamadı.|


## <a name="error-0060"></a>Hata 0060  
 Bir sütun seçicide Aralık dışı bir sütun aralığı belirtildiğinde özel durum oluşur.  

 Azure Machine Learning içindeki bu hata, sütun seçicide Aralık dışı bir sütun aralığı belirtildiğinde oluşur. Sütun seçicideki sütun aralığı, veri kümesindeki sütunlara karşılık gelmiyorsa bu hatayı alırsınız.  

**Çözüm:** Sütun seçicisindeki sütun aralığını, veri kümesindeki sütunlara uyacak şekilde değiştirin.  

|Özel durum Iletileri|
|------------------------|
|Geçersiz veya Aralık dışı sütun dizini aralığı belirtildi.|
|"{Column_range}" sütun aralığı geçersiz veya Aralık dışında.|


## <a name="error-0061"></a>Hata 0061  
 Tablodaki farklı sayıda sütuna sahip bir DataTable 'a satır eklenmeye çalışılırken özel durum oluşur.  

 Azure Machine Learning bu hata, veri kümesinden farklı sayıda sütuna sahip bir veri kümesine bir satır eklemeye çalıştığınızda oluşur.  Veri kümesine eklenen satırın giriş veri kümesinden farklı sayıda sütuna sahip olması durumunda bu hatayı alırsınız.  Sütun sayısı farklıysa satır veri kümesine eklenemiyor.  

**Çözüm:** Girdi veri kümesini, eklenen satırla aynı sayıda sütuna sahip olacak şekilde değiştirin veya eklenen satırı, veri kümesiyle aynı sayıda sütuna sahip olacak şekilde değiştirin.  

|Özel durum Iletileri|
|------------------------|
|Tüm tablolar aynı sayıda sütuna sahip olmalıdır.|
|"{Chunk_id_1}" öbekteki sütunlar, öbek boyutu: {chunk_size} olan "{chunk_id_2}" öbeğiyle farklı.|
|"{Filename_1}" dosyasındaki (Count = {column_count_1}) sütun sayısı "{filename_2}" (Count = {column_count_2}) dosyası ile farklı.|


## <a name="error-0062"></a>Hata 0062  
 Farklı Learner türleriyle iki modeli karşılaştırmaya çalışırken özel durum oluşur.  

 Azure Machine Learning içindeki bu hata, iki farklı puanlı veri kümesi için değerlendirme ölçümleri karşılaştırılabir zaman üretilemez. Bu durumda, iki puanlanmış veri kümesini oluşturmak için kullanılan modellerin verimliliğini karşılaştırmak mümkün değildir.  

**Çözüm:** Puanlanmış sonuçların aynı türde makine öğrenimi modeli (ikili sınıflandırma, regresyon, çok sınıf sınıflandırması, öneri, kümeleme, anomali algılama vb.) tarafından üretildiğini doğrulayın. Karşılaştırmanız gereken tüm modeller aynı Learner türüne sahip olmalıdır.  

|Özel durum Iletileri|
|------------------------|
|Tüm modeller aynı Learner türüne sahip olmalıdır.|
|Uyumsuz Learner türü alındı: "{actual_learner_type}". Beklenen Learner türleri: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Hata 0064  
 Azure depolama hesabı adı veya depolama anahtarı yanlış belirtilmişse özel durum oluşur.  

 Azure depolama hesabı adı veya depolama anahtarı yanlış belirtilmişse Azure Machine Learning bu hata oluşur. Depolama hesabı için yanlış hesap adı veya parola girerseniz bu hatayı alırsınız. Hesap adını veya parolayı el ile girdiğinizde bu durum oluşabilir. Hesap silinmişse da bu durum oluşabilir.  

**Çözüm:** Hesap adının ve parolanın doğru girildiğini ve hesabın mevcut olduğunu doğrulayın.  

|Özel durum Iletileri|
|------------------------|
|Azure depolama hesabı adı veya depolama anahtarı yanlış.|
|Azure depolama hesabı adı "{account_name}" veya hesap adı için depolama anahtarı yanlış.|


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
|Azure Depolama Blobu adı yanlış.|
|"{Blob_name}" adlı Azure Depolama Blobu adı yanlış.|
|"{Blob_name_prefix}" ön ekine sahip Azure Depolama Blobu adı yok.|
|"{Container_name}" kapsayıcısı altında herhangi bir Azure Storage blob bulunamadı.|
|Joker karakter yolu "{blob_wildcard_path}" olan herhangi bir Azure Depolama Blobu bulunamadı.|


## <a name="error-0066"></a>Hata 0066  
 Bir kaynak Azure Blobuna yüklenemediğinde özel durum oluşur.  

 Azure Machine Learning bu hata, bir kaynak Azure Blobuna yüklenemediğinde oluşur.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Her ikisi de giriş dosyasını içeren hesapla aynı Azure depolama hesabına kaydedilir.  

**Çözüm:** Modülü yeniden ziyaret edin. Azure hesap adının, depolama anahtarının ve kapsayıcının doğru olduğundan ve hesabın kapsayıcıya yazma izni olduğundan emin olun.  

|Özel durum Iletileri|
|------------------------|
|Kaynak Azure depolama 'ya yüklenemedi.|
|"{Source_path}" dosyası, Azure depolama 'ya "{dest_path}" olarak yüklenemedi.|


## <a name="error-0067"></a>Hata 0067  
 Bir veri kümesinde beklenenden farklı sayıda sütun varsa özel durum oluşur.  

 Bir veri kümesinde beklenenden farklı sayıda sütun varsa Azure Machine Learning bu hata oluşur.  Veri kümesindeki sütun sayısı modülün yürütme sırasında beklediği sütun sayısından farklı olduğunda bu hatayı alırsınız.  

**Çözüm:** Giriş veri kümesini veya parametrelerini değiştirin.  

|Özel durum Iletileri|
|------------------------|
|DataTable 'da beklenmeyen sayıda sütun.|
|"{Dataset_name}" veri kümesinde beklenmeyen sayıda sütun.|
|"{Expected_column_count}" sütun bekleniyordu ancak bunun yerine "{actual_column_count}" sütun bulundu.|
|"{Dataset_name}" giriş veri kümesinde "{expected_column_count}" sütun bekleniyordu ancak bunun yerine "{actual_column_count}" sütun bulundu.|


## <a name="error-0068"></a>Hata 0068  
 Belirtilen Hive betiği doğru değilse özel durum oluşur.  

 Azure Machine Learning içindeki bu hata, Hive QL betiğinde sözdizimi hataları varsa veya Hive yorumlayıcı sorgu veya betiği yürütürken bir hatayla karşılaştığında oluşur.  

**Çözüm:**

Hive 'den alınan hata iletisi normalde hata günlüğünde geri bildirilir, böylece belirli hataya göre işlem gerçekleştirebilirsiniz. 

+ Modülü açın ve hataları Sorgula ' yı inceleyin.  
+ Sorgunun, Hadoop kümenizin Hive konsolunda oturum açarak ve sorguyu çalıştırarak Azure Machine Learning dışında düzgün çalıştığını doğrulayın.  
+ Yürütülebilir deyimleri ve açıklamaları tek bir satırda karıştırın tersine, Hive betiğinizdeki açıklamaları ayrı bir satıra yerleştirmeyi deneyin.  

### <a name="resources"></a>Kaynaklar

Makine öğrenimi için Hive sorgularıyla ilgili yardım için aşağıdaki makalelere bakın:

+ [Azure Blob depolamadan Hive tabloları oluşturma ve veri yükleme](../team-data-science-process/move-hive-tables.md)
+ [Hive sorgularıyla tablolardaki verileri araştırma](../team-data-science-process/explore-data-hive-tables.md)
+ [Hive sorgularını kullanarak bir Hadoop kümesindeki verilerin özelliklerini oluşturma](../team-data-science-process/create-features-hive.md)
+ [SQL kullanıcıları için Hive sayfası (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Özel durum Iletileri|
|------------------------|
|Hive betiği yanlış.|


## <a name="error-0069"></a>Hata 0069  
 Belirtilen SQL betiği doğru değilse özel durum oluşur.  

 Bu Azure Machine Learning hatası, belirtilen SQL betiği söz dizimi sorunları içeriyorsa veya betikte belirtilen sütunlar veya tablo geçerli değilse oluşur. 

 SQL Altyapısı sorgu veya betiği yürütürken herhangi bir hatayla karşılaştığında bu hatayı alırsınız. SQL hata iletisi normalde hata günlüğünde geri bildirilir, böylece belirli hataya göre işlem gerçekleştirebilirsiniz.  

**Çözüm:** Modülü yeniden ziyaret edin ve hatalar için SQL sorgusunu inceleyin.  

 Sorgunun, doğrudan veritabanı sunucusunda oturum açarak ve sorguyu çalıştırarak Azure ML dışında düzgün çalıştığını doğrulayın.  

 Modül özel durumu tarafından bildirilen bir SQL tarafından oluşturulan ileti varsa, bildirilen hataya göre işlem yapın. Örneğin, hata iletileri bazen olası hataya özgü yönergeler içerir:
+ *Böyle bir sütun veya eksik veritabanı değil*, bir sütun adı yanlış yazmış olabileceğiniz. Sütun adının doğru olduğundan eminseniz, sütun tanımlayıcısını kapsamak için köşeli ayraç veya tırnak işaretleri kullanmayı deneyin.
+ *Yakınında \<SQL keyword\> SQL Logic Error*, belirtilen anahtar sözcükten önce bir sözdizimi hatası olabileceğini belirtir

  
|Özel durum Iletileri|
|------------------------|
|SQL betiği yanlış.|
|"{Sql_query}" SQL sorgusu doğru değil.|
|"{Sql_query}" SQL sorgusu doğru değil. Özel durum iletisi: {Exception}.|


## <a name="error-0070"></a>Hata 0070  
 Mevcut olmayan Azure tablosuna erişmeye çalışırken özel durum oluştu.  

 Mevcut olmayan bir Azure tablosuna erişmeye çalıştığınızda Azure Machine Learning bu hata oluşur. Azure depolama 'da bir tablo belirtirseniz, Azure Tablo depolama alanından okurken veya yazarken bu hatayı alırsınız. Bu durum, istenen tablonun adını yanlış yazdığınızda veya hedef adı ile depolama türü arasında bir uyuşmazlık olması durumunda gerçekleşebilir. Örneğin, bir tablodan okumayı amaçlıyorsanız, ancak bunun yerine bir Blobun adını girdiniz.  

**Çözüm:** Tablo adının doğru olduğunu doğrulamak için modülü yeniden ziyaret edin.  

|Özel durum Iletileri|
|------------------------|
|Azure tablosu yok.|
|"{Table_name}" Azure tablosu yok.|


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
|Sütun {target_type} olarak dönüştürülemedi.|


## <a name="error-0075"></a>Hata 0075  
Bir veri kümesini satışlama sırasında geçersiz bir binme işlevi kullanıldığında özel durum oluşur.  

Azure Machine Learning içinde bu hata, desteklenmeyen bir yöntemi kullanarak verileri bölmeye çalışırken veya parametre birleşimleri geçersiz olduğunda oluşur.  

**Çözüm:**

Bu olay için hata işleme, daha fazla sayıda daha fazla özelleştirme yönteminin özelleştirilmesine izin veren Azure Machine Learning önceki bir sürümünde kullanıma sunulmuştur. Şu anda tüm binme yöntemleri, bir açılan listeden seçim üzerine dayalıdır, bu nedenle Teknik olarak bu hatayı almak için artık mümkün olmayacaktır.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

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
|Desteklenmeyen blob yazma modu: {blob_write_mode}.|


## <a name="error-0078"></a>Hata 0078  
 [Içeri aktarma verileri](import-data.md) için http seçeneği yeniden yönlendirmeyi belirten bir 3xx durum kodu aldığında özel durum oluşur.  

 Azure Machine Learning bu hata, [Içeri aktarma verileri](import-data.md) için http seçeneği yeniden yönlendirmeyi belirten bir 3xx (301, 302, 304, vb.) durum kodu aldığında oluşur. Tarayıcıyı başka bir sayfaya yönlendiren bir HTTP kaynağına bağlanmaya çalışırsanız bu hatayı alırsınız. Güvenlik nedenleriyle, Web sitelerinin Azure Machine Learning için veri kaynakları olarak yeniden yönlendirilmesi izin verilmez.  

**Çözüm:** Web sitesi güvenilir bir Web sitesi ise, yeniden yönlendirilen URL 'YI doğrudan girin.  

|Özel durum Iletileri|
|------------------------|
|Http yeniden yönlendirmesine izin verilmiyor.|


## <a name="error-0079"></a>Hata 0079  
 Azure depolama kapsayıcısı adı yanlış belirtilmişse özel durum oluşur.  

 Azure depolama kapsayıcısı adı yanlış belirtilmişse Azure Machine Learning bu hata oluşur. Bu hatayı, Azure Blob depolama alanına yazarken **kapsayıcı ile başlayan blob 'un yolunu** kullanarak hem kapsayıcıyı hem de blob (dosya) adını belirttebilmeniz durumunda alırsınız.  

**Çözüm:** [Verileri dışarı aktar](export-data.md) modülünü yeniden ziyaret edin ve belirtilen blob yolunun hem kapsayıcıyı hem de dosya adını kapsayıcı **/filename** biçiminde içerdiğini doğrulayın.  

|Özel durum Iletileri|
|------------------------|
|Azure depolama kapsayıcısı adı yanlış.|
|"{Container_name}" adlı Azure depolama kapsayıcısı adı yanlış; kapsayıcı/blob biçim kapsayıcısı adı bekleniyordu.|


## <a name="error-0080"></a>Hata 0080  
 Özel durum, tüm değerleri eksik olan sütuna modül tarafından izin verilmediği zaman oluşur.  

 Azure Machine Learning bu hata, modül tarafından tüketilen bir veya daha fazla sütun eksik tüm değerleri içerdiğinde üretilir. Örneğin, bir modül her sütun için toplama istatistiklerini hesapladıysanız, veri içermeyen bir sütun üzerinde çalışamaz. Bu gibi durumlarda, modül yürütmesi bu özel durumla durdurulur.  

**Çözüm:** Giriş veri kümesini yeniden ziyaret edin ve tüm eksik değerleri içeren sütunları kaldırın.  

|Özel durum Iletileri|
|------------------------|
|Tüm değerleri eksik olan sütunlara izin verilmez.|
|{Col_index_or_name} sütununun tüm değerleri eksik.|


## <a name="error-0081"></a>Hata 0081  
 En az bir seyrek özellik sütunu içeren giriş veri kümesindeki Özellik sütunlarının sayısına eşitse, PCA modülünde özel durum oluşur.  

 Azure Machine Learning bu hata, aşağıdaki koşullar karşılanıyorsa üretilir: (a) giriş veri kümesinde en az bir seyrek sütun ve (b) istenen en son boyut sayısı, giriş boyutlarının sayısıyla aynıdır.  

**Çözüm:** Çıkışdaki boyut sayısını, girişte bulunan boyut sayısından az olacak şekilde azaltmayı göz önünde bulundurun. Bu, PCA uygulamalarında tipik bir davranıştır.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Özel durum Iletileri|
|------------------------|
|Seyrek Özellik sütunları içeren veri kümesi için, özellik sütunlarının sayısından küçük olması gereken boyut sayısı.|


## <a name="error-0082"></a>Hata 0082  
 Bir modelin serisi başarıyla kaldırıldığında özel durum oluşur.  

 Azure Machine Learning bu hata, kaydedilmiş bir makine öğrenimi modeli veya dönüştürmesi, bir son değişikliğin sonucu olarak Azure Machine Learning çalışma zamanının daha yeni bir sürümü tarafından yüklenemediğinde oluşur.  

**Çözüm:** Modeli veya dönüştürmeyi üreten eğitim ardışık düzeni yeniden çalıştırılmalıdır ve model ya da dönüşüm yeniden kaydedilmelidir.  

|Özel durum Iletileri|
|------------------------|
|Büyük olasılıkla eski bir serileştirme biçimiyle serileştirildiği için model seri durumdan çıkarılamadı. Lütfen modeli yeniden eğitme ve yeniden kaydedin.|


## <a name="error-0083"></a>Hata 0083  
 Eğitim için kullanılan veri kümesi, Learner somut türü için kullanılmıyorsa özel durum oluşur.  

 Azure Machine Learning bu hata, veri kümesi eğitilen öğrenici ile uyumsuz olduğunda üretilir. Örneğin, veri kümesi her satırda en az bir eksik değer içerebilir ve sonuç olarak veri kümesinin tamamı eğitim sırasında atlanır. Diğer durumlarda, anomali algılama gibi bazı makine öğrenimi algoritmaları etiketlerin mevcut olmasını beklemez ve veri kümesinde Etiketler mevcutsa bu özel durumu oluşturabilir.  

**Çözüm:** Giriş veri kümesi için gereksinimleri denetlemek için kullanılan öğrenimlerin belgelerine başvurun. Tüm gerekli sütunların mevcut olduğunu görmek için sütunları inceleyin.  

|Özel durum Iletileri|
|------------------------|
|Eğitim için kullanılan veri kümesi geçersiz.|
|{data_name} eğitim için geçersiz veri içeriyor.|
|{data_name} eğitim için geçersiz veri içeriyor. Learner türü: {learner_type}.|
|{data_name} eğitim için geçersiz veri içeriyor. Learner türü: {learner_type}. Neden: {Reason}.|
|{Data_name} eğitim verileri üzerinde "{action_name}" eylemi uygulanamadı. Neden: {Reason}.|


## <a name="error-0084"></a>Hata 0084  
 Bir R betikten üretilen puanlar değerlendirildiğinde özel durum oluşur. Bu şu anda desteklenmiyor.  

 Azure Machine Learning içinde bu hata, puanları içeren bir R betiğinin çıkışıyla bir modeli değerlendirmek için modüllerden birini kullanmayı denerseniz oluşur.  

**Çözüm:**

|Özel durum Iletileri|
|------------------------|
|Özel model tarafından oluşturulan puanları değerlendirmek Şu anda desteklenmiyor.|


## <a name="error-0085"></a>Hata 0085  
 Betik değerlendirmesi hata vererek başarısız olduğunda özel durum oluşur.  

 Azure Machine Learning bu hata, sözdizimi hataları içeren özel betik çalıştırırken oluşur.  

**Çözüm:** Kodunuzu bir dış düzenleyicide gözden geçirin ve hata olup olmadığını denetleyin.  

|Özel durum Iletileri|
|------------------------|
|Betik değerlendirmesi sırasında hata oluştu.|
|Betik değerlendirmesi sırasında aşağıdaki hata oluştu, daha fazla bilgi için lütfen çıkış günlüğünü görüntüleyin:<br />{Script_language} yorumlayıcısından hata iletisinin başlangıcını--------------------<br />Mesaj<br />{Script_language} yorumlayıcısından hata iletisinin sonuna--------------------|


## <a name="error-0090"></a>Hata 0090  
 Hive tablo oluşturma başarısız olduğunda özel durum oluşur.  

 Azure Machine Learning içindeki bu hata, verileri [dışarı aktarma](export-data.md) veya bir HDInsight kümesine kaydetmek için başka bir seçenek kullandığınızda oluşur ve belirtilen Hive tablosu oluşturulenemez.  

**Çözüm:** Kümeyle ilişkili Azure depolama hesabı adını denetleyin ve modül özelliklerinde aynı hesabı kullandığınızdan emin olun.  

|Özel durum Iletileri|
|------------------------|
|Hive tablosu oluşturulamadı. HDInsight kümesi için, küme ile ilişkili Azure depolama hesabı adının modül parametresi aracılığıyla geçirilen ile aynı olduğundan emin olun.|
|"{Table_name}" Hive tablosu oluşturulamadı. HDInsight kümesi için, küme ile ilişkili Azure depolama hesabı adının modül parametresi aracılığıyla geçirilen ile aynı olduğundan emin olun.|
|"{Table_name}" Hive tablosu oluşturulamadı. HDInsight kümesi için, lütfen kümeyle ilişkili Azure depolama hesabı adının "{cluster_name}" olduğundan emin olun.|


## <a name="error-0102"></a>Hata 0102  
 Bir ZIP dosyası ayıklanamıyor olduğunda oluşturulur.  

 Azure Machine Learning içindeki bu hata,. zip uzantısına sahip bir daraltılmış paketi içeri aktarırken, ancak paket bir ZIP dosyası olmadığında veya dosya desteklenen bir ZIP biçimi kullanmıyorsa oluşur.  

**Çözüm:** Seçili dosyanın geçerli bir. zip dosyası olduğundan ve desteklenen sıkıştırma algoritmalarından birini kullanarak sıkıştırıldığından emin olun.  

 Veri kümelerini sıkıştırılmış biçimde aktarırken bu hatayı alırsanız, içerilen tüm dosyaların desteklenen dosya biçimlerinden birini kullandığınızı ve Unicode biçiminde olduğunu doğrulayın.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 İstenen dosyaları yeni bir sıkıştırılmış daraltılmış klasöre okumayı deneyin ve özel modülü yeniden eklemeyi deneyin.  

|Özel durum Iletileri|
|------------------------|
|Verilen ZIP dosyası doğru biçimde değil.|


## <a name="error-0105"></a>Hata 0105  
 Bu hata bir modül tanımı dosyası desteklenmeyen bir parametre türü içerdiğinde görüntülenir  
  
 Azure Machine Learning içindeki bu hata, özel bir modül xml tanımı oluşturduğunuzda ve bir parametre ya da tanımda bir bağımsız değişkenin türü desteklenen bir türle eşleşmediği zaman üretilir.  
  
**Çözüm:** Özel modül XML tanım dosyasındaki herhangi bir **bağımsız değişken** öğesinin type özelliğinin desteklenen bir tür olduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Desteklenmeyen parametre türü.|  
|Desteklenmeyen parametre türü ' {0} ' belirtildi.|  


## <a name="error-0107"></a>Hata 0107  
 Modül tanımı dosyası desteklenmeyen bir çıkış türünü tanımlıyorsa oluşturulur  
  
 Azure Machine Learning içindeki bu hata, özel bir modül XML tanımındaki bir çıkış bağlantı noktasının türü desteklenen bir türle eşleşmediği zaman üretilir.  
  
**Çözüm:** Özel modül XML tanım dosyasındaki bir output öğesinin type özelliğinin desteklenen bir tür olduğundan emin olun.  
  
|Özel durum Iletileri|  
|------------------------|  
|Desteklenmeyen çıkış türü.|  
|Desteklenmeyen çıkış türü ' {output_type} ' belirtildi.|  


## <a name="error-0125"></a>Hata 0125  
 Birden çok veri kümesi için şema eşleşmediği zaman oluşturulur.  

**Çözüm:**

|Özel durum Iletileri|
|------------------------|
|Veri kümesi şeması eşleşmiyor.|


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
|' {File_path} ' dosyasındaki resim piksel boyutu izin verilen sınırı aşıyor: ' {size_limit} '.|


## <a name="error-0128"></a>Hata 0128  
 Kategorik sütunlar için koşullu olasılıkların sayısı sınırı aşıyor.  

**Çözüm:**

|Özel durum Iletileri|
|------------------------|
|Kategorik sütunlar için koşullu olasılıkların sayısı sınırı aşıyor.|
|Kategorik sütunlar için koşullu olasılıkların sayısı sınırı aşıyor. ' {Column_name_or_index_1} ' ve ' {column_name_or_index_2} ' sütunları sorunlu çiftidir.|


## <a name="error-0129"></a>Hata 0129  
 Veri kümesindeki sütun sayısı izin verilen sınırı aşıyor.  

**Çözüm:**

|Özel durum Iletileri|
|------------------------|
|Veri kümesindeki sütun sayısı izin verilen sınırı aşıyor.|
|' {Dataset_name} ' içindeki veri kümesindeki sütun sayısı izin verilen sınırı aşıyor.|
|' {Dataset_name} ' içindeki veri kümesindeki sütun sayısı ' {component_name} ' olan izin verilen sınırı aşıyor.|
|' {Dataset_name} ' içindeki veri kümesindeki sütun sayısı ' {component_name} ' olan izin verilen ' {limit_columns_count} ' sınırını aşıyor.|


## <a name="error-0134"></a>Hata 0134
Etiket sütunu eksik olduğunda veya etiketli satırlara yetecek kadar özel durum oluşur.  

Bu hata, modül bir etiket sütunu gerektirdiğinde, ancak sütun seçimine bir tane eklemezseniz veya etiket sütununda çok fazla sayıda değer bulunmadığından oluşur.

Bu hata, önceki bir işlem, bir aşağı akış işleminde yeterli sayıda satır olduğu için veri kümesini değiştirdiğinde de oluşabilir. Örneğin, bir veri kümesini değerlere göre bölmek için **bölüm ve örnek** modülünde bir ifade kullandığınızı varsayalım. İfadeniz için eşleşme bulunmazsa, bölümden kaynaklanan veri kümelerinden biri boş olur.

Çözüm: 

 Sütun seçimine bir etiket sütunu eklerseniz, ancak tanınmazsa, etiket sütunu olarak işaretlemek için [meta verileri Düzenle](edit-metadata.md) modülünü kullanın.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Daha sonra, etiket sütununda eksik değerleri olan satırları kaldırmak için [eksik veri silme](clean-missing-data.md) modülünü kullanabilirsiniz. 

 Giriş veri kümelerinizi denetleyerek, geçerli veriler içerdiğinden emin olun ve işlemin gereksinimlerini karşılamak için yeterli sayıda satır vardır. Birçok algoritma, en az sayıda veri satırı gerektiriyorsa, ancak veriler yalnızca birkaç satır ya da yalnızca bir üst bilgi içeriyorsa bir hata mesajı oluşturur.

|Özel durum Iletileri|
|------------------------|
|Etiket sütunu eksik olduğunda veya etiketli satırlara yetecek kadar özel durum oluşur.|
|Etiket sütunu eksik olduğunda ya da {required_rows_count} etiketli satırlardan daha az sütun varsa özel durum oluşur.|
|{Dataset_name} veri kümesindeki etiket sütunu eksik veya {required_rows_count} etiketli satırlardan daha az olduğunda özel durum oluşur.|


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
|Bellek tükendi, modülün çalıştırılması tamamlanamıyor. Ayrıntılar: {details}|


## <a name="error-0141"></a>Hata 0141  
 Kategorik ve dize sütunlarındaki seçili sayısal sütunların ve benzersiz değerlerin sayısı çok küçük olduğunda özel durum oluşur.  

 Azure Machine Learning bu hata, Seçili sütunda işlemi gerçekleştirmek için yeterli sayıda benzersiz değer olmadığında oluşur.  

**Çözüm:** Bazı işlemler, özellik ve kategorik sütunlarda istatistiksel işlemler gerçekleştirir ve yeterince değer yoksa, işlem başarısız olabilir veya geçersiz bir sonuç döndürebilir. Özellik ve etiket sütunlarında kaç değer bulunduğunu görmek için veri kümenizi denetleyin ve gerçekleştirmeye çalıştığınız işlemin istatistiksel olarak geçerli olup olmadığını saptayın.  

 Kaynak veri kümesi geçerliyse, bazı yukarı akış veri işleme veya meta veri işleminin verileri değiştirilip değiştirilmediğini denetleyebilir ve bazı değerleri kaldırmış olabilirsiniz.  

 Yukarı akış işlemleri bölme, örnekleme veya yeniden örnekleme içeriyorsa, çıkışların beklenen satır ve değer sayısını içerdiğini doğrulayın.  

|Özel durum Iletileri|
|------------------------|
|Seçilen sayısal sütunların sayısı ve kategorik ve dize sütunlarındaki benzersiz değerler çok küçük.|
|Seçilen sayısal sütunların ve kategorik ve dize sütunlarındaki benzersiz değerlerin toplam sayısı (Şu anda {actual_num}) en az {lower_boundary} olmalıdır.|


## <a name="error-0154"></a>Hata 0154  
 Kullanıcı, anahtar sütunlardaki verileri uyumsuz sütun türüyle birleştirmeyi denediğinde özel durum oluşur.

|Özel durum Iletileri|
|------------------------|
|Anahtar sütunu öğe türleri uyumlu değil.|
|Anahtar sütunu öğe türleri uyumlu değil. (sol: {keys_left}; sağ: {keys_right})|


## <a name="error-0155"></a>Hata 0155  
 DataSet 'in sütun adları dize olmadığında özel durum oluşur.

|Özel durum Iletileri|
|------------------------|
|Dataframe sütun adı dize türünde olmalıdır. Sütun adları dize değil.|
|Dataframe sütun adı dize türünde olmalıdır. {Column_names} sütun adları dize değil.|


## <a name="error-0156"></a>Hata 0156  
 Azure SQL veritabanından veri okuma başarısız olduğunda özel durum oluşur.

|Özel durum Iletileri|
|------------------------|
|Azure SQL veritabanından veri okunamadı.|
|Azure SQL veritabanı 'ndan veri okuma başarısız: {detailed_message} DB: {database_server_name}: {database_name} sorgu: {sql_statement}|


## <a name="error-0157"></a>Hata 0157  
 Veri deposu bulunamadı.

|Özel durum Iletileri|
|------------------------|
|Veri deposu bilgileri geçersiz.|
|Veri deposu bilgileri geçersiz. ' {Workspace_name} ' çalışma alanındaki ' {datastore_name} ' AzureML veri deposu alınamadı.|


## <a name="error-0158"></a>Hata 0158
 Bir dönüştürme dizini geçersiz olduğunda oluşturulur.

|Özel durum Iletileri|
|------------------------------------------------------------|
|Verilen dönüştürme Tiondirectory geçersiz.|
|"{Arg_name}" dönüştürme işlemi dizini geçersiz. Neden: {Reason}. Lütfen dönüştürme dosyasını üreten eğitim denemesinin yeniden çalıştırın. Eğitim denemesi silinmişse, lütfen dönüştürme dosyasını yeniden oluşturun ve kaydedin.|
|"{Arg_name}" dönüştürme işlemi dizini geçersiz. Neden: {Reason}. {troubleshoot_hint}|


## <a name="error-0159"></a>Hata 0159
 Modül modeli dizini geçersiz ise özel durum oluşur. 

|Özel durum Iletileri|
|------------------------------------------------------------|
|Verilen ModelDirectory geçersiz.|
|ModelDirectory "{arg_name}" geçersiz.|
|ModelDirectory "{arg_name}" geçersiz. Neden: {Reason}.|
|ModelDirectory "{arg_name}" geçersiz. Neden: {Reason}. {troubleshoot_hint}|


## <a name="error-1000"></a>Hata 1000  
İç kitaplık özel durumu.  

Bu hata, işlenmemiş iç altyapı hatalarını yakalamak için sağlanır. Bu nedenle, hatayı oluşturan modüle bağlı olarak bu hatanın nedeni farklı olabilir.  

Daha fazla yardım almak için, hataya eşlik eden ayrıntılı iletiyi, giriş olarak kullanılan veriler de dahil olmak üzere senaryonun açıklamasıyla birlikte [Azure Machine Learning forumuna](/answers/topics/azure-machine-learning.html)nakletmeniz önerilir. Bu geri bildirim, hataları önceliklendirmemize ve daha fazla iş için en önemli sorunları belirlemenize yardımcı olur.  

|Özel durum Iletileri|
|------------------------|
|Kitaplık özel durumu.|
|Kitaplık özel durumu: {Exception}.|
|Bilinmeyen kitaplık özel durumu: {Exception}. {customer_support_guidance}.|


## <a name="execute-python-script-module"></a>Python betik modülünü Yürüt

**Python betik modülünü yürütme** **70_driver_logs** **azureml_main** arama yapın ve hangi satır hatası oluştuğunu bulabilirsiniz. Örneğin, "dosya"/tmp/tmp01_ID/user_script. Kopyala ", satır 17, azureml_main",, Python betiğinizin 17. satırında hata oluştuğunu gösterir.
