---
title: Azure IoT DPS IP bağlantı filtreleri | Microsoft Docs
description: Belirli IP adreslerinden Azure IoT DPS örneğinize gelen bağlantıları engellemek için IP filtrelemeyi kullanma. Tek tek IP adreslerinden veya IP adresi aralıklarından gelen bağlantıları engelleyebilirsiniz.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: e1b175a176255da465433b2db45cb3cb67d360d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934493"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Azure IoT DPS IP bağlantı filtrelerini kullanma

Güvenlik tüm IoT çözümlerinde önemli bir konudur. Bazen güvenlik yapılandırmanız kapsamında cihazların hangi IP adreslerinden bağlanabileceğini açıkça belirtmeniz gerekir. Azure IoT Hub Cihazı Sağlama Hizmeti'nin (DPS) *IP filtresi* özelliği, belirli IPv4 adreslerinden gelen trafiği reddetmek veya kabul etmek için kurallar yapılandırmanıza olanak tanır.

## <a name="when-to-use"></a>Kullanılması gereken durumlar

Belirli IP adreslerinden DPS uç noktasına bağlantıları engellemenin yararlı olduğu iki belirgin kullanım örneği vardır:

* DPS'niz yalnızca belirtilen IP adresi aralığından gelen trafiği almalı ve diğer her şeyi reddetmelidir. Örneğin DPS ile cihazlarınız arasında özel bağlantılar oluşturmak üzere DPS'nizi [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) ile kullanıyor olabilirsiniz.

* DPS yöneticisinin şüpheli olarak tanımladığı IP adreslerinden gelen trafiği reddetmeniz gerekir.

## <a name="how-filter-rules-are-applied"></a>Filtre kuralları nasıl uygulanır?

IP filtresi kuralları DPS örneği düzeyinde uygulanır. Bu nedenle IP filtresi kuralları desteklenen bir protokol kullanılarak cihazlardan ve arka uç uygulamalarından gelen tüm bağlantılara uygulanır.

DPS örneğinizde IP reddetme kuralıyla eşleşen bir IP adresinden gelen tüm bağlantı girişimleri bir yetkisiz 401 durum kodu ve açıklaması alır. Yanıt iletisinde IP kuralı belirtilmez.

## <a name="default-setting"></a>Varsayılan ayar

Varsayılan olarak DPS için portaldaki **IP Filtresi** kılavuzu boştur. Bu varsayılan ayar, DPS'nizin tüm IP adreslerinden gelen bağlantıları kabul ettiği anlamına gelir. Bu varsayılan ayar 0.0.0.0/0 IP adres aralığını kabul eden kuralla eşdeğerdir.

