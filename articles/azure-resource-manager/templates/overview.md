---
title: Şablonlara genel bakış
description: Kaynak dağıtımı için Azure Resource Manager şablonları (ARM şablonları) kullanmanın avantajlarını açıklar.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 14bcbbd7a7ae7315dbb8e9d3e7e44ce0ffe0a4b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419992"
---
# <a name="what-are-arm-templates"></a>ARM şablonları nelerdir?

Buluta geçiş sayesinde, birçok ekip çevik geliştirme yöntemlerini benimsemiştir. Bu takımlar hızla yineleyebilir. Çözümlerini buluta sürekli olarak dağıtmaları ve altyapısının güvenilir bir durumda olduğunu bilmeniz gerekir. Altyapı yinelemeli işlemin bir parçası haline geldiği için, işlemler ve geliştirme arasındaki bölme kayboldu. Ekiplerin, Birleşik bir işlem aracılığıyla altyapıyı ve uygulama kodunu yönetmesi gerekir.

Bu güçlükleri karşılamak için dağıtımları otomatik hale getirebilir ve kod olarak altyapı uygulamasını kullanabilirsiniz. Kodda, dağıtılması gereken altyapıyı tanımlarsınız. Altyapı kodu projenizin bir parçası haline gelir. Uygulama kodu gibi, altyapı kodunu bir kaynak deposunda depolar ve sürümü. Takımınızda herhangi biri kodu çalıştırabilir ve benzer ortamları dağıtabilir.

Altyapıyı Azure çözümleriniz için kod olarak uygulamak üzere Azure Resource Manager şablonları (ARM şablonları) kullanın. Şablon, projeniz için altyapıyı ve yapılandırmayı tanımlayan bir JavaScript Nesne Gösterimi (JSON) dosyasıdır. Şablon, dağıtmak istediğiniz öğeyi oluşturmaya yönelik programlama komutları dizisini yazmak zorunda kalmadan bu öğeyi belirtmenize imkan tanıyan bildirim temelli söz dizimini kullanır. Şablonda, dağıtılacak kaynakları ve bu kaynakların özelliklerini belirtirsiniz.

ARM şablonları geliştirmek için yeni bir dil sunuyoruz. Dil Bıcep olarak adlandırılır ve şu anda önizlemededir. Bıcep ve JSON şablonları aynı yetenekleri sunar. İki dil arasında şablon dönüştürebilirsiniz. Bıcep, şablon oluşturmak için daha kolay bir sözdizimi sağlar. Daha fazla bilgi için bkz. [Bıcep (Önizleme) nedir?](bicep-overview.md).

ARM şablonlarına nasıl başlabileceğinizi öğrenmek için aşağıdaki videoya bakın.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Enablement/How-and-why-to-learn-about-ARM-templates/player]

## <a name="why-choose-arm-templates"></a>ARM şablonlarını neden seçmelisiniz?

ARM şablonlarını kullanma ve kod hizmetleri olarak diğer altyapılardan biri arasında seçim yapmaya çalışıyorsanız, şablonları kullanmanın aşağıdaki avantajlarını göz önünde bulundurun:

* **Bildirime dayalı sözdizimi**: ARM şablonları, tüm Azure altyapısını bildirimli olarak oluşturmanıza ve dağıtmanıza olanak tanır. Örneğin, yalnızca sanal makineler, aynı zamanda ağ altyapısı, depolama sistemleri ve ihtiyacınız olabilecek diğer kaynaklar için dağıtım yapabilirsiniz.

* **Tekrarlanabilir sonuçlar**: altyapınızı geliştirme yaşam döngüsü boyunca sürekli olarak dağıtın ve kaynaklarınızın tutarlı bir şekilde dağıtılmasını sağlayabilirsiniz. Şablonlar, aynı şablonu birçok kez dağıtabileceğiniz ve aynı durumda aynı kaynak türlerini alabileceği anlamına gelen ıdempotent. Güncelleştirmeleri temsil etmek için çok sayıda ayrı şablon geliştirmek yerine istenen durumu temsil eden bir şablon geliştirebilirsiniz.

