---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 53c9dea83fc6d413d7e82194696ffedabcc8cf7b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71830192"
---
Çalıştırdığınız uygulamalar için sanal makinenizin (VM) güvenli tutulması önemlidir. Sanal makinelerinizin güvenliğini sağlamak, sanal makinelerinize güvenli erişimi kapsayan bir veya daha fazla Azure hizmetini ve özelliğini ve verilerinizin güvenli şekilde depolanmasını içerebilir. Bu makalede, sanal makinenizin ve uygulamalarınızın güvenliğini sağlamanıza olanak tanıyan bilgiler sağlanmaktadır.

## <a name="antimalware"></a>Kötü Amaçlı Yazılımdan Koruma

Bulut ortamları için modern tehdit yatay, uyumluluk ve güvenlik gereksinimlerini karşılamak üzere etkili koruma sağlamak için basıncını artırarak dinamik bir deneyimdir. [Azure Için Microsoft Antimalware](../articles/security/fundamentals/antimalware.md) , virüslerin, casus yazılımların ve diğer kötü amaçlı yazılımların tanımlanmasına ve kaldırılmasına yardımcı olan ücretsiz bir gerçek zamanlı koruma özelliğidir. Uyarılar, bilinen kötü amaçlı veya istenmeyen yazılımlar kendisini yüklemeyi veya VM 'niz üzerinde çalışmayı denediğinde sizi bilgilendirmek üzere yapılandırılabilir. Linux veya Windows Server 2008 çalıştıran VM 'lerde desteklenmez.

## <a name="azure-security-center"></a>Azure Güvenlik Merkezi

[Azure Güvenlik Merkezi](../articles/security-center/security-center-intro.md) , sanal makinelerinizdeki tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Güvenlik Merkezi, Azure aboneliklerinizde tümleşik güvenlik izleme ve ilke yönetimi sağlar, aksi takdirde fark edilmemiş tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş bir ekosistemiyle birlikte çalışabilir.

Güvenlik Merkezi 'nin tam zamanında erişimi, sanal makinelerinize gelen trafiği kilitlemek ve gerektiğinde sanal makinelere bağlanmak için kolay erişim sağlarken saldırılara maruz kalma olasılığını azaltmak için VM dağıtımınız genelinde uygulanabilir. Tam zamanında etkin olduğunda ve bir Kullanıcı bir VM 'ye erişim istediğinde, güvenlik merkezi kullanıcının VM 'ye yönelik hangi izinlere sahip olduğunu denetler. Doğru izinlere sahip olmaları durumunda istek onaylanır ve Güvenlik Merkezi, ağ güvenlik gruplarını (NSG 'ler), seçilen bağlantı noktalarına sınırlı bir süre boyunca gelen trafiğe izin verecek şekilde otomatik olarak yapılandırır. Sürenin süresi dolduktan sonra, güvenlik merkezi NSG 'leri önceki durumlarına geri yükler. 

## <a name="encryption"></a>Şifreleme

Gelişmiş [WINDOWS VM](../articles/virtual-machines/windows/encrypt-disks.md) ve [Linux VM](../articles/virtual-machines/linux/disk-encryption-overview.md) güvenliği ve uyumluluğu için, Azure 'daki sanal diskler şifrelenebilir. Windows VM 'lerinde sanal diskler, Rest 'de BitLocker kullanılarak şifrelenir. Linux VM 'lerinde sanal diskler, dm-crypt kullanılarak Rest 'te şifrelenir. 

Azure 'da sanal diskleri şifrelemek için ücret alınmaz. Şifreleme anahtarları yazılım korumasını kullanarak Azure Key Vault depolanır veya anahtarlarınızı FIPS 140-2 düzey 2 standartlarına sertifikalı donanım güvenlik modüllerinde (HSM 'ler) içeri aktarabilir veya oluşturabilirsiniz. Bu şifreleme anahtarları, VM 'nize bağlı sanal diskleri şifrelemek ve şifrelerini çözmek için kullanılır. Bu şifreleme anahtarlarının denetimini koruyabilir ve kullanımlarını denetleyebilir. Azure Active Directory hizmet sorumlusu, VM 'Ler açık ve kapalı olduğu için bu şifreleme anahtarlarını vermek üzere güvenli bir mekanizma sağlar.

## <a name="key-vault-and-ssh-keys"></a>Key Vault ve SSH anahtarları

Gizlilikler ve sertifikalar, kaynak olarak modellenebilir ve [Key Vault](../articles/key-vault/key-vault-whatis.md)tarafından sağlanabilecek. [Windows VM](../articles/virtual-machines/windows/key-vault-setup.md) 'ler için anahtar kasalarını ve [Linux sanal MAKINELERI](../articles/virtual-machines/linux/key-vault-setup.md)için Azure clı 'yi oluşturmak üzere Azure PowerShell kullanabilirsiniz. Şifreleme için anahtarlar da oluşturabilirsiniz.

