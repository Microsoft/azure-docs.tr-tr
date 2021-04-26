---
title: Kod olarak Azure İlkesi iş akışları tasarlama
description: Azure Ilke tanımlarınızı kod olarak dağıtmak ve kaynakları otomatik olarak doğrulamak için iş akışları tasarlamayı öğrenin.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 233a5d4ca6583bc6a2cd9e8366e54f3ed1e2dad3
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106094906"
---
# <a name="design-azure-policy-as-code-workflows"></a>Kod olarak Azure İlkesi iş akışları tasarlama

Bulut yönetimi ile yolculukta ilerleyerek, Azure portal her bir ilke tanımını veya çeşitli SDK 'Ları, kurumsal ölçekte daha yönetilebilir ve yinelenebilir olacak şekilde el ile yönetmeyi tercih edersiniz. Sistemleri bulutta ölçeklendirerek yönetmek için hakim yaklaşımlardan ikisi şunlardır:

- Kod olarak altyapı: ortamınızı tanımlayan içeriği, Azure Resource Manager şablonlarından her şeyi (ARM şablonları) Azure Ilke tanımlarına kaynak kodu olarak kabul eden uygulama.
- DevOps: son kullanıcılarımıza sürekli değer teslimi sağlayan kişiler, süreç ve ürünlerin birleşimi.

Kod olarak Azure Ilkesi, bu fikirlerin birleşimidir. Temelde, ilke tanımlarınızı kaynak denetiminde tutun, her değişiklik yapıldığında, test edin ve bu değişikliği doğrulayın. Bununla birlikte, kod veya DevOps ile altyapıyla ilke katılımı olması gerekmez.

Doğrulama adımı aynı zamanda diğer sürekli tümleştirme veya sürekli dağıtım iş akışlarının bir bileşeni olmalıdır. Örnek olarak bir uygulama ortamının veya sanal altyapının dağıtımı sayılabilir. Azure Ilke doğrulamasını derleme ve dağıtım sürecinin erken bir bileşeni yaparak, uygulama ve operasyon ekipleri, değişikliklerin uyumsuz olup olmadığı ve üretimde dağıtmaya çalıştıkları durumlarda bulur.

## <a name="definitions-and-foundational-information"></a>Tanımlar ve temel bilgiler

Kod iş akışı olarak Azure Ilkesi ayrıntılarına geçmeden önce, aşağıdaki tanımları ve örnekleri gözden geçirin:

- [İlke tanımı](./definition-structure.md)
- [Girişim tanımı](./initiative-definition-structure.md)

Dosya adları, ilke ya da girişim tanımının bölümlerine hizalanır:
- `policy(set).json` -Tüm tanım
- `policy(set).parameters.json` - `properties.parameters` Tanımın bölümü
- `policy.rules.json` - `properties.policyRule` Tanımın bölümü
- `policyset.definitions.json` - `properties.policyDefinitions` Tanımın bölümü