* **Orchestration**: sıralama işlemlerinin karmaşıklıkları hakkında endişelenmeniz gerekmez. Kaynak Yöneticisi, birbirine bağlı kaynakların dağıtımını doğru sırada oluşturulacak şekilde düzenler. Mümkün olduğunda, dağıtımlarınızın seri dağıtımlardan daha hızlı tamamlanabilmesi için Kaynak Yöneticisi Kaynakları paralel olarak dağıtır. Şablonu, birden çok zorunlu komut yerine bir komut aracılığıyla dağıtırsınız.

   ![Şablon dağıtımı karşılaştırma](./media/overview/template-processing.png)

* **Modüler dosyalar**: şablonlarınızı daha küçük, yeniden kullanılabilir bileşenlere bölebilir ve dağıtım zamanında birbirine bağlayabilirsiniz. Ayrıca, bir şablonu başka bir şablon içinde iç içe geçirebilirsiniz.

* **Herhangi bir Azure kaynağı oluşturun**: şablonlarda yeni Azure hizmetlerini ve özelliklerini hemen kullanabilirsiniz. Kaynak sağlayıcısı yeni kaynakları kullanıma sunarak, bu kaynakları şablonlar aracılığıyla dağıtabilirsiniz. Yeni hizmetleri kullanmadan önce araçların veya modüllerin güncelleştirilmesini beklemeniz gerekmez.

* **Genişletilebilirlik**: [dağıtım betikleri](deployment-script-template.md)ile şablonlarınıza PowerShell veya bash betikleri ekleyebilirsiniz. Dağıtım betikleri, dağıtım sırasında kaynakları ayarlama yeteneğinizi genişletmenizi sağlar. Bir betik, şablona dahil edilebilir veya bir dış kaynakta depolanabilir ve şablonda başvurulur. Dağıtım betikleri, uçtan uca ortam kurulumunuzu tek bir ARM şablonunda tamamlamanıza olanak sağlar.

