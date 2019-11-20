---
title: Şablonları bulutlar genelinde yeniden kullanma
description: Azure Resource Manager şablonları, farklı bulut ortamları için tutarlı çalışmasını geliştirin. Yeni veya var olan şablonları Azure Stack için güncelleştirilemiyor.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: 2964bb4365a2c153e7bc82c3292545ad4de985eb
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143778"
---
# <a name="develop-azure-resource-manager-templates-for-cloud-consistency"></a>Azure Resource Manager şablon bulut tutarlılık için geliştirme

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Azure'nın önemli bir avantajı, tutarlılık olur. Bir konum için geliştirme yatırımlarından başka bir programda yeniden kullanılabilir. Bir şablon dağıtımlarınızı genel Azure, Azure bağımsız bulutlarda ve Azure Stack gibi ortamlar genelinde tutarlı ve tekrarlanabilir yapar. Şablonları Bulutlar arasında yeniden kullanmak için ancak, bu kılavuzda açıklandığı gibi buluta özel bağımlılıklar dikkate almanız gerekir.

Microsoft, dahil olmak üzere farklı konumlarda akıllı, Kurumsal kullanıma hazır bir bulut hizmetleri sunar:

* Bölgeleri dünyanın dört bir yanındaki Microsoft tarafından yönetilen veri merkezlerinde, gittikçe büyüyen bir ağı tarafından desteklenen genel Azure platformu.
* Azure Almanya, Azure kamu ve Azure Çin (21Vianet tarafından işletilen Azure) gibi yalıtılmış bağımsız bulutlarda. Bağımsız bulutlarda genel Azure müşteri erişimi harika özelliklerin çoğu ile tutarlı bir platform sağlar.
* Azure Stack, olanak tanıyan bir karma bulut platformu, kuruluşunuzun veri merkezinden Azure Hizmetleri sunun. Kuruluşların kendi veri merkezlerini Azure yığını ayarlamak veya Azure Stack (barındırılan bölgeleri olarak da bilinir) kendi tesislerinde çalışan hizmet sağlayıcılarından Azure hizmetlerini kullanma.

Tüm bulutlar setinin Azure Resource Manager, çok çeşitli kullanıcı arabirimleri, Azure platformuyla iletişim kurmasına izin veren bir API sağlar. Bu API, güçlü kod olarak altyapı özellikleri sunar. Azure bulut platformunda kullanılabilir kaynak herhangi bir türde dağıtılabilir ve Azure Resource Manager ile yapılandırılmış. Basit bir şablonla dağıtma ve tam bir işlem bitiş durumuna uygulamanıza yapılandırın.

![Azure ortamları](./media/templates-cloud-consistency/environments.png)

Küresel Azure, bağımsız bulutlarda tutarlılığını barındırılan Bulut ve veri merkezinizi buluta Azure Kaynak Yöneticisi'nden yararlanabilir yardımcı olur. Şablon tabanlı bir kaynak dağıtımı ve yapılandırması ayarlama, geliştirme yatırımlarınızı bu Bulutlar arasında yeniden kullanabilirsiniz.

Ancak, olsa bile genel bağımsız, barındırılan ve karma Bulutlar tutarlı hizmetleri sağlamak için tüm Bulutlar aynıdır. Sonuç olarak, yalnızca belirli bir bulutta özellikleri ilgili bağımlılıkları olan bir şablon oluşturabilirsiniz.

Bu kılavuzun geri kalanını yeni ya da güncelleştirme mevcut Azure Resource Manager şablonları Azure Stack için geliştirme planlarken göz önünde bulundurmanız gereken alanlar açıklanır. Genel olarak, Denetim listesi, aşağıdaki öğeleri içermelidir:

* İşlevler, uç noktaları, hizmetleri ve diğer kaynakları şablonunuza hedef dağıtım konumlarda kullanılabilir olduğunu doğrulayın.
* İç içe geçmiş şablonlar ve yapıtlar yapılandırma erişilebilir konumlarda bulutlarda erişim sağlayarak Store.
* Dinamik başvuru kodlama sabit bağlantıları ve öğeleri yerine kullanın.
* Kullandığınız şablon parametreleri hedef bulutlarında çalıştığından emin olmak.
* Kaynağa özgü özellikleri bulunduğunu doğrulamak hedef bulut.

Azure Resource Manager şablonları bir giriş için bkz [şablon dağıtımı](template-deployment-overview.md).

## <a name="ensure-template-functions-work"></a>Şablon işlevlerinin çalıştığından emin olmak

Temel söz dizimi bir Resource Manager şablonu JSON ' dir. JSON, ifadeler ve İşlevler sözdizimiyle genişletme üst şablonlarını kullanın. Şablon dil işlemci ek şablon işlevleri desteklemek için sık sık güncelleştirilir. Kullanılabilir şablon işlevlerini ayrıntılı bir açıklaması için bkz. [Azure Resource Manager şablonu işlevleri](resource-group-template-functions.md).

