---
title: Şablonları bulutlar genelinde yeniden kullanma
description: Farklı bulut ortamları için tutarlı şekilde çalışan Azure Resource Manager şablonları (ARM şablonları) geliştirin. Azure Stack için yeni bir şablon oluşturun veya var olan şablonları güncelleştirin.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 120e98ebebec1436c9171f6076baccd35be9b843
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104888830"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Bulut tutarlılığı için ARM şablonları geliştirme

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Azure 'un önemli bir avantajı tutarlılığa sahiptir. Bir konum için geliştirme yatırımları başka bir konuma yeniden kullanılabilir. Azure Resource Manager şablonu (ARM şablonu), dağıtımlarınızın Genel Azure, Azure sogeign bulutları ve Azure Stack dahil olmak üzere ortamlar arasında tutarlı ve yinelenebilir olmasını sağlar. Ancak, şablonları bulutlarda yeniden kullanmak için bu kılavuzda açıklandığı şekilde buluta özgü bağımlılıkları göz önünde bulundurmanız gerekir.

Microsoft birçok konumda akıllı ve kurumsal özellikli bulut hizmetleri sunar:

* Dünya çapındaki bölgelerde Microsoft tarafından yönetilen veri merkezlerinden oluşan büyüyen bir ağ tarafından desteklenen küresel Azure platformu.
* Azure Almanya, Azure Kamu ve Azure Çin 21Vianet gibi yalıtılmış sogeign bulutları. Sovereign bulutları, küresel Azure müşterilerinin erişimi olan harika özelliklerin birçoğunu içeren tutarlı bir platform sağlar.
* Kuruluşunuzun veri merkezinden Azure hizmetleri sunmanıza olanak sağlayan karma bulut platformu Azure Stack. Kuruluşlar kendi veri merkezlerinde Azure Stack ayarlayabilir veya hizmet sağlayıcılarından Azure hizmetlerini tüketebilir (bazen barındırılan bölgeler olarak da bilinir) Azure Stack çalışır.

Tüm bu bulutların çekirdeğine Azure Resource Manager, Azure platformuyla çok çeşitli kullanıcı arabirimlerinin iletişim kurmasına izin veren bir API sağlar. Bu API, size güçlü kod olarak altyapı özellikleri sağlar. Azure bulut platformunda kullanılabilen her türlü kaynak türü, Azure Resource Manager ile dağıtılabilir ve yapılandırılabilir. Tek bir şablon ile, tüm uygulamanızı bir işlemsel bitiş durumuna dağıtabilir ve yapılandırabilirsiniz.

![Azure ortamları](./media/templates-cloud-consistency/environments.png)

Küresel Azure, bağımsız bulutlar, barındırılan bulutlar ve veri merkezinizdeki bir bulut tutarlılığı, Azure Resource Manager avantajlarından faydalanmanıza yardımcı olur. Şablon tabanlı kaynak dağıtımı ve yapılandırmasını ayarlarken bu bulutların geliştirme yatırımlarınızı yeniden kullanabilirsiniz.

Ancak, küresel, sogeign, barındırılan ve Hibrit bulutları tutarlı hizmetler sağlar, ancak tüm bulutlar aynı değildir. Sonuç olarak, yalnızca belirli bir bulutta bulunan özellikler üzerinde bağımlılıklara sahip bir şablon oluşturabilirsiniz.

Bu kılavuzun geri kalanında Azure Stack için var olan ARM şablonlarının yeni veya güncelleştirilmesini planlarken dikkate alınması gereken alan açıklanmaktadır. Genel olarak, denetim listesi aşağıdaki öğeleri içermelidir:

* Şablonunuzda işlevlerin, uç noktaların, hizmetlerin ve diğer kaynakların hedef dağıtım konumlarında kullanılabilir olduğunu doğrulayın.
* Bulut genelinde erişim sağlayan, iç içe şablonları ve yapılandırma yapılarını erişilebilir konumlarda depolayın.
* Sabit kodlama bağlantıları ve öğeleri yerine dinamik başvurular kullanın.
* Kullandığınız şablon parametrelerinin hedef bulutlarda çalışır durumda olduğundan emin olun.
* Kaynağa özgü özelliklerin hedef bulutların kullanılabilir olduğunu doğrulayın.

ARM şablonlarına giriş için bkz. [şablon dağıtımı](overview.md).

## <a name="ensure-template-functions-work"></a>Şablon işlevlerinin çalıştığından emin olun

ARM şablonunun temel sözdizimi JSON ' dur. Şablonlar bir JSON üst kümesini kullanır ve sözdizimini ifadeler ve işlevlerle genişlettikten sonra. Şablon dil işlemcisi, ek şablon işlevlerini desteklemek için sık sık güncelleştirilir. Kullanılabilir şablon işlevlerinin ayrıntılı bir açıklaması için bkz. [ARM şablon işlevleri](template-functions.md).

Azure Resource Manager tanıtılan yeni şablon işlevleri, sogeign bulutlarında veya Azure Stack hemen kullanılamaz. Bir şablonu başarıyla dağıtmak için, şablonda başvurulan tüm işlevler hedef bulutta kullanılabilir olmalıdır.

