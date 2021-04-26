---
title: Azure Izleyici çalışma kitapları-JSONPath ile JSON verilerini dönüştürme
description: Sorgulanan bir uç nokta tarafından döndürülen JSON verilerinin sonuçlarını istediğiniz biçime dönüştürmek için Azure Izleyici çalışma kitaplarında JSONPath kullanma.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 142853f0d5ed787d0b7aaee1bb118a001f144227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722934"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Çalışma kitaplarında JSON verilerini dönüştürmek için JSONPath kullanma

Çalışma kitapları birçok kaynaktaki verileri sorgulayabilir. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) veya özel uç nokta gibi bazı uç noktalar JSON ile sonuçları döndürebilir. Sorgulanan uç nokta tarafından döndürülen JSON verileri, istediğiniz bir biçimde yapılandırılmamışsa, sonuçları dönüştürmek için JSONPath kullanılabilir.

JSONPath, XML için XPath 'e benzer bir JSON sorgu dilidir. XPath gibi, JSONPath, JSON yapısının ayıklanmasını ve veri ölçeğini dışarı aktarmanıza olanak tanır.

JSONPath dönüşümünü kullanarak, çalışma kitabı yazarları JSON 'ı tablo yapısına dönüştürebilir. Tablo daha sonra [çalışma kitabı görselleştirmelerini](./workbooks-overview.md#visualizations)çizmek için kullanılabilir.

## <a name="using-jsonpath"></a>JSONPath kullanma

1. *Düzenleme* araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2.   >  Çalışma kitabına sorgu denetimi eklemek için *Sorgu Ekle* bağlantısını kullanın.
3. Veri kaynağını *JSON* olarak seçin.
4. JSON düzenleyicisini kullanarak aşağıdaki JSON kod parçacığını girin
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Yukarıdaki JSON nesnesi, bir deponun envanterinin temsili olarak verildiğini varsayalım. Görevimiz, ünvanlarını, yazarlarını ve fiyatlarını listeleyerek mağazanın kullanılabilir kitaplarının bir tablosunu oluşturmaktır.

1. *Sonuç ayarları* sekmesini seçin ve sonuç biçimini *JSON yoluna* geçirin.
2. Aşağıdaki JSON yolu ayarlarını uygulayın:

    JSON yol tablosu: `$.store.books` . Bu alan, tablo kökünün yolunu temsil eder. Bu durumda mağazanın defter envanterinden bahseder. Tablo yolu, JSON ile kitap bilgilerine filtre uygular.

   | Sütun kimlikleri | Sütun JSON yolu |
   |:-----------|:-----------------|
   | Başlık      | `$.title`        |
   | Yazma     | `$.author`       |
   | Fiyat      | `$.price`        |

    Sütun kimlikleri sütun başlıkları olacak. Column JSON yollar alanları, tablonun kökünden sütun değerine kadar olan yolu temsil eder.

1. *Sorguyu Çalıştır* ' a tıklayarak yukarıdaki ayarları uygulayın

![ JSON veri kaynağı ve JSON yolu sonuç biçimiyle sorgu öğesi düzenleniyor](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Sonraki adımlar
- [Çalışma kitaplarına genel bakış](./workbooks-overview.md)
- [Azure Izleyici çalışma kitaplarında gruplar](workbooks-groups.md)