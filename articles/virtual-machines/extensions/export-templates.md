---
title: VM uzantıları içeren Azure kaynak gruplarını dışarı aktarma
description: Sanal makine uzantılarını içeren Kaynak Yöneticisi şablonlarını dışarı aktarın.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073119"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>VM uzantılarını içeren kaynak gruplarını dışarı aktarma

Azure Kaynak grupları, daha sonra yeniden dağıtılabilecek yeni bir Kaynak Yöneticisi şablona aktarılabilir. Dışarı aktarma işlemi var olan kaynakları Yorumlar ve dağıtılan sonuçları benzer bir kaynak grubunda elde eden bir Kaynak Yöneticisi şablonu oluşturur. Kaynak grubu dışa aktarma seçeneğini, sanal makine uzantılarını içeren bir kaynak grubuna karşı kullanırken, uzantı uyumluluğu ve korumalı ayarlar gibi bazı öğelerin göz önünde bulundurulmaları gerekir.

Bu belgede, desteklenen uzantıların listesi ve güvenli verileri işleme ayrıntıları dahil olmak üzere, kaynak grubu dışarı aktarma işleminin sanal makine uzantılarıyla ilgili olarak nasıl çalıştığı açıklanır.

## <a name="supported-virtual-machine-extensions"></a>Desteklenen sanal makine uzantıları

Birçok sanal makine uzantısı mevcuttur. Tüm uzantılar, "Otomasyon betiği" özelliği kullanılarak bir Kaynak Yöneticisi şablonuna verilemez. Bir sanal makine uzantısı desteklenmiyorsa, el ile verilen şablona geri yerleştirilmesi gerekir.

Aşağıdaki uzantılar Otomasyon betiği özelliği ile birlikte aktarılabilir.

| Dahili numara ||||
|---|---|---|---|
| Acronis Yedekleme | Dataköpek Windows Aracısı | Linux Için işletim sistemi düzeltme eki uygulama | VM anlık görüntüsü Linux
| Acronis Yedekleme Linux | Docker uzantısı | Pupevcil hayvan Aracısı |
| Bg bilgileri | DSC Uzantısı | Site 7/24 APM öngörüleri |
| BMC CTı Aracısı Linux | DynaTrace Linux | Site 7/24 Linux sunucusu |
| BMC CTı aracı pencereleri | DynaTrace pencereleri | Site 7/24 Windows Server |
| Chef Istemcisi | HPE güvenlik uygulaması Defender | Eğilim mikro DSA |
| Özel Betik | IaaS kötü amaçlı yazılımdan koruma | Eğilim mikro DSA Linux |
| Özel Betik Uzantısı | IaaS tanılaması | Linux Için VM erişimi |
| Linux için özel betik | Linux Chef Istemcisi | Linux Için VM erişimi |
| Dataköpek Linux Aracısı | Linux tanısı | Sanal Makine Anlık Görüntüsü |

## <a name="export-the-resource-group"></a>Kaynak grubunu dışarı aktarma

Bir kaynak grubunu yeniden kullanılabilir bir şablona dışarı aktarmak için aşağıdaki adımları izleyin:

1. Azure portalında oturum açın
2. Hub menüsünde kaynak grupları ' na tıklayın.
3. Listeden hedef kaynak grubunu seçin
4. Kaynak grubu dikey penceresinde Otomasyon betiği ' ne tıklayın.

![Şablon dışarı aktarma](./media/export-templates/template-export.png)

Azure Resource Manager akışlarını otomatikleştirin betiği, bir kaynak yöneticisi şablonu, bir parametre dosyası ve PowerShell ve Azure CLI gibi birkaç örnek dağıtım betiği üretir. Bu noktada, içe aktarılmış şablon, şablon kitaplığına yeni bir şablon olarak eklenen veya Dağıt düğmesi kullanılarak yeniden dağıtılan indirme düğmesi kullanılarak indirilebilir.

## <a name="configure-protected-settings"></a>Korumalı ayarları Yapılandır

Birçok Azure sanal makine uzantısı, kimlik bilgileri ve yapılandırma dizeleri gibi hassas verileri şifreleyen bir korumalı ayarlar yapılandırması içerir. Korumalı ayarlar Otomasyon betiğine aktarılmaz. Gerekirse, korumalı ayarların, oluşturulan şablonlu öğesine yeniden eklenmesi gerekir.

### <a name="step-1---remove-template-parameter"></a>1\. adım-şablon parametresini kaldır

Kaynak grubu verildiğinde,, dışarıya aktarılmış korumalı ayarlara bir değer sağlamak için tek bir şablon parametresi oluşturulur. Bu parametre kaldırılabilir. Parametreyi kaldırmak için parametre listesini inceleyin ve bu JSON örneğine benzeyen parametreyi silin.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>2\. adım-korumalı ayarların özelliklerini al

Korunan her ayarın gerekli özellikler kümesi olduğundan, bu özelliklerin bir listesinin toplanması gerekir. Korumalı ayarlar yapılandırmasının her parametresi, [GitHub 'daki Azure Resource Manager şemasında](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json)bulunabilir. Bu şema yalnızca bu belgenin genel bakış bölümünde listelenen uzantıların parametre kümelerini içerir. 

Şema deposu içinden, istenen uzantıyı arayın, bu örnekte `IaaSDiagnostics`. Uzantılar `protectedSettings` nesne bulduktan sonra her bir parametreye göz atın. `IaasDiagnostic` uzantısının örneğinde, gerekli parametreler `storageAccountName`, `storageAccountKey`ve `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>3\. adım-korumalı yapılandırmayı yeniden oluşturma

İçe aktarılmış şablonda, `protectedSettings` için arama yapın ve bu korumalı ayar nesnesini, gerekli uzantı parametrelerini ve her biri için bir değeri içeren yeni bir ile değiştirin.

`IaasDiagnostic` uzantısının örneğinde, yeni korumalı ayar yapılandırması aşağıdaki örnekteki gibi görünür:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Son uzantı kaynağı aşağıdaki JSON örneğine benzer şekilde görünür:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Özellik değerleri sağlamak için şablon parametreleri kullanılıyorsa, bunların oluşturulması gerekir. Korumalı ayar değerleri için şablon parametreleri oluştururken, hassas değerlerin güvenliğini sağlamak için `SecureString` parametre türünü kullandığınızdan emin olun. Parametreleri kullanma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma](../../resource-group-authoring-templates.md).

`IaasDiagnostic` uzantısının örneğinde, Kaynak Yöneticisi şablonunun parametreler bölümünde aşağıdaki parametreler oluşturulur.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

Bu noktada, şablon herhangi bir şablon dağıtım yöntemi kullanılarak dağıtılabilir.
