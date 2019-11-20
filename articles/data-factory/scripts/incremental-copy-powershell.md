---
title: 'PowerShell betiği: Azure Data Factory kullanarak verileri artımlı olarak yükleme '
description: Bu PowerShell betiği, verileri bir Azure SQL veritabanından Azure Blob depolama alanına artımlı olarak kopyalamak için Azure Data Factory nasıl kullanacağınızı gösterir.
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
ms.openlocfilehash: ef7b00172f0371e09d6462ed357744166b5fb08a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684338"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell betiği-Azure Data Factory kullanarak artımlı olarak veri yükleme
Bu örnek PowerShell betiği, kaynak veri deposundan yalnızca yeni veya güncelleştirilmiş kayıtları, kaynaktan havuza verilerin ilk tam kopyasından sonra bir havuz veri deposuna yükler.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Bu örneği çalıştırmaya yönelik önkoşullar için bkz. [öğretici: artımlı kopya](../tutorial-incremental-copy-powershell.md#prerequisites) . 

## <a name="sample-script"></a>Örnek betik

> [!IMPORTANT]
> Bu betik, c:\ ' daki sabit sürücünüzde Data Factory varlıkları (bağlı hizmet, veri kümesi ve işlem hattı) tanımlayan JSON dosyaları oluşturur. klasörde.

[!code-powershell[main](../../../powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1 "Incremental copy from Azure SQL Database to Azure Blob Storage")]

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
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Veri fabrikasında bağlı bir hizmet oluşturur. Bağlı hizmet bir veri deposunu veya işlem verilerini bir veri fabrikasına bağlar. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Veri fabrikasında bir veri kümesi oluşturur. Bir veri kümesi, bir işlem hattındaki etkinliğin giriş/çıkışını temsil eder. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Veri fabrikasında bir işlem hattı oluşturur. İşlem hattı, belirli bir işlemi gerçekleştiren bir veya daha fazla etkinlik içerir. Bu işlem hattında, bir kopyalama etkinliği verileri bir konumdan Azure Blob depolama alanındaki başka bir konuma kopyalar. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | İşlem hattı için bir çalıştırma oluşturur. Diğer bir deyişle, işlem hattını çalıştırır. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | İşlem hattında etkinliğin (etkinlik çalıştırması) çalıştırılmasıyla ilgili ayrıntıları alır. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](https://docs.microsoft.com/powershell/).

Ek Azure Data Factory PowerShell betiği örnekleri [Azure Data Factory PowerShell betikleri](../samples-powershell.md)içinde bulunabilir.
