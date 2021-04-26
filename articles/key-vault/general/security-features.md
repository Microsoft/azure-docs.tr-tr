---
title: Azure Key Vault güvenliğe genel bakış
description: Azure Key Vault yönelik güvenlik özelliklerine ve en iyi yöntemlere genel bakış.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: fe88933049ad39de57f879789e8c1b86ed7a54f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819465"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault güvenliği

Azure Key Vault, buluttaki şifreleme anahtarlarını ve gizli dizileri (sertifikalar, bağlantı dizeleri ve parolalar gibi) korur. Ancak gizli ve iş açısından kritik verileri depolarken, kasalarınızın güvenliğini ve bunlarda depolanan verileri en üst düzeye çıkarmak için adımları uygulamanız gerekir.

Bu makalede, Azure Key Vault yönelik güvenlik özelliklerine ve en iyi yöntemlere genel bakış sunulmaktadır.

> [!NOTE]
> Azure Key Vault güvenlik önerilerinin kapsamlı bir listesi için [Azure Key Vault güvenlik taban çizgisine](security-baseline.md)bakın.

## <a name="network-security"></a>Ağ güvenliği

Hangi IP adreslerinin bunlara erişebileceğini belirterek kasalarınızın görünürlüğünü azaltabilirsiniz. Azure Key Vault için sanal ağ hizmet uç noktaları, belirtilen sanal ağa erişimi kısıtlayabilmeniz için izin verir. Uç noktalar Ayrıca bir IPv4 (Internet Protokolü sürüm 4) adres aralığı listesine erişimi sınırlamanıza olanak tanır. Anahtar kasanıza bu kaynakların dışından bağlanan herhangi bir kullanıcının erişimi reddedilir.  Tüm ayrıntılar için bkz. [Azure Key Vault Için sanal ağ hizmeti uç noktaları](overview-vnet-service-endpoints.md)

Güvenlik duvarı kuralları etkin olduktan sonra, kullanıcılar yalnızca istekleri izin verilen sanal ağlardan veya IPv4 adres aralıklarından başlatıldığında Key Vault verileri okuyabilir. Bu, Azure portal Key Vault erişmek için de geçerlidir. Kullanıcılar Azure portal bir anahtar kasasına gözatabilse de, istemci makineleri izin verilenler listesinde yoksa anahtarları, parolaları veya sertifikaları listelemeyebilir. Uygulama adımları için bkz. [Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](network-security.md)

Azure özel bağlantı hizmeti, sanal ağınızdaki özel bir uç nokta üzerinden Azure Key Vault ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine erişmenizi sağlar. Azure özel uç noktası, Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Özel uç nokta, sanal ağınızdan bir özel IP adresi kullanarak hizmeti sanal ağınıza etkin bir şekilde getiriyor. Hizmete giden tüm trafik özel uç nokta aracılığıyla yönlendirilebilir, bu nedenle ağ geçitleri, NAT cihazları, ExpressRoute veya VPN bağlantıları ya da genel IP adresleri gerekmez. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır. Bir Azure kaynağı örneğine bağlanarak, erişim denetimi için en yüksek düzeyde ayrıntı düzeyi sağlayabilirsiniz.  Uygulama adımları için bkz. [Azure özel bağlantısı ile Key Vault tümleştirme](private-link-service.md)

## <a name="tls-and-https"></a>TLS ve HTTPS

- Key Vault ön ucu (veri düzlemi), çok kiracılı bir sunucusudur. Bu, farklı müşterilerden gelen anahtar kasalarının aynı genel IP adresini paylaşabileceği anlamına gelir. Yalıtıma ulaşmak için her HTTP isteğinin, diğer isteklerden bağımsız olarak kimliği doğrulanır ve yetkilendirilir.
- Güvenlik açıklarını bildirmek için TLS 'nin eski sürümlerini tanımlayabilir, ancak genel IP adresi paylaşıldığından, Anahtar Kasası hizmet ekibinin Aktarım düzeyinde her Anahtar Kasası için eski TLS sürümlerini devre dışı bırakmasına olanak yoktur.
- HTTPS protokolü, istemcinin TLS anlaşmasına katılmasına izin verir. **İstemciler en son TLS sürümünü zorunlu kılabilir** ve bir istemci bunu her seferinde, tüm bağlantı ilgili düzey korumayı kullanır. Key Vault hala eski TLS sürümlerini desteklediğinde, daha yeni TLS sürümlerini kullanan bağlantıların güvenliğine zarar vermez.
- TLS protokolünde bilinen güvenlik açıklarına karşın, saldırgan güvenlik açıklarına sahip bir TLS sürümü ile bağlantı başlattığında kötü amaçlı bir aracının anahtar kasasından bilgi ayıklamasına imkan tanıyan bilinen bir saldırı yoktur. Saldırganın kendisi için kimlik doğrulaması ve yetkilendirme yapması gerekir ve meşru istemciler her zaman son TLS sürümleriyle bağlandığında, kimlik bilgilerinin eski TLS sürümlerindeki güvenlik açıklarına sızma yolu yoktur.

