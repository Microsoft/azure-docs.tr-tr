---
title: Azure Key Vault güvenliği | Microsoft Docs
description: Azure Key Vault, anahtarlar ve gizli dizileri için erişim izinlerini yönetin. Key Vault için kimlik doğrulama ve yetkilendirme modelini ve anahtar kasanızın güvenliğini sağlamayı içerir.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 728398aeec4715d15ebe44ae6d4e4bfa5f295df8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884779"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault güvenliği

Azure Key Vault kullanarak, bulutta sertifikalar, bağlantı dizeleri ve parolalar gibi şifreleme anahtarlarını ve gizli dizileri korumanız gerekir. Gizli ve iş açısından kritik verileri depolarken, kasalarınızın güvenliğini ve bunlarda depolanan verileri en üst düzeye çıkarmak için adımları uygulamanız gerekir. Bu makale, Azure Key Vault güvenliğini tasarlarken göz önünde bulundurmanız gereken kavramların bazılarını kapsar.

## <a name="identity-and-access-management"></a>Kimlik ve erişim yönetimi

Bir Azure aboneliğinde bir Anahtar Kasası oluşturduğunuzda, bu, aboneliğin Azure AD kiracısı ile otomatik olarak ilişkilendirilir. Bir kasadaki içeriği yönetmeye veya almaya çalışan herkes Azure AD tarafından kimlik doğrulamasından uymalıdır.

- Kimlik doğrulama, arayanın kimliğini belirler.
- Yetkilendirme, çağıranın hangi işlemleri gerçekleştirebileceğini belirler. Key Vault yetkilendirme, [rol tabanlı erişim denetimi](../role-based-access-control/overview.md) (RBAC) ve Azure Key Vault erişim ilkelerinin bir birleşimini kullanır.

### <a name="access-model-overview"></a>Erişim modeline genel bakış

Kasaların erişimi iki arabirim veya düzler aracılığıyla gerçekleşir. Bu düzlemler yönetim düzlemi ve veri düzledir.

- *Yönetim düzlemi* Key Vault kendisini yönettiğiniz yerdir ve bu, kasaların oluşturulması ve silinmesi için kullanılan arabirimdir. Ayrıca, Anahtar Kasası özelliklerini okuyabilir ve erişim ilkelerini yönetebilirsiniz.
- *Veri düzlemi* , bir anahtar kasasında depolanan verilerle çalışmanıza olanak sağlar. Anahtarlar, gizli diziler ve sertifikalar ekleyebilir, silebilir ve değiştirebilirsiniz.

Her iki düzlemde bir anahtar kasasına erişmek için, tüm çağıranların (kullanıcılar veya uygulamalar) kimliğinin doğrulanması ve yetkilendirilmiş olması gerekir. Her iki düzlem de kimlik doğrulaması için Azure Active Directory (Azure AD) kullanır. Yetkilendirme için, yönetim düzlemi rol tabanlı erişim denetimi (RBAC) kullanır ve veri düzlemi bir Key Vault erişim ilkesi kullanır.

Her iki düzlemde kimlik doğrulama için tek bir mekanizmanın çeşitli avantajları vardır:

- Kuruluşlar, kuruluştaki tüm anahtar kasaları için merkezi olarak erişimi denetleyebilir.
- Bir Kullanıcı ayrılsa bile, kuruluştaki tüm anahtar kasalarına erişimi anında kaybeder.
- Kuruluşlar, ek güvenlik için Multi-Factor Authentication 'ı etkinleştirmek gibi Azure AD 'deki seçenekleri kullanarak kimlik doğrulamasını özelleştirebilir

### <a name="managing-administrative-access-to-key-vault"></a>Key Vault için yönetici erişimini yönetme

Bir kaynak grubunda bir Anahtar Kasası oluşturduğunuzda, Azure AD 'yi kullanarak erişimi yönetirsiniz. Kullanıcılara veya gruplara bir kaynak grubundaki anahtar kasalarını yönetme yeteneği vermiş olursunuz. Uygun RBAC rollerini atayarak belirli bir kapsam düzeyinde erişim izni verebilirsiniz. Anahtar kasalarını yönetmek üzere bir kullanıcıya erişim izni vermek için, belirli bir kapsamdaki kullanıcıya `key vault Contributor` önceden tanımlanmış bir rol atarsınız. Aşağıdaki kapsamlar düzeyleri RBAC rolüne atanabilir:

- **Abonelik**: Abonelik düzeyinde atanan bir RBAC rolü, bu aboneliğin içindeki tüm kaynak grupları ve kaynaklar için geçerlidir.
- **Kaynak grubu**: Kaynak grubu düzeyinde atanan bir RBAC rolü, kaynak grubundaki tüm kaynaklar için geçerlidir.
- **Belirli kaynak**: Belirli bir kaynak için atanan RBAC rolü bu kaynak için geçerlidir. Bu durumda, kaynak belirli bir Anahtar Kasası olur.

