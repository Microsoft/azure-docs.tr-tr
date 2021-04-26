---
title: Güvenli ve kullanım ilkeleri
description: Azure 'daki sanal makinelere yönelik güvenlik ve ilkeler hakkında bilgi edinin.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 6754e4e60c31c35531b27e6cc2047e9f8ce3d0bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817404"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Azure'daki sanal makinelerde ilkeleri güvenli hale getirme ve kullanma

Çalıştırdığınız uygulamalar için sanal makinenizin (VM) güvenli tutulması önemlidir. Sanal makinelerinizin güvenliğini sağlamak, sanal makinelerinize güvenli erişimi kapsayan bir veya daha fazla Azure hizmetini ve özelliğini ve verilerinizin güvenli şekilde depolanmasını içerebilir. Bu makalede, sanal makinenizin ve uygulamalarınızın güvenliğini sağlamanıza olanak tanıyan bilgiler sağlanmaktadır.

## <a name="antimalware"></a>Kötü amaçlı yazılımdan koruma

Bulut ortamları için modern tehdit yatay, uyumluluk ve güvenlik gereksinimlerini karşılamak üzere etkili koruma sağlamak için basıncını artırarak dinamik bir deneyimdir. [Azure Için Microsoft Antimalware](../security/fundamentals/antimalware.md) , virüslerin, casus yazılımların ve diğer kötü amaçlı yazılımların tanımlanmasına ve kaldırılmasına yardımcı olan ücretsiz bir gerçek zamanlı koruma özelliğidir. Uyarılar, bilinen kötü amaçlı veya istenmeyen yazılımlar kendisini yüklemeyi veya VM 'niz üzerinde çalışmayı denediğinde sizi bilgilendirmek üzere yapılandırılabilir. Linux veya Windows Server 2008 çalıştıran VM 'lerde desteklenmez.

## <a name="azure-security-center"></a>Azure Güvenlik Merkezi

[Azure Güvenlik Merkezi](../security-center/security-center-introduction.md) , sanal makinelerinizdeki tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Güvenlik Merkezi, Azure aboneliklerinizde tümleşik güvenlik izleme ve ilke yönetimi sağlar, aksi takdirde fark edilmemiş tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş bir ekosistemiyle birlikte çalışabilir.

Güvenlik Merkezi 'nin tam zamanında erişimi, sanal makinelerinize gelen trafiği kilitlemek ve gerektiğinde sanal makinelere bağlanmak için kolay erişim sağlarken saldırılara maruz kalma olasılığını azaltmak için VM dağıtımınız genelinde uygulanabilir. Tam zamanında etkin olduğunda ve bir Kullanıcı bir VM 'ye erişim istediğinde, güvenlik merkezi kullanıcının VM 'ye yönelik hangi izinlere sahip olduğunu denetler. Doğru izinlere sahip olmaları durumunda istek onaylanır ve Güvenlik Merkezi, ağ güvenlik gruplarını (NSG 'ler), seçilen bağlantı noktalarına sınırlı bir süre boyunca gelen trafiğe izin verecek şekilde otomatik olarak yapılandırır. Sürenin süresi dolduktan sonra, güvenlik merkezi NSG 'leri önceki durumlarına geri yükler. 

## <a name="encryption"></a>Şifreleme

Yönetilen diskler için iki şifreleme yöntemi sunulur. Azure disk şifrelemesi olan işletim sistemi düzeyinde şifreleme ve sunucu tarafı şifreleme olan platform düzeyinde şifreleme.

### <a name="server-side-encryption"></a>Sunucu tarafı şifrelemesi

Azure yönetilen diskler, buluta kalıcı hale geldiğinde verilerinizi varsayılan olarak otomatik olarak şifreler. Sunucu tarafı şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. Azure yönetilen disklerindeki veriler, 256 bit [AES şifrelemesi](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)kullanılarak saydam bir şekilde şifrelenir, en güçlü blok şifrelemeleri KULLANILABILIR ve FIPS 140-2 uyumludur.

Şifreleme, yönetilen disklerin performansını etkilemez. Şifreleme için ek bir maliyet yoktur.

Yönetilen diskinizin şifrelenmesi için platform tarafından yönetilen anahtarları kullanabilir veya kendi anahtarlarınızı kullanarak şifrelemeyi yönetebilirsiniz. Şifrelemeyi kendi anahtarlarınız ile yönetmeyi seçerseniz, yönetilen disklerdeki tüm verileri şifrelemek ve şifrelerini çözmek için kullanılacak *müşteri tarafından yönetilen bir anahtar* belirtebilirsiniz. 

Sunucu tarafı şifreleme hakkında daha fazla bilgi edinmek için, [Windows](./disk-encryption.md) veya [Linux](./disk-encryption.md)makalelerine bakın.

### <a name="azure-disk-encryption"></a>Azure Disk Şifrelemesi

Gelişmiş [WINDOWS VM](windows/disk-encryption-overview.md) ve [Linux VM](linux/disk-encryption-overview.md) güvenliği ve uyumluluğu için, Azure 'daki sanal diskler şifrelenebilir. Windows VM 'lerinde sanal diskler, Rest 'de BitLocker kullanılarak şifrelenir. Linux VM 'lerinde sanal diskler, dm-crypt kullanılarak Rest 'te şifrelenir. 

