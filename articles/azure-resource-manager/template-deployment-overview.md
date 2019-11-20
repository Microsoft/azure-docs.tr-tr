---
title: Şablonlara genel bakış
description: Kaynak dağıtımı için Azure Resource Manager şablonları kullanma avantajlarını açıklar.
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 0765ec1cd5952a05a168dfed72ba4d6fd78ed89c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150294"
---
# <a name="azure-resource-manager-templates-overview"></a>Azure Resource Manager şablonlarına genel bakış

Buluta geçiş sayesinde, birçok ekip çevik geliştirme yöntemlerini benimsemiştir. Bu takımlar hızla yineleyebilir. Çözümlerini buluta sürekli olarak dağıtmaları ve altyapısının güvenilir bir durumda olduğunu bilmeniz gerekir. Altyapı yinelemeli işlemin bir parçası haline geldiği için, işlemler ve geliştirme arasındaki bölme kayboldu. Ekiplerin, Birleşik bir işlem aracılığıyla altyapıyı ve uygulama kodunu yönetmesi gerekir.

Bu güçlükleri karşılamak için dağıtımları otomatik hale getirebilir ve kod olarak altyapı uygulamasını kullanabilirsiniz. Kodda, dağıtılması gereken altyapıyı tanımlarsınız. Altyapı kodu projenizin bir parçası haline gelir. Uygulama kodu gibi, altyapı kodunu bir kaynak deposunda depolar ve sürümü. Takımınızda herhangi biri kodu çalıştırabilir ve benzer ortamları dağıtabilir.

Altyapıyı Azure çözümleriniz için kod olarak uygulamak üzere Azure Resource Manager şablonlarını kullanın. Şablon, projeniz için altyapıyı ve yapılandırmayı tanımlayan bir JavaScript Nesne Gösterimi (JSON) dosyasıdır. Şablon bildirim temelli sözdizimini kullanır, bu, oluşturmak için programlama komutlarının dizisini yazmak zorunda kalmadan ne dağıtmayı istediğinizi belirtmenize olanak tanır. Şablonda, dağıtılacak kaynakları ve bu kaynakların özelliklerini belirtirsiniz.

## <a name="why-choose-resource-manager-templates"></a>Kaynak Yöneticisi şablonlarını neden seçmeliyim?

Kaynak Yöneticisi şablonları ve diğer altyapılardan birini kod hizmetleri olarak kullanmaya karar vermeye çalışıyorsanız, şablonları kullanmanın aşağıdaki avantajlarını göz önünde bulundurun:

* **Bildirime dayalı sözdizimi**: Kaynak Yöneticisi şablonlar tüm Azure altyapısını bildirimli olarak oluşturmanıza ve dağıtmanıza olanak tanır. Örneğin, yalnızca sanal makineler, aynı zamanda ağ altyapısı, depolama sistemleri ve ihtiyacınız olabilecek diğer kaynaklar için dağıtım yapabilirsiniz.

* **Tekrarlanabilir sonuçlar**: altyapınızı geliştirme yaşam döngüsü boyunca sürekli olarak dağıtın ve kaynaklarınızın tutarlı bir şekilde dağıtılmasını sağlayabilirsiniz. Şablonlar, aynı şablonu birçok kez dağıtabileceğiniz ve aynı durumda aynı kaynak türlerini alabileceği anlamına gelen ıdempotent. Güncelleştirmeleri temsil etmek için çok sayıda ayrı şablon geliştirmek yerine istenen durumu temsil eden bir şablon geliştirebilirsiniz.

* **Orchestration**: sıralama işlemlerinin karmaşıklıkları hakkında endişelenmeniz gerekmez. Kaynak Yöneticisi, birbirine bağlı kaynakların dağıtımını doğru sırada oluşturulacak şekilde düzenler. Mümkün olduğunda, dağıtımlarınızın seri dağıtımlardan daha hızlı tamamlanabilmesi için Kaynak Yöneticisi Kaynakları paralel olarak dağıtır. Şablonu, birden çok zorunlu komut yerine bir komut aracılığıyla dağıtırsınız.

   ![Şablon dağıtımı karşılaştırma](./media/template-deployment-overview/template-processing.png)

