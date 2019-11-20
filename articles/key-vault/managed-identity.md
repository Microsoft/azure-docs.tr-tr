---
title: Azure Key Vault erişmek için sistem tarafından atanan yönetilen kimlik kullanma
description: App Service uygulamalar için yönetilen bir kimlik oluşturmayı ve bunu nasıl kullanabileceğinizi öğrenin Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 75317f73ad724b4ce8ad7a894890b2269bd8c5d0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837507"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Yönetilen kimlik ile Key Vault kimlik doğrulaması sağlama

Azure Active Directory yönetilen bir kimlik, uygulamanızın diğer Azure AD korumalı kaynaklara kolayca erişmesini sağlar. Kimlik, Azure platformu tarafından yönetilir ve herhangi bir gizli dizi sağlamanızı veya döndürmenizi gerektirmez. Daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md). 

Bu makalede, bir App Service uygulaması için yönetilen kimlik oluşturma ve Azure Key Vault erişmek için kullanma açıklanmaktadır. Azure VM 'lerinde barındırılan uygulamalar için bkz. [Azure Key Vault erişmek Için WINDOWS VM sistem tarafından atanan bir yönetilen kimlik kullanma](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Ön koşullar 

Bu kılavuzu gerçekleştirmek için aşağıdaki kaynaklara sahip olmanız gerekir. 

- Bir Anahtar Kasası. Mevcut bir anahtar kasasını kullanabilir veya şu hızlı başlangıçlardan birindeki adımları izleyerek yeni bir tane oluşturabilirsiniz:
   - [Azure CLı ile Anahtar Kasası oluşturma](quick-create-cli.md)
   - [Azure PowerShell ile bir Anahtar Kasası oluşturma](quick-create-powershell.md)
   - [Azure Portal bir Anahtar Kasası oluşturun](quick-create-portal.md).
- Anahtar Kasası erişimi verilecek mevcut bir App Service uygulaması. [App Service belgelerindeki](../app-service/overview.md)adımları izleyerek, hızlıca bir tane oluşturabilirsiniz.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) veya [Azure PowerShell](/powershell/azure/overview). Alternatif olarak, [Azure Portal](https://portal.azure.com)de kullanabilirsiniz.


## <a name="adding-a-system-assigned-identity"></a>Sistem tarafından atanan kimlik ekleme 

İlk olarak, bir uygulamaya sistem tarafından atanan bir kimlik eklemeniz gerekir. 
 
### <a name="azure-portal"></a>Azure portal 

Portalda yönetilen bir kimlik ayarlamak için öncelikle normal olarak bir uygulama oluşturun ve ardından özelliği etkinleştirmeniz gerekir. 

1. Bir işlev uygulaması kullanıyorsanız, **platform özellikleri**' ne gidin. Diğer uygulama türleri için, sol gezinti bölmesinde **Ayarlar** grubuna gidin. 

1. **Yönetilen kimlik**' i seçin. 

1. **Sistem atandı** sekmesinde **durumu** **Açık**olarak değiştirin. **Kaydet** düğmesine tıklayın. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Bu hızlı başlangıç, Azure CLı sürüm 2.0.4 veya üstünü gerektirir. Geçerli sürümünüzü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Azure CLı ile oturum açmak için [az Login](/cli/azure/reference-index?view=azure-cli-latest#az-login) komutunu kullanın:

```azurecli-interactive
az login
```

Azure CLı ile oturum açma seçenekleri hakkında daha fazla bilgi için bkz. [Azure CLI Ile oturum açma](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Bu uygulamanın kimliğini oluşturmak için Azure CLı [az WebApp Identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) komutunu veya [az functionapp Identity Assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) komutunu kullanın:


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Sonraki bölümde gerekli olacak `PrincipalId`bir yer yapın.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Uygulamanıza Key Vault erişim izni verin 

### <a name="azure-portal"></a>Azure portal

1.  Key Vault kaynağına gidin. 

1.  **Erişim ilkeleri** ' ni seçin ve **erişim ilkesi Ekle**' ye tıklayın. 

1.  **Gizli izinler**' de **Al, Listele '** yi seçin. 

1.  **Sorumluyu Seç**' i seçin ve arama alanına uygulamanın adını girin.  Sonuç listesinden uygulamayı seçin ve **Seç**' e tıklayın. 

1.  Yeni erişim ilkesini eklemeyi sona bırakmak için **Ekle** ' ye tıklayın.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Uygulamanıza anahtar kasanıza erişim izni vermek için, yukarıda not ettiğiniz **PrincipalId** 'ye sahip **ObjectID** parametresini sağlayarak Azure CLI [az keykasa Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) komutunu kullanın.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault güvenliği: kimlik ve erişim yönetimi](overview-security.md#identity-and-access-management)
- [Erişim denetimi ilkesiyle Key Vault kimlik doğrulaması sağlama](key-vault-group-permissions-for-apps.md)
- [Anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-and-certificates.md)
- [Anahtar kasanızın güvenliğini sağlayın](key-vault-secure-your-key-vault.md).
- [Geliştirici Kılavuzu Azure Key Vault](key-vault-developers-guide.md)
- [En iyi uygulamaları](key-vault-best-practices.md) gözden geçirin Azure Key Vault