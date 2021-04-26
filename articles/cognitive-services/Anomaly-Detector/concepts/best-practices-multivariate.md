---
title: Anomali algılayıcı multivariate API 'sini kullanmak için en iyi uygulamalar
titleSuffix: Azure Cognitive Services
description: Anomali algılayıcısı kullanmanın en iyi yöntemleri, zaman serisi verilerinize anomali algılama uygulamak için çok değişen API 'leri.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: anomali algılama, makine öğrenimi, algoritmalar
ms.openlocfilehash: 7de25b4a099c706c05b32b52492096923033f822
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315710"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>Çok değişen zaman serisi anomali algılayıcısı en iyi uygulamaları

Bu makalede, çok sayıda anomali algılayıcı API 'Leri kullanılırken izlenecek önerilen uygulamalar etrafında rehberlik sağlanır.

## <a name="how-to-prepare-data-for-training"></a>Eğitim için verileri hazırlama

Anomali algılayıcı çok değişkenli API 'Leri kullanmak için, algılamayı kullanmadan önce kendi modelimizi eğitmemiz gerekir. Eğitim için kullanılan veriler, her zaman serisinin iki sütunlu, zaman damgası ve değer içeren CSV biçiminde olması gereken bir zaman dizisidir. Tüm zaman serileri tek bir zip dosyasında sıkıştırılmalıdır ve Azure Blob depolama alanına yüklenmelidir. Varsayılan olarak, zaman serisi değişkenini temsil etmek için dosya adı kullanılacaktır. Alternatif olarak, değişkenin adını. zip dosya adından farklı olacak şekilde isterseniz, dosyanın bir ek meta.jsZIP dosyasına dahil edilebilir. [BLOB SAS (paylaşılan erişim imzaları) URL 'si](../../../storage/common/storage-sas-overview.md)oluşturduğumuzdan, bunu eğitim için kullanabiliriz.

## <a name="data-quality-and-quantity"></a>Veri kalitesi ve miktarı

Anomali algılayıcı çok değişkenli API 'si, geçmiş verilerden normal desenleri öğrenmek için son teknoloji ürünü derin sinir ağlarını kullanır ve gelecekteki değerlerin bozukluklar olup olmadığını tahmin eder. Eğitim verilerinin kalitesi ve miktarı, en iyi modeli eğitmek için önemlidir. Model, geçmiş verilerden normal desenleri öğrendiğinde, eğitim verileri sistemin genel normal durumunu temsil etmelidir. Eğitim verileri anormalilerin tamamsın bu tür desenler öğrenilecek şekilde modelin bu tür desenleri öğreni zor olur. Ayrıca, modelde milyonlarca parametre vardır ve en uygun parametre kümesini öğrenmek için en az sayıda veri noktasına ihtiyacı vardır. Genel kural, modeli doğru bir şekilde eğitebilmeniz için değişken başına en az 15.000 veri noktası sağlamanız gerekir. Daha fazla veri, model daha iyidir.

Birçok zaman serisinde eksik değerler olduğu yaygındır ve bu, eğitilen modellerin performansını etkileyebilir. Her bir zaman serisinin eksik oranı makul bir değer altında denetlenmelidir. %90 değeri eksik olan bir zaman serisi sistemin normal desenleri hakkında çok az bilgi sağlar. Daha da kötüsü, model genellikle düz parçalar veya sabit değerler olan normal desenler olarak doldurulmuş değerleri kabul edebilir. ' De yeni veri akışı yapıldığında veriler, bozukluklar olarak algılanabilir.

Önerilen en büyük değer eşiği %20 ' dir, ancak bazı koşullarda daha yüksek bir eşik kabul edilebilir. Örneğin, bir dakikalık ayrıntı düzeyi ve saatlik ayrıntı düzeyi olan başka bir zaman serisine sahip bir zaman seriniz varsa.  Her saat, veri başına 60 veri noktası ve saatlik veriler için 1 veri noktası vardır. Bu, saatlik verilerin eksik oranının% 98,33 olduğu anlamına gelir. Ancak saatlik zaman serisi genellikle çok fazla dalgalanmadığında saatlik verileri tek değerle doldurmanız ince bir değer olacaktır.

## <a name="parameters"></a>Parametreler

### <a name="sliding-window"></a>Kayan pencere

Multivarianomali algılama, giriş olarak uzunluğu veri noktalarının bir kesimini alır `slidingWindow` ve sonraki veri noktasının bir anomali olup olmadığı konusunda karar verir. Örnek uzunluk arttıkça, bir karar için daha fazla veri kabul edilir. İçin uygun bir değer seçerken iki şeyi aklınızda tutmanız gerekir `slidingWindow` : giriş verilerinin özellikleri ve eğitim/çıkarım süresi ve potansiyel performans iyileştirmesi arasında denge. `slidingWindow` 28 ve 2880 arasında bir tamsayı oluşur. Verilerinizin düzenli olup olmadığına ve verilerinizin örnekleme hızına göre giriş olarak kaç veri noktasının kullanıldığına karar verebilirsiniz.