## <a name="identity-management"></a>Kimlik yönetimi

Bir Azure aboneliğinde bir Anahtar Kasası oluşturduğunuzda, bu, aboneliğin Azure AD kiracısı ile otomatik olarak ilişkilendirilir. Bir kasadaki içeriği yönetmeye veya almaya çalışan herkes Azure AD tarafından kimlik doğrulamasından uymalıdır. Her iki durumda da, uygulamalar üç şekilde Key Vault erişebilir:

- **Yalnızca uygulama**: uygulama bir hizmet sorumlusunu veya yönetilen kimliği temsil eder. Bu kimlik, anahtar kasasından düzenli olarak sertifikalara, anahtarlara veya gizli uygulamalara erişmesi gereken uygulamalar için en yaygın senaryolardır. Bu senaryonun çalışması için, `objectId` uygulamanın erişim ilkesinde belirtilmesi gerekir ve `applicationId` belirtilmemelidir veya olması gerekir  `null` .
- **Yalnızca Kullanıcı**: Kullanıcı, kiracıda kayıtlı herhangi bir uygulamadan anahtar kasasına erişir. Bu tür erişimin örnekleri Azure PowerShell ve Azure portal içerir. Bu senaryonun çalışması için, `objectId` kullanıcının erişim ilkesinde belirtilmesi ve `applicationId` belirtilmemelidir veya olması _gerekir_ `null` .
- **Uygulama-Plus-Kullanıcı** (bazen _bileşik kimlik_ olarak adlandırılır): kullanıcının belirli bir uygulamadan anahtar kasasına erişmesi _ve_ uygulamanın, kullanıcının kimliğine bürünmek için, Kullanıcı adına kimlik doğrulaması (OBO) akışı kullanması gerekir. Bu senaryonun çalışması için, her ikisi `applicationId` de `objectId` erişim ilkesinde belirtilmelidir. , `applicationId` Gerekli uygulamayı tanımlar ve `objectId` kullanıcıyı tanımlar. Şu anda bu seçenek Azure RBAC veri düzlemi için kullanılamaz.

Tüm erişim türlerinde, uygulama Azure AD ile kimlik doğrulaması yapar. Uygulama, uygulama türüne göre desteklenen herhangi bir [kimlik doğrulama yöntemini](../../active-directory/develop/authentication-vs-authorization.md) kullanır. Uygulama, erişim izni vermek için düzlemdeki bir kaynak için bir belirteç alır. Kaynak, Azure ortamına göre yönetim veya veri düzleminde bir uç noktadır. Uygulama belirteci kullanır ve Key Vault bir REST API isteği gönderir. Daha fazla bilgi edinmek için [tüm kimlik doğrulama akışını](../../active-directory/develop/v2-oauth2-auth-code-flow.md)gözden geçirin.

Tüm ayrıntılar için bkz. [Key Vault kimlik doğrulaması temelleri](authentication-fundamentals.md)

## <a name="key-vault-authentication-options"></a>Key Vault kimlik doğrulama seçenekleri

Bir Azure aboneliğinde bir Anahtar Kasası oluşturduğunuzda, bu, aboneliğin Azure AD kiracısı ile otomatik olarak ilişkilendirilir. Her iki düzlemdeki tüm çağıranlar bu kiracıya kaydolmalıdır ve anahtar kasasına erişmek için kimliğini doğrular. Her iki durumda da, uygulamalar üç şekilde Key Vault erişebilir:

