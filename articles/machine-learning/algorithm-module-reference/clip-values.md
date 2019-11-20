---
title: Klip değerleri
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning hizmetindeki Clip Values modülünü kullanarak aykırı değerleri ve klipleri algılamalarını ve bu değerlerin değiştirilmesini öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 704b7c8a0c55bdcdd69bd8a44b0f9f44e8ddf457
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493832"
---
# <a name="clip-values"></a>Klip değerleri

Bu makalede bir Azure Machine Learning Designer (Önizleme) modülü açıklanır.

Belirtilen eşiğin üstünde veya altında olan veri değerlerini bir ortalama, sabit veya diğer bir alternatif değerle tanımlamak ve isteğe bağlı olarak değiştirmek için klip değerleri modülünü kullanın.  

Modüle, kırpmak istediğiniz sayıları içeren bir veri kümesine bağlanırsınız, birlikte çalışmak istediğiniz sütunları seçin ve ardından bir eşik veya değer aralığı ve bir değiştirme yöntemi ayarlayın. Modül yalnızca sonuçları veya değiştirilen değerleri özgün veri kümesine eklenmiş şekilde yazabilir.

## <a name="how-to-configure-clip-values"></a>Klip değerlerini yapılandırma

Başlamadan önce, kırpmak istediğiniz sütunları ve kullanılacak yöntemi belirlemeniz gerekir. Önce küçük bir veri alt kümesinde herhangi bir kırpma yöntemini test etmenizi öneririz.

Modül, seçime dahil ettiğiniz **Tüm** sütunlara aynı ölçütleri ve değiştirme yöntemini uygular. Bu nedenle, değiştirmek istemediğiniz sütunları dışlediğinizden emin olun.

Bazı sütunlara kırpma yöntemleri veya farklı ölçütler uygulamanız gerekiyorsa, her bir benzer sütun kümesi için **klip değerlerinin** yeni bir örneğini kullanmanız gerekir.

1.  Ardışık düzene **Clip Values** modülünü ekleyin ve değiştirmek istediğiniz veri kümesine bağlayın. Bu modülü, **veri dönüştürme**altında, **ölçek ve azalt** kategorisinde bulabilirsiniz. 
  
1.  Sütun **listesinde**, **kırpma değerlerinin** uygulanacağı sütunları seçmek için sütun seçiciyi kullanın.  
  
1.  **Eşik kümesi**için, açılan listeden aşağıdaki seçeneklerden birini seçin. Bu seçenekler, kabul edilebilir değerler ve kırpılmakta olması gereken değerler için üst ve alt sınırları nasıl ayarlayabileceğiniz belirlenir.  
  
    - **Clienppeaks**: değerleri en üste kadar kırpdığınızda, yalnızca bir üst sınır belirtirsiniz. Bu sınır değerinden büyük değerler değiştirilmiştir.
  
    -  **Clipsubpeaks**: değerleri alt seviyeye göre kırpdığınızda, yalnızca alt sınır belirtirsiniz. Sınır değerinden daha az olan değerler değiştirilmiştir.  
  
    - **Clienppeaksandsubpeaks**: değerleri en üst ve alt sınır değerlerine göre kırpdığınızda, hem üst hem de alt sınırları belirtebilirsiniz. Bu aralığın dışında kalan değerler değiştirilmez. Sınır değerleriyle eşleşen değerler değiştirilmez.
  
1.  Önceki adımda yaptığınız seçiminize bağlı olarak, aşağıdaki eşik değerlerini ayarlayabilirsiniz: 

    + **Alt eşik**: yalnızca **Clipsubpeaks** ' i seçerseniz gösterilir
    + **Üst eşik**: yalnızca **Clienppeaks** ' i seçerseniz gösterilir
    + **Eşik**: yalnızca **Clippeaksandsubpeaks** ' i seçerseniz gösterilir

    Her eşik türü için **sabit** veya **yüzdebirlik**' i seçin.

1. **Sabit**' i seçerseniz, metin kutusuna en büyük veya en küçük değeri yazın. Örneğin, 999 değerini bir yer tutucu değeri olarak kullandığınızı varsayalım. Üst eşik için **sabit** ' i seçebilir ve **üst eşik için sabit değer**olarak 999 yazabilirsiniz.
  
