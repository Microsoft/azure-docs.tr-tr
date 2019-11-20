---
title: 'PowerShell betiği: Azure Data Factory kullanarak bulutta veri kopyalama '
description: Bu PowerShell betiği, verileri bir Azure Blob depolama alanındaki bir konumdan aynı BLOB depolama alanındaki başka bir konuma kopyalar.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: jingwang
ms.openlocfilehash: 7ff9bd39a6a2d9785009d0f8bd8cb4c46fc09882
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684374"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Verileri bulutta kopyalamak için bir Data Factory işlem hattı oluşturmak için PowerShell 'i kullanma

Bu örnek PowerShell betiği, verileri bir konumdan Azure Blob depolama alanındaki başka bir konuma kopyalayan Azure Data Factory bir işlem hattı oluşturur.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Ön koşullar
* **Azure Depolama hesabı**. Blob depolama alanını hem **kaynak** hem de **havuz** veri deposu olarak kullanabilirsiniz. Azure depolama hesabınız yoksa oluşturma bilgileri için bkz. [Depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md). 
* Blob Depolama içinde bir **blob kapsayıcısı** oluşturun, kapsayıcıda bir giriş **klasörü** oluşturun ve bazı dosyaları klasöre yükleyin. [Azure Depolama gezgini](https://azure.microsoft.com/features/storage-explorer/) gibi araçları kullanarak Azure Blob depolama hesabına bağlanabilir, bir blob kapsayıcısı oluşturabilir, giriş dosyasını karşıya yükleyebilir ve çıktı dosyasını doğrulayabilirsiniz.

## <a name="sample-script"></a>Örnek betik

> [!IMPORTANT]
> Bu betik, c:\ ' daki sabit sürücünüzde Data Factory varlıkları (bağlı hizmet, veri kümesi ve işlem hattı) tanımlayan JSON dosyaları oluşturur. klasörde.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


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

Ek Azure Data Factory PowerShell betiği örnekleri [Azure Data Factory PowerShell örneklerinde](../samples-powershell.md)bulunabilir.