- **Yalnızca uygulama**: uygulama bir hizmet sorumlusunu veya yönetilen kimliği temsil eder. Bu kimlik, anahtar kasasından düzenli olarak sertifikalara, anahtarlara veya gizli uygulamalara erişmesi gereken uygulamalar için en yaygın senaryolardır. Bu senaryonun çalışması için, `objectId` uygulamanın erişim ilkesinde belirtilmesi gerekir ve `applicationId` belirtilmemelidir veya olması gerekir  `null` .
- **Yalnızca Kullanıcı**: Kullanıcı, kiracıda kayıtlı herhangi bir uygulamadan anahtar kasasına erişir. Bu tür erişimin örnekleri Azure PowerShell ve Azure portal içerir. Bu senaryonun çalışması için, `objectId` kullanıcının erişim ilkesinde belirtilmesi ve `applicationId` belirtilmemelidir veya olması _gerekir_ `null` .
- **Uygulama-Plus-Kullanıcı** (bazen _bileşik kimlik_ olarak adlandırılır): kullanıcının belirli bir uygulamadan anahtar kasasına erişmesi _ve_ uygulamanın, kullanıcının kimliğine bürünmek için, Kullanıcı adına kimlik doğrulaması (OBO) akışı kullanması gerekir. Bu senaryonun çalışması için, her ikisi `applicationId` de `objectId` erişim ilkesinde belirtilmelidir. , `applicationId` Gerekli uygulamayı tanımlar ve `objectId` kullanıcıyı tanımlar. Şu anda bu seçenek, Azure RBAC (Önizleme) veri düzlemi için kullanılamaz.

Tüm erişim türlerinde, uygulama Azure AD ile kimlik doğrulaması yapar. Uygulama, uygulama türüne göre desteklenen herhangi bir [kimlik doğrulama yöntemini](../../active-directory/develop/authentication-vs-authorization.md) kullanır. Uygulama, erişim izni vermek için düzlemdeki bir kaynak için bir belirteç alır. Kaynak, Azure ortamına göre yönetim veya veri düzleminde bir uç noktadır. Uygulama belirteci kullanır ve Key Vault bir REST API isteği gönderir. Daha fazla bilgi edinmek için [tüm kimlik doğrulama akışını](../../active-directory/develop/v2-oauth2-auth-code-flow.md)gözden geçirin.

Her iki düzlemde kimlik doğrulama için tek bir mekanizmanın çeşitli avantajları vardır:

- Kuruluşlar, kuruluştaki tüm anahtar kasaları için merkezi olarak erişimi denetleyebilir.
- Bir Kullanıcı ayrılsa bile, kuruluştaki tüm anahtar kasalarına erişimi anında kaybeder.
- Kuruluşlar, ek güvenlik için Multi-Factor Authentication 'ı etkinleştirmek gibi Azure AD 'deki seçenekleri kullanarak kimlik doğrulamasını özelleştirebilir.

## <a name="access-model-overview"></a>Erişim modeline genel bakış

Bir anahtar kasasına erişim, iki arabirim aracılığıyla denetlenir: **Yönetim düzlemi** ve **veri düzlemi**. Yönetim düzlemi Key Vault kendisini yönettiğiniz yerdir. Bu düzlemdeki işlemler, anahtar kasalarını oluşturmayı ve silmeyi, Key Vault özelliklerini almayı ve erişim ilkelerini güncelleştirmeyi içerir. Veri düzlemi, bir anahtar kasasında depolanan verilerle çalıştığınız yerdir. Anahtarlar, gizli diziler ve sertifikalar ekleyebilir, silebilir ve değiştirebilirsiniz.

Her iki düzlem de kimlik doğrulaması için [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) kullanır. Yönetim düzlemi, yetkilendirme için [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) kullanır ve veri düzlemi, [Key Vault veri düzlemi işlemleri için](./rbac-guide.md) [Key Vault erişim ilkesi](./assign-access-policy-portal.md) ve Azure RBAC kullanır.

Her iki düzlemde bir anahtar kasasına erişmek için, tüm çağıranların (kullanıcılar veya uygulamalar) uygun kimlik doğrulaması ve yetkilendirmesi olması gerekir. Kimlik doğrulama, arayanın kimliğini belirler. Yetkilendirme, çağıranın hangi işlemleri yürütebileceğini belirler. Key Vault kimlik doğrulaması, herhangi bir **güvenlik sorumlusunun** kimliğini kimlik doğrulamasından getirmekten sorumlu olan [Azure ACTIVE DIRECTORY (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md)ile birlikte çalışıyor.

Güvenlik sorumlusu, Azure kaynaklarına erişim isteyen bir Kullanıcı, Grup, hizmet veya uygulamayı temsil eden bir nesnedir. Azure her güvenlik sorumlusuna benzersiz bir **nesne kimliği** atar.

- Bir **Kullanıcı** güvenlik sorumlusu, Azure Active Directory bir profili olan bir bireyi tanımlar.
- Bir **Grup** güvenlik sorumlusu Azure Active Directory içinde oluşturulan bir kullanıcı kümesini tanımlar. Gruba atanan tüm roller veya izinler, Grup içindeki tüm kullanıcılara verilir.
- **Hizmet sorumlusu** , bir kullanıcı veya Grup yerine bir kod parçası olan bir uygulamayı veya hizmeti tanımlayan bir güvenlik sorumlusu türüdür. Hizmet sorumlusunun nesne KIMLIĞI, **ISTEMCI kimliği** olarak bilinir ve Kullanıcı adı gibi davranır. Hizmet sorumlusunun **istemci gizli** dizisi veya **sertifikası** , parolası gibi davranır. Birçok Azure hizmeti, [yönetilen kimliğin](../../active-directory/managed-identities-azure-resources/overview.md) **istemci kimliği** ve **sertifika** otomatik yönetimiyle atanmasını destekler. Yönetilen kimlik, Azure 'da kimlik doğrulaması için en güvenli ve önerilen seçenektir.

Key Vault kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure Key Vault kimlik](authentication.md) doğrulama

