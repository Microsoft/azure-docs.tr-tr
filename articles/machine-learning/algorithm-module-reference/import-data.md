---
title: 'İçeri aktarma verileri: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Mevcut bulut veri hizmetlerinden bir makine öğrenimi ardışık düzenine veri yüklemek için Azure Machine Learning veri alma modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 5fa8d3984c758d0bf95372864f3bffeb6f302c83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497805"
---
# <a name="import-data-module"></a>Veri modülünü içeri aktarma

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Mevcut bulut veri hizmetlerinden bir makine öğrenimi ardışık düzenine veri yüklemek için bu modülü kullanın. 

> [!Note]
> Bu modül tarafından sunulan tüm işlevler, çalışma alanı giriş sayfasında **veri deposu** ve **veri kümeleri** tarafından yapılabilir. Veri izleme gibi ek özellikler içeren veri **deposu** ve veri **kümesi** kullanmanızı öneririz. Daha fazla bilgi edinmek için bkz. [verilere erişme ve veri](../service/how-to-access-data.md) [kümelerini kaydetme](../service/how-to-create-register-datasets.md) makalesi.
> Bir veri kümesini kaydettikten sonra, tasarımcı arabirimindeki veri **kümeleri kategorim** -> **veri** kümelerinde bulabilirsiniz. Bu modül, tanıdık bir deneyim için Studio (klasik) kullanıcıları için ayrılmıştır. 
>

İlk olarak, okuduğunuz kaynağı seçin ve ek ayarları sona erdirin. **Veri Içeri aktarma** modülü aşağıdaki kaynaklardan gelen verileri oku destekler:

- HTTP aracılığıyla URL
- Azure bulut, [**veri depoları**](../service/how-to-access-data.md)üzerinden depolar)
    - Azure Blob kapsayıcısı
    - Azure Dosya Paylaşımı
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Veritabanı
    - Azure PostgreSQL    

Bulut depolama 'yı kullanmadan önce, Azure Machine Learning çalışma alanınıza önce bir veri deposu kaydetmeniz gerekir. Daha fazla bilgi için bkz. [verilere erişme](../service/how-to-access-data.md). 

İstediğiniz verileri tanımladıktan ve kaynağa bağlandıktan sonra, **[verileri Içeri aktarın](./import-data.md)** her bir sütunun veri türünü içerdiği değerlere göre alır ve verileri Tasarımcı işlem hattınızla yükler. **Içeri aktarma verilerinin** çıktısı, herhangi bir tasarımcı işlem hattı ile kullanılabilen bir veri kümesidir.

Kaynak verileriniz değişirse, [verileri Içeri aktar](./import-data.md)' ı yeniden çalıştırarak veri kümesini yenileyebilir ve yeni veri ekleyebilirsiniz. Ancak, işlem hattını her çalıştırdığınızda kaynaktan yeniden okumak istemiyorsanız, **önbelleğe alınmış sonuçları kullan** seçeneğini true olarak ayarlayın. Bu seçenek belirlendiğinde modül, işlem hattının, aynı kaynak ve aynı giriş seçeneklerini kullanarak daha önce çalıştırılıp çalıştırılmadığını denetler. Önceki bir çalıştırma bulunursa, verileri kaynaktan yeniden yükleme yerine önbellekteki veriler kullanılır.

## <a name="how-to-configure-import-data"></a>Içeri aktarma verilerini yapılandırma

1. **Veri alım** modülünü işlem hattınızı ekleyin. Bu modülü, tasarımcıda **veri girişi ve çıkış** kategorisinde bulabilirsiniz.

1. Sihirbaz kullanarak veri kaynağını yapılandırmak için **veri alma Sihirbazı 'Nı Başlat** ' a tıklayın.

    Sihirbaz hesap adını ve kimlik bilgilerini alır ve diğer seçenekleri yapılandırmanıza yardımcı olur. Var olan bir yapılandırmayı düzenliyorsanız, önce geçerli değerleri yükler.

1. **Veri kaynağı**' nı seçin ve veri kaynağı türünü seçin. HTTP veya veri deposu olabilir.

    Veri deposu ' nu seçerseniz, Azure Machine Learning çalışma alanınıza zaten kayıtlı olan mevcut veri depolarını seçebilirsiniz veya yeni bir veri deposu oluşturabilirsiniz. Ardından veri deposuna aktarılacak verilerin yolunu tanımlayın. **Yolu ![içeri** aktarma-veri yolu](media/module/import-data-path.png) ' ne tıklayarak yola kolayca gözatamazsınız

1. Dahil etmek istediğiniz sütunları filtrelemek için Önizleme şemasını seçin. Ayrıca, ayrıştırma seçeneklerinde sınırlayıcı gibi gelişmiş ayarları tanımlayabilirsiniz.

    ![İçeri aktarma-veri önizleme](media/module/import-data.png)

1. Veri kümesini art arda çalışma sırasında yeniden kullanmak üzere önbelleğe almak istiyorsanız **önbelleğe alınmış sonuçları kullan** seçeneğini belirleyin.

    Modül parametrelerinde başka değişiklik olmadığı varsayılarak, işlem hattı yalnızca modülün ilk kez çalıştırıldığı sırada verileri yükler ve bundan sonra veri kümesinin önbelleğe alınmış bir sürümünü kullanır.

    İşlem hattını her çalıştırdığınızda verileri yeniden yüklemeniz gerekiyorsa bu seçeneğin işaretini kaldırın.

1. İşlem hattını çalıştırma.

    Veri Içeri aktarma verileri tasarımcıya yüklerken, her bir sütunun veri türünü, sayısal veya kategorik, içerdiği değerlere göre alır.

    Bir üst bilgi varsa, çıkış veri kümesinin sütunlarını adlandırmak için üst bilgi kullanılır.

    Verilerde mevcut sütun başlıkları yoksa, yeni sütun adları Sütun1, col2 biçimi kullanılarak üretilir,... , Coln *.

## <a name="results"></a>Sonuçlar

İçeri aktarma tamamlandığında, çıktı veri kümesine tıklayın ve verilerin başarıyla içeri aktarılmadığını görmek için **Görselleştir** ' i seçin.

İşlem hattı her çalıştırıldığında yeni bir veri kümesi almak yerine yeniden kullanım için verileri kaydetmek istiyorsanız, çıkışa sağ tıklayın ve veri **kümesi olarak kaydet**' i seçin. Veri kümesi için bir ad seçin. Kaydedilen veri kümesi verileri kaydetme sırasında korur ve işlem hattında veri kümesi değişse bile, ardışık düzen yeniden çalıştırıldığında veriler güncellenmez. Bu, verilerin anlık görüntülerini almak için yararlı olabilir.

Verileri içeri aktardıktan sonra, modelleme ve analiz için bazı ek hazırlıklar gerekebilir:

- Sütun adlarını değiştirmek, bir sütunu farklı bir veri türü olarak işlemek veya bazı sütunların Etiketler ya da Özellikler olduğunu göstermek için [meta verileri Düzenle](./edit-metadata.md) ' yi kullanın.

- Modelde dönüştürülecek veya kullanılacak sütunların bir alt kümesini seçmek için [veri kümesindeki sütunları seç '](./select-columns-in-dataset.md) i kullanın. Dönüştürülen veya kaldırılan sütunlar, [sütun Ekle](./add-columns.md) modülü kullanılarak, özgün veri kümesine kolayca yeniden katılabilir.  

- Veri kümesini bölmek, örnekleme gerçekleştirmek veya ilk n satırı almak için [bölüm ve örnek](./partition-and-sample.md) kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
