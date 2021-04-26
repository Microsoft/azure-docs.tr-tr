---
title: 'İçeri aktarma verileri: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Mevcut bulut veri hizmetlerinden bir makine öğrenimi ardışık düzenine veri yüklemek için Azure Machine Learning veri alma modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 69d27c102ca059974da87224e44f0ad7aa103fff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94592643"
---
# <a name="import-data-module"></a>Veri modülünü içeri aktarma

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Mevcut bulut veri hizmetlerinden bir makine öğrenimi ardışık düzenine veri yüklemek için bu modülü kullanın. 

> [!Note]
> Bu modül tarafından sunulan tüm işlevler, çalışma alanı giriş sayfasında **veri deposu** ve **veri kümeleri** tarafından yapılabilir. Veri izleme gibi ek özellikler içeren veri **deposu** ve veri **kümesi** kullanmanızı öneririz. Daha fazla bilgi edinmek için bkz. [verilere erişme ve veri](../how-to-access-data.md) [kümelerini kaydetme](../how-to-create-register-datasets.md) makalesi.
> Bir veri kümesini kaydettikten sonra, tasarımcı arabirimindeki **DataSet**  ->  **My** DataSet kategorisinden bulabilirsiniz. Bu modül, tanıdık bir deneyim için Studio (klasik) kullanıcıları için ayrılmıştır. 
>

**Veri Içeri aktarma** modülü aşağıdaki kaynaklardan gelen verileri oku destekler:

- HTTP aracılığıyla URL
- Azure bulut, [**veri depoları**](../how-to-access-data.md)üzerinden depolar)
    - Azure Blob kapsayıcısı
    - Azure Dosya Paylaşımı
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Veritabanı
    - Azure PostgreSQL    

Bulut depolama 'yı kullanmadan önce, Azure Machine Learning çalışma alanınıza önce bir veri deposu kaydetmeniz gerekir. Daha fazla bilgi için bkz. [verilere erişme](../how-to-access-data.md). 

İstediğiniz verileri tanımladıktan ve kaynağa bağlandıktan sonra, **[verileri Içeri aktarın](./import-data.md)** her bir sütunun veri türünü içerdiği değerlere göre alır ve verileri Tasarımcı işlem hattınızla yükler. **Içeri aktarma verilerinin** çıktısı, herhangi bir tasarımcı işlem hattı ile kullanılabilen bir veri kümesidir.

Kaynak verileriniz değişirse, [verileri Içeri aktar](./import-data.md)' ı yeniden çalıştırarak veri kümesini yenileyebilir ve yeni veri ekleyebilirsiniz.

> [!WARNING]
> Çalışma alanınız bir sanal ağda ise, veri mağazalarınızı tasarımcının veri görselleştirme özelliklerini kullanacak şekilde yapılandırmanız gerekir. Bir sanal ağda veri depoları ve veri kümelerinin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure sanal ağında Azure Machine Learning Studio 'Yu kullanma](../how-to-enable-studio-virtual-network.md).


## <a name="how-to-configure-import-data"></a>Içeri aktarma verilerini yapılandırma

1. **Veri alım** modülünü işlem hattınızı ekleyin. Bu modülü, tasarımcıda **veri girişi ve çıkış** kategorisinde bulabilirsiniz.

1. Sağ bölmeyi açmak için modülü seçin.

