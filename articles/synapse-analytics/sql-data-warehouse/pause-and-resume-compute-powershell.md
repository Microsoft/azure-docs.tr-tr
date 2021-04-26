---
title: 'Hızlı başlangıç: Azure PowerShell ile adanmış SQL havuzunda (eski adıyla SQL DW) işlem duraklatma ve devam ettirme'
description: Adanmış SQL havuzunu (eski adıyla SQL DW) duraklatmak ve devam etmek için Azure PowerShell kullanabilirsiniz. işlem kaynakları.
services: synapse-analytics
author: julieMSFT
ms.author: jrasnick
manager: craigg
ms.reviewer: igorstan
ms.date: 03/20/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: be82b6dcc17c2850b9a35085316cd0905a5b6b75
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566809"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell ile adanmış SQL havuzunda (eski adıyla SQL DW) işlem duraklatma ve devam ettirme

Adanmış SQL Havuzu (eski adıyla SQL DW) işlem kaynaklarını duraklatmak ve devam etmek için Azure PowerShell kullanabilirsiniz.
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu hızlı başlangıçta, duraklatmanızı ve devam ettirebildiğiniz özel bir SQL Havuzu (eski adıyla SQL DW) zaten var olduğu varsayılır. Bir tane oluşturmanız gerekiyorsa, **Mysampledatawarehouse** adlı özel bir SQL Havuzu (eskı ADıYLA SQL DW) oluşturmak için [Create and Connect-Portal](create-data-warehouse-portal.md) ' ı kullanabilirsiniz.

## <a name="log-in-to-azure"></a>Azure'da oturum açma

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) komutunu kullanarak Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

Hangi aboneliğin kullandığınızı görmek için [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)komutunu çalıştırın.

```powershell
Get-AzSubscription
```

Varsayılandan farklı bir abonelik kullanmanız gerekiyorsa, [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)komutunu çalıştırın.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Adanmış SQL Havuzu (eski adıyla SQL DW) bilgilerini arama

Duraklatmak ve devam etmeyi planladığınız adanmış SQL havuzunun (eskiden SQL DW) veritabanı adını, sunucu adını ve kaynak grubunu bulun.

Adanmış SQL havuzunuzun konum bilgilerini (eski adıyla SQL DW) bulmak için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Azure portal sol sayfasında **Azure SYNAPSE Analytics (eski ADıYLA SQL DW)** seçeneğine tıklayın.
1. **Azure SYNAPSE Analytics (eski ADıYLA SQL DW)** sayfasından **mysampledatawarehouse** öğesini seçin. SQL Havuzu açılır.

    ![Sunucu adı ve kaynak grubu](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Veritabanı adı olan adanmış SQL Havuzu (eski adıyla SQL DW) adını yazın. Ayrıca sunucu adını ve kaynak grubunu da not alın.
1. PowerShell cmdlet 'lerinde sunucu adının yalnızca ilk kısmını kullanın. Yukarıdaki görüntüde, tam sunucu adı sqlpoolservername.database.windows.net ' dir. PowerShell cmdlet 'inde sunucu adı olarak **sqlpoolservername** kullanıyoruz.

## <a name="pause-compute"></a>İşlem Duraklat

Maliyetleri kaydetmek için, talep üzerine işlem kaynaklarını duraklatabilir ve sürdürebilirsiniz. Örneğin, gece ve hafta sonları sırasında veritabanını kullanmıyorsanız, bu zamanlarda duraklatıp gün içinde devam edebilirsiniz.

>[!NOTE]
>Veritabanı duraklatıldığında işlem kaynakları için ücret alınmaz. Ancak, depolama alanı için ücretlendirilmeye devam edersiniz.

Bir veritabanını duraklatmak için, [askıya al-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet 'ini kullanın. Aşağıdaki örnek, **sqlpoolservername** adlı bir sunucuda barındırılan **MYSAMPLEDATAWAREHOUSE** adlı bir SQL havuzunu duraklatır. Sunucu, **Myresourcegroup** adlı bir Azure Kaynak grubunda bulunur.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

Aşağıdaki örnek veritabanını $database nesnesine alır. Sonra, nesneyi [askıya al-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)' a yöneltin. Sonuçlar, nesne resultDatabase içinde depolanır. Son komut sonuçları gösterir.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>İşlem işlemine geri dön

Bir veritabanını başlatmak için, [özgeçmişi-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet 'ini kullanın. Aşağıdaki örnek, **sqlpoolservername** adlı bir sunucuda barındırılan **mysampledatawarehouse** adlı bir veritabanını başlatır. Sunucu, **Myresourcegroup** adlı bir Azure Kaynak grubunda bulunur.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

Sonraki örnek veritabanını $database nesnesine alır. Ardından, nesneyi [sürdürülecek-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ve sonuçları $resultDatabase depolar. Son komut sonuçları gösterir.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>SQL havuzu operasyonunuzun durumunu denetleyin

Adanmış SQL havuzunuzun durumunu denetlemek için (eski adıyla SQL DW), [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet 'ini kullanın.

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Veri ambarı birimleri ve özel SQL havuzunuzu (eski adıyla SQL DW) depolanan veriler için ücretlendirilirsiniz. Bu işlem ve depolama alanı kaynakları ayrı ayrı faturalandırılır.

- Verileri depolama alanında tutmak istiyorsanız, işlem ' i duraklatın.
- Gelecekteki ücretleri kaldırmak istiyorsanız, SQL havuzunu silebilirsiniz.

Kaynakları istediğiniz gibi temizlemek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com)oturum açın ve SQL havuzunuza tıklayın.

    ![Kaynakları temizleme](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. İşlemi duraklatmak için, **Duraklat** düğmesine tıklayın. SQL havuzu duraklatıldığında, bir **Başlat** düğmesi görürsünüz.  İşlemi sürdürmek için **Başlat**’a tıklayın.

3. İşlem veya depolama için ücretlendirilmemek üzere SQL havuzunu kaldırmak için **Sil**' e tıklayın.

4. Oluşturduğunuz SQL Server 'ı kaldırmak için **sqlpoolservername.Database.Windows.net** ve ardından **Sil**' e tıklayın.  Sunucuyu silmek sunucuyla ilişkili tüm veritabanlarını da sileceğinden bu silme işlemini gerçekleştirirken dikkatli olun.

5. Kaynak grubunu kaldırmak için, **myResourceGroup**’a tıklayıp daha sonra **Kaynak grubunu sil**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

SQL havuzu hakkında daha fazla bilgi edinmek için [verileri ADANMıŞ SQL Havuzu (eski ADıYLA SQL DW) Ile yükleme](./load-data-from-azure-blob-storage-using-copy.md) makalesine geçin. İşlem yeteneklerini yönetme hakkında daha fazla bilgi için bkz. [işlem yönetimine genel bakış](sql-data-warehouse-manage-compute-overview.md) makalesi.