Azure Resource Manager özellikleri her zaman genel Azure 'a sunulacaktır. Yeni sunulan şablon işlevlerinin Azure Stack de kullanılabilir olup olmadığını doğrulamak için aşağıdaki PowerShell betiğini kullanabilirsiniz:

1. GitHub deposunun bir kopyasını oluşturun: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) .

1. Deponun yerel bir kopyasını aldıktan sonra, PowerShell ile hedefin Azure Resource Manager bağlayın.

1. Psm1 modülünü içeri aktarın ve Test-AzureRmTemplateFunctions cmdlet 'ini yürütün:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Betiği, her biri yalnızca benzersiz şablon işlevleri içeren birden çok ve küçültülmüş şablonlar dağıtır. Betiğin çıkışı desteklenen ve kullanılamayan şablon işlevlerini raporlar.

## <a name="working-with-linked-artifacts"></a>Bağlantılı yapıtlar ile çalışma

Şablon, bağlantılı yapıtlara başvurular içerebilir ve başka bir şablona bağlanan bir dağıtım kaynağı içerebilir. Bağlantılı şablonlar (iç içe geçmiş şablon olarak da bilinir) çalışma zamanında Kaynak Yöneticisi tarafından alınır. Şablon, sanal makine (VM) uzantıları için yapılara başvurular da içerebilir. Bu yapıtlar, şablon dağıtımı sırasında VM uzantısının yapılandırması için VM 'nin içinde çalışan VM uzantısı tarafından alınır.

Aşağıdaki bölümlerde, ana dağıtım şablonunun dışındaki yapıtları içeren şablonlar geliştirirken bulut tutarlılığı hakkında dikkat edilecek noktalar açıklanır.

### <a name="use-nested-templates-across-regions"></a>Bölgeler arasında iç içe Şablonlar kullanma

Şablonlar, her biri belirli bir amaca sahip olan ve dağıtım senaryolarında yeniden kullanılabilen küçük, yeniden kullanılabilir şablonlar halinde parçalanmış olabilir. Bir dağıtımı yürütmek için, ana veya ana şablon olarak bilinen tek bir şablon belirtirsiniz. Sanal ağlar, VM 'Ler ve Web uygulamaları gibi dağıtılacak kaynakları belirtir. Ana şablon, başka bir şablon için bir bağlantı da içerebilir, böylece şablonları iç içe geçirebilirsiniz. Benzer şekilde, iç içe yerleştirilmiş bir şablon, diğer şablonların bağlantılarını içerebilir. En fazla beş düzey derinlikte oluşturabilirsiniz.

Aşağıdaki kod, templateLink parametresinin iç içe geçmiş bir şablona nasıl başvurduğunu gösterir:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2020-10-01",
     "name": "linkedTemplate",
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

Azure Resource Manager, ana şablonu çalışma zamanında değerlendirir ve iç içe geçmiş her şablonu alır ve değerlendirir. Tüm iç içe şablonlar alındıktan sonra, şablon düzleştirilir ve daha fazla işleme başlatılır.

### <a name="make-linked-templates-accessible-across-clouds"></a>Bağlı şablonları bulutlar genelinde erişilebilir hale getirme

Kullandığınız herhangi bir bağlı şablonu nerede ve nasıl depolayacağınızı göz önünde bulundurun. Çalışma zamanında, Azure Resource Manager getirme ve bu nedenle tüm bağlantılı şablonlar için doğrudan erişim gerektirir. Yaygın bir uygulama, iç içe şablonları depolamak için GitHub kullanmaktır. GitHub deposu, bir URL aracılığıyla herkese açık olan dosyalar içerebilir. Bu teknik genel bulut ve bağımsız bulutlarda iyi çalışabilse de bir Azure Stack ortamı, giden Internet erişimi olmadan bir şirket ağında veya bağlantısı kesilen uzak bir konumda bulunabilir. Bu gibi durumlarda, iç içe şablonları almak Azure Resource Manager başarısız olur.

Platformlar arası dağıtımlar için daha iyi bir yöntem, bağlı şablonlarınızı hedef bulut için erişilebilen bir konumda depolemelidir. İdeal olarak tüm dağıtım yapıtları, sürekli tümleştirme/sürekli geliştirme (CI/CD) ardışık düzeninde saklanır ve dağıtılır. Alternatif olarak, iç içe şablonları Azure Resource Manager bir BLOB depolama kapsayıcısında depolayabilirler.

Her buluttaki blob depolaması farklı bir uç nokta tam etki alanı adı (FQDN) kullandığından, şablonu bağlantılı şablonların konumuyla birlikte iki parametre ile yapılandırın. Parametreler, dağıtım zamanında Kullanıcı girişini kabul edebilir. Şablonlar genellikle birden çok kişi tarafından yazılır ve paylaşılır, bu nedenle en iyi yöntem bu parametreler için standart bir ad kullanmaktır. Adlandırma kuralları, şablonların bölgeler, bulutlar ve yazarlar arasında daha yeniden kullanılabilir olmasını sağlamaya yardımcı olur.

