---
title: Sınıflandırma, Sözlük terimleri ve değişiklik kişilerini etiketlemek için varlıkları toplu olarak düzenleme
description: Azure purview 'da varlıkları Toplu Düzenle hakkında bilgi edinin.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 4dc1af590a1965c155a7af7b233b431f982a73d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024243"
---
# <a name="how-to-bulk-edit-assets-to-annotate-classifications-glossary-terms-and-modify-contacts"></a>Varlıkları not ekleme sınıflandırmaları, Sözlük terimleri ve değişiklik kişileri için toplu düzenleme

Bu makalede birden çok sözlük terimi, sınıflandırmalar, sahipler ve uzmanların tek bir eylemde seçili varlıkların bir listesine nasıl etiketleneceğini açıklanmaktadır.

### <a name="add-assets-to-view-selected-list-using-search"></a>Arama kullanarak seçilen listeyi görüntülemek için varlıklar ekleme

1. Toplu düzenlemeyle ilgili listeye eklemek istediğiniz veri varlığını arayın.

2. Arama sonucu sayfasında, bir onay kutusunu görmek için toplu düzenleme **görünümü seçili** listesine eklemek istediğiniz varlığın üzerine gelin.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Onay kutusunun ekran görüntüsü.":::

3. Toplu düzenleme **görünümü seçili** listesine eklemek için onay kutusunu seçin. Eklendikten sonra, sayfanın alt kısmında seçili öğeler simgesini görürsünüz.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Listenin ekran görüntüsü.":::

4. Tüm veri varlıklarını listeye eklemek için yukarıdaki adımları tekrarlayın.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Varlık ayrıntısı sayfasından seçili listeyi görüntülemek için varlık ekleyin

1. Varlık ayrıntıları sayfasında, varlığı toplu düzenleme **görünümü seçili** listesine eklemek için sağ üst köşedeki onay kutusunu seçin.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Varlığın ekran görüntüsü.":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Sözlük terimleri eklemek, değiştirmek veya kaldırmak için seçili listedeki varlıkları Toplu Düzenle.

1. Toplu olarak düzenlenilmesi gereken tüm veri varlıklarının kimliği ile işiniz bittiğinde, arama sonuçları sayfasından veya varlık ayrıntıları sayfasından **Seçili listeyi görüntüle** ' yi seçin.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Görünümün ekran görüntüsü.":::

2. Tüm koşulları kaldırmak istiyorsanız listeyi gözden geçirin ve **Kaldır** ' ı seçin.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Kaldır 'ın ekran görüntüsü.":::

3. Seçili tüm varlıkların sözlük koşullarını eklemek, kaldırmak veya değiştirmek için toplu düzenleme ' yi seçin.

4. Sözlük terimleri eklemek için, **Ekle**' yi seçin. Yeni değerde eklemek istediğiniz tüm sözlük koşullarını seçin. Tamamlandığında Uygula ' yı seçin.
    - Ekleme işlemi, veri varlıklarına zaten etiketlenmiş olan Sözlük terimleri listesine yeni değer ekler.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Add öğesinin ekran görüntüsü.":::

5. Sözlük koşullarını değiştirmek için **, Ile değiştirin**' i seçin. Yeni değerde değiştirmek istediğiniz tüm sözlük koşullarını seçin. Tamamlandığında Uygula ' yı seçin.
    - Değiştirme işlemi, seçili veri varlıklarının tüm sözlük koşullarını yeni değerde seçilen koşullara göre değiştirecek.
   
6. Sözlük koşullarını kaldırmak için Işlemi **Kaldır**' ı seçin. Tamamlandığında Uygula ' yı seçin.
    - Kaldırma işlemi seçili veri varlıklarının tüm sözlük koşullarını kaldırır.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Kaldırma koşullarının ekran görüntüsü.":::

7. Sınıflandırmalar, sahipler ve uzmanlar için yukarıdaki adımları tekrarlayın.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="Sınıflandırmaların ve kişilerin ekran görüntüsü.":::

8. Tamamlandıktan sonra, **Kapat** veya **Tümünü Kaldır ve Kapat 'ı** seçerek toplu düzenleme dikey penceresini kapatın. Kapat Seçili varlıkları kaldırmaz, Tümünü Kaldır ve Kapat Seçili varlıkların tümünü kaldırır.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Kapanın ekran görüntüsü.":::

   > [!Important]
   > Toplu düzenleme için önerilen varlık sayısı 25 ' tir. 25 ' ten fazla seçim yapmak performans sorunlarına neden olabilir.
   > **Seçili görünüm** kutusu yalnızca en az bir varlık seçili olduğunda görünür olur.


Öğreticiyi izleyin: daha fazla bilgi edinmek için [Sözlük terimleri oluşturun ve içeri aktarın](how-to-create-import-export-glossary.md) .
