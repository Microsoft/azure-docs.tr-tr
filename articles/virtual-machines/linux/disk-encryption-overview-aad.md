---
title: Azure AD Uygulaması önkoşulları ile Azure disk şifrelemesi (önceki sürüm)
description: Bu makalede, IaaS VM 'Leri için Microsoft Azure disk şifrelemesi kullanma önkoşulları sağlanır.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5f3de0f877186daa8f6add7fcd1546f91d6ce3d2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748924"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure AD ile Azure disk şifrelemesi (önceki sürüm)

**Azure disk şifrelemesi 'nin yeni sürümü, VM disk şifrelemeyi etkinleştirmek için bir Azure AD uygulama parametresi sağlama gereksinimini ortadan kaldırır. Yeni sürümde, artık şifrelemeyi Etkinleştir adımını kullanarak Azure AD kimlik bilgilerini sağlamanız gerekmez. Yeni sürüm kullanılarak Azure AD uygulama parametreleri olmadan tüm yeni VM 'Ler şifrelenmelidir. Yeni sürümü kullanarak VM disk şifrelemesini etkinleştirme yönergelerini görüntülemek için bkz. [Linux VM 'leri Için Azure disk şifrelemesi](disk-encryption-overview.md). Azure AD uygulama parametreleriyle zaten şifrelenmiş VM 'Ler hala desteklenmektedir ve AAD sözdizimi ile sürdürülmeye devam etmelidir.**

Bu makale, Azure AD (önceki sürüm) ile Azure disk şifrelemesi için ek gereksinimlere ve önkoşullara sahip [Linux sanal makineleri Için Azure disk şifrelemesi](disk-encryption-overview.md) sunmaktadır.

Bu bölümlerdeki bilgiler aynı kalır:

- [Desteklenen VM 'Ler ve işletim sistemleri](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ek VM gereksinimleri](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Ağ ve grup ilkesi

**Eski AAD parametresi söz dizimini kullanarak Azure Disk Şifrelemesi özelliğini etkinleştirmek için IaaS VM 'lerinin aşağıdaki ağ uç noktası yapılandırma gereksinimlerini karşılaması gerekir:** 
  - Anahtar kasanıza bağlanma belirteci almak için, IaaS VM 'nin bir Azure Active Directory uç noktasına bağlanabilmesi gerekir \[login.microsoftonline.com\].
  - Şifreleme anahtarlarını anahtar kasanıza yazmak için, IaaS VM 'sinin Anahtar Kasası uç noktasına bağlanabilmesi gerekir.
  - IaaS sanal makinesi, Azure uzantı deposunu barındıran bir Azure depolama uç noktasına ve VHD dosyalarını barındıran bir Azure depolama hesabına bağlanabilmelidir.
  -  Güvenlik ilkeniz, Azure VM 'lerinden Internet 'e erişimi sınırlayıp, önceki URI 'yi çözümleyebilir ve IP 'lere giden bağlantılara izin vermek için belirli bir kuralı yapılandırabilirsin. Daha fazla bilgi için bkz. [Azure Key Vault bir güvenlik duvarı arkasında](../../key-vault/key-vault-access-behind-firewall.md).
  - Windows 'da, TLS 1,0 açıkça devre dışıysa ve .NET sürümü 4,6 veya üzeri bir sürüme güncelleştirilmemiş ise, aşağıdaki kayıt defteri değişikliği ADE 'nin daha son TLS sürümünü seçmesini sağlayacak:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**grup ilkesi:**
 - Azure disk şifrelemesi çözümü, Windows IaaS VM 'Leri için BitLocker dış anahtar koruyucusunu kullanır. Etki alanına katılmış VM 'Ler için TPM koruyucuları uygulayan herhangi bir grup ilkesi göndermeyin. "Uyumlu TPM olmadan BitLocker 'a Izin ver" Grup ilkesi hakkında bilgi için bkz. [bitlocker Grup İlkesi başvurusu](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Özel grup ilkesiyle etki alanına katılmış sanal makinelerde BitLocker ilkesi şu ayarı içermelidir: [BitLocker kurtarma bilgileri 'nin Kullanıcı depolamasını yapılandırma-> Izin ver 256-bit kurtarma anahtarı](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). BitLocker için özel Grup İlkesi ayarları uyumsuz olduğunda Azure disk şifrelemesi başarısız olur. Doğru ilke ayarına sahip olmayan makinelerde, yeni ilkeyi uygulayın, yeni ilkeyi güncelleştirmeye zorlayın (gpupdate. exe/Force) ve yeniden başlatma gerekebilir.  

## <a name="encryption-key-storage-requirements"></a>Şifreleme anahtarı depolama gereksinimleri  

Azure disk şifrelemesi, disk şifreleme anahtarlarını ve gizli dizileri denetlemek ve yönetmek için bir Azure Key Vault gerektirir. Anahtar kasanızın ve sanal makinelerinizin aynı Azure bölgesinde ve abonelikte bulunması gerekir.

Ayrıntılar için bkz. Azure [ad Ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma (önceki sürüm)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD ile Azure disk şifrelemesi için bir Anahtar Kasası oluşturma ve yapılandırma (önceki sürüm)](disk-encryption-key-vault-aad.md)
- [Linux sanal makinelerinde Azure AD ile Azure disk şifrelemesini etkinleştirme (önceki sürüm)](disk-encryption-linux-aad.md)
- [Azure disk şifrelemesi önkoşulları CLı betiği](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure disk şifrelemesi önkoşulları PowerShell betiği](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)