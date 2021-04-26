---
title: UI tanımı öğeleri oluşturma
description: Azure portal için Kullanıcı arabirimi tanımları oluştururken kullanılacak öğeleri açıklar.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 3e8bdd85c112d90fd5e10f6e65e48ca6b3f71b6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410751"
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
| name | Yes | Bir öğenin belirli bir örneğine başvurmak için bir iç tanımlayıcı. Öğe adının en yaygın kullanımı, `outputs` belirtilen öğelerin çıkış değerlerinin şablonun parametreleriyle eşlendiği yerdir. Ayrıca, bir öğenin çıkış değerini başka bir öğenin öğesine bağlamak için de kullanabilirsiniz `defaultValue` . |
| tür | Yes | Öğesi için işlenecek Kullanıcı arabirimi denetimi. Desteklenen türlerin listesi için bkz. [öğeleri](#elements). |
| etiket | Yes | Öğenin görüntüleme metni. Bazı öğe türleri birden çok etiket içerir, bu nedenle değer birden fazla dize içeren bir nesne olabilir. |
| Değerinin | No | Öğesinin varsayılan değeri. Bazı öğe türleri karmaşık varsayılan değerleri destekler, bu yüzden değer bir nesne olabilir. |
| Ipucuna | No | Öğenin araç ipucunda görüntülenecek metin. Benzer şekilde `label` , bazı öğeler birden çok araç ipucu dizesini destekler. Satır içi bağlantılar markın sözdizimi kullanılarak gömülebilir.
| kısıtlamaları | No | Öğesinin doğrulama davranışını özelleştirmek için kullanılan bir veya daha fazla özellik. Kısıtlamaların desteklenen özellikleri öğe türüne göre farklılık gösterir. Bazı öğe türleri doğrulama davranışının özelleştirilmesini desteklemez ve bu nedenle kısıtlama özelliğine sahip değildir. |
| seçenekler | No | Öğesinin davranışını özelleştiren ek özellikler. Benzer şekilde `constraints` , desteklenen özellikler öğe türüne göre farklılık gösterir. |
| görüne | No | Öğenin görüntülenip görüntülenmeyeceğini gösterir. İse `true` , öğesi ve ilgili alt öğeleri görüntülenir. `true` varsayılan değerdir. Bu özelliğin değerini dinamik olarak denetlemek için [mantıksal işlevleri](create-uidefinition-functions.md#logical-functions) kullanın.

## <a name="elements"></a>Öğeler

Her bir öğe için belge, Kullanıcı arabirimi örneği, şema, öğe (genellikle doğrulama ve desteklenen özelleştirme ile ilgili) ve örnek çıkış gibi bir kullanıcı arabirimi örneği, şema, açıklamalar içerir.

- [Microsoft. Common. CheckBox](microsoft-common-checkbox.md)
- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft. Common. EditableGrid](microsoft-common-editablegrid.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft. Common. Infobox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. Servicesprincipalselector](microsoft-common-serviceprincipalselector.md)
- [Microsoft. Common. Slider](microsoft-common-slider.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft. Common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft. Keykasası. KeyVaultCertificateSelector](microsoft-keyvault-keyvaultcertificateselector.md)
- [Microsoft. Managedıdentity. ıdentityselector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md)
- [Microsoft. Solutions. ResourceSelector](microsoft-solutions-resourceselector.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)
- [Microsoft. Storage. StorageBlobSelector](microsoft-storage-storageblobselector.md)

## <a name="next-steps"></a>Sonraki adımlar

UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
