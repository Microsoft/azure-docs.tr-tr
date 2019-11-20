---
title: 'Filtre tabanlı özellik seçimi: modül başvurusu'
titleSuffix: Azure Machine Learning service
description: En büyük tahmine dayalı bir veri kümesindeki özellikleri belirlemek için Azure Machine Learning hizmetinde filtre tabanlı özellik seçimi modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: f8ae4982f7602cd7403a78517fec79fa95d33aa2
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717213"
---
# <a name="filter-based-feature-selection"></a>Filtre tabanlı özellik seçimi

Bu makalede, Azure Machine Learning tasarımcısında filtre tabanlı özellik seçimi modülünün nasıl kullanılacağı açıklanmaktadır (Önizleme). Bu modül, giriş veri kümenizdeki en büyük tahmine dayalı güce sahip sütunları belirlemenize yardımcı olur. 

Genel olarak, *Özellik seçimi* , belirli bir çıktı verildiğinde girişlere istatistiksel testlerin uygulanması işlemini ifade eder. Amaç, çıktının hangi sütunlarda daha tahmin olduğunu belirlemektir. Filtre tabanlı özellik seçimi modülü, aralarından seçim yapmak için birden çok özellik seçimi algoritması sağlar. Modül, Pearson bağıntı ve Chi-kare değerleri gibi bağıntı yöntemleri içerir. 

Filtre tabanlı özellik seçimi modülünü kullandığınızda bir veri kümesi sağlar ve etiketi veya bağımlı değişkeni içeren sütunu belirlersiniz. Sonra özellik önem derecesine göre kullanılacak tek bir yöntem belirtirsiniz.

Modül, tahmine dayalı güce göre derecelendirilen en iyi özellik sütunlarını içeren bir veri kümesi çıkarır. Ayrıca, özelliklerin adlarını ve bunların puanlarını seçili ölçüden çıkartır.  

## <a name="what-filter-based-feature-selection-is"></a>Filtre tabanlı hangi özellik seçimi  

Bu özellik seçimine yönelik bu modül, ilgisiz öznitelikleri bulmak için seçili ölçümü kullandığınız için "filtre tabanlı" olarak adlandırılır. Daha sonra modelinizdeki gereksiz sütunları filtreleyebilirsiniz. Verilerinize uyan tek bir istatistiksel ölçü seçersiniz ve modül her özellik sütunu için bir puan hesaplar. Sütunlar, özellik puanlarına göre derecelendirilen döndürülür. 

Doğru özellikleri seçerek sınıflandırmanın doğruluğunu ve verimliliğini de artırabilirsiniz. 

Tahmine dayalı modelinizi oluşturmak için genellikle en iyi puanları içeren sütunları kullanırsınız. Özellik seçimi puanlarını kötü olan sütunlar veri kümesinde bırakılabilir ve bir model oluşturduğunuzda yok sayılır.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Özellik seçimi ölçümü seçme

