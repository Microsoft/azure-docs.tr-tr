---
title: Model-özel bir çevirici eğitme
titleSuffix: Azure Cognitive Services
description: Model eğitimi, bir çeviri modeli oluştururken önemli bir adımdır. Eğitim, söz konusu insanlar için seçtiğiniz belgelere göre yapılır.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595690"
---
# <a name="train-a-model"></a>Modeli eğitme

Model eğitimi, bir eğitim olmadan bir çeviri modeli oluşturmak için önemli bir adımdır. Eğitim, seyahat için seçtiğiniz belgelere göre yapılır.

Bir modeli eğitme:

1.  Bir model oluşturmak istediğiniz projeyi seçin.

2.  Proje için veri sekmesi, proje dili çiftinin tüm ilgili belgelerini gösterir. Modelinize eğitebilmeniz için kullanmak istediğiniz belgeleri el ile seçin. Bu ekrandan eğitim, ayarlama ve test belgelerini seçebilirsiniz. Ayrıca, yalnızca eğitim kümesini seçersiniz ve özel çevirmenleriniz sizin için ayarlama ve test kümelerini oluşturur.

    -  Belge adı: Belgenin adı.

    -  Eşleştirilmiş Bu belge bir Parallel veya monolingual belgesidir. Monolingual belgeler şu anda eğitim için desteklenmiyor.

    -  Belge türü: Eğitim, ayarlama, test veya sözlük olabilir.

    -  Dil çifti: Bu, projenin kaynak ve hedef dilini gösterir.

    -  Kaynak cümleler: Kaynak dosyadan ayıklanan Tümcelerin sayısını gösterir.

    -  Hedef cümleler: Hedef dosyadan ayıklanan Tümcelerin sayısını gösterir.

    ![Modeli eğitme](media/how-to/how-to-train-model.png)

3.  Eğitme düğmesine tıklayın.

4.  İletişim kutusunda modelinize bir ad belirtin.

5.  Modeli eğitme ' ye tıklayın.

    ![Model eğitimi iletişim kutusu](media/how-to/how-to-train-model-2.png)

6.  Özel çevirici, eğitimi gönderir ve modeller sekmesinde eğitimin durumunu gösterir.

    ![Modeli eğitme sayfası](media/how-to/how-to-train-model-3.png)

>[!Note]
>Özel çevirmen, zaman içinde herhangi bir zamanda bir çalışma alanı içinde 10 eş zamanlı izleme destekler.


## <a name="edit-a-model"></a>Model düzenleme

Model Ayrıntıları sayfasındaki Düzenle bağlantısını kullanarak bir modeli düzenleyebilirsiniz.

1.  Kalem simgesine tıklayın.

    ![Modeli Düzenle](media/how-to/how-to-edit-model.png)

2.  İletişim kutusu değiştiğinde,

    1.  Model adı (gerekli): Modelinize anlamlı bir ad verin.

        ![Daha fazla Düzenle iletişim kutusu](media/how-to/how-to-edit-model-dialog.png)

3.  Kaydet’e tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

- [Model ayrıntılarının nasıl görüntüleneceğini](how-to-view-model-details.md)öğrenin.