Aşağıdaki kodda, `_artifactsLocation` dağıtımla ilgili tüm yapıtları içeren tek bir konuma işaret etmek için kullanılır. Varsayılan bir değer sağlandığını unutmayın. Dağıtım zamanında, için bir giriş değeri belirtilmemişse `_artifactsLocation` , varsayılan değer kullanılır. , `_artifactsLocationSasToken` İçin giriş olarak kullanılır `sasToken` . Varsayılan değer, güvenli olmadığı senaryolar için bir boş dize olmalıdır `_artifactsLocation` ; Örneğin, genel bir GitHub deposu.

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

Şablon boyunca bağlantılar, temel URI 'yi ( `_artifactsLocation` parametresinden) yapıt göreli yolu ve ile birleştirerek oluşturulur `_artifactsLocationSasToken` . Aşağıdaki kod, URI şablonu işlevi kullanılarak iç içe şablon bağlantısının nasıl ekleneceğini gösterir:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "shared",
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

Bu yaklaşımı kullanarak, parametresi için varsayılan değer `_artifactsLocation` kullanılır. Bağlantılı şablonların farklı bir konumdan alınması gerekiyorsa, parametre girişi dağıtım zamanında varsayılan değeri geçersiz kılmak için kullanılabilir — şablonda değişiklik yapılması gerekmez.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>_ArtifactsLocation kullan bağlantı yerine

İç içe geçmiş şablonlar için kullanılmanın yanı sıra, `_artifactsLocation` PARAMETRESINDEKI URL bir dağıtım şablonunun ilgili tüm yapıtları için temel olarak kullanılır. Bazı VM uzantıları, şablon dışında depolanan bir betiğin bağlantısını içerir. Bu uzantılar için bağlantıları hiçbir şekilde kodkullanmamalısınız. Örneğin, özel betik ve PowerShell DSC uzantıları, aşağıda gösterildiği gibi, GitHub 'daki bir dış komut dosyasına bağlantı verebilir:

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

Komut dosyasının bağlantılarını sorunsuz bir şekilde kodlamak, şablonun başka bir konuma başarıyla dağıtılmasını önler. VM kaynağının yapılandırması sırasında, VM içinde çalışan VM Aracısı, VM uzantısına bağlı tüm betiklerin indirilmesini başlatır ve ardından betikleri VM 'nin yerel diskine depolar. Bu yaklaşım, "iç içe yerleştirilmiş şablonları bölgeler arasında kullanma" bölümünde açıklanan iç içe geçmiş şablon bağlantıları gibi çalışır.

Kaynak Yöneticisi, çalışma zamanında iç içe geçmiş şablonlar alır. VM uzantıları için, herhangi bir dış yapıtların alınması VM Aracısı tarafından gerçekleştirilir. Yapıt alımı farklı başlatıcısının yanı sıra, şablon tanımındaki çözüm de aynıdır. _ArtifactsLocation parametresini, tüm yapıtların depolandığı temel yolun varsayılan değeri ile (VM Uzantısı betikleri dahil) ve `_artifactsLocationSasToken` sasToken girişi için parametresiyle kullanın.

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

Bir yapıtın mutlak URI 'sini oluşturmak için tercih edilen yöntem, Concat şablon işlevi yerine URI şablonu işlevini kullanmaktır. Sabit kodlanmış bağlantıları, URI şablonu işleviyle VM uzantısındaki betiklerle değiştirerek, şablondaki bu işlev bulut tutarlılığı için yapılandırılır.

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

Bu yaklaşımda, yapılandırma betikleri dahil olmak üzere tüm dağıtım yapıtları, şablonun kendisi ile aynı konumda depolanabilir. Tüm bağlantıların konumunu değiştirmek için, yalnızca _Artifactslocation parametreleri_ için farklı bır temel URL belirtmeniz gerekir.

## <a name="factor-in-differing-regional-capabilities"></a>Farklı bölgesel özelliklerde faktör

Çevik geliştirme ve sürekli güncelleştirmeler ve Azure 'a sunulan yeni hizmetler ile bölgeler, hizmet veya güncelleştirme kullanılabilirliğine [göre farklılık gösterebilir](https://azure.microsoft.com/regions/services/) . Kapsamlı iç test sonrasında, yeni hizmetler veya mevcut hizmetlere yapılan güncelleştirmeler genellikle bir doğrulama programına katılan küçük bir müşteri kitlelerine eklenmiştir. Müşteri doğrulamasından başarıyla başlandıktan sonra, hizmetler veya güncelleştirmeler Azure bölgelerinin bir alt kümesi içinde kullanıma sunulmuştur, daha sonra sogeign bulutlarında kullanıma sunulmuştur ve potansiyel olarak Azure Stack müşterileri için kullanılabilir hale getirilir.