Bu dosya biçimlerine örnek olarak [Azure Policy GitHub](https://github.com/Azure/azure-policy/)deposunda ulaşılabilir:

- İlke tanımı: [kaynaklara etiket ekleme](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- Girişim tanımı: [faturalandırma etiketleri](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

## <a name="workflow-overview"></a>İş akışına genel bakış

Kod olarak Azure Ilkesinin önerilen genel iş akışı şu diyagram gibi görünür:

:::image type="complex" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="Azure Ilkesini, oluşturma ' dan dağıtılacak test ' den kod iş akışı kutuları olarak gösteren diyagram." border="false":::
   Azure Ilkesini kod iş akışı kutuları olarak gösteren diyagram. İlke ve girişim tanımlarının oluşturulması için oluşturma içerir. Test, zorlama modu devre dışı olan atamayı kapsıyor. Uyumluluk durumu için bir ağ geçidi denetimi, izin verme ve kaynakları düzeltme atamalarına göre daha sonra yapılır.  Dağıtım, atama modu etkinken atamanın güncelleştirilmesini içerir.
:::image-end:::

### <a name="create-and-update-policy-definitions"></a>İlke tanımları oluşturma ve güncelleştirme

İlke tanımları JSON kullanılarak oluşturulur ve kaynak denetiminde depolanır. Her ilkenin aynı klasöre depolanması gereken parametreler, kurallar ve ortam parametreleri gibi kendi dosya kümesi vardır. Aşağıdaki yapı, ilke tanımlarınızı kaynak denetiminde tutmanın önerilen bir yoludur.

```text
.
|
|- policies/  ________________________ # Root folder for policy resources
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|
```

Yeni bir ilke eklendiğinde veya var olan bir ilke güncelleştirilirse, iş akışı Azure 'daki ilke tanımını otomatik olarak güncelleştirmelidir. Yeni veya güncelleştirilmiş ilke tanımının testi sonraki bir adımda gelir.

Ayrıca, var olan tanımlarınızı ve atamalarınızı kaynak kodu yönetim ortamı [GitHub](https://www.github.com)' da almak Için [Azure Ilke kaynaklarını dışarı aktarmayı](../how-to/export-resources.md) gözden geçirin.

### <a name="create-and-update-initiative-definitions"></a>Girişim tanımları oluşturma ve güncelleştirme

Benzer şekilde, girişimlerin kendi JSON dosyası ve aynı klasörde depolanması gereken ilgili dosyaları vardır. Girişim tanımı, ilke tanımının zaten var olmasını gerektirir, bu nedenle ilke kaynağı kaynak denetiminde güncelleştirilene ve ardından Azure 'da güncelleştirilene kadar oluşturulamaz veya güncellenemiyor. Aşağıdaki yapı, kaynak denetiminde girişim tanımlarınızı tutmanın önerilen bir yoludur:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
```

İlke tanımları gibi, var olan bir girişimi eklerken veya güncelleştirirken, iş akışı otomatik olarak Azure 'daki girişim tanımını güncelleştirmelidir. Yeni veya güncelleştirilmiş girişim tanımının testi sonraki bir adımda gelir.

### <a name="test-and-validate-the-updated-definition"></a>Güncelleştirilmiş tanımı test edin ve doğrulayın

Otomasyon yeni oluşturulmuş veya güncelleştirilmiş ilke ya da girişim tanımlarınızı tamamladıktan sonra Azure 'daki nesne ile ilgili güncelleştirmeyi yaptıktan sonra, yapılan değişiklikleri test etmek zaman alabilir. Uygulamasının parçası olan ilke veya girişim, üretimden en uzak ortamda bulunan kaynaklara atanmalıdır. Bu ortam genellikle _dev_'dir.

Atama, kaynak oluşturma ve güncelleştirmelerin engellenmemesi, ancak mevcut kaynakların güncelleştirilmiş ilke tanımıyla uyumluluk için denetlenmeye devam edebilmesi için, _Disabled_ 'ın [Enforcementmode](./assignment-structure.md#enforcement-mode) kullanması gerekir. EnforcementMode da dahil olmak üzere, atama kapsamının bir kaynak grubu ya da özel olarak ilkeleri doğrulamak için bir abonelik olması önerilir.

> [!NOTE]
> Zorlama modu yararlı olsa da, çeşitli koşullarda bir ilke tanımının kapsamlı bir şekilde test edilmesine yönelik bir değişiklik değildir. İlke tanımı, ile test edilmelidir `PUT` ve `PATCH` REST API çağrılarınız, uyumlu ve uyumlu olmayan kaynaklar ve uç durumları, kaynakta eksik bir özellik gibi.

Atama dağıtıldıktan sonra, yeni atamaya yönelik [uyumluluk verilerini almak](../how-to/get-compliance-data.md) Için Azure ilke SDK 'Sını, [Azure Ilke uyumluluk taraması GitHub eylemini](https://github.com/marketplace/actions/azure-policy-compliance-scan)veya [Azure Pipelines güvenlik ve uyumluluk değerlendirmesi görevini](/azure/devops/pipelines/tasks/deploy/azure-policy) kullanın. İlkeleri ve atamaları test etmek için kullanılan ortamda hem uyumlu hem de uyumlu olmayan kaynaklar olmalıdır.
Kod için iyi bir birim testi gibi, kaynakların beklenen şekilde olduğunu ve yanlış pozitif veya yanlış-negatifler olduğunu test etmek istersiniz. Test edin ve yalnızca beklediğiniz kadar doğrulandıysanız, ilkeden beklenmedik ve tanımlanamayan etki olabilir. Daha fazla bilgi için bkz. [Yeni bir Azure ilke tanımının etkisini değerlendirme](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Düzeltme görevlerini etkinleştir

Atamanın doğrulanması beklentileri karşılıyorsa, sonraki adım düzeltmeyi doğrulamaktır.
[Deployifnotexists](./effects.md#deployifnotexists) veya [MODIFY](./effects.md#modify) kullanan ilkeler bir düzeltme görevine açılabilir ve uyumlu olmayan bir durumda kaynakları doğru şekilde düzeltebilir.

Kaynakları yeniden düzeltme için ilk adım, ilke tanımında tanımlanan rol ataması ilke atamasını verildir. Bu rol ataması, kaynağı uyumlu hale getirmek için gerekli değişiklikleri yapmak üzere ilke atama tarafından yönetilen kimliğe yeterli haklar verir.

İlke atamasının uygun haklara sahip olması durumunda, uyumsuz olduğu bilinen bir kaynak kümesine karşı düzeltme görevi tetiklemek için Ilke SDK 'sını kullanın. Devam etmeden önce bu düzeltilen görevlerle üç test tamamlanmalıdır:

- Düzeltme görevinin başarıyla tamamlandığını doğrulama
- İlke uyumluluğu sonuçlarının beklendiği gibi güncelleştirildiğini görmek için ilke değerlendirmesini Çalıştır
- Özelliklerinin değiştiğini doğrulamak için kaynaklara karşı doğrudan bir ortam birimi testi çalıştırın

Hem güncelleştirilmiş ilke değerlendirme sonuçlarının hem de ortamın test edilmesi, düzeltme görevlerinin beklenmiş olduğunu değiştiği ve ilke tanımının uyumluluk değişikliğini beklendiği gibi gördiğine ilişkin onay sağlar.

### <a name="update-to-enforced-assignments"></a>Zorlanan atamalara güncelleştirme

Tüm doğrulama kapıları tamamlandıktan sonra, _etkin_' ın **Enforcementmode** öğesini kullanmak için atamayı güncelleştirin. Bu değişikliği ilk olarak üretim ortamından aynı ortamda yapmanız önerilir. Bu ortam beklenildiği şekilde doğrulandıktan sonra, bu, ilke üretim kaynaklarına dağıtılana kadar, daha sonra bir sonraki ortamı dahil edilmelidir ve bu şekilde devam eder.

## <a name="process-integrated-evaluations"></a>İşlem tümleşik değerlendirmeleri

Kod olarak Azure Ilkesi için genel iş akışı, bir ortamda bir ortama ilke ve girişim geliştirme ve dağıtmaya yöneliktir. Ancak, ilke değerlendirmesi Azure 'da uygulama dağıtma veya altyapı oluşturmak için ARM şablonları çalıştırma gibi kaynakları dağıtan veya oluşturan herhangi bir iş akışı için dağıtım sürecinin bir parçası olmalıdır.

Bu durumlarda, uygulama veya altyapı dağıtımı bir test aboneliğine veya kaynak grubuna gerçekleştirildikten sonra, bu kapsam için ilke değerlendirmesi yapılmalıdır. bu kapsam, var olan tüm ilke ve girişimlerin doğrulanmasını denetlemelidir. Bu tür bir ortamda **Enforcementmode** _devre dışı bırakılmış_ olarak yapılandırılabilirler ancak, bir uygulama veya altyapı dağıtımının ilke tanımlarının erken ihlal edildiğini erken bilmek yararlı olur. Bu ilke değerlendirmesi bu iş akışlarında bir adım olmalıdır ve uyumlu olmayan kaynaklar oluşturan dağıtımlar başarısız olur.

## <a name="review"></a>Gözden geçirme

Bu makalede, kod olarak Azure Ilkesi için genel iş akışı ve ayrıca ilke değerlendirmesi diğer dağıtım iş akışlarının parçası olmalıdır. Bu iş akışı, tetikleyicilere göre betikleştirilmiş adımları ve otomasyonu destekleyen herhangi bir ortamda kullanılabilir. Bu iş akışını GitHub 'da kullanmaya yönelik bir öğretici için bkz. [öğretici: Azure Ilkesini GitHub Ile kod olarak uygulama](../tutorials/policy-as-code-github.md).

## <a name="next-steps"></a>Sonraki adımlar

- [İlke tanımı yapısı](./definition-structure.md)hakkında bilgi edinin.
- [İlke atama yapısı](./assignment-structure.md)hakkında bilgi edinin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/get-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
