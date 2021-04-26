---
title: Visual Studio ve SSDT ile SYNAPSE SQL 'i bağlama ve sorgulama
description: Azure SYNAPSE Analytics kullanarak adanmış SQL havuzunu sorgulamak için Visual Studio 'Yu kullanın.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: ef8e2a3d1a6b78e8f2b6b9a900ed2485c1a4a5d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451598"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Visual Studio ve SSDT ile Synapse SQL’e bağlanma

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Azure SYNAPSE Analytics kullanarak adanmış SQL havuzunu sorgulamak için Visual Studio 'Yu kullanın. Bu yöntem, Visual Studio 2019 ' de SQL Server Veri Araçları (SSDT) uzantısını kullanır. 

> [!NOTE]
> Sunucusuz SQL havuzu SSDT tarafından desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi kullanmak için aşağıdaki bileşenlere sahip olmanız gerekir:

* Mevcut bir adanmış SQL Havuzu. Bir tane yoksa, bu önkoşulu tamamlamaya yönelik [adanmış BIR SQL havuzu oluşturma](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) bölümüne bakın.
* Visual Studio için SSDT. Visual Studio kullanıyorsanız büyük olasılıkla bu bileşene sahipsiniz demektir. Yükleme yönergeleri ve seçenekleri için bkz. [Visual Studio’yu ve SSDT’yi yükleme](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Tam SQL server adı. Bu sunucu adını bulmak için bkz. [adanmış BIR SQL havuzuna bağlanma](connect-overview.md).

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. ayrılmış bir SQL havuzuna bağlanma
1. Visual Studio 2019 ' i açın.
2. **Görünüm** SQL Server Nesne Gezgini ' i seçerek SQL Server Nesne Gezgini açın  >  .
   
    ![SQL Server Nesne Gezgini](./media/get-started-visual-studio/open-ssdt.png)
3. **SQL Server ekle** simgesine tıklayın.
   
    ![SQL Server ekleme](./media/get-started-visual-studio/add-server.png)
4. Sunucuya Bağlan penceresindeki alanları doldurun.
   
    ![Sunucuya bağlanma](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Sunucu adı**: daha önce tanımlanan **sunucu adını** girin.
   * **Kimlik doğrulaması**: **SQL Server kimlik doğrulaması** veya **Active Directory tümleşik kimlik doğrulaması** seçin:
   * **Kullanıcı adı** ve **parola**: yukarıda SQL Server kimlik doğrulaması seçildiyse, Kullanıcı adınızı ve parolanızı girin.
   * **Bağlan**'a tıklayın.
5. Araştırmak için Azure SQL sunucunuzu genişletin. Sunucuyla ilişkili veritabanlarını görüntüleyebilirsiniz. Örnek veritabanınızdaki tabolaları görmek için AdventureWorksDW'yi genişletin.
   
    ![AdventureWorksDW'yi araştırma](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. örnek bir sorgu çalıştırın
Artık veritabanınıza bir bağlantı kuruldığına göre bir sorgu yazacaksınız.

1. SQL Server Nesne Gezgini'nde veritabanınıza sağ tıklayın.
2. **Yeni Sorgu**’yu seçin. Yeni bir sorgu penceresi açılır.
   
    ![Yeni sorgu](./media/get-started-visual-studio/new-query2.png)
3. Aşağıdaki T-SQL sorgusunu sorgu penceresine kopyalayın:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Yeşil oka tıklayarak sorguyu çalıştırın veya şu kısayolu kullanın: `CTRL` + `SHIFT` + `E` .
   
    ![Sorgu çalıştırma](./media/get-started-visual-studio/run-query.png)
5. Sorgu sonuçlarına bakın. Bu örnekte FactInternetSales tablosunda 60398 satır var.
   
    ![Sorgu sonuçları](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Sonraki adımlar
Artık bağlanıp sorgulama yaptığınızda, [Power BI verileri görselleştirmeyi](get-started-power-bi-professional.md)deneyin.
Ortamınızı Azure Active Directory kimlik doğrulaması için yapılandırmak üzere bkz. [ADANMıŞ SQL havuzunda kimlik doğrulama](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 