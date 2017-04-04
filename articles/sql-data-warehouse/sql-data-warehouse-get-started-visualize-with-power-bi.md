---
title: "Power BI ile SQL Data Warehouse verilerini görselleştirme | Microsoft Azure"
description: "Power BI ile SQL Data Warehouse verilerini görselleştirme"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: d7fb89d1-da1d-4788-a111-68d0e3fda799
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 8790bacecac0fa824189b5c212c2d803092ff4ed
ms.lasthandoff: 01/30/2017


---
# <a name="visualize-data-with-power-bi"></a>Power BI ile verileri görselleştirme
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Bu öğreticide SQL Data Warehouse'a bağlanmak ve birkaç temel görselleştirme oluşturmak üzere Power BI'ı nasıl kullanacağınız gösterilmiştir.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Ön koşullar
Bu öğreticide ilerleyebilmeniz için şunlar gereklidir:

* AdventureWorksDW veritabanı önceden yüklenmiş bir SQL Data Warehouse. Bunu sağlamak için [SQL Veri Ambarı Oluşturma][Create a SQL Data Warehouse] bölümüne bakın ve örnek verileri yüklemeyi seçin. Bir veri ambarınız olmasına karşın örnek verileriniz yoksa [örnek verileri elle yükleyebilirsiniz][load sample data manually].

## <a name="1-connect-to-your-database"></a>1. Veritabanınıza bağlanın
Power BI'ı açmak ve AdventureWorksDW veritabanınıza bağlanmak için şunları yapın:

1. [Azure portal][Azure portal] oturumunu açın.
2. **SQL veritabanları** seçeneğine tıklayın ve AdventureWorks SQL Data Warehouse veritabanınızı seçin.
   
    ![Veritabanınızı bulma][1]
3. "Power BI'da aç" düğmesine tıklayın.
   
    ![Power BI düğmesi][2]
4. Bu işlemin ardından veritabanınızın web adresinin görüntülendiği SQL Data Warehouse bağlantı sayfası açılır. İleri'ye tıklayın
   
    ![Power BI bağlantısı][3]
5. Azure SQL sunucusuna ilişkin kullanıcı adı ve parolanızı girdikten sonra SQL Data Warehouse veritabanınıza tamamen bağlanmış olursunuz.
   
    ![Power BI'da oturum açma][4]
6. Power BI'da oturum açtıktan sonra sol dikey penceredeki AdventureWorksDW veri kümesine tıklayın. Bu işlemin ardından veritabanı açılır.
   
    ![Power BI AdventureWorksDW'yi açma][5]

## <a name="2-create-a-report"></a>2. Bir rapor oluşturun
Artık AdventureWorksDW örnek verilerinizi Power BI kullanarak çözümlemeye hazırsınız. AdventureWorksDW'de çözümlemenin gerçekleştirileceği AggregateSales adlı bir görünüm vardır. Bu görünüm, şirket satışlarının çözümlenmesine yönelik ana ölçümlerden birkaçını içerir.

1. Posta koduna göre satış tutarlarının bir haritasını oluşturmak için sağ taraftaki alanlar bölmesinde AggregateSales görünümüne tıklayıp görünümü genişletin. PostalCode ve SalesAmount sütünlarını tıklayarak seçin.
   
    ![Power BI AggregateSales görünümünü seçme][6]
   
    Power BI otomatik olarak bu verilerin coğrafi veriler olduğunu tanır ve verileri sizin için bir haritaya yerleştirir.
   
    ![Power BI haritası][7]
2. Bu adımda müşteri geliri başına satış tutarını gösteren bir çubuk grafiği oluşturulur. Bu grafiği oluşturmak için genişletilmiş AggregateSales görünümüne gidin. SalesAmount alanına tıklayın. Müşteri Geliri alanını sola sürükleyip Eksene bırakın.
   
    ![Power BI eksen seçme][8]
   
    Çubuk grafiğini sola taşıdık.
   
    ![Power BI çubuğu][9]
3. Bu adımda, sipariş tarihi başına satış tutarını gösteren bir çizgi grafiği oluşturulur. Bu grafiği oluşturmak için genişletilmiş AggregateSales görünümüne gidin. SalesAmount ve OrderDate öğelerine tıklayın Görselleştirmeler sütununun ikinci satırındaki ilk simge olan Çizgi Grafiği simgesine tıklayın.
   
    ![Power BI çizgi grafiğini seçme][10]
   
    Artık verilerin üç farklı görselleştirmesini gösteren bir raporunuz var.
   
    ![Power BI çizgi grafiği][11]

**Dosya**'ya tıklayıp **Kaydet**'i seçerek ilerleme durumunuzu istediğiniz zaman kaydedebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Size örnek verilerle alıştırma yapmanız için biraz zaman tanıdığımıza göre, [geliştirme][develop], [yükleme][load] veya [geçirme][migrate] işlemlerini nasıl gerçekleştireceğinize göz atın. Veya [Power BI web sitesi][Power BI website]'ni ziyaret edin.

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure portal]: https://portal.azure.com/
[Power BI website]: http://www.powerbi.com/

