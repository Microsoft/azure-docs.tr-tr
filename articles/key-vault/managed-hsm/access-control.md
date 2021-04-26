---
title: Azure yönetilen HSM erişim denetimi
description: Azure yönetilen HSM ve anahtarlar için erişim izinlerini yönetin. Yönetilen HSM için kimlik doğrulama ve yetkilendirme modelini ve HSM 'nizin güvenliğini sağlama.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: bea1ccf0777c6325bc86c15e0f88304c465d89c9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750293"
---
# <a name="managed-hsm-access-control"></a>Yönetilen HSM erişim denetimi

> [!NOTE]
> Key Vault kaynak sağlayıcısı iki kaynak türünü destekler: **kasa** ve **yönetilen HSM**'ler. Bu makalede açıklanan erişim denetimi yalnızca **yönetilen HSM**'ler için geçerlidir. Yönetilen HSM için erişim denetimi hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi ile Key Vault anahtarlarına, sertifikalara ve gizli anahtarlara erişim sağlama](../general/rbac-guide.md).

Azure Key Vault yönetilen HSM, şifreleme anahtarlarını koruyan bir bulut hizmetidir. Bu veriler hassas ve iş açısından kritik olduğundan, yalnızca yetkili uygulamalara ve kullanıcılara erişmesine izin vererek yönetilen HSM 'lerinizdeki erişimi güvenli hale getirmeniz gerekir. Bu makalede, yönetilen HSM erişim denetimi modeline bir genel bakış sunulmaktadır. Kimlik doğrulama ve yetkilendirmeyi açıklar ve yönetilen HSM 'lerinizde erişimin güvenliğini nasıl sağlayabileceğinizi açıklar.

## <a name="access-control-model"></a>Erişim denetimi modeli

Yönetilen bir HSM 'ye erişim iki arabirim aracılığıyla denetlenir: **Yönetim düzlemi** ve **veri düzlemi**. Yönetim düzlemi, HSM 'yi yönettiğiniz yerdir. Bu düzlemdeki işlemler, yönetilen HSM 'leri oluşturma ve silmeyi ve yönetilen HSM özelliklerini almayı içerir. Veri düzlemi, HSM ile desteklenen şifreleme anahtarları olan yönetilen bir HSM 'de depolanan verilerle birlikte çalıştığınız yerdir. Şifreleme işlemleri gerçekleştirmek için anahtarları ekleyebilir, silebilir, değiştirebilir ve kullanabilirsiniz, anahtarlara erişimi denetlemek için rol atamalarını yönetebilir, tam bir HSM yedeklemesi oluşturabilir, tam yedeklemeyi geri yükleyebilir ve veri düzlemi arabiriminden güvenlik etki alanını yönetebilirsiniz.

Her iki düzlemde yönetilen bir HSM 'ye erişmek için, tüm Arayanların uygun kimlik doğrulaması ve yetkilendirmesi olması gerekir. Kimlik doğrulama, arayanın kimliğini belirler. Yetkilendirme, çağıranın hangi işlemleri yürütebileceğini belirler. Bir çağıran, Azure Active Directory-Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik içinde tanımlanan [güvenlik sorumlularından](../../role-based-access-control/overview.md#security-principal) herhangi biri olabilir.

Her iki düzlem de kimlik doğrulaması için Azure Active Directory kullanır. Yetkilendirme için, farklı sistemleri aşağıdaki gibi kullanırlar
- Yönetim düzlemi, Azure rol tabanlı erişim denetimi (Azure RBAC) kullanır ve Azure Azure Resource Manager yerleşik bir yetkilendirme sistemidir 
- Veri düzlemi yönetilen bir HSM düzeyi RBAC (yönetilen HSM yerel RBAC) kullanır--yönetilen HSM düzeyinde uygulanan ve zorlanan bir yetkilendirme sistemi.

