---
title: Azure depolama hesabının varsayılan erişim katmanını yönetme
description: GPv2 veya blob depolama hesabının varsayılan erişim katmanını değiştirme hakkında bilgi edinin
author: twooley
ms.author: twooley
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 026ab6be1fd4ef79f818f796c4725f6613a9bc6d
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277394"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Azure depolama hesabının varsayılan erişim katmanını yönetme

Her Azure Storage hesabının, sık erişimli veya seyrek erişimli bir varsayılan erişim katmanı vardır. Bir depolama hesabı oluşturduğunuzda erişim katmanını atarsınız. Varsayılan erişim katmanı etkin.

Depolama hesabındaki **erişim katmanı** özniteliğini ayarlayarak varsayılan hesap katmanını değiştirebilirsiniz. Hesap katmanını değiştirmek, hesapta depolanan ve açık katman kümesi olmayan tüm nesneler için geçerlidir. Hesap katmanını, yalnızca GPv2 hesaplarında ayarlanmış bir katman olmadan tüm Bloblar için sık erişimli ve seyrek erişimli bir yazma işlemi (10.000 başına), BLOB depolama ve GPv2 hesaplarındaki tüm Bloblar için hem okuma işlemleri (10.000 başına) hem de veri alma (GB başına) ücretleri olarak değiştirme.

Katman kümesi nesne düzeyinde ayarlanan Bloblar için hesap katmanı uygulanmaz. Arşiv katmanı yalnızca nesne düzeyinde uygulanabilir. İstediğiniz zaman erişim katmanları arasında geçiş yapabilirsiniz.

Aşağıdaki adımları izleyerek, bir depolama hesabı oluşturulduktan sonra varsayılan erişim katmanını değiştirebilirsiniz.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2 veya blob depolama hesabının varsayılan hesap erişim katmanını değiştirme

Aşağıdaki senaryolar Azure portal veya PowerShell kullanır:

# <a name="portal"></a>[Portal](#tab/portal)

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portal, **tüm kaynakları** arayıp seçin.

1. Depolama hesabınızı seçin.

1. **Ayarlar**' da, hesap yapılandırmasını görüntülemek ve değiştirmek için **yapılandırma** ' yı seçin.

1. Gereksinimleriniz için doğru erişim katmanını seçin: **erişim katmanını** seyrek **erişimli veya** **sık** erişimli olarak ayarlayın.

1. En üstteki **Kaydet** ' e tıklayın.

![Azure portal varsayılan hesap katmanını Değiştir](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Hesap katmanını değiştirmek için aşağıdaki PowerShell betiği kullanılabilir. `$rgName`Değişken, kaynak grubu adınızla başlatılmalıdır. `$accountName`Değişken, depolama hesabı adınızla başlatılmalıdır.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Azure depolama hesabındaki bir Blobun katmanını yönetme](../blobs/manage-access-tier.md)
- [Premium performansının uygulamanıza avantajına sahip olup olmadığını belirleme](../blobs/storage-blob-performance-tiers.md)
- [Azure Depolama ölçümlerini etkinleştirerek geçerli depolama hesaplarınızın kullanımını değerlendirme](../blobs/monitor-blob-storage.md)
