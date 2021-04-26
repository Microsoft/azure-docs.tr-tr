---
title: Azure İlkesine Genel Bakış
description: Azure İlkesi, Azure ortamında ilke tanımlarınızı oluşturmak, atamak ve yönetmek için kullandığınız bir Azure hizmetidir.
ms.date: 01/14/2021
ms.topic: overview
ms.openlocfilehash: df430586af2e701ec2881f6ea760095fd2ca79d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98220737"
---
# <a name="what-is-azure-policy"></a>Azure İlkesi nedir?

Azure İlkesi, kuruluş standartlarının uygulanmasına ve büyük ölçekte uyumluluk değerlendirmesi yapılmasına yardımcı olur. Uyumluluk panosu sayesinde, ortamın genel durumunu değerlendirmek için toplanan bir görünüm sağlar. Bu özellik, kaynak başına, ilke başına ayrıntı düzeyi detayına gidebilir. Ayrıca, mevcut kaynaklar için toplu düzeltme ve yeni kaynaklar için otomatik düzeltme aracılığıyla kaynaklarınızı uyumluluğa getirmeye yardımcı olur.

Azure Ilkesi için genel kullanım örnekleri, kaynak tutarlılığı, mevzuata uyumluluk, güvenlik, maliyet ve yönetim için idare uygulamayı içerir. Bu yaygın kullanım örnekleri için ilke tanımları, Azure ortamınızda, başlamanıza yardımcı olacak yerleşik olarak zaten kullanılabilir.

Tüm Azure Ilke verileri ve nesneleri bekleyen olarak şifrelenir. Daha fazla bilgi için bkz. [bekleyen Azure veri şifrelemesi](../../security/fundamentals/encryption-atrest.md).

## <a name="overview"></a>Genel Bakış

Azure Ilkesi, Azure 'daki kaynakları, bu kaynakların özelliklerini iş kurallarıyla karşılaştırarak değerlendirir. [JSON biçiminde](./concepts/definition-structure.md)açıklanan bu iş kuralları [ilke tanımları](#policy-definition)olarak bilinir. Yönetimi basitleştirmek için, bir [ilke girişimi](#initiative-definition) (bazen _policyset_ olarak adlandırılır) oluşturmak üzere çeşitli iş kuralları birlikte gruplandırılabilir. İş kurallarınız oluşturulduktan sonra, [Yönetim grupları](../management-groups/overview.md), abonelikler, [kaynak grupları](../../azure-resource-manager/management/overview.md#resource-groups)veya tek tek kaynaklar gibi Azure tarafından desteklenen herhangi bir kaynak kapsamına ilke tanımı veya girişimi [atanır](#assignments) . Atama, bu atamanın [Kaynak Yöneticisi kapsamındaki](../../azure-resource-manager/management/overview.md#understand-scope) tüm kaynaklar için geçerlidir. Gerekirse alt kapsamlar dışarıda bırakılabilirler. Daha fazla bilgi için bkz. [Azure Ilkesinde kapsam](./concepts/scope.md).

