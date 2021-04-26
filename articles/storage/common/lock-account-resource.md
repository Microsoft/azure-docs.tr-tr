---
title: Depolama hesabına Azure Resource Manager kilidi uygulama
titleSuffix: Azure Storage
description: Bir depolama hesabına Azure Resource Manager kilidi uygulamayı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 79b88ad58a2eb95a48a140b3b98d606af495cb94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799794"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Depolama hesabına Azure Resource Manager kilidi uygulama

Microsoft, depolama hesabının yanlışlıkla veya kötü amaçlı olarak silinmesini engellemek için tüm depolama hesaplarınızı Azure Resource Manager kilidi ile kilitlemenizi önerir. İki tür Azure Resource Manager kaynak kilidi vardır:

- **Cannotdelete** kilidi, kullanıcıların bir depolama hesabını silmesini engeller, ancak yapılandırmasını okuyup değiştirmeye izin verir.
- **Salt okunur** kilit, kullanıcıların bir depolama hesabını silmesini veya yapılandırmasını değiştirmesini önler, ancak yapılandırmanın okunmasına izin verir.

Azure Resource Manager kilitleri hakkında daha fazla bilgi için bkz. [değişiklikleri engellemek için kaynakları kilitleme](../../azure-resource-manager/management/lock-resources.md).

> [!CAUTION]
> Bir depolama hesabının kilitlenmesi, bu hesabın içindeki kapsayıcıların veya Blobların silinmesini veya üzerine yazılmasını engellemez. Blob verilerini koruma hakkında daha fazla bilgi için bkz. [veri korumasına genel bakış](../blobs/data-protection-overview.md).

## <a name="configure-an-azure-resource-manager-lock"></a>Azure Resource Manager kilidi yapılandırma

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal bir depolama hesabında bir kilit yapılandırmak için aşağıdaki adımları izleyin:

1. Azure portalda depolama hesabınıza gidin.
1. **Ayarlar** bölümünde, **kilitler**' ı seçin.
1. **Add (Ekle)** seçeneğini belirleyin.
1. Kaynak kilidi için bir ad girin ve kilit türünü belirtin. İsterseniz kilit hakkında bir bilgi ekleyin.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Bir depolama hesabını CannotDelete kilidi ile kilitlemeyi gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell ile bir depolama hesabında bir kilit yapılandırmak için, önce [az PowerShell modülünü](https://www.powershellgallery.com/packages/Az)yüklediğinizden emin olun. Ardından, [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) komutunu çağırın ve aşağıdaki örnekte gösterildiği gibi oluşturmak istediğiniz kilit türünü belirtin:

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile bir depolama hesabında bir kilit yapılandırmak için, [az Lock Create](/cli/azure/lock#az_lock_create) komutunu çağırın ve aşağıdaki örnekte gösterildiği gibi oluşturmak istediğiniz kilit türünü belirtin:

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Salt okunur bir kilit etkin olduğunda veri işlemlerini yetkilendirme

Bir depolama hesabına **salt okunur** bir kilit uygulandığında, bu depolama hesabı Için [liste anahtarları](/rest/api/storagerp/storageaccounts/listkeys) işlemi engellenir. **Anahtar Listele** IŞLEMI BIR https Post işlemidir ve hesap Için **salt okunur** BIR kilit yapılandırıldığında tüm post işlemleri engellenir. **Anahtarları Listele** işlemi, daha sonra depolama hesabındaki herhangi bir veriyi okumak ve bu verilere yazmak için kullanılabilen hesap erişim anahtarlarını döndürür.

Bir istemci, kilitleme depolama hesabına uygulandığı sırada hesap erişim anahtarlarına sahip ise, bu istemci verilere erişmek için anahtarları kullanmaya devam edebilir. Ancak, anahtarlara erişimi olmayan istemcilerin depolama hesabındaki blob veya kuyruk verilerine erişmesi için Azure Active Directory (Azure AD) kimlik bilgilerini kullanması gerekir.

Azure portal kullanıcıları, bir **salt okunur** kilit uygulandığında, hesap erişim anahtarlarıyla portalda blob veya kuyruk verilerine daha önce erişiliyorsa etkilenmiş olabilir. Kilit uygulandıktan sonra Portal kullanıcılarının, portalda blob veya kuyruk verilerine erişmek için Azure AD kimlik bilgilerini kullanması gerekir. Bunu yapmak için, bir kullanıcıya en az iki RBAC rolü atanmış olmalıdır: en düşük Azure Resource Manager okuyucu rolü ve Azure Storage veri erişim rollerinden biridir. Daha fazla bilgi için aşağıdaki makalelerden birine bakın:

- [Azure portal blob verilerine erişim yetkisi verme seçeneğini belirleyin](../blobs/authorize-data-operations-portal.md)
- [Azure portal kuyruk verilerine erişim yetkisi verme seçeneğini belirleyin](../queues/authorize-data-operations-portal.md)

Azure dosyalarındaki veya tablo hizmetindeki veriler, daha önce hesap anahtarlarına erişen istemciler tarafından erişilebilir hale gelebilir. En iyi uygulama olarak, bir depolama hesabına **salt okunur** bir kilit uygulamanız gerekiyorsa Azure dosya ve tablo hizmeti iş yüklerinizi **salt okunur** bir kilit ile kilitlemez bir depolama hesabına taşıyın.

## <a name="next-steps"></a>Sonraki adımlar

- [Veri korumaya genel bakış](../blobs/data-protection-overview.md)
- [Değişiklikleri engellemek için kaynakları kilitle](../../azure-resource-manager/management/lock-resources.md)
