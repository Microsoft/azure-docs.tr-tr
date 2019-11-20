---
title: Şablonlar için en iyi uygulamalar
description: Azure Resource Manager şablonları yazmak için önerilen yaklaşımları açıklar. Şablonları kullanırken yaygın sorunlardan kaçınmak için öneriler sunar.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 7e1b6496302af3edde4d888c67ec3e461d300a5a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150305"
---
# <a name="azure-resource-manager-template-best-practices"></a>Azure Resource Manager şablonu en iyi uygulamaları

Bu makale, Kaynak Yöneticisi şablonunuzun nasıl oluşturulacağı hakkında öneriler sağlar. Bu öneriler, bir çözümü dağıtmak için şablon kullanırken karşılaşılan yaygın sorunlardan kaçınmanıza yardımcı olur.

Azure aboneliklerinizi yönetme hakkında öneriler için bkz. [Azure Kurumsal yapı iskelesi: seçkin abonelik](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)İdaresi.

Tüm Azure bulut ortamlarında çalışan şablonların nasıl oluşturulacağı hakkında öneriler için bkz. [bulut tutarlılığı için Azure Resource Manager şablonları geliştirme](templates-cloud-consistency.md).

## <a name="template-limits"></a>Şablon sınırları

Şablonunuzun boyutunu 4 MB ve her bir parametre dosyası 64 KB olarak sınırlandırın. 4 MB sınırı, yineleme, yinelemeli kaynak tanımları ve değişkenler ve parametreler için genişletildikten sonra şablonun son durumuna uygulanır. 

Şunları da sınırlayabilirsiniz:

* 256 parametreleri
* 256 değişkenleri
* 800 kaynak (kopya sayısı dahil)
* 64 çıkış değeri
* Şablon ifadesinde 24.576 karakter