Anahtar Kasası erişim ilkeleri anahtarlar, gizlilikler ve sertifikalara ayrı ayrı izinler verir. Örneğin, bir kullanıcıya parolalar için herhangi bir izin vermeden yalnızca anahtarlar için erişim verebilirsiniz. Ancak, anahtar, parola veya sertifikalara erişim izni kasa düzeyinde verilir. Diğer bir deyişle, [Anahtar Kasası erişim ilkesi](../articles/key-vault/key-vault-secure-your-key-vault.md) nesne düzeyindeki izinleri desteklemez.

VM 'lere bağlandığınızda, üzerinde oturum açmak için daha güvenli bir yol sağlamak üzere ortak anahtar şifrelemeyi kullanmanız gerekir. Bu işlem, bir Kullanıcı adı ve parola yerine kendi kimlik doğrulaması için güvenli kabuk (SSH) komutunu kullanan ortak ve özel anahtar değişimini içerir. Parolalar, özellikle de Web sunucuları gibi Internet 'e yönelik VM 'lerde, deneme yanılma saldırılarına karşı savunmasız kalır. Secure Shell (SSH) anahtar çifti ile, kimlik doğrulaması için SSH anahtarları kullanan bir [Linux sanal makinesi](../articles/virtual-machines/linux/mac-create-ssh-keys.md) oluşturabilir ve oturum açmak için parola gereksinimini ortadan kaldırabilirsiniz. [WINDOWS VM](../articles/virtual-machines/linux/ssh-from-windows.md) 'den Linux sanal makinesine bağlanmak için SSH anahtarlarını da kullanabilirsiniz.

## <a name="managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikler

Bulut uygulamaları oluştururken yaygın olarak karşılaşılan bir zorluk, bulut hizmetlerinde kimlik doğrulaması yapmak için kodunuzda bulunan kimlik bilgilerinin yönetimidir. Kimlik bilgilerinin güvenlik altında tutulması önemli bir görevdir. İdeal olan kimlik bilgilerinin geliştirici iş istasyonlarında asla gösterilmemesi ve kaynak denetimine kaydedilmemesidir. Azure Key Vault kimlik bilgilerini, gizli dizileri ve diğer anahtarları güvenle depolamak için bir yol sağlar, ama bunları alabilmek için kodunuzun Key Vault'ta kimlik doğrulaması yapması gerekir. 

Azure Active Directory (Azure AD) hizmetindeki Azure kaynakları yönetilen hizmetleri bu sorunu çözer. Bu özellik, Azure hizmetlerine Azure AD üzerinde otomatik olarak yönetilen bir kimlik sağlar. Bu kimliği kullanarak, Key Vault da dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen tüm hizmetlerde kodunuzda kimlik bilgileri olmadan kimlik doğrulaması yapabilirsiniz.  Bir VM üzerinde çalışan kodunuz, yalnızca VM içinden erişilebilen iki uç noktasından belirteç isteyebilir. Bu hizmet hakkında daha ayrıntılı bilgi için [Azure kaynaklarına yönelik yönetilen kimlikler](../articles/active-directory/managed-identities-azure-resources/overview.md) genel bakış sayfasını gözden geçirin.   

## <a name="policies"></a>İlkeler

[Azure ilkeleri](../articles/azure-policy/azure-policy-introduction.md) , kuruluşunuzun [Windows VM](../articles/virtual-machines/windows/policy.md) 'Leri ve [Linux VM 'leri](../articles/virtual-machines/linux/policy.md)için istenen davranışı tanımlamak üzere kullanılabilir. Bir kuruluş, ilkeleri kullanarak kuruluş genelinde çeşitli kuralları ve kuralları zorunlu kılabilir. İstenen davranışın uygulanması, kuruluşun başarısına katkıda bulunmaya karşın riski azaltmaya yardımcı olabilir.

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi

[Rol tabanlı erişim denetimi 'ni (RBAC)](../articles/role-based-access-control/overview.md)kullanarak, ekipinizdeki görevleri ayırabilirsiniz ve yalnızca VM 'nizin işlerini gerçekleştirmesi için ihtiyaç duydukları kullanıcılara erişim miktarını verebilirsiniz. SANAL makinede herkes için sınırsız izin vermek yerine yalnızca belirli eylemlere izin verebilirsiniz. [Azure CLI](https://docs.microsoft.com/cli/azure/role)veya[Azure POWERSHELL](../articles/role-based-access-control/role-assignments-powershell.md)kullanarak [Azure Portal](../articles/role-based-access-control/role-assignments-portal.md)VM için erişim denetimi yapılandırabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
- [Linux](../articles/security/fundamentals/overview.md) veya [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md)için Azure Güvenlik Merkezi 'ni kullanarak sanal makine güvenliğini izleme adımlarını adım adım inceleyin.
