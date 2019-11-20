---
title: Azure Kullanıcı arabirimi tanım öğesi oluştur | Microsoft Docs
description: Azure portal için Kullanıcı arabirimi tanımları oluştururken kullanılacak öğeleri açıklar.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 9f952b8301f1d85d81fcc63e5d46dc57b1fb1106
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932000"
---
# <a name="createuidefinition-elements"></a>Createuıdefinition öğeleri

Bu makalede bir Createuıdefinition öğesinin tüm desteklenen öğelerinin şeması ve özellikleri açıklanmaktadır. 

## <a name="schema"></a>Şema

Çoğu öğenin şeması aşağıdaki gibidir:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Özellik | Gerekli | Açıklama |
| -------- | -------- | ----------- |
| ad | Yes | Bir öğenin belirli bir örneğine başvurmak için bir iç tanımlayıcı. Öğe adının en yaygın kullanımı, belirtilen öğelerin çıkış değerlerinin şablonun parametreleriyle eşlendiği `outputs`. Ayrıca, bir öğenin çıkış değerini başka bir öğenin `defaultValue` bağlamak için de kullanabilirsiniz. |
| type | Yes | Öğesi için işlenecek Kullanıcı arabirimi denetimi. Desteklenen türlerin listesi için bkz. [öğeleri](#elements). |
| etiket | Yes | Öğenin görüntüleme metni. Bazı öğe türleri birden çok etiket içerir, bu nedenle değer birden fazla dize içeren bir nesne olabilir. |
| defaultValue | Hayır | Öğesinin varsayılan değeri. Bazı öğe türleri karmaşık varsayılan değerleri destekler, bu yüzden değer bir nesne olabilir. |
| Ipucuna | Hayır | Öğenin araç ipucunda görüntülenecek metin. `label`benzer şekilde, bazı öğeler birden çok araç ipucu dizesini destekler. Satır içi bağlantılar markın sözdizimi kullanılarak gömülebilir.
| kısıtlamaları | Hayır | Öğesinin doğrulama davranışını özelleştirmek için kullanılan bir veya daha fazla özellik. Kısıtlamaların desteklenen özellikleri öğe türüne göre farklılık gösterir. Bazı öğe türleri doğrulama davranışının özelleştirilmesini desteklemez ve bu nedenle kısıtlama özelliğine sahip değildir. |
| Seçenekler | Hayır | Öğesinin davranışını özelleştiren ek özellikler. `constraints`benzer şekilde, desteklenen özellikler öğe türüne göre farklılık gösterir. |
| Görüne | Hayır | Öğenin görüntülenip görüntülenmeyeceğini gösterir. `true`, öğe ve ilgili alt öğeler görüntülenir. Varsayılan değer `true`. Bu özelliğin değerini dinamik olarak denetlemek için [mantıksal işlevleri](create-uidefinition-functions.md#logical-functions) kullanın.

## <a name="elements"></a>Öğeler

Her bir öğe için belge, Kullanıcı arabirimi örneği, şema, öğe (genellikle doğrulama ve desteklenen özelleştirme ile ilgili) ve örnek çıkış gibi bir kullanıcı arabirimi örneği, şema, açıklamalar içerir.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft. Common. Infobox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft. Common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Sonraki adımlar

UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
