---
title: Azure Blueprints’e genel bakış
description: Azure şemaları hizmetinin Azure ortamınızda yapıt oluşturmanıza, tanımlamanıza ve dağıtmanıza nasıl olanak sağladığını anlayın.
ms.date: 08/26/2019
ms.topic: overview
ms.openlocfilehash: dadb7568a720d23f58d23896e84b3155ed2f12f4
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048282"
---
# <a name="overview-of-the-azure-blueprints-service"></a>Azure şemaları hizmetine genel bakış

Mühendislerin veya mimarların projenin ana hatlarını oluşturmak için kullandıkları şemalar gibi Azure Blueprints de bulut mimarlarının ve merkezi bilgi teknolojisi gruplarının bir kuruluşun standartlarına, desenlerine ve gereksinimlerine uygun Azure kaynaklarından oluşan tekrarlanabilir bir küme tanımlamasını sağlar. Azure Blueprints geliştirme ekiplerinin yeni ortamları hızlı bir şekilde oluşturup kullanıma almalarını ve bunu yaparken kurumsal uyumluluk çerçevesinde olduklarından ve ağ iletişimi gibi yerleşik bileşenlere sahip olduklarından emin olmalarını mümkün hale getirir.

Blueprints, aşağıdakiler gibi çeşitli kaynak şablonlarını ve diğer yapıtları dağıtma sürecini yönetmenin bildirim temelli bir yoludur:

- Rol Atamaları
- İlke Atamaları
- Azure Resource Manager şablonları
- Kaynak Grupları

Azure Blueprints hizmeti, genel olarak dağıtılmış [Azure Cosmos DB](../../cosmos-db/introduction.md) tarafından desteklenir.
Şema nesneleri birden çok Azure bölgesinde çoğaltılır. Bu çoğaltma Blueprints’in kaynaklarınızı dağıttığı bölgeden bağımsız olarak tüm şema nesnelerinizde düşük gecikme süresi, yüksek kullanılabilirlik ve tutarlılık sağlar.

## <a name="how-its-different-from-resource-manager-templates"></a>Resource Manager şablonlarından farkı

Bu hizmet, _ortam kurulumu_ aşamasında yardımcı olmak üzere tasarlanmıştır. Bu kurulum genellikle bir dizi kaynak grubu, ilke, rol ataması ve Resource Manager şablon dağıtımını içerir. Şema, tüm bu _yapıt_ türlerini bir araya getirerek CI/CD işlem hattı dahil olmak üzere oluşturmanızı ve sürüm belirlemenizi sağlayan bir pakettir. Sonuç olarak her biri tek bir işlem içindeki bir aboneliğe atanır ve denetlenip izlenebilir.

Blueprints ile dağıtmak için kullanmak istediğiniz hemen her şey Resource Manager şablonu ile gerçekleştirilebilir. Ancak Resource Manager şablonu, Azure'da yerel olarak bulunan bir belge değildir, her birinin yerel ortamda veya kaynak denetiminde depolanması gerekir. Şablon bir veya daha fazla Azure kaynağının dağıtılması için kullanılır ancak bu kaynaklar dağıtıldıktan sonra kullanılan şablonla etkin bir bağlantı veya ilişki kalmaz.

Blueprints ile şema tanımı (dağıtılması _gereken şey_) ile şema ataması (dağıtılan _şey_) arasındaki ilişki korunur. Bu bağlantı dağıtımların daha iyi izlenmesini ve denetlenmesini destekler. Blueprints ile aynı şema tarafından yönetilen birden fazla aboneliğin aynı anda yükseltilmesi de mümkündür.

Resource Manager şablonu ile şema arasında seçim yapmanıza gerek yoktur. Her şema sıfır veya daha fazla Resource Manager şablonu _yapıtı_ içerebilir. Bu destek, Resource Manager şablonu kitaplığı geliştirme ve koruma çabalarının Blueprints ile yeniden kullanılabileceği anlamına gelir.

## <a name="how-its-different-from-azure-policy"></a>Azure İlkesi'nden farkı

Şema tutarlılık ve uyumluluk sağlamak için yeniden kullanılabilen Azure bulut hizmetleri, güvenlik ve tasarım uygulamasıyla ilgili belirli bir alana yönelik standart, desen ve gereksinim kümesi oluşturmak için kullanılan bir paket veya kapsayıcıdır.

[İlke](../policy/overview.md) ise yeni dağıtılan ve var olan kaynakların özelliklerine odaklanan varsayılan olarak izin verme ve açıkça reddetme sistemidir. Bir abonelik içindeki kaynakların gereksinimlere ve standartlara uygun olduğunu doğrulayarak bulut yönetimini destekler.

