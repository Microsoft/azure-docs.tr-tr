---
title: Azure Data Factory veri akışlarını eşleme ile sabit uzunluklu metin dosyalarını işle
description: Veri akışlarını eşleme kullanarak Azure Data Factory sabit uzunluklu metin dosyalarını nasıl işleyeceğini öğrenin.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d6885e9b30cc71bda822a29574c4d574f2b020a0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387041"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Data Factory eşleme veri akışlarını kullanarak sabit uzunluklu metin dosyalarını işleyin

Microsoft Azure Data Factory veri akışlarını eşleme kullanarak, sabit genişlikli metin dosyalarından verileri dönüştürebilirsiniz. Aşağıdaki görevde, bir metin dosyası için sınırlayıcı olmadan bir veri kümesi tanımlayacağız ve sonra sıra konumuna göre alt dize bölmelerini ayarlayacağız.

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

1. Yeni bir işlem hattı oluşturmak için **+ Yeni Işlem hattı** ' nı seçin.

2. Sabit genişlikli dosyaları işlemek için kullanılacak bir veri akışı etkinliği ekleyin:

    ![Sabit genişlikli işlem hattı](media/data-flow/fwpipe.png)

3. Veri akışı etkinliğinde **Yeni eşleme veri akışı**' nı seçin.

4. Kaynak, türetilmiş sütun, seçme ve havuz dönüşümü ekleyin:

    ![Sabit genişlikli veri akışı](media/data-flow/fw2.png)

5. Kaynak dönüşümünü, ayrılmış metin türünde olacak şekilde yeni bir veri kümesi kullanacak şekilde yapılandırın.

6. Herhangi bir sütun sınırlayıcısı veya üst bilgi ayarlama.

   Artık bu dosyanın içeriği için alan başlangıç noktaları ve uzunlukları ayarlayacağız:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Kaynak dönüşümünüzün **İzdüşüm** sekmesinde, *Column_1*adlı bir dize sütunu görmeniz gerekir.

8. Türetilmiş sütununda yeni bir sütun oluşturun.

9. Sütunları *Sütun1*gibi basit adlara vereceğiz.

10. İfade Oluşturucusu ' nda, aşağıdakileri yazın:

    ```substring(Column_1,1,4)```

    ![türetilmiş sütun](media/data-flow/fwderivedcol1.png)

11. Ayrıştırmak için gereken tüm sütunlar için 10. adımı tekrarlayın.

12. Oluşturulacak yeni sütunları görmek için **İnceleme** sekmesini seçin:

    ![Bilgiyi](media/data-flow/fwinspect.png)

13. Dönüştürme için gerekli olmayan sütunları kaldırmak için Seç dönüşümünü kullanın:

    ![dönüşüm seçin](media/data-flow/fwselect.png)

14. Verileri bir klasöre çıkarmak için havuz kullanın:

    ![Sabit genişlikli havuz](media/data-flow/fwsink.png)

    Çıktının şöyle göründüğü aşağıda verilmiştir:

    ![Sabit genişlikli çıkış](media/data-flow/fxdoutput.png)

  Sabit genişlikli veriler artık, dört karakterle ve Sütun1, Col2, Col3, col4, vb. ' e atanmış olarak bölünür. Önceki örneğe göre, veriler dört sütuna bölünür.

## <a name="next-steps"></a>Sonraki adımlar

* Veri akışı [dönüştürmelerini](concepts-data-flow-overview.md)eşleme kullanarak veri akışı mantığınızın geri kalanını oluşturun.
