---
title: Kaynakları yönetim gruplarıyla düzenleme-Azure Idare
description: Yönetim grupları, izinlerinin nasıl çalıştığı ve bu grupların nasıl kullanıldığı hakkında bilgi edinin.
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.date: 04/22/2019
ms.topic: overview
ms.openlocfilehash: 7e121ed256e04332ca7fd33c9fc48cd2bc7bae03
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960193"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Kaynaklarınızı Azure yönetim gruplarıyla düzenleme

Kuruluşunuzda birden fazla abonelik varsa bu abonelikler için verimli bir şekilde erişim, ilke ve uyumluluk yönetimi gerçekleştirmek isteyebilirsiniz. Azure yönetim grupları, aboneliklerin üzerinde bir kapsam düzeyi sunar. Abonelikleri "yönetim grupları" adlı kapsayıcılarla düzenler ve idare koşullarınızı bu yönetim gruplarına uygularsınız. Bir yönetim grubu içindeki aboneliklerin tümü otomatik olarak yönetim grubuna uygulanmış olan koşulları devralır. Yönetim grupları, sahip olabileceğiniz abonelik türüne bakılmaksızın kurumsal düzeyde yönetimi büyük ölçekte sunar. Tek bir yönetim grubu içindeki tüm abonelikler aynı Azure Active Directory kiracısına güvenmelidir.

Örneğin, sanal makine (VM) oluşturma işlemi için kullanılabilir olan bölgeleri sınırlayan bir yönetim grubuna ilkeleri uygulayabilirsiniz. Bu ilke, yalnızca o bölgede VM oluşturulmasına izin vererek söz konusu yönetim grubu altındaki tüm yönetim gruplarına, aboneliklere ve kaynaklara uygulanır.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Yönetim grupları ve abonelikler hiyerarşisi

Birleşik ilke ve erişim yönetimi için kaynaklarınızı bir hiyerarşi altında düzenlemek amacıyla yönetim grupları ve aboneliklerden oluşan esnek bir yapı inşa edebilirsiniz. Aşağıdaki diyagramda, yönetim grupları kullanılarak idare amaçlı bir hiyerarşi oluşturma örneği gösterilmektedir.

![Yönetim grubu hiyerarşi ağacı örneği](./media/tree.png)

İlke uygulayan bir hiyerarşi oluşturabilirsiniz. Örneğin bu, “Üretim” adlı grupta VM konumlarını ABD Batı Bölgesiyle sınırlayan bir ilke olabilir. Bu ilke, o yönetim grubu altındaki her iki EA aboneliğine devredilir ve o abonelikler altındaki tüm VM’ler için geçerli olur. Bu güvenlik ilkesi kaynak veya abonelik sahibi tarafından değiştirilemez ve bu da idarenin geliştirilmesine olanak tanır.

Yönetim gruplarını kullanacağınız başka bir senaryo ise birden fazla aboneliğe kullanıcı erişimi sağlamaktır. Birden çok aboneliği söz konusu yönetim grubu altına taşıyarak, yönetim grubu üzerinde tüm aboneliklere erişimi devralacak bir [rol tabanlı erişim denetimi](../../role-based-access-control/overview.md) (RBAC) ataması oluşturabilirsiniz.
Yönetim grubunda bir atama olması, farklı abonelikler üzerinde RBAC komut dosyası kullanmak yerine kullanıcıların ihtiyaç duydukları her şeye erişmesini sağlayabilir.

### <a name="important-facts-about-management-groups"></a>Yönetim grupları hakkında önemli bilgiler

- Tek bir dizinde 10.000 yönetim grubu desteklenebilir.
- Bir yönetim grubu en fazla altı derinlik düzeyini destekleyebilir.
  - Bu sınır, Kök düzeyini veya abonelik düzeyini içermez.
