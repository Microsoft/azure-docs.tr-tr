---
title: Yeniden Izleme Azure Linux aracı uzantısını stackbelirt
description: Bir Linux sanal makinesine Stackbelirt yeniden Izleme Linux Aracısı dağıtın.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 04/12/2018
ms.openlocfilehash: 1fc437637fde524da125af9191bf9de79a2e9c37
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559010"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Yeniden Izleme Linux Aracısı uzantısını stackbelirt

## <a name="overview"></a>Genel Bakış

Stackbelirt, sorunları hızla bulmaya ve gidermeye yardımcı olmak üzere uygulamanız hakkındaki ayrıntıları izleyen ürünler sağlar. Geliştirici ekipleri için, yeniden Izleme tamamen tümleşik, çok ortamlı, uygulama performansı süper güçü. Her geliştirme ekibinin ihtiyacı olan çeşitli araçları birleştirir.

Yeniden izleme özelliği, tüm ortamlarda aşağıdaki tüm özellikleri tek bir platformda sunan tek araçtır.

* Uygulama performans yönetimi (APM)
* Uygulama ve sunucu günlüğü
* Hata izleme ve izleme
* Sunucu, uygulama ve özel ölçümler

**Stackbelirt Linux Aracısı uzantısı hakkında**

Bu uzantı, yeniden Izleme için Linux Aracısı için bir install yolu sağlar. 

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi 

Retrace Aracısı bu Linux dağıtımlarına karşı çalıştırılabilir

| Dağıtım | Sürüm |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS, 16,10 ve 17,04 |
| Debian | 7.9 + ve 8.2 +, 9 |
| Red Hat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>İnternet bağlantısı

Linux için Stacktarget aracı uzantısı, hedef sanal makinenin Internet 'e bağlı olmasını gerektirir. 

Stackbir bağlantı kurulmasına izin vermek için ağ yapılandırmanızı ayarlamanız gerekebilir, bkz https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall .. 


## <a name="extension-schema"></a>Uzantı şeması

---

Aşağıdaki JSON, Stackbelirt retrace aracı uzantısının şemasını gösterir. Uzantı, ve gerektirir `environment` `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Şablon dağıtımı 

Azure VM uzantıları, Azure Resource Manager şablonlarıyla dağıtılabilir. Önceki bölümde ayrıntılı JSON şeması, bir Azure Resource Manager şablon dağıtımı sırasında Stackbelirt Linux Aracısı uzantısını çalıştırmak için bir Azure Resource Manager şablonunda kullanılabilir.  

Bir sanal makine uzantısı için JSON, sanal makine kaynağının içinde iç içe veya Kaynak Yöneticisi JSON şablonunun kök veya üst düzeyine yerleştirilmiş olabilir. JSON yerleştirmesi, kaynak adının ve türün değerini etkiler. Daha fazla bilgi için bkz. alt kaynaklar için ad ve tür ayarlama.

Aşağıdaki örnek, Stackbir retrace Linux uzantısının sanal makine kaynağının içinde iç içe olduğunu varsayar. Uzantı kaynağını yuvalama sırasında JSON, sanal makinenin "resources": [] nesnesine yerleştirilir.

Uzantı, ve gerektirir `environment` `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

JSON uzantısını şablon köküne yerleştirirken, kaynak adı üst sanal makineye bir başvuru içerir ve tür iç içe geçmiş yapılandırmayı yansıtır.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell dağıtımı

`Set-AzVMExtension`Komut, mevcut bir sanal makineye Stackbelirt yeniden Izleme Linux Aracısı sanal makinesi uzantısını dağıtmak için kullanılabilir. Komutu çalıştırmadan önce, ortak ve özel yapılandırmaların bir PowerShell karma tablosunda depolanması gerekir.

Uzantı, ve gerektirir `environment` `activationKey` .

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı 

Azure CLı Aracı, Stackbir retrace Linux Aracısı sanal makinesi uzantısını var olan bir sanal makineye dağıtmak için kullanılabilir.  

Uzantı, ve gerektirir `environment` `activationKey` .

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

### <a name="error-codes"></a>Hata kodları

| Hata kodu | Anlamı | Olası eylem |
| :---: | --- | --- |
| 10 | Yüklemesi hatası | wget gereklidir |
| 20 | Yüklemesi hatası | Python gereklidir |
| 30 | Yüklemesi hatası | sudo gereklidir |
| 40 | Yüklemesi hatası | activationKey gereklidir |
| 51 | Yüklemesi hatası | İşletim sistemi tarafından kapatma desteklenmiyor |
| 60 | Yüklemesi hatası | ortam gereklidir |
| 70 | Yüklemesi hatası | Bilinmiyor |
| 80 | Etkinleştirme hatası | Hizmet kurulumu başarısız oldu |
| 90 | Etkinleştirme hatası | Hizmet başlatılamadı |
| 100 | Hatayı devre dışı bırak | Hizmet durdurulamadı |
| 110 | Hatayı devre dışı bırak | Hizmet kaldırılamadı |
| 120 | Kaldırma hatası | Hizmet durdurulamadı |

Daha fazla yardıma ihtiyacınız varsa, ' de Stackbelirt desteğine başvurabilirsiniz https://support.stackify.com .