1. **Yüzdebirlik**değerini seçerseniz, sütun değerlerini bir yüzdebirlik aralığıyla sınırlandırın. 

    Örneğin, yalnızca 10-80 yüzdebirlik aralığındaki değerleri tutmak istediğinizi varsayalım ve diğerlerinin yerini alır. **Yüzdelik**değer ' i seçin ve ardından, **daha düşük eşiğin yüzde birlik değeri**için 10 yazın ve **üst eşiğe yönelik yüzdebirlik değeri**için 80 yazın. 

    Yüzdebirlik aralıklarının nasıl kullanılacağına ilişkin bazı örnekler için [yüzdebirlik değeri](#examples-for-clipping-using-percentiles) adresindeki bölümüne bakın.  
  
1.  Bir değiştirme değeri tanımlayın.

    Belirttiğiniz sınırlara tam olarak eşleşen sayıların izin verilen değer aralığı içinde olduğu kabul edilir ve bu nedenle değiştirilmez. Belirtilen aralığın dışında kalan tüm sayılar değiştirme değeri ile değiştirilmiştir. 
  
    + **Tepe noktaları için yedek değer**: belirtilen eşikten daha büyük olan tüm sütun değerlerinin yerine konacak değeri tanımlar.  
    + **Subpeaks Için ikame değer**: belirtilen eşikten daha küçük olan tüm sütun değerleri için alternatif olarak kullanılacak değeri tanımlar.  
    + **Clippeaksandsubpeaks** seçeneğini kullanırsanız, üst ve alt kırpılan değerler için ayrı bir değiştirme değeri belirtebilirsiniz.  

    Aşağıdaki değiştirme değerleri desteklenir:  
  
    -   **Eşik**: kırpılan değerleri belirtilen eşik değeriyle değiştirir.  
  
    -   **Ortalama**: kırpılan değerleri sütun değerlerinin ortalaması ile değiştirir. Ortalama değerler kırpılmadan önce hesaplanır.  
  
    -   **Ortanca**: kırpılan değerleri sütun değerlerinin ortancası ile değiştirir. Ortanca değerler kırpılmadan önce hesaplanır.   
  
    -   **Yok**. Kırpılan değerleri eksik (boş) değeriyle değiştirir.  
  
1.  **Gösterge sütunları ekle**: belirtilen kırpma işleminin o satırdaki verilere uygulanıp uygulanmadığını belirten yeni bir sütun oluşturmak istiyorsanız bu seçeneği belirleyin. Yeni bir kırpma ve değiştirme değerleri kümesini sınarken Bu seçenek faydalıdır.  
  
1. **Üzerine yazma bayrağı**: yeni değerlerin nasıl oluşturulmasını istediğinizi belirtin. Varsayılan olarak, **klip değerleri** en yoğun değerleri istenen eşiğe kırparak yeni bir sütun oluşturur. Yeni değerler özgün sütunun üzerine yazılır.  
  
    Özgün sütunu tutmak ve kırpılan değerlerle yeni bir sütun eklemek için bu seçeneğin seçimini kaldırın.  
  
1.  İşlem hattını çalıştırma.  
  
    **Clip Values** modülünün çıktısına sağ tıklayın ve değerleri gözden geçirmek Için **Görselleştir** ' i seçin ve kırpma işleminin beklentilerinizi karşılamasını sağlayın.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Yüzdebirlik değeri kullanarak kırpma örnekleri

Yüzdebirlik değeri tarafından nasıl kırpılmasının çalıştığını anlamak için, 1-10 değerlerinin her birini içeren 10 satırlık bir veri kümesini düşünün.  
  
- En büyük eşik olarak yüzdebirlik değerini kullanıyorsanız, 90. yüzdebirlik değerinde, veri kümesindeki tüm değerlerin yüzde 90 ' unun bu değerden küçük olması gerekir.  
  
- Yüzdebirlik değerini alt eşik olarak kullanıyorsanız, 10 yüzdelik değerde, veri kümesindeki tüm değerlerin yüzde 10 ' u bu değerden küçük olmalıdır.  
  
1.  **Eşik kümesi**Için **Clippeaksandsubpeaks**' i seçin.  
  
1.  **Üst eşik**için **yüzdebirlik**' ü seçin ve **yüzdebirlik numarası**için 90 yazın.  
  
1.  **Büyük alternatif değeri**Için **eksik değer**' i seçin.  
  
1.  **Daha düşük eşik**Için, **yüzdebirlik**' ü seçin ve **yüzdebirlik numarası**için 10 yazın.  
  
1.  **Daha düşük yedek değer**Için **eksik değer**' i seçin.  
  
1.  **Üzerine yazma bayrağı**seçeneğinin işaretini kaldırın ve **gösterge sütunu Ekle**seçeneğini belirleyin.  
  
Şimdi de aynı işlem hattını, üst yüzdebirlik eşiği olarak 60 ve alt yüzdebirlik eşiği olarak 30 ' u kullanarak deneyin ve eşik değerini değiştirme değeri olarak kullanın. Aşağıdaki tabloda bu iki sonuç karşılaştırılmaktadır:  
  
1.  Eksik ile Değiştir; Üst eşik = 90; Alt eşik = 20  
  
1.  Eşikle değiştirin; Üst yüzdebirlik = 60; Alt yüzdebirlik = 40  
  
|Özgün veriler|Eksik ile Değiştir|Eşikle Değiştir|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|DEĞERI<br /><br /> DEĞERI<br /><br /> 3, YANLıŞ<br /><br /> 4, YANLıŞ<br /><br /> 5, YANLıŞ<br /><br /> 6, YANLıŞ<br /><br /> 7, YANLıŞ<br /><br /> 8, YANLıŞ<br /><br /> 9, YANLıŞ<br /><br /> DEĞERI|4, DOĞRU<br /><br /> 4, DOĞRU<br /><br /> 4, DOĞRU<br /><br /> 4, DOĞRU<br /><br /> 5, YANLıŞ<br /><br /> 6, YANLıŞ<br /><br /> 7, DOĞRU<br /><br /> 7, DOĞRU<br /><br /> 7, DOĞRU<br /><br /> 7, DOĞRU| 
 
## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 
