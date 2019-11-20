---
title: 'Verileri el Ile girin: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Değer yazarak küçük bir veri kümesi oluşturmak için Azure Machine Learning verileri el Ile gir modülünü nasıl kullanacağınızı öğrenin. Veri kümesinde birden çok sütun olabilir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 7c4d62c19984e1316a01213f579c059b97f709c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497940"
---
# <a name="enter-data-manually-module"></a>Verileri el Ile gir modülü

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Değer yazarak küçük bir veri kümesi oluşturmak için bu modülü kullanın. Veri kümesinde birden çok sütun olabilir.
  
Bu modül bunlar gibi senaryolarda yararlı olabilir:  
  
- Test için küçük bir değer kümesi oluşturma  
  
- Etiketlerin kısa bir listesini oluşturma
  
- Bir veri kümesine eklenecek sütun adlarının listesini yazma

## <a name="enter-data-manually"></a>Verileri El ile Girme 
  
1.  [Verileri el Ile gir](./enter-data-manually.md) modülünü ardışık düzene ekleyin. Bu modülü, Azure Machine Learning **veri girişi ve çıkış** kategorisinde bulabilirsiniz. 
  
2.  **DataFormat**için aşağıdaki seçeneklerden birini seçin. Bu seçenekler, sağladığınız verilerin nasıl ayrıştırılaceğini belirlemektir. Her biçimin gereksinimleri önemli ölçüde farklılık gösterir, bu nedenle ilgili konuları okuduğunuzdan emin olun.  
  
    -   **Arff**. WEKA tarafından kullanılan öznitelik ilişkisi dosya biçimi.   
  
    -   **CSV**. Virgülle ayrılmış değerler biçimi. Daha fazla bilgi için bkz. [CSV 'ye dönüştürme](./convert-to-csv.md).  
  
    -   **Svmlight**. Vowpal Wabbit ve diğer makine öğrenimi çerçeveleri tarafından kullanılan bir biçim.  
  
    -   **TSV**. Sekmeyle ayrılmış değerler biçimi.

     Bir biçim seçer ve biçim belirtimlerini karşılayan veriler sağlamazsanız, bir çalışma zamanı hatası oluşur.
  
3.  Veri girmeye başlamak için **veri** metin kutusunun içine tıklayın. Aşağıdaki biçimler özel dikkat gerektirir:  
  
    - **CSV**: birden çok sütun oluşturmak için, virgülle ayrılmış metinde yapıştırın veya alanlar arasında virgül kullanarak birden çok sütun yazın.
  
        **HasHeader** seçeneğini belirlerseniz, ilk değer satırını sütun başlığı olarak kullanabilirsiniz.  
  
        Bu seçeneğin işaretini kaldırırsanız, sütünadı, col2 vb. sütunları kullanılır. Daha sonra, [verileri Düzenle](./edit-metadata.md)' i kullanarak sütun adlarını ekleyebilir veya değiştirebilirsiniz.  
  
    - **TSV**: birden çok sütun oluşturmak için sekmeyle ayrılmış metin içine yapıştırın veya alanlar arasındaki sekmeleri kullanarak birden çok sütun yazın.  
  
        **HasHeader** seçeneğini belirlerseniz, ilk değer satırını sütun başlığı olarak kullanabilirsiniz.  
  
        Bu seçeneğin işaretini kaldırırsanız, sütünadı, col2 vb. sütunları kullanılır. Daha sonra, [verileri Düzenle](./edit-metadata.md)' i kullanarak sütun adlarını ekleyebilir veya değiştirebilirsiniz.  
  
    -   **Arff**: varolan bir arff biçim dosyasını yapıştırın. Değerleri doğrudan yazıyorsanız, verilerin başlangıcında isteğe bağlı üstbilgiyi ve gerekli öznitelik alanlarını eklediğinizden emin olun. 
    
        Örneğin, aşağıdaki üst bilgi ve öznitelik satırları basit bir listeye eklenebilir. Sütun başlığı `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **Svmlight**: svmlight biçimini kullanarak değerleri yazın veya yapıştırın.  
  
        Örneğin, aşağıdaki örnek, kanlı olarak şu biçimde olan kan bağış veri kümesinin ilk birkaç satırını temsil eder:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        [Verileri el Ile gir](./enter-data-manually.md) modülünü çalıştırdığınızda, bu satırlar sütun ve dizin değerlerinin veri kümesine aşağıdaki şekilde dönüştürülür:  
  
        |Süt|Col2|Col3|Col4|Etiketler|  
        |-|-|-|-|-|  
        |0,00016|0,004|0,999961|0,00784|1|  
        |0|0,004|0,999955|0,008615|1|  
  
4.  Her satırdan sonra ENTER tuşuna basarak yeni bir satır başlatın.  
  
     **Son satırdan sonra ENTER tuşuna bastığınızdan emin olun.** 
     
     Birden çok kez daha fazla boş satır eklemek için ENTER tuşuna basarsanız, son boş satır atılır, ancak diğer boş satırlar eksik değerler olarak değerlendirilir.  
  
     Eksik değerleri olan satırlar oluşturursanız, bunları her zaman daha sonra filtreleyebilirsiniz.  
  
5.  Modüle sağ tıklayın ve verileri ayrıştırmak ve veri kümesi olarak çalışma alanınıza yüklemek için **Seçileni Çalıştır** ' ı seçin.  
  
     Veri kümesini görüntülemek için çıkış bağlantı noktasına tıklayın ve **Görselleştir**' i seçin.  
## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 