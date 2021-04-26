---
title: Azure Key Vault erişim ilkesi atama (CLı)
description: Azure CLı kullanarak bir güvenlik sorumlusu veya uygulama kimliğine Key Vault erişim ilkesi atama.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 96b4daa027871201a201b253721114372e58f377
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751445"
---
# <a name="assign-a-key-vault-access-policy"></a>Key Vault erişim ilkesi atama

Key Vault erişim ilkesi, bir Kullanıcı, uygulama veya Kullanıcı grubu gibi belirli bir güvenlik sorumlusunun Key Vault [gizli](../secrets/index.yml)diziler, [anahtarlar](../keys/index.yml)ve [Sertifikalar](../certificates/index.yml)üzerinde farklı işlemler yapıp gerçekleştiremeyeceğini belirler. [Azure Portal](assign-access-policy-portal.md), Azure CLI (Bu makale) veya [Azure PowerShell](assign-access-policy-powershell.md)kullanarak erişim ilkeleri atayabilirsiniz.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Azure CLı kullanarak Azure Active Directory Grup oluşturma hakkında daha fazla bilgi için, bkz. [az Ad Group Create](/cli/azure/ad/group#az-ad-group-create) ve [az Ad Group member Add](/cli/azure/ad/group/member#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Azure CLı 'yı yapılandırma ve oturum açma

1. Azure CLı komutlarını yerel olarak çalıştırmak için [Azure CLI](/cli/azure/install-azure-cli)'yı yüklemelisiniz.
 
    Komutları bulutta doğrudan çalıştırmak için [Azure Cloud Shell](../../cloud-shell/overview.md)kullanın.

1. Yalnızca yerel CLı: kullanarak Azure 'da oturum açın `az login` :

    ```bash
    az login
    ```

    Bu `az login` komut, gerekirse kimlik bilgilerini toplamak için bir tarayıcı penceresi açar.

## <a name="acquire-the-object-id"></a>Nesne KIMLIĞINI edinin

Erişim ilkesini atamak istediğiniz uygulamanın, grubun veya kullanıcının nesne KIMLIĞINI belirleme:

- Uygulamalar ve diğer hizmet sorumluları: hizmet sorumlularını almak için [az ad SP List](/cli/azure/ad/sp#az-ad-sp-list) komutunu kullanın. Erişim ilkesini atamak istediğiniz güvenlik sorumlusunun nesne KIMLIĞINI belirleme komutunun çıkışını inceleyin.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Gruplar: sonuçları parametresiyle filtreleyerek [az Ad Group List](/cli/azure/ad/group#az-ad-group-list) komutunu kullanın `--display-name` :

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Kullanıcılar: Kullanıcı e-posta adresini parametreye geçirerek [az ad User Show](/cli/azure/ad/user#az-ad-user-show) komutunu kullanın `--id` :

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Erişim ilkesini atama
    
İstenen izinleri atamak için [az keykasası Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) komutunu kullanın:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

`<object-id>`Güvenlik sorumlusunun nesne kimliğiyle değiştirin.

Yalnızca `--secret-permissions` `--key-permissions` `--certificate-permissions` Bu belirli türlere izinler atarken, ve dahil etmeniz gerekir. , Ve için izin verilen değerler, `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` [az keykasa Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) belgelerinde verilmiştir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault güvenliği: kimlik ve erişim yönetimi](security-overview.md#identity-management)
- [Anahtar kasanızın güvenliğini sağlayın](security-overview.md).
- [Geliştirici Kılavuzu Azure Key Vault](developers-guide.md)