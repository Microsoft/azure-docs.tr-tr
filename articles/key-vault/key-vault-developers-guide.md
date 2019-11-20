---
title: Geliştirici Kılavuzu Azure Key Vault
description: Geliştiriciler, Microsoft Azure ortamındaki şifreleme anahtarlarını yönetmek için Azure Key Vault kullanabilir.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 1bfd156f9fbdb69766e4588a342a695fbd79ca3a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595307"
---
# <a name="azure-key-vault-developers-guide"></a>Geliştirici Kılavuzu Azure Key Vault

Key Vault, uygulamalarınızın içinden hassas bilgilere güvenli bir şekilde erişmenizi sağlar:

- Anahtarlar ve gizlilikler, kodu kendiniz yazmak zorunda kalmadan korunur ve bunları uygulamalarınızdan kolayca kullanabilirsiniz.
- Müşterilerinizin temel yazılım özelliklerini sağlamaya odaklanabilmeniz için kendi anahtarlarını sahip ve bunları yönetebileceksiniz. Bu şekilde, uygulamalarınız müşterilerinizin kiracı anahtarları ve gizli dizileri için sorumluluğa ya da potansiyel sorumluluğa sahip olmayacaktır.
- Uygulamanız imzalama ve şifreleme için anahtarlar kullanabilir, ancak çözümünüz coğrafi olarak dağıtılmış bir uygulama olarak uygun olacak şekilde uygulamanızda önemli yönetim sağlar.
- Key Vault Eylül 2016 sürümünden itibaren uygulamalarınız artık Key Vault sertifikaları yönetebilir. Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/rest/api/keyvault/about-keys--secrets-and-certificates).

Azure Key Vault hakkında daha fazla genel bilgi için bkz. [Key Vault nedir](key-vault-overview.md).

## <a name="public-previews"></a>Genel önizlemeler

Düzenli olarak, yeni bir Key Vault özelliğinin genel önizlemesini yayınlarız. Bunları deneyin ve azurekeyvault@microsoft.com, geri bildirim e-posta adresi ile düşüncelerinizi bize iletin.

## <a name="creating-and-managing-key-vaults"></a>Anahtar kasaları oluşturma ve yönetme

Azure Key Vault kimlik bilgilerini ve diğer anahtarlarla gizli dizileri güvenle depolamak için bir yol sağlar, ama bunları alabilmek için kodunuzun Key Vault'ta kimlik doğrulaması yapması gerekir. Azure kaynaklarına yönelik yönetilen kimlikler, Azure hizmetlerine Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik vererek bu sorunu daha kolay bir şekilde çözmenizi sağlar. Bu kimliği kullanarak, Key Vault da dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen tüm hizmetlerde kodunuzda kimlik bilgileri bulunmasına gerek kalmadan kimlik doğrulaması yapabilirsiniz. 

Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [yönetilen kimliklere genel bakış](../active-directory/managed-identities-azure-resources/overview.md). AAD ile çalışma hakkında daha fazla bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](../active-directory/develop/active-directory-integrating-applications.md).

Anahtar kasaınızdaki anahtarlar, gizlilikler veya sertifikalarla çalışmadan önce, aşağıdaki makalelerde açıklandığı gibi CLı, PowerShell, Kaynak Yöneticisi şablonları veya REST aracılığıyla anahtar kasanızı oluşturup yönetirsiniz:

