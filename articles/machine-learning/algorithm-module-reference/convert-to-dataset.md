---
title: "DataSet 'e Dönüştür: modül başvurusu"
titleSuffix: Azure Machine Learning service
description: Veri girişini Microsoft Azure Machine Learning tarafından kullanılan iç veri kümesi biçimine dönüştürmek için Azure Machine Learning hizmetindeki veri kümesine dönüştürme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 81eb71da43623f6478e267f55d6576789d494f9b
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717262"
---
# <a name="convert-to-dataset"></a>Veri kümesine Dönüştür

Bu makalede, bir işlem hattının herhangi bir verisini tasarımcı iç biçimine dönüştürmek için Azure Machine Learning tasarımcısında (Önizleme) veri kümesi dönüştürme modülünün nasıl kullanılacağı açıklanır.
  
Çoğu durumda dönüştürme gerekli değildir. Azure Machine Learning veriler üzerinde herhangi bir işlem yapıldığında, verileri örtülü olarak yerel veri kümesi biçimine dönüştürür. 

Bir veri kümesinde bir dizi normalleştirme veya temizleme işlemi gerçekleştirdiyseniz ve değişikliklerin başka işlem hatlarında kullanılmasını sağlamak istiyorsanız veri kümesi biçimine veri kaydetmenizi öneririz.  
  
> [!NOTE]
> Veri kümesine dönüştürme yalnızca verilerin biçimini değiştirir. Çalışma alanındaki verilerin yeni bir kopyasını kaydetmez. Veri kümesini kaydetmek için, çıkış bağlantı noktasına çift tıklayın, **veri kümesi olarak kaydet**' i seçin ve yeni bir ad girin.  
  
## <a name="how-to-use-convert-to-dataset"></a>Veri kümesine dönüştürme kullanma  

Veri kümesine Dönüştür kullanmadan önce veri kümesini hazırlamak için [meta verileri Düzenle](edit-metadata.md) modülünü kullanmanızı öneririz. Sütun adlarını ekleyebilir veya değiştirebilir, veri türlerini ayarlayabilir ve gerektiğinde başka değişiklikler yapabilirsiniz.

1.  Veri kümesine dönüştürme modülünü ardışık düzene ekleyin. Bu modülü, tasarımcıda **veri dönüştürme** kategorisinde bulabilirsiniz. 

2. Veri kümesini çıkaran herhangi bir modüle bağlayın.   

    Veriler [tablosal](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)olduğu sürece, veri kümesine dönüştürebilirsiniz. Bu, verileri [Içeri aktarma](import-data.md)yoluyla yüklenen verileri, [verileri el ile girerek](enter-data-manually.md)oluşturulan verileri veya [Dönüşüm uygulama](apply-transformation.md)aracılığıyla dönüştürülmüş veri kümelerini içerir.

3.  **Eylem** açılan listesinde, veri kümesini kaydetmeden önce verilerde temizlik yapmak istediğinizi belirtin:  
  
    - **Hiçbiri**: verileri olduğu gibi kullanın.  
  
    - **Setmissingvalue**: veri kümesindeki eksik bir değere belirli bir değer ayarlayın. Varsayılan yer tutucu, soru işareti karakteridir (?), ancak farklı bir değer girmek için **özel eksik değer** seçeneğini kullanabilirsiniz. Örneğin, **özel eksik değer**Için **TAXI** girerseniz, veri kümesindeki tüm **TAXI** örnekleri eksik değere değiştirilir.
  
    - **Replacevalues**: başka bir tam değer ile değiştirilmeleri için tek bir tam değer belirtmek için bu seçeneği kullanın. Eksik değerleri veya özel değerleri **değiştirme** yöntemini ayarlayarak değiştirebilirsiniz:

      - **Eksik**: giriş veri kümesindeki eksik değerleri değiştirmek için bu seçeneği belirleyin. **Yeni değer**için eksik değerleri ile değiştirecek değeri girin.
      - **Özel**: giriş veri kümesindeki özel değerleri değiştirmek için bu seçeneği belirleyin. **Özel değer**için bulmak istediğiniz değeri girin. Örneğin, verileriniz eksik değerler için yer tutucu olarak kullanılan `obs` dize içeriyorsa, `obs`girersiniz. **Yeni değer**için, özgün dizeyi ile değiştirilecek yeni değeri girin.
  
    **Replacevalues** işleminin yalnızca tam eşleşmeler için geçerli olduğunu unutmayın. Örneğin, bu dizeler etkilenmemelidir: `obs.`, `obsolete`.  
 
  
5.  İşlem hattını çalıştırın veya veri kümesine Dönüştür modülüne sağ tıklayıp **Seçileni Çalıştır**' ı seçin.  

## <a name="results"></a>Sonuçlar

+  Elde edilen veri kümesini yeni bir adla kaydetmek için, veri kümesine Dönüştür çıktısına sağ tıklayın ve **veri kümesi olarak kaydet**' i seçin.  
  
## <a name="technical-notes"></a>Teknik notlar  

-   Giriş olarak bir veri kümesi alan herhangi bir modül, CSV dosyasında veya TSV dosyasında da veri alabilir. Herhangi bir modül kodu çalıştırılmadan önce, girişler önceden işlenir. Ön işleme, girişte veri kümesine Dönüştür modülüne çalıştırmaya eşdeğerdir.  
  
-   SVMLight biçiminden bir veri kümesine dönüştüremezsiniz.  
  
-   Özel bir değiştirme işlemi belirttiğinizde, arama ve değiştirme işlemi tam değerlere uygulanır. Kısmi eşleştirmelere izin verilmiyor. Örneğin, 3 ' ü bir-1 veya 33 ile değiştirebilirsiniz, ancak 3 35 gibi iki basamaklı bir sayı içinde 3 ' ü değiştiremezsiniz.  
  
-   Özel değiştirme işlemleri için, sütunun geçerli veri türüyle uyumlu olmayan herhangi bir karakter değiştirme olarak kullanırsanız değiştirme işlemi sessizce başarısız olur.  

  
## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 
