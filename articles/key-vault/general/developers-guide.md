---
title: Azure Key Vault Geliştirici Kılavuzu
description: Geliştiriciler, Microsoft Azure ortamındaki şifreleme anahtarlarını yönetmek için Azure Key Vault kullanabilir.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 08ac1ae09741b63648aec2b51b6a774a46b9af7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818449"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault Geliştirici Kılavuzu

Key Vault, uygulamalarınızın içinden hassas bilgilere güvenli bir şekilde erişmenizi sağlar:

- Anahtarlar, gizli diziler ve sertifikalar kodu kendiniz yazmak zorunda kalmadan korunur ve bunları uygulamalarınızdan kolayca kullanabilirsiniz.
- Müşterilerin, temel yazılım özellikleri sağlamaya odaklanabilmeniz için kendi anahtarlarını, sırlarını ve sertifikalarını yönetmesine ve yönetmesine izin verebilirsiniz. Bu şekilde, uygulamalarınız müşterilerinizin kiracı anahtarları, gizli dizileri ve sertifikaları için sorumluluğa ya da potansiyel sorumluluğa sahip olmayacaktır.
- Uygulamanız imzalama ve şifreleme için anahtarlar kullanabilir, ancak önemli yönetimi uygulamanız dışında tutar. Anahtarlar hakkında daha fazla bilgi için bkz. [anahtarlar hakkında](../keys/about-keys.md)
- Parolalar, erişim tuşları ve SAS belirteçleri gibi kimlik bilgilerini Key Vault gizli dizi olarak depolayarak yönetebilirsiniz, bkz. [gizlilikler hakkında](../secrets/about-secrets.md)
- Sertifikaları yönetin. Daha fazla bilgi için bkz. [Sertifikalar hakkında](../certificates/about-certificates.md)

Azure Key Vault hakkında daha fazla genel bilgi için bkz. [Key Vault nedir](overview.md).

## <a name="public-previews"></a>Genel önizlemeler

Düzenli olarak, yeni bir Key Vault özelliğinin genel önizlemesini yayınlarız. Genel Önizleme özelliklerini deneyin ve azurekeyvault@microsoft.com geri bildirim e-posta adresi ile düşüncelerinizi öğrenmemizi sağlayın.

## <a name="creating-and-managing-key-vaults"></a>Anahtar kasaları oluşturma ve yönetme

Diğer Azure hizmetlerine benzer Key Vault yönetimi, Azure Resource Manager hizmeti aracılığıyla yapılır. Azure Resource Manager, Azure için dağıtım ve yönetim hizmetidir. Azure hesabınızda kaynak oluşturma, güncelleştirme ve silme işlemlerini gerçekleştirmenizi sağlayan bir yönetim katmanı sunar. Daha fazla bilgi için bkz. [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

Yönetim katmanına erişim, [Azure rol tabanlı erişim denetimi](../../role-based-access-control/overview.md)tarafından denetlenir. Yönetim katmanı, yönetim veya denetim düzlemi olarak da bilinen Key Vault, erişim ilkeleri de dahil olmak üzere anahtar kasalarını ve özniteliklerini oluşturmanıza ve yönetmenize izin verir, ancak bu, veri düzlemine göre yönetilen anahtarlar, gizli diziler ve sertifikalar değildir. `Key Vault Contributor`Key Vault için yönetim erişimi vermek üzere önceden tanımlı rolü kullanabilirsiniz.     

**Anahtar Kasası yönetimi için API 'ler ve SDK 'lar:**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Başvuru](/cli/azure/keyvault)<br>[Hızlı Başlangıç](quick-create-cli.md)|[Başvuru](/powershell/module/az.keyvault)<br>[Hızlı Başlangıç](quick-create-powershell.md)|[Başvuru](/rest/api/keyvault/)|[Başvuru](/azure/templates/microsoft.keyvault/vaults)<br>[Hızlı Başlangıç](./vault-create-template.md)|[Başvuru](/dotnet/api/microsoft.azure.management.keyvault)|[Başvuru](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Başvuru](/java/api/com.microsoft.azure.management.keyvault)|[Başvuru](/javascript/api/@azure/arm-keyvault)|

