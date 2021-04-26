---
title: Yönetilen HSM veri düzlemi rol yönetimi-Azure Key Vault | Microsoft Docs
description: Yönetilen HSM 'nizin rol atamalarını yönetmek için bu makaleyi kullanın
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 4d36b2c2178c7205246cd7c59aefedef3358e473
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951751"
---
# <a name="managed-hsm-role-management"></a>Yönetilen HSM rol yönetimi

> [!NOTE]
> Key Vault iki tür kaynak destekler: kasaların ve yönetilen HSM 'ler. Bu makale, **YÖNETILEN HSM** ile ilgilidir. Kasayı yönetme hakkında bilgi edinmek istiyorsanız lütfen bkz. [Azure CLI kullanarak Key Vault yönetme](../general/manage-with-cli2.md).

Yönetilen HSM 'ye genel bakış için bkz. [YÖNETILEN HSM nedir?](overview.md). Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Bu makalede, yönetilen bir HSM veri düzlemi için rolleri nasıl yöneteceğiniz gösterilmektedir. Yönetilen HSM erişim denetimi modeli hakkında bilgi edinmek için bkz. [YÖNETILEN HSM erişim denetimi](access-control.md).

Bir güvenlik sorumlusuna (örneğin, bir Kullanıcı, hizmet sorumlusu, Grup veya yönetilen kimlik) yönetilen HSM veri düzlemi işlemleri gerçekleştirmesini sağlamak için, bu işlemleri gerçekleştirmeye izin veren bir rol atanması gerekir. Örneğin, bir uygulamanın anahtar kullanarak bir imzalama işlemi gerçekleştirmesine izin vermek istiyorsanız, veri eylemlerinden biri olarak "Microsoft. Keykasası/managedHSM/Keys/Sign/Action" öğesini içeren bir rol atanması gerekir. Bir rol, belirli bir kapsamda atanabilir. Yönetilen HSM yerel RBAC, HSM genelinde iki kapsam ( `/` veya `/keys` ) ve anahtar başına () destekler `/keys/<keyname>` .

Tüm yönetilen HSM yerleşik rollerinin ve izin verdikleri işlemlerin listesi için bkz. [YÖNETILEN HSM yerleşik rolleri](built-in-roles.md).

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki Azure CLı komutlarını kullanmak için aşağıdaki öğelere sahip olmanız gerekir:

* Bir Microsoft Azure aboneliği. Hesabınız yoksa, [ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/pricing/free-trial).
* Azure CLı sürüm 2.21.0 veya üzeri. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).
* Aboneliğinizde yönetilen bir HSM. Bkz. [hızlı başlangıç: Azure CLI kullanarak](quick-create-cli.md) yönetilen bir HSM sağlama ve etkinleştirme.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

CLı kullanarak Azure 'da oturum açmak için şunu yazabilirsiniz:

```azurecli
az login
```

CLı aracılığıyla oturum açma seçenekleri hakkında daha fazla bilgi için bkz. [Azure CLI ile oturum açma](/cli/azure/authenticate-azure-cli)

## <a name="create-a-new-role-assignment"></a>Yeni bir rol ataması oluştur

### <a name="assign-roles-for-all-keys"></a>Tüm anahtarlar için roller atama

`az keyvault role assignment create`ContosoHSM içindeki tüm **anahtarlar** (kapsam) için Kullanıcı asıl adı **kullanıcı2 \@ contoso.com** tarafından tanımlanan kullanıcıya **yönetilen bir HSM şifre Müdürü** rolü atamak için komutunu kullanın `/keys` .

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Belirli bir anahtar için rol ata

`az keyvault role assignment create` **Myrsakey** adlı belirli bir anahtar için Kullanıcı asıl adı **kullanıcı2 \@ contoso.com** tarafından tanımlanan kullanıcıya **yönetilen bir HSM şifre Müdürü** rolü atamak için komutunu kullanın.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Varolan rol atamalarını listeleyin

`az keyvault role assignment list`Rol atamalarını listelemek için kullanın.

