---
title: "PowerShell kullanarak SQL Veri Ambarı oluşturma | Microsoft Belgeleri"
description: "PowerShell kullanarak SQL Data Warehouse oluşturma"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 58ba34f8f99b7cd2b6a9a199bc70d79431405100
ms.lasthandoff: 03/10/2017


---
# <a name="create-sql-data-warehouse-using-powershell"></a>PowerShell kullanarak SQL Data Warehouse oluşturma
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Bu makalede PowerShell ile SQL Veri Ambarı oluşturma işlemi gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar
Başlamak için gerekli olanlar:

* **Azure hesabı**: Hesap oluşturmak için [Azure Ücretsiz Deneme][Azure Free Trial] veya [MSDN Azure Kredileri][MSDN Azure Credits] sayfasını ziyaret edin.
* **Azure SQL server**: Daha fazla bilgi için bkz. [Azure portalında Azure SQL veritabanı oluşturma][Create an Azure SQL database in the Azure Portal] veya [PowerShell ile Azure SQL veritabanı oluşturma][Create an Azure SQL database with PowerShell].
* **Kaynak grubu**: Azure SQL sunucunuz ile aynı kaynak grubunu kullanın veya [kaynak grubu oluşturma](../azure-resource-manager/resource-group-portal.md) işlemine bakın.
* **PowerShell 1.0.3 sürümü veya sonraki bir sürümü**: **Get-Module -ListAvailable -Name Azure** komutunu çalıştırarak sürümünüzü kontrol edebilirsiniz.  [Microsoft Web Platformu Yükleyicisi][Microsoft Web Platform Installer]'nden en son sürümü yükleyebilirsiniz.  En son sürümü yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma][How to install and configure Azure PowerShell].

> [!NOTE]
> Bir SQL Veri Ambarı'nın oluşturulması ek hizmet ücretlerinin alınmasına neden olabilir.  Fiyatlandırmayla ilgili ayrıntılı bilgi için bkz. [SQL Veri Ambarı fiyatlandırması][SQL Data Warehouse pricing].
>
>

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse oluşturma
1. Windows PowerShell'i açın.
2. Azure Resource Manager'da oturum açmak için bu cmdlet'i çalıştırın

    ```Powershell
    Login-AzureRmAccount
    ```
3. Geçerli oturumunuz için kullanmak istediğiniz aboneliği seçin.

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Veritabanı oluşturun. Bu örnekte "mywesteuroperesgp1" adlı kaynak grubunda bulunan "sqldwserver1" adlı sunucuda, hizmet hedefi düzeyi "DW400" olan "mynewsqldw" adlı bir veritabanı oluşturulmaktadır.

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

Gerekli Parametreler şunlardır:

* **RequestedServiceObjectiveName**: İstediğiniz [DWU][DWU] sayısı.  Desteklenen değerler: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 ve DW6000.
* **DatabaseName**: Oluşturduğunuz SQL Data Warehouse'un adı.
* **ServerName**: Oluşturma işlemi için kullandığınız sunucunun adı (V12 olmalıdır).
* **ResourceGroupName**: Kullandığınız kaynak grubu.  Aboneliğinizdeki kullanılabilir kaynak gruplarını bulmak için Get-AzureResource komutunu kullanın.
* **Edition**: SQL Veri Ambarı oluşturmak için "DataWarehouse" olmalıdır.

İsteğe Bağlı Parametreler şunlardır:

* **CollationName**: Belirtilmezse varsayılan harmanlama SQL_Latin1_General_CP1_CI_AS şeklindedir.  Bir veritabanı üzerinde harmanlama değiştirilemez.
* **MaxSizeBytes**: Bir veritabanının varsayılan en büyük boyutu 10 GB'dir.

Parametre seçenekleri hakkında daha ayrıntılı bilgi için bkz. [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] ve [Veritabanı Oluşturma (Azure SQL Veri Ambarı)][Create Database (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Sonraki adımlar
SQL Veri Ambarınızın hazırlanması tamamlandıktan sonra [örnek veri yükleme][loading sample data] işlemini gerçekleştirmeyi veya [geliştirme][develop], [yükleme][load] veya [geçirme][migrate] işlemlerinin nasıl gerçekleştirileceğini incelemeyi tercih edebilirsiniz.

Programlama yoluyla Veri Ambarı’nı yönetme hakkında daha fazla bilgi edinmek istiyorsanız [PowerShell cmdlet’leri ve REST API'lerinin][PowerShell cmdlets and REST APIs] kullanımına ilişkin makalemize göz atın.

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