Azure Resource Manager'a sunulan yeni şablon işlevleri, bağımsız bulutlarda veya Azure Stack'te hemen kullanılabilir değildir. Şablon başarıyla dağıtmak için şablonda başvurulan tüm işlevler hedef bulutta kullanılabilir olmalıdır. 

Azure Resource Manager özellikleri her zaman genel Azure'a ilk sunulacaktır. Yeni çıkan şablon işlevleri de Azure Stack'te kullanılabilir olup olmadığını doğrulamak için aşağıdaki PowerShell betiğini kullanabilirsiniz: 

1. GitHub deposunun bir kopya yapmak: [ https://github.com/marcvaneijk/arm-template-functions ](https://github.com/marcvaneijk/arm-template-functions).

1. Depo yerel kopyasını oluşturduktan sonra için hedef Azure Resource Manager PowerShell ile bağlanın.

1. Psm1 modülünü içeri aktarın ve test-AzureRmureRmTemplateFunctions cmdlet 'ini yürütün:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Birden çok betik dağıtır şablonları, her biri yalnızca benzersiz şablon işlevlerini içeren simge. Komut çıktısı, desteklenen ve kullanılabilir şablon işlevleri bildirir.

## <a name="working-with-linked-artifacts"></a>Bağlantılı yapıtlar ile çalışma

Şablon, bağlı yapıtlar için başvurular içerir ve başka bir şablona bağlanan bir dağıtım kaynağı içeriyor. (İç içe geçmiş şablon olarak da bilinir) bağlı şablonların, çalışma zamanında Resource Manager tarafından alınır. Bir şablon yapıtlar sanal makine (VM) uzantıları için başvurular da içerebilir. Bu yapılar, şablon dağıtımı sırasında yapılandırması VM uzantısı için bir VM içinde çalışan VM uzantısı tarafından alınır. 

Aşağıdaki bölümlerde, ana dağıtım şablonu için dış yapıtları dahil şablonlarınızı geliştirirken bulut tutarlılık için ilgili önemli noktalar açıklanmaktadır.

### <a name="use-nested-templates-across-regions"></a>Bölgeler arasında iç içe geçmiş şablonlarını kullanma

Şablonlar, her biri belirli bir amaca sahip küçük, yeniden kullanılabilir şablonlar ile ayrılmış ve dağıtım senaryoları arasında yeniden kullanılabilir. Dağıtımı yürütmek için ana veya ana şablon olarak bilinen tek bir şablonu belirtin. Bu, sanal ağlar, VM'ler ve web uygulamaları gibi dağıtmak amacıyla kaynaklarınızı belirtir. Ana Şablon bağlantı şablonları iç içe yerleştirebilirsiniz yani başka bir şablon de içerebilir. Benzer şekilde, bir iç içe geçmiş şablon diğer şablonlarının bağlantılarını içerir. En fazla beş düzey derinliğinde iç içe yerleştirebilirsiniz.

Aşağıdaki kod nasıl templateLink parametresi için bir iç içe geçmiş şablon başvuruyor gösterir:

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager, çalışma zamanında ana şablon değerlendirir ve alır ve her iç içe geçmiş şablon değerlendirir. Tüm iç içe geçmiş şablonlar alınır, şablon düzleştirilmiş ve daha fazla işleme başlatılır.

### <a name="make-linked-templates-accessible-across-clouds"></a>Bağlı şablonların Bulutlar arasında erişilebilir olun

Nerede ve nasıl göz önünde bulundurun herhangi depolamak için kullandığınız şablonları bağlı. Azure Resource Manager çalışma zamanında getirir; ve bu nedenle doğrudan erişim gerektirir — herhangi bir bağlantılı şablonları. İç içe geçmiş şablonlar depolamak için GitHub'ı kullanmak yaygın bir uygulamadır. Bir GitHub deposuna genel bir URL ile erişilebilir olan dosyalar içerebilir. Bu teknik genel Bulut ve bağımsız bulutlarda için iyi çalışır, ancak bir Azure Stack ortamınıza tüm giden Internet erişimi olmayan bağlantısı kesilmiş bir uzak konuma veya kurumsal ağ üzerinde yer olabilir. Bu gibi durumlarda, iç içe geçmiş şablonlarını almak üzere Azure Resource Manager başarısız olur. 

Bulutlar arası dağıtımlar için daha iyi bir uygulama için hedef Bulutu erişebileceği bir konuma bağlı şablonlarınızı depolamaktır. İdeal olarak tüm dağıtım yapıtları saklanır ve bir sürekli tümleştirme/sürekli geliştirme (CI/CD) işlem hattını dağıtılabilir. Alternatif olarak, Azure Resource Manager alabileceğini bir blob depolama kapsayıcısında, iç içe geçmiş şablonlar depolayabilir. 

Her bulut blob depolama farklı uç noktası bir tam etki alanı adı (FQDN) kullandığından, şablon iki parametre ile bağlı Şablonları konumu ile yapılandırın. Parametreleri, dağıtım sırasında kullanıcı girişi kabul edebilir. Şablonları genellikle yazılan ve birden çok kişi tarafından paylaşılabilir, böylece bu parametreler için standart bir ad kullanmak için en iyi uygulamadır. Adlandırma kuralları şablonları daha yeniden kullanılabilir bölgeler, Bulutlar ve yazarlar yardımcı olur.

Aşağıdaki kodda, `_artifactsLocation` dağıtımıyla ilgili tüm yapıtlar içeren tek bir konuma işaret edecek şekilde kullanılır. Varsayılan değer sağlanan dikkat edin. Dağıtım sırasında için hiçbir giriş değeri belirtilmişse `_artifactsLocation`, varsayılan değer kullanılır. `_artifactsLocationSasToken` İçin girdi olarak kullanılan `sasToken`. Varsayılan değer senaryoları için boş bir dize olmalıdır nerede `_artifactsLocation` güvenli değilse — Örneğin, bir ortak GitHub deposu.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Taban URI birleştirerek bağlantıları oluşturulan şablonun tamamında (gelen `_artifactsLocation` parametresi) bir yapıt göreli yolu ile ve `_artifactsLocationSasToken`. Aşağıdaki kod, URI şablon işlevi kullanılarak iç içe geçmiş şablon bağlantısını belirtmek gösterilmektedir:

```json
"resources": [
  {
    "name": "shared",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Varsayılan değer için bu yaklaşımı kullanarak `_artifactsLocation` parametresi kullanılır. Bağlı şablonlar, farklı bir konumdan alınacak gerekiyorsa, giriş parametresi dağıtım sırasında varsayılan değeri geçersiz kılmak için kullanılabilir — şablona hiçbir değişikliği gerekli değildir.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>_ArtifactsLocation yerine runbook'a kod bağlantıları kullanın.

İç içe geçmiş şablonlar, URL'de kullanılan yanı sıra `_artifactsLocation` parametresi, ilgili tüm yapıtlar bir dağıtım şablonu için bir temel olarak kullanılır. Bazı VM uzantıları, şablonun dışında depolanan bir komut dosyası için bir bağlantı içerir. Bu uzantılar için şimdilik bağlantıları gerekir. Örneğin, özel betik ve PowerShell DSC uzantı bir dış betik gösterildiği github'da bağlamak: 

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

Runbook'a kod betik bağlantılarını potansiyel olarak şablonu başarıyla başka bir konuma dağıtması engeller. Yapılandırma VM kaynağının sırasında sanal makine içinde çalışan VM Aracısı VM uzantısı'nda bağlı tüm betikleri indirilmesini başlatır ve ardından komut dosyaları sanal makinenin yerel diskte depolar. Bu yaklaşım işlevleri iç içe geçmiş şablon bağlantılar gibi daha önce "Kullanımı şablonları bölgeler arasında nested" bölümünde açıklanmıştır.

Resource Manager, çalışma zamanında iç içe geçmiş şablonlar alır. VM uzantıları için herhangi bir dış yapı alınmasını VM aracısı tarafından gerçekleştirilir. Yapıt alma farklı başlatan yanı sıra, şablon tanımı çözümde aynıdır. Tüm yapıtlar (VM uzantısı komut dosyaları) depolandığı temel yol ile bir varsayılan değer _artifactsLocation parametresini kullanın ve `_artifactsLocationSasToken` sasToken için giriş parametresi.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Bir yapıtın mutlak URI oluşturmak için tercih edilen yöntem URI şablon işlevi yerine concat şablon işlevi kullanmaktır. VM uzantısı Komut sabit kodlanmış bağlantı URI'si şablon işlevi ile değiştirerek, bu işlev şablonu bulut tutarlılık için yapılandırılır.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Bu yaklaşımda, yapılandırma betiklerini içeren tüm dağıtım yapıtları şablonu ile aynı konumda saklanabilir. Tüm bağlantıların konumunu değiştirmek için, yalnızca _Artifactslocation parametreleri_için farklı bır temel URL belirtmeniz gerekir.

## <a name="factor-in-differing-regional-capabilities"></a>Bölgesel özellikleri farklı etmeni

Çevik Geliştirme ve sürekli akışı güncelleştirmeleri ve yeni hizmetleri, Azure'da sunulan [bölgeleri gösterebileceğini](https://azure.microsoft.com/regions/services/) hizmetlerin veya güncelleştirmelerin kullanılabilirlik. Sıkı iç test edildikten sonra yeni hizmetlerin veya güncelleştirmelerin mevcut hizmetlere genellikle bir doğrulama programa katılan müşterilerin küçük bir kitle için kullanıma sunulmuştur. Başarılı müşteri doğrulama sonrasında hizmetlerin veya güncelleştirmelerin kümesini Azure bölgeleri içinde kullanılabilir hale sonra için daha fazla bölgede sunulan, bağımsız bulutlarda için piyasaya sunuluyor ve potansiyel olarak da Azure Stack müşterileri için kullanılabilir.

Azure bölgeleri ve bulut de kullanılabilir hizmetlerini değişebilir bilerek, bazı proaktif şablonlarınızı hakkında kararlar verebilirsiniz. Başlatmak için uygun bir bulut için kullanılabilir kaynak sağlayıcılarını inceleyerek yerdir. Bir kaynak sağlayıcısı, bir dizi kaynak ve bir Azure hizmeti için kullanılabilen işlemleri bildirir.

Bir şablon dağıtır ve kaynakları yapılandırır. Bir kaynak türü, bir kaynak sağlayıcısı tarafından sağlanır. Örneğin, işlem kaynak Sağlayıcısı'nı (Microsoft.Compute) virtualMachines ve availabilitySets gibi birden çok kaynak türü sağlar. Her kaynak sağlayıcısı, Azure Resource ortak bir anlaşma tarafından tanımlanan tüm kaynak sağlayıcıları arasında tutarlı ve birleştirilmiş bir yazma deneyimi etkinleştirme Manager'a bir API sağlar. Ancak, genel Azure'da kullanılabilir bir kaynak sağlayıcısı bağımsız bir bulut ya da bir Azure Stack bölgenizde kullanılamıyor olabilir.

![Kaynak sağlayıcıları](./media/templates-cloud-consistency/resource-providers.png) 

Belirli bir bulut kullanılabilir kaynak sağlayıcıları doğrulamak için Azure komut satırı arabirimi aşağıdaki betiği çalıştırın ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Kullanılabilir kaynak sağlayıcılarını görmek için aşağıdaki PowerShell cmdlet'ini de kullanabilirsiniz:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Tüm kaynak türleri sürümünü doğrulayın

Tüm kaynak türleri için ortak bir özellikler kümesini, ancak her kaynak kendi belirli özellikleri de vardır. Bazen yeni bir API sürümü aracılığıyla mevcut kaynak türleri için yeni özellikler ve ilgili özellikleri eklendi. Bir kaynağı bir şablonda kendi - API sürümü özelliği olan `apiVersion`. Bu sürüm bir şablon içinde varolan bir kaynak yapılandırma platformunda değişikliklerden etkilenmez sağlar.

Mevcut kaynak türlerine genel azure'da kullanıma sunulan yeni API sürümlerinde hemen tüm bölgeler, bağımsız bulutlarda veya Azure Stack kullanılamayabilir. Kullanılabilir kaynak sağlayıcıları, kaynak türleri ve API sürümleri için Bulutu listesini görüntülemek için Azure portalında kaynak Gezgini'ni kullanabilirsiniz. Tüm hizmetler menüsündeki kaynak Gezgini arayın. Tüm kullanılabilir kaynak sağlayıcıları, kaynak türlerini ve API sürümlerini, bulutta döndürmek için kaynak Gezgini'nde sağlayıcıları düğümünü genişletin.

Azure CLI'de verilen bulutundaki tüm kaynak türleri için kullanılabilir API sürümü listelemek için aşağıdaki betiği çalıştırın:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Aşağıdaki PowerShell cmdlet'ini de kullanabilirsiniz:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Bir parametre ile kaynak konumları bakın

Şablon, her zaman bir bölgede bulunan bir kaynak grubuna dağıtılır. Dağıtım kendisi yanı sıra, bir şablonunda her bir kaynak belirtmesine dağıtmak için kullandığınız bir location özelliği de vardır. Bulut tutarlılık için şablonunuzu geliştirmek için her Azure Stack benzersiz konum adlarını içerdiğinden kaynak konumları için dinamik bir yol gerekir. Genellikle kaynakları kaynak grubu ile aynı bölgede dağıtılır, ancak bölgeler arası uygulama kullanılabilirliği gibi senaryoları desteklemek için kaynakları bölgeler arasında yaymak yararlı olabilir.

Kaynak özelliklerini belirtmek için bir şablonunda sabit kodlamayın bölge adları olabilir olsa da, bölge adı büyük olasılıkla var olmadığından bu yaklaşım şablon diğer Azure Stack ortamlara dağıtılabilir garanti etmez.

Farklı bölgelerdeki uyum sağlamak için varsayılan bir değerle şablona bir giriş parametresi konumuna ekleyin. Dağıtım sırasında hiçbir değer belirtilmemişse, varsayılan değer kullanılır. 

Şablon işlevi `[resourceGroup()]` aşağıdaki anahtar/değer çiftleri içeren bir nesne döndürür:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Giriş parametresi defaultValue nesnenin konumu anahtarı başvurarak Azure Resource Manager çalışma zamanında yerini alır `[resourceGroup().location]` şablon işlevi şablonu dağıtıldığı kaynak grubu konumunu adı.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "name": "storageaccount1",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "location": "[parameters('location')]",
    ...
```

Bu şablon işlevi ile bile bölge adlarını önceden farkında olmadan şablonunuzu herhangi bir buluta dağıtabilirsiniz. Ayrıca, şablonda belirli bir kaynak için bir konum, kaynak grubu konumu farklı olabilir. Bu durumda, bunu diğer kaynakları aynı şablonu, yine de ilk konum giriş parametresi kullanırken belirli bu kaynak için ek giriş parametreleri kullanarak yapılandırabilirsiniz.

### <a name="track-versions-using-api-profiles"></a>API profillerini kullanarak sürümleri izleyin

Tüm kullanılabilir kaynak sağlayıcıları ve Azure Stack'te mevcut olan ilgili API sürümlerini izlemek için çok zor olabilir. Örneğin, yazma, en yeni API sürümüne zaman **Microsoft.Compute/availabilitySets** azure'da `2018-04-01`, Azure ve Azure Stack için ortak API sürümü kullanılabilir olsa da `2016-03-30`. Yaygın API sürümü için **Microsoft.Storage/storageAccounts** tüm Azure ve Azure Stack konumları arasında paylaşılan olan `2016-01-01`en yeni Azure API sürümünde bilgileriyse `2018-02-01`.

Bu nedenle, Resource Manager şablonları için API profillerini kavramını sundu. API profillerini bir şablonunda her bir kaynak ile yapılandırılmış bir `apiVersion` öğesi, belirli bir kaynak için API sürümü açıklanmaktadır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "apiVersion": "2016-03-30",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Bir API profili sürümü, Azure ve Azure Stack için ortak kaynak türü başına tek bir API sürümü için bir diğer ad olarak görev yapar. Her kaynak için bir API sürümü bir şablonda belirtmek yerine, yalnızca API profil sürümü adlı yeni bir kök öğesi içinde belirttiğiniz `apiProfile` ve çıkarın `apiVersion` tek tek kaynaklar için öğesi.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

API profili el ile belirli bir konumda kullanılabilir apiVersions doğrulamak zorunda kalmazsınız API sürümlerini konumlar arasında kullanılabilir olmasını sağlar. API profilinizi tarafından başvurulan API sürümleri Azure Stack ortamında mevcut olduğundan emin olmak için Azure Stack operatörlerinin desteği için ilke göre çözüm güncel tutmanız gerekir. Altı aydan daha eski bir sistemi olması halinde, uyumsuz olarak kabul edilir ve ortam güncelleştirilmelidir.

API profili şablonunda gerekli bir öğe değil. Öğe ekleseniz bile yalnızca kaynaklar için hangi Hayır kullanım biçimine `apiVersion` belirtilir. Bu öğe için kademeli değişiklikleri sağlar, ancak var olan şablonları için herhangi bir değişiklik yapılması gerekmez.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Onay uç noktası başvuruları

Kaynaklar diğer hizmetlere yönelik başvuruları platformunda olabilir. Örneğin, bir genel IP, kendisine atanmış bir genel DNS adı olabilir. Genel bulut, bağımsız bulutlarda ve Azure Stack çözümleri, kendi ayrı bir uç nokta ad alanları vardır. Çoğu durumda, bir kaynak şablonundaki giriş olarak yalnızca bir önek gerektirir. Çalışma zamanı sırasında Azure Resource Manager uç nokta değeri için ekler. Bazı uç nokta değerleri şablonda açıkça belirtilmesi gerekir. 

> [!NOTE]
> Bulut tutarlılık için şablonları geliştirmek için sabit kodlamayın uç nokta ad alanları yok.

Aşağıdaki iki örnek, bir kaynak oluşturulurken açıkça belirtilmesi gereken ortak bir uç nokta ad alanları şunlardır:

* Depolama hesapları (blob, kuyruk, tablo ve dosya)
* Veritabanları ve Azure önbelleği için Redis için bağlantı dizeleri

Dağıtım tamamlandığında uç nokta ad alanları da şablon çıktısında bilgiler kullanıcı için kullanılabilir. Genel örnekleri aşağıda verilmiştir:

* Depolama hesapları (blob, kuyruk, tablo ve dosya)
* Bağlantı dizeleri (MySql, SQLServer, SQLAzure, özel, NotificationHub, Service Bus, EventHub, ApiHub, DocDb, gerekiyor, PostgreSQL)
* Traffic Manager
* Genel bir IP adresinin Etkialanıadetiketi
* Bulut hizmetleri

Genel olarak, bir şablonu sabit kodlanmış uç noktaların kaçının. En iyi uç noktalarını dinamik olarak almak için başvuru şablon işlevi kullanmaktır. Depolama hesapları için uç nokta ad alanı sabit kodlanmış olan örnek için en yaygın olarak uç nokta. Her Depolama hesabı uç nokta ad alanıyla depolama hesabının adını birleştirerek oluşturulan benzersiz bir FQDN'ye sahip. Buluta bağlı olarak farklı FQDN'leri mystorageaccount1 sonuçlarında adlı bir blob depolama hesabı:

* **mystorageaccount1.BLOB.Core.Windows.NET** genel Azure Bulutu üzerinde oluşturduğunuzda.
* **mystorageaccount1.BLOB.Core.chinacloudapi.CN** Azure Çin oluşturduğunuzda.

Aşağıdaki başvuru şablon işlevi uç nokta ad alanı, depolama kaynak Sağlayıcısı'ndan alır:

```json
"diskUri":"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Depolama hesabı uç noktası ile kodlanmış değerini değiştirerek `reference` şablon işlevi, uç nokta başvurusu için hiçbir değişiklik yapmadan başarıyla farklı ortamlara dağıtmak için aynı şablonu kullanabilirsiniz.

### <a name="refer-to-existing-resources-by-unique-id"></a>Benzersiz Kimliğine göre mevcut kaynaklara bakın

Mevcut bir kaynak için aynı veya başka bir başvurabilir kaynak grubunu ve aynı abonelik veya aynı bulutta aynı kiracıda başka bir abonelik. Kaynak özelliklerini almak için kaynak için benzersiz tanımlayıcı kullanmanız gerekir. `resourceId` Şablon işlevi aşağıdaki kodun gösterdiği olarak SQL Server gibi bir kaynağın benzersiz kimliği alır: 

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Ardından `resourceId` içinde işlev `reference` bir veritabanının özelliklerini almak için şablon işlevi. Dönüş nesnesini içeren `fullyQualifiedDomainName` tam uç nokta değeri tutan özelliği. Bu değer, çalışma zamanında alınır ve bulut ortama özgü uç nokta ad alanı sağlar. Bağlantı dizesini runbook'a kod uç nokta ad alanı olmadan tanımlamak için gösterildiği gibi doğrudan bağlantı dizesindeki dönüş nesnesinin özelliğini başvurabilirsiniz:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Kaynak özelliklerini göz önünde bulundurun.

Azure Stack ortamlarında belirli kaynakları şablonunuza dikkate almanız gereken benzersiz özelliklere sahiptir.

### <a name="ensure-vm-images-are-available"></a>VM görüntüleri kullanılabilir olduğundan emin olun

Azure VM görüntüleri zengin bir seçim sunar. Bu görüntüler oluşturulur ve dağıtım için Microsoft ve iş ortakları tarafından hazırlandı. Görüntüleri, sanal makineler için platformunda temelini oluşturur. Ancak, bulutta tutarlı bir şablon yalnızca kullanılabilir parametrelerin başvurmanız gerekir — belirli, yayımcı, teklif ve SKU genel Azure, Azure bağımsız bulutlarda veya bir Azure Stack çözüm kullanılabilir VM görüntüleri.

Kullanılabilir VM görüntüleri bir konumda bir listesini almak için aşağıdaki Azure CLI komutunu çalıştırın:

```azurecli-interactive
az vm image list -all
```

Azure PowerShell cmdlet'i ile aynı listesini alabilirsiniz [Get-Azurermvmımagepublisher](/powershell/module/az.compute/get-azvmimagepublisher) ile istediğiniz konumu belirtin `-Location` parametresi. Örneğin:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Bu komut birkaç genel Azure bulutunun Batı Avrupa bölgesinde kullanılabilir tüm görüntüleri döndürülecek dakika sürer.

Bu sanal makine görüntüleri Azure Stack için kullanılabilir duruma, tüm kullanılabilir depolama kullanılması. Bile küçük ölçek birimi uyum sağlamak için Azure Stack, bir ortama eklemek istediğiniz görüntü seçmenizi sağlar.

Aşağıdaki kod örneği, yayımcı, teklif ve SKU, Azure Resource Manager şablonlarınızı parametrelerinde başvurmak için tutarlı bir yaklaşım gösterilmektedir:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Yerel sanal makine boyutlarını denetleme

Bulut tutarlılık için şablonunuzu geliştirmek için tüm hedef ortamlarında istediğiniz VM boyutu kullanılabilir emin olmanız gerekir. VM boyutları, performans özellikleri ve yetenekleri gruplandırmasıdır. Bazı VM boyutları, sanal Makinenin üzerinde çalıştığı donanım bağlıdır. GPU için iyileştirilmiş bir VM dağıtmak istiyorsanız, örneğin, Hiper yöneticiyi çalıştıran donanımı GPU donanım olmalıdır.

Microsoft yeni bir belirli donanım bağımlılıkları olan VM boyutunu sunar, VM boyutu genellikle ilk küçük bir alt kümesinde Azure bulut bölgelerinde kullanılabilir hale getirilir. Daha sonra bunu diğer bölgeler ve Bulutlar için kullanıma sunulmaktadır. VM boyutunu dağıttığınız her bir bulutta bulunduğundan emin olmak için kullanılabilir boyutları aşağıdaki Azure CLI komutuyla alabilirsiniz:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Azure PowerShell için şunu kullanın:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Kullanılabilir hizmetlerin tam listesi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Azure Stack'te Azure yönetilen diskler kullanımını denetleyin

Depolama diskleri tanıtıcı bir Azure kiracısı için yönetilen. Açıkça bir depolama hesabı oluşturma ve bir sanal sabit disk (VHD) için URI belirtmek yerine, bir VM'nin dağıtımın yaptığınızda örtük olarak bu eylemleri gerçekleştirmek için yönetilen diskleri kullanabilirsiniz. Yönetilen diskler Vm'lerden gelen tüm disklerin aynı kullanılabilirlik kümesindeki farklı depolama birimlerine yerleştirerek kullanılabilirliğini artırın. Ayrıca, mevcut VHD standart katmandan Premium depolamaya önemli ölçüde daha az kapalı kalma süresi ile dönüştürülebilir.

Yönetilen diskler, Azure Stack için yol haritası üzerinde olsa da şu anda desteklenmemektedir. Bunlar kabul edilene kadar bulutta tutarlı şablonları Azure Stack için VHD kullanarak açıkça belirterek geliştirebilirsiniz `vhd` gösterildiği gibi VM kaynağı şablonda öğe:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Buna karşılık, şablonda bir yönetilen disk yapılandırmasını belirtmek için kaldırmak `vhd` disk yapılandırma öğesinden.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Ayrıca aynı değişiklikleri uygulamak [veri diskleri](../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>VM uzantıları Azure Stack'te kullanılabilir olduğunu doğrulayın

Başka bir bulut tutarlılığını kullanımını husustur [sanal makine uzantıları](../virtual-machines/windows/extensions-features.md) içindeki VM kaynaklarını yapılandırmak için. Tüm VM uzantıları, Azure Stack kullanılabilir. Şablondan VM uzantısı, bağımlılıklar ve koşullar şablonu içinde oluşturmak için ayrılmış kaynaklar belirtebilirsiniz.

Örneğin, Microsoft SQL Server çalıştıran bir VM yapılandırmak istiyorsanız, VM uzantısı şablon dağıtımı bir parçası olarak SQL Server yapılandırabilirsiniz. Ne olacağını düşünün dağıtım şablonu da SQL Server çalıştıran bir VM üzerinde bir veritabanı oluşturmak için yapılandırılmış bir uygulama sunucusu varsa. VM uzantısı uygulama sunucuları için de kullanmanın yanı sıra, SQL Server VM uzantısı kaynak başarılı getirisini uygulama sunucusunun bağımlılık yapılandırabilirsiniz. Bu yaklaşım, uygulama sunucusu veritabanını oluşturmak için istendiğinde SQL Server çalıştıran sanal makine yapılandırılmış ve kullanılabilir olmasını sağlar.

Şablonun bildirim temelli bir yaklaşım, platform bağımlılıkları için gerekli mantığı ilgilenirken kaynakları ve arası bağımlılıkları son durumunu tanımlamanızı sağlar.

#### <a name="check-that-vm-extensions-are-available"></a>VM uzantıları mevcut olup olmadığını denetleyin

Türlerde VM uzantıları mevcut. Bulut tutarlılık için şablon geliştirirken, yalnızca tüm bölgelerde şablonunun hedeflediği kullanılabilir uzantıları kullanmak emin olun.

Belirli bir bölgede kullanılabilen VM uzantılarının bir listesini almak için (Bu örnekte, `myLocation`), aşağıdaki Azure CLI komutunu çalıştırın:

```azurecli-interactive
az vm extension image list --location myLocation
```

Ayrıca Azure PowerShell yürütebilir [Get-Azurermvmımagepublisher](/powershell/module/az.compute/get-azvmimagepublisher) cmdlet'ini kullanıp `-Location` sanal makine görüntüsünün konumunu belirtmek için. Örneğin:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Sürümler kullanılabilir olduğundan emin olun

VM uzantıları birinci taraf Resource Manager kaynaklarını olduğundan, kendi API sürümlerini sahiptirler. Aşağıdaki kodda gösterildiği gibi VM uzantısı türü Microsoft.Compute kaynak sağlayıcısındaki iç içe geçmiş bir kaynaktır.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

VM uzantısı kaynağı API sürümünün şablonunuzla birlikte hedef planladığınız tüm konumlarda mevcut olması gerekir. Konum bağımlılık kaynak sağlayıcısı API sürümü, kullanılabilirlik, daha önce "Tüm kaynak türleri sürümünü doğrulama" bölümünde açıklandığı gibi çalışır.

VM uzantısı kaynağın kullanılabilir API sürümlerinin listesini almak için kullanın [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) cmdlet'iyle **Microsoft.Compute** gösterildiği gibi kaynak sağlayıcısı:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Sanal makine ölçek kümeleri VM uzantılarını de kullanabilirsiniz. Aynı konum koşulları geçerlidir. Bulut tutarlılık için şablonunuzu geliştirmek için API sürümleri için dağıtmayı planladığınız tüm konumlarda kullanılabilir olduğundan emin olun. Ölçek kümeleri için VM uzantısı kaynağının API sürümlerini almak için önce olarak aynı cmdlet'i kullanın, ancak kaynak türü, gösterildiği gibi sanal makine ölçek kümeleri belirtin:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Her özel de tutulan uzantısıdır. Bu sürüm gösterilen `typeHandlerVersion` VM uzantısı özelliği. Sürüm olarak belirttiğinizden emin olun. `typeHandlerVersion` VM uzantılarının, şablonun öğe şablonu dağıtmayı planladığınız konumlarda kullanılabilir. Örneğin, aşağıdaki kod, sürüm 1.7 belirtir:

```json
{
    "name": "MyCustomScriptExtension",
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...   
```

Belirli bir VM uzantısı için kullanılabilir sürümlerin listesini almak için kullanın [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) cmdlet'i. Aşağıdaki örnek, kullanılabilir sürümler için PowerShell DSC (Desired State Configuration) sanal makine uzantısını alır. **double**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Yayımcıların listesini almak için kullanın [Get-Azurermvmımagepublisher](/powershell/module/az.compute/get-azvmimagepublisher) komutu. İstek türü için kullanın [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) commend.

## <a name="tips-for-testing-and-automation"></a>Test ve Otomasyon için ipuçları

Bu, tüm ilgili ayarları, özelliklerini ve sınırlamalarını şablon yazarken izlemek zor olur. Ortak bir yaklaşım geliştirin ve başka konumlara hedeflenen önce şablonları tek bulut karşı test sağlamaktır. Ancak, önceki test yazma işlemi, daha az sorun giderme ve Geliştirme ekibiniz yapmanız gerekecek kodu yeniden yazma içinde gerçekleştirilir. Konum bağımlılıklar nedeniyle başarısız olan dağıtımları sorunlarını gidermek zaman alabilir. İşte bu otomatik mümkün olduğunca erken geliştirme döngüsünde test etmenizi öneririz. Sonuç olarak, geliştirme süresini ve daha az kaynak gerekir ve bulut tutarlı yapıtlarınızı daha da değerli hale gelir.

Aşağıdaki resimde, bir geliştirme süreci bir tümleşik geliştirme ortamı (IDE) kullanarak bir takım için tipik bir örneği gösterilmektedir. Farklı test türleri zaman çizelgesinde farklı aşamalarında yürütülür. Burada iki geliştiricilerin aynı çözüm üzerinde çalışıyoruz, ancak bu senaryo tek bir geliştirici ya da büyük bir takım için eşit oranda geçerlidir. Her geliştirici genellikle aynı dosyaları üzerinde çalışan diğer etkilemeden yerel kopyasında çalışmak her bir etkinleştirme merkezi bir depo yerel bir kopyasını oluşturur.

![İş akışı](./media/templates-cloud-consistency/workflow.png) 

Test ve Otomasyon için aşağıdaki ipuçlarını göz önünde bulundurun:

* Olun test araçları kullanın. Örneğin, Visual Studio Code ve Visual Studio IntelliSense içerir ve şablonlarınızı yardımcı olabilecek diğer özelliklerini doğrulayın.
* Yerel IDE üzerinde geliştirme sırasında kod kalitesini artırmak için statik Kod Analizi ile birim testleri ve tümleştirme testleri gerçekleştirin. 
* Bir sorun bulunduğunda için daha da iyi bir deneyim ilk geliştirme sırasında birim testleri ve tümleştirme testleri yalnızca bildirmelisiniz ve testlerle devam edin. Bu şekilde, giderilen sorunları belirleyebilir ve teste dayalı dağıtım (TDD) da adlandırılan bu değişiklikleri sırasını öncelik verin.
* Bazı testler Azure Resource Manager'a bağlı olmadan gerçekleştirilebilir dikkat edin. Diğerleri ise, şablon dağıtımı test etme gibi Resource Manager'ın çevrimdışı gerçekleştirilemiyor belirli eylemleri gerçekleştirmek için gerektirir.
* Bir dağıtım şablonu API doğrulama karşı test etmek için gerçek bir dağıtım eşit değildir. Ayrıca, yerel bir dosyaya bir şablonu dağıtmayı bile şablondaki iç içe şablonlara yönelik tüm başvuruları doğrudan kaynak yöneticisi tarafından alınır ve VM uzantıları tarafından başvurulan yapıtları dağıtılan sanal makine içinde çalışan VM aracısı tarafından alınır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Resource Manager şablonu konuları](/azure-stack/user/azure-stack-develop-templates)
* [Azure Resource Manager şablonları için en iyi uygulamalar](resource-group-authoring-templates.md)
