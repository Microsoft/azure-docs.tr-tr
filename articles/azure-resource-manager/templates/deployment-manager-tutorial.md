---
title: Şablonları dağıtmak için Azure Deployment Manager kullanma
description: Azure kaynaklarını dağıtmak için Azure Deployment Manager ile Kaynak Yöneticisi şablonlarını nasıl kullanacağınızı öğrenin.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627591"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Öğretici: Azure Deployment Manager’ı Resource Manager şablonlarıyla kullanma (Genel önizleme)

[Azure Deployment Manager](./deployment-manager-overview.md)’ı kullanarak uygulamalarınızı birden çok bölgede nasıl dağıtacağınızı öğrenin. Daha hızlı bir yaklaşım tercih ediyorsanız, [Azure Deployment Manager hızlı başlangıç](https://github.com/Azure-Samples/adm-quickstart) aboneliğinizde gerekli konfigürasyonları oluşturur ve bir uygulamayı birden çok bölgede dağıtmak için yapıtları özelleştirir. Hızlı başlangıç, bu öğreticide olduğu gibi aynı görevleri gerçekleştirir.

Deployment Manager kullanmak için iki şablon oluşturmanız gerekir:

* **Topoloji şablonu**: Uygulamalarınızı Azure kaynaklarını ve bunların dağıtılacağı yeri açıklar.
* **Dağıtım şablonu**: Uygulamalarınızı dağıtırken uygulanacak adımları açıklar.

> [!IMPORTANT]
> Aboneliğiniz yeni Azure özelliklerini test etmek üzere işaretlenmişse, Azure Deployment Manager 'yi yalnızca Canary bölgelerine dağıtmak için kullanabilirsiniz.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Senaryoyu anlama
> * Öğretici dosyalarını indirme
> * Yapıtları hazırlama
> * Kullanıcı tanımlı yönetilen kimliği oluşturma
> * Hizmet topolojisi şablonunu oluşturma
> * Piyasaya çıkarma şablonunu oluşturma
> * Şablonları dağıtma
> * Dağıtımı doğrulama
> * Yeni sürümü dağıtma
> * Kaynakları temizleme

Ek kaynaklar:

* [Azure Deployment Manager REST API başvurusu](/rest/api/deploymentmanager/).
* [Öğretici: Azure Deployment Manager 'da sistem durumu denetimi kullanın](./deployment-manager-tutorial-health-check.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
* [Azure Resource Manager şablonlarını](overview.md) geliştirme konusunda deneyim.
* Azure PowerShell. Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya başlayın](/powershell/azure/get-started-azureps).
* Deployment Manager cmdlet'leri. Bu yayın öncesi cmdlet’leri yüklemek için PowerShellGet’in en son sürümü gereklidir. En son sürümü edinmek için bkz. [PowerShellGet’i Yükleme](/powershell/scripting/gallery/installing-psget). PowerShellGet’i yükledikten sonra PowerShell penceresini kapatın. Yeni bir yükseltilmiş PowerShell penceresi açın ve aşağıdaki komutu kullanın:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Senaryoyu anlama

Hizmet topolojisi şablonu, hizmetinizi oluşturan Azure kaynaklarını ve bunların nereye dağıtılacağını açıklar. Hizmet topolojisi tanımı şu hiyerarşiye sahiptir:

* Hizmet topolojisi
  * Hizmetler
    * Hizmet birimleri

Aşağıdaki diyagramda, bu öğreticide kullanılan hizmet topolojisi gösterilmektedir:

![Azure Deployment Manager öğreticisi senaryo diyagramı](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Batı ABD ve Doğu ABD konumlarında ayrılan iki hizmet vardır. Her hizmetin iki hizmet birimi vardır: ön uç Web uygulaması ve arka uç depolama hesabı. Hizmet birimi tanımları, Web uygulamalarını ve depolama hesaplarını oluşturan şablon ve parametre dosyalarının bağlantılarını içerir.

## <a name="download-the-tutorial-files"></a>Öğretici dosyalarını indirme

1. Bu öğreticide kullanılan [şablonları ve yapıtları](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) indirin.
1. Dosyaları bilgisayarınızdaki konuma ayıklayın.

Kök klasörde iki klasör vardır:

* _ADMTemplates_: burada, aşağıdakileri içeren Deployment Manager şablonları yer alır:
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore_: hem şablon yapıtlarını hem de ikili dosya yapıtlarını içerir. [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.

İki şablon kümesi vardır. Bir küme, hizmet topolojisini ve dağıtımı dağıtmak için kullanılan Deployment Manager şablonlardır. Diğer küme, Web Hizmetleri ve depolama hesapları oluşturmak için hizmet birimlerinden çağırılır.

## <a name="prepare-the-artifacts"></a>Yapıtları hazırlama

İndirilen ArtifactStore klasörü iki klasör içerir:

![Azure Deployment Manager öğreticisi yapıt kaynağı diyagramı](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* _Şablonlar_ klasörü: şablon yapıtlarını içerir. _1.0.0.0_ ve _1.0.0.1_ klasörleri ikili yapıtların iki sürümünü temsil eder. Her sürümde, her bir hizmet için bir klasör vardır: _Serviceeus_ (Service Doğu ABD) ve _servicewus_ (hizmet Batı ABD). Her hizmette depolama hesabı oluşturmak için bir şablon çifti ve web uygulaması oluşturmak için başka bir şablon çifti vardır. Web uygulaması şablonu, web uygulaması dosyalarını içeren sıkıştırılmış bir paket çağırır. Sıkıştırılmış dosya, ikili dosyalar klasöründe depolanan bir ikili dosya yapıtıdır.
* _İkili dosyalar_ klasörü: ikili dosya yapıtları içerir. _1.0.0.0_ ve _1.0.0.1_ klasörleri ikili yapıtların iki sürümünü temsil eder. Her sürümde, Batı ABD konumunda Web uygulaması oluşturmak için bir ZIP dosyası ve Doğu ABD konumunda Web uygulamasını oluşturmak için ise ZIP dosyası vardır.

İki sürüm (1.0.0.0 ve 1.0.0.1) [sürüm dağıtımı](#deploy-the-revision) içindir. Şablon yapıtları ve ikili dosya yapıtlarının iki sürümü olsa da, iki sürüm arasında yalnızca ikili dosya yapıtları farklıdır. Gerçekte, ikili dosya yapıtları şablon yapıtlarına göre daha sık güncelleştirilir.

1. _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_ dosyasını bir metin düzenleyicisinde açın. Bu, bir depolama hesabı oluşturmak için temel bir şablondur.
1. _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_ dosyasını açın.

    ![Azure Deployment Manager öğreticisi web uygulaması şablonu oluşturma](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Şablon, web uygulamasının dosyalarını içeren bir dağıtım paketi çağırır. Bu öğreticide, sıkıştırılmış paket yalnızca bir _index.html_ dosyası içerir.
1. _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_ dosyasını açın.

    ![Azure Deployment Manager öğreticisi web uygulaması şablonu parametreleri containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Değeri, `deployPackageUri` dağıtım paketinin yoludur. Parametresi bir değişken içeriyor `$containerRoot` . Değeri, `$containerRoot` yapıt kaynağı SAS konumu, yapıt kökü ve ile birleştirerek [dağıtım şablonunda](#create-the-rollout-template) sağlanır `deployPackageUri` .
1. _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_ dosyasını açın.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    HTML, konumu ve sürüm bilgilerini gösterir. _1.0.0.1_ klasöründeki Ikili dosya _sürümü 1.0.0.1_ gösterir. Hizmeti dağıttıktan sonra bu sayfalara göz atabilirsiniz.
1. Diğer yapıt dosyalarını inceleyin. Senaryoyu daha iyi anlamanıza yardımcı olur.

Şablon yapıtları hizmet topolojisi şablonu tarafından kullanılır ve ikili dosya yapıtları piyasaya çıkarma şablonu tarafından kullanılır. Topoloji şablonu ve piyasaya çıkarma şablonu bir yapıt kaynağı Azure kaynağı tanımlar. Bu kaynak, Resource Manager’ı dağıtımda kullanılan şablona ve ikili dosya yapıtlarına yönlendirmek için kullanılır. Öğreticiyi basitleştirmek amacıyla, hem şablon yapıtları hem de ikili dosya yapıtlarını depolamak için bir depolama hesabı kullanılmıştır. Her iki yapıt kaynağı aynı depolama hesabını işaret eder.

Bir kaynak grubu oluşturmak, bir depolama kapsayıcısı oluşturmak, bir blob kapsayıcısı oluşturmak, indirilen dosyaları karşıya yüklemek ve ardından bir SAS belirteci oluşturmak için aşağıdaki PowerShell betiğini çalıştırın.

> [!IMPORTANT]
> `projectName` PowerShell betiği içinde, bu öğreticide dağıtılan Azure hizmetlerinin adlarını oluşturmak için kullanılır. Farklı Azure Hizmetleri, adlar üzerinde farklı gereksinimlere sahiptir. Dağıtımın başarılı olduğundan emin olmak için, yalnızca küçük harf ve rakam içeren 12 karakterden daha az bir ad seçin.
> Proje adının bir kopyasını kaydedin. `projectName`Öğreticinin tamamında aynısını kullanırsınız.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

SAS belirteciyle URL 'nin bir kopyasını oluşturun. İki parametre dosyasındaki bir alanı doldurmak için bu URL gereklidir: topoloji parametreleri dosyası ve dağıtım parametreleri dosyası.

Kapsayıcıyı Azure portal açın ve _ikili_ dosyaların ve _şablonların_ klasörlerinin ve dosyaların karşıya yüklendiğini doğrulayın.

## <a name="create-the-user-assigned-managed-identity"></a>Kullanıcı tarafından atanmış yönetilen kimliği oluşturma

Öğreticinin sonraki bölümlerinde bir piyasaya çıkarma dağıtacaksınız. Dağıtım eylemlerini gerçekleştirmek (örneğin web uygulamalarını ve depolama hesabını dağıtma) için kullanıcı tarafından atanmış bir yönetilen kimlik gereklidir. Bu kimliğe, hizmeti dağıttığınız Azure aboneliğine erişim izni verilmelidir ve kimliğin yapıt dağıtımını tamamlamak için yeterli izni olmalıdır.

Kullanıcı tarafından atanmış yönetilen bir kimlik oluşturmanız ve aboneliğiniz için erişim denetimini yapılandırmanız gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. [Kullanıcı tarafından atanmış bir yönetilen kimlik](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) oluşturun.
1. Portalda sol menüden **Abonelikler**’i ve ardından aboneliğinizi seçin.
1. **Erişim denetimi (IAM)** öğesini seçin ve ardından **rol ataması Ekle**' yi seçin.
1. Aşağıdaki değerleri yazın veya seçin:

    ![Azure Deployment Manager öğreticisi kullanıcı tarafından atanmış yönetilen kimlik erişim denetimi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Rol**: yapıt dağıtımını (web uygulamaları ve depolama hesapları) tamamlamak için yeterli izinleri verin. Bu öğreticideki **Katkıda Bulunanı** seçin. Gerçekte, izinleri asgari seviyeyle sınırlamak istersiniz.
    * **Erişim ata**: **Kullanıcı tarafından atanan yönetilen kimlik**' i seçin.
    * Öğreticide daha önce oluşturduğunuz kullanıcı tarafından atanmış yönetilen kimliği seçin.
1. **Kaydet**’i seçin.

## <a name="create-the-service-topology-template"></a>Hizmet topolojisi şablonunu oluşturma

_\ADMTemplates\CreateADMServiceTopology.json_ dosyasını açın.

### <a name="the-parameters"></a>Parametreler

Şablon aşağıdaki parametreleri içerir:

* `projectName`: Bu ad Deployment Manager kaynaklarının adlarını oluşturmak için kullanılır. Örneğin, **demo** kullanarak hizmet topolojisi adı **demo** servicetopology ' dir. Kaynak adları, şablonun `variables` bölümünde tanımlanmıştır.
* `azureResourcelocation`: Öğreticiyi basitleştirmek için, aksi belirtilmediği takdirde tüm kaynaklar bu konumu paylaşır.
* `artifactSourceSASLocation`: Hizmet birim şablonu ve parametre dosyalarının dağıtım için depolandığı blob kapsayıcısının SAS URI 'SI. [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
* `templateArtifactRoot`: Şablon ve parametrelerin depolandığı blob kapsayıcısından gelen konum yolu. Varsayılan değer: _templates/1.0.0.0_. [Yapıtları hazırlama](#prepare-the-artifacts) bölümünde açıklanan klasör yapısını değiştirmek istemiyorsanız bu değeri değiştirmeyin. Bu öğreticide göreli yollar kullanılır. Tam yol `artifactSourceSASLocation` , `templateArtifactRoot` , ve `templateArtifactSourceRelativePath` (veya) birleştirerek oluşturulur `parametersArtifactSourceRelativePath` .
* `targetSubscriptionID`: Deployment Manager kaynaklarının dağıtılacağı ve faturalandırılacağı abonelik KIMLIĞI. Bu öğreticide abonelik kimliğinizi kullanın.

### <a name="the-variables"></a>Değişkenler

Değişkenler bölümü, kaynakların adlarını, iki hizmet için Azure konumlarını, ve `ServiceWUS` `ServiceEUS` ve yapıt yollarını tanımlar:

![Azure Deployment Manager öğreticisi topoloji şablonu değişkenleri](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Yapıt yollarını depolama hesabına yüklediğiniz klasör yapısıyla karşılaştırın. Yapıt yollarının göreli yollar olduğuna dikkat edin. Tam yol `artifactSourceSASLocation` , `templateArtifactRoot` , ve `templateArtifactSourceRelativePath` (veya) birleştirerek oluşturulur `parametersArtifactSourceRelativePath` .

### <a name="the-resources"></a>Kaynaklar

Kök düzeyinde, iki kaynak tanımlanmıştır: *bir yapıt kaynağı* ve *bir hizmet topolojisi*.

Yapıt kaynağı tanımı:

![Azure Deployment Manager öğreticisi topoloji şablonu kaynakları yapıt kaynağı](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Aşağıdaki ekran görüntüsünde hizmet topolojisinin, hizmetlerin ve hizmet birimi tanımlarının yalnızca bazı bölümleri gösterilmektedir:

![Azure Deployment Manager öğreticisi topoloji şablonu kaynakları hizmet topolojisi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId`: Yapıt kaynak kaynağını hizmet topolojisi kaynağıyla ilişkilendirmek için kullanılır.
* `dependsOn`: Tüm hizmet topolojisi kaynakları, yapıt kaynak kaynağına bağlıdır.
* `artifacts`: Şablon yapıtlarını işaret edin. Burada göreli yollar kullanılır. Tam yol, ( `artifactSourceSASLocation` yapıt kaynağında tanımlanır), `artifactRoot` ve `templateArtifactSourceRelativePath` (veya) bitiştirerek oluşturulur `parametersArtifactSourceRelativePath` .

### <a name="topology-parameters-file"></a>Topoloji parametre dosyası

Topoloji şablonuyla kullanılan bir parametre dosyası oluşturursunuz.

1. Visual Studio Code veya herhangi bir metin düzenleyicisinde _\ADMTemplates\CreateADMServiceTopology.Parameters.js_ açın.
1. Parametre değerlerini girin:

    * `projectName`: 4-5 karakter içeren bir dize girin. Bu ad, benzersiz Azure Kaynak adları oluşturmak için kullanılır.
    * `azureResourceLocation`: Azure konumları hakkında bilginiz yoksa, bu öğreticide **merkezileştirme** 'yi kullanın.
    * `artifactSourceSASLocation`: Hizmet birimi şablonunun ve parametre dosyalarının dağıtım için depolandığı kök dizine (blob kapsayıcısı) SAS URI 'sini girin.  [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
    * `templateArtifactRoot`: Yapıtların klasör yapısını değiştirmediğiniz müddetçe, bu öğreticide _Şablonlar/1.0.0.0_ kullanın.

> [!IMPORTANT]
> Topoloji şablonu ve piyasaya çıkarma şablonu bazı ortak parametreleri paylaşır. Bu parametreler aynı değerlere sahip olmalıdır. Bu parametreler: `projectName` , `azureResourceLocation` , ve `artifactSourceSASLocation` (her iki yapıt kaynağı da bu öğreticide aynı depolama hesabını paylaşır).

## <a name="create-the-rollout-template"></a>Piyasaya çıkarma şablonunu oluşturma

_\ADMTemplates\CreateADMRollout.json_ dosyasını açın.

### <a name="the-parameters"></a>Parametreler

Şablon aşağıdaki parametreleri içerir:

![Azure Deployment Manager öğreticisi piyasaya çıkarma şablonu parametreleri](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName`: Bu ad Deployment Manager kaynaklarının adlarını oluşturmak için kullanılır. Örneğin, **tanıtım** kullanımı, dağıtım adı **tanıtım** sunumlarından sorumludur. Adlar, şablonun `variables` bölümünde tanımlanmıştır.
* `azureResourcelocation`: Öğreticiyi basitleştirmek için, aksi belirtilmediği takdirde tüm Deployment Manager kaynakları bu konumu paylaşır.
* `artifactSourceSASLocation`: Hizmet birim şablonu ve parametre dosyalarının dağıtım için depolandığı kök dizine (blob kapsayıcısı) SAS URI 'SI. [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
* `binaryArtifactRoot`: Varsayılan değer _ikililer/1.0.0.0_ değeridir. [Yapıtları hazırlama](#prepare-the-artifacts) bölümünde açıklanan klasör yapısını değiştirmek istemiyorsanız bu değeri değiştirmeyin. Bu öğreticide göreli yollar kullanılır. Tam yol, `artifactSourceSASLocation` `binaryArtifactRoot` , ve `deployPackageUri` _CreateWebApplicationParameters.js_' de belirtilen ile birleştirerek oluşturulur. [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
* `managedIdentityID`: Dağıtım eylemlerini gerçekleştiren kullanıcı tarafından atanan yönetilen kimlik. Bkz. [Kullanıcı tarafından atanmış yönetilen kimlik oluşturma](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Değişkenler

`variables`Bölüm, kaynakların adlarını tanımlar. Hizmet topolojisi adının, hizmet adlarının ve hizmet birimi adlarının [topoloji şablonunda](#create-the-service-topology-template) tanımlı adlarla eşleştiğinden emin olun.

![Azure Deployment Manager öğreticisi piyasaya çıkarma şablonu değişkenleri](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Kaynaklar

Kök düzeyde tanımlı üç kaynak vardır: yapıt kaynağı, adım ve piyasaya çıkarma.

Yapıt kaynağı tanımı, topoloji şablonunda tanımlı olanla aynıdır. Daha fazla bilgi için bkz. [Hizmet topolojisi şablonunu oluşturma](#create-the-service-topology-template).

Aşağıdaki ekran görüntüsünde `wait` adım tanımı gösterilmektedir:

![Azure Deployment Manager öğreticisi piyasaya çıkarma şablonu kaynakları beklemede adımı](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Süre [ıso 8601 standardını](https://en.wikipedia.org/wiki/ISO_8601#Durations)kullanır. **PT1M** (büyük harf zorunludur) 1 dakikalık bekleme süresine örnektir.

Aşağıdaki ekran görüntüsünde piyasaya çıkarma tanımının yalnızca bazı bölümleri gösterilmektedir:

![Azure Deployment Manager öğreticisi piyasaya çıkarma şablonu kaynakları piyasaya çıkarma](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn`: Dağıtım kaynağı, yapıt kaynak kaynağına ve tanımlanan adımlara bağlıdır.
* `artifactSourceId`: Yapıt kaynak kaynağını piyasaya çıkma kaynağıyla ilişkilendirmek için kullanılır.
* `targetServiceTopologyId`: Hizmet topolojisi kaynağını piyasaya çıkma kaynağıyla ilişkilendirmek için kullanılır.
* `deploymentTargetId`: Hizmet topolojisi kaynağının hizmet birimi kaynak KIMLIĞI.
* `preDeploymentSteps` ve `postDeploymentSteps` : dağıtım adımlarını içerir. Şablonda bir `wait` adım çağırılır.
* `dependsOnStepGroups`: Adım grupları arasındaki bağımlılıkları yapılandırın.

### <a name="rollout-parameters-file"></a>Piyasaya çıkarma parametre dosyası

Piyasaya çıkarma şablonuyla kullanılan bir parametre dosyası oluşturursunuz.

1. Visual Studio Code veya herhangi bir metin düzenleyicisinde _\ADMTemplates\CreateADMRollout.Parameters.js_ açın.
1. Parametre değerlerini girin:

    * `projectName`: 4-5 karakter içeren bir dize girin. Bu ad, benzersiz Azure Kaynak adları oluşturmak için kullanılır.
    * `azureResourceLocation`: Bir Azure konumu belirtin.
    * `artifactSourceSASLocation`: Hizmet birimi şablonunun ve parametre dosyalarının dağıtım için depolandığı kök dizine (blob kapsayıcısı) SAS URI 'sini girin.  [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
    * `binaryArtifactRoot`: Yapıtların klasör yapısını değiştirmediğiniz müddetçe bu öğreticide _ikili/1.0.0.0_ kullanın.
    * `managedIdentityID`: Kullanıcı tarafından atanan yönetilen kimliği girin. Bkz. [Kullanıcı tarafından atanmış yönetilen kimlik oluşturma](#create-the-user-assigned-managed-identity). Söz dizimi aşağıdaki gibidir:

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Topoloji şablonu ve piyasaya çıkarma şablonu bazı ortak parametreleri paylaşır. Bu parametreler aynı değerlere sahip olmalıdır. Bu parametreler: `projectName` , `azureResourceLocation` , ve `artifactSourceSASLocation` (her iki yapıt kaynağı da bu öğreticide aynı depolama hesabını paylaşır).

## <a name="deploy-the-templates"></a>Şablonları dağıtma

Azure PowerShell şablonları dağıtmak için kullanılabilir.

1. Hizmet topolojisini dağıtmak için betiği çalıştırın.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Bu betiği, [yapıtları hazırlama](#prepare-the-artifacts) betiğini çalıştırdığınız bilgisayardan farklı bir PowerShell oturumundan çalıştırırsanız, ve dahil olmak üzere önce değişkenleri yeniden doldurmanız gerekir `$resourceGroupName` `$filePath` .

    > [!NOTE]
    > `New-AzResourceGroupDeployment` zaman uyumsuz bir çağrıdır. Başarı iletisi yalnızca dağıtımın başarıyla başladığını gösterir. Dağıtımı doğrulamak için, bu yordamın adım 2 ve adım 4 ' e bakın.

1. Azure portalı kullanarak hizmet topolojisinin ve temel kaynakların başarıyla oluşturulduğunu doğrulayın:

    ![Azure Deployment Manager öğreticisi dağıtılan hizmet topolojisi kaynakları](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Kaynakları görmek için **Gizli türleri göster** seçeneği belirlenmelidir.

1. <a id="deploy-the-rollout-template"></a>Piyasaya çıkarma şablonunu dağıtın:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. Aşağıdaki PowerShell betiğini kullanarak piyasaya çıkarma ilerleme durumunu kontrol edin:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Bu cmdlet'in çalıştırılabilmesi için Deployment Manager PowerShell cmdlet'lerinin yüklü olması gerekir. [Ön koşullara](#prerequisites) bakın. `-Verbose`Tüm çıktıyı görmek için parametresi kullanılabilir.

    Aşağıdaki örnekte çalışma durumu gösterilmektedir:

    ```Output
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Dağıtım başarıyla dağıtıldıktan sonra, her bir hizmet için bir tane daha oluşturulmuş iki kaynak grubu görürsünüz.

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

1. [Azure portalını](https://portal.azure.com) açın.
1. Dağıtım dağıtımı tarafından oluşturulan yeni kaynak grupları altındaki yeni oluşturulan Web uygulamalarına gidin.
1. Web uygulamasını bir web tarayıcıda açın. _index.html_ dosyasındaki konumu ve sürümü doğrulayın.

## <a name="deploy-the-revision"></a>Düzeltmeyi dağıtma

Web uygulamasının yeni bir sürümüne (1.0.0.1) sahip olduğunuzda. Web uygulamasını yeniden dağıtmak için aşağıdaki yordamı kullanabilirsiniz.

1. _ÜzerindeCreateADMRollout.Parameters.js_ açın.
1. `binaryArtifactRoot` _İkililer/1.0.0.1_ güncelleştirin.
1. [Şablonları dağıtma](#deploy-the-rollout-template) bölümünde anlatıldığı gibi piyasaya çıkarmayı yeniden dağıtın.
1. [Dağıtımı doğrulama](#verify-the-deployment) bölümünde anlatıldığı gibi dağıtımı doğrulayın. Web sayfası 1.0.0.1 sürümünü gösterir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
1. Bu öğreticide oluşturulan kaynak gruplarını daraltmak için **Ada göre filtrele** alanını kullanın.

    * **&lt; ProjectName>rg**: Deployment Manager kaynaklarını içerir.
    * **&lt; ProjectName>ServiceWUSrg**: servicewus tarafından tanımlanan kaynakları içerir.
    * **&lt; ProjectName>ServiceEUSrg**: serviceeus tarafından tanımlanan kaynakları içerir.
    * Kullanıcı tanımlı yönetilen kimlik için kaynak grubu.
1. Kaynak grubu adını seçin.
1. Üstteki menüden **kaynak grubunu sil** ' i seçin.
1. Bu öğretici tarafından oluşturulan diğer kaynak gruplarını silmek için son iki adımı tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Deployment Manager'ı kullanmayı öğrendiniz. Azure Deployment Manager sistem durumu izlemeyi tümleştirmek için bkz. [öğretici: Azure 'da sistem durumu denetimi kullanma Deployment Manager](./deployment-manager-tutorial-health-check.md).
