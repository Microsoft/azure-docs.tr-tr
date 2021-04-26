---
title: Azure Key Vault'ta kimliği doğrulama
description: Azure Key Vault için kimlik doğrulaması yapmayı öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7d219b752b894bbce9815911658c804ecb850ea1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753443"
---
# <a name="authenticate-to-azure-key-vault"></a>Azure Key Vault'ta kimliği doğrulama

Azure Key Vault, gizli dizileri depolamanıza ve bunların dağıtımını, uygulamalarda kimlik bilgilerinin depolanması gereksinimini ortadan kaldıran merkezi bir güvenli bulut deposunda depolamanızı sağlar. Uygulamalar, bu gizli dizileri erişmek için çalışma zamanında yalnızca Key Vault kimlik doğrulaması gerektirir.

## <a name="app-identity-and-security-principals"></a>Uygulama kimliği ve güvenlik sorumluları

Key Vault kimlik doğrulaması, herhangi bir **güvenlik sorumlusunun** kimliğini kimlik doğrulamasından getirmekten sorumlu olan [Azure ACTIVE DIRECTORY (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md)ile birlikte çalışıyor.

Güvenlik sorumlusu, Azure kaynaklarına erişim isteyen bir Kullanıcı, Grup, hizmet veya uygulamayı temsil eden bir nesnedir. Azure her güvenlik sorumlusuna benzersiz bir **nesne kimliği** atar.

* Bir **Kullanıcı** güvenlik sorumlusu, Azure Active Directory bir profili olan bir bireyi tanımlar.

* Bir **Grup** güvenlik sorumlusu Azure Active Directory içinde oluşturulan bir kullanıcı kümesini tanımlar. Gruba atanan tüm roller veya izinler, Grup içindeki tüm kullanıcılara verilir.

* **Hizmet sorumlusu** , bir uygulama veya hizmet için bir kullanıcı veya Grup yerine bir kod parçası olan bir güvenlik sorumlusu türüdür. Hizmet sorumlusunun nesne KIMLIĞI, **ISTEMCI kimliği** olarak bilinir ve Kullanıcı adı gibi davranır. Hizmet sorumlusunun **istemci gizli anahtarı** , parolası gibi davranır.

Uygulamalar için hizmet sorumlusu almanın iki yolu vardır:

* Önerilir: uygulama için sistem tarafından atanan **yönetilen kimliği** etkinleştirin.

    Yönetilen kimlik ile Azure, uygulamanın hizmet sorumlusunu dahili olarak yönetir ve uygulamanın diğer Azure hizmetleriyle kimliğini otomatik olarak doğrular. Yönetilen kimlik, çeşitli hizmetlere dağıtılan uygulamalar için kullanılabilir.

    Daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](../../active-directory/managed-identities-azure-resources/overview.md). Ayrıca, belirli hizmetler (örneğin App Service, Azure Işlevleri, sanal makineler vb.) için yönetilen kimliğin nasıl etkinleştireceğinizi açıklayan makalelere bağlanan [Azure hizmetlerini destekleyen Azure hizmetleri](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)de bölümüne bakın.

* Yönetilen kimliği kullandıysanız, bunun yerine uygulamayı Azure AD kiracınızla **kaydedin** . [hızlı başlangıç: Azure Identity platformu ile uygulama kaydetme](../../active-directory/develop/quickstart-register-app.md). Kayıt Ayrıca, tüm kiracıların genelinde uygulamayı tanımlayan ikinci bir uygulama nesnesi oluşturur.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Güvenlik sorumlusunu Key Vault erişim yetkisi verme

Key Vault iki ayrı yetkilendirme düzeyiyle kullanılabilir:

