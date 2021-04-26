---
title: Şablonlar için en iyi uygulamalar
description: Azure Resource Manager şablonları (ARM şablonları) yazmak için önerilen yaklaşımları açıklar. Şablonları kullanırken yaygın sorunlardan kaçınmak için öneriler sunar.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: ff2b9ecf0e4004aa6689294867f5ff93006211ec
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219952"
---
# <a name="arm-template-best-practices"></a>ARM şablonu en iyi uygulamaları

Bu makalede, Azure Resource Manager şablonunuzu (ARM şablonu) oluştururken önerilen uygulamaların nasıl kullanılacağı gösterilmektedir. Bu öneriler, bir çözümü dağıtmak için ARM şablonu kullanırken yaygın sorunlardan kaçınmanıza yardımcı olur.

## <a name="template-limits"></a>Şablon sınırları

Şablonunuzun boyutunu 4 MB ve her bir parametre dosyası 64 KB olarak sınırlandırın. 4 MB sınırı, yineleme, yinelemeli kaynak tanımları ve değişkenler ve parametreler için genişletildikten sonra şablonun son durumuna uygulanır.

Şunları da sınırlayabilirsiniz:

* 256 parametreleri
* 256 değişkenleri
* 800 kaynak (kopya sayısı dahil)
* 64 çıkış değeri
* Şablon ifadesinde 24.576 karakter

İç içe geçmiş bir şablon kullanarak bazı şablon sınırlarını aşabilirsiniz. Daha fazla bilgi için bkz. [Azure kaynaklarını dağıttığınızda bağlı ve iç içe Şablonlar kullanma](linked-templates.md). Parametre, değişken veya çıkış sayısını azaltmak için, birkaç değeri bir nesne içinde birleştirebilirsiniz. Daha fazla bilgi için bkz. [nesneler parametreler olarak](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).

## <a name="resource-group"></a>Kaynak grubu

Kaynakları bir kaynak grubuna dağıttığınızda, kaynak grubu kaynaklarla ilgili meta verileri depolar. Meta veriler, kaynak grubunun konumunda depolanır.

Kaynak grubunun bölgesi geçici olarak kullanılamıyorsa, meta veriler kullanılamadığından kaynak grubundaki kaynakları güncelleştiremezsiniz. Diğer bölgelerdeki kaynaklar beklendiği gibi çalışmaya devam eder, ancak bunları güncelleştiremezsiniz. Riski en aza indirmek için, kaynak grubunuzu ve kaynaklarınızı aynı bölgede bulun.

## <a name="parameters"></a>Parametreler

Bu bölümdeki bilgiler, [parametrelerle](template-parameters.md)çalışırken yararlı olabilir.

### <a name="general-recommendations-for-parameters"></a>Parametreler için genel öneriler

* Parametrelerin kullanımını en aza indirin. Bunun yerine, dağıtım sırasında belirtilmesi gerekmeyen özellikler için değişkenler veya sabit değerler kullanın.

* Parametre adları için ortası Case kullanın.

* Ortama göre değişen SKU, boyut veya kapasite gibi ayarlar için parametreleri kullanın.

* Kolay tanımlama için belirtmek istediğiniz kaynak adları için parametreleri kullanın.

* Meta verilerdeki her parametre için bir açıklama sağlayın.

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
   }
   ```

* `allowedValues`Gelişigüzel kullanın. Yalnızca bazı değerlerin izin verilen seçeneklere dahil edilmediğinden emin olmanız gerektiğinde bunu kullanın. `allowedValues`Çok geniş kullanıyorsanız, listenizi güncel tutmayan geçerli dağıtımları engelleyebilirsiniz.

* Şablonunuzda bir parametre adı PowerShell dağıtım komutundaki bir parametreyle eşleştiğinde Kaynak Yöneticisi, bu adlandırma çakışmasını, sonek **FromTemplate** 'i Şablon parametresine ekleyerek çözer. Örneğin, şablonunuza **resourcegroupname** adlı bir parametre eklerseniz, bu, [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet 'inin **resourcegroupname** parametresiyle çakışıyor. Dağıtım sırasında, **Resourcegroupnamefromtemplate** için bir değer sağlamanız istenir.

### <a name="security-recommendations-for-parameters"></a>Parametreler için güvenlik önerileri

* Her zaman Kullanıcı adları ve parolalar (veya gizli diziler) için parametreleri kullanın.

* `securestring`Tüm parolalar ve gizlilikler için kullanın. Gizli verileri bir JSON nesnesinde geçirirseniz, `secureObject` türü kullanın. Güvenli dize veya güvenli nesne türleri olan şablon parametreleri kaynak dağıtımdan sonra okunamaz.

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

* Kullanıcı adları, parolalar veya tür gerektiren herhangi bir değer için varsayılan değerler sağlamaz `secureString` .

* Uygulamanın saldırı yüzeyi alanını artıran özellikler için varsayılan değerler sağlamama.

### <a name="location-recommendations-for-parameters"></a>Parametrelerin konum önerileri

* Kaynak konumunu belirtmek için bir parametre kullanın ve varsayılan değeri olarak ayarlayın `resourceGroup().location` . Bir konum parametresi sağlamak, şablon kullanıcılarının kaynakları dağıtma iznine sahip oldukları bir konum belirtmesini sağlar.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   }
   ```