Yönetilen bir HSM oluşturulduğunda, istek sahibi ayrıca veri düzlemi yöneticilerinin bir listesini sağlar (tüm [güvenlik sorumluları](../../role-based-access-control/overview.md#security-principal) desteklenir). Yalnızca bu Yöneticiler, anahtar işlemleri gerçekleştirmek ve veri düzlemi rol atamalarını yönetmek (yönetilen HSM yerel RBAC) için yönetilen HSM veri düzlemine erişebiliyor.

Her iki düzlem için de izin modeli aynı sözdizimini kullanır, ancak farklı düzeylerde zorlanır ve rol atamaları farklı kapsamlar kullanır. Yönetim düzlemi Azure RBAC, Azure Resource Manager tarafından zorlanır ve veri düzlemi yönetilen HSM yerel RBAC, yönetilen HSM 'nin kendisi tarafından zorlanır.

> [!IMPORTANT]
> Yönetilen bir HSM 'ye güvenlik sorumlusu yönetim düzlemi erişimi verilmesi, bunlara erişim anahtarlarına veya veri düzlemi rol atamalarına yönetilen HSM yerel RBAC 'e herhangi bir erişim vermez. Bu yalıtım, yönetilen HSM 'de depolanan anahtarlara erişimi etkileyen ayrıcalıkların yanlışlıkla genişlemesine engel olmak için tasarımdır.

Örneğin, bir abonelik Yöneticisi (abonelikteki tüm kaynaklar için "katkıda bulunan" iznine sahip oldukları için), aboneliklerinde yönetilen bir HSM 'yi silebilir, ancak yönetilen HSM yerel RBAC aracılığıyla özel olarak verilen veri düzlemi erişimi yoksa, kendilerine veya başkalarının veri düzlemine erişmesini sağlamak için yönetilen HSM 'de anahtar erişimi veya rol atamasını yönetmez.

## <a name="azure-active-directory-authentication"></a>Azure Active Directory kimlik doğrulaması

Bir Azure aboneliğinde yönetilen bir HSM oluşturduğunuzda, aboneliğin Azure Active Directory kiracısıyla otomatik olarak ilişkilendirilir. Her iki düzlemdeki tüm çağıranlar bu kiracıya kaydedilmelidir ve yönetilen HSM 'ye erişmek için kimlik doğrulaması yapılmalıdır.

Uygulama, herhangi bir düzlemi çağrılmadan önce Azure Active Directory kimliğini doğrular. Uygulama, uygulama türüne bağlı olarak desteklenen herhangi bir [kimlik doğrulama yöntemini](../../active-directory/develop/authentication-vs-authorization.md) kullanabilir. Uygulama, erişim kazanmak için düzlemdeki bir kaynak için bir belirteç alır. Kaynak, Azure ortamına göre yönetim veya veri düzleminde bir uç noktadır. Uygulama belirteci kullanır ve yönetilen HSM uç noktasına bir REST API isteği gönderir. Daha fazla bilgi edinmek için [tüm kimlik doğrulama akışını](../../active-directory/develop/v2-oauth2-auth-code-flow.md)gözden geçirin.

Her iki düzlemde tek bir kimlik doğrulama mekanizması kullanımının çeşitli avantajları vardır:

- Kuruluşlar, kuruluştaki tüm yönetilen HSMs 'lere merkezi olarak erişimi denetleyebilir.
- Bir Kullanıcı ayrıldıklarında, kuruluştaki tüm yönetilen HSM 'lere anında erişimi kaybeder.
- Kuruluşlar, ek güvenlik için Multi-Factor Authentication 'ı etkinleştirmek gibi Azure Active Directory seçeneklerini kullanarak kimlik doğrulamasını özelleştirebilir.

## <a name="resource-endpoints"></a>Kaynak uç noktaları

Güvenlik sorumluları, uç noktalar aracılığıyla düzlemleri erişir. İki düzlemi için erişim denetimleri bağımsız olarak çalışır. Bir uygulamaya yönetilen bir HSM 'de anahtar kullanma izni vermek için, yönetilen HSM yerel RBAC kullanarak veri düzlemi erişimi verirsiniz. Yönetilen HSM kaynağına bir kullanıcı erişimi vermek için yönetilen HSM 'leri oluşturma, okuma, silme, taşıma ve Azure RBAC kullandığınız diğer özellikleri ve etiketleri düzenleme.

Aşağıdaki tabloda yönetim ve veri düzlemleri için uç noktalar gösterilmektedir.

| Erişim &nbsp; düzlemi | Erişim uç noktaları | Operations | Erişim denetimi mekanizması |
| --- | --- | --- | --- |
| Yönetim düzlemi | **Genel**<br> management.azure.com:443<br> | Yönetilen HSM 'ler oluşturun, okuyun, güncelleştirin, silin ve taşıyın<br>Yönetilen HSM etiketlerini ayarla | Azure RBAC |
| Veri düzlemi | **Genel**<br> &lt;HSM-Name &gt; . managedhsm.Azure.net:443<br> | **Anahtarlar**: şifre çözme, şifreleme,<br> sarmalama geri alma, sarmalama, doğrulama, imzalama, alma, listeleme, güncelleştirme, oluşturma, içeri aktarma, silme, yedekleme, geri yükleme, Temizleme<br/><br/> **Veri düzlemi rol yönetimi (yönetilen HSM yerel RBAC)**_: rol tanımlarını listeleme, rol atama, rol atamalarını silme, özel rolleri <br/> <br/> tanımlama_* yedekleme/geri yükleme **: yedekleme, geri yükleme, durum yedekleme/geri yükleme <br/> <br/> işlemleri** güvenlik etki alanı * *: güvenlik etki alanını indirme ve karşıya yükleme | Yönetilen HSM yerel RBAC |
|||||

## <a name="management-plane-and-azure-rbac"></a>Yönetim düzlemi ve Azure RBAC

Yönetim düzleminde, bir çağıranın yürütebileceği işlemleri yetkilendirmek için Azure RBAC 'yi kullanırsınız. Azure RBAC modelinde, her Azure aboneliğinin bir Azure Active Directory örneği vardır. Bu dizinden kullanıcılara, gruplara ve uygulamalara erişim izni verirsiniz. Azure aboneliğindeki Azure Resource Manager dağıtım modelini kullanan kaynakları yönetmek için erişim izni verilir. Erişim vermek için [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/install-classic-cli), [Azure PowerShell](/powershell/azureps-cmdlets-docs)veya [Azure Resource Manager REST API 'lerini](/rest/api/authorization/roleassignments)kullanın.

Bir kaynak grubunda bir Anahtar Kasası oluşturup Azure Active Directory kullanarak erişimi yönetebilirsiniz. Kullanıcılara veya gruplara bir kaynak grubundaki anahtar kasalarını yönetme yeteneği vermiş olursunuz. Uygun Azure rolleri atayarak erişimi belirli bir kapsam düzeyinde verirsiniz. Anahtar kasalarını yönetmek üzere bir kullanıcıya erişim izni vermek için, belirli bir kapsamdaki kullanıcıya önceden tanımlanmış bir `key vault Contributor` rol atarsınız. Aşağıdaki kapsamlar düzeyleri bir Azure rolüne atanabilir:

- **Yönetim grubu**: abonelik düzeyinde atanan bir Azure rolü, o yönetim grubundaki tüm abonelikler için geçerlidir.
- **Abonelik**: abonelik düzeyinde atanan bir Azure rolü, bu aboneliğin içindeki tüm kaynak grupları ve kaynaklar için geçerlidir.
- **Kaynak grubu**: kaynak grubu düzeyinde atanan bir Azure rolü, bu kaynak grubundaki tüm kaynaklar için geçerlidir.
- **Belirli kaynak**: belirli bir kaynak için atanan bir Azure rolü bu kaynak için geçerlidir. Bu durumda, kaynak belirli bir Anahtar Kasası olur.

Önceden tanımlanmış birkaç rol vardır. Önceden tanımlanmış bir rol gereksinimlerinize uygun değilse, kendi rolünüzü tanımlayabilirsiniz. Daha fazla bilgi için bkz. [Azure RBAC: yerleşik roller](../../role-based-access-control/built-in-roles.md).

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Veri düzlemi ve yönetilen HSM yerel RBAC

Bir rol atayarak belirli önemli işlemleri yürütmek için bir güvenlik sorumlusu erişimi verirsiniz. Her rol ataması için, bu atamanın uygulandığı bir rol ve kapsam belirtmeniz gerekir. Yönetilen HSM yerel RBAC için iki kapsam mevcuttur.

- **"/" veya "/Keys"**: HSM düzeyi kapsamı. Bu kapsamda bir rolün atandığı güvenlik sorumluları, yönetilen HSM 'deki tüm nesneler (anahtarlar) için rolde tanımlanan işlemleri gerçekleştirebilir.
- **"/Keys/ &lt; Key-Name &gt; "**: anahtar düzeyi kapsamı. Bu kapsamda bir rolün atandığı güvenlik sorumluları, yalnızca belirtilen anahtarın tüm sürümleri için bu rolde tanımlanan işlemleri gerçekleştirebilir.

## <a name="next-steps"></a>Sonraki adımlar

- Bir yöneticiye yönelik bir başlangıç öğreticisi için bkz. [YÖNETILEN HSM nedir?](overview.md)
- Rol yönetimi öğreticisi için bkz. [YÖNETILEN HSM yerel RBAC](role-management.md)
- Yönetilen HSM günlüğü için kullanım günlüğü hakkında daha fazla bilgi için bkz. [YÖNETILEN HSM günlüğü](logging.md)