## <a name="privileged-access"></a>Ayrıcalıklı erişim

Yetkilendirme, çağıranın hangi işlemleri gerçekleştirebileceğini belirler. Key Vault yetkilendirme, [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) ve Azure Key Vault erişim ilkelerinin bir birleşimini kullanır.

Kasaların erişimi iki arabirim veya düzler aracılığıyla gerçekleşir. Bu düzlemler yönetim düzlemi ve veri düzledir.

- *Yönetim düzlemi* Key Vault kendisini yönettiğiniz yerdir ve bu, kasaların oluşturulması ve silinmesi için kullanılan arabirimdir. Ayrıca, Anahtar Kasası özelliklerini okuyabilir ve erişim ilkelerini yönetebilirsiniz.
- *Veri düzlemi* , bir anahtar kasasında depolanan verilerle çalışmanıza olanak sağlar. Anahtarlar, gizli diziler ve sertifikalar ekleyebilir, silebilir ve değiştirebilirsiniz.

Uygulamalar, uç noktalar aracılığıyla düzlemleri erişir. İki düzlemi için erişim denetimleri bağımsız olarak çalışır. Bir uygulamaya anahtar kasasındaki anahtarları kullanma izni vermek için, Key Vault erişim ilkesi veya Azure RBAC kullanarak veri düzlemi erişimi verirsiniz. Kullanıcıya Key Vault özelliklerine ve etiketlere yönelik okuma erişimi vermek, ancak verilere (anahtarlar, gizlilikler veya sertifikalar) erişmek için Azure RBAC ile yönetim düzlemi erişimi verirsiniz.

Aşağıdaki tabloda yönetim ve veri düzlemleri için uç noktalar gösterilmektedir.

| Erişim &nbsp; düzlemi | Erişim uç noktaları | Operations | Erişim &nbsp; denetimi mekanizması |
| --- | --- | --- | --- |
| Yönetim düzlemi | **Genel**<br> management.azure.com:443<br><br> **Azure Çin 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure ABD kamu:**<br> management.usgovcloudapi.net:443<br><br> **Azure Almanya:**<br> management.microsoftazure.de:443 | Anahtar kasaları oluşturun, okuyun, güncelleştirin ve silin<br><br>Key Vault erişim ilkelerini ayarlama<br><br>Key Vault etiketlerini ayarla | Azure RBAC |
| Veri düzlemi | **Genel**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Çin 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure ABD kamu:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Almanya:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Anahtarlar: şifreleme, şifre çözme, wrapKey, unwrapKey, imzala, doğrula, alma, listeleme, oluşturma, güncelleştirme, içeri aktarma, silme, kurtarma, yedekleme, geri yükleme, Temizleme<br><br> Sertifikalar: managecontacts, getısers, listissuers, setısers, silme, yönetim verenler, alma, listeleme, oluşturma, içeri aktarma, güncelleştirme, silme, kurtarma, yedekleme, geri yükleme, Temizleme<br><br>  Gizlilikler: Al, Listele, ayarla, Sil, kurtar, Yedekle, geri yükle, temizle | Key Vault erişim ilkesi veya Azure RBAC |

### <a name="managing-administrative-access-to-key-vault"></a>Key Vault için yönetici erişimini yönetme

Bir kaynak grubunda bir Anahtar Kasası oluşturduğunuzda, Azure AD 'yi kullanarak erişimi yönetirsiniz. Kullanıcılara veya gruplara bir kaynak grubundaki anahtar kasalarını yönetme yeteneği vermiş olursunuz. Uygun Azure rollerini atayarak belirli bir kapsam düzeyinde erişim izni verebilirsiniz. Anahtar kasalarını yönetmek üzere bir kullanıcıya erişim izni vermek için, belirli bir kapsamdaki kullanıcıya önceden tanımlanmış bir `key vault Contributor` rol atarsınız. Aşağıdaki kapsamlar düzeyleri bir Azure rolüne atanabilir:

