---
title: İlkeyi Kod iş akışları olarak tasarklama
description: Azure Ilke tanımlarınızı kod olarak dağıtmak ve kaynakları otomatik olarak doğrulamak için iş akışları tasarlamayı öğrenin.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 935616145ee32bea8c3b514c495690ce4105cb80
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072084"
---
# <a name="design-policy-as-code-workflows"></a>İlkeyi Kod iş akışları olarak tasarklama

Bulut yönetimi ile yolculukta ilerleyerek, Azure portal her bir ilke tanımını veya çeşitli SDK 'Ları, kurumsal ölçekte daha yönetilebilir ve yinelenebilir olacak şekilde el ile yönetmeyi tercih edersiniz. Sistemleri bulutta ölçeklendirerek yönetmek için hakim yaklaşımlardan ikisi şunlardır:

- Kod olarak altyapı: ortamlarınızı tanımlayan içeriği, Kaynak Yöneticisi şablonlarından her şeyi Azure Ilke tanımlarına kaynak kodu olarak Azure 'a göre değerlendirme yöntemi.
- DevOps: son kullanıcılarımıza sürekli değer teslimi sağlayan kişiler, süreç ve ürünlerin birleşimi.

Kod olarak ilke, bu fikirlerin birleşimidir. Temelde, ilke tanımlarınızı kaynak denetiminde tutun, her değişiklik yapıldığında bu değişikliği test edin ve doğrulayın. Bununla birlikte, kod veya DevOps ile altyapıyla ilke katılımı olması gerekmez.

Doğrulama adımı aynı zamanda diğer sürekli tümleştirme veya sürekli dağıtım iş akışlarının bir bileşeni olmalıdır. Örnek olarak bir uygulama ortamının veya sanal altyapının dağıtımı sayılabilir. Azure Ilke doğrulamasını derleme ve dağıtım sürecinin erken bir bileşeni haline getirerek uygulama ve operasyon ekipleri, değişiklikleri çok geç olmayan ve üretimde dağıtmaya çalıştıkları zaman, daha uzun bir süre boyunca, daha fazla zaman uyumlu olup olmadığını bulur.

## <a name="workflow-overview"></a>İş akışına genel bakış

Kod olarak Ilkenin önerilen genel iş akışı şu diyagram gibi görünür:

