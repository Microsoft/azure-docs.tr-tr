---
title: Özellik karma modülü başvurusu
titleSuffix: Azure Machine Learning
description: Metin verilerini korlaleştirmek için Azure Machine Learning Özellik karma modülünü kullanmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 48960eae4941bb744a937639e1308e1b5f6aaf9f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497824"
---
# <a name="feature-hashing-module-reference"></a>Özellik karma modülü başvurusu

Bu makalede Azure Machine Learning tasarımcısında (Önizleme) bulunan bir modül açıklanmaktadır.

Ingilizce metin akışını bir tamsayı özellikleri kümesine dönüştürmek için özellik karma modülünü kullanın. Daha sonra bu karma özellik kümesini bir makine öğrenimi algoritmasına geçirebilirsiniz ve bir metin analizi modelini eğitebilirsiniz.

Bu modülde sunulan özellik karma işlevselliği nimbusml çerçevesini temel alır. Daha fazla bilgi için bkz. [Ngramhash sınıfı](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Özellik karma nedir?

Özellik karması, benzersiz belirteçleri tamsayılara dönüştürerek işe yarar. Giriş olarak sağladığınız tam dizeler üzerinde çalışır ve herhangi bir dil analizi veya ön işleme gerçekleştirmez. 

Örneğin, bunlar gibi bir basit tümceler ve ardından bir yaklaşım puanı alın. Bir model oluşturmak için bu metni kullanmak istediğinizi varsayın.

|Kullanıcı metni|Yaklaşım|
|--------------|---------------|
|Bu kitabı sevdim|3|
|Bu kitabı aldım|1|
|Bu kitap harika|3|
|Sevdiğim kitaplar|2|

Dahili olarak, özellik karma modülü n-gram sözlüğü oluşturur. Örneğin, bu veri kümesi için bigram listesi şuna benzer olacaktır:

|Terim (bigram)|Frequency|
|------------|---------------|
|Bu kitap|3|
|Sevdim|1|
|Ben|1|
|Sevdiğim|1|

N **-gram özelliğini kullanarak** n-gram boyutunu kontrol edebilirsiniz. Bigram seçerseniz, tek tek gram da hesaplanır. Sözlük aşağıdaki gibi tek terimleri de içerir:

|Terim (tek gram)|Frequency|
|------------|---------------|
|Kitabın|3|
|I|3|
|kitap|1|
|bulunamadı|1|

Sözlük derlendikten sonra, özellik karma modülü sözlük koşullarını karma değerlerine dönüştürür. Daha sonra her durumda bir özelliğin kullanılıp kullanılmadığını hesaplar. Her metin verisi satırı için, modül bir sütun kümesi, her karma özellik için bir sütun verir.

Örneğin, karma olduktan sonra Özellik sütunları şuna benzeyebilir:

|Derecesiyle|Karma Özellik 1|Karma özelliği 2|Karma özellik 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Sütundaki değer 0 ise, satır karma özelliği içermiyordu.
* Değer 1 ise, satır özelliği içerir.

Özellik karması, boyut boyutunu azaltmak için değişken uzunluktaki metin belgelerinin eşit uzunlukta sayısal Özellik vektörleri olarak temsil etmenize olanak tanır. Eğitim için metin sütununu olduğu gibi kullanmaya çalıştıysanız, çok sayıda farklı değer içeren kategorik özellik sütunu olarak kabul edilir.

Sayısal çıktılar Ayrıca sınıflandırma, kümeleme ve bilgi alma dahil olmak üzere ortak makine öğrenimi yöntemlerini kullanmayı mümkün kılar. Arama işlemleri dize karşılaştırmaları yerine tamsayı karmaları kullanabileceğinden, özellik ağırlıklarını elde etmek de çok daha hızlıdır.

## <a name="configure-the-feature-hashing-module"></a>Özellik karma modülünü yapılandırma

1.  Tasarımcı 'daki işlem hattınızı Özellik karma modülünü ekleyin.

1. Çözümlemek istediğiniz metni içeren veri kümesini bağlayın.

    > [!TIP]
    > Özelliği karma hale getirmek veya kesilme gibi sözcük temelli işlemler gerçekleştirmediğinden, özellik karmakarmasını uygulamadan önce metin ön işlemden yararlanarak bazen daha iyi sonuçlar elde edebilirsiniz. 

1. **Hedef sütunları** karma özelliklerine dönüştürmek istediğiniz metin sütunlarına ayarlayın. Şunları göz önünde bulundurun:

    * Sütunlar dize veri türü olmalıdır.
    
    * Birden çok metin sütununun seçilmesi Özellik boyutalliği üzerinde önemli bir etkiye sahip olabilir. Örneğin, 10 bitlik bir karma için sütun sayısı, iki sütun için tek bir sütun 1.024 ' den 2.048 ' e gider.

1. Karma tablo oluştururken kullanılacak bit sayısını belirtmek için **karma bit boyutunu** kullanın.
    
    Varsayılan bit boyutu 10 ' dur. Birçok sorun için bu değer yeterlidir. Eğitim metnindeki n gram sözlüğü boyutuna bağlı olarak çarpışmalardan kaçınmak için daha fazla alana ihtiyacınız bulunabilir.
    
1. **N-gram**için, eğitim sözlüğüne eklemek üzere en fazla n gram uzunluğunu tanımlayan bir sayı girin. N-gram, benzersiz birim olarak kabul edilen *n* sözcükten oluşan bir dizidir.

    Örneğin, 3, unigram, bigram ve trigram girerseniz.

1. İşlem hattını çalıştırma.

## <a name="results"></a>Sonuçlar

İşlem tamamlandıktan sonra modül, özgün metin sütununun birden çok sütuna dönüştürüldüğü dönüştürülmüş bir veri kümesini çıktı. Her sütun metindeki bir özelliği temsil eder. Sözlüğün ne kadar önemli olduğuna bağlı olarak, elde edilen veri kümesi büyük olabilir:

|Sütun adı 1|Sütun türü 2|
|-------------------|-------------------|
|Kullanıcı METNI|Özgün veri sütunu|
|YAKLAŞıM|Özgün veri sütunu|
|USERTEXT-karma Özellik 1|Karma özellik sütunu|
|USERTEXT-karma özelliği 2|Karma özellik sütunu|
|USERTEXT-karma özelliği n|Karma özellik sütunu|
|USERTEXT-karma özelliği 1024|Karma özellik sütunu|

Dönüştürülmüş veri kümesini oluşturduktan sonra, bunu eğitme modeli modülüne giriş olarak kullanabilirsiniz.
 
### <a name="best-practices"></a>En iyi uygulamalar

Aşağıdaki en iyi yöntemler, özellik karma modülünden en iyi şekilde yararlanmanıza yardımcı olabilir:

* Giriş metnini önceden işlemek için özellik karma özelliğini kullanmadan önce bir ön Işlem metin modülü ekleyin. 

* Metin sütunlarını çıkış veri kümesinden kaldırmak için özellik karma modülünden sonra bir sütun seçme modülü ekleyin. Karma Özellikler oluşturulduktan sonra metin sütunlarına gerek yoktur.
    
* Sonuçları basitleştirmek ve doğruluğu artırmak için bu metin ön işleme seçeneklerini kullanmayı göz önünde bulundurun:

    * Sözcük bölme
    * Word kaldırma durduruluyor
    * Örnek olay normalleştirme
    * Noktalama işaretleri ve özel karakterlerin kaldırılması
    * Kesintilerinden kaynaklanan  

Herhangi bir çözümde uygulanacak en uygun ön işleme yöntemi kümesi, etki alanı, sözlük ve iş gereksinimlerinize bağlıdır. hangi metin işleme yöntemlerinin en etkili olduğunu görmek için verilerinize sahip işlem hattı.

## <a name="next-steps"></a>Sonraki adımlar
            
Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın 
