---
title: Görüntü Dizinine Dönüştürme
titleSuffix: Azure Machine Learning
description: Veri kümesini görüntü dizini biçimine dönüştürmek için görüntü dizinine dönüştürme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: 1489ce74da2ecff5212feb5a1a2e3c9151b73424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94555592"
---
# <a name="convert-to-image-directory"></a>Görüntü Dizinine Dönüştürme

Bu makalede, görüntü veri kümesini, Azure Machine Learning tasarımcısında görüntü sınıflandırması gibi görüntüyle ilgili görevlerde standartlaştırılmış veri biçimi olan görüntü *dizini* veri türüne dönüştürmeye yardımcı olması için görüntü dizin oluşturma modülünün nasıl kullanılacağı açıklanır.

## <a name="how-to-use-convert-to-image-directory"></a>Görüntü dizinine Dönüştür kullanımı  

1. Önce görüntü veri kümenizi hazırlayın. 

    Denetimli öğrenme için eğitim veri kümesinin etiketini belirtmeniz gerekir. Görüntü veri kümesi dosyası şu yapıda olmalıdır:
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    Görüntü veri kümesi klasöründe birden çok alt klasör vardır. Her alt klasör sırasıyla bir kategorinin görüntülerini içerir. Alt klasörlerin adları, görüntü sınıflandırması gibi görevler için Etiketler olarak değerlendirilir. Daha fazla bilgi için [torchvision veri kümelerine](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) bakın.

    > [!WARNING]
    > Veri etiketlemenin içinden aktarılmış olan etiketlenmiş veri kümeleri tasarımcıda desteklenmez.

    Bu uzantılara sahip görüntüler (küçük harfle) desteklenir: '. jpg ', '. jpeg ', '. png ', '. ppm ', '. bmp ', '. PGM ', '. tif ', '. tiff ', '. webp '. Ayrıca, bir klasörde birden fazla görüntü türüne sahip olabilirsiniz. Her kategori klasöründeki aynı görüntü sayısını içermesi gerekmez.

    Klasörü veya sıkıştırılmış dosyayı '. zip ', '. tar ', '. gz ' ve '. bz2 ' uzantılı bir dosya kullanabilirsiniz. **Daha iyi performans için sıkıştırılmış dosyalar önerilir.** 
    
    ![Görüntü örneği veri kümesi](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > Çıkarım için, görüntü veri kümesi klasörünün yalnızca sınıflandırılmayan görüntüleri içermesi gerekir.

1. Görüntü dizini modülüne dönüştürme girişi bir **dosya veri kümesi** olması gerektiğinden, görüntü veri kümesini çalışma alanınıza [bir dosya veri kümesi olarak kaydedin](../how-to-create-register-datasets.md) .

1. Kayıtlı görüntü veri kümesini tuvale ekleyin. Kayıtlı veri kümenizi, tuvalin solundaki modül listesindeki **veri kümeleri** kategorisinde bulabilirsiniz. Şu anda tasarımcı Image veri kümesini görselleştirmeyi desteklemiyor.

    > [!WARNING]
    > **Veri alma** modülünün çıkış türü yalnızca dosya yolu dizesini Içeren Dataframe dizini olduğundan, görüntü veri kümesini içeri aktarmak Için **veri alma** **modülünü kullanamazsınız.**

1. **Görüntü dizinine Dönüştür** modülünü tuvale ekleyin. Modül listesindeki ' Görüntü İşleme/Image Data Transformation ' kategorisinde bu modülü bulabilirsiniz. Görüntü veri kümesine bağlayın.
    
3.  İşlem hattını gönderme. Bu modül GPU ya da CPU üzerinde çalıştırılabilir.

## <a name="results"></a>Sonuçlar

**Görüntü dizini modülüne dönüştürme** çıkışı **görüntü dizini** biçimindedir ve giriş bağlantı noktası biçiminin de görüntü dizini olduğu görüntüyle ilgili diğer modüllere bağlanabilir.

![Görüntü dizini çıkışına Dönüştür](./media/module/convert-to-image-directory-output.png)

## <a name="technical-notes"></a>Teknik notlar 

###  <a name="expected-inputs"></a>Beklenen girişler  

| Ad          | Tür                  | Description   |
| ------------- | --------------------- | ------------- |
| Giriş veri kümesi | AnyDirectory, ZipFile | Giriş veri kümesi |

###  <a name="output"></a>Çıktı  

| Ad                   | Tür           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Çıkış resmi dizini | ImageDirectory | Çıkış resmi dizini |

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.