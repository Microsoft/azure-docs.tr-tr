---
title: Şablonlarda Kullanıcı tanımlı işlevler
description: Bir Azure Resource Manager şablonunda (ARM şablonu) Kullanıcı tanımlı işlevlerin nasıl tanımlanacağını ve kullanılacağını açıklar.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 802ac6e1c70db8caa58aecde3efe814430c79682
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307784"
---
# <a name="user-defined-functions-in-arm-template"></a>ARM şablonunda Kullanıcı tanımlı işlevler

Şablonunuz içinde kendi işlevlerinizi oluşturabilirsiniz. Bu işlevler, şablonunuzda kullanıma sunulmuştur. Kullanıcı tanımlı işlevler, şablonunuzda otomatik olarak kullanılabilen [Standart şablon işlevlerinden](template-functions.md) ayrıdır. Şablonunuzda tekrar tekrar kullanılan karmaşık deyimleriniz varsa kendi işlevlerinizi oluşturun.

Bu makalede, Azure Resource Manager şablonunuzda (ARM şablonu) Kullanıcı tanımlı işlevlerin nasıl ekleneceği açıklanmaktadır.

## <a name="define-the-function"></a>İşlevi tanımlayın

İşlevleriniz, şablon işlevleriyle adlandırma çakışmalarını önlemek için bir ad alanı değeri gerektirir. Aşağıdaki örnek, benzersiz bir ad döndüren bir işlevi göstermektedir:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>İşlevini kullanma

Aşağıdaki örnek, bir depolama hesabı için benzersiz bir ad almak üzere Kullanıcı tanımlı bir işlev içeren bir şablonu gösterir. Şablon, `storageNamePrefix` işlevine parametre olarak geçirilen adlı bir parametreye sahiptir.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

Dağıtım sırasında `storageNamePrefix` parametresi işlevine geçirilir:

* Şablon adlı bir parametreyi tanımlar `storageNamePrefix` .
* İşlevi `namePrefix` yalnızca işlevinde tanımlanmış parametreleri kullanabileceğiniz için kullanır. Daha fazla bilgi için bkz. [sınırlamalar](#limitations).
* Şablonun `resources` bölümünde `name` öğesi işlevini kullanır ve `storageNamePrefix` değeri işleve geçirir `namePrefix` .

## <a name="limitations"></a>Sınırlamalar

Bir Kullanıcı işlevi tanımlarken bazı kısıtlamalar vardır:

* İşlev değişkenlere erişemez.
* İşlevi yalnızca işlevinde tanımlanan parametreleri kullanabilir. Kullanıcı tanımlı bir işlev içinde [Parameters](template-functions-deployment.md#parameters) işlevini kullandığınızda, bu işlevin parametreleriyle sınırlandırılırsınız.
* İşlev, Kullanıcı tanımlı diğer işlevleri çağıramaz.
* İşlev, [başvuru](template-functions-resource.md#reference) işlevini veya [liste](template-functions-resource.md#list) işlevlerinden hiçbirini kullanamaz.
* İşlev [Datetimeadd](template-functions-date.md#datetimeadd) işlevini kullanamaz.
* İşlevin parametreleri varsayılan değerlere sahip olamaz.

## <a name="next-steps"></a>Sonraki adımlar

* Kullanıcı tanımlı işlevlerin kullanılabilir özellikleri hakkında bilgi edinmek için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
* Kullanılabilir şablon işlevlerinin bir listesi için bkz. [ARM şablon işlevleri](template-functions.md).
