---
title: Proje oluşturma-özel çevirici
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler özel çeviricisinde bir projenin nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5b3a6f2e903d8178554b7a076ae1277633569353
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836557"
---
# <a name="create-a-project"></a>Proje oluşturma

Proje, modeller, belgeler ve testler için bir kapsayıcıdır. Her proje, doğru dil çiftine sahip olan bu çalışma alanına yüklenen tüm belgeleri otomatik olarak içerir.

Proje oluşturmak, model oluşturmaya yönelik ilk adımdır.

## <a name="create-a-project"></a>Proje oluşturun:

1.  [Özel çevirmen](https://portal.customtranslator.azure.ai) portalında proje oluştur ' a tıklayın.

    ![Proje oluşturma](media/how-to/how-to-create-project.png)

2.  İletişim kutusunda projenizle ilgili aşağıdaki ayrıntıları girin:

    a.  Proje adı (gerekli): projenize benzersiz ve anlamlı bir ad verin. Başlık içindeki dillerin bahsetmek gerekli değildir.

    b.  Açıklama: proje hakkında kısa bir Özet. Bu açıklamanın özel çevirmen veya ortaya çıkan özel sisteminizin davranışı üzerinde hiçbir etkisi yoktur, ancak farklı projeler arasında ayrım yapmanıza yardımcı olabilir.

    c.  Dil çifti (gerekli): çeviri yaptığınız dili ve ' ı seçin.

    d.  Kategori (gerekli): projeniz için en uygun kategoriyi seçin. Kategori, çevirmek istediğiniz belgelerin terminolojisini ve stilini açıklar.

    e.  Kategori açıklaması: bu alanı, çalıştığınız belirli bir alanı veya sektöri daha iyi açıklayacak şekilde kullanın. Örneğin kategorinizin ilaç olması halinde, belirli bir belge (örneğin, bir Sury veya Pediatrics) ekleyebilirsiniz. Açıklamanın özel çevirmen veya elde edilen özel sisteminizin davranışı üzerinde hiçbir etkisi yoktur.

    f.  Proje etiketi: [Proje etiketi](workspace-and-project.md#project-labels) aynı dil çiftine ve kategoriye sahip projeler arasında ayrım yapar. En iyi uygulama olarak, *yalnızca* aynı dil çifti ve aynı kategori için birden fazla proje oluşturmayı planlıyor ve bu projelere farklı bir CategoryID ile erişmek istiyorsanız bir etiket kullanın. Yalnızca bir kategori için sistem oluşturuyorsanız bu alanı kullanmayın. Proje etiketi gerekli değildir ve dil çiftlerini ayırt etmek için yararlı değildir. Birden çok proje için aynı etiketi kullanabilirsiniz.

    ![Proje oluştur iletişim kutusu](media/how-to/how-to-create-project-dialog.png)

3.  Oluştur’a tıklayın

## <a name="view-project-details"></a>Proje ayrıntılarını görüntüle

Özel çevirici giriş sayfası, çalışma alanınızdaki ilk 10 projeyi gösterir. Proje adı, dil çifti, kategori, durum ve BLEU Puanını görüntüler.

Proje seçildikten sonra proje sayfasında şunları görürsünüz:

- CategoryID: çalışma alanı kimliği, proje etiketi ve kategori kodu bitiştirerek CategoryID oluşturulur. Özel Çeviriler almak için metin Çeviricisi API 'SI ile CategoryID 'yi kullanırsınız.

- Eğitme düğmesi: [bir modeli eğitbaşlamak](how-to-train-model.md)için bu düğmeyi kullanın.

- Belge Ekle düğmesi: [belgeleri karşıya yüklemek](how-to-upload-document.md)için bu düğmeyi kullanın.

- Belgeleri filtrele düğmesi: belirli belgeleri filtrelemek ve aramak için bu düğmeyi kullanın.

    ![Proje ayrıntılarını görüntüle](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Projeyi arama, düzenleme, silme hakkında](how-to-search-edit-delete-projects.md)bilgi edinin.
- Çeviri modellerini derlemek için [belgeyi karşıya yüklemeyi](how-to-upload-document.md) öğrenin.
