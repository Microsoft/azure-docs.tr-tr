---
title: Azure Key Vault Azure PowerShell yönetilen bir anahtarın özniteliklerini oluşturun ve alın
description: Azure PowerShell kullanarak Azure Key Vault yönetilen bir anahtarın nasıl ayarlanacağını ve alınacağını gösteren hızlı başlangıç
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom:
- mode-api
ms.openlocfilehash: aa984a8f3899db72ead878e2c4381ea6a080e32d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815441"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak Azure Key Vault yönetilen anahtar ayarlama ve alma

Bu hızlı başlangıçta, Azure PowerShell Azure Key Vault ' de bir Anahtar Kasası oluşturacaksınız. Azure Key Vault, güvenli bir gizli dizi deposu olarak çalışan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Key Vault hakkında daha fazla bilgi için [genel bakışı](../general/overview.md)gözden geçirebilirsiniz. Azure PowerShell komutları veya betikleri kullanarak Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bunu tamamladıktan sonra bir anahtar depolayacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üzerini gerektirir. `$PSVersionTable.PSVersion`Sürümü bulmak için yazın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Login-AzAccount` komutunu da çalıştırmanız gerekir.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. *Westus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturmak Için Azure PowerShell [New-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanın. 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Asıl KIMLIĞINIZ "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" biçiminde döndürülür.

## <a name="create-a-managed-hsm"></a>Yönetilen bir HSM oluşturma

Yeni bir Key Vault yönetilen HSM oluşturmak için Azure PowerShell [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) cmdlet 'ini kullanın. Bazı bilgileri sağlamanız gerekir:

- Yönetilen HSM adı: yalnızca rakam (0-9), harf (A-z, A-Z) ve kısa çizgi (-) içerebilen 3 ile 24 karakter arasında bir dize

  > [!Important]
  > Her yönetilen HSM 'nin benzersiz bir adı olmalıdır. -Unique-Managed-HSM-Name> <aşağıdaki örneklerde yönetilen HSM 'nizin adıyla değiştirin.

- Kaynak grubu adı: **Myresourcegroup**.
- Konum: **EastUS**.
- Asıl KIMLIĞINIZ: son bölümde aldığınız Azure Active Directory asıl KIMLIĞINI "Yönetici" parametresine geçirin. 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

Bu cmdlet 'in çıktısı, yeni oluşturulan yönetilen HSM 'nin özelliklerini gösterir. Aşağıda listelenen iki özelliği not edin:

- **YÖNETILEN HSM adı**: Yukarıdaki--name parametresine verdiğiniz addır.
- **Kasa URI 'si**: örnekte, https://- &lt; Unique-Managed-HSM-Name. Vault.Azure.net/' dir &gt; . REST API'si aracılığıyla kasanızı kullanan uygulamaların bu URI'yi kullanması gerekir.

Bu noktada Azure hesabınız, bu yeni anahtar kasasında herhangi bir işlemi gerçekleştirmeye yetkili olan tek hesaptır.

## <a name="activate-your-managed-hsm"></a>Yönetilen HSM 'nizi etkinleştirin

Tüm veri düzlemi komutları HSM etkinleştirilinceye kadar devre dışı bırakılır. Anahtar oluşturamaz veya rol atayamazsınız. Yalnızca create komutu sırasında atanan belirlenmiş yöneticiler HSM 'yi etkinleştirebilir. HSM 'yi etkinleştirmek için [güvenlik etki alanını](security-domain.md)indirmeniz gerekir.

HSM 'nizi etkinleştirmek için şunlar gerekir:
- En az 3 RSA anahtar çifti (en fazla 10)
- Güvenlik etki alanının şifresini çözmek için gereken minimum anahtar sayısını belirtin (çekirdek)

HSM 'yi etkinleştirmek için HSM 'ye en az 3 (en fazla 10) RSA ortak anahtarı gönderirsiniz. HSM, güvenlik etki alanını bu anahtarlarla şifreler ve geri gönderir. Bu güvenlik etki alanı indirmesi başarıyla tamamlandıktan sonra HSM 'niz kullanıma hazırız. Ayrıca, güvenlik etki alanının şifresini çözmek için gereken en az sayıda özel anahtar olan çekirdek belirtmeniz gerekir.

Aşağıdaki örnekte, `openssl` 3 otomatik olarak imzalanan sertifika oluşturmak için ( [](https://slproweb.com/products/Win32OpenSSL.html)Windows için kullanılabilir) ' nin nasıl kullanılacağı gösterilmektedir.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Bu adımda oluşturulan RSA anahtar çiftlerini ve güvenlik etki alanı dosyasını güvenli bir şekilde oluşturun ve saklayın.

Güvenlik etki alanını indirmek ve yönetilen HSM 'nizi etkinleştirmek için Azure PowerShell [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) cmdlet 'ini kullanın. Aşağıdaki örnek, 3 RSA anahtar çifti kullanır (Bu komut için yalnızca ortak anahtarlar gerekir) ve çekirdeği 2 olarak ayarlar.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Lütfen güvenlik etki alanı dosyasını ve RSA anahtar çiftlerini güvenli bir şekilde depolayın. Bu dosyalara olağanüstü durum kurtarma veya aynı güvenlik etki alanını paylaşan başka bir yönetilen HSM oluşturma için ihtiyaç duyarsınız. bu sayede anahtarları paylaşabilir.

Güvenlik etki alanı başarıyla indirildikten sonra, HSM 'niz etkin duruma gelir ve kullanıma sunulacaktır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturup içinde bir sertifika depoladığınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Azure PowerShell Key Vault cmdlet 'lerine](/powershell/module/az.keyvault/) yönelik başvuruya bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