![Kod iş akışına genel bakış olarak ilke](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>İlke tanımları oluşturma ve güncelleştirme

İlke tanımları JSON kullanılarak oluşturulur ve kaynak denetiminde depolanır. Her ilke, aynı klasöre depolanması gereken parametreler, kurallar ve ortam parametreleri gibi kendi dosya kümesidir. Aşağıdaki yapı, ilke tanımlarınızı kaynak denetiminde tutmanın önerilen bir yoludur.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Yeni bir ilke eklendiğinde veya var olan bir ilke güncelleştirilirse, iş akışı Azure 'daki ilke tanımını otomatik olarak güncelleştirmelidir. Yeni veya güncelleştirilmiş ilke tanımının testi sonraki bir adımda gelir.

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
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

İlke tanımları gibi, var olan bir girişimi eklerken veya güncelleştirirken, iş akışı otomatik olarak Azure 'daki girişim tanımını güncelleştirmelidir. Yeni veya güncelleştirilmiş girişim tanımının testi sonraki bir adımda gelir.

### <a name="test-and-validate-the-updated-definition"></a>Güncelleştirilmiş tanımı test edin ve doğrulayın

Otomasyon yeni oluşturulmuş veya güncelleştirilmiş ilke ya da girişim tanımlarınızı tamamladıktan sonra Azure 'daki nesne ile ilgili güncelleştirmeyi yaptıktan sonra, yapılan değişiklikleri test etmek zaman alabilir. Uygulamasının parçası olan ilke veya girişim, üretimden en uzak ortamda bulunan kaynaklara atanmalıdır. Bu ortam genellikle _dev_'dir.

Atama, kaynak oluşturma ve güncelleştirmelerin engellenmemesi, ancak mevcut kaynakların güncelleştirilmiş ilke tanımıyla uyumluluk için denetlenmeye devam edebilmesi için, _Disabled_ 'ın [Enforcementmode](./assignment-structure.md#enforcement-mode) kullanması gerekir. EnforcementMode da dahil olmak üzere, atama kapsamının bir kaynak grubu ya da ilkeleri doğrulamak için özel olarak kullanılan bir abonelik olması önerilir.

> [!NOTE]
> Zorlama modu yararlı olsa da, çeşitli koşullarda bir ilke tanımının kapsamlı bir şekilde test edilmesine yönelik bir değişiklik değildir. İlke tanımı, `PUT` ve `PATCH` REST API çağrılar, uyumlu ve uyumlu olmayan kaynaklar ve uç durumları, kaynakta eksik bir özellik gibi test edilmelidir.

Atama dağıtıldıktan sonra, yeni atama için [uyumluluk verilerini almak](../how-to/get-compliance-data.md) üzere ilke SDK 'sını kullanın. İlkeleri ve atamaları test etmek için kullanılan ortamda hem uyumlu hem de uyumlu olmayan kaynaklar olmalıdır. Kod için iyi bir birim testi gibi, kaynakların beklenen şekilde olduğunu ve yanlış pozitif veya yanlış-negatifler olduğunu test etmek istersiniz. Test edin ve yalnızca beklediğiniz kadar doğrulandıysanız, ilkeden beklenmedik ve tanımlanamayan etki olabilir. Daha fazla bilgi için bkz. [Yeni bir Azure ilkesinin etkisini değerlendirme](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Düzeltme görevlerini etkinleştir

Atamanın doğrulanması beklentileri karşılıyorsa, sonraki adım düzeltmeyi doğrulamaktır.
[Deployifnotexists](./effects.md#deployifnotexists) veya [MODIFY](./effects.md#modify) kullanan ilkeler bir düzeltme görevine açılabilir ve uyumlu olmayan bir durumda kaynakları doğru şekilde düzeltebilir.

Bunu yapmanın ilk adımı, ilke tanımına, ilke tanımında tanımlanan rol atamasını vermek için kullanılır. Bu rol ataması, kaynağı uyumlu hale getirmek için gerekli değişiklikleri yapmak üzere ilke atama tarafından yönetilen kimliğe yeterli haklar verir.

İlke atamasının uygun haklara sahip olması durumunda, uyumsuz olduğu bilinen bir kaynak kümesine karşı düzeltme görevi tetiklemek için Ilke SDK 'sını kullanın. Devam etmeden önce bu düzeltilen görevlerle üç test tamamlanmalıdır:

- Düzeltme görevinin başarıyla tamamlandığını doğrulama
- İlke uyumluluğu sonuçlarının beklendiği gibi güncelleştirildiğini görmek için ilke değerlendirmesini Çalıştır
- Özelliklerinin değiştiğini doğrulamak için kaynaklara karşı doğrudan bir ortam birimi testi çalıştırın

Hem güncelleştirilmiş ilke değerlendirme sonuçlarının hem de ortamın test edilmesi, düzeltme görevlerinin beklenmiş olduğunu değiştiği ve ilke tanımının uyumluluk değişikliğini beklendiği gibi gördiğine ilişkin onay sağlar.

### <a name="update-to-enforced-assignments"></a>Zorlanan atamalara güncelleştirme

Tüm doğrulama kapıları tamamlandıktan sonra, _etkin_' ın **Enforcementmode** öğesini kullanmak için atamayı güncelleştirin. Bu değişiklik başlangıçta üretimden yalnızca aynı ortamda yapılmalıdır. Bu ortam beklenen şekilde doğrulandıktan sonra, bu değişiklik daha sonra, ilke üretim kaynaklarına dağıtılana kadar bir sonraki ortamı dahil edilmelidir.

## <a name="process-integrated-evaluations"></a>İşlem tümleşik değerlendirmeleri

Kod olarak Ilke için genel iş akışı, bir ortamda bir ortama ilke ve girişim geliştirme ve dağıtmaya yöneliktir. Ancak, ilke değerlendirmesi, Azure 'da uygulama dağıtma veya altyapı oluşturmak için Kaynak Yöneticisi şablonları çalıştırma gibi kaynakları dağıtan veya oluşturan herhangi bir iş akışı için dağıtım sürecinin bir parçası olmalıdır.

Bu durumlarda, uygulama veya altyapı dağıtımı bir test aboneliğine veya kaynak grubuna gerçekleştirildikten sonra, bu kapsam için ilke değerlendirmesi yapılmalıdır. bu kapsam, var olan tüm ilke ve girişimlerin doğrulanmasını denetlemelidir. Bu tür bir ortamda **Enforcementmode** _devre dışı bırakılmış_ olarak yapılandırılabilirler ancak, bir uygulama veya altyapı dağıtımının ilke tanımlarının erken ihlal edildiğini erken bilmek yararlı olur. Bu ilke değerlendirmesi bu iş akışlarında bir adım olmalıdır ve uyumlu olmayan kaynaklar oluşturan dağıtımlar başarısız olur.

## <a name="review"></a>Gözden geçirme

Bu makalede kod olarak Ilke için genel iş akışı ve ayrıca ilke değerlendirmesi diğer dağıtım iş akışlarının parçası olmalıdır. Bu iş akışı, tetikleyicilere göre betikleştirilmiş adımları ve otomasyonu destekleyen herhangi bir ortamda kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [İlke tanımı yapısı](./definition-structure.md)hakkında bilgi edinin.
- [İlke atama yapısı](./assignment-structure.md)hakkında bilgi edinin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/getting-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.