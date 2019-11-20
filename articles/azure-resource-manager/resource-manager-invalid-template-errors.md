---
title: Geçersiz şablon hataları
description: Azure Resource Manager şablonları dağıtılırken geçersiz şablon hatalarının nasıl çözümleneceğini açıklar.
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.openlocfilehash: 7b9b8021ad34a7ec7f879ce9413f4ae5d44e2720
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150577"
---
# <a name="resolve-errors-for-invalid-template"></a>Geçersiz şablon hatalarını çözme

Bu makalede, geçersiz şablon hatalarının nasıl çözümleneceği açıklanır.

## <a name="symptom"></a>Belirti

Bir şablonu dağıttığınızda şunu belirten bir hata alırsınız:

```
Code=InvalidTemplate
Message=<varies>
```

Hata iletisi hata türüne bağlıdır.

## <a name="cause"></a>Nedeni

Bu hata, çeşitli farklı hata türlerinin oluşmasına neden olabilir. Genellikle şablonda bir sözdizimi veya yapısal hata içerir.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Çözüm 1-söz dizimi hatası

Şablonun doğrulanmadığını belirten bir hata iletisi alırsanız, şablonunuzda bir sözdizimi sorununuz olabilir.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Bu hata kolayca yapılabilir çünkü şablon ifadeleri karmaşık olabilir. Örneğin, bir depolama hesabı için aşağıdaki ad atamasında bir parantez kümesi, üç işlev, parantez kümesi, bir tek tırnak kümesi ve bir özellik vardır:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Eşleşen söz dizimini sağlamazsanız, şablon, sizin niyetinden farklı bir değer üretir.

Bu tür bir hata aldığınızda, ifade söz dizimini dikkatle gözden geçirin. [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) veya [VISUAL STUDIO Code](resource-manager-vs-code.md)gibi bir JSON Düzenleyicisi kullanmayı düşünün; bu, söz dizimi hataları hakkında sizi uyarabilir.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Çözüm 2-yanlış kesim uzunlukları

Kaynak adı doğru biçimde olmadığında başka bir geçersiz şablon hatası oluşur.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Kök düzeyi kaynak, ad içinde kaynak türünden daha az bir kesime sahip olmalıdır. Her segment bir eğik çizgiyle farklılaştırılabilir. Aşağıdaki örnekte, türünün iki kesimi vardır ve ad bir kesime sahiptir, bu nedenle **geçerli bir addır**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Ancak bir sonraki örnek, türle aynı sayıda parçaya sahip olduğundan **geçerli bir ad değildir** .

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Alt kaynaklar için tür ve ad aynı sayıda kesimde sahiptir. Alt öğenin tam adı ve türü, üst adı ve türü içerdiğinden, bu sayıda segment mantıklı olur. Bu nedenle, tam ad hala tam türden daha az bir kesime sahiptir.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

Segmentlerin alınması, kaynak sağlayıcıları genelinde uygulanan Kaynak Yöneticisi türleriyle karmaşık olabilir. Örneğin, bir Web sitesine kaynak kilidi uygulamak dört kesimle bir tür gerektirir. Bu nedenle, ad üç kesimdir:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Çözüm 3-parametre geçerli değil

İzin verilen değerlerden biri olmayan bir parametre değeri sağlarsanız, aşağıdaki hataya benzer bir ileti alırsınız:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Şablonda izin verilen değerleri çift işaretleyin ve dağıtım sırasında bir tane belirtin. İzin verilen parametre değerleri hakkında daha fazla bilgi için [Azure Resource Manager şablonlarının parametreler bölümüne](resource-group-authoring-templates.md#parameters)bakın.

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Çözüm 4-çok fazla hedef kaynak grubu

Tek bir dağıtımda beşten fazla hedef kaynak grubu belirtirseniz, bu hatayı alırsınız. Dağıtımınızdaki kaynak gruplarının sayısını veya bazı şablonların ayrı dağıtımlar olarak dağıtılmasını düşünün. Daha fazla bilgi için bkz. [Azure kaynaklarını birden fazla aboneliğe veya kaynak grubuna dağıtma](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Çözüm 5-döngüsel bağımlılık algılandı

Kaynakların, dağıtımın başlamasını engelleyecek şekilde birbirlerine bağlı olması durumunda bu hatayı alırsınız. Her iki veya daha fazla kaynak de bekleyen diğer kaynaklar için bir arada bir bağımlılık sağlar. Örneğin, Resource1 Resource3 'e bağlıdır, Resource2 Resource1 'e bağlıdır ve Resource3 Resource2 bağımlıdır. Bu sorunu, genellikle gereksiz bağımlılıkları kaldırarak çözebilirsiniz.

Döngüsel bağımlılığı çözümlemek için:

1. Şablonunuzda, döngüsel bağımlılığa göre tanımlanan kaynağı bulun. 
2. Bu kaynak için, bağımlı olduğu kaynakları görmek için **Bağımlıdson** özelliğini ve **başvuru** işlevinin tüm kullanımlarını inceleyin. 
3. Bağımlı oldukları kaynakları görmek için bu kaynakları inceleyin. Özgün kaynağa bağlı bir kaynak fark edinceye kadar bağımlılıkları izleyin.
5. Döngüsel bağımlılığa dahil olan kaynaklar için, gerekli olmayan bağımlılıkları belirlemek üzere **Bağımlıdson** özelliğinin tüm kullanımlarını dikkatle inceleyin. Bu bağımlılıkları kaldırın. Bağımlılığın gerekli olduğundan emin değilseniz kaldırmayı deneyin. 
6. Şablonu yeniden dağıtın.

**Bağımlıdson** özelliğinden değer kaldırma, şablonu dağıtırken hatalara neden olabilir. Bir hata alırsanız, bağımlılığı şablona geri ekleyin. 

Bu yaklaşım dairesel bağımlılığı çözmezse, dağıtım mantığınızın bir kısmını alt kaynaklara (uzantılar veya yapılandırma ayarları gibi) taşımayı göz önünde bulundurun. Bu alt kaynakları, döngüsel bağımlılığa dahil edilen kaynaklardan sonra dağıtılacak şekilde yapılandırın. Örneğin, iki sanal makine dağıttığını, ancak birbirlerine başvuran her bir üzerinde Özellikler ayarlamanız gerektiğini varsayalım. Bunları aşağıdaki sırayla dağıtabilirsiniz:

1. vm1
2. vm2
3. VM1 üzerindeki uzantı VM1 ve VM2 'ye bağımlıdır. Uzantı, VM1 'teki VM2 'dan aldığı değerleri ayarlar.
4. VM2 üzerindeki uzantı VM1 ve VM2 'ye bağımlıdır. Uzantı, VM2 'teki VM1 'dan aldığı değerleri ayarlar.

Aynı yaklaşım App Service uygulamalar için de geçerlidir. Yapılandırma değerlerini uygulama kaynağının alt bir kaynağına taşımayı düşünün. İki Web uygulaması aşağıdaki sırayla dağıtılabilir:

1. webapp1
2. webapp2
3. WebApp1 için yapılandırma WebApp1 ve webapp2 'ye bağımlıdır. Webapp2 ' deki değerler ile uygulama ayarlarını içerir.
4. webapp2 için yapılandırma WebApp1 ve webapp2 'ye bağımlıdır. WebApp1 ' deki değerler ile uygulama ayarlarını içerir.