* **Test etme**: Bu şablonu ARM şablon aracı seti (ARM-TTK) ile test ederek, şablonunuzun önerilen kurallara uyduğundan emin olabilirsiniz. Bu test kiti, [GitHub](https://github.com/Azure/arm-ttk)'dan indirebileceğiniz bir PowerShell betiğine sahiptir. Araç seti, şablon dilini kullanarak uzmanlık geliştirmenizi kolaylaştırır.

* **Değişiklikleri Önizle**: şablonu dağıtmadan önce değişikliklerin önizlemesini almak için [ne yapılır işlemini](template-deploy-what-if.md) kullanabilirsiniz. Bununla birlikte, hangi kaynakların oluşturulacağını, güncelleştirileceğini veya silineceğini ve değiştirilecek kaynak özelliklerini görürsünüz. Durum işlemi ortamınızın geçerli durumunu denetler ve durumu yönetme ihtiyacını ortadan kaldırır.

* **Yerleşik doğrulama**: şablonunuz yalnızca doğrulama işlemi yapıldıktan sonra dağıtılır. Kaynak Yöneticisi dağıtımın başarılı olduğundan emin olmak için dağıtımı başlatmadan önce şablonu denetler. Dağıtımınız, yarı tamamlanmış bir durumda durmak daha az olabilir.

* **İzlenen dağıtımlar**: Azure Portal dağıtım geçmişini gözden geçirebilir ve şablon dağıtımı hakkında bilgi alabilirsiniz. Dağıtılan şablonu, geçirilen parametre değerlerini ve tüm çıktı değerlerini görebilirsiniz. Kod hizmetleri olarak diğer altyapı Portal üzerinden izlenmez.

   ![Dağıtım geçmişi](./media/overview/deployment-history.png)

* **Kod olarak ilke**: [Azure ilkesi](../../governance/policy/overview.md) , idare otomatikleştiren kod çerçevesi olarak bir ilkedir. Azure ilkeleri kullanıyorsanız, ilke düzeltme, şablonlar aracılığıyla dağıtıldığında uyumlu olmayan kaynaklar üzerinde yapılır.

* **Dağıtım planları**: Microsoft tarafından, mevzuat ve uyumluluk standartlarını karşılamak [için sunulan planların](../../governance/blueprints/overview.md) avantajlarından yararlanabilirsiniz. Bu planlar, çeşitli mimarilere yönelik önceden oluşturulmuş şablonlar içerir.

* **CI/CD tümleştirmesi**: şablonları, sürekli tümleştirme ve sürekli DAĞıTıM (CI/CD) araçlarınızla tümleştirebilir. Bu, hızlı ve güvenilir uygulama ve altyapı güncelleştirmelerine yönelik yayın işlem hatlarınızı otomatikleştirebilirler. Azure DevOps ve Kaynak Yöneticisi şablon görevi kullanarak, ARM şablon projelerini sürekli olarak derlemek ve dağıtmak için Azure Pipelines kullanabilirsiniz. Daha fazla bilgi edinmek için bkz. işlem hatları ve öğretici [Ile vs projesi](add-template-to-azure-pipelines.md) [: Azure Pipelines Azure Resource Manager şablonlarının sürekli tümleştirilmesi](./deployment-tutorial-pipeline.md).

* **Dışarı aktarılabilir kod**: kaynak grubunun geçerli durumunu dışarı aktararak veya belirli bir dağıtım için kullanılan şablonu görüntüleyerek mevcut kaynak grubu için bir şablon alabilirsiniz. [Dışarı aktarılan şablonu](export-template-portal.md) görüntülemek şablon söz dizimi hakkında bilgi edinmek için yararlı bir yoldur.

* **Yazma araçları**: şablonları [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md) ve şablon aracı uzantısıyla yazabilirsiniz. IntelliSense, sözdizimi vurgulama, çevrimiçi Yardım ve diğer birçok dil işlevini edinirsiniz. Visual Studio Code Ayrıca, [Visual Studio 'yu](create-visual-studio-deployment-project.md)da kullanabilirsiniz.

## <a name="template-file"></a>Şablon dosyası

Şablonunuzda, JSON özelliklerini genişleten [şablon ifadeleri](template-expressions.md) yazabilirsiniz. Bu ifadeler Kaynak Yöneticisi tarafından sunulan [işlevleri](template-functions.md) kullanır.

Şablon aşağıdaki bölümlere sahiptir:

* [Parametreler](template-parameters.md) -dağıtım sırasında, aynı şablonun farklı ortamlarla kullanılmasına izin veren değerler sağlar.

* [Değişkenler](template-variables.md) -şablonlarınız için yeniden kullanılan değerleri tanımlayın. Parametre değerlerinden oluşturulabilirler.

* [Kullanıcı tanımlı işlevler](template-user-defined-functions.md) -şablonunuzu basitleştiren özelleştirilmiş işlevler oluşturun.

* [Kaynaklar](resource-declaration.md) -dağıtılacak kaynakları belirtin.

* [Çıktılar](template-outputs.md) -dağıtılan kaynaklardan değer döndürür.

## <a name="template-deployment-process"></a>Şablon dağıtımı işlemi

Bir şablonu dağıtırken Kaynak Yöneticisi şablonu REST API işlemlerine dönüştürür. Örneğin, Resource Manager aşağıdaki kaynak tanımına sahip bir şablonu aldığında:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-04-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "StorageV2",
    "properties": {}
  }
]
```

Tanımı aşağıdaki REST API işlemine dönüştürerek Microsoft.Storage kaynak sağlayıcısına gönderir:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2019-04-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "StorageV2",
  "properties": {}
}
```

