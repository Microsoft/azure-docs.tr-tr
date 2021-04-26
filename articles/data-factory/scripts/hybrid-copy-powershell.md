---
title: PowerShell kullanarak verileri Şirket içinden Azure 'a kopyalama
description: Bu PowerShell betiği, verileri bir SQL Server veritabanından başka bir Azure Blob depolama alanına kopyalar.
ms.service: data-factory
ms.topic: article
ms.author: jingwang
author: linda33wj
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 34c5497a8b059260dfe60e8015e62c0f1511f021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373394"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-sql-server-to-azure"></a>SQL Server verileri Azure 'a kopyalamak için bir Data Factory işlem hattı oluşturmak üzere PowerShell 'i kullanma

Bu örnek PowerShell betiği, verileri bir SQL Server veritabanından Azure Blob depolama alanına kopyalayan Azure Data Factory bir işlem hattı oluşturur.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Önkoşullar

- **SQL Server**. Bu örnekteki bir SQL Server veritabanını **kaynak** veri deposu olarak kullanırsınız.
- **Azure depolama hesabı**. Azure Blob depolama alanını bu örnekteki **hedef/havuz** veri deposu olarak kullanırsınız. bir Azure depolama hesabınız yoksa, oluşturma adımları için [depolama hesabı oluşturma](../../storage/common/storage-account-create.md) makalesine bakın.
- **Şirket içinde barındırılan tümleştirme çalışma zamanı**. MSI dosyasını [indirme merkezi](https://www.microsoft.com/download/details.aspx?id=39717) 'nden indirin ve makinenize şirket içinde barındırılan tümleştirme çalışma zamanı yüklemek için çalıştırın.  

### <a name="create-sample-database-in-sql-server"></a>SQL Server örnek veritabanı oluşturma
1. SQL Server veritabanında, aşağıdaki SQL komut **dosyasını kullanarak,** şunu kullanan bir tablo oluşturun:

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Tabloya bazı örnek veriler ekleyin:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Örnek betik

> [!IMPORTANT]
> Bu betik, c:\ ' daki sabit sürücünüzde Data Factory varlıkları (bağlı hizmet, veri kümesi ve işlem hattı) tanımlayan JSON dosyaları oluşturur. klasörde.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Örnek betiği çalıştırdıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanabilirsiniz:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Veri fabrikasını kaynak grubundan kaldırmak için aşağıdaki komutu çalıştırın:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır:

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Veri fabrikası oluşturma. |
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Bağlı bir hizmette kimlik bilgilerini şifreler ve şifrelenmiş kimlik bilgisiyle yeni bir bağlı hizmet tanımı oluşturur.
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Veri fabrikasında bağlı bir hizmet oluşturur. Bağlı hizmet bir veri deposunu veya işlem verilerini bir veri fabrikasına bağlar. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Veri fabrikasında bir veri kümesi oluşturur. Bir veri kümesi, bir işlem hattındaki etkinliğin giriş/çıkışını temsil eder. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Veri fabrikasında bir işlem hattı oluşturur. İşlem hattı, belirli bir işlemi gerçekleştiren bir veya daha fazla etkinlik içerir. Bu işlem hattında, bir kopyalama etkinliği verileri bir konumdan Azure Blob depolama alanındaki başka bir konuma kopyalar. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | İşlem hattı için bir çalıştırma oluşturur. Diğer bir deyişle, işlem hattını çalıştırır. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | İşlem hattında etkinliğin (etkinlik çalıştırması) çalıştırılmasıyla ilgili ayrıntıları alır.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).

Ek Azure Data Factory PowerShell betiği örnekleri [Azure Data Factory PowerShell örneklerinde](../samples-powershell.md)bulunabilir.