Azure Ilkesi, değerlendirmenin bir kaynağın uyumlu olup olmadığını belirlemede kullandığı mantığı oluşturmak için bir [JSON biçimi](./concepts/definition-structure.md) kullanır. Tanımlar meta veriler ve ilke kuralı içerir. Tanımlı kural, tam olarak istediğiniz senaryoyu eşleştirmek için işlevleri, parametreleri, mantıksal işleçleri, koşulları ve özellik [diğer adlarını](./concepts/definition-structure.md#aliases) kullanabilir. İlke kuralı, atama kapsamındaki hangi kaynakların değerlendirildiğini belirler.

### <a name="understand-evaluation-outcomes"></a>Değerlendirme sonuçlarını anlama

Kaynaklar, kaynak yaşam döngüsü, ilke atama yaşam döngüsü ve düzenli olarak devam eden uyumluluk değerlendirmesi için belirli zamanlarda değerlendirilir. Aşağıdakiler, bir kaynağın değerlendirilmesine neden olan zamanlar veya olaylardır:

- Bir kaynak, ilke atamasına sahip bir kapsamda oluşturulur, güncelleştirilir veya silinir.
- Bir ilke veya girişim yeni bir kapsama atanır.
- Bir kapsama zaten atanmış olan bir ilke veya girişim güncellenir.
- Her 24 saatte bir gerçekleşen standart uyumluluk değerlendirme çevrimi sırasında.

İlke değerlendirmesinin ne zaman ve nasıl yapılacağı hakkında ayrıntılı bilgi için bkz. [değerlendirme Tetikleyicileri](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Bir değerlendirmenin yanıtını denetleme

Uyumlu olmayan kaynakları işlemeye yönelik iş kuralları kuruluşlar arasında farklılık gösterir. Kuruluşun uyumlu olmayan bir kaynağa yanıt vermesini nasıl istediğini gösteren örnekler şunlardır:

- Kaynak değişikliğini reddetme
- Değişikliği kaynağa Kaydet
- Değişiklikten önce kaynağı değiştirme
- Değişiklikten sonra kaynağı değiştirme
- İlgili uyumlu kaynakları dağıtma

Azure Ilkesi, bu iş yanıtlarının her birini [etkileri](./concepts/effects.md)uygulama aracılığıyla mümkün hale getirir. Etkiler, [ilke tanımının](./concepts/definition-structure.md) **ilke kuralı** bölümünde ayarlanır.

### <a name="remediate-non-compliant-resources"></a>Uyumlu olmayan kaynakları düzeltme

Bu efektler, kaynak oluşturulduğunda veya güncelleştirilirken bir kaynağı öncelikle etkiler, ancak Azure Ilkesi, bu kaynağı değiştirmek zorunda kalmadan, mevcut uyumsuz kaynaklarla ilgili olarak da destekler. Mevcut kaynakları uyumlu hale getirme hakkında daha fazla bilgi için bkz. [kaynakları](./how-to/remediate-resources.md)düzeltme.

### <a name="video-overview"></a>Genel bakış videosu

Aşağıdaki Azure İlkesi genel bakış videosu Build 2018 etkinliğinde kaydedilmiştir. Slaytlar veya video indirme için, Kanal 9 ' da [Azure ilkesi aracılığıyla Azure ortamınızı](https://channel9.msdn.com/events/Build/2018/THR2030) yönetme makalesini ziyaret edin.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Kullanmaya başlama

### <a name="azure-policy-and-azure-rbac"></a>Azure Ilkesi ve Azure RBAC

Azure Ilkesi ile Azure rol tabanlı erişim denetimi (Azure RBAC) arasında bazı önemli farklılıklar vardır. Azure Ilkesi, bazı kaynak sağlayıcılarının Kaynak Yöneticisi ve özelliklerinde temsil edilen kaynaklardaki özellikleri inceleyerek durumu değerlendirir. Azure Ilkesi eylemleri kısıtlayamaz ( _işlemler_ olarak da adlandırılır). Azure Ilkesi, değişiklik yapan veya değişiklik yapma iznine sahip olan bir sorun olmadan kaynak durumunun iş kurallarınız ile uyumlu olmasını sağlar.

Azure RBAC, farklı kapsamlardaki Kullanıcı [eylemlerinin](../../role-based-access-control/resource-provider-operations.md) yönetimine odaklanır. Bir eylemin denetimi gerekiyorsa, Azure RBAC kullanılacak doğru araçtır. Bir bireyin bir eylem gerçekleştirmek için erişime sahip olsa bile, sonuç uyumlu olmayan bir kaynaktır, Azure Ilkesi hala oluştur veya Güncelleştir ' i engeller.

Azure RBAC ve Azure Ilkesinin birleşimi Azure 'da tam kapsam denetimi sağlar.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Azure Ilkesinde Azure RBAC izinleri

Azure İlkesi iki Kaynak Sağlayıcısı’nda işlemler olarak bilinen bazı izinlere sahiptir:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft. Poliyelei](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Birçok Yerleşik rol Azure İlkesi kaynaklarına izin verir. **Kaynak Ilkesi katılımcısı** rolü çoğu Azure ilke işlemini içerir. **Sahibinin** tam hakları vardır. **Katkıda bulunan** ve **okuyucunun** _tüm Azure_ ilke işlemlerine erişimi vardır. **Katkıda bulunan** kaynak düzeltmesini tetikleyebilir, ancak tanımları veya atamaları _oluşturamaz_ . **Kullanıcı erişimi Yöneticisi** , yönetilen kimliği **Deployifnotexists** üzerinde vermek veya atamaları gereken izinleri **değiştirmek** için gereklidir.

Yerleşik rollerin hiçbirinde gerekli izinler yoksa [özel rol](../../role-based-access-control/custom-roles.md) oluşturun.

> [!NOTE]
> Bir **Deployifnotexists** veya **değişiklik** ilkesi atamasının yönetilen kimliği, hedeflenen kaynakları oluşturmak veya güncelleştirmek için yeterli izinlere sahip olmalıdır. Daha fazla bilgi için bkz. [düzeltme için ilke tanımlarını yapılandırma](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Azure Ilkesi kapsamındaki kaynaklar

Azure Ilkesi, yayın etkinleştirilmiş kaynaklar dahil olmak üzere, abonelik düzeyindeki veya altındaki tüm Azure kaynaklarını değerlendirir. [Konuk yapılandırması](./concepts/guest-configuration.md), [Azure Kubernetes hizmeti](../../aks/intro-kubernetes.md)ve [Azure Key Vault](../../key-vault/general/overview.md)gibi belirli kaynak sağlayıcıları için ayarları ve nesneleri yönetmeye yönelik daha derin bir tümleştirme vardır. Daha fazla bilgi edinmek için bkz. [kaynak sağlayıcısı modları](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>İlkeleri yönetme ile ilgili öneriler

Göz önünde bulundurmanız gereken birkaç işaretçi ve ipucu aşağıda verilmiştir:

- Ortamınızdaki kaynaklarda ilke tanımınızın etkisini izlemek için bir reddetme etkisi yerine bir denetim etkisi ile başlayın. Uygulamalarınızı otomatik olarak otomatik ölçeklendirme için kullanabileceğiniz betikleriniz varsa, bir reddetme efektinin ayarlanması, bu tür otomasyon görevlerinin zaten var olduğunu gösterebilir.

- Tanımlar ve atamalar oluştururken kuruluş hiyerarşilerini göz önünde bulundurun. Yönetim grubu veya abonelik düzeyi gibi daha yüksek düzeylerde tanımlar oluşturmanızı öneririz. Ardından, bir sonraki alt düzeyde atama oluşturun. Bir yönetim grubunda bir tanım oluşturursanız, atama söz konusu yönetim grubu içindeki bir abonelik veya kaynak grubu için kapsamı değiştirilebilir.

- Tek bir ilke tanımı için bile girişim tanımlarının oluşturulmasını ve atanmasını öneririz.
  Örneğin, ilke tanımı _policyDefA_ ve bunu girişim tanımı _initiativeDefC_ altında oluşturabilirsiniz. _Policydefb_ için daha sonra _policyDefA_ benzeri hedefleri olan bir Ilke tanımı oluşturursanız, _initiativeDefC_ altına ekleyebilir ve bunları birlikte izleyebilirsiniz.

- Bir girişim ataması oluşturduktan sonra, girişim öğesine eklenen ilke tanımları da bu girişim atamalarının bir parçası haline gelir.

- Bir girişim ataması değerlendirildiğinde, girişim içindeki tüm ilkeler de değerlendirilir.
  Bir ilkeyi ayrı ayrı değerlendirmeniz gerekiyorsa, bunu bir girişimde dahil etmek daha iyidir.

## <a name="azure-policy-objects"></a>Azure Ilke nesneleri

### <a name="policy-definition"></a>İlke tanımı

Azure İlkesi'nde bir ilke oluşturmak ve uygulamak için önce ilke tanımını oluşturmanız gerekir. Her ilke tanımında, bu ilkelerin uygulandığı koşullar bulunur. Ve koşullar karşılanıyorsa, tanımlanmış bir etkiye sahiptir.

Azure Ilkesinde, varsayılan olarak kullanılabilen birkaç yerleşik ilke sunuyoruz. Örnek:

- **Izin verilen depolama hesabı SKU 'ları** (reddetme): dağıtılmakta olan bir depolama HESABıNıN bir SKU boyutları kümesi içinde olup olmadığını belirler. Etkisi, tanımlı SKU boyutları kümesine bağlı olmayan tüm depolama hesaplarını reddetmektir.
- **Izin verilen kaynak türü** (reddetme): dağıtabileceğiniz kaynak türlerini tanımlar. Etkisi, bu tanımlı listenin parçası olmayan tüm kaynakları reddetmektir.
- **Izin verilen konumlar** (reddetme): yeni kaynaklar için kullanılabilir konumları kısıtlar. Sahip olduğu eylem ise coğrafi uyumluluk gereksinimlerinizi uygulamaktır.
- **Izin verilen sanal makine SKU 'ları** (reddetme): dağıtabileceğiniz bir sanal makine SKU 'Su kümesi belirtir.
- **Kaynaklara bir etiket ekleyin** (değiştir): dağıtım isteği tarafından belirtilmemişse gerekli bir etiketi ve varsayılan değerini uygular.
- **İzin verilmeyen kaynak türleri** (reddetme): bir kaynak türleri listesinin dağıtılmasını engeller.

Bu ilke tanımlarını uygulamak için (hem yerleşik hem de özel tanımlar), bunları atamanız gerekir. Bu ilkelerden herhangi birini Azure portalı, PowerShell veya Azure CLI üzerinden atayabilirsiniz.

İlke değerlendirmesi, ilke ataması veya ilke güncelleştirmeleri gibi çeşitli farklı eylemlerle gerçekleştirilir. Tüm liste için bkz. [ilke değerlendirme Tetikleyicileri](./how-to/get-compliance-data.md#evaluation-triggers).

İlke tanımlarının yapıları hakkında daha fazla bilgi edinmek için [İlke Tanımı Yapısı](./concepts/definition-structure.md) adlı makaleye göz atın.

İlke parametreleri, oluşturmanız gereken ilke tanımlarının sayısını azaltarak ilke yönetiminizi basitleştirmeye yardımcı olur. İlke tanımı oluştururken parametreleri belirleyerek bunları daha genel hale getirebilirsiniz. Daha sonra bu ilke tanımını farklı senaryolar için kullanabilirsiniz. Bu işlemi, ilke tanımlarının atamasını yaparken farklı değerler girerek gerçekleştirebilirsiniz. Örneğin, abonelik için bir konum kümesi belirtme.

Bir ilke tanımı oluşturulurken parametreler tanımlanmıştır. Tanımlanan parametreye bir ad ve isteğe bağlı olarak bir değer verilir. Örneğin, _konum_ başlıklı bir ilke için parametre tanımlayabilirsiniz. Daha sonra, ilkenin atamasını yaparken _EastUS_ veya _WestUS_ gibi farklı değerler verebilirsiniz.

İlke parametreleri hakkında daha fazla bilgi için bkz. [tanım yapısı-parametreler](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Girişim tanımı

Girişim tanımı, tekil kapsamlı bir hedefi gerçekleştirmek üzere belirlenmiş ilke tanımlarının bir koleksiyonudur. Girişim tanımları, ilke tanımlarının yönetimini ve atanmasını basitleştirir. İlke kümelerini gruplandırıp tek bir öğe haline getirerek basitleştirirler. Örneğin, Azure Güvenlik Merkezinizdeki tüm kullanılabilir güvenlik önerilerini izlemeyi hedefleyen **Azure Güvenlik Merkezi'nde İzlemeyi Etkinleştirme** başlıklı bir girişim oluşturabilirsiniz.

> [!NOTE]
> Azure CLı ve Azure PowerShell gibi SDK, girişimlere başvurmak için **Policyset** adlı özellikleri ve parametreleri kullanır.

Bu girişimin altında sahip olabileceğiniz ilke tanımlarından bazıları şunlardır:

- **Güvenlik Merkezi’ndeki şifrelenmemiş SQL Veritabanı’nı izleme** – Şifrelenmemiş SQL veritabanlarını ve sunucuları izlemek için.
- **Güvenlik Merkezi 'nde işletim sistemi güvenlik açıklarını** izleme – yapılandırılmış temeli karşılamayan sunucuları izleme.
- **Güvenlik Merkezi’ndeki eksik Endpoint Protection’ı izleme** – Yüklü bir bitiş noktası koruma aracısı olmadan sunucuları izlemek için.

İlke parametreleri gibi, girişim parametreleri de fazlalıkları azaltarak girişim yönetiminin basitleştirilmesine yardımcı olur. Girişim parametreleri, girişim içindeki ilke tanımları tarafından kullanılan parametrelerdir.

Örneğin **initiativeC** adına her biri farklı bir parametre türü bekleyen **policyA** ve **policyB** ilke tanımlarına sahip olan bir girişim tanımına sahip olduğunuzu düşünelim:

| İlke | Parametrenin adı |Parametrenin türü  |Not |
|---|---|---|---|
| policyA | allowedLocations | array  |Parametre türü “array” olarak tanımlandığından bu parametre, bir değer için dizilerin bulunduğu bir liste bekler |
| policyB | allowedSingleLocation |string |Parametre türü “array” olarak tanımlandığından bu parametre, bir değer için bir sözcük bekler |

Bu senaryoda **initiativeC** için girişim parametreleri tanımlanırken üç seçeneğiniz vardır:

- Bu girişim dahilinde ilke tanımlarının parametrelerini kullanın: Bu örnekte, _allowedLocations_ ve _allowedSingleLocation_, **initiativeC** için girişim parametreleri olur.
- Bu girişim tanımındaki ilke tanımlarının parametrelerine değerler sağlayın. Bu örnekte, **Policya**'ın parametresi – **Allowedlocations** ve **Policyb**'nin parametresi – **allowedsinglelocation** için konumların bir listesini sağlayabilirsiniz. Bu girişimin atamasını yaparken değerleri de sağlayabilirsiniz.
- Bu girişimin atamasını yaparken kullanılabilecek bir _değer_ seçenekleri listesi sağlayın. Bu girişimi atadığınızda, girişim dahilindeki ilke tanımlarından alınan parametreler yalnızca bu sağlanan listedeki değerleri alabilir.

Bir girişim tanımında değer seçenekleri oluştururken, girişim ataması sırasında, listenin bir parçası olmadığından farklı bir değer girmeyebilir.

Girişim tanımlarının yapıları hakkında daha fazla bilgi edinmek için [girişim tanımı yapısını](./concepts/initiative-definition-structure.md)inceleyin.

### <a name="assignments"></a>Atamalar

Atama, belirli bir kapsamda gerçekleşmesi için atanmış bir ilke tanımıdır veya girişimidir. Bu kapsam bir [Yönetim grubundan](../management-groups/overview.md) tek bir kaynağa kadar değişebilir. _Kapsam_ terimi, tanımın atandığı tüm kaynakları, kaynak grupları, abonelikleri veya Yönetim gruplarını ifade eder. Atamalar tüm alt kaynaklar tarafından devralınır. Bu tasarım, kaynak grubuna uygulanan bir tanımın bu kaynak grubundaki kaynaklara da uygulanacağını gösterir. Ancak, bir alt kapsamı atamadan dışlayabilirsiniz.

Örneğin, abonelik kapsamında, ağ kaynaklarının oluşturulmasını önleyen bir tanım atayabilirsiniz. Ağ altyapısı için tasarlanan bir kaynak grubunu bu abonelikte dışlayabilirsiniz. Daha sonra, ağ kaynakları oluşturma ile güvendiğiniz kullanıcılara bu ağ kaynak grubuna erişim izni verirsiniz.

Başka bir örnekte, yönetim grubu düzeyinde bir kaynak türü izin verilenler listesi tanımına atamak isteyebilirsiniz. Daha sonra bir alt yönetim grubunda veya doğrudan abonelikler üzerinde daha fazla izin veren bir ilke (daha fazla kaynak türüne izin vererek) atarsınız. Ancak, Azure Ilkesi açık bir reddetme sistemi olduğundan bu örnek çalışmaz. Bunun yerine, alt yönetim grubunu veya aboneliğini yönetim grubu düzeyi atamasından çıkarmanız gerekir. Daha sonra, alt yönetim grubu veya abonelik düzeyinde daha fazla izin veren tanımını atayın. Bir kaynağın herhangi bir atama reddedilmesiyle sonuçlanırsa, kaynağa izin vermek için tek yol reddetme atamasını değiştirmektir.

Portal aracılığıyla atamaları ayarlama hakkında daha fazla bilgi için bkz. [Azure ortamınızda uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma](./assign-policy-portal.md). [PowerShell](./assign-policy-powershell.md) ve [Azure CLI](./assign-policy-azurecli.md) adımları da mevcuttur. Atama yapısı hakkında bilgi için bkz. atama [yapısı](./concepts/assignment-structure.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Maksimum Azure Ilke nesnesi sayısı

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure İlkesi ve bazı önemli kavramlar ile ilgili bir genel bakışa sahipsiniz, önerdiğimiz diğer adımları aşağıda bulabilirsiniz:

- [İlke tanımı yapısını gözden geçirin](./concepts/definition-structure.md).
- [Portalı kullanarak bir ilke tanımı atayın](./assign-policy-portal.md).