Kaynak için şablonda ayarladığınız **Apiversion** 'ın Rest IŞLEMI için API sürümü olarak kullanıldığını unutmayın. Şablonu tekrar tekrar dağıtabilir ve güvenle çalışmaya devam edecektir. Aynı API sürümünü kullanarak, sonraki sürümlerde kullanıma sunulmuş olabilecek son değişiklikler hakkında endişelenmeniz gerekmez.

Bir şablonu dağıtmak için, aşağıdaki seçeneklerden birini kullanın:

* [Azure portalı](deploy-portal.md)
* [Azure CLI](deploy-cli.md)
* [PowerShell](deploy-powershell.md)
* [REST API](deploy-rest.md)
* [GitHub deposunda düğme](deploy-to-azure-button.md)
* [Azure Cloud Shell](deploy-cloud-shell.md)

## <a name="template-design"></a>Şablon tasarımı

Şablonları ve kaynak gruplarını tanımlama şekli tamamen size ve çözümünüzü yönetme biçiminize bağlıdır. Örneğin, üç katmanlı uygulamanızı tek bir şablondan tek bir kaynak grubuna dağıtabilirsiniz.

![üç katmanlı şablon](./media/overview/3-tier-template.png)

Ancak, bütün altyapınızı tek bir şablonda tanımlamak zorunda değilsiniz. Genellikle, en uygun seçenek dağıtım gereksinimlerinizi hedeflenen, amaca yönelik bir dizi şablona bölüştürmektir. Bu şablonları farklı çözümler için kolayca yeniden kullanabilirsiniz. Belirli bir çözümü dağıtmak için gerekli tüm şablonları bağlayan bir ana şablon oluşturursunuz. Aşağıdaki görüntüde üç katmanlı çözümün iç içe üç şablon içeren ana şablon aracılığıyla nasıl dağıtıldığı gösterilmektedir.

![iç içe geçmiş şablon](./media/overview/nested-tiers-template.png)

Katmanlarınızın farklı yaşam döngülerine sahip olacağını düşünüyorsanız, üç katmanı farklı kaynak gruplarına dağıtabilirsiniz. Kaynaklar diğer kaynak gruplarındaki kaynaklara bağlanmaya devam edebilir.

![katman şablonu](./media/overview/tier-templates.png)

İç içe geçmiş şablonlar hakkında daha fazla bilgi için bkz. [Azure Resource Manager ile bağlı şablonları kullanma](linked-templates.md).

## <a name="share-templates"></a>Şablonları paylaşma

Şablonunuzu oluşturduktan sonra kuruluşunuzdaki diğer kullanıcılarla paylaşmak isteyebilirsiniz. [Şablon Özellikleri](template-specs.md) , bir şablonu kaynak türü olarak depolamanızı sağlar. Şablon belirtimine erişimi yönetmek için rol tabanlı erişim denetimi kullanırsınız. Şablon belirtimine okuma erişimi olan kullanıcılar bu özelliği dağıtabilir ancak şablonu değiştirmez.

Bu yaklaşım, kuruluşunuzun standartlarını karşılayan şablonları güvenli bir şekilde paylaşabileceğiniz anlamına gelir.

## <a name="next-steps"></a>Sonraki adımlar

* Şablon oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için bkz. [öğretici: Ilk ARM şablonunuzu oluşturma ve dağıtma](template-tutorial-create-first-template.md).
* Microsoft Learn üzerinde bir Kılavuzlu dizi modülle ARM şablonları hakkında bilgi edinmek için bkz. [ARM şablonlarını kullanarak Azure 'da kaynakları dağıtma ve yönetme](/learn/paths/deploy-manage-resource-manager-templates/).
* Şablon dosyalarındaki özellikler hakkında daha fazla bilgi için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
* Şablonları dışarı aktarma hakkında bilgi edinmek için bkz. [hızlı başlangıç: Azure Portal kullanarak ARM şablonları oluşturma ve dağıtma](quickstart-create-templates-use-the-portal.md).
* Yaygın soruların yanıtları için bkz. [ARM şablonları hakkında sık sorulan sorular](frequently-asked-questions.md).