- Her yönetim grubu ve abonelik yalnızca bir üst öğeyi destekler.
- Her yönetim grubunda birçok alt öğe olabilir.
- Tüm abonelikler ve yönetim grupları, her bir dizindeki tek bir hiyerarşi içinde yer alır. Bkz. [Kök yönetim grubu hakkında önemli bilgiler](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Her dizin için kök yönetim grubu

Her dizinde "Kök" yönetim grubu olarak adlandırılan tek bir üst düzey yönetim grubu bulunur.
Diğer tüm yönetim grupları ve abonelikler hiyerarşide en üstte yer alan bu kök yönetim grubunun altındadır. Bu kök yönetim grubu, genel ilkelerin ve RBAC atamalarının dizin düzeyinde uygulanmasını sağlar. Başlangıçta Azure AD Genel Yöneticisinin bu kök grubunun Kullanıcı Erişimi Yönetici rolüne [kendisini yükseltmesi gerekir](../../role-based-access-control/elevate-access-global-admin.md). Yönetici, erişimi yükselttikten sonra hiyerarşiyi yönetmek için diğer dizin kullanıcılarına veya gruplara herhangi bir RBAC rolü atayabilir. Yönetici olarak, kendi hesabınızı kök yönetim grubunun sahibi olarak atayabilirsiniz.

### <a name="important-facts-about-the-root-management-group"></a>Kök yönetim grubu hakkında önemli bilgiler

- Varsayılan olarak kök yönetim grubunun görünen adı, **Kiracı kök grubu**’dur. Kimlik, Azure Active Directory Kimliği’dir.
- Görünen adı değiştirmek için hesabınızın, kök yönetim grubunun Sahip veya Katkıda Bulunan rolüne atanması gerekir. Adı değiştirme adımları için bkz. [Yönetim grubunun adını değiştirme](manage.md#change-the-name-of-a-management-group).
- Diğer yönetim gruplarının aksine kök yönetim grubu taşınamaz veya silinemez.  
- Tüm abonelikler ve yönetim grupları, dizinin içindeki bir kök yönetim grubu altında birleşir.
  - Dizindeki tüm kaynaklar, genel yönetim için kök yönetim grubu altında birleşir.
  - Yeni abonelikler, oluşturulduğunda kök yönetim grubuna otomatik olarak eklenir.
- Tüm Azure müşterileri kök yönetim grubunu görebilir ancak tüm müşteriler o kök yönetim grubunu yönetmek için erişime sahip değildir.
  - Bir aboneliğe erişimi olan herkes bu aboneliğin hiyerarşide bulunduğu bağlamı görebilir.  
  - Kök yönetim grubuna hiç kimsenin varsayılan erişimi yoktur. Erişim kazanmak için yalnızca Azure AD Genel Yöneticileri kendi rollerini yükseltebilir.  Kök yönetim grubuna erişim elde ettikten sonra, genel yöneticiler bunu yönetecek kullanıcılara herhangi bir RBAC rolü atayabilir.  

> [!IMPORTANT]
> Kök yönetim grubu grubunda yapılan herhangi bir kullanıcı erişimi ataması veya ilke ataması, **dizin içindeki tüm kaynaklara uygulanır**.
> Bu nedenle, tüm müşterilerin bu kapsamda tanımlanmış öğelere sahip olma gereksinimini değerlendirmesi gerekir.
> Kullanıcı erişimi ve ilke atamaları yalnızca bu kapsamda "Sahip Olunması Zorunlu" olmalıdır.  

## <a name="initial-setup-of-management-groups"></a>Yönetim gruplarının ilk ayarı

Herhangi bir kullanıcı yönetim gruplarını kullanmaya başladığında gerçekleştirilen bir ilk ayar işlemi vardır. İlk adım, dizinde kök yönetim grubunun oluşturulmasıdır. Bu grup oluşturulduktan sonra dizinde mevcut olan tüm abonelikler, kök yönetim grubunun alt öğesi yapılır. Bu işlemin yapılmasının nedeni, bir dizin içinde yalnızca bir yönetim grubu hiyerarşisi olmasını sağlamaktır. Dizin içinde tek hiyerarşi olması, yönetim müşterilerinin dizindeki diğer müşteriler tarafından atlanamayacak genel erişim ve ilkeler uygulamasına olanak tanır. Kökte atanan her şey hiyerarşinin tamamına uygulanır. Buna söz konusu Azure AD Kiracısı içindeki tüm yönetim grupları, abonelikler, kaynak grupları ve kaynaklar dahildir.

## <a name="trouble-seeing-all-subscriptions"></a>Tüm abonelikler görüntülenirken oluşan sorun

25 Haziran 2018’den önce, önizlemenin ilk aşamasında yönetim gruplarını kullanmaya başlayan birkaç dizin, tüm aboneliklerin hiyerarşi içinde yer almamasıyla ilgili bir hatayla karşılaşabiliyordu. Tüm abonelikleri hiyerarşiye almaya yönelik işlem, dizindeki kök yönetim grubunda bir rol veya ilke ataması yapıldıktan sonra gerçekleştiriliyordu. 

### <a name="how-to-resolve-the-issue"></a>Sorunu çözme

Bu sorunu çözmek için kullanabileceğiniz iki seçenek vardır.

1. Kök yönetim grubundan tüm Rol ve İlke atamalarını kaldırma
   1. Kök yönetim grubundan bir ilke ve rol atamasını kaldırdığınızda, bir sonraki gece döngüsünde hizmet tüm abonelikleri hiyerarşiye geriye dönük olarak doldurur.  Bu işlemde, tüm kiracı aboneliklerine yanlışlıkla erişim verilmediğinden ve ilke ataması yapılmadığından emin olunur.
   1. Hizmetlerinizi etkilemeden bu işlemi yapmanın en iyi yolu, rolü veya ilke atamasını Kök yönetim grubunun bir alt düzeyine uygulamaktır. Daha sonra tüm atamaları kök kapsamından çıkarabilirsiniz.
1. Geriye dönük doldurma işlemini başlatmak için doğrudan API’yi çağırın
   1. Dizindeki herhangi bir müşteri *TenantBackfillStatusRequest* veya *StartTenantBackfillRequest* API’lerini çağırabilir. StartTenantBackfillRequest API’si çağrıldığında tüm abonelikleri hiyerarşiye taşımanın ilk kurulum işlemini başlatır. Ayrıca bu işlem, tüm yeni abonelikleri kök yönetim grubunun alt öğesi olmaya zorlama işlemini de başlatır. Bu işlem, kök düzeyde hiçbir atama değiştirilmeden yapılabilir. API'yi çağırarak, kökteki her ilke veya erişim atamasının tüm aboneliklere uygulanmasının sorun olmadığını belirtmiş olursunuz.

Bu geriye dönük doldurma işlemi hakkında sorularınız varsa şu adresten bize ulaşın: managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Yönetim grubu erişimi

Azure yönetim grupları tüm kaynak erişimleri ve rol tanımları için [Azure Rol Tabanlı Erişim Denetimi’ni (RBAC)](../../role-based-access-control/overview.md) destekler.
Bu izinler, hiyerarşide mevcut olan alt kaynaklara devredilir. Herhangi bir yerleşik RBAC rolü, hiyerarşiyi kaynaklara devredecek bir yönetim grubuna atanabilir.
Örneğin, VM katılımcısı adlı RBAC rolü bir yönetim grubuna atanabilir. Bu rolün yönetim grubu üzerinde herhangi bir eylemi yoktur ancak o yönetim grubu altındaki tüm VM’lere devredilir.

Aşağıdaki grafikte rollerin listesi ve yönetim gruplarında desteklenen eylemler gösterilmektedir.

| RBAC Rol Adı             | Oluşturma | Yeniden Adlandır | Taşı** | Sil | Erişim Ata | İlke Ata | Okuma  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Sahip                       | X      | X      | X      | X      | X             | X             | X     |
|Katılımcı                 | X      | X      | X      | X      |               |               | X     |
|MG Katılımcısı*             | X      | X      | X      | X      |               |               | X     |
|Okuyucu                      |        |        |        |        |               |               | X     |
|MG Okuyucusu*                  |        |        |        |        |               |               | X     |
|Kaynak İlkesine Katkıda Bulunan |        |        |        |        |               | X             |       |
|Kullanıcı Erişimi Yöneticisi   |        |        |        |        | X             | X             |       |

*: MG Katılımcısı ve MG Okuyucusu, kullanıcıların bu eylemleri yalnızca yönetim grubu kapsamında gerçekleştirmesine izin verir.  
\* *: Kök yönetim grubundaki rol atamaları, bir aboneliği veya yönetim grubunu bu gruba taşımak için gerekli değildir.  Hiyerarşi içindeki öğeleri taşımayla ilgili ayrıntılar için bkz. [Kaynaklarınızı yönetim gruplarıyla yönetme](manage.md).

### <a name="custom-rbac-role-definition-and-assignment"></a>Özel RBAC Rol Tanımı ve Ataması

Özel RBAC rolleri şu anda yönetim gruplarında desteklenmemektedir. Bu maddenin durumunu görüntülemek için [yönetim grubu geri bildirim forumuna](https://aka.ms/mgfeedback) bakın.

## <a name="audit-management-groups-using-activity-logs"></a>Etkinlik günlüklerini kullanarak yönetim gruplarını denetleme

Yönetim grupları [Azure Etkinlik Günlüğü](../../azure-monitor/platform/activity-logs-overview.md)'nde desteklenir. Yönetim grubunda gerçekleşen tüm olayları, diğer Azure kaynaklarıyla aynı merkezi konumda arayabilirsiniz.  Örneğin, belirli bir yönetim grubunda yapılan tüm Rol Atamalarını veya İlke Ataması değişikliklerini görebilirsiniz.

![Yönetim Gruplarıyla Etkinlik Günlükleri](media/al-mg.png)

Azure portalının dışında Yönetim Gruplarını sorgulamak istediğinizde, yönetim gruplarının hedef kapsamı **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** gibi görünür.

## <a name="next-steps"></a>Sonraki adımlar

Yönetim grupları hakkında daha fazla bilgi almak için bkz.:

- [Azure kaynaklarını düzenlemek için yönetim grupları oluşturma](create.md)
- [Yönetim gruplarınızı değiştirme, silme veya yönetme](manage.md)
- [Azure PowerShell Kaynak Modülünde yönetim gruplarını gözden geçirme](/powershell/module/az.resources#resources)
- [REST API'de yönetim gruplarını gözden geçirme](/rest/api/resources/managementgroups)
- [Azure CLI'de yönetim gruplarını gözden geçirme](/cli/azure/account/management-group)
