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
ms.openlocfilehash: c1a6a53e6db883c63164a6367012faf32ed75519
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673560"
---
## <a name="prepare-for-akv-integration"></a>AKV tümleştirme için hazırlama
SQL Server VM'nize yapılandırmak için Azure anahtar kasası tümleştirmeyi kullanmak için birkaç önkoşul vardır: 

1. [Azure PowerShell'i yükleme](#install)
2. [Bir Azure Active Directory oluşturun](#register)
3. [Anahtar kasası oluşturma](#createkeyvault)

Aşağıdaki bölümlerde, bu önkoşulları ve daha sonra PowerShell cmdlet'lerini çalıştırmak üzere toplamanız gereken bilgiler açıklanmaktadır.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a id="install"></a> Azure PowerShell'i yükleme
En son Azure PowerShell modülü yüklediğinizden emin olun. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-az-ps).

### <a id="register"></a> Azure Active Directory'niz içinde bir uygulamayı kaydetme

İlk olarak, ihtiyacınız bir [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD), aboneliğinizdeki. Birçok avantaj arasında anahtar kasanız belirli kullanıcılar ve uygulamalar için izin vermenizi sağlar.

Ardından, bir uygulamayı AAD'ye kaydetme. Bu, sanal Makinenizin gerekir, anahtar kasası erişimi olan bir hizmet sorumlusu hesabı verecektir. Bu adımlarda Azure Key Vault makalesinde bulabilirsiniz [bir uygulamayı Azure Active Directory'ye kaydetme](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) bölüm veya ekran görüntülerinde adımlarla görebilirsiniz **bir kimlik almak için uygulama bölümüne**  , [bu blog gönderisini](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Bu adımları gerçekleştirmeden önce SQL sanal makinenizde Azure anahtar kasası tümleştirmeyi etkinleştirdiğinizde, daha sonra gerekli bu kayıt sırasında aşağıdaki bilgi toplamanız gerekir.

* Uygulama eklendikten sonra bulma **uygulama kimliği** üzerinde **kayıtlı uygulama** dikey penceresi.
    Uygulama Kimliği daha sonra atanan **$spName** Azure anahtar kasası tümleştirmeyi etkinleştirmek için PowerShell betiğini parametresinde (hizmet asıl adı).

   ![Uygulama Kimliği](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Anahtarınızı yeniden oluşturduğunuzda, bu adımları sırasında gizli anahtarınız için aşağıdaki ekran görüntüsünde gösterildiği gibi kopyalayın. Bu anahtarı gizli anahtarı daha sonra atanan **$spSecret** PowerShell betiğini parametresinde (hizmet sorumlusu gizli anahtarı).

   ![AAD gizli anahtarı](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Uygulama Kimliğini ve parolasını SQL Server'da bir kimlik bilgisi oluşturmak için de kullanılır.

* Bu yeni istemci kimliği aşağıdaki erişim izinlerine sahip yetkilendirmeniz gerekir: **alma**, **wrapKey**, **unwrapKey**. Bunun [kümesi AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet'i. Daha fazla bilgi için [Azure anahtar kasası genel bakış](../articles/key-vault/key-vault-overview.md).

### <a id="createkeyvault"></a> Anahtar kasası oluşturma
Kullanacağınız için şifreleme, sanal anahtarları depolamak için Azure anahtar Kasası'nı kullanmak için bir anahtar kasasına erişim gerekir. Anahtar kasanız zaten ayarlamadıysanız adımları izleyerek bir tane oluşturmak [Azure anahtar kasası ile çalışmaya başlama](../articles/key-vault/key-vault-overview.md) makalesi. Bu adımları gerçekleştirmeden önce SQL sanal makinenizde Azure anahtar kasası tümleştirmeyi etkinleştirdiğinizde, daha sonra gerekli yedekleme sırasında bu kümesi toplamanız gereken bazı bilgileri yoktur.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Bir anahtar kasası adım Oluştur aldığınızda döndürülen Not **vaultUri** anahtar kasası URL'si özelliği. Anahtar kasası adı olan ContosoKeyVault, aşağıda gösterilen Bu adımda, sağlanan örneği, bu nedenle anahtar kasası URL'si olacaktır https://contosokeyvault.vault.azure.net/.

Key vault URL'si daha sonra atanan **$akvURL** Azure anahtar kasası tümleştirmeyi etkinleştirmek için PowerShell komut parametresi.

Anahtar kasası oluşturulur, oluşturduğumuzda bu anahtarı başvurulan bir anahtar kasasına bir anahtar eklemek ihtiyacımız sonra asimetrik bir anahtar oluşturun SQL Server'da daha sonra.