- [CLı ile Anahtar Kasası oluşturma ve yönetme](key-vault-manage-with-cli2.md)
- [PowerShell ile Anahtar Kasası oluşturma ve yönetme](key-vault-overview.md)
- [Bir Anahtar Kasası oluşturma ve bir Azure Resource Manager şablonu aracılığıyla gizli dizi ekleme](../azure-resource-manager/resource-manager-template-keyvault.md)
- [REST ile anahtar kasaları oluşturma ve yönetme](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Key Vault kodlama

Programcılar için Key Vault yönetim sistemi çeşitli arabirimlerden oluşur. Bu bölüm, bazı dillerin ve bazı kod örneklerinin bağlantılarını içerir. 

### <a name="supported-programming-and-scripting-languages"></a>Desteklenen programlama ve betik oluşturma dilleri

#### <a name="rest"></a>REST

Key Vault kaynaklarınızın tamamına REST arabiriminden erişilebilir. Kasalar, anahtarlar, gizlilikler vb. 

[Key Vault REST API başvurusu](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Key Vault için .NET API başvurusu](/dotnet/api/microsoft.azure.keyvault).

.NET SDK 'sının 2. x sürümü hakkında daha fazla bilgi için [sürüm notlarına](key-vault-dotnet2api-release-notes.md)bakın.

#### <a name="java"></a>Java

[Key Vault için Java SDK](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

Node. js ' de, Key Vault yönetim API 'SI ve Key Vault nesne API 'SI ayrıdır. Aşağıdaki genel bakış makalesi, her ikisine de erişmenizi sağlar. 

[Node. js için Azure Key Vault modüller](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Python için Azure Key Vault kitaplıkları](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLı 2

[Key Vault için Azure CLı](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Key Vault için Azure PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quickstart-guides"></a>Hızlı Başlangıç Kılavuzu

- [Key Vault oluştur](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Node. js ' de Key Vault kullanmaya başlama](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Kod örnekleri

Uygulamalarınızla Key Vault kullanan tüm örnekler için bkz.:

- [Kod örneklerini Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) -Azure Key Vault Için kod örnekleri. 
- [Bir Web uygulamasından Azure Key Vault kullanın](quick-create-net.md) -Azure 'da bir web uygulamasından Azure Key Vault kullanmayı öğrenmenize yardımcı olmak için öğreticiden yararlanabilirsiniz. 

## <a name="how-tos"></a>Nasıl yapılır makaleleri

Aşağıdaki makaleler ve senaryolar Azure Key Vault çalışmak için göreve özgü rehberlik sağlar:

- [Anahtar Kasası KIRACı kimliğini değiştirme abonelik taşıma sonrasında](key-vault-subscription-move-fix.md) , Azure aboneliğinizi A Kiracısından b kiracısına taşıdığınızda mevcut anahtar kasalarınız, b kiracısındaki sorumlular (kullanıcılar ve uygulamalar) tarafından erişilemez. bu kılavuzu kullanarak bunu düzeltin.
- [Güvenlik duvarının arkasındaki Key Vault erişme](key-vault-access-behind-firewall.md) -anahtar kasasına erişmek için Anahtar Kasası istemci uygulamanızın çeşitli işlevlere ilişkin birden çok uç noktaya erişebilmesi gerekir.
- [Azure Key Vault IÇIN HSM korumalı anahtarlar oluşturma ve aktarma](key-vault-hsm-protected-keys.md) -bu, Azure Key Vault ile kullanmak IÇIN kendi HSM korumalı anahtarlarınızı planlayıp oluşturmanıza yardımcı olur.
- [Dağıtım sırasında güvenli değerleri (parolalar](../azure-resource-manager/resource-manager-keyvault-parameter.md) gibi) geçirme-dağıtım sırasında bir parametre olarak güvenli bir değer (parola gibi) iletmeniz gerektiğinde, bu değeri bir Azure Key Vault gizli olarak saklayabilir ve diğer kaynak yöneticisi değere başvurabilirsiniz şablondan.
- [SQL Server ile Genişletilebilir anahtar yönetimi için Key Vault nasıl kullanılır](https://msdn.microsoft.com/library/dn198405.aspx) -Azure Key Vault için SQL Server Bağlayıcısı SQL Server ve SQL-ın-a-VM ' y i bir Genişletilebilir anahtar YÖNETIMI (ekm) sağlayıcısı olarak Azure Key Vault hizmetini korumak için uygulamalar için şifreleme anahtarları bağlantısı; Saydam Veri Şifrelemesi, yedekleme şifrelemesi ve sütun düzeyinde şifreleme.
- [Key Vault 'Ten sanal makinelere sertifika dağıtma](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) -Azure 'da VM 'de çalışan bir bulut uygulaması, bir sertifikaya ihtiyaç duyuyor. Bu sertifikayı bugün bu sanal makineye nasıl alırsınız?
- [Uçtan uca anahtar döndürme ve denetleme ile Key Vault ayarlama](key-vault-key-rotation-log-monitoring.md) -bu, Azure Key Vault ile anahtar döndürmeyi ve denetlemeyi nasıl ayarlayacağınızı açıklar.
- [Key Vault aracılığıyla Azure Web App sertifikası dağıtmak]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) , [App Service sertifikası](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) teklifin bir parçası olarak Key Vault depolanan sertifikaları dağıtmaya yönelik adım adım yönergeler sağlar.
- [Birçok uygulamaya bir anahtar kasasına erişmek için Izin verme](key-vault-group-permissions-for-apps.md) Key Vault Access Control Policy en fazla 1024 girişi destekler. Ancak, Azure Active Directory bir güvenlik grubu oluşturabilirsiniz. Tüm ilişkili hizmet sorumlularını bu güvenlik grubuna ekleyin ve Key Vault için bu güvenlik grubuna erişim izni verin.
- Anahtar kasalarını Azure ile tümleştirmeyle ve kullanmaya yönelik daha fazla göreve özgü rehberlik için bkz. [Ryan Jones ' Azure Resource Manager şablon örnekleri Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [CLI ile geçici silme Key Vault kullanımı](key-vault-soft-delete-cli.md) , anahtar kasasının kullanımı ve yaşam döngüsü boyunca ve geçici silme etkin olan çeşitli Anahtar Kasası nesnelerinde size rehberlik eder.
- [PowerShell ile geçici silme Key Vault](key-vault-soft-delete-powershell.md) , bir anahtar kasasının kullanımı ve yaşam döngüsü ve yumuşak silme etkin olan çeşitli Anahtar Kasası nesneleri boyunca size rehberlik eder.

## <a name="integrated-with-key-vault"></a>Key Vault ile tümleşik

Bu makaleler, Key Vault kullanan veya ile tümleştirilebilen diğer senaryolar ve hizmetlerle ilgilidir.

- [Azure disk şifrelemesi](../security/fundamentals/encryption-overview.md) , işletim sistemi ve veri diskleri için birim şifrelemesi sağlamak üzere Windows 'un sektör standardı [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) özelliğini ve Linux 'un [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) özelliğini kullanır. Çözüm, Anahtar Kasası aboneliğinizdeki disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak üzere Azure Key Vault ile tümleşiktir. böylece, sanal makine disklerindeki tüm verilerin Azure depolamadaki geri kalanında şifrelenmesini sağlayabilirsiniz.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) hesapta depolanan verilerin şifrelenmesi için seçenek sağlar. Anahtar yönetimi için Data Lake Store, ana şifreleme anahtarlarınızı (MEKs) yönetmek için, Data Lake Store depolanan verilerin şifresini çözmek için gereken iki mod sağlar. Sizin için, MEKs 'leri Data Lake Store yönetebilir veya Azure Key Vault hesabınızı kullanarak MEKs 'in sahipliğini tutmayı seçebilirsiniz. Data Lake Store bir hesap oluştururken anahtar yönetiminin modunu belirtirsiniz.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) kendi kiracı anahtarınızı yöneticinize etmenizi sağlar. Örneğin, kiracı anahtarınızı Microsoft 'un yönetmesi yerine (varsayılan), kuruluşunuz için uygun olan belirli düzenlemelere uymak üzere kendi kiracı anahtarınızı yönetebilirsiniz. Kendi kiracı anahtarınızı yönetmek, kendi anahtarını getir veya BYOK olarak da adlandırılır.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault genel bakış ve kavramlar

- [Key Vault geçici silme davranışı](key-vault-ovw-soft-delete.md) , silmenin yanlışlıkla mi yoksa bilerek mi olduğunu, silinen nesnelerin kurtarılmasına izin veren bir özelliği açıklar.
- [Key Vault istemci kısıtlama](key-vault-ovw-throttling.md) , sizi daraltma temel kavramlara yönlendirir ve uygulamanız için bir yaklaşım sunar.
- [Key Vault depolama hesabı anahtarlarına genel bakış](key-vault-ovw-storage-keys.md) , Key Vault tümleştirme Azure depolama hesapları anahtarlarını açıklar.
- [Key Vault güvenliği](key-vault-ovw-security-worlds.md) , bölgeler ve güvenlik alanları arasındaki ilişkileri açıklar.

## <a name="social"></a>Sosyal

- [Key Vault blogu](https://aka.ms/kvblog)
- [Key Vault Forumu](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Destekleyici kitaplıklar

- [Microsoft Azure Key Vault Çekirdek Kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) , tanımlayıcılardan anahtar bulmaya ve anahtarlarla işlem gerçekleştirmeye yönelik **Ikey** ve **ıkeyresolver** arabirimlerini sağlar.
- [Microsoft Azure Key Vault uzantıları](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) Azure Key Vault için genişletilmiş yetenekler sağlar.
