---
title: Güvenli bağlantıları güvence altına almak için güvenli aktarım gerektir
titleSuffix: Azure Storage
description: Azure depolama 'ya yönelik istekler için güvenli aktarım yapmayı isteme hakkında bilgi edinin. Bir depolama hesabı için güvenli aktarım gerektiğinde, güvenli olmayan bir bağlantıdan kaynaklanan istekler reddedilir.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a0a942daae6b106bce928f8214e92c0c6294203d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555710"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Güvenli bağlantıları güvence altına almak için güvenli aktarım gerektir

Depolama hesabınızı, güvenli bağlantılardan gelen istekleri kabul edecek şekilde yapılandırabilir ve depolama hesabı için **Güvenli aktarım gerekli** özelliğini ayarlayabilirsiniz. Güvenli aktarım gerekli olduğunda, güvensiz bir bağlantıdan kaynaklanan istekler reddedilir. Microsoft, tüm depolama hesaplarınız için her zaman güvenli aktarım gerektirmenizi önerir.

Güvenli aktarım gerekli olduğunda, HTTPS üzerinden Azure depolama REST API işlemi çağrısı yapılmalıdır. HTTP üzerinden yapılan tüm istekler reddedilir.

Depolama hesabı için güvenli aktarım gerektiğinde şifreleme olmadan SMB üzerinden bir Azure dosya paylaşımıyla bağlantı kurma işlemi başarısız olur. Güvenli olmayan bağlantılara örnek olarak, SMB 2,1 üzerinden yapılan, şifrelemeden SMB 3,0 veya Linux SMB istemcisinin bazı sürümleri dahildir.

Varsayılan olarak, bir depolama hesabı oluşturduğunuzda **Güvenli aktarım gerekli** özelliği etkinleştirilir.

> [!NOTE]
> Azure depolama, özel etki alanı adları için HTTPS 'yi desteklemediğinden, özel bir etki alanı adı kullandığınızda bu seçenek uygulanmaz. Ve klasik depolama hesapları desteklenmez.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Azure portal için güvenli aktarım gerektir

[Azure Portal](https://portal.azure.com)bir depolama hesabı oluşturduğunuzda **Güvenli aktarım gerekli** özelliğini açabilirsiniz. Ayrıca, var olan depolama hesapları için etkinleştirebilirsiniz.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Yeni bir depolama hesabı için güvenli aktarım gerektir

1. Azure portal **depolama hesabı oluştur** bölmesini açın.
1. **Güvenli aktarım gerekli** altında, **etkin**' i seçin.

   ![Depolama hesabı oluştur dikey penceresi](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Var olan bir depolama hesabı için güvenli aktarım gerektir

1. Azure portal var olan bir depolama hesabını seçin.
1. Depolama hesabı menü bölmesinde, **Ayarlar**' ın altında **yapılandırma**' yı seçin.
1. **Güvenli aktarım gerekli** altında, **etkin**' i seçin.

   ![Depolama hesabı menü bölmesi](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Koddan güvenli aktarım gerektir

Programlı olarak güvenli aktarım gerektirmek için, depolama hesabında _enableHttpsTrafficOnly_ özelliğini _true_ olarak ayarlayın. Bu özelliği, depolama kaynak sağlayıcısı REST API, istemci kitaplıkları veya araçları kullanarak ayarlayabilirsiniz:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/@azure/arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK'sı](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>PowerShell ile güvenli aktarım gerektir

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu örnek Azure PowerShell modülünü az sürüm 0,7 veya üstünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-Az-ps).

Azure ile bağlantı oluşturmak için `Connect-AzAccount` komutunu çalıştırın.

 Ayarı denetlemek için aşağıdaki komut satırını kullanın:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Ayarı etkinleştirmek için aşağıdaki komut satırını kullanın:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Azure CLı ile güvenli aktarım gerektir

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Ayarı denetlemek için aşağıdaki komutu kullanın:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Ayarı etkinleştirmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Sonraki adımlar

[BLOB depolama için güvenlik önerileri](../blobs/security-recommendations.md)