- **Erişim ilkeleri** , bir Kullanıcı, Grup veya hizmet sorumlusunun var olan bir Key Vault kaynağı *içinde* gizli dizi, anahtar ve sertifikalara erişme yetkisi olup olmadığını denetler (bazen "veri düzlemi" işlemleri olarak adlandırılır). Erişim ilkeleri genellikle kullanıcılara, gruplara ve uygulamalara verilir.

    Erişim ilkeleri atamak için aşağıdaki makalelere bakın:

    - [Azure portalı](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Rol izinleri** , bir Kullanıcı, Grup veya hizmet sorumlusunun bir Key Vault kaynağı oluşturma, silme ve başka bir şekilde yönetme yetkisi olup olmadığını denetler (bazen "Yönetim düzlemi" işlemleri olarak adlandırılır). Bu tür roller genellikle yalnızca Yöneticiler için verilir.
 
    Rolleri atamak ve yönetmek için aşağıdaki makalelere bakın:

    - [Azure portalı](../../role-based-access-control/role-assignments-portal.md)
    - [Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Roller hakkında genel bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../../role-based-access-control/overview.md).


> [!IMPORTANT]
> En büyük güvenlik için, her zaman en az ayrıcalık sorumlusunu izleyin ve yalnızca en özel erişim ilkelerine ve gerekli rollere izin verin. 
    
## <a name="configure-the-key-vault-firewall"></a>Key Vault güvenlik duvarını yapılandırma

Varsayılan olarak, Key Vault genel IP adresleri aracılığıyla kaynaklara erişime izin verir. Daha fazla güvenlik için belirli IP aralıklarına, hizmet uç noktalarına, sanal ağlara veya özel uç noktalara erişimi de kısıtlayabilirsiniz.

Daha fazla bilgi için bkz. [erişim Azure Key Vault bir güvenlik duvarı arkasında](./access-behind-firewall.md).


## <a name="the-key-vault-authentication-flow"></a>Key Vault kimlik doğrulama akışı

1. Azure AD 'de kimlik doğrulaması yapmak için bir hizmet sorumlusu istekleri, örneğin:
    * Kullanıcı Kullanıcı adı ve parola kullanarak Azure portal oturum açar.
    * Bir uygulama, bir istemci KIMLIĞI ve gizli dizi ya da istemci sertifikası sunarak Azure REST API çağırır.
    * Yönetilen kimliğe sahip bir sanal makine gibi bir Azure kaynağı, erişim belirteci almak için [azure Instance Metadata Service (ıMDS)](../../virtual-machines/windows/instance-metadata-service.md) REST uç noktasıyla iletişim kurar.

1. Azure AD ile kimlik doğrulaması başarılı olursa, hizmet sorumlusuna bir OAuth belirteci verilir.

1. Hizmet sorumlusu Key Vault uç noktası (URI) üzerinden Key Vault REST API bir çağrı yapar.

1. Key Vault güvenlik duvarı aşağıdaki ölçütleri denetler. Herhangi bir ölçüt karşılanırsa, çağrıya izin verilir. Aksi takdirde, çağrı engellenir ve yasaklanmış bir yanıt döndürülür.

    * Güvenlik duvarı devre dışı bırakılır ve Key Vault genel uç noktasına genel İnternet üzerinden erişilebilir.
    * Çağıran [Key Vault güvenilir bir hizmettir](./overview-vnet-service-endpoints.md#trusted-services)ve güvenlik duvarının atlanmasını sağlar.
    * Arayan, IP adresi, sanal ağ veya hizmet uç noktası tarafından güvenlik duvarında listelenir.
    * Çağıran, yapılandırılmış bir özel bağlantı bağlantısı üzerinden Key Vault alabilir.    

1. Güvenlik Duvarı çağrıya izin veriyorsa, Key Vault hizmet sorumlusunun erişim belirtecini doğrulamak üzere Azure AD ' ı çağırır.

1. Key Vault, hizmet sorumlusunun istenen işlem için gerekli erişim ilkesine sahip olup olmadığını denetler. Aksi takdirde, Key Vault yasaklanmış bir yanıt döndürür.

1. Key Vault istenen işlemi gerçekleştirir ve sonucu döndürür.

Aşağıdaki diyagramda, bir Key Vault "gizli dizi" API 'sini çağıran bir uygulama işlemi gösterilmektedir:

![Azure Key Vault kimlik doğrulama akışı](../media/authentication/authentication-flow.png)

> [!NOTE]
> Gizli dizileri, sertifikalar ve anahtarlar için SDK istemcileri Key Vault, erişim belirteci olmadan Key Vault ek bir çağrı yapar ve bu da kiracı bilgilerinin alınması için 401 yanıtı oluşur. Daha fazla bilgi için bkz. [kimlik doğrulama, istekler ve yanıtlar](authentication-requests-and-responses.md)

## <a name="code-examples"></a>Kod örnekleri

Aşağıdaki tablo, söz konusu dilin Azure SDK kitaplıklarını kullanarak uygulama kodundaki Key Vault nasıl çalışabileceğini gösteren farklı makalelere bağlantı sağlar. Azure CLı ve Azure portal gibi diğer arabirimler kolaylık sağlaması için eklenmiştir.

| Key Vault gizli dizileri | Key Vault anahtarları | Key Vault sertifikaları |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET](../secrets/quick-create-net.md) | [.NET](../keys/quick-create-net.md) | [.NET](../certificates/quick-create-net.md) |
| [Java](../secrets/quick-create-java.md) | [Java](../keys/quick-create-java.md) | [Java](../certificates/quick-create-java.md) |
| [JavaScript](../secrets/quick-create-node.md) | [JavaScript](../keys/quick-create-node.md) | [JavaScript](../certificates/quick-create-node.md) | 
| [Azure portalı](../secrets/quick-create-portal.md) | [Azure portalı](../keys/quick-create-portal.md) | [Azure portalı](../certificates/quick-create-portal.md) |
| [Azure CLI](../secrets/quick-create-cli.md) | [Azure CLI](../keys/quick-create-cli.md) | [Azure CLI](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [ARM şablonu](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>Sonraki Adımlar

- [Key Vault erişim ilkesi sorunlarını giderme](troubleshooting-access-issues.md)
- [Key Vault REST API hata kodları](rest-error-codes.md)
- [Geliştirici Kılavuzu Key Vault](developers-guide.md)
- [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../../role-based-access-control/overview.md)
