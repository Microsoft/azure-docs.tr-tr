---
title: Power BI Professional ile SYNAPSE SQL 'e bağlanma
description: Bu öğreticide, Power BI Desktop 'ı sunucusuz SQL havuzuna bağlama adımlarını inceleyeceğiz.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: b33edb99109b7516e1792497a936031cf954bc15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451603"
---
# <a name="connect-to-serverless-sql-pool-with-power-bi-professional"></a>Power BI Professional ile sunucusuz SQL havuzuna bağlanma

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

Bu öğreticide, Power BI Desktop 'ı sunucusuz SQL havuzuna bağlama adımlarını inceleyeceğiz.

## <a name="prerequisites"></a>Önkoşullar

Sorgu vermek için aşağıdaki araçlara ihtiyacınız vardır:

- Tercih ettiğiniz SQL istemcisi:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI Masaüstü yüklendi

Parametreler:

| Parametre                                 | Açıklama                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Sunucusuz SQL havuzu hizmeti uç noktası adresi    | Sunucu adı olarak kullanılacak                                   |
| Sunucusuz SQL havuzu hizmet uç noktası bölgesi     | Örneklerde hangi depolamanın kullanılacağını belirlemek için kullanılır |
| Uç nokta erişimi için Kullanıcı adı ve parola | Uç noktaya erişmek için kullanılacak                               |
| Görünümler oluşturmak için kullanacağınız veritabanı       | Bu veritabanı, örneklerde başlangıç noktası olarak kullanılacaktır       |

## <a name="first-time-setup"></a>İlk kez kurulum

Örnek kullanılmadan önce iki adım vardır:

1. Görünümleriniz için veritabanı oluşturma
2. Depolama alanındaki dosyalara erişmek için sunucusuz SQL havuzu tarafından kullanılacak kimlik bilgilerini oluşturma

### <a name="create-database"></a>Veritabanı oluşturma

Bu başlangıç makalesinde, bir demo olarak kullanmak üzere kendi veritabanınızı oluşturmanız gerekir. Görünümler oluşturmak için bir veritabanı gereklidir. Bu veritabanı, bu belge içindeki bazı örnek sorgulardan bu veritabanını kullanacaksınız.

> [!NOTE]
> Veritabanları, gerçek veriler için değil yalnızca meta verileri görüntülemek için kullanılır.
>
> Kullanmakta olduğunuz veritabanı adını yazın, daha sonra üzerinde gerekecektir.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Kimlik bilgileri oluştur

Sorguları çalıştırabilmeniz için önce kimlik bilgileri oluşturuyoruz. Bu kimlik bilgileri, depolama alanındaki dosyalara erişmek için sunucusuz SQL havuzu hizmeti tarafından kullanılacaktır.

> [!NOTE]
> Depolama hesabı erişimi için kimlik bilgileri oluşturmanız gerekir. Sunucusuz SQL havuzu farklı bölgelerdeki depolamaya erişebilse de depolama ve Azure SYNAPSE çalışma alanının aynı bölgede bulunması daha iyi bir performans deneyimi sağlar.

**Census veri kapsayıcıları için kimlik bilgileri oluşturma hakkında kod parçacığı**, şunu çalıştırın:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>Power BI Masaüstü raporu oluşturma

Power BI Masaüstü uygulamasını açın ve **veri al** seçeneğini belirleyin.

![Power BI Masaüstü uygulamasını açın ve veri al ' ı seçin.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>1. adım-veri kaynağını seçme

Menüden **Azure** ' u ve ardından **Azure SQL veritabanı**' nı seçin.
![Veri kaynağını seçin.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>2. adım-veritabanı seçme

Veritabanının URL 'sini ve görünümün bulunduğu veritabanının adını yazın.
![Uç noktada veritabanı ' nı seçin.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure Data Studio kullanarak sunucusuz SQL havuzuna nasıl bağlanacağınızı öğrenmek için [sorgu depolama dosyalarına](get-started-azure-data-studio.md) ilerleyin.