* **Yerleşik doğrulama**: şablonunuz yalnızca doğrulama işlemi yapıldıktan sonra dağıtılır. Kaynak Yöneticisi dağıtımın başarılı olduğundan emin olmak için dağıtımı başlatmadan önce şablonu denetler. Dağıtımınız, yarı tamamlanmış bir durumda durmak daha az olabilir.

* **Modüler dosyalar**: şablonlarınızı daha küçük, yeniden kullanılabilir bileşenlere bölebilir ve dağıtım zamanında birbirine bağlayabilirsiniz. Ayrıca, bir şablonu başka bir şablon içinde iç içe geçirebilirsiniz.

* **Herhangi bir Azure kaynağı oluşturun**: şablonlarda yeni Azure hizmetlerini ve özelliklerini hemen kullanabilirsiniz. Kaynak sağlayıcısı yeni kaynakları kullanıma sunarak, bu kaynakları şablonlar aracılığıyla dağıtabilirsiniz. Yeni hizmetleri kullanmadan önce araçların veya modüllerin güncelleştirilmesini beklemeniz gerekmez.

* **İzlenen dağıtımlar**: Azure Portal dağıtım geçmişini gözden geçirebilir ve şablon dağıtımı hakkında bilgi alabilirsiniz. Dağıtılan şablonu, geçirilen parametre değerlerini ve tüm çıktı değerlerini görebilirsiniz. Kod hizmetleri olarak diğer altyapı Portal üzerinden izlenmez.

   ![Dağıtım geçmişi](./media/template-deployment-overview/deployment-history.png)

* **Kod olarak ilke**: [Azure ilkesi](../governance/policy/overview.md) , idare otomatikleştiren kod çerçevesi olarak bir ilkedir. Azure ilkeleri kullanıyorsanız, ilke düzeltme, şablonlar aracılığıyla dağıtıldığında uyumlu olmayan kaynaklar üzerinde yapılır.

* **Dağıtım planları**: Microsoft tarafından, mevzuat ve uyumluluk standartlarını karşılamak [için sunulan planların](../governance/blueprints/overview.md) avantajlarından yararlanabilirsiniz. Bu planlar, çeşitli mimarilere yönelik önceden oluşturulmuş şablonlar içerir.

* **CI/CD tümleştirmesi**: şablonları, sürekli tümleştirme ve sürekli DAĞıTıM (CI/CD) araçlarınızla tümleştirebilir. Bu, hızlı ve güvenilir uygulama ve altyapı güncelleştirmelerine yönelik yayın işlem hatlarınızı otomatikleştirebilirler. Azure DevOps ve Kaynak Yöneticisi şablon görevi kullanarak Azure Resource Manager şablon projelerini sürekli olarak derlemek ve dağıtmak için Azure Pipelines kullanabilirsiniz. Daha fazla bilgi edinmek için bkz. işlem [hatları Ile vs projesi](./vs-resource-groups-project-devops-pipelines.md) ve [Azure Pipelines ile sürekli tümleştirme](./resource-manager-tutorial-use-azure-pipelines.md).

* **Dışarı aktarılabilir kod**: kaynak grubunun geçerli durumunu dışarı aktararak veya belirli bir dağıtım için kullanılan şablonu görüntüleyerek mevcut kaynak grubu için bir şablon alabilirsiniz. [Dışarı aktarılan şablonu](export-template-portal.md) görüntülemek şablon söz dizimi hakkında bilgi edinmek için yararlı bir yoldur.