İç içe geçmiş bir şablon kullanarak bazı şablon sınırlarını aşabilirsiniz. Daha fazla bilgi için bkz. [Azure kaynaklarını dağıtmaya yönelik bağlı şablonları kullanma](resource-group-linked-templates.md). Parametre, değişken veya çıkış sayısını azaltmak için, birkaç değeri bir nesne içinde birleştirebilirsiniz. Daha fazla bilgi için bkz. [nesneler parametreler olarak](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Kaynak grubu

Kaynakları bir kaynak grubuna dağıttığınızda, kaynak grubu kaynaklarla ilgili meta verileri depolar. Meta veriler, kaynak grubunun konumunda depolanır.

Kaynak grubunun bölgesi geçici olarak kullanılamıyorsa, meta veriler kullanılamadığından kaynak grubundaki kaynakları güncelleştiremezsiniz. Diğer bölgelerdeki kaynaklar beklendiği gibi çalışmaya devam eder, ancak bunları güncelleştiremezsiniz. Riski en aza indirmek için, kaynak grubunuzu ve kaynaklarınızı aynı bölgede bulun.

## <a name="parameters"></a>Parametreler

Bu bölümdeki bilgiler, [parametrelerle](template-parameters.md)çalışırken yararlı olabilir.

### <a name="general-recommendations-for-parameters"></a>Parametreler için genel öneriler

* Parametrelerin kullanımını en aza indirin. Bunun yerine, dağıtım sırasında belirtilmesi gerekmeyen özellikler için değişkenler veya sabit değerler kullanın.

* Parametre adları için ortası Case kullanın.

* SKU, boyut veya kapasite gibi ortama göre farklılık gösteren ayarlar için parametreleri kullanın.

* Kolay tanımlama için belirtmek istediğiniz kaynak adları için parametreleri kullanın.

* Meta verilerdeki her parametre için bir açıklama girin:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Gizli olmayan parametreler için varsayılan değerleri tanımlayın. Varsayılan bir değer belirterek, şablonu dağıtmak daha kolaydır ve şablonunuzun kullanıcıları uygun bir değere bir örnek görür. Varsayılan dağıtım yapılandırmasındaki tüm kullanıcılar için bir parametre için herhangi bir varsayılan değer geçerli olmalıdır. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* İsteğe bağlı bir parametre belirtmek için, varsayılan değer olarak boş bir dize kullanmayın. Bunun yerine, bir değer oluşturmak için bir sabit değer veya dil ifadesi kullanın.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Bir kaynak türü için API sürümü için bir parametre kullanmayın. Kaynak özellikleri ve değerleri, sürüm numarasına göre farklılık gösterebilir. API sürümü bir parametreye ayarlandığında, bir kod düzenleyicisinde IntelliSense doğru şemayı belirleyemiyor. Bunun yerine, şablondaki API sürümünü sabit kodlayın.

* `allowedValues` gelişigüzel kullanın. Yalnızca bazı değerlerin izin verilen seçeneklere dahil edilmediğinden emin olmanız gerektiğinde bunu kullanın. `allowedValues` çok büyük kullanıyorsanız, listenizi güncel tutmamak için geçerli dağıtımları engelleyebilirsiniz.

* Şablonunuzda bir parametre adı PowerShell dağıtım komutundaki bir parametreyle eşleştiğinde Kaynak Yöneticisi, bu adlandırma çakışmasını, sonek **FromTemplate** 'i Şablon parametresine ekleyerek çözer. Örneğin, şablonunuza **resourcegroupname** adlı bir parametre eklerseniz, bu, [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet 'inin **resourcegroupname** parametresiyle çakışıyor. Dağıtım sırasında, **Resourcegroupnamefromtemplate**için bir değer sağlamanız istenir.

### <a name="security-recommendations-for-parameters"></a>Parametreler için güvenlik önerileri

* Her zaman Kullanıcı adları ve parolalar (veya gizli diziler) için parametreleri kullanın.

* Tüm parolalar ve gizlilikler için `securestring` kullanın. Gizli verileri bir JSON nesnesinde geçirirseniz `secureObject` türünü kullanın. Güvenli dize veya güvenli nesne türleri olan şablon parametreleri kaynak dağıtımdan sonra okunamaz. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Kullanıcı adları, parolalar veya `secureString` türü gerektiren herhangi bir değer için varsayılan değerler sağlamaz.

* Uygulamanın saldırı yüzeyi alanını artıran özellikler için varsayılan değerler sağlamama.

### <a name="location-recommendations-for-parameters"></a>Parametrelerin konum önerileri

* Kaynak konumunu belirtmek için bir parametre kullanın ve varsayılan değeri `resourceGroup().location`olarak ayarlayın. Bir konum parametresi sağlamak, şablon kullanıcılarının, dağıtma iznine sahip oldukları bir konum belirtmesini sağlar.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Konum parametresi için `allowedValues` belirtmeyin. Belirttiğiniz konumlar tüm bulutlarda kullanılamayabilir.

* Büyük olasılıkla aynı konumda olabilecek kaynaklar için konum parametresi değerini kullanın. Bu yaklaşım, kullanıcıların konum bilgilerini sağlaması için kaç kez sorulduğu sayısını en aza indirir.

* Tüm konumlarda kullanılamayan kaynaklar için ayrı bir parametre kullanın veya sabit bir konum değeri belirtin.

## <a name="variables"></a>Değişkenler

Aşağıdaki bilgiler, [değişkenlerle](template-variables.md)çalışırken yararlı olabilir:

* Değişken adları için ortası Case kullanın.

* Bir şablonda birden çok kez kullanmanız gereken değerler için değişkenleri kullanın. Bir değer yalnızca bir kez kullanılıyorsa, sabit kodlanmış bir değer şablonunuzun okunmasını kolaylaştırır.

* Şablon işlevlerinin karmaşık bir düzenlemesini oluşturduğunuz değerler için değişkenleri kullanın. Karmaşık ifade yalnızca değişkenlerde göründüğünde şablonunuz daha kolay okunabilir.

* Bir kaynaktaki `apiVersion` için değişkenler kullanmayın. API sürümü, kaynağın şemasını belirler. Genellikle, kaynağın özelliklerini değiştirmeden sürümü değiştiremezsiniz.

* Şablonun **değişkenler** bölümünde [başvuru](resource-group-template-functions-resource.md#reference) işlevini kullanamazsınız. **Başvuru** işlevi, kaynağın çalışma zamanı durumundan değerini türetir. Ancak, değişkenler, şablonun ilk ayrıştırması sırasında çözümlenir. Şablonun **kaynaklar** veya **çıktılar** bölümünde **başvuru** işlevine ihtiyacı olan değerleri doğrudan oluşturun.

* Benzersiz olması gereken kaynak adları için değişkenleri dahil edin.

* JSON nesnelerinin tekrarlanmış bir modelini oluşturmak için [değişkenlerde bir kopyalama döngüsü](resource-group-create-multiple.md#variable-iteration) kullanın.

* Kullanılmayan değişkenleri kaldırın.

## <a name="resource-dependencies"></a>Kaynak bağımlılıkları

Ayarlanacak [bağımlılıklara](resource-group-define-dependencies.md) karar verirken aşağıdaki yönergeleri kullanın:

* Bir özelliği paylaşması gereken kaynaklar arasında örtük bir bağımlılık ayarlamak için **başvuru** işlevini kullanın ve kaynak adını geçirin. Örtük bir bağımlılığı zaten tanımladığınız zaman açık bir `dependsOn` öğesi eklemeyin. Bu yaklaşım, gereksiz bağımlılıklara sahip olma riskini azaltır.

* Alt kaynağı üst kaynağına bağımlı olarak ayarlayın.

* [Koşul öğesi](conditional-resource-deployment.md) false olarak ayarlanan kaynaklar, bağımlılık siparişinden otomatik olarak kaldırılır. Bağımlılıkları, kaynak her zaman dağıtılır gibi ayarlayın.

* Bağımlılıkların açıkça Ayarlamasız şekilde basamaklı olmasına izin verin. Örneğin, sanal makineniz sanal bir ağ arabirimine bağlıdır ve sanal ağ arabirimi bir sanal ağ ve genel IP adreslerine bağlıdır. Bu nedenle, sanal makine üç kaynaktan sonra dağıtılır, ancak sanal makineyi tüm üç kaynağa bağımlı olarak açıkça ayarlamazsanız. Bu yaklaşım bağımlılık sırasını açıklar ve şablonu daha sonra değiştirmeyi kolaylaştırır.

* Dağıtım öncesinde bir değer belirlenebileceği takdirde, kaynağı bir bağımlılık olmadan dağıtmaya çalışın. Örneğin, bir yapılandırma değeri başka bir kaynağın adına ihtiyaç duyuyorsa, bağımlılığa gerek duymayabilir. Bazı kaynaklar diğer kaynağın varlığını doğrulamadığı için bu kılavuz her zaman çalışmaz. Bir hata alırsanız, bir bağımlılık ekleyin.

## <a name="resources"></a>Kaynaklar

[Kaynaklarla](resource-group-authoring-templates.md#resources)çalışırken aşağıdaki bilgiler yararlı olabilir:

* Diğer katkı sağlayanlar kaynağın amacını anlamalarına yardımcı olmak için şablondaki her bir kaynak için **açıklamalar** belirtin:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Şablonunuzda *ortak bir uç nokta* (Azure Blob depolama genel uç noktası gibi) kullanıyorsanız, ad alanını sabit bir şekilde *kodmayın* . Ad alanını dinamik olarak almak için **başvuru** işlevini kullanın. Şablonu şablondaki uç noktayı el ile değiştirmeden farklı genel ad alanı ortamlarına dağıtmak için bu yaklaşımı kullanabilirsiniz. API sürümünü, şablonunuzda depolama hesabı için kullandığınız sürüme ayarlayın:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Depolama hesabı oluşturmakta olduğunuz şablona dağıtılmışsa ve depolama hesabının adı şablondaki başka bir kaynakla paylaşılmamışsa, kaynağa başvurduğunuzda sağlayıcı ad alanını veya apiVersion 'ı belirtmeniz gerekmez. Aşağıdaki örnek basitleştirilmiş sözdizimini göstermektedir:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Farklı bir kaynak grubunda bulunan mevcut bir depolama hesabına de başvurabilirsiniz:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Bir sanal makineye yalnızca bir uygulama gerektirdiğinde genel IP adresleri atayın. Hata ayıklama için bir sanal makineye (VM) bağlanmak veya yönetim veya yönetim amaçlarıyla, gelen NAT kuralları, bir sanal ağ geçidi veya bir atlama kutusu kullanın.
   
     Sanal makinelere bağlanma hakkında daha fazla bilgi için bkz.:
   
   * [Azure 'da N katmanlı mimari için VM 'Leri çalıştırma](../guidance/guidance-compute-n-tier-vm.md)
   * [Azure Resource Manager VM 'Ler için WinRM erişimi ayarlama](../virtual-machines/windows/winrm.md)
   * [Azure portal kullanarak sanal makinenize dış erişime izin verin](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [PowerShell kullanarak sanal makinenize dış erişime izin verin](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Azure CLı kullanarak Linux VM 'nize dışarıdan erişime izin verin](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* Genel IP adresleri için **Domainnamelabel** özelliği benzersiz olmalıdır. **Domainnamelabel** değeri 3 ila 63 karakter uzunluğunda olmalı ve bu normal ifade tarafından belirtilen kuralları izlemelidir: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. **Uniquestring** işlevi 13 karakter uzunluğunda bir dize oluşturduğundan, **Dnsprefixstring** parametresi 50 karakterle sınırlıdır:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Özel bir betik uzantısına bir parola eklediğinizde **Protectedsettings** özelliğindeki **commandtoexecute** özelliğini kullanın:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > VM 'Ler ve uzantılara parametre olarak geçirildiğinde gizli dizileri şifrelendiğinden emin olmak için ilgili uzantıların **Protectedsettings** özelliğini kullanın.
   > 
   > 

## <a name="outputs"></a>Çıkışlar

Genel IP adresleri oluşturmak için bir şablon kullanırsanız, IP adresi ayrıntılarını ve tam etki alanı adını (FQDN) döndüren bir [çıktılar bölümü](template-outputs.md) ekleyin. Çıkış değerleri, bir kolayca dağıtımdan sonra genel IP adresleri ve FQDN'ler hakkında ayrıntıları almak için kullanabilirsiniz.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* Kaynak Yöneticisi Şablon dosyasının yapısı hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](resource-group-authoring-templates.md).
* Tüm Azure bulut ortamlarında çalışan şablonların nasıl oluşturulacağı hakkında öneriler için bkz. [bulut tutarlılığı için Azure Resource Manager şablonları geliştirme](templates-cloud-consistency.md).
