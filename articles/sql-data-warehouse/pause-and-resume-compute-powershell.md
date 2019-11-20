---
title: 'Hızlı başlangıç: duraklatma & devam eden işlem-PowerShell '
description: Maliyetleri kaydetmek için Azure SQL veri ambarı 'nda işlem duraklatmak için PowerShell 'i kullanın. Veri ambarını kullanmaya hazırsanız işlem işlemine da izin verilmez.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2ad83e51a11e08373f2132e861ec1c8f3ac20458
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686022"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell ile Azure SQL veri ambarı 'nda işlem duraklatma ve devam ettirme

Maliyetleri kaydetmek için Azure SQL veri ambarı 'nda işlem duraklatmak için PowerShell 'i kullanın. Veri ambarını kullanmaya hazırsanız [Işlem işlemine](sql-data-warehouse-manage-compute-overview.md) da izin verilmez.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu hızlı başlangıçta, duraklatıp devam ettirebildiğiniz bir SQL veri ambarı zaten var. Bir tane oluşturmanız gerekiyorsa, **Mysampledatawarehouse**adlı bir veri ambarı oluşturmak için [Create and Connect-Portal](create-data-warehouse-portal.md) ' ı kullanabilirsiniz.

## <a name="log-in-to-azure"></a>Azure'da oturum açma

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu kullanarak Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

Hangi aboneliğin kullandığınızı görmek için [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)komutunu çalıştırın.

```powershell
Get-AzSubscription
```

Varsayılandan farklı bir abonelik kullanmanız gerekiyorsa, [set-AzContext](/powershell/module/az.accounts/set-azcontext)komutunu çalıştırın.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Veri ambarı bilgilerini arama

Duraklatmayı ve sürdürmeyi planladığınız veri ambarı için veritabanı adını, sunucu adını ve kaynak grubunu bulun.

Veri ambarınız için konum bilgilerini bulmak amacıyla aşağıdaki adımları uygulayın.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure portalının sol taraftaki sayfasında **SQL veritabanları**’na tıklayın.
3. **SQL veritabanları** sayfasından **mySampleDataWarehouse** seçeneğini belirleyin. Veri ambarı açılır.

    ![Sunucu adı ve kaynak grubu](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Veritabanı adı olan veri ambarı adı ' nı yazın. Ayrıca sunucu adını ve kaynak grubunu da not alın.
6. Sunucunuz foo.database.windows.net ise, PowerShell cmdlet'lerinde sunucu adı olarak yalnızca ilk bölümü kullanın. Önceki görüntüde tam sunucu adı newserver-20171113.database.windows.net şeklindedir. Son eki bırakın ve PowerShell cmdlet 'inde sunucu adı olarak **newserver-20171113** ' i kullanın.

## <a name="pause-compute"></a>İşlem Duraklat

Maliyetleri kaydetmek için, talep üzerine işlem kaynaklarını duraklatabilir ve sürdürebilirsiniz. Örneğin, gece ve hafta sonları sırasında veritabanını kullanmıyorsanız, bu zamanlarda duraklatıp gün içinde devam edebilirsiniz. Veritabanı duraklatıldığında işlem kaynakları için ücret alınmaz. Ancak, depolama alanı için ücretlendirilmeye devam edersiniz.

Bir veritabanını duraklatmak için, [askıya al-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) cmdlet 'ini kullanın. Aşağıdaki örnekte, **newserver-20171113**adlı bir sunucuda barındırılan **mysampledatawarehouse** adlı bir veri ambarı duraklatılır. Sunucu, **Myresourcegroup**adlı bir Azure Kaynak grubunda bulunur.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

Bir çeşitleme, bu sonraki örnek veritabanını $database nesnesine alır. Sonra, nesneyi [askıya al-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase)' a yöneltin. Sonuçlar, nesne resultDatabase içinde depolanır. Son komut sonuçları gösterir.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>İşlem işlemine geri dön

Bir veritabanını başlatmak için, [özgeçmişi-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) cmdlet 'ini kullanın. Aşağıdaki örnek, newserver-20171113 adlı bir sunucuda barındırılan mySampleDataWarehouse adlı bir veritabanını başlatır. Sunucu, myResourceGroup adlı bir Azure Kaynak grubunda bulunur.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

Bir çeşitleme, bu sonraki örnek veritabanını $database nesnesine alır. Ardından, nesneyi [sürdürülecek-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) ve sonuçları $resultDatabase depolar. Son komut sonuçları gösterir.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>Veri ambarı işleminin durumunu denetleyin

Veri ambarınızın durumunu denetlemek için [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) cmdlet 'ini kullanın.

```
Get-AzSqlDatabaseActivity -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database02"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Veri ambarı birimleri ve veri ambarınızda depolanan veriler için ücretlendirilirsiniz. Bu işlem ve depolama alanı kaynakları ayrı ayrı faturalandırılır.

- Verileri depolama alanında tutmak istiyorsanız, işlem ' i duraklatın.
- Gelecekteki ücretlendirmeleri kaldırmak istiyorsanız, veri ambarını silebilirsiniz.

Kaynakları istediğiniz gibi temizlemek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com)oturum açın ve veri ambarınıza tıklayın.

    ![Kaynakları temizleme](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. İşlemi duraklatmak için, **Duraklat** düğmesine tıklayın. Veri ambarı duraklatıldığında, bir **Başlat** düğmesi görürsünüz.  İşlemi sürdürmek için **Başlat**’a tıklayın.

3. İşlem veya depolama için ücretlendirilmemek üzere veri ambarını kaldırmak için **Sil**’e tıklayın.

4. Oluşturduğunuz SQL Server 'ı kaldırmak için **MyNewServer-20171113.Database.Windows.net**ve ardından **Sil**' e tıklayın.  Sunucuyu silmek sunucuyla ilişkili tüm veritabanlarını da sileceğinden bu silme işlemini gerçekleştirirken dikkatli olun.

5. Kaynak grubunu kaldırmak için, **myResourceGroup**’a tıklayıp daha sonra **Kaynak grubunu sil**’e tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

Artık veri ambarınız için işlem duraklamış ve devam ettirildi. Azure SQL Veri Ambarı hakkında daha fazla bilgi edinmek için, veri yükleme öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Verileri bir SQL veri ambarına yükleme](load-data-from-azure-blob-storage-using-polybase.md)
