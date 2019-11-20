---
title: 'PowerShell betiği: Azure Data Factory kullanarak verileri toplu olarak kopyalama '
description: Bu PowerShell betiği, verileri bir kaynak veri deposundan hedef veri deposuna toplu olarak kopyalamak için Azure Data Factory nasıl kullanacağınızı gösterir.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 8ab373d049e34a6a5528a4e0a3418a3304835d4b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684437"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell betiği-Azure Data Factory kullanarak birden çok tabloyu toplu olarak kopyalama

Bu örnek PowerShell betiği, verileri bir Azure SQL veritabanında bulunan birden çok tablodan Azure SQL veri ambarı 'na kopyalar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Bu örneği çalıştırmaya yönelik önkoşullar için bkz. [öğretici: toplu kopyalama](../tutorial-bulk-copy.md#prerequisites) .

## <a name="sample-script"></a>Örnek betik

> [!IMPORTANT]
> Bu betik, c:\ ' daki sabit sürücünüzde Data Factory varlıkları (bağlı hizmet, veri kümesi ve işlem hattı) tanımlayan JSON dosyaları oluşturur. klasörde.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Veri fabrikası oluşturma. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Veri fabrikasında bağlı bir hizmet oluşturur. Bağlı hizmet bir veri deposunu veya işlem verilerini bir veri fabrikasına bağlar. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Veri fabrikasında bir veri kümesi oluşturur. Bir veri kümesi, bir işlem hattındaki etkinliğin giriş/çıkışını temsil eder. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Veri fabrikasında bir işlem hattı oluşturur. İşlem hattı, belirli bir işlemi gerçekleştiren bir veya daha fazla etkinlik içerir. Bu işlem hattında, bir kopyalama etkinliği verileri bir konumdan Azure Blob depolama alanındaki başka bir konuma kopyalar. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | İşlem hattı için bir çalıştırma oluşturur. Diğer bir deyişle, işlem hattını çalıştırır. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | İşlem hattında etkinliğin (etkinlik çalıştırması) çalıştırılmasıyla ilgili ayrıntıları alır. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](https://docs.microsoft.com/powershell/).

Ek Azure Data Factory PowerShell betiği örnekleri [Azure Data Factory PowerShell betikleri](../samples-powershell.md)içinde bulunabilir.
