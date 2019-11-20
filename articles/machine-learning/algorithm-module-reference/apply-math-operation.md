---
title: Matematik Işlemini Uygula
titleSuffix: Azure Machine Learning service
description: Bir işlem hattındaki sütun değerlerine matematik işlemi uygulamak için Azure Machine Learning hizmetinde Math Işlem modülünü uygulama ' yı nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: a2f3665355cc2023aaf4b66c9207aaff4a3bc6db
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493932"
---
# <a name="apply-math-operation"></a>Matematik Işlemini Uygula

Bu makalede bir Azure Machine Learning Designer (Önizleme) modülü açıklanır.

Giriş veri kümesindeki sayısal sütunlara uygulanan hesaplamalar oluşturmak için matematik Uygula Işlemini kullanın. 

Desteklenen matematik işlemleri, çarpma ve bölme, Trigonometrik İşlevler, çeşitli yuvarlama işlevleri ve gama ve hata işlevleri gibi veri bilimi için kullanılan özel işlevler gibi yaygın aritmetik işlevleri içerir.  

Bir işlemi tanımladıktan ve işlem hattını çalıştırdıktan sonra, değerler veri kümenize eklenir. Modülünü nasıl yapılandırdığınıza bağlı olarak, şunları yapabilirsiniz:

+ Sonuçları veri kümenize ekleyin. Bu, özellikle işlemin sonucunu doğrularken yararlı olur.
+ Sütun değerlerini yeni, hesaplanan değerlerle değiştirin.
+ Sonuçlar için yeni bir sütun oluşturun ve özgün verileri göstermez. 

Aşağıdaki kategorilerde ihtiyacınız olan işlemi arayın:  

