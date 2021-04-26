---
title: Hesap erişim anahtarlarını yönetme
titleSuffix: Azure Storage
description: Depolama hesabı erişim anahtarlarınızı görüntüleme, yönetme ve döndürme hakkında bilgi edinin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 82d272f22295a5b68d1e8de3fb5a70c45d4c14a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791220"
---
# <a name="manage-storage-account-access-keys"></a>Depolama hesabı erişim anahtarlarını yönetme

Bir depolama hesabı oluşturduğunuzda, Azure 2 512 bit depolama hesabı erişim anahtarları oluşturur. Bu anahtarlar, paylaşılan anahtar yetkilendirmesi aracılığıyla Depolama hesabınızdaki verilere erişim yetkisi vermek için kullanılabilir.

Microsoft, erişim anahtarlarınızı yönetmek için Azure Key Vault kullanmanızı ve anahtarlarınızı düzenli olarak döndürmenizi ve yeniden oluşturmanızı önerir. Azure Key Vault kullanmak, anahtarlarınızı uygulamalarınıza kesinti olmadan döndürmeyi kolaylaştırır. Ayrıca, anahtarlarınızı el ile de döndürebilirsiniz.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Hesap erişim anahtarlarını görüntüle

Hesap erişim anahtarlarınızı Azure portal, PowerShell veya Azure CLı ile görüntüleyebilir ve kopyalayabilirsiniz. Azure portal, depolama hesabınız için kopyalayabilmeniz için bir bağlantı dizesi de sağlar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Depolama hesabı erişim anahtarlarınızı veya Bağlantı dizenizi Azure portal görüntülemek ve kopyalamak için:

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.
1. **Ayarlar**’ın altında **Erişim anahtarları**’nı seçin. Hesap erişim anahtarlarınız ve her bir anahtar için tam bağlantı dizesi görüntülenir.
1. **KEY1** altındaki **anahtar** değerini bulun ve **Kopyala** düğmesine tıklayarak hesap anahtarını kopyalayın.
1. Alternatif olarak, tüm bağlantı dizesini kopyalayabilirsiniz. **key1** bölümünde **Bağlantı dizesi** değerini bulun ve **Kopyala** düğmesine tıklayarak bağlantı dizesini kopyalayın.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Azure portal erişim tuşlarının nasıl görüntüleneceğini gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Hesap erişim anahtarlarınızı PowerShell ile almak için [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) komutunu çağırın.

