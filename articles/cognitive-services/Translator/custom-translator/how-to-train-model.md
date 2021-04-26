---
title: Model-özel bir çevirici eğitme
titleSuffix: Azure Cognitive Services
description: Model eğitimi, bir çeviri modeli oluştururken önemli bir adımdır. Eğitim, söz konusu insanlar için seçtiğiniz belgelere göre yapılır.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 5c17f842b93fb99a6a06b94f84ae26126794b776
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896435"
---
# <a name="train-a-model"></a>Modeli eğitme

Model eğitimi, bir çeviri modeli oluşturmaya yönelik ilk ve en önemli adımdır, aksi takdirde model derlenebilir. Eğitim, seyahat için seçtiğiniz belgelere göre yapılır. "Eğitim" belge türü belgeler ' i seçtiğinizde, 10.000 paralel cümle minimum gereksiniminin en az olması gerekir. Belgeler ' i seçerken size kılavuzluk eden eğitim cümlelerin toplam sayısını görüntüyoruz. Bu gereksinim, yalnızca bir modeli eğitebilmeniz için sözlük belge türünün belgelerini seçtiğinizde uygulanmaz.

Bir modeli eğitme:

1. Bir model oluşturmak istediğiniz projeyi seçin.

2. Proje için veri sekmesi, proje dili çiftinin tüm ilgili belgelerini gösterir. Modelinize eğitebilmeniz için kullanmak istediğiniz belgeleri el ile seçin. Bu ekrandan eğitim, ayarlama ve test belgelerini seçebilirsiniz. Ayrıca, yalnızca eğitim kümesini seçersiniz ve özel çevirmenleriniz sizin için ayarlama ve test kümelerini oluşturur.

    - Belge adı: belgenin adı.

    - Eşleştirme: Bu belge bir Parallel veya monolingual belgesidir. Monolingual belgeler şu anda eğitim için desteklenmiyor.

    - Belge türü: eğitim, ayarlama, test veya sözlük olabilir.

    - Dil çifti: Bu, projenin kaynak ve hedef dilini gösterir.

    - Kaynak cümleler: kaynak dosyadan ayıklanan Tümcelerin sayısını gösterir.

    - Hedef cümleler: hedef dosyadan ayıklanan Tümcelerin sayısını gösterir.

    ![Modeli eğitme](media/how-to/how-to-train-model.png)

3. "Model oluştur" düğmesine tıklayın.

4. İletişim kutusunda modelinize ilişkin adı belirtin. "Model oluştur" düğmesine tıkladığınızda eğitim ardışık düzenine başlamak için varsayılan olarak "hemen eğitme" seçilidir. Model meta verilerini oluşturmak ve modeli taslak durumuna koymak için "taslak olarak Kaydet" seçeneğini belirleyebilirsiniz, ancak model eğitimi başlatılmaz. Daha sonra, eğitmek için taslak durumundaki modelleri el ile seçmeniz gerekir.

5. "Model oluştur" düğmesine tıklayın.

    ![Model eğitimi iletişim kutusu](media/how-to/how-to-train-model-2.png)

6. Özel çevirici, eğitimi gönderir ve modeller sekmesinde eğitimin durumunu gösterir.

    ![Modeli eğitme sayfası](media/how-to/how-to-train-model-3.png)

>[!Note]
>Özel çevirmen, zaman içinde herhangi bir zamanda bir çalışma alanı içinde 10 eş zamanlı izleme destekler.

## <a name="modify-a-model-name"></a>Model adını değiştirme

Model ayrıntıları sayfasından bir model adı değiştirebilirsiniz.

1. Projeler sayfasında, modelin bulunduğu proje adına tıklayın.
2. Model sekmesine tıklayın.
3. Model ayrıntılarını görüntülemek için model adına tıklayın.
4. Kalem simgesine tıklayın.

    ![Modeli düzenle](media/how-to/how-to-edit-model.png)

5. İletişim kutusunda model adını değiştirin ve modelinize anlamlı bir ad verin.

    ![Daha fazla Düzenle iletişim kutusu](media/how-to/how-to-edit-model-dialog.png)

6. Kaydet’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Model ayrıntılarının nasıl görüntüleneceğini](how-to-view-model-details.md)öğrenin.
