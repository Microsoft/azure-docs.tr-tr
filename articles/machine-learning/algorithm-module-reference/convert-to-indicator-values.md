---
title: Gösterge Değerlerine dönüştürme
titleSuffix: Azure Machine Learning
description: Kategorik sütunları bir dizi ikili gösterge sütununa dönüştürmek için Azure Machine Learning tasarımcısında gösterge değerlerini Dönüştür modülünü kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 81b3c113f46428327842c1555fdd1934e9ae8762
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420861"
---
# <a name="convert-to-indicator-values"></a>Gösterge Değerlerine dönüştürme
Bu makalede bir Azure Machine Learning Designer modülü açıklanmaktadır.

Kategorik değerlerini içeren sütunları bir dizi ikili gösterge sütununa dönüştürmek için Azure Machine Learning tasarımcısında **gösterge değerlerini Dönüştür** modülünü kullanın.  

Bu modül aynı zamanda gösterge değerlerine dönüştürmek için kullanılan dönüşümün tanımını da verir. Bu dönüştürmeyi, [dönüştürme modülünü Uygula](apply-transformation.md) ' yı kullanarak aynı şemaya sahip diğer veri kümelerinde yeniden kullanabilirsiniz.

## <a name="how-to-configure-convert-to-indicator-values"></a>Gösterge değerlerine dönüştürme yapılandırma

1.  **Gösterge değerlerini Dönüştür** ' ü bulun ve ardışık düzen taslağınızla sürükleyin. Bu modülü, **veri dönüştürme** kategorisi altında bulabilirsiniz.
    > [!NOTE]
    > Hedef sütunları kategorik olarak işaretlemek için, **ındiciator değerlerini Dönüştür** modülüne göre [verileri Düzenle](edit-metadata.md) modülünü kullanabilirsiniz.

1. **Gösterge değerlerini Dönüştür** modülüne dönüştürmek istediğiniz sütunları içeren veri kümesine bağlayın. 

1. Bir veya daha fazla kategorik sütun seçmek için **sütunu Düzenle** ' yi seçin.

1. **Yalnızca** yeni Boole sütunlarının çıktısını almak istiyorsanız **kategorik sütunların üzerine yaz** seçeneğini belirleyin. Varsayılan olarak, bu değer kapalıdır.
    

    > [!TIP]
    >  Üzerine yazma seçeneğini belirlerseniz, kaynak sütun aslında silinmez veya değiştirilmez. Bunun yerine, yeni sütunlar oluşturulur ve çıkış veri kümesinde sunulur ve kaynak sütunu çalışma alanında kullanılabilir kalır. Özgün verileri görmeniz gerekirse, kaynak sütunu yeniden eklemek için istediğiniz zaman [sütun Ekle](add-columns.md) modülünü kullanabilirsiniz.

1. İşlem hattını gönderme.

## <a name="results"></a>Sonuçlar

Bir sunucunun yüksek, orta veya düşük bir hata olup olmadığını belirten puanlarla bir sütununuz olduğunu varsayalım.  

| Sunucu KIMLIĞI | Başarısızlık puanı |
| --------- | ------------- |
| 10301     | Düşük           |
| 10302     | Orta        |
| 10303     | Yüksek          |

**Gösterge değerlerine Dönüştür** uyguladığınızda, tasarımcı tek bir etiket sütununu, Boolean değerler içeren birden çok sütuna dönüştürür:  

| Sunucu KIMLIĞI | Başarısızlık puanı-düşük | Başarısızlık puanı-orta | Başarısızlık puanı-yüksek |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Dönüştürmenin nasıl çalıştığı aşağıda verilmiştir:  

-   Riski açıklayan **başarısızlık puanı** sütununda yalnızca üç olası değer vardır (yüksek, orta ve düşük) ve eksik değer yoktur. Bu nedenle, tam olarak üç yeni sütun oluşturulur.  

-   Yeni gösterge sütunları, bu model kullanılarak kaynak sütunun sütun başlıklarına ve değerlerine göre adlandırılır: *\<source column>- \<data value>* .  

-   Her sunucunun yalnızca bir risk derecelendirmesi olduğundan, tam olarak bir gösterge sütununda 1 ve diğer tüm gösterge sütunlarında 0 olmalıdır.  