Tüm kullanıcılar için kapsam/(hiçbir kapsam belirtilmediğinde varsayılan) tüm rol atamaları (--atane belirtildiğinde varsayılan)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Tüm rol atamaları belirli bir kullanıcı için HSM düzeyinde **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> Kapsam/(veya/anahtarlar) olduğunda list komutu yalnızca en üst düzeydeki tüm rol atamalarını listeler ve tek bir anahtar düzeyinde rol atamalarını göstermez.

Belirli bir anahtar için belirli bir kullanıcı için tüm rol atamaları **user2@contoso.com** **myrsakey**.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Belirli bir  **user2@contoso.com** anahtar **myrsakey** için belirli BIR kullanıcı Için rol yönetimli HSM şifre müdürü için belirli bir rol ataması


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Rol atamasını silme

`az keyvault role assignment delete`Key **myrsakey2** için Kullanıcı **kullanıcı2 \@ contoso.com** 'e atanan bir **yönetilen HSM şifre Müdürü** rolünü silmek için komutunu kullanın.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Tüm kullanılabilir rol tanımlarını listeleyin

`az keyvault role definition list`Tüm rol tanımlarını listelemek için komutunu kullanın.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="create-a-new-role-definition"></a>Yeni bir rol tanımı oluştur

Yönetilen HSM, en yaygın kullanım senaryoları için faydalı olan çeşitli yerleşik (önceden tanımlanmış) roller içerir. Rolün gerçekleştirmesine izin verilen belirli eylemlerin bir listesiyle kendi rolünüzü tanımlayabilirsiniz. Ardından, belirtilen eylemlere izin vermek için bu rolü sorumlularına atayabilirsiniz. 

`az keyvault role definition create`BIR JSON dizesi kullanarak **özel rol** adlı bir rol için komutunu kullanın.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
    "roleName": "My Custom Role",
    "description": "The description of the custom rule.",
    "actions": [],
    "notActions": [],
    "dataActions": [
        "Microsoft.KeyVault/managedHsm/keys/read/action"
    ],
    "notDataActions": []
}'
```

`az keyvault role definition create`Bir rol tanımı IÇIN JSON dizesini içeren **my-custom-role-definition.js** adlı dosyadan bir rol için komutunu kullanın. Yukarıdaki örneğe bakın.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition @my-custom-role-definition.json
```

## <a name="show-details-of-a-role-definition"></a>Rol tanımının ayrıntılarını göster

`az keyvault role definition show`Adı (bır GUID) kullanarak belirli bir rol tanımının ayrıntılarını görmek için komutunu kullanın.

```azurecli-interactive
az keyvault role definition show --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="update-a-custom-role-definition"></a>Özel bir rol tanımını güncelleştirme

`az keyvault role definition update`JSON dizesi kullanarak **özel rolmy** adlı bir rolü güncelleştirmek için komutunu kullanın.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
            "roleName": "My Custom Role",
            "name": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "id": "Microsoft.KeyVault/providers/Microsoft.Authorization/roleDefinitions/xxxxxxxx-
        xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "description": "The description of the custom rule.",
            "actions": [],
            "notActions": [],
            "dataActions": [
                "Microsoft.KeyVault/managedHsm/keys/read/action",
                "Microsoft.KeyVault/managedHsm/keys/write/action",
                "Microsoft.KeyVault/managedHsm/keys/backup/action",
                "Microsoft.KeyVault/managedHsm/keys/create"
            ],
            "notDataActions": []
        }'
```

## <a name="delete-custom-role-definition"></a>Özel rol tanımını sil

`az keyvault role definition delete`Adı (bır GUID) kullanarak belirli bir rol tanımının ayrıntılarını görmek için komutunu kullanın. 
```azurecli-interactive
az keyvault role definition delete --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

> [!NOTE]
> Yerleşik roller silinemez. Özel roller silindiğinde, bu özel rolü kullanan tüm rol atamaları işlevsiz olur.


## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure rol tabanlı erişim denetimine genel bakış (Azure RBAC)](../../role-based-access-control/overview.md).
- [YÖNETILEN HSM rol yönetimi](role-management.md) hakkında öğreticiye bakın
- [YÖNETILEN HSM erişim denetimi modeli](access-control.md) hakkında daha fazla bilgi edinin
- [YÖNETILEN HSM yerel RBAC için yerleşik rollere](built-in-roles.md) bakın
