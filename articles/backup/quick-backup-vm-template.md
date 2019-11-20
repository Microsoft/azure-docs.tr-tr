---
title: Hızlı başlangıç-Kaynak Yöneticisi şablonu VM yedeklemesi
description: Azure Resource Manager şablonuyla sanal makinelerinizi nasıl yedekleyeceğinizi öğrenin
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc
ms.openlocfilehash: 7218b496a3cd94362d27b1883fa5055b819768c3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171928"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Azure 'da Kaynak Yöneticisi şablonuyla bir sanal makineyi yedekleme

[Azure Backup](backup-overview.md) , şirket içi makineleri ve uygulamaları ve Azure VM 'lerini yedekler. Bu makalede, bir Azure VM 'yi Kaynak Yöneticisi şablonuyla ve Azure PowerShell nasıl yedekleyeceğiniz gösterilmektedir. Bu hızlı başlangıç, bir kurtarma hizmetleri Kasası oluşturmak için bir Kaynak Yöneticisi şablonu dağıtma işlemine odaklanmaktadır. Kaynak Yöneticisi şablonları geliştirme hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi belgeleri](/azure/azure-resource-manager/) ve [şablon başvurusu](/azure/templates/microsoft.recoveryservices/allversions).

Alternatif olarak, [Azure PowerShell](./quick-backup-vm-powershell.md), [Azure CLI](quick-backup-vm-cli.md)veya [Azure Portal](quick-backup-vm-portal.md)kullanarak bir VM 'yi yedekleyebilirsiniz.

## <a name="create-a-vm-and-recovery-services-vault"></a>VM ve kurtarma hizmetleri Kasası oluşturma

[Kurtarma Hizmetleri Kasası](backup-azure-recovery-services-vault-overview.md) , Azure VM 'leri gibi korunan kaynaklar için yedekleme verilerini depolayan bir mantıksal kapsayıcıdır. Bir yedekleme işi çalıştırıldığında, kurtarma hizmetleri kasasının içinde bir kurtarma noktası oluşturur. Daha sonra bu kurtarma noktalarından birini kullanarak verileri dilediğiniz zaman geri yükleyebilirsiniz.

Bu hızlı başlangıçta kullanılan şablon [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Bu şablon, koruma için DefaultPolicy ile yapılandırılmış basit Windows VM ve kurtarma hizmetleri kasasını dağıtmanıza olanak tanır.

Şablonu dağıtmak için **dene** ' yi seçerek Azure Cloud Shell 'i açın ve sonra aşağıdaki PowerShell betiğini kabuk penceresine yapıştırın. Kodu yapıştırmak için kabuk penceresine sağ tıklayıp **Yapıştır**' ı seçin.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Azure PowerShell, bu hızlı başlangıçta Kaynak Yöneticisi şablonunu dağıtmak için kullanılır. [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md), [Azure CLı](../azure-resource-manager/resource-group-template-deploy-cli.md)ve [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md) , şablon dağıtmak için de kullanılabilir.

## <a name="start-a-backup-job"></a>Bir yedekleme işi başlatma

Şablon bir VM oluşturur ve VM 'yi yeniden sunar. Şablonu dağıttıktan sonra bir yedekleme işi başlatmanız gerekir. Daha fazla bilgi için bkz. [bir yedekleme Işi başlatma](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Yedekleme işini izleme

Yedekleme işini izlemek için bkz. [yedekleme Işini izleme](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Dağıtımı temizle

Artık VM 'yi yedeklemeniz gerekmiyorsa, temizleyebilirsiniz.

- VM 'yi geri yüklemeyi denemek istiyorsanız, temizleme işlemini atlayın.
- Var olan bir VM kullandıysanız, kaynak grubunu ve VM 'yi yerinde bırakmak için son [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 'ini atlayabilirsiniz.

Korumayı devre dışı bırakın, geri yükleme noktalarını ve kasasını kaldırın. Daha sonra kaynak grubunu ve ilişkili VM kaynaklarını aşağıdaki gibi silin:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Kurtarma Hizmetleri kasası oluşturdunuz, bir sanal makine için koruma özelliklerini etkinleştirdiniz ve ilk kurtarma noktasını oluşturdunuz.

- Azure portal VM 'Leri [nasıl yedekleyeceğinizi öğrenin](tutorial-backup-vm-at-scale.md) .
- Bir VM 'yi hızlıca geri yüklemeyi [öğrenin](tutorial-restore-disk.md)
