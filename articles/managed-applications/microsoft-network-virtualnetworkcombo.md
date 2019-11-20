---
title: Azure VirtualNetworkCombo UI öğesi | Microsoft Docs
description: Azure portal için Microsoft. Network. VirtualNetworkCombo UI öğesini açıklar.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: d325230f603be4ccfe4fe42f1b58c6ad892fdb2c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151471"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft. Network. VirtualNetworkCombo UI öğesi

Yeni veya var olan bir sanal ağı seçmek için bir denetim grubu.

## <a name="ui-sample"></a>UI örneği

Kullanıcı yeni bir sanal ağ seçtiğinde, Kullanıcı her bir alt ağın adını ve adres önekini özelleştirebilir. Alt ağları yapılandırma isteğe bağlıdır.

![Microsoft. Network. VirtualNetworkCombo yeni](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Kullanıcı var olan bir sanal ağı seçtiğinde, dağıtım şablonu için gereken her bir alt ağı eşleştirmelidir. Bu durumda alt ağların yapılandırılması gerekir.

![Microsoft. Network. VirtualNetworkCombo var](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Açıklamalar

- Belirtilmişse, `defaultValue.addressPrefixSize` boyutunun ilk çakışmayan adres ön eki, kullanıcının aboneliğindeki var olan sanal ağlara göre otomatik olarak belirlenir.
- `defaultValue.name` ve `defaultValue.addressPrefixSize` için varsayılan değer **null**.
- `constraints.minAddressPrefixSize` belirtilmelidir. Belirtilen değerden daha küçük bir adres alanına sahip var olan sanal ağlar seçim için kullanılamaz.
- `subnets` belirtilmelidir ve her alt ağ için `constraints.minAddressPrefixSize` belirtilmelidir.
- Yeni bir sanal ağ oluştururken, her alt ağın adres öneki sanal ağın adres ön ekine ve ilgili `addressPrefixSize`göre otomatik olarak hesaplanır.
- Mevcut bir sanal ağ kullanılırken, ilgili `constraints.minAddressPrefixSize` küçük alt ağlar seçim için kullanılamaz. Ayrıca, belirtilmişse, en az `minAddressCount` kullanılabilir adrese sahip olmayan alt ağlar seçim için kullanılamaz. Varsayılan değer **0**' dır. Kullanılabilir adreslerin bitişik olduğundan emin olmak için `requireContiguousAddresses`için **true değerini** belirtin. Varsayılan değer **true**'dur.
- Mevcut bir sanal ağda alt ağların oluşturulması desteklenmez.
- `options.hideExisting` **true**ise, Kullanıcı var olan bir sanal ağı seçemiyorum. Varsayılan değer **false**.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
