---
title: "Azure Resource Manager’a Genel Bakış | Microsoft Belgeleri"
description: "Azure’daki kaynakların dağıtımı, yönetimi ve erişim denetimi için Azure Resource Manager’ın nasıl kullanılacağı açıklanmaktadır."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 76df7de1-1d3b-436e-9b44-e1b3766b3961
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 6d88b7c5ad96a1c7cfb60bde3c9d952b654adb9f
ms.lasthandoff: 03/31/2017


---
# <a name="azure-resource-manager-overview"></a>Azure Resource Manager genel bakış
Uygulamanızın altyapısı genellikle bir sanal makine, depolama hesabı, sanal ağ veya web uygulaması, veritabanı, veritabanı sunucusu ya da 3. taraf hizmetler gibi birçok bileşenden meydana gelir.  Bu bileşenleri ayrı varlıklar olarak değerlendirmez, bunun yerine bunları tek bir varlığın ilgili ve birbirine bağımlı parçaları olarak kabul edersiniz. Bunları gruplar halinde dağıtmak, yönetmek ve izlemek isteyebilirsiniz. Azure Resource Manager, çözümünüzdeki kaynaklar ile gruplar halinde çalışmanıza olanak sağlar. Çözümünüzdeki tüm kaynakları tek ve eşgüdümlü bir işlemle dağıtabilir, güncelleştirebilir veya silebilirsiniz. Dağıtım için bir şablon kullanabilirsiniz. Üstelik bu şablon test, hazırlık ve üretim gibi farklı ortamlarda da çalışabilir. Resource Manager kaynaklarınızı dağıttıktan sonra yönetmenize yardımcı olmak için güvenlik, denetleme ve etiketleme özellikleri sunar. 

## <a name="terminology"></a>Terminoloji
Azure Resource Manager’ı kullanmaya yeni başladıysanız bilmiyor olabileceğiniz bazı terimler vardır.

