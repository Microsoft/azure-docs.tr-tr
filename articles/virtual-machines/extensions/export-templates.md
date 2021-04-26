---
title: VM uzantıları içeren Azure kaynak gruplarını dışarı aktarma
description: Sanal makine uzantılarını içeren Kaynak Yöneticisi şablonlarını dışarı aktarın.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 12/05/2016
ms.openlocfilehash: df1ae43b2c6a74448a6782a43fb86f8f4939b13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560013"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>VM uzantılarını içeren kaynak gruplarını dışarı aktarma

Azure Kaynak grupları, daha sonra yeniden dağıtılabilecek yeni bir Kaynak Yöneticisi şablona aktarılabilir. Dışarı aktarma işlemi var olan kaynakları Yorumlar ve dağıtılan sonuçları benzer bir kaynak grubunda elde eden bir Kaynak Yöneticisi şablonu oluşturur. Kaynak grubu dışa aktarma seçeneğini, sanal makine uzantılarını içeren bir kaynak grubuna karşı kullanırken, uzantı uyumluluğu ve korumalı ayarlar gibi bazı öğelerin göz önünde bulundurulmaları gerekir.

Bu belgede, desteklenen uzantıların listesi ve güvenli verileri işleme ayrıntıları dahil olmak üzere, kaynak grubu dışarı aktarma işleminin sanal makine uzantılarıyla ilgili olarak nasıl çalıştığı açıklanır.

## <a name="supported-virtual-machine-extensions"></a>Desteklenen sanal makine uzantıları

Birçok sanal makine uzantısı mevcuttur. Tüm uzantılar, "Otomasyon betiği" özelliği kullanılarak bir Kaynak Yöneticisi şablonuna verilemez. Bir sanal makine uzantısı desteklenmiyorsa, el ile verilen şablona geri yerleştirilmesi gerekir.

Aşağıdaki uzantılar Otomasyon betiği özelliği ile birlikte aktarılabilir.

> Acronis Backup, Acronis Backup Linux, bg Info, BMC CTı aracı Linux, BMC CTı aracı Windows, Chef Istemcisi, Özel Betik, Özel Betik uzantısı, Linux için özel betik, Dataköpek Linux Aracısı, Dataköpek Windows Aracısı, Docker uzantısı, DSC Uzantısı, dynaTrace Linux, dynaTrace Windows, HPE güvenlik uygulaması Defender, IaaS antimalware, IaaS Diagnostics, Linux Chef Client, Linux Tanılama, Linux Için düzeltme eki uygulama, Pupevcil hayvan, site 7/24 APM Insight , Site 7/24 Linux sunucusu, site 7/24 Windows Server, eğilim mikro DSA, eğilim mikro DSA Linux, Linux Için VM erişimi, Linux için VM erişimi, VM anlık görüntüsü, VM Snapshot Linux

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

### <a name="step-1---remove-template-parameter"></a>1. adım-şablon parametresini kaldır

Kaynak grubu verildiğinde,, dışarıya aktarılmış korumalı ayarlara bir değer sağlamak için tek bir şablon parametresi oluşturulur. Bu parametre kaldırılabilir. Parametreyi kaldırmak için parametre listesini inceleyin ve bu JSON örneğine benzeyen parametreyi silin.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>2. adım-korumalı ayarların özelliklerini al

Korunan her ayarın gerekli özellikler kümesi olduğundan, bu özelliklerin bir listesinin toplanması gerekir. Korumalı ayarlar yapılandırmasının her parametresi, [GitHub 'daki Azure Resource Manager şemasında](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json)bulunabilir. Bu şema yalnızca bu belgenin genel bakış bölümünde listelenen uzantıların parametre kümelerini içerir. 

Şema deposu içinden, bu örnek için istenen uzantıyı arayın `IaaSDiagnostics` . Uzantılar nesnesi olduktan sonra `protectedSettings` her bir parametreye göz atın. Uzantı örneğinde, `IaasDiagnostic` gerektir parametreleri, ve ' dir `storageAccountName` `storageAccountKey` `storageAccountEndPoint` .

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

### <a name="step-3---re-create-the-protected-configuration"></a>3. adım-korumalı yapılandırmayı yeniden oluşturma

İçe aktarılmış şablonda, için ' i arayın `protectedSettings` ve bu korumalı ayar nesnesini, gerekli uzantı parametrelerini ve her biri için bir değeri içeren yeni bir ile değiştirin.

`IaasDiagnostic`Uzantı örneğinde, yeni korumalı ayar yapılandırması aşağıdaki örnekteki gibi görünür:

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

Özellik değerleri sağlamak için şablon parametreleri kullanılıyorsa, bunların oluşturulması gerekir. Korumalı ayar değerleri için şablon parametreleri oluştururken, `SecureString` hassas değerlerin güvenliğini sağlamak için parametre türünü kullandığınızdan emin olun. Parametreleri kullanma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma](../../azure-resource-manager/templates/template-syntax.md).

`IaasDiagnostic`Uzantı örneğinde, Kaynak Yöneticisi şablonunun parametreler bölümünde aşağıdaki parametreler oluşturulur.

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
