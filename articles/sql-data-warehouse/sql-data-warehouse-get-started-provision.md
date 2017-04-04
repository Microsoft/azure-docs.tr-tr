---
title: "Azure portalında SQL Veri Ambarı Oluşturma | Microsoft Belgeleri"
description: "Azure portalında Azure SQL Veri Ambarı oluşturmayı öğrenin"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 75f4792ff2aa2b73ebc3ff976887a74ce09988f3
ms.lasthandoff: 03/10/2017


---
# <a name="create-an-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse oluşturma
> [!div class="op_single_selector"]
> * [Azure portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Bu öğreticide, AdventureWorksDW örnek veritabanı içeren bir SQL Veri Ambarı'nın oluşturulması için Azure portalı kullanılmaktadır.

## <a name="prerequisites"></a>Ön koşullar
Başlamak için gerekli olanlar:

* **Azure hesabı**: Hesap oluşturmak için [Azure Ücretsiz Deneme][Azure Free Trial] veya [MSDN Azure Kredileri][MSDN Azure Credits] sayfasını ziyaret edin.
* **Azure SQL sunucusu**: Daha ayrıntılı bilgi için bkz. [Azure portalı ile Azure SQL veritabanı oluşturma][Create an Azure SQL database in the Azure portal].

> [!NOTE]
> SQL Veri Ambarı'nın oluşturulması ek hizmet ücretlerinin alınmasına neden olabilir.  Ayrıntılı bilgi için bkz. [SQL Veri Ambarı fiyatlandırması][SQL Data Warehouse pricing].
>
>

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse oluşturma
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **+ Yeni** > **Veritabanları** > **SQL Veri Ambarı**'na tıklayın.

    ![Oluşturma](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. **SQL Data Warehouse** dikey penceresinde gerekli bilgileri girin ve "Oluştur" düğmesine basın.

    ![Veritabanı oluşturma](./media/sql-data-warehouse-get-started-provision/create-database.png)

   * **Sunucu**: İlk önce sunucunuzu seçmenizi öneririz.  
   * **Veritabanı adı**: SQL Veri Ambarı'na başvurmak için kullanılan ad.  Sunucu için benzersiz olmalıdır.
   * **Performans**: 400 [DWU][DWU] ile başlamanızı öneririz. Veri ambarınızın performansını ayarlamak için kaydırıcıyı sağa veya sola hareket ettirebilir ya da oluşturma işlemi tamamlandıktan sonra ölçeği artırıp azaltabilirsiniz.  DWU’lar hakkında daha fazla bilgi edinmek için [ölçeklendirme](sql-data-warehouse-manage-compute-overview.md) ile ilgili belgelerimizi veya [fiyatlandırma][SQL Data Warehouse pricing] sayfamızı inceleyebilirsiniz.
   * **Abonelik**: Bu SQL Data Warehouse'un faturalanacağı [aboneliği] seçin.
   * **Kaynak grubu**: [Kaynak grupları][Resource group], Azure kaynak koleksiyonunu yönetmenize yardımcı olmak üzere tasarlanmış kapsayıcılardır. [Kaynak grupları](../azure-resource-manager/resource-group-overview.md) hakkında daha fazla bilgi edinin.
   * **Kaynak seçme**: **Kaynak seç** > **Örnek** seçeneğine tıklayın. Azure, **Örnek seçin** alanını AdventureWorksDW olarak otomatik doldurur.

   > [!NOTE]
   > SQL Veri Ambarı için varsayılan harmanlama SQL_Latin1_General_CP1_CI_AS şeklindedir. Farklı bir harmanlama gerekiyorsa veritabanını farklı bir harmanlama ile oluşturmak için [T-SQL][T-SQL] kullanılabilir.
   >
   >

1. SQL Data Warehouse'unuzu oluşturmak için **Oluştur** düğmesine tıklayın.
2. Birkaç dakika bekleyin. Veri ambarınız hazır olduğunda [Azure Portal](https://portal.azure.com) yeniden yönlendirileceksiniz. SQL Data Warehouse'unuzu SQL Database'ler altında listelenmiş bir şekilde panonuzda veya SQL Data Warehouse'unuzu oluşturmak için kullandığınız kaynak grubunda bulabilirsiniz.

    ![portal görünümü](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar
Bir SQL Data Warehouse oluşturduğunuza göre [Bağlanın](sql-data-warehouse-connect-overview.md) ve sorgulamaya başlayın.

SQL Data Warehouse'a veri yüklemek için bkz. [yüklemeye genel bakış](sql-data-warehouse-overview-load.md).

Var olan bir veritabanını SQL Data Warehouse'a geçirmeye çalışıyorsanız [Geçirmeye genel bakış](sql-data-warehouse-overview-migrate.md) konu başlığına göz atın veya [Geçiş Yardımcı Programı](sql-data-warehouse-migrate-migration-utility.md)'nı kullanın.

Güvenlik duvarı kuralları, Transact-SQL kullanarak de yapılandırılabilir. Daha fazla bilgi için bkz. [sp_set_firewall_rule][sp_set_firewall_rule] ve [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

[En iyi uygulamalar][Best practices] bölümüne bakmak da iyi bir fikir olabilir.

<!--Article references-->
[Create an Azure SQL database in the Azure portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[resource groups]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[Best practices]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md
[aboneliği]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

