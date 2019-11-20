---
title: Azure PowerShell kullanarak Log Analytics çalışma alanı oluştur | Microsoft Docs
description: Bulut ve şirket içi ortamlarınızdaki Azure PowerShell yönetim çözümlerini ve veri toplamayı etkinleştirmek için Log Analytics çalışma alanı oluşturmayı öğrenin.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.openlocfilehash: d9ac472c320767919301f5de634fd5158e824726
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900524"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Azure PowerShell Log Analytics çalışma alanı oluşturma

Azure PowerShell modülü, PowerShell komut satırından veya betik içinden Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta, Azure Izleyici 'de bir Log Analytics çalışma alanı dağıtmak için Azure PowerShell modülünün nasıl kullanılacağı gösterilmektedir. Log Analytics çalışma alanı, Azure Izleyici günlük verileri için benzersiz bir ortamdır. Her çalışma alanının kendi veri deposu ve yapılandırması vardır ve veri kaynakları ve çözümleri verilerini belirli bir çalışma alanında depolamak üzere yapılandırılır. Aşağıdaki kaynaklardan veri toplamayı planlıyorsanız bir Log Analytics çalışma alanı gerekir:

* Aboneliğinizdeki Azure kaynakları  
* System Center Operations Manager tarafından izlenen şirket içi bilgisayarlar  
* System Center Configuration Manager cihaz koleksiyonları  
* Azure depolama biriminden tanılama veya günlük verileri  
 
Ortamınızdaki Azure VM 'Leri ve Windows veya Linux VM 'Leri gibi diğer kaynaklar için aşağıdaki konulara bakın:

* [Azure sanal makinelerinden veri toplama](../learn/quick-collect-azurevm.md)
* [Karma Linux bilgisayarından veri topla](../learn/quick-collect-linux-computer.md)
* [Karma Windows bilgisayarından veri topla](quick-collect-windows-computer.md)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici Azure PowerShell az modülünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma
[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)ile bir çalışma alanı oluşturun. Aşağıdaki örnek, yerel makinenizden Kaynak Yöneticisi şablonu kullanarak *eastus* konumunda bir çalışma alanı oluşturur. JSON şablonu yalnızca size çalışma alanının adını isteyecek şekilde yapılandırılmıştır ve ortamınızda standart bir yapılandırma olarak kullanılabilecek diğer parametreler için varsayılan bir değer belirtir. 

Desteklenen bölgeler hakkında daha fazla bilgi için, bkz. [Log Analytics bölgeler kullanılabilir](https://azure.microsoft.com/regions/services/) ve **bir ürün Için aramadan** Azure izleyici araması yapın. 

Aşağıdaki parametreler varsayılan bir değer ayarlar:

* konum-varsayılan olarak Doğu ABD
* SKU-Nisan 2018 fiyatlandırma modelinde yayınlanan yeni GB başına fiyatlandırma katmanına varsayılan olarak sahiptir

>[!WARNING]
>Yeni Nisan 2018 fiyatlandırma modelini kabul eden bir abonelikte Log Analytics çalışma alanı oluşturuyor veya yapılandırıyorsanız, geçerli Log Analytics fiyatlandırma katmanı yalnızca **PerGB2018**olur. 
>

### <a name="create-and-deploy-template"></a>Şablon oluşturma ve dağıtma

1. Aşağıdaki JSON söz dizimini kopyalayıp dosyanıza yapıştırın:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Gereksinimlerinizi karşılayacak şekilde şablonu düzenleyin. Hangi özelliklerin ve değerlerin desteklendiğini öğrenmek için [Microsoft. Operationalınsights/Workspaces şablon](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) başvurusunu gözden geçirin. 
3. Bu dosyayı bir yerel klasöre **deploylaworkspace Template. JSON** olarak kaydedin.   
4. Bu şablonu dağıtmaya hazırsınız. Şablonu içeren klasörden aşağıdaki komutları kullanın. Bir çalışma alanı adı sorulduğunda, tüm Azure abonelikleri genelinde genel olarak benzersiz bir ad sağlayın.

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Dağıtımın tamamlanması birkaç dakika sürebilir. Tamamlandığında, sonucu içeren aşağıdakine benzer bir ileti görürsünüz:

![Dağıtım tamamlandığında örnek sonuç](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Sonraki adımlar
Artık kullanılabilir bir çalışma alanınız olduğuna göre, izleme telemetri koleksiyonunu yapılandırabilir, bu verileri çözümlemek için günlük aramalarını çalıştırabilir ve ek veri ve analitik Öngörüler sağlamak için bir yönetim çözümü ekleyebilirsiniz.  

* Azure Tanılama veya Azure depolama ile Azure kaynaklarından veri toplamayı etkinleştirmek için bkz. Azure [izleyici 'de Kullanım Için Azure hizmet günlükleri ve ölçümleri toplama](../platform/collect-azure-metrics-logs.md).  
* Operations Manager yönetim grubunuzu raporlayan aracılardan veri toplamak ve Log Analytics çalışma alanınızda depolamak için [veri kaynağı olarak System Center Operations Manager](../platform/om-agents.md) ekleyin.  
* Hiyerarşideki koleksiyonların üyesi olan bilgisayarları içeri aktarmak için [Configuration Manager](../platform/collect-sccm.md) bağlanın.  
* Kullanılabilir [izleme çözümlerini](../insights/solutions.md) ve çalışma alanınızdan bir çözümün nasıl ekleneceğini ve kaldırılacağını gözden geçirin.