Şema içine bir ilkenin dahil edilmesi, şema ataması sırasında doğru desenin veya tasarımın oluşturulmasını da sağlar. Bu ilke şemanın amacıyla uyumluluk sağlamak için ortamda yalnızca onaylanan veya beklenen değişikliklerin yapılmasını garanti eder.

Bir ilke, şema tanımındaki birçok _yapıtlardan_ biri olarak eklenebilir. Şemalar ayrıca ilkeler ve girişimlerle parametrelerin kullanılmasını da destekler.

## <a name="blueprint-definition"></a>Şema tanımı

Şemalar, _yapıtlardan_ meydana gelir. Şu an için aşağıdaki kaynaklar şemalarda yapıtlar olarak kullanılabilir:

|Kaynak  | Hiyerarşi seçenekleri| Açıklama  |
|---------|---------|---------|
|Kaynak Grupları | Abonelik | Şema içindeki diğer yapıtlar tarafından kullanılacak yeni bir kaynak grubu oluşturur.  Bu yer tutucu kaynak grupları, kaynakları tam olarak istediğiniz yapıda düzenlemenizi sağlar ve dahil olan ilke ve rol ataması yapıtlarına ek olarak Azure Resource Manager şablonları için kapsam sınırlayıcı olarak görev yapar. |
|Azure Resource Manager şablonu | Abonelik, Kaynak Grubu | İç içe ve bağlı şablonlar dahil olmak üzere şablonlar, karmaşık ortamları oluşturmak için kullanılır. Örnek ortamlar: SharePoint grubu, Azure Otomasyonu Durum Yapılandırması veya Log Analytics çalışma alanı. |
|İlke Ataması | Abonelik, Kaynak Grubu | Bir ilkenin veya girişimin, şemanın atanmış olduğu aboneliğe atanmasını sağlar. İlke veya girişim, şema tanım konumunun kapsamında olmalıdır. İlke veya girişimde parametre varsa bu parametreler şema oluşturma veya şema ataması sırasında atanır. |
|Rol Ataması | Abonelik, Kaynak Grubu | Kaynaklarınıza her zaman doğru kişilerin doğru şekilde erişmesini sağlamak için var olan bir kullanıcıyı veya grubu yerleşik role ekleyin. Rol atamaları aboneliğin tamamı için tanımlanabilir veya şemada bulunan belirli bir kaynak grubuna yerleştirilebilir. |

### <a name="blueprint-definition-locations"></a>Şema tanımı konumlarını şeması

Şema tanımı oluştururken şemanın kaydedileceği yeri de tanımlarsınız. Planlar, **katkıda** bulunan erişimi olan bir [yönetim grubuna](../management-groups/overview.md) veya aboneliğe kaydedilebilir. Konum bir yönetim grubumu ise, bu yönetim grubunun herhangi bir alt aboneliğine atamak için şema kullanılabilir.

### <a name="blueprint-parameters"></a>Şema parametreleri

Şemalar bir ilkeye/girişime veya bir Azure Resource Manager şablonuna parametre iletebilir.
Bir şemaya yeni bir _yapıt_ eklendiğinde yazar her bir şema ataması için tanımlı bir değer sunar veya her şema atamasının atama sırasında değer sağlamasına izin verir. Bu esneklik şemanın tüm kullanımları için önceden tanımlanmış bir değer tanımlama veya bu kararın atama sırasında verilmesi seçeneğini sunar.

> [!NOTE]
> Şema kendi parametrelerine sahip olabilir ancak bu özellik şu an için yalnızca şemanın portal yerine REST API ile oluşturulması durumunda kullanılabilir.