Azure 'da sanal diskleri şifrelemek için ücret alınmaz. Şifreleme anahtarları yazılım korumasını kullanarak Azure Key Vault depolanır veya anahtarlarınızı FIPS 140-2 düzey 2 standartlarına sertifikalı donanım güvenlik modüllerinde (HSM 'ler) içeri aktarabilir veya oluşturabilirsiniz. Bu şifreleme anahtarları, VM 'nize bağlı sanal diskleri şifrelemek ve şifrelerini çözmek için kullanılır. Bu şifreleme anahtarlarının denetimini koruyabilir ve kullanımlarını denetleyebilir. Azure Active Directory hizmet sorumlusu, VM 'Ler açık ve kapalı olduğu için bu şifreleme anahtarlarını vermek üzere güvenli bir mekanizma sağlar.

## <a name="key-vault-and-ssh-keys"></a>Key Vault ve SSH anahtarları

Gizlilikler ve sertifikalar, kaynak olarak modellenebilir ve [Key Vault](../key-vault/general/basic-concepts.md)tarafından sağlanabilecek. [Windows VM](windows/key-vault-setup.md) 'ler için anahtar kasalarını ve [Linux sanal MAKINELERI](linux/key-vault-setup.md)için Azure clı 'yi oluşturmak üzere Azure PowerShell kullanabilirsiniz. Şifreleme için anahtarlar da oluşturabilirsiniz.

Anahtar Kasası erişim ilkeleri anahtarlar, gizlilikler ve sertifikalara ayrı ayrı izinler verir. Örneğin, bir kullanıcıya parolalar için herhangi bir izin vermeden yalnızca anahtarlar için erişim verebilirsiniz. Ancak, anahtar, parola veya sertifikalara erişim izni kasa düzeyinde verilir. Diğer bir deyişle, [Anahtar Kasası erişim ilkesi](../key-vault/general/security-features.md) nesne düzeyindeki izinleri desteklemez.

VM 'lere bağlandığınızda, üzerinde oturum açmak için daha güvenli bir yol sağlamak üzere ortak anahtar şifrelemeyi kullanmanız gerekir. Bu işlem, bir Kullanıcı adı ve parola yerine kendi kimlik doğrulaması için güvenli kabuk (SSH) komutunu kullanan ortak ve özel anahtar değişimini içerir. Parolalar, özellikle de Web sunucuları gibi Internet 'e yönelik VM 'lerde, deneme yanılma saldırılarına karşı savunmasız kalır. Secure Shell (SSH) anahtar çifti ile, kimlik doğrulaması için SSH anahtarları kullanan bir [Linux sanal makinesi](linux/mac-create-ssh-keys.md) oluşturabilir ve oturum açmak için parola gereksinimini ortadan kaldırabilirsiniz. [WINDOWS VM](linux/ssh-from-windows.md) 'den Linux sanal makinesine bağlanmak için SSH anahtarlarını da kullanabilirsiniz.

## <a name="managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikler

Bulut uygulamaları oluştururken yaygın olarak karşılaşılan bir zorluk, bulut hizmetlerinde kimlik doğrulaması yapmak için kodunuzda bulunan kimlik bilgilerinin yönetimidir. Kimlik bilgilerinin güvenlik altında tutulması önemli bir görevdir. İdeal olan kimlik bilgilerinin geliştirici iş istasyonlarında asla gösterilmemesi ve kaynak denetimine kaydedilmemesidir. Azure Key Vault kimlik bilgilerini, gizli dizileri ve diğer anahtarları güvenle depolamak için bir yol sağlar, ama bunları alabilmek için kodunuzun Key Vault'ta kimlik doğrulaması yapması gerekir. 

Azure Active Directory (Azure AD) hizmetindeki Azure kaynakları yönetilen hizmetleri bu sorunu çözer. Bu özellik, Azure hizmetlerine Azure AD üzerinde otomatik olarak yönetilen bir kimlik sağlar. Bu kimliği kullanarak, Key Vault da dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen tüm hizmetlerde kodunuzda kimlik bilgileri olmadan kimlik doğrulaması yapabilirsiniz.  Bir VM üzerinde çalışan kodunuz, yalnızca VM içinden erişilebilen iki uç noktasından belirteç isteyebilir. Bu hizmet hakkında daha ayrıntılı bilgi için [Azure kaynaklarına yönelik yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) genel bakış sayfasını gözden geçirin.   

## <a name="policies"></a>İlkeler

[Azure ilkeleri](../governance/policy/overview.md) , kuruluşunuzun [Windows VM](./windows/policy.md) 'Leri ve [Linux VM 'leri](./linux/policy.md)için istenen davranışı tanımlamak üzere kullanılabilir. Bir kuruluş, ilkeleri kullanarak kuruluş genelinde çeşitli kuralları ve kuralları zorunlu kılabilir. İstenen davranışın uygulanması, kuruluşun başarısına katkıda bulunmaya karşın riski azaltmaya yardımcı olabilir.

## <a name="azure-role-based-access-control"></a>Azure rol tabanlı erişim denetimi

[Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md)kullanarak, ekiplerinizi takımınızın içinde ayırabilirsiniz ve yalnızca VM 'nizin işlerini gerçekleştirmesi için ihtiyaç duydukları kullanıcılara erişim miktarını verebilirsiniz. SANAL makinede herkes için sınırsız izin vermek yerine yalnızca belirli eylemlere izin verebilirsiniz. [Azure CLI](/cli/azure/role)veya[Azure POWERSHELL](../role-based-access-control/role-assignments-powershell.md)kullanarak [Azure Portal](../role-based-access-control/role-assignments-portal.md)VM için erişim denetimi yapılandırabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
- [Linux](../security/fundamentals/overview.md) veya [Windows](/previous-versions/azure/virtual-machines/tutorial-azure-security)için Azure Güvenlik Merkezi 'ni kullanarak sanal makine güvenliğini izleme adımlarını adım adım inceleyin.