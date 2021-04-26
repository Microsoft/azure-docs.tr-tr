---
title: Azure AD uygulama önkoşulları (önceki sürüm) ile Azure disk şifrelemesi
description: Bu makale, Azure AD ile Azure disk şifrelemesi için ek gereksinimlere ve önkoşullara sahip Linux sanal makineleri için Azure disk şifrelemesi 'ne ek niteliğindedir.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e27eb64139c20ac2b8c776edc2d0840b80fddb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558347"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure Active Directory (AD) ile Azure disk şifrelemesi (önceki sürüm)

Azure disk şifrelemesi 'nin yeni sürümü, VM disk şifrelemeyi etkinleştirmek için bir Azure Active Directory (Azure AD) uygulama parametresi sağlama gereksinimini ortadan kaldırır. Yeni sürümle birlikte, şifrelemeyi Etkinleştir adımını kullanarak artık Azure AD kimlik bilgilerini sağlamanız gerekmez. Yeni sürüm kullanılarak tüm yeni VM 'Ler Azure AD uygulama parametreleri olmadan şifrelenmelidir. Yeni sürümü kullanarak VM disk şifrelemesini etkinleştirme hakkında yönergeler için bkz. [Linux VM 'leri Için Azure disk şifrelemesi](disk-encryption-overview.md). Azure AD uygulama parametreleriyle zaten şifrelenmiş VM 'Ler hala desteklenmektedir ve AAD sözdizimi ile sürdürülmeye devam etmelidir.

Bu makalede, Azure AD (önceki sürüm) ile Azure disk şifrelemesi için ek gereksinimlere ve önkoşullara sahip [Linux sanal makineleri Için Azure disk şifrelemesi](disk-encryption-overview.md) 'nin nasıl sağladığı açıklanır.

Bu bölümlerdeki bilgiler aynı kalır:

- [Desteklenen VM 'Ler ve işletim sistemleri](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ek VM gereksinimleri](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>Ağ ve grup ilkesi

Azure Disk Şifrelemesi özelliğini eski AAD parametresi sözdizimini kullanarak etkinleştirmek için hizmet olarak altyapı (IaaS) VM 'Leri aşağıdaki ağ uç noktası yapılandırma gereksinimlerini karşılamalıdır: 
  - Anahtar kasanıza bağlanmak için bir belirteç almak üzere, IaaS VM 'sinin bir Azure AD uç noktasına bağlanabilmelidir, \[ login.microsoftonline.com \] .
  - Şifreleme anahtarlarını anahtar kasanıza yazmak için, IaaS VM 'sinin Anahtar Kasası uç noktasına bağlanabilmesi gerekir.
  - IaaS sanal makinesi, Azure uzantı deposunu barındıran bir Azure depolama uç noktasına ve VHD dosyalarını barındıran bir Azure depolama hesabına bağlanabilmelidir.
  -  Güvenlik ilkeniz, Azure VM 'lerinden internet 'e erişimi sınırlayıp, önceki URI 'yi çözümleyebilir ve IP 'lere giden bağlantılara izin vermek için belirli bir kuralı yapılandırabilirsin. Daha fazla bilgi için bkz. [Azure Key Vault bir güvenlik duvarı arkasında](../../key-vault/general/access-behind-firewall.md).
  - Windows 'da, TLS 1,0 açık olarak devre dışıysa ve .NET sürümü 4,6 veya üzeri bir sürüme güncellenmemişse, aşağıdaki kayıt defteri değişikliği Azure disk şifrelemesi 'nin daha son TLS sürümünü seçmesini sağlar:

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>Grup İlkesi
 - Azure disk şifrelemesi çözümü, Windows IaaS VM 'Leri için BitLocker dış anahtar koruyucusunu kullanır. Etki alanına katılmış VM 'Ler için TPM koruyucuları uygulayan herhangi bir grup Ilkesi göndermeyin. **Uyumlu TPM olmadan BitLocker 'A Izin ver** seçeneğinin Grup ilkesi hakkında bilgi için, bkz. [BitLocker Grup İlkesi başvurusu](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- Özel bir grup ilkesi etki alanına katılmış sanal makinelerde BitLocker ilkesi şu ayarı içermelidir: [BitLocker kurtarma bilgileri 'nin Kullanıcı depolamasını yapılandırma-> Izin ver 256-bit kurtarma anahtarı](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). BitLocker için özel grup ilkesi ayarları uyumsuz olduğunda Azure disk şifrelemesi başarısız olur. Doğru ilke ayarına sahip olmayan makinelerde, yeni ilkeyi uygulayın, yeni ilkeyi güncelleştirmeye zorlayın (gpupdate.exe/Force) ve gerekirse yeniden başlatın. 

## <a name="encryption-key-storage-requirements"></a>Şifreleme anahtarı depolama gereksinimleri 

Azure disk şifrelemesi, disk şifreleme anahtarlarını ve gizli dizileri denetlemek ve yönetmek için Azure Key Vault gerektirir. Anahtar kasanızın ve sanal makinelerinizin aynı Azure bölgesinde ve abonelikte bulunması gerekir.

Daha fazla bilgi için bkz. Azure [ad Ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma (önceki sürüm)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma (önceki sürüm)](disk-encryption-key-vault-aad.md)
- [Linux sanal makinelerinde Azure AD ile Azure disk şifrelemesini etkinleştirme (önceki sürüm)](disk-encryption-linux-aad.md)
- [Azure disk şifrelemesi önkoşulları CLı betiği](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure disk şifrelemesi önkoşulları PowerShell betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
