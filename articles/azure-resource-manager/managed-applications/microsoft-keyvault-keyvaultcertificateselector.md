---
title: KeyVaultCertificateSelector Kullanıcı arabirimi öğesi
description: Azure portal için Microsoft. Keykasası. KeyVaultCertificateSelector Kullanıcı arabirimi öğesini açıklar.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101230"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Microsoft. Keykasası. KeyVaultCertificateSelector Kullanıcı arabirimi öğesi

Anahtar Kasası sertifikası seçmeye yönelik bir denetim.

## <a name="ui-sample"></a>UI örneği

Kullanıcıya kullanılabilir bir sertifika seçme seçeneği sunulur.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft. Keykasası. KeyVaultCertificateSelector":::

**Sertifika seç**' i seçtikten sonra, Kullanıcı anahtar kasasından bir anahtar kasası ve sertifika belirtebilir.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft. Keykasası. KeyVaultCertificateSelector sertifika seç":::

Denetim, seçili anahtar kasasını ve sertifika adını görüntüleyecek şekilde güncelleştirilir.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft. Keykasası. KeyVaultCertificateSelector seçili sertifikayı göster":::

## <a name="schema"></a>Şema

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