Bkz. yükleme paketleri ve kaynak kodu için [Istemci kitaplıkları](client-libraries.md) .

Key Vault yönetim düzlemi hakkında daha fazla bilgi için bkz. [Azure Key Vault güvenlik özellikleri](security-features.md)

## <a name="authenticate-to-key-vault-in-code"></a>Kodda Key Vault kimlik doğrulaması

Key Vault, Azure AD kimlik doğrulamasını kullanarak erişim izni vermesini gerektirir. Azure AD güvenlik sorumlusu, bir Kullanıcı, bir uygulama hizmeti sorumlusu, [Azure kaynakları için yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md)veya herhangi bir güvenlik sorumlusu türü olabilir.

### <a name="authentication-best-practices"></a>En iyi kimlik doğrulama uygulamaları

Azure 'a dağıtılan uygulamalar için yönetilen kimlik kullanılması önerilir. Yönetilen kimliği desteklemeyen veya şirket içinde dağıtılan uygulamalar için Azure hizmetlerini kullanıyorsanız, [sertifikaya sahip hizmet sorumlusu](../../active-directory/develop/howto-create-service-principal-portal.md) olası bir alternatiftir. Bu senaryoda, sertifika Key Vault depolanmalıdır ve genellikle döndürülmelidir. Gizli hizmet sorumlusu, geliştirme ve test ortamları için ve yerel olarak veya Cloud Shell Kullanıcı sorumlusu kullanılarak kullanılması önerilir.

Ortam başına önerilen güvenlik sorumluları:
- **Üretim ortamı**:
  - Bir sertifikayla yönetilen kimlik veya hizmet sorumlusu
- **Test ve geliştirme ortamları**:
  - Yönetilen kimlik, sertifika veya gizli hizmet sorumlusu olan hizmet sorumlusu
- **Yerel geliştirme**:
  - Gizli anahtar içeren Kullanıcı sorumlusu veya hizmet sorumlusu

Yukarıdaki kimlik doğrulama senaryoları, **Azure Identity istemci kitaplığı** tarafından desteklenir ve Key Vault SDK 'lar ile tümleşiktir. Azure kimlik kitaplığı, kodunuzu değiştirmeden farklı ortamlar ve platformlar arasında kullanılabilir. Azure Identity Ayrıca Azure CLı, Visual Studio, Visual Studio Code ve diğer kişilerle Azure User 'a oturum açmış olan kimlik doğrulama belirtecini otomatik olarak alır. 

Azure Identity Client Libarary hakkında daha fazla bilgi için bkz.:

**Azure Identity istemci kitaplıkları**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK 'Sı Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> Şu anda etkin olan Key Vault .NET SDK sürüm 3 ' ü için önerilen [uygulama kimlik doğrulama kitaplığı](/dotnet/api/overview/azure/service-to-service-authentication) . Key Vault .NET SDK sürüm 4 ' e geçiş yapmak için lütfen [Azure. Identity geçiş kılavuzu 'na yönelik Appauthentication 'ı](/dotnet/api/overview/azure/app-auth-migration) izleyin.

