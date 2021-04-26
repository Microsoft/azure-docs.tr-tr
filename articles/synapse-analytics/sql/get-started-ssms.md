---
title: SQL Server Management Studio (SSMS) ile SYNAPSE SQL 'e bağlanma
description: Azure SYNAPSE Analytics 'te SYNAPSE SQL 'e bağlanmak ve sorgulamak için SQL Server Management Studio (SSMS) kullanın.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 222505b03aac5bfc4e8d00b8c8977bece34dee85
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567528"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) ile SYNAPSE SQL 'e bağlanma
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) kullanarak, SUNUCUSUZ SQL havuzu veya adanmış SQL havuzu kaynakları aracılığıyla Azure SYNAPSE Analytics 'TE SYNAPSE SQL 'e bağlanabilir ve bunları sorgulayabilirsiniz. 

### <a name="supported-tools-for-serverless-sql-pool"></a>Sunucusuz SQL havuzu için desteklenen Araçlar

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) , 1.18.0 sürümünden başlayarak tam olarak desteklenmektedir. SSMS, sürüm 18,5 ' den başlayarak kısmen desteklenir, bunu yalnızca bağlanmak ve sorgulamak için kullanabilirsiniz.

> [!NOTE]
> AAD oturumunun sorgu yürütme sırasında 1 saatten uzun süre açık bağlantısı varsa, AAD 'yi kullanan herhangi bir sorgu başarısız olur. Bu, AAD ile etkileşime geçen AAD geçişli geçiş ve deyimlerini kullanarak depolamayı sorgulamayı içerir (dış sağlayıcı oluşturma gibi). Bu, SSMS ve REKLAMLARı 'nda sorgu Düzenleyicisi gibi bağlantı açık tutan tüm araçları etkiler. Sorgu yürütmek için yeni bağlantı açan araçlar SYNAPSE Studio gibi etkilenmez.
> Bu sorunu gidermek için SSMS 'yi yeniden başlatabilir veya reklamlar ' a bağlanıp bağlantıyı kesebilirsiniz. .
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki önkoşullara sahip olduğunuzdan emin olun:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Adanmış SQL havuzu için, mevcut bir veri ambarına ihtiyacınız vardır. Bir tane oluşturmak için bkz. [adanmış BIR SQL havuzu oluşturma](../quickstart-create-sql-pool-portal.md). Sunucusuz SQL havuzu için, oluşturma sırasında çalışma alanınızda yerleşik olarak adlandırılan bir tane zaten sağlanmış olur. 
* Tam nitelikli SQL Server adı. Bu adı bulmak için bkz. [SYNAPSE SQL 'e bağlanma](connect-overview.md).

## <a name="connect"></a>Bağlanma

### <a name="dedicated-sql-pool"></a>Ayrılmış SQL havuzu

Adanmış SQL havuzu kullanarak SYNAPSE SQL 'e bağlanmak için şu adımları izleyin: 

1. SQL Server Management Studio (SSMS) açın. 
1. **Sunucuya Bağlan** iletişim kutusunda alanları doldurup **Bağlan**' ı seçin. 
  
    ![Sunucu 1 ' e bağlanma](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Sunucu adı**: daha önce tanımlanan **sunucu adını** girin.
   * **Kimlik doğrulaması**: **SQL Server kimlik doğrulaması** veya **Active Directory tümleşik kimlik doğrulaması** gibi bir kimlik doğrulama türü seçin.
   * **Kullanıcı adı** ve **parola**: yukarıda SQL Server kimlik doğrulaması seçildiyse, Kullanıcı adınızı ve parolanızı girin.

1. Azure SQL Server **Nesne Gezgini**' ni genişletin. Sunucu ile ilişkili veritabanlarını (örneğin, örnek AdventureWorksDW veritabanı) görüntüleyebilirsiniz. Tabloları görmek için veritabanını genişletebilirsiniz:
   
    ![AdventureWorksDW 1 ' i keşfet](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="serverless-sql-pool"></a>Sunucusuz SQL havuzu

Sunucusuz SQL havuzu kullanarak SYNAPSE SQL 'e bağlanmak için şu adımları izleyin: 

1. SQL Server Management Studio (SSMS) açın.
1. **Sunucuya Bağlan** iletişim kutusunda alanları doldurup **Bağlan**' ı seçin. 
   
    ![Sunucu 2 ' ye Bağlan](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Sunucu adı**: daha önce tanımlanan **sunucu adını** girin.
   * **Kimlik doğrulaması**: **SQL Server kimlik doğrulaması** veya **Active Directory tümleşik kimlik doğrulaması** gibi bir kimlik doğrulama türü seçin:
   * **Kullanıcı adı** ve **parola**: yukarıda SQL Server kimlik doğrulaması seçildiyse, Kullanıcı adınızı ve parolanızı girin.
   * **Bağlan**’ı seçin.

4. Araştırmak için Azure SQL sunucunuzu genişletin. Sunucuyla ilişkili veritabanlarını görüntüleyebilirsiniz. Örnek veritabanınızdaki içeriği görmek için *demo* ' i genişletin.
   
    ![AdventureWorksDW 2 ' i keşfet](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Örnek sorgu çalıştırma

### <a name="dedicated-sql-pool"></a>Ayrılmış SQL havuzu

Bir veritabanı bağlantısı kuruldığına göre, verileri sorgulayabilirsiniz.

1. SQL Server Nesne Gezgini'nde veritabanınıza sağ tıklayın.
2. **Yeni Sorgu**’yu seçin. Yeni bir sorgu penceresi açılır.
   
    ![Yeni sorgu 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Aşağıdaki T-SQL sorgusunu sorgu penceresine kopyalayın:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. `Execute`Aşağıdaki kısayolu seçerek veya kullanarak sorguyu çalıştırın: `F5` .
   
    ![Sorgu 1 ' i Çalıştır](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Sorgu sonuçlarına bakın. Aşağıdaki örnekte, FactInternetSales tablosunda 60398 satır vardır.
   
    ![Sorgu sonuçları 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="serverless-sql-pool"></a>Sunucusuz SQL havuzu

Bir veritabanı bağlantısı kurdığınıza göre, verileri sorgulayabilirsiniz.

1. SQL Server Nesne Gezgini'nde veritabanınıza sağ tıklayın.
2. **Yeni Sorgu**’yu seçin. Yeni bir sorgu penceresi açılır.
   
    ![Yeni sorgu 2](./media/get-started-ssms/new-query.png)
3. Aşağıdaki T-SQL sorgusunu sorgu penceresine kopyalayın:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. `Execute`Aşağıdaki kısayolu seçerek veya kullanarak sorguyu çalıştırın: `F5` .
   
    ![Sorgu Çalıştır 2](./media/get-started-ssms/execute-query.png)
5. Sorgu sonuçlarına bakın. Bu örnekte, usPopulationView görünümü 3664512 satıra sahiptir.
   
    ![Sorgu sonuçları 2](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Sonraki adımlar
Artık bağlanıp sorgulama yaptığınızda, [Power BI verileri görselleştirmeyi](get-started-power-bi-professional.md)deneyin.

Ortamınızı Azure Active Directory kimlik doğrulaması için yapılandırmak üzere bkz. [SYNAPSE SQL 'de kimlik](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)doğrulama.