1. **Veri kaynağı**' nı seçin ve veri kaynağı türünü seçin. HTTP veya veri deposu olabilir.

    Veri deposu ' nu seçerseniz, Azure Machine Learning çalışma alanınıza zaten kayıtlı olan mevcut veri depolarını seçebilirsiniz veya yeni bir veri deposu oluşturabilirsiniz. Ardından veri deposuna aktarılacak verilerin yolunu tanımlayın. Yolu, yol seçimi iletişim kutusunu açan yolu araştır bağlantısına **tıklayarak yola kolayca gözatacaksınız** ![ .](media/module/import-data-path.png)

    > [!NOTE]
    > **Veri alma** modülü yalnızca **tablo** verileri içindir.
    > Birden çok tablolu veri dosyasını bir kez içeri aktarmak istiyorsanız, aşağıdaki koşulları gerektirir, aksi takdirde hatalar oluşur:
    > 1. Tüm veri dosyalarını klasöre dahil etmek için, yol için giriş yapmanız gerekir `folder_name/**` . 
    > 2. Tüm veri dosyaları Unicode-8 ile kodlanmalıdır.
    > 3. Tüm veri dosyaları aynı sütun numaralarına ve sütun adlarına sahip olmalıdır.
    > 4. Birden çok veri dosyasının içe aktarılması sonucunda, birden fazla dosyanın tüm satırları sırayla birleştiriliyor.

1. Dahil etmek istediğiniz sütunları filtrelemek için Önizleme şemasını seçin. Ayrıca, ayrıştırma seçeneklerinde sınırlayıcı gibi gelişmiş ayarları tanımlayabilirsiniz.

    ![İçeri aktarma-veri önizleme](media/module/import-data.png)

1. Onay kutusu, **çıktıyı yeniden oluştur**, çalışma zamanında çıktıyı yeniden oluşturmak için modülün yürütülüp yürütülmeyeceğine karar verir. 

    Bu, varsayılan olarak seçili değildir, bu da modülün aynı parametrelerle daha önce yürütülmesi durumunda sistemin, çalışma süresini azaltmak için son çalıştırışınızda çıktıyı yeniden kullanacağı anlamına gelir. 

    Seçilirse, sistem çıktıyı yeniden oluşturmak için modülü yeniden yürütür. Bu nedenle, depolama alanındaki temel alınan veriler güncelleştirilirken bu seçeneği belirleyin. Bu seçenek, en son verileri almaya yardımcı olabilir.


1. İşlem hattını gönderme.

    Veri Içeri aktarma verileri tasarımcıya yüklerken, her bir sütunun veri türünü, sayısal veya kategorik, içerdiği değerlere göre alır.

    Bir üst bilgi varsa, çıkış veri kümesinin sütunlarını adlandırmak için üst bilgi kullanılır.

    Verilerde mevcut sütun başlıkları yoksa, yeni sütun adları Sütun1, col2 biçimi kullanılarak üretilir,... , Coln *.

## <a name="results"></a>Sonuçlar

İçeri aktarma tamamlandığında, çıktı veri kümesine sağ tıklayın ve verilerin başarıyla içe aktarılmadığını görmek için **Görselleştir** ' i seçin.

İşlem hattı her çalıştırıldığında yeni bir veri kümesini içeri aktarmak yerine yeniden kullanım için kaydetmek istiyorsanız, modülün sağ panelindeki **çıktılar + Günlükler** sekmesinin altında veri **kümesi kaydet** simgesini seçin. Veri kümesi için bir ad seçin. Kaydedilen veri kümesi, kaydetme sırasında verileri korur, işlem hattının veri kümesi değişse bile, işlem hattı yeniden çalıştırıldığında veri kümesi güncellenmez. Bu, verilerin anlık görüntülerini almak için yararlı olabilir.

Verileri içeri aktardıktan sonra, modelleme ve analiz için bazı ek hazırlıklar gerekebilir:

- Sütun adlarını değiştirmek, bir sütunu farklı bir veri türü olarak işlemek veya bazı sütunların Etiketler ya da Özellikler olduğunu göstermek için [meta verileri Düzenle](./edit-metadata.md) ' yi kullanın.

- Modelde dönüştürülecek veya kullanılacak sütunların bir alt kümesini seçmek için [veri kümesindeki sütunları seç '](./select-columns-in-dataset.md) i kullanın. Dönüştürülen veya kaldırılan sütunlar, [sütun Ekle](./add-columns.md) modülü kullanılarak, özgün veri kümesine kolayca yeniden katılabilir.  

- Veri kümesini bölmek, örnekleme gerçekleştirmek veya ilk n satırı almak için [bölüm ve örnek](./partition-and-sample.md) kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