![IoT DPS varsayılan IP filtresi ayarları](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>IP filtresi kuralını ekleme veya düzenleme

IP filtresi kuralı eklemek için **+ IP Filtresi Kuralı Ekle**'yi seçin.

![IoT DPS'ye IP filtresi kuralı ekleme](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

**IP Filtresi Kuralı Ekle**'yi seçtikten sonra alanları doldurun.

![IP Filtresi Kuralı Ekle'yi seçtikten sonra](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* IP Filtresi kuralı için bir **ad** girin. Benzersiz, büyük-küçük harfe duyarlı olmayan alfasayısal bir dize olmalı ve en fazla 128 karakterden oluşmalıdır. Yalnızca ASCII 7-bit alfasayısal karakterler ile `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` karakterleri kabul edilir.

* Tek IPv4 adresi veya CIDR gösteriminde bir IP adresleri bloğu belirtin. Örneğin CIDR gösteriminde 192.168.100.0/22, 192.168.100.0 ile 192.168.103.255 arasındaki 1024 IPv4 adresini temsil eder.

* IP filtresi kuralının **eylemi** olarak **İzin Ver** veya **Engelle**'yi seçin.

Alanları doldurduktan sonra **Kaydet**'i seçerek kuralı kaydedin. Güncelleştirmenin devam ettiğini bildiren bir uyarı görürsünüz.

![IP filtresi kuralını kaydetme bildirimi](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

En fazla 100 IP filtresi kuralına ulaştığınızda **Ekle** seçeneği devre dışıdır.

Mevcut kuralı düzenlemek için, değiştirmek istediğiniz verileri seçin, değişikliği yapın ve sonra da **Kaydet**'i seçerek düzenlemenizi kaydedin.

> [!NOTE]
> IP adreslerinin reddedilmesi diğer Azure Hizmetlerinin DPS örneğiyle etkileşim kurmasını engelleyebilir.

## <a name="delete-an-ip-filter-rule"></a>IP filtresi kuralını silme

IP filtresi kuralını silmek için, söz konusu satırdaki çöp kutusu simgesini seçin ve sonra da **Kaydet**'i seçin. Kural kaldırılır ve değişiklik kaydedilir.

![IoT DPS IP filtresi kuralını silme](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Kodda IP filtresi kurallarını güncelleştirme

Azure kaynak sağlayıcısının REST uç noktasını kullanarak DPS IP filtrenizi alabilir ve değiştirebilirsiniz. [createorupdate yöntemi](/rest/api/iot-dps/iotdpsresource/createorupdate) altında `properties.ipFilterRules` bölümüne bakın.

Şu anda DPS IP filtresi kurallarını Azure CLI veya Azure PowerShell ile güncelleştirme desteği yoktur ama Azure Resource Manager şablonlarıyla güncelleştirilebilir. Resource Manager şablonlarını kullanma yönergeleri için bkz. [Azure Resource Manager şablonları](../azure-resource-manager/templates/overview.md). Aşağıda verilen şablon örneklerinde DPS IP filtresi kurallarını oluşturma, düzenleme ve silme işlemleri gösterilir.

### <a name="add-an-ip-filter-rule"></a>IP filtresi kuralı ekleme

Aşağıdaki şablon örneği tüm trafiği kabul eden "AllowAll" adlı yeni bir IP filtresi kuralı oluşturur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Şablonun IP filtresi kuralı özniteliklerini gereksinimlerinize göre güncelleştirin.

| Öznitelik                | Açıklama |
| ------------------------ | ----------- |
| **FilterName**           | IP Filtresi kuralı için bir ad girin. Benzersiz, büyük-küçük harfe duyarlı olmayan alfasayısal bir dize olmalı ve en fazla 128 karakterden oluşmalıdır. Yalnızca ASCII 7 bit alfasayısal karakterler ve kabul edilir  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`   . |
| **Eylem**               | IP filtresi kuralının eylemi olarak kabul edilen değerler **Accept** veya  **Reject** 'tir. |
| **ipMask**               | Tek IPv4 adresi veya CIDR gösteriminde bir IP adresleri bloğu belirtin. Örneğin CIDR gösteriminde 192.168.100.0/22, 192.168.100.0 ile 192.168.103.255 arasındaki 1024 IPv4 adresini temsil eder. |


### <a name="update-an-ip-filter-rule"></a>IP filtresi kuralını güncelleştirme

Aşağıdaki şablon örneği daha önce gösterilen "AllowAll" adlı IP filtresi kuralını tüm trafiği reddedecek şekilde güncelleştirir.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>IP filtresi kuralını silme

Aşağıdaki şablon örneği DPS örneği için tüm IP filtresi kurallarını siler.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>IP filtresi kuralı değerlendirmesi

IP filtresi kuralları sırayla uygulanır ve kabul etme veya reddetme eylemini IP adresiyle eşleşen ilk kural belirler.

Örneğin 192.168.100.0/22 aralığındaki adresleri kabul etmek ve diğer her şeyi reddetmek istiyorsanız, kılavuzdaki ilk kural 192.168.100.0/22 adres aralığını kabul etmelidir. Sonraki kural 0.0.0.0/0 aralığını kullanarak tüm adresleri reddetmelidir.

Kılavuzda satırın başındaki üç dikey noktaya tıklayarak ve sürükleyip bırakmayı kullanarak IP filtresi kurallarınızın sırasını değiştirebilirsiniz.

IP filtresi kurallarınızın yeni sırasını kaydetmek için **Kaydet**'e tıklayın.

![DPS IP filtresi kurallarınızın sırasını değiştirme](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Sonraki adımlar

DPS yönetimini daha fazla incelemek için bkz:

* [IoT DPS IP adreslerini anlama](iot-dps-understand-ip-address.md)
* [Azure CLı kullanarak DPS ayarlama](quick-setup-auto-provision-cli.md)
* [DPS'ye erişimi denetleme](how-to-control-access.md)