- **Abonelik**: abonelik düzeyinde atanan bir Azure rolü, bu aboneliğin içindeki tüm kaynak grupları ve kaynaklar için geçerlidir.
- **Kaynak grubu**: kaynak grubu düzeyinde atanan bir Azure rolü, bu kaynak grubundaki tüm kaynaklar için geçerlidir.
- **Belirli kaynak**: belirli bir kaynak için atanan bir Azure rolü bu kaynak için geçerlidir. Bu durumda, kaynak belirli bir Anahtar Kasası olur.

Önceden tanımlanmış birkaç rol vardır. Önceden tanımlanmış bir rol gereksinimlerinize uygun değilse, kendi rolünüzü tanımlayabilirsiniz. Daha fazla bilgi için bkz. [Azure RBAC: yerleşik roller](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Bir kullanıcının bir `Contributor` Anahtar Kasası yönetim düzlemine izinleri varsa, kullanıcı Key Vault erişim ilkesi ayarlayarak kendilerine veri düzlemine erişim izni verebilir. `Contributor`Anahtar kasalarınıza kimin rol erişimi olduğunu sıkı bir şekilde denetleyebilirsiniz. Anahtar kasalarınızı, anahtarlarınızı, sırları ve sertifikalarınızı yalnızca yetkili kişilerin erişebildiğinden ve yönetebilmesi için emin olun.

### <a name="controlling-access-to-key-vault-data"></a>Key Vault verilerine erişimi denetleme

Key Vault erişim ilkeleri, izinleri anahtarlar, gizlilikler veya sertifikaya göre ayrı olarak verir. Gizli dizileri değil yalnızca anahtarlara bir kullanıcı erişimi verebilirsiniz. Anahtarlar, gizli diziler ve sertifikalar için erişim izinleri kasa düzeyinde yönetilir.

> [!IMPORTANT]
> Key Vault erişim ilkeleri, belirli bir anahtar, gizli dizi ya da sertifika gibi ayrıntılı, nesne düzeyindeki izinleri desteklemez. Bir kullanıcıya anahtar oluşturma ve silme izni verildiğinde, bu işlemleri ilgili anahtar kasasındaki tüm anahtarlar üzerinde gerçekleştirebilirler.

Bir Anahtar Kasası için erişim ilkeleri ayarlayabilirsiniz [Azure Portal](assign-access-policy-portal.md), [Azure CLI](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)veya [Key Vault yönetimi REST API 'lerini](/rest/api/keyvault/)kullanabilirsiniz.

## <a name="logging-and-monitoring"></a>Günlüğe kaydetme ve izleme

Key Vault günlük kaydı, kasanızda gerçekleştirilen etkinliklerle ilgili bilgileri kaydeder. Tüm ayrıntılar için bkz. [Key Vault günlüğe kaydetme](logging.md).

Anahtar Kasası 'nda depolanan bir anahtarın, sertifikanın veya bir parolanın durumu değiştiğinde bildirim almak için Key Vault Event Grid tümleştirebilirsiniz. Ayrıntılar için bkz. [Azure Event Grid Ile izleme Key Vault](event-grid-overview.md)

Ayrıca, hizmetinizin istendiği gibi çalıştığından emin olmak için anahtar kasanızın sistem durumunu izlemek önemlidir. Bunun nasıl yapılacağını öğrenmek için bkz. [Azure Key Vault Için izleme ve uyarı](alert.md)verme.

## <a name="backup-and-recovery"></a>Yedekleme ve kurtarma

Azure Key Vault geçici silme ve Temizleme koruması, silinen kasaların ve kasa nesnelerinin kurtarılmasına olanak tanır. Tüm ayrıntılar için bkz. [Azure Key Vault geçici genel bakış](soft-delete-overview.md).

Ayrıca, bir kasadaki nesnelerin güncelleştirilmesi/silinmesi/oluşturulması sırasında kasanızın düzenli olarak yeniden yedeklenmesini gerçekleştirmeniz gerekir.  

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure Key Vault güvenlik temeli](security-baseline.md)
- [En iyi Azure Key Vault uygulamalar](security-baseline.md)
- [Azure Key Vault için sanal ağ hizmet uç noktaları](overview-vnet-service-endpoints.md)
- [Azure RBAC: yerleşik roller](../../role-based-access-control/built-in-roles.md)