Daha fazla bilgi için bkz. [şema parametreleri](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Şema yayımlama

Şema ilk oluşturulduğunda **Taslak** modunda olur. Atanmaya hazır olduğunda **Yayımlandı** durumuna alınması gerekir. Yayımlamak için bir **Sürüm** dizesi (harfler, sayılar ve kısa çizgiler, maksimum 20 karakter) ve isteğe bağlı **Değişiklik notları** tanımlanması gerekir. **Sürüm** değeri şemada daha sonra yapılacak değişikliklerin tanımlanmasını sağlar ve her sürümün ayrıca atanmasını mümkün hale getirir. Sürüm oluşturma aynı zamanda aynı şemanın farklı **Sürümlerinin** aynı aboneliğe atanabileceği anlamına da gelir. Şemada ek değişiklikler yapıldığında **Yayımlanmış** **Sürüm** ile birlikte **Yayımlanmamış değişiklikler** de var olmaya devam eder. Değişiklikler tamamlandıktan sonra güncelleştirilen şema yeni ve benzersiz bir **Sürüm** değeriyle **Yayımlanır** ve atamaya hazır hale gelir.

## <a name="blueprint-assignment"></a>Şema ataması

Bir şema 'in **yayımlanan** her **sürümü** var olan bir aboneliğe atanabilir (en fazla 90 karakter uzunluğunda bir ad olabilir). Portalda varsayılan olarak en son **Yayımlanmış** olan şema **Sürümü** kullanılır. Yapıt parametreleri (veya şema parametreleri) varsa atama işlemi sırasında ilgili parametreler tanımlanır.

## <a name="permissions-in-azure-blueprints"></a>Azure Blueprints'te izinler

Şemaları kullanmak için [Rol tabanlı erişim denetimi](../../role-based-access-control/overview.md) (RBAC) ile gerekli izinlerin verilmiş olması gerekir. Şema oluşturmak için hesabınız şu izinlere sahip olmalıdır:

- `Microsoft.Blueprint/blueprints/write` - Şema tanımı oluştur
- `Microsoft.Blueprint/blueprints/artifacts/write` - Şema tanımında yapıt oluştur
- `Microsoft.Blueprint/blueprints/versions/write` - Şema yayımla

Şemaları silmek için hesabınız şu izinlere sahip olmalıdır:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Şema tanımı izinleri, kaydedildiği veya yönetim grubu ya da abonelik kapsamında kaydedildiği yere devralınmalıdır.

Bir şemayı atamak veya atamasını kaldırmak için hesabınız şu izinlere sahip olmalıdır:

- `Microsoft.Blueprint/blueprintAssignments/write` - Şema ata
- `Microsoft.Blueprint/blueprintAssignments/delete` - Şemanın atamasını kaldır

> [!NOTE]
> Şema atamaları abonelikte oluşturulduğundan şema atama ve atamasını kaldırma izinlerinin abonelik kapsamında verilmesi veya abonelik kapsamında devralınması gerekir.

Aşağıdaki yerleşik roller kullanılabilir:

|RBAC rolü | Açıklama |
|-|-|
|[Sahip](../../role-based-access-control/built-in-roles.md#owner) | Diğer izinlerin yanı sıra, tüm Azure Blueprint ilgili izinleri içerir. |
|[Katılımcı](../../role-based-access-control/built-in-roles.md#contributor) | Diğer izinlerin yanı sıra, şema tanımları oluşturup silebilir, ancak şema atama izinlerine sahip değildir. |
|[Blueprint Katılımcısı](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Şema tanımlarını yönetebilir, ancak atamazsınız. |
|[Blueprint Işleci](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Varolan yayımlanmış şemaları atayabilir, ancak yeni şema tanımları oluşturamaz. Blueprint ataması, yalnızca atama Kullanıcı tarafından atanan yönetilen kimlikle yapıldığında işe yarar. |

Bu yerleşik roller güvenlik gereksinimlerinize uygun değilse [özel rol](../../role-based-access-control/custom-roles.md) oluşturabilirsiniz.

> [!NOTE]
> Sistem tarafından atanan yönetilen kimlik kullanılıyorsa, Azure şemaları için hizmet sorumlusu, dağıtımı etkinleştirmek için atanan abonelikte **sahip** rolünü gerektirir. Portalı kullanıyorsanız bu rol dağıtım için otomatik olarak verilir ve iptal edilir. REST API kullanıyorsanız bu rolün el ile verilmesi gerekir ancak dağıtım tamamlandıktan sonra iptal işlemi otomatik olarak gerçekleştirilir. Kullanıcı tarafından atanan yönetilen kimlik kullanılıyorsa, yalnızca şema atamasını oluşturan kullanıcının hem **sahip** hem de şema **operatörü** yerleşik rollerinde bulunan `Microsoft.Blueprint/blueprintAssignments/write` izni vardır.

## <a name="naming-limits"></a>Adlandırma sınırları

Belirli alanlar için aşağıdaki sınırlamalar mevcuttur:

|Nesne|Alan|İzin verilen karakterler|En çok, Uzunluk|
|-|-|-|-|
|Şema|Ad|harfler, rakamlar, tireler ve noktalar|48|
|Şema|Sürüm|harfler, rakamlar, tireler ve noktalar|20|
|Şema ataması|Ad|harfler, rakamlar, tireler ve noktalar|90|
|Blueprint yapıtı|Ad|harfler, rakamlar, tireler ve noktalar|48|

## <a name="video-overview"></a>Videoya genel bakış

Azure 'a yönelik olarak aşağıdaki genel bakış Azure Fridays. Video indirme için, Channel 9 ' da Azure 'a [yönelik bir genel bakış olan Azure Fridays](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) adresini ziyaret edin.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Sonraki adımlar

- [Şema oluşturma - Portal](create-blueprint-portal.md)
- [Şema oluşturma - REST API](create-blueprint-rest-api.md)