Filtre tabanlı özellik seçimi modülü, her sütunda bilgi değerini değerlendirmek için çeşitli ölçümler sağlar. Bu bölüm, her ölçüm için genel bir açıklama ve nasıl uygulandığını sağlar. Her bir ölçümü, [Teknik notlarda](#technical-notes) ve her bir modülü yapılandırmaya yönelik [yönergelerden](#how-to-configure-filter-based-feature-selection) kullanmaya yönelik ek gereksinimler bulabilirsiniz.

-   **Pearson bağıntı**  

    Pearson 'un bağıntı istatistiği veya Pearson 'un bağıntı katsayısı, `r` değeri olarak istatistiksel modellerle de bilinir. Her iki değişken için de bağıntı gücünü gösteren bir değer döndürür.

    Pearson 'un bağıntı katsayısı, iki değişkenin kovaryansını alarak ve standart sapmalarının çarpımı ile ayırarak hesaplanır. İki değişkendeki ölçek değişikliği, katsayısı etkilemez.  

-   **Çi kare**  

    İki yönlü kikare test, beklenen değerleri kapatma değerinin gerçek sonuçlara nasıl olduğunu ölçtüğünden oluşan istatistiksel bir yöntemdir. Yöntemi, değişkenlerin rastgele olduğunu varsayar ve bağımsız değişkenlerin yeterli bir örneğinden çizilir. Elde edilen kikare istatistiği, sonuçların beklenen (rastgele) sonuçtan ne kadar olduğunu gösterir.  


> [!TIP]
> Özel özellik seçimi yöntemi için farklı bir seçeneğe ihtiyaç duyuyorsanız, [R betiği Yürüt](execute-r-script.md) modülünü kullanın. 

## <a name="how-to-configure-filter-based-feature-selection"></a>Filtre tabanlı özellik seçimini yapılandırma

Standart istatistiksel bir ölçüm seçersiniz. Modül bir sütun çifti arasındaki bağıntıyı hesaplar: etiket sütunu ve bir özellik sütunu.

1.  İşlem hattınızla filtre tabanlı özellik seçimi modülünü ekleyin. Tasarımcı 'daki **Özellik seçimi** kategorisinde bulabilirsiniz.

2. Olası özellikler olan en az iki sütun içeren bir giriş veri kümesini bağlayın.  

    Bir sütunun çözümlendiğinden ve bir özellik puanının oluşturulduğundan emin olmak için, **ısfeature** özniteliğini ayarlamak Için [meta verileri Düzenle](edit-metadata.md) modülünü kullanın. 

    > [!IMPORTANT]
    > Giriş olarak sağladığınızı sütunların olası özellikler olduğundan emin olun. Örneğin, tek bir değer içeren bir sütun hiçbir bilgi değeri içermez.
    >
    > Bazı sütunlarda kötü özellikler olacağını biliyorsanız, bunları sütun seçiminden kaldırabilirsiniz. Ayrıca, bunları **kategorik**olarak Işaretlemek Için [meta verileri Düzenle](edit-metadata.md) modülünü de kullanabilirsiniz. 
3.  **Özellik Puanlama yöntemi**için, puanları hesaplamada kullanmak üzere aşağıdaki belirlenen istatistiksel yöntemlerden birini seçin.  

    | Yöntem              | Gereksinimler                             |
    | ------------------- | ---------------------------------------- |
    | Pearson bağıntı | Etiket metin veya sayısal olabilir. Özellikler sayısal olmalıdır. |
    Çi kare| Etiketler ve Özellikler metin veya sayısal olabilir. İki kategorik sütunun özellik önemini hesaplamak için bu yöntemi kullanın.|

    > [!TIP]
    > Seçili ölçüyü değiştirirseniz, diğer tüm seçimler sıfırlanır. Bu nedenle, önce bu seçeneği ayarladığınızdan emin olun.
4.  Yalnızca daha önce özellik olarak işaretlenmiş sütunlarda puan oluşturmak için yalnızca **Özellik sütunları üzerinde** çalışma seçeneğini belirleyin. 

    Bu seçeneği temizlerseniz, modül, **istenen özellikler sayısında**belirtilen sütun sayısına kadar, diğer bir deyişle ölçütü karşılayan herhangi bir sütun için bir puan oluşturur.  

5.  **Hedef sütun**için, etiket sütununu ada veya dizinine göre seçmek için **sütun seçiciyi Başlat** ' ı seçin. (Dizinler tek tabanlıdır.)  
    İstatistiksel bağıntı içeren tüm yöntemler için bir etiket sütunu gereklidir. Etiket sütunu veya birden çok etiket sütunu seçerseniz modül bir tasarım zamanı hatası döndürür. 

6.  **İstenen özellik sayısı**için, sonuç olarak döndürülmesini istediğiniz özellik sütunlarının sayısını girin:  

    - Belirtebileceğiniz özelliklerin en az biri bir tane olabilir, ancak bu değeri artırmanız önerilir.  

    - İstenen özellik sayısı, veri kümesindeki sütun sayısından büyükse, tüm özellikler döndürülür. Sıfır puan içeren özellikler de döndürülür.  

    - Özellik sütunlarından daha az sonuç sütunu belirtirseniz, Özellikler azalan puana göre sıralanır. Yalnızca en üstteki Özellikler döndürülür. 

7.  İşlem hattını çalıştırın veya filtre tabanlı özellik seçimi modülünü seçin ve ardından **Seçileni Çalıştır**' ı seçin.


## <a name="results"></a>Sonuçlar

İşlem tamamlandıktan sonra:

+ Analiz edilen Özellik sütunlarının tam listesini ve puanlarını görmek için, modüle sağ tıklayın. **Özellikler**' i seçin ve ardından **Görselleştir**' i seçin.  

+ Özellik seçim ölçütleriniz temel alınarak oluşturulan veri kümesini görüntülemek için modüle sağ tıklayın. **Veri kümesi**' ni ve ardından **Görselleştir**' i seçin. 

Veri kümesi beklediğinizden daha az sütun içeriyorsa modül ayarlarını kontrol edin. Ayrıca, giriş olarak belirtilen sütunların veri türlerini kontrol edin. Örneğin, **istenen özelliklerin sayısını** 1 olarak ayarlarsanız, çıkış veri kümesi yalnızca iki sütun içerir: etiket sütunu ve en yüksek dereceli özellik sütunu.


##  <a name="technical-notes"></a>Teknik notlar  

### <a name="implementation-details"></a>Uygulama ayrıntıları

Bir sayısal özellikte ve kategorik bir etikette Pearson bağıntı kullanırsanız, özellik puanı aşağıdaki gibi hesaplanır:  

1.  Kategorik sütunundaki her bir düzey için sayısal sütunun koşullu ortalaması olarak hesaplama yapın.  

2.  Koşullu sütunu, sayısal sütunla bağıntılı.  

### <a name="requirements"></a>Gereksinimler  

-   **Etiket** veya **puan** sütunu olarak belirlenmiş herhangi bir sütun için bir özellik seçimi puanı üretilemez.  

-   Yöntemi tarafından desteklenmeyen bir veri türü sütunuyla bir Puanlama yöntemi kullanmaya çalışırsanız, modül bir hata oluşturacak. Ya da sütuna sıfır puanı atanır.  

-   Bir sütun mantıksal (true/false) değerler içeriyorsa, `True = 1` ve `False = 0`olarak işlenir.  

-   Bir sütun **etiket** veya **puan**olarak belirlendiyse bir özellik olamaz.  

### <a name="how-missing-values-are-handled"></a>Eksik değerler nasıl işlenir  

-   Tüm eksik değerlere sahip herhangi bir sütun hedef (etiket) sütunu olarak belirtemezsiniz.  

-   Bir sütunda eksik değerler varsa, bu, sütun Puanını hesaplarken modül onları yoksayar.  

-   Özellik sütunu olarak belirlenmiş bir sütunda tüm eksik değerler varsa, modül sıfır puanı atar.   


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 

