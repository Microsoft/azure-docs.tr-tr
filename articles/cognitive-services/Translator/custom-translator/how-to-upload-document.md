---
title: Belgeyi karşıya yükleme-özel çevirici
titleSuffix: Azure Cognitive Services
description: Belgeyi karşıya yükleme özelliği paralel belgeleri (biri kaynağın kaynağı ve diğeri ise çeviri olduğu iki belge) hizmete yükler.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 83cce31d3eee9b5cca7b54e996e41a270fec0030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896403"
---
# <a name="upload-a-document"></a>Bir belgeyi karşıya yükleme

[Özel çevirmende](https://portal.customtranslator.azure.ai), çeviri modellerinizi eğmek için paralel belgeleri karşıya yükleyebilirsiniz. [Paralel belgeler](what-are-parallel-documents.md) , birinin diğerinin çevirisi olduğu belge çiftleridir. Çiftteki bir belge kaynak dilde cümleler içeriyor ve diğer belge, hedef dile çevrilmiş olan bu cümleleri içeriyor.

Belgelerinizi karşıya yüklemeden önce [belge biçimlerini ve adlandırma kuralı kılavuzunu](document-formats-naming-convention.md) gözden geçirerek dosya biçiminizi özel çevirmende desteklendiğinden emin olun.

## <a name="how-to-upload-document"></a>Belgeyi karşıya yükleme

Belgeler sayfasına gitmek için [özel çevirmen](https://portal.customtranslator.azure.ai) portalından "belgeler" sekmesine tıklayın.

![Belgeyi karşıya yükleme bağlantısı](media/how-to/how-to-upload-1.png)


1.  Belgeler sayfasındaki dosyaları karşıya yükle düğmesine tıklayın.

    ![Belge sayfasını karşıya yükle](media/how-to/how-to-upload-2.png)

2.  İletişim kutusunda aşağıdaki bilgileri girin:

    a.  Belge türü:

    -  Eğitim: Bu belgeler, eğitim kümesi için kullanılacaktır.
    -  Ayarlama: Bu belgeler, ayarlama kümesi için kullanılacaktır.
    -  Test: Bu belgeler, test kümesi için kullanılacaktır.
    -  Tümcecik sözlüğü: Bu belgeler, tümcecik sözlüğü için kullanılacaktır.
    -  Tümce sözlüğü: Bu belgeler, tümce sözlüğü için kullanılacak

    b.  Dil çifti

    c.  Varsa belgeyi geçersiz kıl: aynı ada sahip mevcut belgelerin üzerine yazmak istiyorsanız bu onay kutusunu Işaretleyin.

    d.  Paralel veriler ya da Birleşik veri verileri için ilgili bölümü girin.

    -  Paralel veriler:
        -  Kaynak dosya: yerel bilgisayarınızdan kaynak dil dosyasını seçin.
        -  Hedef dosya: yerel bilgisayarınızdan hedef dil dosyasını seçin.
        -  Belge adı: yalnızca paralel dosyaları karşıya yüklüyorsanız kullanılır.

    - Birleşik veri verileri:
        -  Birleşik giriş dosyası: yerel bilgisayarınızdan Birleşik dosya dosyasını seçin. Birleşik dosyanızın her ikisi de kaynak ve hedef dil cümlelerinizi içerir. [Adlandırma kuralı](document-formats-naming-convention.md) , Birleşik dosyalar için önemlidir.

    e.  Karşıya yükle 'ye tıklayın

    ![Belgeyi karşıya yükle iletişim kutusu](media/how-to/how-to-upload-dialog.png)

3.  Bu noktada belgelerinizi işliyoruz ve cümleleri çıkarmaya çalışıyorsunuz. Belgelerinin işlem sırasında durumunu denetlemek için "karşıya yükleme Ilerlemesini görüntüle" ye tıklayabilirsiniz.

    ![Belge işleme iletişim kutusunu karşıya yükle](media/how-to/how-to-upload-processing-dialog.png)

4.  Bu sayfada durum ve karşıya yüklemenizin içindeki her dosya için hata görüntülenir. "Geçmişi karşıya yükle" sekmesine tıklayarak istediğiniz zaman geçmiş karşıya yükleme durumunu görüntüleyebilirsiniz.

    ![Belge geçmişini karşıya yükle iletişim kutusu](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Karşıya yükleme geçmişini görüntüle

Karşıya yükleme geçmişi sayfasında belge türü, dil çifti, karşıya yükleme durumu gibi tüm belge karşıya yükleme ayrıntılarının geçmişini görüntüleyebilirsiniz.

1. [Özel çevirmen](https://portal.customtranslator.azure.ai) portalından geçmişi görüntülemek Için geçmişi karşıya yükle ' ye tıklayın.

    ![Karşıya yükleme geçmişi sekmesi](media/how-to/how-to-upload-history-1.png)

2. Bu sayfa, son karşıya yüklemelerinizin tümünün durumunu gösterir. En güncel olan karşıya yüklemeleri en son en az son olarak görüntüler. Her karşıya yükleme için belge adı, karşıya yükleme durumu, karşıya yükleme tarihi, karşıya yüklenen dosya sayısı, karşıya yüklenen dosya türü ve dosyanın dil çifti gösterilir.

    ![Karşıya yükleme geçmişi sayfası](media/how-to/how-to-document-history-2.png)

3. Karşıya yükleme geçmişi kaydına tıklayın. Karşıya yükleme geçmişi ayrıntıları sayfasında karşıya yükleme işleminin bir parçası olarak karşıya yüklenen dosyaları, dosyanın karşıya yüklenme durumunu, dosyanın dilini ve hata iletisini (karşıya yükleme sırasında herhangi bir hata varsa) görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Ayıklanan Tümcelerin listesini gözden geçirmek için [Belge ayrıntıları sayfasını](how-to-view-document-details.md) kullanın.
- [Bir modeli eğitme](how-to-train-model.md).