* **kaynak** - Azure ile kullanılabilen yönetilebilir bir öğe. Sanal makine, depolama hesabı, web uygulaması, veritabanı ve sanal ağ bazı yaygın kaynaklardandır, ancak çok daha fazlası mevcuttur.
* **kaynak grubu** - Bir Azure çözümü için ilgili kaynakları bir arada tutan bir kapsayıcıdır. Kaynak grubu bir çözümün tüm kaynaklarını veya yalnızca grup olarak yönetmek istediğiniz kaynakları içerebilir. Kuruluş için önemli olan faktörleri temel alarak kaynakları kaynak gruplarına nasıl ayıracağınıza siz karar verirsiniz. Bkz. [Kaynak grupları](#resource-groups).
* **kaynak sağlayıcısı** - Resource Manager aracılığıyla dağıtabileceğiniz ve yönetebileceğiniz kaynakları sağlayan bir hizmet. Her kaynak sağlayıcısı dağıtılan kaynaklarla çalışmaya yönelik işlemler sunar. Sanal makine kaynağı sağlayan Microsoft.Compute, depolama hesabı kaynağı sağlayan Microsoft.Storage ve web uygulamalarıyla ilgili kaynakları sağlayan Microsoft.Web bazı yaygın kaynak sağlayıcılarındandır. Bkz. [Kaynak sağlayıcıları](#resource-providers).
* **Resource Manager şablonu** - Bir kaynak grubuna dağıtılacak bir veya daha fazla kaynağı tanımlayan JavaScript Nesne Gösterimi (JSON) dosyası. Ayrıca dağıtılan kaynaklar arasındaki bağımlılıkları tanımlar. Şablon, kaynakları tutarlı ve sürekli olarak dağıtmak için kullanılabilir. Bkz. [Şablon dağıtımı](#template-deployment).
* **bildirim temelli söz dizimi** - Oluşturmaya yönelik programlama komutları dizisini yazmak zorunda kalmadan "Oluşturmak istediğiniz şeyi" belirtmenize imkan tanıyan söz dizimi. Resource Manager şablonu, bildirim temelli söz diziminin bir örneğidir. Dosya içinde Azure’a dağıtılacak altyapının özelliklerini tanımlarsınız. 

## <a name="the-benefits-of-using-resource-manager"></a>Resource Manager’ı kullanmanın avantajları
Resource Manager çeşitli avantajlar sunar:

* Çözümünüzdeki tüm kaynakları ayrı ayrı ele almak yerine bunları grup halinde dağıtabilir, yönetebilir ve izleyebilirsiniz.
* Çözümünüzü geliştirme yaşam döngüsü boyunca defalarca dağıtabilirsiniz. Kaynaklarınızın tutarlı bir durumda dağıtılması da size güven verir.
* Altyapınızı betikler yerine bildirim temelli şablonları kullanarak yönetebilirsiniz.
* Doğru sırayla dağıtılmalarını sağlamak için kaynaklarınız arasındaki bağımlılıkları tanımlayabilirsiniz.
* Rol Tabanlı Erişim Denetimi (RBAC) yönetim platformuyla doğrudan tümleşik olduğu için kaynak grubunuzdaki tüm hizmetlere erişim denetimi uygulayabilirsiniz.
* Aboneliğinizdeki tüm kaynakları mantıksal olarak düzenlemek için kaynaklarınıza etiketler ekleyebilirsiniz.
* Aynı etiketi paylaşan bir kaynak grubunun maliyetlerini görüntüleyerek kuruluşunuzun faturalarına açıklık getirebilirsiniz.  

Resource Manager çözümlerinizi dağıtmanın ve yönetmenin yeni bir yolunu sunar. Önceki dağıtım modelini kullandıysanız ve değişiklikler hakkında bilgi edinmek isterseniz, bkz. [Resource Manager dağıtımını ve klasik dağıtımı anlama](resource-manager-deployment-model.md)

## <a name="consistent-management-layer"></a>Tutarlı yönetim katmanı
Resource Manager; Azure PowerShell, Azure CLI, Azure portalı, REST API’si ve geliştirme araçları ile gerçekleştirdiğiniz görevler için tutarlı bir yönetim katmanı sunar. Tüm araçlar ortak işlem kümesini kullanmaktadır. İşinize en çok yarayan araçları kullanabilir ve kafa karışıklığı yaşamadan araçlar arasında geçiş yapabilirsiniz. 

Aşağıdaki görüntüde tüm araçların aynı Azure Resource Manager API’si ile nasıl etkileşimde bulunduğu gösterilmektedir. API, istekleri kimlik doğrulamasından geçiren ve yetkilendiren Resource Manager hizmetine iletir. Resource Manager da bu istekleri ilgili kaynak sağlayıcılarına yönlendirir.

![Resource Manager istek modeli](./media/resource-group-overview/consistent-management-layer.png)

## <a name="guidance"></a>Rehber
Çözümleriniz üzerinde çalışırken aşağıdaki önerilerden yararlanarak Resource Manager’dan tam anlamıyla yararlanabilirsiniz.

1. Altyapınızı kesinlik temelli komutlar yerine Resource Manager şablonlarındaki bildirim temelli söz dizimini kullanarak tanımlayabilir ve dağıtabilirsiniz.
2. Şablondaki tüm dağıtım ve yapılandırma adımlarını tanımlayın. Çözümünüzü ayarlarken el ile yapılan hiçbir adım olmaması gerekir.
3. Kaynaklarınızı yönetirken, örneğin bir uygulamayı veya makineyi başlatırken ya da durdururken, kesinlik temelli komutlar çalıştırın.
4. Kaynak grubundaki aynı yaşam döngüsüne sahip kaynakları düzenleyin. Diğer tüm düzenleyici kaynaklar için etiketler kullanın.

Şablonlar hakkında öneriler için bkz. [Azure Resource Manager şablonları oluşturmaya yönelik en iyi uygulamalar](resource-manager-template-best-practices.md).

Kuruluşların abonelikleri etkili bir şekilde yönetmek için Resource Manager'ı nasıl kullanabileceği hakkında yönergeler için bkz. [Azure kurumsal iskelesi: öngörücü abonelik idaresi](resource-manager-subscription-governance.md).

## <a name="resource-groups"></a>Kaynak grupları
Kaynak gruplarınızı tanımlarken göz önüne almanız gereken bazı önemli faktörler bulunur:

1. Grubunuzdaki tüm kaynaklar aynı yaşam döngüsünü paylaşmalıdır. Bunları birlikte dağıtır, güncelleştirir ve silersiniz. Veritabanı sunucusu gibi bir kaynağın farklı bir dağıtım döngüsünde bulunması gerekiyorsa, bu kaynak farklı bir kaynak grubuna konulmalıdır.
2. Her kaynak yalnızca bir kaynak grubunda yer alabilir.
3. Bir kaynak grubuna dilediğiniz zaman kaynak ekleyebilir veya kaldırabilirsiniz.
4. Bir kaynağı, bir kaynak grubundan bir diğerine taşıyabilirsiniz. Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](resource-group-move-resources.md).
5. Bir kaynak grubu farklı bölgelerde bulunan kaynakları içerebilir.
6. Bir kaynak grubu, yönetim eylemleri için erişim denetimini incelemek üzere kullanılabilir.
7. Bir kaynak diğer kaynak gruplarındaki kaynaklarla etkileşim kurabilir. İki kaynak ilişkili olmasına karşın aynı yaşam döngüsünü paylaşmadığında (örneğin, bir veritabanına bağlanan web uygulamaları) bu etkileşim yaygın olarak görülür.

Bir kaynak grubu oluştururken bu kaynak grubu için bir konum belirtmeniz gerekir. "Bir kaynak grubu için neden konum gerekli olsun? Ayrıca kaynaklar kaynak grubundan farklı konumlarda olabiliyorsa kaynak grubu konumu neden önemli olsun?" diye soruyor olabilirsiniz Kaynak grubu, kaynaklarla ilgili meta verileri depolar. Bu nedenle, kaynak grubu için bir konum belirttiğinizde meta verilerin nereye depolanacağını belirtirsiniz. Uyumluluk nedeniyle verilerinizin belirli bir bölgeye depolandığından emin olmanız gerekebilir.

## <a name="resource-providers"></a>Kaynak sağlayıcıları
Her kaynak sağlayıcısı bir Azure hizmetiyle çalışmaya yönelik bir dizi kaynak ve işlem sunar. Örneğin, anahtarları ve parolaları saklamak isterseniz **Microsoft.KeyVault** kaynak sağlayıcısı ile çalışırsınız. Bu kaynak sağlayıcısı, anahtar kasasını oluşturmak için **vaults** adlı bir kaynak türü sağlar. 

Kaynaklarınızı dağıtmaya başlamadan önce kullanılabilir kaynak sağlayıcılarını anlamanız gerekir. Kaynak sağlayıcılarının ve kaynakların adlarını bilmeniz, Azure’a dağıtmak istediğiniz kaynakları tanımlamanıza yardımcı olur.

Portal üzerinden tüm kaynak sağlayıcıları görebilirsiniz. Aboneliğinizin dikey penceresinde **Kaynak sağlayıcıları** öğesini seçin:

![kaynak sağlayıcılarını görüntüleme](./media/resource-group-overview/view-resource-providers.png)

Tüm kaynak sağlayıcılarını aşağıdaki PowerShell cmdlet’i ile alabilirsiniz:

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Veya Azure CLI 2.0 kullanarak tüm kaynak sağlayıcılarını aşağıdaki komutla alabilirsiniz:

```azurecli
az provider list
```

Kullanmanız gereken kaynak sağlayıcıları için döndürülen listeye bakabilirsiniz.

Bir kaynak sağlayıcısına ilişkin ayrıntıları almak için sağlayıcı ad alanını komutunuza ekleyin. Komut, kaynak sağlayıcısı için desteklenen kaynak türlerini ve her bir kaynak türüne ilişkin desteklenen konumlar ile API sürümlerini döndürür. Aşağıdaki PowerShell cmdlet’i Microsoft.Compute hakkındaki ayrıntıları alır:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Veya Azure CLI 2.0 kullanarak aşağıdaki komutla Microsoft.Compute için desteklenen kaynak türlerini, konumları ve API sürümlerini alabilirsiniz:

```azurecli
az provider show --namespace Microsoft.Compute
```

Daha fazla bilgi için bkz. [Resource Manager sağlayıcıları, bölgeleri, API sürümleri ve şemaları](resource-manager-supported-services.md).

## <a name="template-deployment"></a>Şablon dağıtımı
Resource Manager’ı kullanarak Azure çözümünüzün altyapısını ve yapılandırmasını tanımlayan bir şablon (JSON biçiminde) oluşturabilirsiniz. Bir şablon kullanarak çözümünü yaşam döngüsü boyunca defalarca dağıtabilir ve kaynaklarınızın tutarlı bir durumda dağıtıldığından emin olabilirsiniz. Portaldan bir çözüm oluşturduğunuzda çözüm otomatik olarak bir dağıtım şablonu içerir. Bir şablonla başlayacağınız ve bu şablonu size özel ihtiyaçlara göre özelleştirebileceğiniz için yeni bir şablon oluşturmanız gerekmez. Kaynak grubunun mevcut durumunu dışarı aktararak veya belirli bir dağıtım için kullanılan şablonu görüntüleyerek mevcut kaynak grubu için bir şablon elde edebilirsiniz. [Dışarı aktarılan şablonu](resource-manager-export-template.md) görüntülemek şablon söz dizimi hakkında bilgi edinmek için yararlı bir yoldur.

Şablon biçimi ve nasıl oluşturulduğu hakkında bilgi almak için bkz. [İlk Azure Resource Manager şablonunuzu oluşturma](resource-manager-create-first-template.md). Kaynak türleri için JSON söz dizimini görüntülemek üzere bkz. [Azure Resource Manager şablonlarında kaynak tanımlama](/azure/templates/).

Resource Manager, şablonu diğer istekler gibi işler ([Tutarlı yönetim katmanı](#consistent-management-layer) görüntüsüne bakın). Şablonu ayrıştırarak söz dizimini ilgili kaynak sağlayıcıları için REST API işlemlerine dönüştürür. Örneğin, Resource Manager aşağıdaki kaynak tanımına sahip bir şablonu aldığında:

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

Şablonları ve kaynak gruplarını tanımlama şekli tamamen size ve çözümünüzü yönetme biçiminize bağlıdır. Örneğin, üç katmanlı uygulamanızı tek bir şablondan tek bir kaynak grubuna dağıtabilirsiniz.

![üç katmanlı şablon](./media/resource-group-overview/3-tier-template.png)

Ancak, bütün altyapınızı tek bir şablonda tanımlamak zorunda değilsiniz. Genellikle, en uygun seçenek dağıtım gereksinimlerinizi hedeflenen, amaca yönelik bir dizi şablona bölüştürmektir. Bu şablonları farklı çözümler için kolayca yeniden kullanabilirsiniz. Belirli bir çözümü dağıtmak için gerekli tüm şablonların bağlandığı bir ana şablon oluşturun. Aşağıdaki görüntüde üç katmanlı çözümün iç içe üç şablon içeren ana şablon aracılığıyla nasıl dağıtıldığı gösterilmektedir.

![iç içe geçmiş şablon](./media/resource-group-overview/nested-tiers-template.png)

Katmanlarınızın farklı yaşam döngülerine sahip olacağını düşünüyorsanız, üç katmanı farklı kaynak gruplarına dağıtabilirsiniz. Kaynaklar diğer kaynak gruplarındaki kaynaklara bağlanmaya devam edebilir.

![katman şablonu](./media/resource-group-overview/tier-templates.png)

Şablonlarınızı tasarlama hakkında daha fazla öneri için bkz. [Azure Resource Manager şablonlarını tasarlama düzenleri](best-practices-resource-manager-design-templates.md). İç içe geçmiş şablonlar hakkında daha fazla bilgi için bkz. [Azure Resource Manager ile bağlı şablonları kullanma](resource-group-linked-templates.md).

Dağıtımı otomatik hale getirme hakkında dört bölümlük bir seri için bkz. [Azure Sanal Makinelerine uygulama dağıtımlarını otomatik hale getirme](../virtual-machines/windows/dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Bu seri; uygulama mimarisi, erişim ve güvenlik, kullanılabilirlik ve ölçek ve uygulama dağıtımını kapsar.

Azure Resource Manager kaynakların doğru sırada oluşturulmasını sağlamak için bağımlılıkları analiz eder. Bir kaynak başka bir kaynaktaki değere bağımlıysa (diskler için depolama hesabına ihtiyaç duyan bir sanal makine gibi) bir bağımlılık ayarlayın. Daha fazla bilgi için bkz. [Azure Resource Manager’da bağımlılıkları tanımlama](resource-group-define-dependencies.md).

Ayrıca, şablonu altyapıda yapılan güncelleştirmeler için kullanabilirsiniz. Örneğin, çözümünüze bir kaynak ekleyebilir ve zaten dağıtılmış kaynaklar için yapılandırma kuralları ekleyebilirsiniz. Şablon zaten var olan bir kaynak için bir kaynak oluşturulmasını belirtiyorsa, Azure Resource Manager yeni bir varlık oluşturmak yerine bir güncelleştirme gerçekleştirir. Azure Resource Manager varlığı yeni oluşturulacak kaynak ile aynı duruma gelecek şekilde güncelleştirir.  

Resource Manager, kurulumun içermediği belirli bir yazılımı yükleme gibi ek işlemlere ihtiyaç duymanız halinde size uzantılar sunar. Zaten DSC, Chef veya Puppet gibi bir yapılandırma yönetim hizmeti kullanıyorsanız, uzantıları kullanarak bu hizmetle çalışmaya devam edebilirsiniz. Sanal makine uzantıları hakkında daha fazla bilgi için bkz. [Sanal makine uzantıları ve özellikleri hakkında](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Son olarak, uygulamanızın kaynak kodunun bir parçası haline gelir. Bunu kaynak kod deponuza dahil edebilir ve uygulamanız geliştikçe buna göre güncelleştirebilirsiniz. Visual Studio üzerinden şablonu düzenleyebilirsiniz.

Şablonunuzu tanımladıktan sonra kaynakları Azure'a dağıtmak için hazır olursunuz. Kaynakları dağıtma komutları için bkz.:

* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](resource-group-template-deploy.md)
* [Kaynakları Resource Manager şablonları ve Azure CLI ile dağıtma](resource-group-template-deploy-cli.md)
* [Kaynakları Resource Manager şablonları ve Azure portalı ile dağıtma](resource-group-template-deploy-portal.md)
* [Kaynakları Resource Manager şablonları ve Resource Manager REST API’si ile dağıtma](resource-group-template-deploy-rest.md)

## <a name="tags"></a>Etiketler
Resource Manager, kaynakları yönetme ve fatura gereksinimlerine göre kategorize etmenize olanak tanıyan bir etiketleme özelliği sunar. Karmaşık bir kaynak grubu ve kaynak koleksiyonunuz olduğunda ve bu varlıkları sizin için anlamlı bir şekilde görselleştirmeniz gerektiğinde etiketleri kullanabilirsiniz. Örneğin, kuruluşunuzda benzer görevleri üstlenen veya aynı departmana ait olan kaynakları etiketleyebilirsiniz. Kuruluşunuzdaki kullanıcılar etiketleri kullanmadan birden fazla kaynak oluşturduğunda, bunları daha sonra tanımlamak ve yönetmek zor olabilir. Örneğin, belirli bir projenin tüm kaynaklarını silmek isteyebilirsiniz. Kaynaklar proje için etiketlenmemişse bunları el ile bulmanız gerekir. Etiketleme, aboneliğinizden doğan gereksiz maliyetleri azaltmanın önemli bir yoludur. 

Kaynakların bir etiketi paylaşması için aynı kaynak grubunda bulunmaları gerekmez. Kuruluşunuzdaki tüm kullanıcıların yanlışlıkla birbirinden kısmen farklı etiketler (örneğin “departman” yerine “depart.”) kullanmak yerine genel etiketler kullanmasını sağlamak için kendi etiket sınıflandırmanızı oluşturabilirsiniz.

Aşağıdaki örnekte sanal makineye uygulanan bir etiket gösterilmektedir.

```json
"resources": [    
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2015-06-15",
    "name": "SimpleWindowsVM",
    "location": "[resourceGroup().location]",
    "tags": {
        "costCenter": "Finance"
    },
    ...
  }
]
```

Bir etiket değerine sahip tüm kaynakları almak için aşağıdaki PowerShell cmdlet'ini kullanın:

```powershell
Find-AzureRmResource -TagName costCenter -TagValue Finance
```

Veya aşağıdaki Azure CLI 2.0 komutunu kullanın:

```azurecli
az resource list --tag costCenter=Finance
```

Azure portalından etiketli kaynakları da görüntüleyebilirsiniz.

Aboneliğinize ait [kullanım raporu](../billing/billing-understand-your-bill.md), maliyetleri etiketlere göre ayırmanızı sağlayan etiket adları ve değerler içerir. Etiketler hakkında daha fazla bilgi için bkz. [Etiketleri kullanarak Azure kaynaklarınızı düzenleme](resource-group-using-tags.md).

## <a name="access-control"></a>Erişim denetimi
Resource Manager, belirli eylemlere kuruluşunuzda kimlerin erişebildiğini denetlemenize olanak tanır. Rol tabanlı erişim denetimini (RBAC) doğrudan yönetim platformu ile tümleştirir ve bu erişim denetimini kaynak grubunuzdaki tüm hizmetlere uygular. 

Rol tabanlı erişim denetimi ile çalışırken anlamanız gereken iki ana kavram vardır:

* Rol tanımları: Bir izin kümesini açıklar ve birden fazla atama için kullanılabilir.
* Rol atamaları: Belirli bir kapsam (abonelik, kaynak grubu veya kaynak) için bir tanımı bir kimlikle (kullanıcı veya grup) ilişkilendirir. Atama, alt kapsamlar tarafından devralınır.

Kullanıcıları önceden tanımlanmış platforma ve kaynağa özgü rollere ekleyebilirsiniz. Örneğin, kullanıcıların kaynakları görüntülemesine izin veren, ancak değiştirmesine izin vermeyen Okuyucu adlı önceden tanımlanmış rolden yararlanabilirsiniz. Kuruluşunuzda Okuyucu rolüne bu tür bir erişimin gerektiği kullanıcılar ekler ve bu rolü aboneliğe, kaynak grubuna ya da kaynağa uygularsınız.

Azure aşağıdaki dört platform rolünü sağlar:

1. Sahip - erişim dahil her şeyi yönetebilir
2. Katılımcı - erişim dışında her şeyi yönetebilir
3. Okuyucu - her şeyi görüntüleyebilir, ancak değişiklik yapamaz
4. Kullanıcı Erişimi Yöneticisi - Azure kaynaklarına kullanıcı erişimini yönetebilir

Azure ayrıca kaynağa özgü birkaç rol sağlar. Yaygın olanlarından bazıları şunlardır:

1. Sanal Makine Katılımcısı - sanal makineleri yönetebilir, ancak sanal makinelere erişim veremez ve bunların bağlı olduğu sanal ağı ya da depolama hesabını yönetemez
2. Ağ Katılımcısı - tüm ağ kaynaklarını yönetebilir, ancak bunlara erişimi veremez
3. Depolama Hesabı Katılımcısı - Depolama hesaplarını yönetebilir, ancak bunlara erişimi veremez
4. SQL Server Katılımcısı - SQL sunucularını ve veritabanlarını yönetebilir, ancak güvenlikle ilgili ilkelerini yönetemez
5. Web Sitesi Katılımcısı - Web sitelerini yönetebilir, ancak bağlı oldukları web planlarını yönetemez

Rollerin ve izin verilen eylemlerin tam listesi için bkz. [RBAC: Yerleşik Roller](../active-directory/role-based-access-built-in-roles.md). Rol tabanlı erişim denetimi hakkında daha fazla bilgi için bkz. [Azure Rol Tabanlı Erişim Denetimi](../active-directory/role-based-access-control-configure.md). 

Bazı durumlarda, kaynaklara erişen bir kod ya da komut dosyası çalıştırmak istersiniz, ancak bunu bir kullanıcının kimlik bilgileri altında çalıştırmayı istemezsiniz. Bunun yerine, uygulama için hizmet sorumlusu adlı bir kimlik oluşturmak ve hizmet sorumlusu için uygun rolü atamak istersiniz. Resource Manager, uygulama için kimlik bilgileri oluşturmanızı ve uygulamanın kimliğini programlı olarak doğrulamanızı sağlar. Hizmet sorumluları oluşturma hakkında bilgi için aşağıdaki konulardan birine bakın:

* [Kaynaklara erişmek üzere hizmet sorumlusu oluşturmak için Azure PowerShell kullanma](resource-group-authenticate-service-principal.md)
* [Kaynaklara erişmek üzere hizmet sorumlusu oluşturmak için Azure CLI kullanma](resource-group-authenticate-service-principal-cli.md)
* [Kaynaklara erişebilen bir Active Directory uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](resource-group-create-service-principal-portal.md)

Kullanıcıların kritik kaynakları silmesini ve değiştirmesini önlemek için bunları açıkça kilitleyebilirsiniz. Daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](resource-group-lock-resources.md).

## <a name="activity-logs"></a>Etkinlik günlükleri
Resource Manager bir kaynağı oluşturan, değiştiren veya silen tüm işlemleri günlüğe kaydeder. Sorun giderme sırasında bir hata bulmak veya kuruluşunuzdaki kullanıcının bir kaynağı nasıl değiştirdiğini izlemek için etkinlik günlüklerini kullanabilirsiniz. Günlükleri görmek için bir kaynak grubunun **Ayarlar** dikey penceresindeki **Etkinlik günlükleri** öğesini seçin. İşlemi hangi kullanıcının başlattığı dahil olmak üzere, filtreleri çok sayıda farklı değere göre filtreleyebilirsiniz. Etkinlik günlükleri ile çalışma hakkında daha fazla bilgi için bkz. [Azure kaynaklarını yönetmek için etkinlik günlüklerini görüntüleme](resource-group-audit.md).

## <a name="customized-policies"></a>Özelleştirilmiş ilkeler
Resource Manager kaynaklarınızı yönetmek üzere özelleştirilmiş ilkeler oluşturmanıza olanak tanır Oluşturduğunuz ilke türleri çeşitli senaryolar içerebilir. Kaynaklar üzerinde bir adlandırma kuralı uygulayabilir, hangi kaynak türlerinin ve örneklerinin dağıtılabileceğini sınırlayabilir veya hangi bölgelerin bir kaynak türünü barındırabileceğini sınırlayabilirsiniz. Faturaları bölümlere göre düzenlemek için kaynaklar üzerinde bir etiket değeri olmasını isteyebilirsiniz. Aboneliğinizin maliyetlerini düşürmeye ve tutarlılık sağlanmasına yardımcı olmak üzere ilkeler oluşturabilirsiniz. 

İlkeleri JSON ile tanımlar ve sonra bu ilkeleri aboneliğinize ya da bir kaynak grubuna uygularsınız. İlkeler kaynak türlerine uygulandığı için rol tabanlı erişim denetiminden farklıdır.

Aşağıdaki örnekte tüm kaynakların bir costCenter etiketi içerdiğini belirterek etiket tutarlılığını sağlayan ilke gösterilmektedir.

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

Oluşturabileceğiniz birçok ilke türü daha vardır. Daha fazla bilgi için bkz. [Kaynakları yönetmek ve erişimi denetlemek için İlke kullanma](resource-manager-policy.md).

## <a name="sdks"></a>SDK’lar
Azure SDK'ları birden çok dil ve platform için kullanılabilir.
Bu dil uygulamalarının her biri ekosistem paket yöneticisi ve GitHub üzerinden kullanılabilir.

Bu SDK’ların her birindeki kod Azure RESTful API belirtimlerinden oluşturulur.
Bu belirtimler açık kaynaklıdır ve Swagger 2.0 belirtimini temel alır.
SDK kodu, AutoRest adlı açık kaynaklı bir proje aracılığıyla oluşturulur.
AutoRest bu RESTful API belirtimlerini birden fazla dilde istemci kitaplıklarına dönüştürür.
SDK’larda oluşturulan kodu herhangi bir açıdan geliştirmek isterseniz SDK’ları oluşturmaya yönelik tüm araçlar açıktır, ücretsiz olarak kullanılabilir ve geniş ölçekte benimsenen bir API belirtim biçimini temel alır.

Açık Kaynak SDK depolarımız aşağıda verilmiştir. Geri bildirimler, sorunlar ve çekme isteklerini memnuniyetle karşılıyoruz.

[.NET](https://github.com/Azure/azure-sdk-for-net) | [Java](https://github.com/Azure/azure-sdk-for-java) | [Node.js](https://github.com/Azure/azure-sdk-for-node) | [PHP](https://github.com/Azure/azure-sdk-for-php) | [Python](https://github.com/Azure/azure-sdk-for-python) | [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [!NOTE]
> SDK gerekli işlevleri sağlamıyorsa doğrudan [Azure REST API'sine](https://docs.microsoft.com/rest/api/resources/) de çağrı yapabilirsiniz.
> 
> 

## <a name="samples"></a>Örnekler
### <a name="net"></a>.NET
* [Azure kaynaklarını ve kaynak gruplarını yönetme](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
* [Bir şablonla SSH özellikli bir VM dağıtma](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)

### <a name="java"></a>Java
* [Azure kaynaklarını yönetme](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource/)
* [Azure kaynak gruplarını yönetme](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
* [Bir şablonla SSH özellikli bir VM dağıtma](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)

### <a name="nodejs"></a>Node.js
* [Azure kaynaklarını ve kaynak gruplarını yönetme](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
* [Bir şablonla SSH özellikli bir VM dağıtma](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)

### <a name="python"></a>Python
* [Azure kaynaklarını ve kaynak gruplarını yönetme](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
* [Bir şablonla SSH özellikli bir VM dağıtma](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)

### <a name="ruby"></a>Ruby
* [Azure kaynaklarını ve kaynak gruplarını yönetme](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)
* [Bir şablonla SSH özellikli bir VM dağıtma](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)

Bu örneklere ek olarak galeri örnekleri arasında arama yapabilirsiniz.

[.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) | [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) | [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs) | [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python) | [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

## <a name="next-steps"></a>Sonraki adımlar
* Şablonlar ile çalışmaya genel bir giriş yapmak için bkz. [Mevcut kaynaklardan Azure Resource Manager şablonu aktarma](resource-manager-export-template.md).
* Şablon oluşturmayla ilgili daha kapsamlı bir kılavuz için bkz. [İlk Azure Resource Manager şablonunuzu oluşturma](resource-manager-create-first-template.md).
* Bir şablonda kullanabileceğiniz işlevleri anlamak için bkz. [Şablon işlevleri](resource-group-template-functions.md)
* Resource Manager ile Visual Studio’yu kullanma hakkında bilgi edinmek için bkz. [Azure kaynak gruplarını Visual Studio aracılığıyla oluşturma ve dağıtma](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

Bu genel bakışın sunulduğu videoya şuradan ulaşabilirsiniz:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Azure-Resource-Manager-Overview/player]


[powershellref]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/azurerm.resources

