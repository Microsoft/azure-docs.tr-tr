---
title: Azure PowerShell veya Kaynak Yöneticisi şablonları kullanarak VM'ler için Azure İzleyici (Önizleme) etkinleştirme | Microsoft Docs
description: Bu makalede, Azure PowerShell veya Azure Resource Manager şablonları kullanarak bir veya daha fazla Azure sanal makinesi veya sanal makine ölçek kümesi için VM'ler için Azure İzleyici nasıl etkinleştirileceği açıklanır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/14/2019
ms.openlocfilehash: 25e9848124b84822749d3be4d1ead7b86e6e584e
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091765"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Azure PowerShell veya Kaynak Yöneticisi şablonları kullanarak VM'ler için Azure İzleyici (Önizleme) etkinleştirme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu makalede, Azure PowerShell veya Azure Resource Manager şablonları kullanılarak Azure sanal makineler veya sanal makine ölçek kümeleri için VM'ler için Azure İzleyici (Önizleme) nasıl etkinleştirileceği açıklanır. Bu işlemin sonunda, tüm sanal makinelerinizi izlemeye başarıyla başlacaksınız ve herhangi bir performans veya kullanılabilirlik sorunu yaşadığını öğrenirsiniz.

## <a name="set-up-a-log-analytics-workspace"></a>Bir Log Analytics çalışma alanını ayarlama 