Azure bölgelerinin ve bulutların kullanılabilir hizmetlerinde farklı olabileceğini bilmenin, şablonlarınız hakkında bazı öngörülü kararlar verebilirsiniz. Bir bulut için kullanılabilir kaynak sağlayıcılarını inceleyerek başlamak iyi bir yerdir. Bir kaynak sağlayıcısı, bir Azure hizmeti için kullanılabilen kaynak ve işlemler kümesini size bildirir.

Şablon, kaynakları dağıtır ve yapılandırır. Kaynak türü bir kaynak sağlayıcısı tarafından sağlanır. Örneğin, işlem kaynak sağlayıcısı (Microsoft. COMPUTE) virtualMachines ve kullanılabilirlik kümeleri gibi birden çok kaynak türü sağlar. Her kaynak sağlayıcı, tüm kaynak sağlayıcıları genelinde tutarlı ve birleştirilmiş bir yazma deneyimi sağlayarak ortak bir sözleşme tarafından tanımlanan Azure Resource Manager bir API sağlar. Ancak, küresel Azure 'da bulunan bir kaynak sağlayıcısı, bir bağımsız bulutu veya bir Azure Stack bölgesinde bulunmayabilir.

![Kaynak sağlayıcıları](./media/templates-cloud-consistency/resource-providers.png)

Belirli bir bulutta bulunan kaynak sağlayıcılarını doğrulamak için, Azure komut satırı arabiriminde ([CLI](/cli/azure/install-azure-cli)) aşağıdaki betiği çalıştırın:

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Kullanılabilir kaynak sağlayıcılarını görmek için aşağıdaki PowerShell cmdlet 'ini de kullanabilirsiniz:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Tüm kaynak türlerinin sürümünü doğrulama

Bir dizi özellik tüm kaynak türleri için ortaktır, ancak her kaynak kendi kendine özgü özelliklerine de sahiptir. Yeni özellikler ve ilgili özellikler, yeni bir API sürümü aracılığıyla mevcut kaynak türlerine zaman eklenir. Şablondaki bir kaynağın kendi API sürümü özelliği vardır `apiVersion` . Bu sürüm oluşturma, bir şablondaki mevcut bir kaynak yapılandırmasının, platformda yapılan değişikliklerden etkilenmemesini sağlar.

Global Azure 'daki mevcut kaynak türlerine sunulan yeni API sürümleri, tüm bölgelerde, sogeign bulutlarında veya Azure Stack hemen kullanılamayabilir. Bir bulut için kullanılabilir kaynak sağlayıcılarının, kaynak türlerinin ve API sürümlerinin bir listesini görüntülemek için Azure portal Kaynak Gezgini kullanabilirsiniz. Tüm hizmetler menüsünde Kaynak Gezgini araması yapın. Tüm kullanılabilir kaynak sağlayıcılarını, kaynak türlerini ve bu buluttaki API sürümlerini döndürmek için Kaynak Gezgini sağlayıcılar düğümünü genişletin.

Azure CLı 'de belirli bir buluttaki tüm kaynak türleri için kullanılabilir API sürümünü listelemek için aşağıdaki betiği çalıştırın:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Aşağıdaki PowerShell cmdlet 'ini de kullanabilirsiniz:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Kaynak konumlarına bir parametre ile bakın

Bir şablon her zaman bir bölgede bulunan bir kaynak grubuna dağıtılır. Dağıtımın kendisinin yanı sıra, bir şablondaki her kaynak de içinde dağıtılacak bölgeyi belirtmek için kullandığınız bir location özelliğine sahiptir. Şablonunuzun bulut tutarlılığı için geliştirilmesi için, kaynak konumlarına başvurmak üzere dinamik bir yönteme ihtiyacınız vardır çünkü her Azure Stack benzersiz konum adları içerebilir. Genellikle kaynakları kaynak grubuyla aynı bölgeye dağıtılır, ancak siteler arası uygulama kullanılabilirliği gibi senaryoları desteklemek için, kaynakları bölgeler arasında yaymak yararlı olabilir.

Kaynak özelliklerini bir şablonda belirtirken bölge adlarını edebilseniz bile, bu yaklaşım, büyük olasılıkla bölge adı mevcut olmadığından, şablonun diğer Azure Stack ortamlarına dağıtılabildiğinden emin olmaz.

Farklı bölgelere uyum sağlamak için, varsayılan değer ile şablona bir giriş parametresi konumu ekleyin. Dağıtım sırasında hiçbir değer belirtilmemişse varsayılan değer kullanılacaktır.

Şablon işlevi `[resourceGroup()]` aşağıdaki anahtar/değer çiftlerini içeren bir nesne döndürür:

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

Giriş parametresinin defaultValue ' ı içindeki nesnesinin konum anahtarına başvurarak, Azure Resource Manager çalışma zamanında, `[resourceGroup().location]` şablon işlevini şablonun dağıtıldığı kaynak grubu konumunun adıyla değiştirin.

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
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Bu şablon işleviyle, daha önce bölge adlarını bilmeden bile şablonunuzu herhangi bir buluta dağıtabilirsiniz. Ayrıca, şablondaki belirli bir kaynak için bir konum, kaynak grubu konumundan farklı olabilir. Bu durumda, söz konusu kaynak için ek giriş parametrelerini kullanarak yapılandırabilirsiniz, ancak aynı şablondaki diğer kaynaklar ilk konum giriş parametresini kullanmaya devam eder.

