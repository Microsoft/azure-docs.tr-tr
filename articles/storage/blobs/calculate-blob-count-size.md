---
title: Azure depolama envanterini kullanarak blob sayısını ve boyutunu hesaplama
description: Kapsayıcı başına Blobların sayısını ve toplam boyutunu hesaplamayı öğrenin.
services: storage
author: twooley
ms.author: twooley
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: e752d40ce2f237c2ab08bac2e71133cd06ec40e4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277190"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Azure depolama envanterini kullanarak kapsayıcı başına blob sayısı ve toplam boyut hesaplama

Bu makalede, blob sayısını ve kapsayıcı başına Blobların toplam boyutunu hesaplamak için Azure Blob Storage Inventory Feature ve Azure SYNAPSE kullanılmaktadır. Bu değerler, kapsayıcı başına blob kullanımını iyileştirmek için faydalıdır.

Blob meta verileri Bu metoda dahil değildir. Azure Blob Storage Inventory özelliği, varsayılan parametrelerle REST API [bloblarını](/rest/api/storageservices/list-blobs) kullanır. Bu nedenle, örnek anlık görüntüleri, ' $ ' kapsayıcılarını ve benzerlerini desteklemez.

> [!IMPORTANT]
> Blob envanteri şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan yasal koşullara yönelik [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

## <a name="enable-inventory-reports"></a>Envanter raporlarını etkinleştir

Bu yöntemin ilk adımı, depolama hesabınızda [Envanter raporlarının etkinleştirilme](blob-inventory.md#enable-inventory-reports) yöntemidir. İlk Raporunuzun oluşturulması için envanter raporlarını etkinleştirdikten sonra 24 saate kadar beklemeniz gerekebilir.

Analiz edilecek bir envanter raporunuz varsa, kendinize blob, rapor CSV dosyasının bulunduğu kapsayıcıya okuma erişimi verin.

1. Inventory CSV rapor dosyası ile kapsayıcıya gidin.
1. **Access Control (IAM)** öğesini seçin ve ardından **rol atamaları ekleyin**

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Rol atamaları Ekle ' yi seçin":::

1. **Rol** açılan listesinden **Depolama Blobu veri okuyucu** ' yı seçin.

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Açılan listeden Depolama Blobu veri okuyucusu rolünü ekleyin":::

1. Raporu **Seç** alanında çalıştırmak için kullandığınız hesabın e-posta adresini girin.

## <a name="create-an-azure-synapse-workspace"></a>Azure Synapse çalışma alanı oluşturma

Ardından, envanter sonuçlarını raporlamak üzere bir SQL sorgusu yürütebileceğiniz [bir Azure SYNAPSE çalışma alanı oluşturun](../../synapse-analytics/get-started-create-workspace.md) .

## <a name="create-the-sql-query"></a>SQL sorgusu oluşturma

Azure SYNAPSE çalışma alanınızı oluşturduktan sonra, aşağıdaki adımları uygulayın.

1. Öğesine gidin [https://web.azuresynapse.net](https://web.azuresynapse.net) .
1. Sol kenardaki **Geliştir** sekmesini seçin.
1. Bir öğe eklemek için büyük artı işaretini (+) seçin.
1. **SQL betiği** seçin.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Yeni bir sorgu oluşturmak için SQL betiği seçin":::

## <a name="run-the-sql-query"></a>SQL sorgusunu çalıştırma

1. [INVENTORY CSV dosyasını okumak](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file)IÇIN aşağıdaki SQL sorgusunu Azure SYNAPSE çalışma alanınıza ekleyin.

    Parametresi için `bulk` , çözümlemek istediğiniz envanter raporu CSV dosyasının URL 'sini kullanın.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Sağdaki Özellikler bölmesinde SQL sorgunuzu adlandırın.

1. CTRL + S tuşlarına basarak veya **Tümünü Yayımla** DÜĞMESINI seçerek SQL sorgunuzu yayımlayın.

1. SQL sorgusunu yürütmek için **Çalıştır** düğmesini seçin. BLOB sayısı ve kapsayıcı başına toplam boyut, **sonuçlar** bölmesinde raporlanır.

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Blob sayısını ve toplam boyutunu hesaplamak için betiği çalıştırmanın çıkışı.":::

## <a name="next-steps"></a>Sonraki adımlar

- [Blob verilerini yönetmek için Azure Storage blob envanterini kullanma](blob-inventory.md)
- [Bir blob kapsayıcısının toplam fatura boyutunu hesaplama](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)