- [Temel](#basic-math-operations)  
  
     **Temel** kategorideki işlevler, tek bir değeri veya değer sütununu işlemek için kullanılabilir. Örneğin, bir sütundaki tüm sayıların mutlak değerini alabilir veya bir sütundaki her bir değerin kare kökünü hesaplayabilirsiniz.  
  
-   [Karşılaştır](#comparison-operations)  
  
      **Karşılaştırma kategorisindeki işlevlerin** tamamı karşılaştırma için kullanılır: iki sütundaki değerleri çift yönlü bir şekilde karşılaştırın veya bir sütundaki her değeri belirtilen bir sabitle karşılaştırabilirsiniz. Örneğin, değerlerin iki veri kümelerinde aynı olup olmadığını belirleyecek sütunları karşılaştırabilirsiniz. Ya da, bir sayısal sütunda aykırı değerleri bulmak için izin verilen maksimum değer gibi bir sabit kullanabilirsiniz.  
  
-   [İşlemler](#arithmetic-operations)  
  
     **İşlemler** kategorisi temel matematik işlevlerini içerir: toplama, çıkarma, çarpma ve bölme. Sütunlarla veya sabitlerle çalışabilirsiniz. Örneğin, A sütunundaki değeri B sütunundaki değere ekleyebilirsiniz. Ya da, A sütunundaki her bir değerden daha önce hesaplanan bir ortalama gibi bir sabiti çıkartabilir.  
  
-   [Ondalı](#rounding-operations)  
  
     **Yuvarlama** kategorisi, çeşitli duyarlık düzeylerine yuvarlama, tavan, kat ve kesme gibi işlemler gerçekleştirmeye yönelik çeşitli işlevler içerir. Her iki ondalık ve tam sayı için duyarlık düzeyini belirtebilirsiniz.  
  
-   [Spec](#special-math-functions)  
  
     **Özel** kategori, özellikle eliptik integrals ve Gauss hata işlevi gibi veri bilimi içinde kullanılan matematik işlevlerini içerir.  
  
-   [Trigonometrik](#trigonometric-functions)  
  
     **Trigonometrik** kategori tüm standart trigonometrik işlevlerini içerir. Örneğin radyan ya da derece cinsinden radyan veya derece cinsinden işlem işlevlerini dönüştürebilirsiniz.
     Bu işlevler, tek bir değer sütununu giriş olarak alır, trigonometrik işlevi uygular ve sonuç olarak bir değer sütunu döndürür.  Bu nedenle, giriş sütununun uygun tür olduğundan ve belirtilen işlem için doğru değer türünü içerdiğinden emin olmanız gerekir.   

## <a name="how-to-configure-apply-math-operation"></a>Matematik Işlemini Uygula Işlemini yapılandırma  

**Matematik Işlemi uygulama** modülü, yalnızca sayı içeren en az bir sütun içeren bir veri kümesi gerektirir. Sayılar ayrık veya sürekli olabilir, ancak sayısal bir veri türünde olmalıdır, dize değil.

Aynı işlemi birden çok sayısal sütuna uygulayabilirsiniz, ancak tüm sütunlar aynı veri kümesinde olmalıdır. 

Bu modülün her örneği aynı anda yalnızca bir işlem türü gerçekleştirebilir. Karmaşık matematik işlemleri gerçekleştirmek için, **Math işlem** modülünün birkaç örneğini birlikte zincirlemek gerekebilir.  
  
1.  Işlem hattınızı **matematik işlemi Için uygulama** modülünü ekleyin.

1. En az bir sayısal sütun içeren bir veri kümesini bağlayın.  

1.  Hesaplamanın gerçekleştirileceği bir veya daha fazla kaynak sütun seçin.   
  
    - Seçtiğiniz herhangi bir sütun sayısal bir veri türü olmalıdır. 
    - Veri aralığı, seçilen matematik işlemi için geçerli olmalıdır. Aksi takdirde, bir hata veya NaN (sayı değil) sonucu oluşabilir. Örneğin, LN (-1,0) geçersiz bir işlemdir ve `NaN`değeriyle sonuçlanır.
  
1.  Gerçekleştirilecek matematik işleminin **türünü** seçmek için **Kategori** ' ye tıklayın.
    
1. Bu kategorideki listeden belirli bir işlem seçin.
  
1.  Her işlem türü için gereken ek parametreleri ayarlayın.  
  
1.  Math işleminin nasıl oluşturulmasını istediğinizi belirtmek için **çıkış modu** seçeneğini kullanın: 

    - **Ekle**. Giriş olarak kullanılan tüm sütunlar çıktı veri kümesine dahil edilir ve matematik işleminin sonuçlarını içeren bir ek sütun eklenir.
    - **InPlace**. Giriş olarak kullanılan sütunlardaki değerler, yeni hesaplanmış değerlerle değiştirilmiştir. 
    - **Resultonly**. Matematik işleminin sonuçlarını içeren tek bir sütun döndürülür.
  
1.  İşlem hattını çalıştırma.  
  
## <a name="results"></a>Sonuçlar

Sonuçları **append** veya **resultonly** seçeneklerini kullanarak oluşturursanız, döndürülen veri kümesinin sütun başlıkları işlemi ve kullanılan sütunları gösterir. Örneğin, **eşittir** işlecini kullanarak iki sütunu karşılaştırırsanız, sonuçlar şöyle görünür:  
  
-   **Eşittir (Col2_Col1)** , col2 ile test edildiğini gösterir.  
-   **Eşittir (Col2_ $10)** , sütun 2 ' yi sabit 10 ' a karşılaştırdığını gösterir.  

**InPlace** seçeneğini kullanıyor olsanız bile, kaynak veriler silinmez veya değiştirilmez; özgün veri kümesindeki sütun tasarımcıda hala kullanılabilir. Özgün verileri görüntülemek için, [sütun ekleme](add-columns.md) modülünü bağlanabilir ve **matematik işlemini Uygula işleminin**çıktısına katabilirsiniz.  
    
## <a name="basic-math-operations"></a>Temel matematik işlemleri 

**Temel** kategorideki işlevler genellikle bir sütundan tek bir değer alır, önceden tanımlanmış işlemi gerçekleştirir ve tek bir değer döndürür. Bazı işlevler için ikinci bir bağımsız değişken olarak bir sabit veya bir sütun kümesi belirtebilirsiniz.  
  
 Azure Machine Learning, **temel** kategoride aşağıdaki işlevleri destekler:  

### <a name="abs"></a>Mutlak

Seçili sütunların mutlak değerini döndürür.  
  
### <a name="atan2"></a>Atan2

Dört Çeyrekli ters tanjant döndürür.  

Nokta koordinatlarını içeren sütunları seçin. X koordinatına karşılık gelen ikinci bağımsız değişken için de bir sabit belirtebilirsiniz.  

MATLAB içindeki ATAN2 işlevine karşılık gelir.  

### <a name="conj"></a>Conj

Seçilen sütundaki değerlerin eşleniğini döndürür.  

### <a name="cuberoot"></a>Külifot

Seçili sütundaki değerler için Küp kökünü hesaplar.  

### <a name="doublefactorial"></a>Örneksel Solaci  
 Seçili sütundaki değerler için çift çarpınımını hesaplar. Çift çarpınımı, normal çarpınımı işlevinin bir uzantısıdır ve x!!. olarak gösterilir  

### <a name="eps"></a>EPS

Geçerli değer ile bir sonraki en yüksek, çift duyarlıklı sayı arasındaki boşluğun boyutunu döndürür. MATLAB içindeki EPS işlevine karşılık gelir.  
  
### <a name="exp"></a>Exp

Seçili sütundaki değerin üssüne yükseltilmiş e döndürür. Bu, Excel EXP işleviyle aynıdır.  

### <a name="exp2"></a>Exp2

Y = x * 2<sup>t</sup> için çözüm olan bağımsız değişkenlerin taban 2 üssünü döndürür; burada t, üs içeren değerlerin bir sütunudur.  

**Sütun kümesi**' nde, t değerlerini içeren sütunu seçin.

**Exp2** için ikinci bir bağımsız değişken x belirtebilirsiniz ve bu değer bir sabit ya da başka bir sütun olabilir. **İkinci bağımsız değişken türünde**, x çarpanını bir sabit olarak veya bir sütunda bir değer sağlayıp sağlamadığını belirtin.  

Örneğin, hem çarpanı hem de üs için {0,1,2,3,4,5} değerleri olan bir sütun seçerseniz, işlev {0, 2, 8, 24, 64 160) döndürür.  

### <a name="expminus1"></a>ExpMinus1 

Seçili sütundaki değerler için negatif üs döndürür.  

### <a name="factorial"></a>Imı
Seçilen sütundaki değerlerin faktöriyelini döndürür.  

### <a name="hypotenuse"></a>Hipotenüsü
Bir kenarın uzunluğunun bir değer sütunu olarak belirtildiği ve ikinci tarafın uzunluğu sabit ya da iki sütun olarak belirtilen bir üçgen için hipotenüsü hesaplar.  

### <a name="ln"></a>LN

Seçili sütundaki değerler için doğal logaritmayı döndürür.  

### <a name="lnplus1"></a>LnPlus1

Seçilen sütundaki değerler için doğal logaritmayı artı bir değer döndürür.  

### <a name="log"></a>Günlük

Seçilen sütundaki değerlerin günlüğünü belirtilen temel olarak döndürür.  

Temeli (ikinci bağımsız değişkeni) sabit ya da başka bir değer sütunu seçerek belirtebilirsiniz.  

### <a name="log10"></a>Log10

Seçili sütun için 10 tabanında logaritma değerlerini döndürür.  

### <a name="log2"></a>Log2

Seçili sütun için taban 2 logaritma değerlerini döndürür.  

### <a name="nthroot"></a>NthRoot
Belirttiğiniz n öğesini kullanarak değerin n. kökünü döndürür.  

**ColumnSet** seçeneğini kullanarak kökü hesaplamak istediğiniz sütunları seçin.  

**İkinci bağımsız değişken türünde**, kökü içeren başka bir sütun seçin veya kök olarak kullanılacak bir sabit belirtin.  

İkinci bağımsız değişken bir sütundaysa, sütundaki her bir değer, karşılık gelen satır için n değeri olarak kullanılır. İkinci bağımsız değişken bir sabit ise, **ikinci bağımsız değişken** metin kutusuna n değerini yazın.
### <a name="pow"></a>POW

Seçili olan sütundaki her bir değer için X ' in üssünü hesaplar.  

İlk olarak, **ColumnSet** seçeneğini kullanarak, float olması gereken **tabanı**içeren sütunları seçin.  

**İkinci bağımsız değişken türünde**, üs içeren sütunu seçin veya üs olarak kullanılacak bir sabit belirtin.  

İkinci bağımsız değişken bir sütunse, sütundaki her bir değer karşılık gelen satırın üs değeri olarak kullanılır. İkinci bağımsız değişken bir sabit ise **ikinci bağımsız değişken** metin kutusuna üs değerini yazın.  

### <a name="sqrt"></a>K

Seçilen sütundaki değerlerin kare kökünü döndürür.  

### <a name="sqrtpi"></a>SqrtPi

Seçili sütundaki her bir değer için değeri Pi ile çarpar ve sonucun kare kökünü döndürür.  

### <a name="square"></a>Square

Seçili sütundaki değerleri kareler.  

## <a name="comparison-operations"></a>Karşılaştırma işlemleri  

Azure Machine Learning tasarımcısında karşılaştırma işlevlerini, iki değer kümesini birbirlerine karşı test etmeniz gereken her zaman kullanın. Örneğin, bir işlem hattının bu karşılaştırma işlemlerini yapmanız gerekebilir:  

- Bir olasılık değerine karşı olasılık puanları modelinin bir sütununu değerlendirin.
- İki sonuç kümesinin aynı olup olmadığını belirler. Farklı olan her satır için, daha fazla işleme veya filtreleme için kullanılabilecek bir yanlış bayrak ekleyin.  

### <a name="equalto"></a>EqualTo

Değerler aynıysa true döndürür.  

### <a name="greaterthan"></a>GreaterThan

**Sütun kümesindeki** değerler belirtilen sabitten daha büyükse veya karşılaştırma sütunundaki karşılık gelen değerlerden daha büyükse true değerini döndürür.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

**Sütun kümesindeki** değerler belirtilen sabitten büyük veya buna eşitse true, karşılaştırma sütunundaki karşılık gelen değerlere eşit veya daha büyük bir değer döndürür.  

### <a name="lessthan"></a>LessThan

**Sütun kümesindeki** değerler belirtilen sabitten küçük veya karşılaştırma sütunundaki karşılık gelen değerlerden daha az olursa true değerini döndürür.  
  
### <a name="lessthanorequalto"></a>Okarşılandığından Alto Ile Lessme

**Sütun kümesindeki** değerler belirtilen sabitten küçük veya ona eşitse true, karşılaştırma sütunundaki karşılık gelen değerlere eşit veya daha az bir değer döndürür.  

### <a name="notequalto"></a>Not Qualto

**Sütun kümesindeki** değerler sabit veya karşılaştırma sütununa eşit değilse true, eşitse false değerini döndürür.  

### <a name="pairmax"></a>PairMax

Daha büyük değeri döndürür; **sütun kümesindeki** değer veya sabit ya da karşılaştırma sütunundaki değer.  

### <a name="pairmin"></a>PairMin

**Sütun kümesindeki** değer veya sabit ya da karşılaştırma sütunundaki değer olan küçük değeri döndürür  
  
##  <a name="arithmetic-operations"></a>Aritmetik işlemler   

Temel aritmetik işlemleri içerir: toplama ve çıkarma, bölme ve çarpma.  Çoğu işlem ikili olduğundan, iki sayı gerektiren bir işlem seçin ve ardından ilk ve ikinci bağımsız değişkenlerde kullanılacak sütunu veya rakamları seçersiniz.

Bölme ve çıkarma için sütunları seçme sırası, anlaşılır görünebilir; Ancak, sonuçların anlaşılması daha kolay hale getirmek için, sütun başlığı işlem adını ve sütunların kullanıldığı sırayı sağlar.

İşlem|Num1|Num2|Sonuç sütunu|Sonuç değeri|
----|----|----|----|----
|Ek olarak|1|5|Ekle (Num2_Num1)| 4|
|Anda|1|5|Birden çok (Num2_Num1)|5|
|StrA|1|5|Çıkart (Num2_Num1)|4|
|StrA|0|1|Çıkart (Num2_Num1)|0|
|Bölmenin|1|5|Böl (Num2_Num1)|5|
|Bölmenin|0|1|Böl (Num2_Num1)|Infinity|

### <a name="add"></a>Ekle

**Sütun kümesini**kullanarak kaynak sütunları belirtin ve ardından bu değerlere **ikinci bağımsız değişkende**belirtilen bir sayı ekleyin.  

Değerleri iki sütuna eklemek için, **sütun kümesi**kullanarak bir sütun veya sütun seçin ve ardından **ikinci bağımsız değişkeni**kullanarak ikinci bir sütun seçin.  

### <a name="divide"></a>Sayısına

**Sütundaki** değerleri bir sabitine veya **ikinci bağımsız değişkende**tanımlanan sütun değerlerine böler.  Diğer bir deyişle, önce böleni ve ardından Bölüneni seçer. Çıkış değeri, bölüm.

### <a name="multiply"></a>Çarpma

Belirtilen sabit veya sütun değerleriyle **ayarlanan sütundaki** değerleri çarpar.  

### <a name="subtract"></a>Çıkarma

**Sütun kümesi** seçeneğini kullanarak, farklı bir sütun seçerek üzerinde çalışılacak değer sütununu belirtin ( *eksilen*). Ardından, **ikinci bağımsız değişken** açılan listesini kullanarak çıkarılacak sayıyı ( *çıkarılan*) belirtin. Bir sabit ya da bir değer sütunu seçebilirsiniz.

##  <a name="rounding-operations"></a>Yuvarlama işlemleri 

Azure Machine Learning tasarımcı, çeşitli yuvarlama işlemlerini destekler. Birçok işlem için, yuvarlama sırasında kullanılacak duyarlık miktarını belirtmeniz gerekir. Sabit olarak belirtilen bir statik duyarlık düzeyi kullanabilir veya bir değer sütunundan elde edilen dinamik bir duyarlık değeri uygulayabilirsiniz.  

- Sabit kullanırsanız **duyarlık türünü** **sabit** olarak ayarlayın ve ardından **Sabit duyarlık** metin kutusunda basamak sayısını tamsayı olarak yazın. Tamsayı olmayan bir dize yazarsanız modül bir hata oluşturmaz, ancak sonuçlar beklenmedik olabilir.  

- Veri kümenizdeki her satır için farklı bir duyarlık değeri kullanmak istiyorsanız **duyarlık türünü** **ColumnSet**olarak ayarlayın ve ardından uygun duyarlık değerlerini içeren sütunu seçin.  

### <a name="ceiling"></a>Tavan

**Sütun kümesindeki**değerler için tavan döndürür.  

### <a name="ceilingpower2"></a>CeilingPower2

**Sütun kümesindeki**değerler için kare yukarı tavan döndürür.  

### <a name="floor"></a>Sını

**Sütun kümesindeki**değerlerin taban değerini belirtilen duyarlığa döndürür.  

### <a name="mod"></a>alma

**Sütun kümesindeki**değerlerin kesirli kısmını belirtilen duyarlığa döndürür.  

### <a name="quotient"></a>Bölümü

**Sütun kümesindeki**değerlerin kesirli kısmını belirtilen duyarlığa döndürür.  

### <a name="remainder"></a>Geri kalanında

**Sütun kümesindeki**değerler için kalanı döndürür.  

### <a name="rounddigits"></a>RoundDigits

**Sütun kümesindeki**değerleri döndürür ve 4/5 kuralına göre belirtilen basamak sayısına yuvarlanır.  

### <a name="rounddown"></a>RoundDown

**Sütun kümesindeki**değerleri döndürür, belirtilen basamak sayısına yuvarlanır.  

### <a name="roundup"></a>RoundUp

**Sütun kümesindeki**değerleri döndürür, belirtilen basamak sayısına yuvarlanır.  

### <a name="toeven"></a>Tobile

**Sütun kümesindeki**değerleri döndürür, hatta en yakın tam sayıya yuvarlanır.  

### <a name="toodd"></a>Totek

**Sütun kümesindeki**değerleri döndürür ve en yakın tamsayıya, tek sayıya yuvarlanır.  

### <a name="truncate"></a>Kesilemedi

Belirtilen duyarlık tarafından izin verilmeyen tüm basamakları kaldırarak **sütun kümesindeki** değerleri keser.  
  
## <a name="special-math-functions"></a>Özel matematik işlevleri

Bu kategori, genellikle veri bilimi 'nde kullanılan özel matematik işlevlerini içerir. Aksi belirtilmediği takdirde, işlev birli olur ve seçilen sütun veya sütunlardaki her bir değer için belirtilen hesaplamayı döndürür.  

### <a name="beta"></a>Beta

Euler 'ın Beta işlevinin değerini döndürür.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Tamamlanmamış eliptik tamsayısı değerini döndürür.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Tam eliptik integral (K) değerini döndürür.  

### <a name="erf"></a>İşlevi

Hata işlevinin değerini döndürür.  

Hata işlevi (Gauss Error işlevi olarak da bilinir), bir, diffıd 'nin tanımlanmasında kullanılan sigmoid şeklinin özel bir işlevidir.  

### <a name="erfc"></a>Erfc

Tamamlayıcı hata işlevinin değerini döndürür.  

Erfc, 1 – HATAİŞLEV (x) olarak tanımlanır.  

### <a name="erfscaled"></a>Erfölçekli

Ölçeklendirilmiş hata işlevinin değerini döndürür.  

Hata işlevinin ölçeklendirilen sürümü, aritmetik sınırın önüne geçmek için kullanılabilir.  

### <a name="erfinverse"></a>Erfınverse

Ters HATAİŞLEV işlevinin değerini döndürür.  

### <a name="exponentialintegralein"></a>Üs Alıntegralet

Üstel integral ei değerini döndürür.  

### <a name="gamma"></a>Gama

Gama işlevinin değerini döndürür.  

### <a name="gammaln"></a>GAMALN

Gama işlevinin doğal logaritmasını döndürür.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Regularized eksik gama işlevinin değerini döndürür.  

Bu işlev, bir sabit veya bir değer sütunu olarak sağlanılabilen ikinci bir bağımsız değişken alır.  

### <a name="gammaregularizedpinverse"></a>Gammaregularizedpters

Ters regularized tamamlanmamış gama işlevinin değerini döndürür.  

Bu işlev, bir sabit veya bir değer sütunu olarak sağlanılabilen ikinci bir bağımsız değişken alır.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Regularized eksik gama işlevinin değerini döndürür.  

Bu işlev, bir sabit veya bir değer sütunu olarak sağlanılabilen ikinci bir bağımsız değişken alır.  

### <a name="gammaregularizedqinverse"></a>Gammaregularizedqters

Ters Genelleştirilmiş regularized tamamlanmamış gama işlevinin değerini döndürür.

Bu işlev, bir sabit veya bir değer sütunu olarak sağlanılabilen ikinci bir bağımsız değişken alır.  

### <a name="polygamma"></a>Poligamma

Polygama işlevinin değerini döndürür.  

Bu işlev, bir sabit veya bir değer sütunu olarak sağlanılabilen ikinci bir bağımsız değişken alır.

##  <a name="trigonometric-functions"></a>Trigonometrik işlevleri 

Bu kategori, önemli trigonometrik ve ters kullanılan ölçüm işlevlerinin çoğunu Içerir. Tüm Trigonometrik İşlevler birli ve ek bağımsız değişken gerektirmez.  

### <a name="acos"></a>Acos

Sütun değerleri için arkkosinüsünü hesaplar.  

### <a name="acosdegree"></a>Acosderece

Sütun değerlerinin ark kosinüsünü derece cinsinden hesaplar.  

### <a name="acosh"></a>Acosh

Sütun değerlerinin hiperbolik ark kosinüsünü hesaplar.  

### <a name="acot"></a>ACOT

Sütun değerlerinin arckotanjant değerini hesaplar.  

### <a name="acotdegrees"></a>Acotderece

Sütun değerlerinin, derece cinsinden arckotanjant değerini hesaplar.  

### <a name="acoth"></a>Acoth

Sütun değerlerinin hiperbolik arckotanjant değerini hesaplar.  

### <a name="acsc"></a>ACSC

Sütun değerlerinin arckosekant değerini hesaplar.  

### <a name="acscdegrees"></a>Acscderece

Sütun değerlerinin, derece cinsinden arckosekant değerini hesaplar.  
### <a name="asec"></a>ASEC

Sütun değerlerinin arcsekant değerini hesaplar.  

### <a name="asecdegrees"></a>Asecderece

Sütun değerlerinin, derece cinsinden arcsekant değerini hesaplar.  

### <a name="asech"></a>AsecH

Sütun değerlerinin hiperbolik arcsekant değerini hesaplar.  

### <a name="asin"></a>Asin

Sütun değerlerinin arksinüsünü hesaplar.  

### <a name="asindegrees"></a>AsinDegrees

Sütun değerlerinin derece cinsinden arksinüsünü hesaplar.  

### <a name="asinh"></a>ASİNH

Sütun değerleri için hiperbolik ark sinüsünü hesaplar.  

### <a name="atan"></a>Atan

Sütun değerlerinin arktanjantını hesaplar.  

### <a name="atandegrees"></a>AtanDegrees

Sütun değerlerinin derece cinsinden ark tanjantını hesaplar.  

### <a name="atanh"></a>ATANH

Sütun değerlerinin hiperbolik ark tanjantını hesaplar.  

### <a name="cos"></a>Cos

Sütun değerlerinin kosinüsünü hesaplar.  

### <a name="cosdegrees"></a>Cosdereceler

Sütun değerlerinin derece cinsinden kosinüsünü hesaplar.  

### <a name="cosh"></a>Cosh

Sütun değerleri için hiperbolik kosinüsü hesaplar.  

### <a name="cot"></a>COT

Sütun değerleri için kotanjantı hesaplar.  

### <a name="cotdegrees"></a>Cotderece

Sütun değerleri için kotanjantı derece cinsinden hesaplar.  

### <a name="coth"></a>Coth
Sütun değerleri için hiperbolik kotanjantı hesaplar.  

### <a name="csc"></a>CSC

Sütun değerleri için kosekant hesaplar.  

### <a name="cscdegrees"></a>Cscdereceler

Sütun değerlerinin değerini derece cinsinden hesaplar.  

### <a name="csch"></a>Csch

Sütun değerleri için hiperbolik kovaryansı hesaplar.  

### <a name="degreestoradians"></a>Degreestoradyan

Dereceyi radyana dönüştürür.  

### <a name="sec"></a>Sn

Sütun değerlerinin Sekant değerini hesaplar.  

### <a name="asecdegrees"></a>Asecderece

Sütun değerleri için, derece cinsinden Sekant değerini hesaplar.  

### <a name="asech"></a>aSech

Sütun değerlerinin Hiperbolik Sekant değerini hesaplar.  

### <a name="sign"></a>İmzalayabilirsiniz

Sütun değerlerinin işaretini döndürür.  

### <a name="sin"></a>Sin

Sütun değerlerinin sinüsünü hesaplar.  

### <a name="sinc"></a>Sinc

Sütun değerlerinin sinüs-kosinüs değerini hesaplar.  

### <a name="sindegrees"></a>SinDegrees

Sütun değerleri için, derece cinsinden sinüsü hesaplar.  

### <a name="sinh"></a>Sinh

Sütun değerlerinin hiperbolik sinüsünü hesaplar.  

### <a name="tan"></a>Başlangıçtan

Sütun değerlerinin tanjantını hesaplar.  

### <a name="tandegrees"></a>Tandereceler

Bağımsız değişken için tanjantı derece cinsinden hesaplar.  

### <a name="tanh"></a>Tanh

Sütun değerlerinin hiperbolik tanjantını hesaplar.  
  
## <a name="technical-notes"></a>Teknik notlar

İkinci operatör olarak birden fazla sütun seçerken dikkatli olun. Sonuçların, tüm sütunlara bir sabit ekleme gibi basit olduğunu anlamak kolaydır. 

Veri kümenizin birden çok sütunu olduğunu varsayın ve veri kümesini kendi kendine eklersiniz. Sonuçlarda her bir sütun kendine aşağıdaki gibi eklenir:  
  
|Num1|Num2|Num3|Ekle (Num1_Num1)|Ekle (Num2_Num2)|Ekle (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Daha karmaşık hesaplamalar yapmanız gerekiyorsa, **matematik Işlemi Uygula**işleminin birden çok örneğini zincirleyebilirsiniz. Örneğin, **matematik Işlemi Uygula**' nın bir örneğini kullanarak iki sütun ekleyebilir ve sonra da ortalaması elde etmek üzere toplamı bir sabite bölmek Için **matematik işlemi Uygula** ' nın başka bir örneğini kullanabilirsiniz.  
  
Alternatif olarak, SQL, R veya Python betiği kullanarak tüm hesaplamaları tek seferde yapmak için aşağıdaki modüllerden birini kullanın:
 
+ [R betiğini Yürüt](execute-r-script.md)
+ [Python betiğini Yürüt](execute-python-script.md)
+ [SQL dönüşümünü Uygula](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 