Önceden tanımlanmış birkaç rol vardır. Önceden tanımlanmış bir rol gereksinimlerinize uygun değilse, kendi rolünüzü tanımlayabilirsiniz. Daha fazla bilgi için bkz [. RBAC: Yerleşik roller](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Bir kullanıcının `Contributor` bir Anahtar Kasası yönetim düzlemine izinleri varsa, Kullanıcı Key Vault erişim ilkesi ayarlayarak kendilerine veri düzlemine erişim izni verebilir. Anahtar kasalarınıza kimin rol erişimi `Contributor` olduğunu sıkı bir şekilde denetleyebilirsiniz. Anahtar kasalarınızı, anahtarlarınızı, sırları ve sertifikalarınızı yalnızca yetkili kişilerin erişebildiğinden ve yönetebilmesi için emin olun.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Key Vault verilerine erişimi denetleme

Key Vault erişim ilkeleri, izinleri anahtarlar, gizlilikler veya sertifikaya göre ayrı olarak verir. Gizli dizileri değil yalnızca anahtarlara bir kullanıcı erişimi verebilirsiniz. Anahtarlar, gizli diziler ve sertifikalar için erişim izinleri kasa düzeyinde yönetilir.

> [!IMPORTANT]
> Key Vault erişim ilkeleri, belirli bir anahtar, gizli dizi ya da sertifika gibi ayrıntılı, nesne düzeyindeki izinleri desteklemez. Bir kullanıcıya anahtar oluşturma ve silme izni verildiğinde, bu işlemleri ilgili anahtar kasasındaki tüm anahtarlar üzerinde gerçekleştirebilirler.

Bir Anahtar Kasası için erişim ilkeleri ayarlamak için [Azure Portal](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)veya [Key Vault yönetimi REST API 'lerini](https://msdn.microsoft.com/library/azure/mt620024.aspx)kullanın.

[Azure Key Vault için sanal ağ hizmet uç noktalarını](key-vault-overview-vnet-service-endpoints.md)kullanarak veri düzlemi erişimini kısıtlayabilirsiniz. Güvenlik [duvarları ve sanal ağ kurallarını](key-vault-network-security.md) ek bir güvenlik katmanı için yapılandırabilirsiniz.

## <a name="network-access"></a>Ağ erişimi

Hangi IP adreslerinin bunlara erişebileceğini belirterek kasalarınızın görünürlüğünü azaltabilirsiniz. Azure Key Vault için sanal ağ hizmet uç noktaları, belirtilen sanal ağa erişimi kısıtlayabilmeniz için izin verir. Uç noktalar Ayrıca bir IPv4 (Internet Protokolü sürüm 4) adres aralığı listesine erişimi sınırlamanıza olanak tanır. Anahtar kasanıza bu kaynakların dışından bağlanan herhangi bir kullanıcının erişimi reddedilir.

Güvenlik duvarı kuralları etkin olduktan sonra, kullanıcılar yalnızca istekleri izin verilen sanal ağlardan veya IPv4 adres aralıklarından başlatıldığında Key Vault verileri okuyabilir. Bu, Azure portal Key Vault erişmek için de geçerlidir. Kullanıcılar Azure portal bir anahtar kasasına gözatabilse de, istemci makineleri izin verilenler listesinde yoksa anahtarları, parolaları veya sertifikaları listelemeyebilir. Bu, diğer Azure hizmetleri tarafından Key Vault seçiciyi de etkiler. Kullanıcılar, güvenlik duvarı kuralları istemci makinesini engelliyorsa, anahtar kasalarının listesini görebilirler, ancak liste anahtarlarını göremez.

Azure Key Vault ağ adresi hakkında daha fazla bilgi için [sanal ağ hizmet uç noktalarını](key-vault-overview-vnet-service-endpoints.md) gözden geçirin Azure Key Vault

## <a name="monitoring"></a>İzleme

Key Vault günlük kaydı, kasanızda gerçekleştirilen etkinliklerle ilgili bilgileri kaydeder. Key Vault günlükleri:

- Başarısız istekler dahil tüm kimliği doğrulanmış REST API istekleri
  - Anahtar kasasındaki işlemler. Bu işlemler oluşturma, silme, erişim ilkelerini ayarlama ve Etiketler gibi Anahtar Kasası özniteliklerini güncelleştirme içerir.
  - Anahtar kasasındaki anahtarlar ve gizli diziler için aşağıdakiler de dahil olmak üzere işlemler:
    - Bu anahtarları veya parolaları oluşturma, değiştirme veya silme.
    - Anahtarları imzalama, doğrulama, şifreleme, şifre çözme, sarmalama ve kaldırma, gizli dizileri alma ve anahtarları ve gizli dizileri (ve bunların sürümlerini) listeleme.
- Bir 401 yanıtına neden olan kimliği doğrulanmamış istekler. Örnek olarak, hatalı biçimlendirilmiş veya geçerliliği olmayan ya da geçersiz bir belirtece sahip bir taşıyıcı belirteci olmayan isteklerdir.

Anahtar Kasası işleminden sonraki 10 dakika içinde günlüğe kaydetme bilgilerine erişilebilir. Bu, depolama hesabınızda günlüklerinizi yönetmek için kullanabileceğiniz bir hesaptır. 

- Günlüklerinize erişebilecek kişileri kısıtlayarak güvenliklerini sağlamak için standart Azure erişim denetimi yöntemlerini kullanın.
- Artık depolama hesabınızda tutmak istemediğiniz günlükleri silin.

Depolama hesaplarını güvenle yönetme önerisi için [Azure depolama Güvenlik Kılavuzu ' nu](../storage/common/storage-security-guide.md) gözden geçirin

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure Key Vault için sanal ağ hizmeti uç noktaları](key-vault-overview-vnet-service-endpoints.md)
- [RAPORDAKI Yerleşik roller](../role-based-access-control/built-in-roles.md)
- [Azure Key Vault için sanal ağ hizmeti uç noktaları](key-vault-overview-vnet-service-endpoints.md)