Verileriniz periyodik olduğunda, giriş olarak 1-3 döngü ekleyebilirsiniz ve verileriniz, dakika düzeyi veya ikinci düzey veriler gibi yüksek bir sıklıkta (küçük ayrıntı düzeyi) örneklenir, girdi olarak daha fazla veri seçebilirsiniz. Diğer bir sorun da daha uzun girişler eğitim/çıkarım süresine neden olabilir ve daha fazla giriş noktasının performans kazanmasına yol açacağından emin olmaz. Çok az veri noktası olmasına karşın, modelin en iyi çözüme yakınsamasını zorlaştırıyor olabilir. Örneğin, giriş verilerinde yalnızca iki noktaya sahip olduğunda bozukluklar tespit etmek zordur.

### <a name="align-mode"></a>Hizalama modu

Parametresi, `alignMode` zaman damgalarına birden çok zaman serisini nasıl hizalamak istediğinizi belirtmek için kullanılır. Bunun nedeni, çoğu zaman serisinin eksik değerleri vardır ve bunları daha fazla işlemeden önce aynı zaman damgalarına hizalamız gerekir. Bu parametre ve için iki seçenek vardır `inner join` `outer join` . `inner join` , **Her serinin** bir değere sahip olduğu zaman damgalarına yönelik algılama sonuçlarını raporlarız, ancak `outer join` bir değer içeren **herhangi bir zaman serisinde** algılama sonuçlarını zaman damgalarına bildirecağız.  **`alignMode` Ayrıca modelin giriş dizisini etkiler, bu** nedenle `alignMode` sonuçlar önemli ölçüde farklı olabileceğinden senaryonuz için uygun bir seçim yapın.

Burada, farklı değerleri açıklamak için bir örnek gösterilmektedir `alignModel` .

#### <a name="series1"></a>Series1

|timestamp | değer|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Series2

timestamp | değer  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>İç birleşim iki seri
  
timestamp | Series1 | Series2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>Dış birleşim iki seri

timestamp | series1 | series2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| NA | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | NA

### <a name="fill-not-available-na"></a>Doldur yok (yok)

Değişkenler dış birleşime göre zaman damgasına hizalandıktan sonra bazı `Not Available` değişkenlerde bazı ( `NA` ) bir değer olabilir. Bu değeri dolduracak yöntemi belirtebilirsiniz. İçin seçenekler,, `fillNAMethod` , `Linear` `Previous` `Subsequent`  `Zero` ve `Fixed` .

| Seçenek     | Yöntem                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| Doğrusal     | Değerleri doğrusal ilişkilendirmeden doldur                                                           |
| Önceki   | Boşlukları dolduracak son geçerli değeri yayar. Örnek: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| Sonra | Boşlukları doldurmanız için sonraki geçerli değeri kullanın. Örnek: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| Sıfır       | YOK değerlerini 0 ile doldurur.                                                                           |
| Sabit      | Değeri, içinde sağlanması gereken belirtilen geçerli bir değer ile doldurur `paddingValue` .          |

## <a name="model-analysis"></a>Model Analizi

### <a name="training-latency"></a>Eğitim gecikmesi

Çoklu varianomali algılama eğitimi zaman alıcı olabilir. Özellikle, eğitim için çok fazla zaman damgalarına sahip olduğunuzda. Bu nedenle, eğitim sürecinin bir kısmının zaman uyumsuz olarak tamamlanmasına izin veririz. Genellikle, kullanıcılar model API 'SI aracılığıyla eğitme görevi gönderir. Sonra model durumunu aracılığıyla alın `Get Multivariate Model API` . Burada, eğitim tamamlanmadan önce kalan süreyi nasıl ayıklayabileceğiniz gösterilmektedir. Çok değişkenli model API 'SI yanıtını Al öğesinde adlı bir öğe vardır `diagnosticsInfo` . Bu öğede bir `modelState` öğesi vardır. Kalan süreyi hesaplamak için ve kullanmanız gerekir `epochIds` `latenciesInSeconds` . Bir dönem, eğitim verileri aracılığıyla bir bütün döngüyü temsil eder. Her 10 dönemlerinde, durum bilgilerini göndereceğiz. Toplam olarak 100 dönemler için eğeceğiz, gecikme süresi bir dönem ne kadar sürdüğünü gösterir. Bu bilgilerle, modeli eğitebilmeniz için kalan zaman kalmadı.

### <a name="model-performance"></a>Model performansı

Denetimli olmayan bir model olarak çok sayıda anomali algılama. Bunu değerlendirmek için en iyi yol, anomali sonuçlarını el ile denetkullanmaktır. Çok değişken modeli al yanıtında, model performansını çözümleyebilmemiz için bazı temel bilgiler sağlıyoruz. `modelState`Get multivariate model API 'si tarafından döndürülen öğesinde, `trainLosses` `validationLosses` modelin beklenen şekilde eğitilip eğitilmediğini değerlendirmek için ve kullanabiliriz. Çoğu durumda, iki kayıp de kademeli olarak azalır. ' De model performansını analiz etmek için başka bir bilgi parçası `variableStates` . Değişkenler durum listesi, `filledNARatio` azalan düzende sıralanır. Performanmızda ne kadar büyük olursa, genellikle bu mümkün olduğunca çok azalmamız gerekir `NA ratio` . `NA` bir zaman damgası perspektifinden eksik değerler veya hizalanmamış değişkenlerin nedeni olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç](../quickstarts/client-libraries-multivariate.md).
- [Güç anomali algılayıcısının çok sayıda olduğunu gösteren temeldeki algoritmalar hakkında bilgi edinin](https://arxiv.org/abs/2009.02040)