Log Analytics çalışma alanınız yoksa bir tane oluşturmanız gerekir. Yapılandırma adımlarına devam etmeden önce [Önkoşullar](vminsights-enable-overview.md#log-analytics) bölümünde önerilen yöntemleri gözden geçirin. Daha sonra, Azure Resource Manager şablonu yöntemini kullanarak VM'ler için Azure İzleyici dağıtımını tamamlayaseçebilirsiniz.

### <a name="enable-performance-counters"></a>Performans sayaçları sağlar

Çözüm tarafından başvurulan Log Analytics çalışma alanı zaten çözüm için gerekli performans sayaçları toplamak için yapılandırılmamışsa, bunları etkinleştirmeniz gerekir. Bunu iki şekilde yapabilirsiniz:
* İçinde açıklandığı şekilde el ile [Log analytics'te Windows ve Linux performans veri kaynakları](../../azure-monitor/platform/data-sources-performance-counters.md)
* [Azure PowerShell galerisinde](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1) bulunan bir PowerShell betiğini indirip çalıştırarak

### <a name="install-the-servicemap-solution"></a>ServiceMap çözümünü yükler

Bu yöntem, Log Analytics çalışma alanınızda çözüm bileşenlerini etkinleştirmek için yapılandırmasını belirten bir JSON şablonu içerir.

Bir şablon kullanarak kaynakların nasıl dağıtılacağını bilmiyorsanız, bkz:
* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/resource-group-template-deploy.md)
* [Kaynakları Resource Manager şablonları ve Azure CLI ile dağıtma](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLı 'yi kullanmak için, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLI Sürüm 2.0.27 çalıştırıyor olmanız gerekir veya üzeri. Sürümünüzü belirlemek için çalıştırma `az --version`. Azure CLı 'yı yüklemek veya yükseltmek için bkz. [Azure CLI 'Yi yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Aşağıdaki JSON söz dizimini kopyalayıp dosyanıza yapıştırın:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Bu dosyayı farklı Kaydet *installsolutionsforvminsights.json* yerel bir klasöre.

1. *Çalışmaadı*, *Resourcegroupname*ve *WorkspaceLocation*değerlerini yakalayın. *Çalışmaadı* değeri, Log Analytics çalışma alanınızın adıdır. Değeri *WorkspaceLocation* çalışma alanı içinde tanımlanan bölgedir.

1. Bu şablonu dağıtmaya hazırsınız.
 
    * Şablonu içeren klasörde aşağıdaki PowerShell komutlarını kullanın:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. İşiniz bittiğinde, aşağıdakine benzer ve sonucu içeren bir ileti görüntülenir:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Azure CLI kullanarak aşağıdaki komutu çalıştırmak için:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Bu tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonuç şunu içerir:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarıyla etkinleştir

Sanal makinelerinizi ve sanal makine ölçek kümelerinizi eklemeye yönelik örnek Azure Resource Manager şablonlar oluşturduk. Bu şablonlar, var olan bir kaynakta izlemeyi etkinleştirmek ve izleme özelliği etkin olan yeni bir kaynak oluşturmak için kullanabileceğiniz senaryolar içerir.

>[!NOTE]
>Şablonun, panoda kullanılacak kaynakla aynı kaynak grubunda dağıtılması gerekir.

Bir şablon kullanarak kaynakların nasıl dağıtılacağını bilmiyorsanız, bkz:
* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/resource-group-template-deploy.md)
* [Kaynakları Resource Manager şablonları ve Azure CLI ile dağıtma](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLı 'yi kullanmak için, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLI Sürüm 2.0.27 çalıştırıyor olmanız gerekir veya üzeri. Sürümünüzü belirlemek için çalıştırma `az --version`. Azure CLı 'yı yüklemek veya yükseltmek için bkz. [Azure CLI 'Yi yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Şablonları indir

Azure Resource Manager şablonları, GitHub deponuzdan [indirebileceğiniz](https://aka.ms/VmInsightsARMTemplates) bir arşiv dosyasında (. zip) verilmiştir. Dosya içeriği, bir şablon ve parametre dosyası ile her bir dağıtım senaryosunu temsil eden klasörleri içerir. Çalıştırmadan önce, parametreler dosyasını değiştirin ve gerekli değerleri belirtin. Şablon dosyasını, özel gereksinimlerinizi destekleyecek şekilde özelleştirmeniz gerekmedikçe değiştirmeyin. Parametre dosyasını değiştirdikten sonra, bu makalenin ilerleyen kısımlarında açıklanan aşağıdaki yöntemleri kullanarak dağıtabilirsiniz. 

İndirme dosyası farklı senaryolar için aşağıdaki şablonları içerir:

- **Existingvmontaslak** şablonu, sanal makine zaten varsa VM'ler için Azure izleyici etkinleştirilir.
- **Newvmontaslak** şablonu, bir sanal makine oluşturur ve VM'ler için Azure izleyici bunu izlemesini sağlar.
- **ExistingVmssOnboarding** şablonu, sanal makine ölçek kümesi zaten varsa VM'ler için Azure izleyici etkinleştirilir.
- **NewVmssOnboarding** şablonu, sanal makine ölçek kümeleri oluşturur ve VM'ler için Azure İzleyici bunları izlemesini sağlar.
- **Configureworkspace** şablonu, Linux ve Windows işletim sistemi performans sayaçlarının çözümlerini ve toplanmasını etkinleştirerek Log Analytics çalışma alanınızı VM'ler için Azure izleyici destekleyecek şekilde yapılandırır.

>[!NOTE]
>Sanal Makine Ölçek Kümeleri zaten varsa ve yükseltme ilkesi **el ile**olarak ayarlandıysa, **ExistingVmssOnboarding** Azure Resource Manager şablonu çalıştırıldıktan sonra örnekler için VM'ler için Azure izleyici varsayılan olarak etkinleştirilmez. Örnekleri el ile yükseltmeniz gerekir.

### <a name="deploy-by-using-azure-powershell"></a>Azure PowerShell'i kullanarak dağıtma

Aşağıdaki adım Azure PowerShell kullanarak izlemeye izin vermez.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. İşiniz bittiğinde, aşağıdakine benzer ve sonucu içeren bir ileti görüntülenir:

```powershell
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Azure CLı kullanarak dağıtma

Aşağıdaki adım Azure CLı kullanarak izlemeye izin vermez.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Çıktı aşağıdakine benzer:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>PowerShell ile etkinleştirme

Birden çok VM veya sanal makine ölçek kümesi için VM'ler için Azure İzleyici etkinleştirmek için, [Install-VMInsights. ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)PowerShell betiğini kullanın. Azure PowerShell Galerisi ' nden kullanılabilir. Bu betik şu şekilde yinelenir:

- Aboneliğinizdeki her sanal makine ve sanal makine ölçek kümesi.
- *ResourceGroup*tarafından belirtilen kapsamlı kaynak grubu. 
- *Ad*ile belirtilen tek bir VM veya sanal makine ölçek kümesi.

Her sanal makine veya sanal makine ölçek kümesi için betik VM uzantısı zaten yüklü olup olmadığını doğrular. VM Uzantısı yüklüyse, betik yeniden yüklemeyi dener. VM uzantısı yüklü değilse, betik Log Analytics ve bağımlılık Aracısı VM uzantılarını yüklenir.

`Enable-AzureRM` uyumluluk diğer adları etkinken Azure PowerShell Module az Version 1.0.0 veya üzeri kullandığınızı doğrulayın. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](https://docs.microsoft.com/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız, aynı zamanda çalıştırmak ihtiyacınız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için.

Betik bağımsız değişkeni ayrıntıları ve örnek kullanım listesini almak için çalıştırın `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

Aşağıdaki örnek, VM'ler için Azure İzleyici etkinleştirmek ve beklenen çıktıyı anlamak için klasörde PowerShell komutlarını kullanarak göstermektedir:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Sonraki adımlar

Artık sanal makineleriniz için izleme etkin olduğuna göre, bu bilgiler VM'ler için Azure İzleyici analiz için kullanılabilir.
 
- Bulunan Uygulama bağımlılıklarını görüntülemek için bkz: [Vm'leri harita görünümü Azure İzleyici](vminsights-maps.md). 

- VM performanlarınızın performans sorunlarını ve genel kullanımını belirlemek için bkz. [Azure VM performansını görüntüleme](vminsights-performance.md). 
