---
title: StorageBlobSelector Kullanıcı arabirimi öğesi
description: Azure portal için Microsoft. Storage. StorageBlobSelector Kullanıcı arabirimi öğesini açıklar.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754620"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Microsoft. Storage. StorageBlobSelector Kullanıcı arabirimi öğesi

Bir Azure depolama hesabından blob seçmeye yönelik bir denetim.

## <a name="ui-sample"></a>UI örneği

Kullanıcıya, kullanılabilir depolama bloblarına gözatmaya yönelik seçenek sunulur.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft. Storage. StorageBlobSelector-zat":::

**Gözden** geçirme seçildikten sonra Kullanıcı bir depolama hesabı seçebilir.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft. Storage. StorageBlobSelector-depolamayı seçin":::

Kullanıcı, kapsayıcıyı depolama hesabında görür ve birini seçebilir.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft. Storage. StorageBlobSelector-kapsayıcı seçin":::

Kullanıcı, kapsayıcıda bir dosya seçebilir.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft. Storage. StorageBlobSelector-dosya":::

Denetim seçili dosya adını görüntüleyecek şekilde güncelleştirilir.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft. Storage. StorageBlobSelector-seçme dosyasını göster":::

## <a name="schema"></a>Şema

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>Açıklamalar

**Kısıtlamalar. allowedFileExtensions** özelliği izin verilen dosya türlerini belirtir.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