Aşağıdaki örnek ilk anahtarı alır. İkinci anahtarı almak için `Value[1]` yerine kullanın `Value[0]` . Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Hesap erişim anahtarlarınızı Azure CLı ile listelemek için, aşağıdaki örnekte gösterildiği gibi [az Storage Account Keys List](/cli/azure/storage/account/keys#az_storage_account_keys_list) komutunu çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Azure depolama 'ya erişmek için iki anahtardan birini kullanabilirsiniz, ancak genel olarak ilk anahtarı kullanmak iyi bir uygulamadır ve anahtarları döndürürken ikinci anahtarın kullanımını ayırabilirsiniz.

Hesabın erişim anahtarlarını görüntülemek veya okumak için, kullanıcının bir hizmet yöneticisi olması ya da **Microsoft. Storage/storageAccounts/ListKeys/Action** Içeren bir Azure rolü atanması gerekir. Bu eylemi içeren bazı Azure yerleşik rolleri, **sahip**, **katkıda** bulunan ve **depolama hesabı anahtar operatörü hizmeti rolü** rolleridir. Hizmet Yöneticisi rolü hakkında daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md). Azure depolama için yerleşik roller hakkında ayrıntılı bilgi için Azure [RBAC Için Azure yerleşik rollerinin](../../role-based-access-control/built-in-roles.md#storage) **depolama** bölümüne bakın.

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Erişim anahtarlarınızı yönetmek için Azure Key Vault kullanın

Microsoft, erişim anahtarlarınızı yönetmek ve döndürmek için Azure Key Vault kullanmayı önerir. Uygulamanız Key Vault içindeki Anahtarlarınıza güvenli bir şekilde erişebilir, böylece bunları uygulama kodunuzla depolamaktan kaçınabilirsiniz. Anahtar yönetimi için Key Vault kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure Key Vault ve PowerShell ile depolama hesabı anahtarlarını yönetme](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Azure Key Vault ve Azure CLı ile depolama hesabı anahtarlarını yönetme](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Erişim tuşlarını el ile döndürme

Microsoft, depolama hesabınızı güvende tutmaya yardımcı olmak için erişim anahtarlarınızı düzenli aralıklarla döndürmenizi önerir. Mümkünse, erişim anahtarlarınızı yönetmek için Azure Key Vault kullanın. Key Vault kullanmıyorsanız, anahtarlarınızı el ile döndürmeniz gerekir.

Anahtarlarınızı döndürebilmeniz için iki erişim tuşu atanır. İki anahtara sahip olmak, uygulamanızın işlem boyunca Azure depolama 'ya erişimini korumasını sağlar.

> [!WARNING]
> Erişim anahtarlarınızın yeniden oluşturulması, depolama hesabı anahtarına bağlı olan tüm uygulamaları veya Azure hizmetlerini etkileyebilir. Depolama hesabına erişmek için hesap anahtarını kullanan istemciler, Media Services, bulut, masaüstü ve mobil uygulamalar gibi yeni anahtarı kullanacak şekilde ve [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)gibi Azure depolama için grafik kullanıcı arabirimi uygulamalarına yönelik olarak güncelleştirilmeleri gerekir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Depolama hesabı erişim anahtarlarınızı Azure portal döndürmek için:

1. Depolama hesabı için ikincil erişim anahtarına başvurmak üzere uygulama kodunuzda bağlantı dizelerini güncelleştirin.
1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.
1. **Ayarlar**’ın altında **Erişim anahtarları**’nı seçin.
1. Depolama hesabınız için birincil erişim anahtarını yeniden oluşturmak üzere birincil erişim anahtarı ' nın yanındaki **Oluştur** düğmesini seçin.
1. Yeni birincil erişim anahtarını referans olarak kullanmak için bağlantı dizelerini güncelleştirin.
1. İkincil erişim anahtarını da aynı şekilde yeniden oluşturun.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Depolama hesabı erişim anahtarlarınızı PowerShell ile döndürmek için:

1. Depolama hesabı için ikincil erişim anahtarına başvurmak üzere uygulama kodunuzda bağlantı dizelerini güncelleştirin.
1. Aşağıdaki örnekte gösterildiği gibi, birincil erişim anahtarını yeniden oluşturmak için [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) komutunu çağırın:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Yeni birincil erişim anahtarını referans olarak kullanmak için bağlantı dizelerini güncelleştirin.
1. İkincil erişim anahtarını da aynı şekilde yeniden oluşturun. İkincil anahtarı yeniden oluşturmak için `key2` yerine anahtar adı olarak kullanın `key1` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Depolama hesabı erişim anahtarlarınızı Azure CLı ile döndürmek için:

1. Depolama hesabı için ikincil erişim anahtarına başvurmak üzere uygulama kodunuzda bağlantı dizelerini güncelleştirin.
1. Aşağıdaki örnekte gösterildiği gibi, birincil erişim anahtarını yeniden oluşturmak için [az Storage Account Keys Renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) komutunu çağırın:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Yeni birincil erişim anahtarını referans olarak kullanmak için bağlantı dizelerini güncelleştirin.
1. İkincil erişim anahtarını da aynı şekilde yeniden oluşturun. İkincil anahtarı yeniden oluşturmak için `key2` yerine anahtar adı olarak kullanın `key1` .

---

> [!NOTE]
> Microsoft, tüm uygulamalarınızda aynı anda yalnızca bir tane anahtar kullanılmasını önerir. Anahtar 1 ' i bazı yerlerde ve anahtar 2 ' de kullanırsanız, bazı uygulama erişimi kaybetmeksizin anahtarlarınızı döndüremezsiniz.

Bir hesabın erişim anahtarlarını döndürmek için, kullanıcının bir hizmet yöneticisi olması veya **Microsoft. Storage/storageAccounts/RegenerateKey/Action** Içeren bir Azure rolü atanması gerekir. Bu eylemi içeren bazı Azure yerleşik rolleri, **sahip**, **katkıda** bulunan ve **depolama hesabı anahtar operatörü hizmeti rolü** rolleridir. Hizmet Yöneticisi rolü hakkında daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md). Azure depolama için Azure yerleşik rolleri hakkında ayrıntılı bilgi için Azure [RBAC Için Azure yerleşik rollerinin](../../role-based-access-control/built-in-roles.md#storage) **depolama** bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure depolama hesabına genel bakış](storage-account-overview.md)
- [Depolama hesabı oluşturma](storage-account-create.md)
