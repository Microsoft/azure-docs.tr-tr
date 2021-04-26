---
title: 'Hızlı başlangıç: Azure PowerShell kullanarak SYNAPSE çalışma alanı oluşturma'
description: Bu kılavuzdaki adımları izleyerek Azure PowerShell kullanarak bir Azure SYNAPSE çalışma alanı oluşturun.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1f02833ef7497c34b72db6b858a51c6046bbf3df
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567579"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell Azure SYNAPSE çalışma alanı oluşturma

Azure PowerShell, Azure kaynaklarını doğrudan PowerShell 'den yönetmeye yönelik bir cmdlet kümesidir. Azure Cloud Shell ile kendi tarayıcınızda da kullanabilirsiniz. Bu dosyayı macOS, Linux veya Windows 'a de yükleyebilirsiniz.

Bu hızlı başlangıçta Azure PowerShell kullanarak bir Synapse çalışma alanı oluşturmayı öğreneceksiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- [Azure Data Lake Storage 2. depolama hesabı](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Azure SYNAPSE çalışma alanının seçili ADLS 2. hesabını okuyup yazabilbilmesi gerekir. Birincil depolama hesabı olarak bağlanan herhangi bir depolama hesabı için, depolama hesabı [oluşturma](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account)bölümünde açıklandığı gibi depolama hesabı oluşturulurken **hiyerarşik ad alanını** etkinleştirmeniz gerekir.

Cloud Shell kullanmayı seçerseniz, daha fazla bilgi için bkz. [Azure Cloud Shell Genel Bakış](../cloud-shell/overview.md) .

### <a name="install-the-azure-powershell-module-locally"></a>Azure PowerShell modülünü yerel olarak yükler

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps).

Azure PowerShell ile kimlik doğrulama hakkında daha fazla bilgi için bkz. [Azure PowerShell Ile oturum açma](/powershell/azure/authenticate-azureps).

### <a name="install-the-azure-synapse-powershell-module"></a>Azure SYNAPSE PowerShell modülünü yükler

> [!IMPORTANT]
> **Az. SYNAPSE** PowerShell modülü önizlemedeyken cmdlet 'ini kullanarak ayrı olarak yüklemelisiniz `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduktan sonra, gelecekteki Az PowerShell modülü sürümlerinin bir parçası haline gelecek ve Azure Cloud Shell içinden varsayılan olarak sağlanacaktır.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Azure PowerShell kullanarak bir Azure SYNAPSE çalışma alanı oluşturma

1. Azure SYNAPSE çalışma alanı için kaynak oluşturmak üzere gerekli ortam değişkenlerini tanımlayın.

   |        Değişken adı        |                                                 Description                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | Mevcut ADLS 2. depolama hesabınızın adı.                                                           |
   | StorageAccountResourceGroup | Mevcut ADLS 2. depolama hesabı kaynak grubunuzun adı.                                             |
   | FileShareName               | Mevcut depolama dosya sisteminizin adı.                                                                  |
   | SynapseResourceGroup        | Azure SYNAPSE kaynak grubunuz için yeni bir ad seçin.                                                    |
   | Region                      | [Azure bölgelerinden](https://azure.microsoft.com/global-infrastructure/geographies/#overview)birini seçin. |
   | SynapseWorkspaceName        | Yeni Azure SYNAPSE çalışma alanınız için benzersiz bir ad seçin.                                                  |
   | SqlUser                     | Yeni Kullanıcı adı için bir değer seçin.                                                                          |
   | SQLPassword de belirtilmelidir                 | Güvenli bir parola seçin.                                                                                   |
   | ClientIP                    | PowerShell 'i çalıştırmakta olduğunuz sistemin genel IP adresi.                                             |
   |                             |                                                                                                             |

1. Azure SYNAPSE çalışma alanınız için kapsayıcı olarak bir kaynak grubu oluşturun:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Azure SYNAPSE çalışma alanı oluşturma:

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Azure SYNAPSE çalışma alanı için Web ve dev URL 'sini alın:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Makinenizden Azure SYNAPSE çalışma alanına erişmenize izin vermek için bir güvenlik duvarı kuralı oluşturun:

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. Çalışma alanınıza erişmek için ortam değişkeninde depolanan Azure SYNAPSE Workspace Web URL adresini açın `WorkspaceWeb` :

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure SYNAPSE çalışma alanı Web](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure SYNAPSE çalışma alanını silmek için aşağıdaki adımları izleyin.

> [!WARNING]
> Azure SYNAPSE çalışma alanını silmek, bulunan SQL havuzlarının ve çalışma alanı meta verilerinin veritabanında depolanan analiz altyapılarını ve verileri kaldırır. SQL veya Apache Spark uç noktalarına bağlanmak artık mümkün olmayacaktır. Tüm kod yapıtları silinecek (sorgular, Not defterleri, iş tanımları ve işlem hatları). Çalışma alanını silmek, çalışma alanına bağlı Data Lake Store Gen2 **verileri etkilemez.**

Bu makalede oluşturulan Azure SYNAPSE çalışma alanı gerekmiyorsa, aşağıdaki örneği çalıştırarak silebilirsiniz.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, verilerinizi analiz etmeye ve keşfetmeye başlamak için [SQL havuzları oluşturabilir](quickstart-create-sql-pool-studio.md) veya [Apache Spark havuzları oluşturabilirsiniz](quickstart-create-apache-spark-pool-studio.md) .