* `allowedValues`Konum parametresi için belirtmeyin. Belirttiğiniz konumlar tüm bulutlarda kullanılamayabilir.

* Büyük olasılıkla aynı konumda olabilecek kaynaklar için konum parametresi değerini kullanın. Bu yaklaşım, kullanıcıların konum bilgilerini sağlaması için kaç kez sorulduğu sayısını en aza indirir.

* Tüm konumlarda kullanılamayan kaynaklar için ayrı bir parametre kullanın veya sabit bir konum değeri belirtin.

## <a name="variables"></a>Değişkenler

Aşağıdaki bilgiler, [değişkenlerle](template-variables.md)çalışırken yararlı olabilir:

* Değişken adları için ortası Case kullanın.

* Bir şablonda birden çok kez kullanmanız gereken değerler için değişkenleri kullanın. Bir değer yalnızca bir kez kullanılıyorsa, sabit kodlanmış bir değer şablonunuzun okunmasını kolaylaştırır.

* Şablon işlevlerinin karmaşık bir düzenlemesini oluşturduğunuz değerler için değişkenleri kullanın. Karmaşık ifade yalnızca değişkenlerde göründüğünde şablonunuz daha kolay okunabilir.

* Şablonun bölümünde [başvuru](template-functions-resource.md#reference) işlevini kullanamazsınız `variables` . `reference`İşlevi, kaynağın çalışma zamanı durumundan değerini türetir. Ancak, değişkenler, şablonun ilk ayrıştırması sırasında çözümlenir. `reference`Doğrudan `resources` şablonun veya bölümündeki işlevine ihtiyacı olan değerleri oluşturun `outputs` .

* Benzersiz olması gereken kaynak adları için değişkenleri dahil edin.

* JSON nesnelerinin tekrarlanmış bir modelini oluşturmak için [değişkenlerde bir kopyalama döngüsü](copy-variables.md) kullanın.

* Kullanılmayan değişkenleri kaldırın.

## <a name="api-version"></a>API sürümü

Özelliği, `apiVersion` kaynak türü için sabit kodlanmış BIR API sürümüne ayarlayın. Yeni bir şablon oluştururken, bir kaynak türü için en son API sürümünü kullanmanızı öneririz. Kullanılabilir değerleri anlamak için bkz. [şablon başvurusu](/azure/templates/).

Şablonunuz beklendiği gibi çalışıyorsa, aynı API sürümünü kullanmaya devam etmenizi öneririz. Aynı API sürümünü kullanarak, sonraki sürümlerde kullanıma sunulmuş olabilecek son değişiklikler hakkında endişelenmeniz gerekmez.

API sürümü için bir parametre kullanmayın. Kaynak özellikleri ve değerler, API sürümüne göre farklılık gösterebilir. API sürümü bir parametreye ayarlandığında, bir kod düzenleyicisinde IntelliSense doğru şemayı belirleyemiyor. Şablonunuzda özelliklerle eşleşmeyen bir API sürümünü geçirirseniz dağıtım başarısız olur.

API sürümü için değişkenler kullanmayın. 

## <a name="resource-dependencies"></a>Kaynak bağımlılıkları

Ayarlanacak [bağımlılıklara](define-resource-dependency.md) karar verirken aşağıdaki yönergeleri kullanın:

* `reference`Bir özelliği paylaşması gereken kaynaklar arasında örtük bir bağımlılık ayarlamak için işlevini kullanın ve kaynak adını geçirin. `dependsOn`Örtük bir bağımlılığı zaten tanımladıysanız açık bir öğe eklemeyin. Bu yaklaşım, gereksiz bağımlılıklara sahip olma riskini azaltır. Örtük bir bağımlılık ayarlamaya ilişkin bir örnek için bkz. [başvuru ve liste işlevleri](define-resource-dependency.md#reference-and-list-functions).

* Alt kaynağı üst kaynağına bağımlı olarak ayarlayın.

* [Koşul öğesi](conditional-resource-deployment.md) false olarak ayarlanan kaynaklar, bağımlılık siparişinden otomatik olarak kaldırılır. Bağımlılıkları, kaynak her zaman dağıtılır gibi ayarlayın.

* Bağımlılıkların açıkça Ayarlamasız şekilde basamaklı olmasına izin verin. Örneğin, sanal makineniz sanal bir ağ arabirimine bağlıdır ve sanal ağ arabirimi bir sanal ağ ve genel IP adreslerine bağlıdır. Bu nedenle, sanal makine üç kaynaktan sonra dağıtılır, ancak sanal makineyi tüm üç kaynağa bağımlı olarak açıkça ayarlamazsanız. Bu yaklaşım bağımlılık sırasını açıklar ve şablonu daha sonra değiştirmeyi kolaylaştırır.

* Dağıtım öncesinde bir değer belirlenebileceği takdirde, kaynağı bir bağımlılık olmadan dağıtmaya çalışın. Örneğin, bir yapılandırma değeri başka bir kaynağın adına ihtiyaç duyuyorsa, bağımlılığa gerek duymayabilir. Bazı kaynaklar diğer kaynağın varlığını doğrulamadığı için bu kılavuz her zaman çalışmaz. Bir hata alırsanız, bir bağımlılık ekleyin.

## <a name="resources"></a>Kaynaklar

[Kaynaklarla](template-syntax.md#resources)çalışırken aşağıdaki bilgiler yararlı olabilir:

* Diğer katkı sağlayanlar kaynağın amacını anlamalarına yardımcı olmak için `comments` şablondaki her kaynak için belirtin.

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

* Şablonunuzda *ortak bir uç nokta* (Azure Blob depolama genel uç noktası gibi) kullanıyorsanız, ad alanını sabit bir şekilde *kodmayın* . `reference`Ad alanını dinamik olarak almak için işlevini kullanın. Şablonu şablondaki uç noktayı el ile değiştirmeden farklı genel ad alanı ortamlarına dağıtmak için bu yaklaşımı kullanabilirsiniz. API sürümünü, şablonunuzda depolama hesabı için kullandığınız sürüme ayarlayın.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

   Depolama hesabı oluşturmakta olduğunuz şablona dağıtılmışsa ve depolama hesabının adı şablondaki başka bir kaynakla paylaşılmamışsa, sağlayıcı ad alanını belirtmeniz veya kaynağa başvurduğunuzda emin olmanız gerekmez `apiVersion` . Aşağıdaki örnek basitleştirilmiş söz dizimini gösterir.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

   Ayrıca, farklı bir kaynak grubunda bulunan mevcut bir depolama hesabına de başvurabilirsiniz.

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

   * [Azure 'da N katmanlı mimari için VM 'Leri çalıştırma](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [Azure Resource Manager VM 'Ler için WinRM erişimi ayarlama](../../virtual-machines/windows/winrm.md)
   * [Azure portal kullanarak sanal makinenize dış erişime izin verin](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [PowerShell kullanarak sanal makinenize dış erişime izin verin](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Azure CLı kullanarak Linux VM 'nize dışarıdan erişime izin verin](../../virtual-machines/linux/nsg-quickstart.md)

* `domainNameLabel`Genel IP adresleri özelliği benzersiz olmalıdır. `domainNameLabel`Değer 3 ila 63 karakter uzunluğunda olmalı ve bu normal ifade tarafından belirtilen kuralları izlemelidir: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` . `uniqueString`İşlevi 13 karakter uzunluğunda bir dize oluşturduğundan, `dnsPrefixString` parametre 50 karakterle sınırlıdır.

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

* Özel bir betik uzantısına bir parola eklediğinizde, özelliğindeki `commandToExecute` özelliğini kullanın `protectedSettings` .

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
   > VM 'Ler ve uzantılara parametre olarak geçirildiğinde gizli dizi şifrelendiğinden emin olmak için `protectedSettings` ilgili uzantıların özelliğini kullanın.

* Zaman içinde değişebilir varsayılan değerlere sahip özellikler için açık değerler belirtin. Örneğin, bir AKS kümesi dağıtıyorsanız, özelliği belirtebilir ya da atlayabilirsiniz `kubernetesVersion` . Bunu belirtmezseniz, [küme varsayılan olarak N-1 alt sürüm ve en son düzeltme eki olarak ayarlanır](../../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions). Bir ARM şablonu kullanarak kümeyi dağıttığınızda, bu varsayılan davranış beklediğiniz gibi olmayabilir. Şablonunuzun yeniden dağıtılması, kümenin yeni bir Kubernetes sürümüne beklenmedik bir şekilde yükseltilmesinden kaynaklanabilir. Bunun yerine, bir açık sürüm numarası belirtmeyi ve sonra kümenizi yükseltmeye hazırsanız el ile değiştirmeyi düşünün.

## <a name="use-test-toolkit"></a>Test araç setini kullanma

ARM şablonu test araç seti, şablonunuzun önerilen uygulamalar kullanıp kullanmadığını denetleyen bir betiktir. Şablonunuz Önerilen uygulamalarla uyumlu olmadığında, önerilen değişikliklerle ilgili uyarıların bir listesini döndürür. Test araç seti, şablonunuzda en iyi uygulamaların nasıl uygulanacağını öğrenmenize yardımcı olabilir.

Şablonunuzu tamamladıktan sonra, uygulamayı iyileştirebileceğinizi görmek için test araç setini çalıştırın. Daha fazla bilgi için bkz. [ARM şablonu test araç seti kullanma](test-toolkit.md).

## <a name="next-steps"></a>Sonraki adımlar

* Şablon dosyasının yapısı hakkında daha fazla bilgi için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
* Tüm Azure bulut ortamlarında çalışan şablonların nasıl oluşturulacağı hakkında öneriler için bkz. [bulut tutarlılığı IÇIN ARM şablonları geliştirme](templates-cloud-consistency.md).