* **Yazma araçları**: şablonları [Visual Studio Code](resource-manager-tools-vs-code.md) ve şablon aracı uzantısıyla yazabilirsiniz. IntelliSense, sözdizimi vurgulama, çevrimiçi Yardım ve diğer birçok dil işlevini edinirsiniz. Visual Studio Code ' a ek olarak, [Visual Studio 'yu](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)da kullanabilirsiniz.

## <a name="template-file"></a>Şablon dosyası

Şablonunuzda, JSON özelliklerini genişleten [şablon ifadeleri](template-expressions.md) yazabilirsiniz. Bu ifadeler Kaynak Yöneticisi tarafından sunulan [işlevleri](resource-group-template-functions.md) kullanır.

Şablon aşağıdaki bölümlere sahiptir:

* [Parametreler](template-parameters.md) -dağıtım sırasında, aynı şablonun farklı ortamlarla kullanılmasına izin veren değerler sağlar.

* [Değişkenler](template-variables.md) -şablonlarınız için yeniden kullanılan değerleri tanımlayın. Parametre değerlerinden oluşturulabilirler.

* [Kullanıcı tanımlı işlevler](template-user-defined-functions.md) -şablonunuzu basitleştiren özelleştirilmiş işlevler oluşturun.

* [Kaynaklar](resource-group-authoring-templates.md#resources) -dağıtılacak kaynakları belirtin.

* [Çıktılar](template-outputs.md) -dağıtılan kaynaklardan değer döndürür.

## <a name="template-deployment-process"></a>Şablon dağıtımı işlemi

Bir şablonu dağıtırken Kaynak Yöneticisi şablonu REST API işlemlerine dönüştürür. Örneğin, Resource Manager aşağıdaki kaynak tanımına sahip bir şablonu aldığında:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Tanımı aşağıdaki REST API işlemine dönüştürerek Microsoft.Storage kaynak sağlayıcısına gönderir:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-design"></a>Şablon tasarımı

Şablonları ve kaynak gruplarını tanımlama şekli tamamen size ve çözümünüzü yönetme biçiminize bağlıdır. Örneğin, üç katmanlı uygulamanızı tek bir şablondan tek bir kaynak grubuna dağıtabilirsiniz.

![üç katmanlı şablon](./media/template-deployment-overview/3-tier-template.png)

Ancak, bütün altyapınızı tek bir şablonda tanımlamak zorunda değilsiniz. Genellikle, en uygun seçenek dağıtım gereksinimlerinizi hedeflenen, amaca yönelik bir dizi şablona bölüştürmektir. Bu şablonları farklı çözümler için kolayca yeniden kullanabilirsiniz. Belirli bir çözümü dağıtmak için gerekli tüm şablonların bağlandığı bir ana şablon oluşturun. Aşağıdaki görüntüde üç katmanlı çözümün iç içe üç şablon içeren ana şablon aracılığıyla nasıl dağıtıldığı gösterilmektedir.

![iç içe geçmiş şablon](./media/template-deployment-overview/nested-tiers-template.png)

Katmanlarınızın farklı yaşam döngülerine sahip olacağını düşünüyorsanız, üç katmanı farklı kaynak gruplarına dağıtabilirsiniz. Kaynaklar diğer kaynak gruplarındaki kaynaklara bağlanmaya devam edebilir.

![katman şablonu](./media/template-deployment-overview/tier-templates.png)

İç içe geçmiş şablonlar hakkında daha fazla bilgi için bkz. [Azure Resource Manager ile bağlı şablonları kullanma](resource-group-linked-templates.md).

## <a name="next-steps"></a>Sonraki adımlar

* Şablon oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için bkz. [öğretici: ilk Azure Resource Manager şablonunuzu oluşturma ve dağıtma](template-tutorial-create-first-template.md).
* Şablon dosyalarındaki özellikler hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](resource-group-authoring-templates.md).
* Şablonları dışarı aktarma hakkında bilgi edinmek için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](./resource-manager-quickstart-create-templates-use-the-portal.md).
