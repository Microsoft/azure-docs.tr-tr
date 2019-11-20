---
title: Microsoft Excel için Azure Veri Gezgini bağlayıcısını kullanarak verileri görselleştirme
description: Bu makalede, Azure Veri Gezgini için Excel bağlayıcısını nasıl kullanacağınızı öğreneceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 18c6499afe450a0387b0d9f0f13ee4378e0a21a9
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173839"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Excel için Azure Veri Gezgini bağlayıcısını kullanarak verileri görselleştirme

Azure Veri Gezgini, Excel 'deki verilere bağlanmak için iki seçenek sunar: yerel bağlayıcıyı kullanın veya Azure Veri Gezgini bir sorgu içeri aktarın. Bu makalede, Excel 'de yerel bağlayıcının nasıl kullanılacağı ve verileri almak ve görselleştirmek için Azure Veri Gezgini kümesine nasıl bağlanacağınız gösterilmektedir.

Azure Veri Gezgini Excel yerel Bağlayıcısı Excel 'e sorgu sonuçları verme olanağı sunar. Ayrıca ek hesaplamalar veya görselleştirmeler için Excel veri kaynağı olarak bir KQL sorgusu da ekleyebilirsiniz.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Excel veri kaynağı olarak kusto sorgusu tanımlama ve verileri Excel 'e yükleme

1. **Microsoft Excel**'i açın.
1. **Veri** sekmesinde Azure 'dan Azure > **Veri Gezgini** **veri** > Al ' ı seçin.

    ![Azure Veri Gezgini veri al](media/excel-connector/get-data-from-adx.png)

1. **Azure Veri Gezgini (kusto)** penceresinde aşağıdaki alanları tamamlayıp **Tamam**' ı seçin.

    ![Azure Veri Gezgini (kusto) penceresi](media/excel-connector/adx-connection-window.png)
    
    |Alan   |Açıklama |
    |---------|---------|
    |**İçi**   |   Küme adı (zorunlu)      |    
    |**Veritabanı**     |    Veritabanının adı      |    
    |**Tablo adı veya Azure Veri Gezgini sorgusu**    |     Tablo veya Azure Veri Gezgini sorgusunun adı    | 
    
    **Gelişmiş Seçenekler:**

     |Alan   |Açıklama |
    |---------|---------|
    |**Sorgu sonucu kayıt numarasını sınırla**     |     Excel 'e yüklenen kayıtların sayısını sınırlayın  |    
    |**Sorgu sonuç verileri boyutunu sınırla (bayt)**    |    Veri boyutunu sınırla      |   
    |**Sonuç kümesi kesilmesini devre dışı bırak**    |         |      
    |**Ek set deyimleri (noktalı virgülle ayrılmış)**    |    Veri `set` kaynağına uygulanacak deyimler ekleyin     |   

1.  **Gezgin** bölmesinde doğru tabloya gidin. Tablo önizleme bölmesinde, verilerde değişiklik yapmak için **verileri Dönüştür** ' ü seçin veya Excel 'e yüklemek için **Yükle** ' yi seçin.

![Tablo önizleme penceresi](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > **Veritabanı** ve/veya **tablo adı ya da Azure Veri Gezgini sorgusu** zaten belirtilmişse, doğru tablo önizleme bölmesi otomatik olarak açılır. 

## <a name="analyze-and-visualize-data-in-excel"></a>Excel 'de verileri çözümleme ve görselleştirme

Veriler Excel 'e yüklendikten ve Excel sayfanızda kullanılabildiğinde, ilişkileri ve görselleri oluşturarak verileri çözümleyebilir, özetler ve görselleştirebilirsiniz. 

1.  **Tablo tasarımı** sekmesinde **PivotTable ile Özetle**' yi seçin. **PivotTable oluştur** penceresinde, ilgili tabloyu seçin ve **Tamam**' ı seçin.

    ![Özet Tablo Oluştur](media/excel-connector/create-pivot-table.png)

1. Özet tablolar oluşturmak için **PivotTable alanları** bölmesinde ilgili tablo sütunlarını seçin. Aşağıdaki örnekte, **EventID** ve **State** seçilidir.
    
    ![PivotTable alanlarını seçin](media/excel-connector/pivot-table-pick-fields.png)

1. **PivotTable çözümle** sekmesinde, tabloya göre görseller oluşturmak için **PivotChart** ' ı seçin. 

    ![Özet Grafik](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. Aşağıdaki örnekte, hava durumu olaylarıyla ilgili ek bilgileri görüntülemek için **olay kimliği**, **StartTime**ve **EventType** bilgilerini kullanın.

    ![Verileri görselleştirme](media/excel-connector/visualize-excel-data.png)

1. Verilerinizi izlemek için tam panolar oluşturun.

