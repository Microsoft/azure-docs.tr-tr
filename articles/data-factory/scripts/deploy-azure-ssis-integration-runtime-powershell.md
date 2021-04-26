---
title: PowerShell kullanarak Azure SSIS tümleştirme çalışma zamanı dağıtma
description: Bu PowerShell betiği, bulutta SSIS paketlerini çalıştırabilirler bir Azure-SSIS tümleştirme çalışma zamanı oluşturur.
ms.service: data-factory
ms.topic: article
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 158e2db5261ce35713e1564cd24f0dd1d563ab78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370215"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell betiği-Azure-SSIS tümleştirme çalışma zamanını dağıtma

Bu örnek PowerShell betiği, Azure 'da SSIS paketlerinizi çalıştırabilirler bir Azure-SSIS tümleştirme çalışma zamanı oluşturur.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

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
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Bulutta SSIS paketlerini çalıştırabilirler bir Azure-SSIS tümleştirme çalışma zamanı oluşturur |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Azure-SSIS tümleştirme çalışma zamanını başlatır. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Azure-SSIS tümleştirme çalışma zamanı hakkında bilgi alır. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).

Ek Azure Data Factory PowerShell betiği örnekleri [Azure Data Factory PowerShell örneklerinde](../samples-powershell.md)bulunabilir.