### <a name="track-versions-using-api-profiles"></a>API profillerini kullanarak sürümleri izleme

Azure Stack mevcut olan tüm kaynak sağlayıcılarını ve ilgili API sürümlerini izlemek çok zor olabilir. Örneğin, yazma sırasında, Azure 'daki **Microsoft. COMPUTE/kullanılabilirliği Bilitysets** için en son API sürümü, Azure 'da ortak olan `2018-04-01` kullanılabilir apı sürümü ve Azure Stack olur `2016-03-30` . Tüm Azure ve Azure Stack konumları arasında paylaşılan **Microsoft. Storage/storageAccounts** IÇIN ortak API sürümü, `2016-01-01` Azure 'DAKI en son API sürümü olduğu sürece `2018-02-01` .

Bu nedenle Kaynak Yöneticisi, API profillerinin, şablonlara kavram kavramını sunmuştur. API profilleri olmadan, şablondaki her kaynak `apiVersion` söz konusu kaynak IÇIN API sürümünü açıklayan bir öğe ile yapılandırılır.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
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

Bir API profili sürümü, Azure ve Azure Stack ortak kaynak türüne göre tek bir API sürümü için bir diğer ad görevi görür. Şablondaki her kaynak için bir API sürümü belirtmek yerine yalnızca API profili sürümünü adlı yeni bir kök öğede belirtirsiniz `apiProfile` ve `apiVersion` tek tek kaynaklar için öğeyi atlayabilirsiniz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
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

API profili, API sürümlerinin konumlar arasında kullanılabilmesini sağlar, bu nedenle belirli bir konumda bulunan apiVersions 'leri el ile doğrulamanız gerekmez. API profiliniz tarafından başvurulan API sürümlerinin Azure Stack bir ortamda mevcut olduğundan emin olmak için, Azure Stack işleçleri, destek ilkesini temel alarak çözümü güncel tutmalıdır. Bir sistem altı aydan uzun bir süre güncel değilse, uyumsuz olarak kabul edilir ve ortam güncelleştirilmeleri gerekir.

API profili bir şablonda gerekli bir öğe değil. Öğesi ekseniz bile, bu yalnızca No belirtilmemiş kaynaklar için kullanılacaktır `apiVersion` . Bu öğe, aşamalı değişikliklere izin verir ancak mevcut şablonlarda herhangi bir değişiklik yapılmasını gerektirmez.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
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

## <a name="check-endpoint-references"></a>Uç nokta başvurularını denetle

Kaynaklar, platformda diğer hizmetlere başvuru içerebilir. Örneğin, genel IP 'ye atanmış ortak bir DNS adı olabilir. Genel bulut, bağımsız bulutlar ve Azure Stack çözümlerinin kendi ayrı uç nokta ad alanları vardır. Çoğu durumda, bir kaynak şablonda girdi olarak yalnızca bir ön ek gerektirir. Çalışma zamanı sırasında Azure Resource Manager uç noktası değerini ekler. Bazı uç nokta değerlerinin şablonda açıkça belirtilmesi gerekir.

> [!NOTE]
> Bulut tutarlılığı için şablon geliştirmek üzere, uç nokta ad alanlarını sabit kullanmayın.

Aşağıdaki iki örnek, bir kaynak oluştururken açıkça belirtilmesi gereken ortak uç nokta ad uzaylardır:

* Depolama hesapları (blob, kuyruk, tablo ve dosya)
* Veritabanları için bağlantı dizeleri ve Redsıs için Azure önbelleği

Uç nokta ad alanları, dağıtım tamamlandığında Kullanıcı için bilgi olarak bir şablonun çıkışında de kullanılabilir. Yaygın örneklerde aşağıdakiler verilmiştir:

