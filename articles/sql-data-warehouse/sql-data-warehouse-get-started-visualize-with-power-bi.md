---
title: Power BI Microsoft Azure ile verileri görselleştirme
description: Power BI ile SQL Data Warehouse verilerini görselleştirme
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: eea4e3b0b1f0e4ec3eaf3e0aba8952f6693d2921
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685801"
---
# <a name="visualize-data-with-power-bi"></a>Power BI ile verileri görselleştirme
Bu öğreticide SQL Data Warehouse'a bağlanmak ve birkaç temel görselleştirme oluşturmak üzere Power BI'ı nasıl kullanacağınız gösterilmiştir.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Ön koşullar
Bu öğreticide ilerleyebilmeniz için şunlar gereklidir:

* AdventureWorksDW veritabanı önceden yüklenmiş bir SQL Data Warehouse. Bir veri ambarı sağlamak için bkz. [SQL veri ambarı oluşturma](create-data-warehouse-portal.md) ve örnek verileri yüklemeyi seçme. Zaten bir veri ambarınız varsa ancak örnek verileriniz yoksa, [WideWorldImportersDW](load-data-wideworldimportersdw.md)' ı yükleyebilirsiniz.

## <a name="1-connect-to-your-database"></a>1. veritabanınıza bağlanın
Power BI'ı açmak ve AdventureWorksDW veritabanınıza bağlanmak için şunları yapın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **SQL veritabanları** seçeneğine tıklayın ve AdventureWorks SQL Data Warehouse veritabanınızı seçin.
   
    ![Veritabanınızı bulma](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. "Power BI'da aç" düğmesine tıklayın.
   
    ![Power BI düğmesi](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Bu işlemin ardından veritabanınızın web adresinin görüntülendiği SQL Data Warehouse bağlantı sayfası açılır. İleri'ye tıklayın.
   
    ![Power BI bağlantısı](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Azure SQL Server Kullanıcı adınızı ve parolanızı girin.
   
    ![Power BI oturum açma](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Veritabanını açmak için sol dikey penceredeki AdventureWorksDW veri kümesine tıklayın.
   
    ![Power BI AdventureWorksDW'yi açma](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. rapor oluşturma
Artık AdventureWorksDW örnek verilerinizi Power BI kullanarak çözümlemeye hazırsınız. AdventureWorksDW'de çözümlemenin gerçekleştirileceği AggregateSales adlı bir görünüm vardır. Bu görünüm, şirket satışlarının çözümlenmesine yönelik ana ölçümlerden birkaçını içerir.

1. Posta koduna göre satış tutarlarının bir haritasını oluşturmak için sağ taraftaki alanlar bölmesinde AggregateSales görünümüne tıklayıp görünümü genişletin. PostalCode ve SalesAmount sütünlarını tıklayarak seçin.
   
    ![Power BI AggregateSales seçer](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Otomatik olarak tanınan coğrafi verileri Power BI ve sizin için bir haritaya koyun.
   
    ![Power BI haritası](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Bu adımda müşteri geliri başına satış tutarını gösteren bir çubuk grafiği oluşturulur. Çubuk grafiği oluşturmak için genişletilmiş AggregateSales görünümüne gidin. SalesAmount alanına tıklayın. Müşteri Geliri alanını sola sürükleyip Eksene bırakın.
   
    ![Power BI eksen seçer](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Sol taraftaki çubuk grafiği.
   
    ![Power BI çubuğu](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Bu adımda, sipariş tarihi başına satış tutarını gösteren bir çizgi grafiği oluşturulur. Çizgi grafiği oluşturmak için genişletilmiş AggregateSales görünümüne gidin. SalesAmount ve OrderDate öğelerine tıklayın Görsel öğeler sütununda, görsel öğeler altındaki ikinci satırdaki ilk simge olan çizgi grafik simgesine tıklayın.
   
    ![Power BI çizgi grafiği seçer](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Artık verilerin üç farklı görselleştirmesini gösteren bir raporunuz var.
   
    ![Power BI çizgi grafiği](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

**Dosya**'ya tıklayıp **Kaydet**'i seçerek ilerleme durumunuzu istediğiniz zaman kaydedebilirsiniz.

## <a name="using-direct-connect"></a>Doğrudan Bağlan kullanma
Azure SQL veritabanı 'nda olduğu gibi, SQL veri ambarı doğrudan bağlantısı, Power BI analitik özellikleri ile birlikte mantıksal bir aşağı bağlantı sağlar. Doğrudan bağlantı ile, sorgular verileri araştırırken gerçek zamanlı olarak Azure SQL veri ambarınıza geri gönderilir.  Bu özellik SQL veri ambarı ölçeğiyle birlikte kullanıldığında, terabaytlarca veri için dinamik raporlar oluşturmanızı sağlar. Ayrıca, Power BI Aç düğmesinin kullanıma sunulması, kullanıcıların Azure 'un diğer parçalarından bilgi toplamadan Power BI doğrudan SQL veri ambarına bağlanmasını sağlar.

Doğrudan Bağlan kullanırken:

* Bağlanırken tam sunucu adını belirtin.
* Veritabanı için Güvenlik Duvarı kurallarının Azure hizmetlerine erişime Izin verecek şekilde yapılandırıldığından emin olun.
* Bir sütun seçme veya filtre ekleme gibi her eylem, doğrudan veri ambarını sorgular.
* Kutucuklar otomatik olarak ve yaklaşık 15 dakikada bir yenilenir.
* Q & A, doğrudan bağlantı veri kümeleri için kullanılamaz.
* Şema değişiklikleri otomatik olarak eklenir.
* Tüm doğrudan bağlanma sorguları 2 dakika sonra zaman aşımına uğrar.

Deneyimler geliştirilmesine yönelik bu kısıtlamalar ve notlar değişebilir.

## <a name="next-steps"></a>Sonraki adımlar
Artık size örnek verilerle ilgili bir süre önce verildiğimiz için, bkz. [geliştirme](sql-data-warehouse-overview-develop.md) veya [yükleme](design-elt-data-loading.md). Veya [Power BI web sitesi](https://www.powerbi.com/)'ni ziyaret edin.