Artık üç gösterge sütununu bir makine öğrenimi modelinde özellikler olarak kullanabilirsiniz.

Modül iki çıkış döndürüyor:

- **Sonuç veri kümesi**: dönüştürülmüş gösterge değerleri sütunları olan bir veri kümesi. Temizleme için seçilmemiş sütunlar da "geçirilir".
- **Gösterge değerleri dönüştürmesi**: çalışma alanınıza kaydedilebilecek ve yeni verilere daha sonra uygulanabilen gösterge değerlerine dönüştürmek için kullanılan bir veri dönüştürmesi.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Yeni verilere kaydedilmiş bir gösterge değerleri işlemi uygulama

Gösterge değerleri işlemlerini sıklıkla tekrarlamanız gerekirse, veri işleme adımlarınızı aynı veri kümesiyle yeniden kullanmak için bir *dönüşüm* olarak kaydedebilirsiniz. Bu, sık sık yeniden içeri aktarmanız ve ardından aynı şemaya sahip verileri temizleyebilmeniz yararlı olur.

1. İşlem hattınızda [dönüştürme modülünü Uygula](apply-transformation.md) ' yı ekleyin.

1. Temizlemek istediğiniz veri kümesini ekleyin ve veri kümesini sağ taraftaki giriş bağlantı noktasına bağlayın.

1. Tasarımcı 'nın sol bölmesindeki **veri dönüştürme** grubunu genişletin. Kaydedilen dönüştürmeyi bulun ve ardışık düzene sürükleyin.

1. Kaydedilen dönüşümü, [dönüşüm Uygula](apply-transformation.md)'nın sol giriş bağlantı noktasına bağlayın.

   Kaydedilen bir dönüşüm uyguladığınızda, hangi sütunların dönüştürüluygulanacağını seçemezsiniz. Bunun nedeni, dönüştürmenin tanımlanması ve özgün işlemde belirtilen veri türlerine otomatik olarak uygulanmaları.

1. İşlem hattını gönderme.
 
## <a name="technical-notes"></a>Teknik notlar  

Bu bölümde, sık sorulan soruların uygulama ayrıntıları, ipuçları ve yanıtları yer almaktadır.

### <a name="usage-tips"></a>Kullanım ipuçları

-   Yalnızca kategorik olarak işaretlenen sütunlar gösterge sütunlarına dönüştürülebilir. Aşağıdaki hatayı görürseniz, seçtiğiniz sütunlardan biri kategorik değildir:  

     Hata 0056: adı olan sütun  \<column name> izin verilen bir kategoride değil.  

     Varsayılan olarak, çoğu dize sütunu dize özellikleri olarak işlenir, bu nedenle bunları doğrudan [düzenleme meta verilerini](edit-metadata.md)kullanarak kategorik olarak işaretlemeniz gerekir.  

-   Gösterge sütunlarına dönüştürebileceğiniz sütun sayısı için bir sınır yoktur. Ancak, her bir değer sütunu birden çok gösterge sütunu sağlayabildiğinden, tek seferde yalnızca birkaç sütunu dönüştürüp gözden geçirmek isteyebilirsiniz.  

-   Sütun eksik değerler içeriyorsa, eksik kategori için bu adla ayrı bir gösterge sütunu oluşturulur: *\<source column> -eksik*  

-   Gösterge değerlerine dönüştürdüğünüz sütun sayı içeriyorsa, bunların diğer özellik sütunları gibi kategorik olarak işaretlenmesi gerekir. Bunu yaptıktan sonra, sayılar ayrık değerler olarak değerlendirilir. Örneğin, yaklaşık olarak 25 ile 30 arasında değişen bir sayısal sütununuz varsa, her ayrık değer için yeni bir gösterge sütunu oluşturulur:  

    | Marka       | Highway MPG-25 | Highway MPG-26 | Highway MPG-27 | Highway MPG-28 | Highway MPG-29 | Highway MPG-30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso otomobilleri | 0               | 0               | 0               | 0               | 0               | 1               |

- Veri kümenize çok fazla boyut eklemekten kaçınmak için. İlk olarak sütundaki değer sayısını kontrol etmenizi ve verileri uygun şekilde gözden geçirin.  


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