* Depolama hesapları (blob, kuyruk, tablo ve dosya)
* Bağlantı dizeleri (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* Genel IP adresinin domainNameLabel 'ı
* Bulut hizmetleri

Genel olarak, bir şablonda sabit kodlanmış uç noktalar kullanmaktan kaçının. En iyi yöntem, uç noktaları dinamik olarak almak için başvuru şablonu işlevini kullanmaktır. Örneğin, en yaygın olarak kodlanmış uç nokta, depolama hesapları için uç nokta ad alanıdır. Her depolama hesabının, uç nokta ad alanı ile depolama hesabının adını birleştirerek oluşturulan benzersiz bir FQDN vardır. Mystorageaccount1 adlı bir BLOB depolama hesabı, buluta bağlı olarak farklı FQDN 'Ler ile sonuçlanır:

* `mystorageaccount1.blob.core.windows.net` Genel Azure bulutu 'nda oluşturulduğunda.
* `mystorageaccount1.blob.core.chinacloudapi.cn` Azure Çin 21Vianet bulutu 'nda oluşturulduğunda.

Aşağıdaki başvuru şablonu işlevi, depolama kaynak sağlayıcısından Endpoint ad alanını alır:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Depolama hesabı uç noktasının sabit kodlanmış değerini `reference` şablon işleviyle değiştirerek, uç nokta başvurusunda değişiklik yapmadan farklı ortamlara başarıyla dağıtmak için aynı şablonu kullanabilirsiniz.

### <a name="refer-to-existing-resources-by-unique-id"></a>Benzersiz KIMLIĞE göre mevcut kaynaklara bakın

Aynı bulutta aynı kiracı dahilinde aynı veya başka bir kaynak grubundaki aynı veya başka bir abonelik içinde de var olan bir kaynağa de başvurabilirsiniz. Kaynak özelliklerini almak için kaynağın kendisi için benzersiz tanımlayıcıyı kullanmanız gerekir. `resourceId`Şablon işlevi, aşağıdaki kodun gösterdiği gibi SQL Server gibi bir kaynağın BENZERSIZ kimliğini alır:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Daha sonra `resourceId` `reference` bir veritabanının özelliklerini almak için şablon işlevinin içindeki işlevi kullanabilirsiniz. Return nesnesi `fullyQualifiedDomainName` tam bitiş noktası değerini tutan özelliği içerir. Bu değer çalışma zamanında alınır ve bulut ortamına özgü uç nokta ad alanını sağlar. Uç nokta ad alanını kodlamadan bağlantı dizesini tanımlamak için, aşağıda gösterildiği gibi, dönüş nesnesinin özelliğine doğrudan bağlantı dizesinde başvurabilirsiniz:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Kaynak özelliklerini göz önünde bulundurun

Azure Stack ortamları içindeki belirli kaynaklar, şablonunuzda dikkate almanız gereken benzersiz özelliklere sahiptir.

### <a name="ensure-vm-images-are-available"></a>VM görüntülerinin kullanılabilir olduğundan emin olun

Azure, sanal makine görüntülerinin zengin bir seçimini sağlar. Bu görüntüler, Microsoft ve iş ortakları tarafından dağıtım için oluşturulur ve hazırlanmaktadır. Görüntüler, platformda VM 'Lerin temelini oluşturur. Ancak, bulut ile tutarlı bir şablon, yalnızca genel Azure, Azure sogeign bulutları veya bir Azure Stack çözümü için kullanılabilen VM görüntülerinin yayımcısı, teklifi ve SKU 'SU için kullanılabilir parametrelere başvurmalıdır.

Bir konumdaki kullanılabilir VM görüntülerinin listesini almak için şu Azure CLı komutunu çalıştırın:

```azurecli-interactive
az vm image list -all
```

[Get-Azurermvmımagepublisher](/powershell/module/az.compute/get-azvmimagepublisher) Azure PowerShell cmdlet 'i ile aynı listeyi alabilir ve parametresiyle istediğiniz konumu belirtebilirsiniz `-Location` . Örnek:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Bu komut, genel Azure bulutunun Batı Avrupa bölgesindeki tüm kullanılabilir görüntüleri döndürmek için birkaç dakika sürer.

Azure Stack için kullanılabilir olan bu VM görüntülerini yaptıysanız, kullanılabilir tüm depolama alanı tüketilecektir. En küçük ölçek birimine eşit uyum sağlamak için, Azure Stack bir ortama eklemek istediğiniz görüntüleri seçmenize olanak sağlar.

Aşağıdaki kod örneğinde, ARM şablonlarınızın Yayımcı, teklif ve SKU parametrelerine başvurmak için tutarlı bir yaklaşım gösterilmektedir:

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

### <a name="check-local-vm-sizes"></a>Yerel VM boyutlarını denetle

Şablonunuzu bulut tutarlılığı için geliştirmek üzere, istediğiniz VM boyutunun tüm hedef ortamlarda kullanılabilir olduğundan emin olmanız gerekir. VM boyutları, performans özellikleri ve özelliklerinin gruplanmasıdır. Bazı VM boyutları, VM 'nin üzerinde çalıştığı donanıma bağlıdır. Örneğin, GPU ile iyileştirilmiş bir VM dağıtmak istiyorsanız, hiper yöneticiyi çalıştıran donanımın donanım GPU 'ları olması gerekir.

Microsoft, belirli donanım bağımlılıkları olan yeni bir sanal makine boyutu sunmakta olduğunda, genellikle Azure bulutu 'ndaki bir bölgenin küçük bir alt kümesinde VM boyutu genellikle kullanılabilir hale getirilir. Daha sonra, diğer bölgeler ve bulutlar tarafından kullanılabilir hale getirilir. Sanal makine boyutunun, dağıttığınız her bulutta mevcut olduğundan emin olmak için, kullanılabilir boyutları aşağıdaki Azure CLı komutuyla alabilirsiniz:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Azure PowerShell için şunu kullanın:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Kullanılabilir hizmetlerin tam listesi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Azure Stack 'de Azure yönetilen disklerinin kullanımını denetleyin

Yönetilen diskler, bir Azure kiracısı için depolamayı işler. Bir depolama hesabı açıkça oluşturmak ve bir sanal sabit disk (VHD) için URI 'yi belirtmek yerine, bir VM dağıtırken bu işlemleri örtük olarak gerçekleştirmek için yönetilen diskleri kullanabilirsiniz. Yönetilen diskler, aynı Kullanılabilirlik kümesindeki VM 'lerden tüm diskleri farklı depolama birimlerine yerleştirerek kullanılabilirliği artırır. Ayrıca, var olan VHD 'ler, çok daha az kapalı kalma süresine sahip standart 'ten Premium depolamaya dönüştürülebilir.

Yönetilen diskler Azure Stack yol haritasında olsa da, bunlar şu anda desteklenmemektedir. Olana kadar, `vhd` sanal makine kaynağı için şablondaki öğesini kullanarak VHD 'leri açıkça belirterek Azure Stack için buluta tutarlı şablonlar geliştirebilirsiniz, burada gösterildiği gibi:

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

Buna karşılık, bir şablonda yönetilen disk yapılandırması belirtmek için, `vhd` öğesini disk yapılandırmasından kaldırın.

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

Aynı değişiklikler [veri disklerini](../../virtual-machines/using-managed-disks-template-deployments.md)de uygular.

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Azure Stack ' de VM uzantılarının kullanılabildiğini doğrulama

Bulut tutarlılığı için başka bir değerlendirme, [sanal makine uzantılarının](../../virtual-machines/extensions/features-windows.md) bir VM içindeki kaynakları yapılandırmak için kullanılması. Azure Stack ' de tüm VM uzantıları kullanılamaz. Şablon, VM uzantısına adanmış kaynakları belirtebilir, şablon içinde bağımlılıklar ve koşullar oluşturabilir.

Örneğin, Microsoft SQL Server çalıştıran bir VM yapılandırmak istiyorsanız, VM uzantısı, şablon dağıtımı kapsamında SQL Server yapılandırabilir. Dağıtım şablonu, SQL Server çalıştıran VM üzerinde veritabanı oluşturmak üzere yapılandırılmış bir uygulama sunucusu içeriyorsa ne olacağını düşünün. Ayrıca, uygulama sunucuları için bir VM Uzantısı kullanmanın yanı sıra, SQL Server VM Uzantısı kaynağının başarıyla geri dönmesi üzerinde uygulama sunucusunun bağımlılığını yapılandırabilirsiniz. Bu yaklaşım, uygulama sunucusu veritabanını oluştururken SQL Server çalıştıran VM 'nin yapılandırıldığından ve kullanılabilir olmasını sağlar.

Şablonun bildirim temelli yaklaşımı, kaynakların ve bağımlılıklarındaki bağımlılıklar için gereken mantığın son durumunu tanımlamanızı sağlar.

#### <a name="check-that-vm-extensions-are-available"></a>VM uzantılarının kullanılabilir olup olmadığını denetleyin

Birçok VM uzantısı türü vardır. Bulut tutarlılığı için şablon geliştirirken, yalnızca şablonun hedeflediği tüm bölgelerde bulunan uzantıları kullandığınızdan emin olun.

Belirli bir bölge için kullanılabilen VM uzantılarının listesini almak için (Bu örnekte, `myLocation` ) aşağıdaki Azure CLI komutunu çalıştırın:

```azurecli-interactive
az vm extension image list --location myLocation
```

Ayrıca Azure PowerShell, [Get-Azurermvmımagepublisher](/powershell/module/az.compute/get-azvmimagepublisher) cmdlet 'ini yürütebilir ve `-Location` sanal makine görüntüsünün konumunu belirtmek için öğesini kullanabilirsiniz. Örnek:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Sürümlerin kullanılabilir olduğundan emin olun

VM uzantıları birinci taraf Kaynak Yöneticisi kaynaklar olduğundan, kendi API sürümleri vardır. Aşağıdaki kodda gösterildiği gibi, VM uzantı türü Microsoft. COMPUTE kaynak sağlayıcısında iç içe bir kaynaktır.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

Şablonunuzda hedeflemek istediğiniz tüm konumlarda VM Uzantısı kaynağının API sürümünün mevcut olması gerekir. Konum bağımlılığı, daha önce "tüm kaynak türlerinin sürümünü doğrulama" bölümünde ele alınan kaynak sağlayıcısı API sürümü kullanılabilirliği gibi çalışmaktadır.

VM Uzantısı kaynağı için kullanılabilir API sürümlerinin bir listesini almak için, şu şekilde gösterildiği gibi [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) cmdlet 'ini **Microsoft. COMPUTE** kaynak sağlayıcısıyla birlikte kullanın:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Sanal makine ölçek kümelerinde da VM uzantıları 'nı kullanabilirsiniz. Aynı konum koşulları geçerlidir. Şablonunuzu bulut tutarlılığı için geliştirmek üzere API sürümlerinin, dağıtım üzerinde planladığınız tüm konumlarda kullanılabilir olduğundan emin olun. Ölçek Kümeleri için VM Uzantısı kaynağının API sürümlerini almak için, önceki ile aynı cmdlet 'i kullanın, ancak gösterilen sanal makine ölçek kümeleri kaynak türünü belirtin:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Her belirli uzantı da sürümlüdür. Bu sürüm, `typeHandlerVersion` VM uzantısının özelliğinde gösterilir. `typeHandlerVersion`ŞABLONUN VM uzantılarının öğesinde belirtilen sürümün, şablonu dağıtmayı planladığınız konumlarda bulunduğundan emin olun. Örneğin, aşağıdaki kod sürüm 1,7 ' i belirtir:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
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

Belirli bir VM uzantısı için kullanılabilir sürümlerin bir listesini almak için [Get-Azurermvmextensionımage](/powershell/module/az.compute/get-azvmextensionimage) cmdlet 'ini kullanın. Aşağıdaki örnek, PowerShell DSC (Istenen durum yapılandırması) VM uzantısının mevcut sürümlerini **MyLocation**'dan alır:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Yayımcıların bir listesini almak için [Get-Azurermvmımagepublisher](/powershell/module/az.compute/get-azvmimagepublisher) komutunu kullanın. Tür istemek için [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) yorumd ' nı kullanın.

## <a name="tips-for-testing-and-automation"></a>Test ve otomasyon ipuçları

Bir şablon yazarken ilgili tüm ayarları, özellikleri ve sınırlamaları izlemek bir zorluk sergilemektir. Yaygın yaklaşım, diğer konumların hedeflenmeden önce tek bir buluta yönelik şablonlar geliştirmektir ve test edilir. Ancak, daha önce testler yazma sürecinde gerçekleştirildiğinde, Geliştirme ekibinizin daha az sorun giderme ve kod yeniden yazma işlemleri yapması gerekecektir. Konum bağımlılıkları nedeniyle başarısız olan dağıtımlar, sorun giderme için zaman alabilir. İşte bu nedenle, yazma döngüsünün mümkün olduğunca erken bir şekilde otomatikleştirilmiş test öneririz. Son olarak, daha az geliştirme süresi ve daha az kaynak olması gerekir ve buluta tutarlı yapılarınız daha değerli hale gelir.

Aşağıdaki görüntüde, tümleşik geliştirme ortamı (IDE) kullanan bir takım için geliştirme sürecinin tipik bir örneği gösterilmektedir. Zaman çizelgesindeki farklı aşamalarda farklı test türleri yürütülür. Burada iki geliştirici aynı çözümde çalışmaktadır, ancak bu senaryo, tek bir geliştiriciye veya büyük bir takıma eşit olarak uygulanır. Her geliştirici genellikle merkezi bir deponun yerel bir kopyasını oluşturur ve her birinin aynı dosyalar üzerinde çalışmakta olabilecek diğerlerini etkilemeden yerel kopyada çalışmasını sağlar.

![Diyagram, yerel ı D 'de paralel olarak iki birim testi ve tümleştirme testi kümesini gösterir. Bu, C g/C D geliştirme akışındaki birim testlerine, sonra tümleştirme testlerine ve test dağıtımına, ardından dağıtıma.](./media/templates-cloud-consistency/workflow.png)

Test ve otomasyon için aşağıdaki ipuçlarını göz önünde bulundurun:

* Test araçlarını kullanın. Örneğin, Visual Studio Code ve Visual Studio, şablonlarınızı doğrulamanıza yardımcı olabilecek IntelliSense ve diğer özellikleri içerir.
* Yerel IDE 'de geliştirme sırasında kod kalitesini geliştirmek için birim testleri ve tümleştirme testleri ile statik kod analizi gerçekleştirin.
* İlk geliştirme sırasında daha iyi bir deneyim için, birim testlerinin ve tümleştirme testlerinin yalnızca bir sorun bulunduğunda uyarması ve testlere devam etmesi gerekir. Bu şekilde, ele alınan sorunları tanımlayabilir ve ayrıca, test odaklı dağıtım (TDD) olarak da adlandırılan değişiklikler sırasını önceliklendirmenize olanak sağlayabilirsiniz.
* Bazı testlerin Azure Resource Manager bağlı olmadan gerçekleştirilebileceğini unutmayın. Diğer bir deyişle, şablon dağıtımını test etme gibi, çevrimdışı olarak gerçekleştirilemediği belirli eylemleri gerçekleştirmek için Kaynak Yöneticisi gerekir.
* Bir dağıtım şablonunu doğrulama API 'sine karşı test etmek gerçek dağıtıma eşit değildir. Ayrıca, yerel bir dosyadan şablon dağıtsanız bile, şablondaki iç içe şablonların başvuruları doğrudan Kaynak Yöneticisi alınır ve VM uzantıları tarafından başvurulan yapıtlar, dağıtılan VM içinde çalışan VM Aracısı tarafından alınır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Resource Manager şablonu konuları](/azure-stack/user/azure-stack-develop-templates)
* [ARM şablonları için en iyi uygulamalar](template-syntax.md)
