---
title: include dosyası
description: include dosyası
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 3509185baa3a9d7be90c1fa4bd8000da4a8a6fe5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561397"
---
## <a name="prepare-for-akv-integration"></a>AKV tümleştirmesi için hazırlanma
SQL Server VM yapılandırmak üzere Azure Key Vault tümleştirmesini kullanmak için birkaç önkoşul vardır: 

1. [Azure PowerShell 'i yükler](#install)
2. [Azure Active Directory oluşturma](#register)
3. [Anahtar kasası oluşturma](#createkeyvault)

Aşağıdaki bölümlerde, bu Önkoşullar ve daha sonra PowerShell cmdlet 'lerini çalıştırmak için toplamanız gereken bilgiler açıklanır.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Azure PowerShell yüklensin
En son Azure PowerShell modülünü yüklediğinizden emin olun. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Azure Active Directory bir uygulamayı kaydetme

İlk olarak, aboneliğinizde bir [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) olması gerekir. Birçok avantaj arasında, bu, belirli kullanıcılar ve uygulamalar için anahtar kasanıza izin vermenizi sağlar.

Sonra, bir uygulamayı AAD ile kaydedin. Bu size, sanal makinenizin ihtiyaç duyduğu anahtar kasanıza erişimi olan bir hizmet sorumlusu hesabı sağlar. Azure Key Vault makalesinde, bu adımları [bir uygulamayı Azure Active Directory Ile kaydet](../articles/key-vault/general/manage-with-cli2.md#registering-an-application-with-azure-active-directory) bölümünde bulabilir veya [Bu blog gönderisinin](/archive/blogs/kv/azure-key-vault-step-by-step) **uygulama için kimlik Al bölümünde** ekran görüntüleriyle ilgili adımları görebilirsiniz. Bu adımları tamamlamadan önce, daha sonra SQL VM 'niz üzerinde Azure Key Vault tümleştirmeyi etkinleştirdiğinizde gerekli olan bu kayıt sırasında aşağıdaki bilgileri toplamanız gerekir.

* Uygulama eklendikten sonra, **kayıtlı** uygulama dikey PENCERESINDE **uygulama kimliğini** (AAD ClientID veya AppID olarak da bilinir) bulun.
    Uygulama KIMLIĞI daha sonra Azure Key Vault tümleştirmesini etkinleştirmek için PowerShell betiğindeki **$spName** (hizmet asıl adı) parametresine atanır.

   ![Uygulama Kimliği](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Anahtarınızı oluştururken bu adımlar sırasında, aşağıdaki ekran görüntüsünde gösterildiği gibi anahtarınızın parolasını kopyalayın. Bu anahtar gizli anahtarı daha sonra PowerShell betiğindeki **$spSecret** (hizmet sorumlusu gizli anahtarı) parametresine atanır.

   ![AAD gizli anahtarı](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Uygulama KIMLIĞI ve gizli anahtar, SQL Server bir kimlik bilgisi oluşturmak için de kullanılacaktır.

* Bu yeni uygulama KIMLIĞINI (veya istemci KIMLIĞI) aşağıdaki erişim izinlerine sahip olacak şekilde yetkilendirmelisiniz: **Get**, **wrapKey**, **unwrapKey**. Bu, [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 'i ile yapılır. Daha fazla bilgi için bkz. [Azure Key Vault genel bakış](../articles/key-vault/general/overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Anahtar Kasası oluşturma
SANAL makinenizde şifreleme için kullanacağınız anahtarları depolamak üzere Azure Key Vault kullanmak için bir anahtar kasasına erişmeniz gerekir. Anahtar kasanızı henüz yüklemediyseniz [Azure Key Vault kullanmaya](../articles/key-vault/general/overview.md) başlama makalesindeki adımları izleyerek bir tane oluşturun. Bu adımları tamamlamadan önce, bu ayarlama sırasında, daha sonra SQL VM 'niz üzerinde Azure Key Vault tümleştirmesinin etkinleştirilmesi için gereken bazı bilgiler vardır.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

Anahtar Kasası oluşturma adımını aldığınızda, Anahtar Kasası URL 'SI olan döndürülen **Vaulturi** özelliğini aklınızda bulabilirsiniz. Bu adımda verilen örnekte, aşağıda gösterildiği gibi, Anahtar Kasası adı Contosokeykasadır, bu nedenle Anahtar Kasası URL 'SI olur https://contosokeyvault.vault.azure.net/ .

Anahtar Kasası URL 'SI daha sonra Azure Key Vault tümleştirmesini etkinleştirmek için PowerShell betiğindeki **$akvURL** parametresine atanır.

Anahtar Kasası oluşturulduktan sonra anahtar kasasına bir anahtar eklememiz gerekir, daha sonra SQL Server bir asimetrik anahtar oluştururken bu anahtara başvurulur.