Uygulamalarda Key Vault kimlik doğrulaması hakkında öğreticiler için bkz.:
- [.NET 'te VM 'de barındırılan uygulamada Key Vault kimlik doğrulaması](./tutorial-net-virtual-machine.md)
- [Python 'da VM 'de barındırılan uygulamada Key Vault kimlik doğrulaması](./tutorial-python-virtual-machine.md)
- [App Service Key Vault için kimlik doğrulama](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Anahtarları, sertifikaları ve gizli dizileri yönetme

Anahtarlar, gizlilikler ve sertifikalara erişim veri düzlemine göre denetlenir. Veri düzlemi erişim denetimi, yerel kasa erişim ilkeleri veya Azure RBAC kullanılarak yapılabilir.

**Anahtarlar API 'leri ve SDK 'Ları**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Başvuru](/cli/azure/keyvault/key)<br>[Hızlı Başlangıç](../keys/quick-create-cli.md)|[Başvuru](/powershell/module/az.keyvault/)<br>[Hızlı Başlangıç](../keys/quick-create-powershell.md)|[Başvuru](/rest/api/keyvault/#key-operations)|[Başvuru](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Hızlı Başlangıç](../keys/quick-create-template.md)|[Başvuru](/dotnet/api/azure.security.keyvault.keys)<br>[Hızlı Başlangıç](../keys/quick-create-net.md)|[Başvuru](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Hızlı Başlangıç](../keys/quick-create-python.md)|[Başvuru](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Hızlı Başlangıç](../keys/quick-create-java.md)|[Başvuru](/javascript/api/@azure/keyvault-keys/)<br>[Hızlı Başlangıç](../keys/quick-create-node.md)|

**Sertifikalar API 'leri ve SDK 'Ları**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Başvuru](/cli/azure/keyvault/certificate)<br>[Hızlı Başlangıç](../certificates/quick-create-cli.md)|[Başvuru](/powershell/module/az.keyvault)<br>[Hızlı Başlangıç](../certificates/quick-create-powershell.md)|[Başvuru](/rest/api/keyvault/#certificate-operations)|Yok|[Başvuru](/dotnet/api/azure.security.keyvault.certificates)<br>[Hızlı Başlangıç](../certificates/quick-create-net.md)|[Başvuru](/python/api/overview/azure/keyvault-certificates-readme)<br>[Hızlı Başlangıç](../certificates/quick-create-python.md)|[Başvuru](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Hızlı Başlangıç](../certificates/quick-create-java.md)|[Başvuru](/javascript/api/@azure/keyvault-certificates/)<br>[Hızlı Başlangıç](../certificates/quick-create-node.md)|

**Gizli dizileri API 'leri ve SDK 'Ları**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Başvuru](/cli/azure/keyvault/secret)<br>[Hızlı Başlangıç](../secrets/quick-create-cli.md)|[Başvuru](/powershell/module/az.keyvault/)<br>[Hızlı Başlangıç](../secrets/quick-create-powershell.md)|[Başvuru](/rest/api/keyvault/#secret-operations)|[Başvuru](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Hızlı Başlangıç](../secrets/quick-create-template.md)|[Başvuru](/dotnet/api/azure.security.keyvault.secrets)<br>[Hızlı Başlangıç](../secrets/quick-create-net.md)|[Başvuru](/python/api/overview/azure/keyvault-secrets-readme)<br>[Hızlı Başlangıç](../secrets/quick-create-python.md)|[Başvuru](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Hızlı Başlangıç](../secrets/quick-create-java.md)|[Başvuru](/javascript/api/@azure/keyvault-secrets/)<br>[Hızlı Başlangıç](../secrets/quick-create-node.md)|

Bkz. yükleme paketleri ve kaynak kodu için [Istemci kitaplıkları](client-libraries.md) .

Key Vault veri düzlemi güvenliği hakkında daha fazla bilgi için bkz. [Azure Key Vault güvenlik özellikleri](security-features.md).

### <a name="code-examples"></a>Kod örnekleri

Uygulamalarınızla Key Vault kullanan tüm örnekler için bkz.:

- [Kod örneklerini Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) -Azure Key Vault Için kod örnekleri. 

## <a name="how-tos"></a>Nasıl yapılır belgeleri

Aşağıdaki makaleler ve senaryolar Azure Key Vault çalışmak için göreve özgü rehberlik sağlar:

- [Güvenlik duvarının arkasındaki Key Vault erişme](access-behind-firewall.md) -anahtar kasasına erişmek için Anahtar Kasası istemci uygulamanızın çeşitli işlevlere ilişkin birden çok uç noktaya erişebilmesi gerekir.
- Key Vault- [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) 'tan sanal makinelere sertifika DAĞıTMA-Azure 'da VM 'de çalışan bir bulut uygulaması, bir sertifika gerektirir. Bu sertifikayı bugün bu sanal makineye nasıl alırsınız?
- [Azure Web App sertifikasını Key Vault aracılığıyla dağıtma](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Erişim ilkesi atama ([CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  [portalı](assign-access-policy-portal.md)). 
- [CLI ile geçici silme Key Vault kullanımı](./key-vault-recovery.md) , anahtar kasasının kullanımı ve yaşam döngüsü boyunca ve geçici silme etkin olan çeşitli Anahtar Kasası nesnelerinde size rehberlik eder.
- [Dağıtım sırasında güvenli değerleri (parolalar](../../azure-resource-manager/templates/key-vault-parameter.md) gibi) geçirme-dağıtım sırasında bir parametre olarak güvenli bir değer (parola gibi) iletmeniz gerektiğinde, bu değeri bir Azure Key Vault gizli olarak saklayabilir ve diğer kaynak yöneticisi şablonlarındaki değere başvurabilirsiniz.

## <a name="integrated-with-key-vault"></a>Key Vault ile tümleşik

Bu makaleler, Key Vault kullanan veya ile tümleştirilebilen diğer senaryolar ve hizmetlerle ilgilidir.

- [Rest 'de şifreleme](../../security/fundamentals/encryption-atrest.md) , kalıcı olduğunda verilerin kodlamasına (şifrelemeye) izin verir. Veri şifreleme anahtarları genellikle, erişimi daha fazla sınırlandırmak için Azure Key Vault bir anahtar şifreleme anahtarıyla şifrelenir.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) kendi kiracı anahtarınızı yöneticinize etmenizi sağlar. Örneğin, kiracı anahtarınızı Microsoft 'un yönetmesi yerine (varsayılan), kuruluşunuz için uygun olan belirli düzenlemelere uymak üzere kendi kiracı anahtarınızı yönetebilirsiniz. Kendi kiracı anahtarınızın yönetilmesi, kendi anahtarını getir (BYOK) olarak da bilinir.
- [Azure özel bağlantı hizmeti](private-link-service.md) , Azure hizmetlerine (örneğin, Azure Key Vault, Azure depolama ve Azure Cosmos DB) ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine sanal ağınızdaki özel bir uç nokta üzerinden erişmenizi sağlar.
- [Event Grid](../../event-grid/event-schema-key-vault.md) ile tümleştirme Key Vault, Anahtar Kasası 'nda depolanan bir gizli dizinin durumu değiştiğinde kullanıcılara bildirim gönderilmesini sağlar. Kesintileri engellemek için, parolaların yeni bir sürümünü uygulamalara dağıtabilir veya süre sonu gizli dizilerini döndürebilirsiniz.
- [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) gizli dizilerinizi Key Vault, istenmeyen erişimden koruyabilirsiniz.
- [Azure depolama 'ya bağlanmak için DataBricks 'te Key Vault depolanan gizli dizi kullanın](./integrate-databricks-blob-storage.md)
- Kubernetes üzerinde [gizli depolama CSI sürücüsü](./key-vault-integrate-kubernetes.md) için Azure Key Vault sağlayıcısını yapılandırın ve çalıştırın

## <a name="key-vault-overviews-and-concepts"></a>Key Vault genel bakış ve kavramlar

- [Key Vault geçici silme davranışı](soft-delete-overview.md) , silmenin yanlışlıkla mi yoksa bilerek mi olduğunu, silinen nesnelerin kurtarılmasına izin veren bir özelliği açıklar.
- [Key Vault istemci kısıtlama](overview-throttling.md) , sizi daraltma temel kavramlara yönlendirir ve uygulamanız için bir yaklaşım sunar.
- [Key Vault güvenliği](overview-security-worlds.md) , bölgeler ve güvenlik alanları arasındaki ilişkileri açıklar.

## <a name="social"></a>Sosyal

- [Key Vault blogu](/archive/blogs/kv/)
- [Key Vault Forumu](https://aka.ms/kvforum)