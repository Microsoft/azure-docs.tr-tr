---
title: Azure Deployment Manager’ı Resource Manager şablonlarıyla kullanma | Microsoft Docs
description: Azure kaynaklarını dağıtmak için Azure Deployment Manager ile Resource Manager şablonlarını kullanın.
author: mumian
ms.date: 10/10/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6a05e45c5dc60cf26b2fb4f50cb4699c0fde142a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147408"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Öğretici: Azure Deployment Manager’ı Resource Manager şablonlarıyla kullanma (Genel önizleme)

[Azure Deployment Manager](./deployment-manager-overview.md)’ı kullanarak uygulamalarınızı birden çok bölgede nasıl dağıtacağınızı öğrenin. Daha hızlı bir yaklaşım tercih ediyorsanız, [Azure dağıtım Yöneticisi hızlı başlangıç](https://github.com/Azure-Samples/adm-quickstart) aboneliğinizde gerekli konfigürasyonları oluşturur ve bir uygulamayı birden çok bölgede dağıtmak için yapıtları özelleştirir. Hızlı başlangıç, bu öğreticide olduğu gibi aynı görevleri gerçekleştirir.

Dağıtım Yöneticisi kullanmak için iki şablon oluşturmanız gerekir:

* **Topoloji şablonu**: Uygulamalarınızı Azure kaynaklarını ve bunların dağıtılacağı yeri açıklar.
* **Dağıtım şablonu**: Uygulamalarınızı dağıtırken uygulanacak adımları açıklar.

> [!IMPORTANT]
> Aboneliğiniz yeni Azure özelliklerini test etmek üzere işaretlenmişse, Azure Dağıtım Yöneticisi 'yi yalnızca Canary bölgelerine dağıtmak için kullanabilirsiniz. 

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

* [Azure Dağıtım Yöneticisi REST API başvurusu](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Öğretici: Azure dağıtım Yöneticisi 'da sistem durumu denetimi kullanın](./deployment-manager-tutorial-health-check.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* [Azure Resource Manager şablonlarını](./resource-group-overview.md) geliştirme konusunda deneyim.
* Azure PowerShell. Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya başlayın](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Deployment Manager cmdlet'leri. Bu yayın öncesi cmdlet’leri yüklemek için PowerShellGet’in en son sürümü gereklidir. En son sürümü edinmek için bkz. [PowerShellGet’i Yükleme](/powershell/gallery/installing-psget). PowerShellGet’i yükledikten sonra PowerShell penceresini kapatın. Yeni bir yükseltilmiş PowerShell penceresi açın ve aşağıdaki komutu kullanın:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Senaryoyu anlama

Hizmet topolojisi şablonu, hizmetinizi oluşturan Azure kaynaklarını ve bunların dağıtılacağı yeri açıklar. Hizmet topolojisi tanımı şu hiyerarşiye sahiptir:

* Hizmet topolojisi
  * Hizmetler
    * Hizmet birimleri

Aşağıdaki diyagramda, bu öğreticide kullanılan hizmet topolojisi gösterilmektedir:

![Azure Deployment Manager öğreticisi senaryo diyagramı](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Batı ABD ve doğu ABD konumlarında ayrılmış iki hizmet vardır.  Her hizmetin iki hizmet birimi vardır: web uygulaması ön ucu ve arka uç için depolama hesabı. Hizmet birimi tanımları, web uygulamaları ve depolama hesapları oluşturmaya yönelik şablon ve parametre dosyalarının bağlantıları içerir.

## <a name="download-the-tutorial-files"></a>Öğretici dosyalarını indirme

1. Bu öğreticide kullanılan [şablonları ve yapıtları](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) indirin.
2. Dosyaları bilgisayarınızdaki konuma ayıklayın.

Kök klasörde iki klasör vardır:

* **ADMTemplates**: burada, aşağıdakileri içeren Deployment Manager şablonları yer alır:
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore**: hem şablon yapıtlarını hem de ikili dosya yapıtlarını içerir. [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.

İki şablon kümesi olduğuna dikkat edin.  Kümelerin biri, hizmet topolojisini ve piyasaya çıkarmayı dağıtmak için kullanılan Deployment Manager şablonlarıdır. Diğer küme ise web hizmetlerini ve depolama hesaplarını oluşturmak için hizmetten çağrılır.

## <a name="prepare-the-artifacts"></a>Yapıtları hazırlama

İndirilen ArtifactStore klasörü iki klasör içerir:

![Azure Deployment Manager öğreticisi yapıt kaynağı diyagramı](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* **Şablonlar** klasörü: şablon yapıtlarını içerir. **1.0.0.0** ve **1.0.0.1** ikili dosya yapıtlarının iki sürümünü temsil eder. Her sürümde her bir hizmete yönelik bir klasör vardır (Hizmet Doğu ABD ve Hizmet Batı ABD). Her hizmette depolama hesabı oluşturmak için bir şablon çifti ve web uygulaması oluşturmak için başka bir şablon çifti vardır. Web uygulaması şablonu, web uygulaması dosyalarını içeren sıkıştırılmış bir paket çağırır. Sıkıştırılmış dosya, ikili dosyalar klasöründe depolanan bir ikili dosya yapıtıdır.
* **İkili dosyalar** klasörü: ikili dosya yapıtları içerir. **1.0.0.0** ve **1.0.0.1** ikili dosya yapıtlarının iki sürümünü temsil eder. Her sürümde, web uygulamasını Batı ABD konumunda oluşturmaya yönelik bir zip dosyası ve web uygulamasını Doğu ABD konumunda oluşturmaya yönelik başka bir zip dosyası vardır.

İki sürüm (1.0.0.0 ve 1.0.0.1) [sürüm dağıtımı](#deploy-the-revision) içindir. Şablon yapıtları ve ikili dosya yapıtlarının iki sürümü olsa da, iki sürüm arasında yalnızca ikili dosya yapıtları farklıdır. Gerçekte, ikili dosya yapıtları şablon yapıtlarına göre daha sık güncelleştirilir.

1. **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** dosyasını bir metin düzenleyicisinde açın. Depolama hesabı oluşturmaya yönelik temel bir şablondur.
2. **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json** dosyasını açın.

    ![Azure Deployment Manager öğreticisi web uygulaması şablonu oluşturma](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Şablon, web uygulamasının dosyalarını içeren bir dağıtım paketi çağırır. Bu öğreticide, sıkıştırılmış paket yalnızca bir index. html dosyası içerir.
3. **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json** dosyasını açın.

    ![Azure Deployment Manager öğreticisi web uygulaması şablonu parametreleri containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    deployPackageUri parametresinin değeri dağıtım paketinin yoludur. Parametre bir **$containerRoot** değişkeni içerir. $containerRoot değeri, [piyasaya çıkarma şablonunda](#create-the-rollout-template) yapıt kaynağı SAS konumu, yapıt kökü ve deployPackageUri birleştirilerek sağlanır.
4. **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html** dosyasını açın.

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

    Html dosyasında konum ve sürüm bilgileri gösterilir. 1\.0.0.1 klasöründeki ikili dosyada "Sürüm 1.0.0.1" gösterilir. Hizmeti dağıttıktan sonra bu sayfalara göz atabilirsiniz.
5. Diğer yapıt dosyalarını inceleyin. Senaryoyu daha iyi anlamanıza yardımcı olur.

Şablon yapıtları hizmet topolojisi şablonu tarafından kullanılır ve ikili dosya yapıtları piyasaya çıkarma şablonu tarafından kullanılır. Topoloji şablonu ve piyasaya çıkarma şablonu bir yapıt kaynağı Azure kaynağı tanımlar. Bu kaynak, Resource Manager’ı dağıtımda kullanılan şablona ve ikili dosya yapıtlarına yönlendirmek için kullanılır. Öğreticiyi basitleştirmek amacıyla, hem şablon yapıtları hem de ikili dosya yapıtlarını depolamak için bir depolama hesabı kullanılmıştır. Her iki yapıt kaynağı aynı depolama hesabını işaret eder.

Bir kaynak grubu oluşturmak, bir depolama kapsayıcısı oluşturmak, bir blob kapsayıcısı oluşturmak, indirilen dosyaları karşıya yüklemek ve ardından bir SAS belirteci oluşturmak için aşağıdaki PowerShell betiğini çalıştırın.

> [!IMPORTANT]
> PowerShell betiğinin **ProjectName** , bu öğreticide dağıtılan Azure hizmetlerinin adlarını oluşturmak için kullanılır. Farklı Azure Hizmetleri, adlar üzerinde farklı gereksinimlere sahiptir. Dağıtımın başarılı olduğundan emin olmak için, yalnızca küçük harf ve rakam içeren 12 karakterden daha az bir ad seçin.
> Proje adının bir kopyasını kaydedin. Öğreticide aynı projectName 'yi kullanırsınız.

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

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)  -Protocol HttpsOrHttp

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

SAS belirteciyle URL 'nin bir kopyasını oluşturun. İki parametre dosyasındaki bir alanı, topoloji parametreleri dosyasını ve dağıtım parametreleri dosyasını doldurmak için bu URL gereklidir.

Kapsayıcıyı Azure portal açın ve **ikili** dosyaların ve **şablonların** klasörlerinin ve dosyaların yüklendiğini doğrulayın.

## <a name="create-the-user-assigned-managed-identity"></a>Kullanıcı tarafından atanmış yönetilen kimliği oluşturma

Öğreticinin sonraki bölümlerinde bir piyasaya çıkarma dağıtacaksınız. Dağıtım eylemlerini gerçekleştirmek (örneğin web uygulamalarını ve depolama hesabını dağıtma) için kullanıcı tarafından atanmış bir yönetilen kimlik gereklidir. Bu kimliğe, hizmeti dağıttığınız Azure aboneliğine erişim izni verilmelidir ve kimliğin yapıt dağıtımını tamamlamak için yeterli izni olmalıdır.

Kullanıcı tarafından atanmış yönetilen bir kimlik oluşturmanız ve aboneliğiniz için erişim denetimini yapılandırmanız gerekir.

> [!IMPORTANT]
> Kullanıcı tarafından atanmış yönetilen kimlik, [piyasaya çıkarma](#create-the-rollout-template) ile aynı konumda olmalıdır. Şu anda piyasaya çıkarma gibi Deployment Manager kaynakları yalnızca Orta ABD veya Doğu ABD 2’de oluşturulabilir. Ancak, bu yalnızca Dağıtım Yöneticisi kaynaklar (hizmet topolojisi, hizmetler, hizmet birimleri, dağıtım ve adımlar gibi) için geçerlidir. Hedef kaynaklarınız, desteklenen herhangi bir Azure bölgesine dağıtılabilir. Bu öğreticide, örneğin Dağıtım Yöneticisi Kaynakları Orta ABD dağıtılır, ancak hizmetler Doğu ABD ve Batı ABD dağıtılır. Bu kısıtlama gelecekte yükseltilmemiş olacaktır.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. [Kullanıcı tarafından atanmış bir yönetilen kimlik](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) oluşturun.
3. Portalda sol menüden **Abonelikler**’i ve ardından aboneliğinizi seçin.
4. **Erişim denetimi (IAM)** öğesini seçin ve ardından **rol ataması Ekle**' yi seçin.
5. Aşağıdaki değerleri yazın veya seçin:

    ![Azure Deployment Manager öğreticisi kullanıcı tarafından atanmış yönetilen kimlik erişim denetimi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Rol**: yapıt dağıtımını (web uygulamaları ve depolama hesapları) tamamlamak için yeterli izinleri verin. Bu öğreticideki **Katkıda Bulunanı** seçin. Gerçekte, izinleri asgari seviyeyle sınırlamak istersiniz.
    * **Erişimi atama hedefi**: **Kullanıcı Tarafından Atanmış Yönetilen Kimlik** öğesini seçin.
    * Öğreticide daha önce oluşturduğunuz kullanıcı tarafından atanmış yönetilen kimliği seçin.
6. **Kaydet**’i seçin.

## <a name="create-the-service-topology-template"></a>Hizmet topolojisi şablonunu oluşturma

**\ADMTemplates\CreateADMServiceTopology.json** dosyasını açın.

### <a name="the-parameters"></a>Parametreler

Şablon aşağıdaki parametreleri içerir:

* **ProjectName**: bu ad Dağıtım Yöneticisi kaynaklarının adlarını oluşturmak için kullanılır. Örneğin, "jtikan" kullanarak hizmet topolojisi adı **jtikan**servicetopology ' dir.  Kaynak adları bu şablonun değişkenler bölümünde tanımlanır.
* **azureResourcelocation**: Öğreticiyi basitleştirmek için, aksi belirtilmedikçe tüm kaynaklar bu konumu paylaşır. Şu anda Azure Deployment Manager kaynakları yalnızca **Orta ABD** veya **Doğu ABD 2**’de oluşturulabilir.
* **artifactSourceSASLocation**: Hizmet birimi şablonu ve parametre dosyalarının dağıtım için depolandığı Blob kapsayıcısının SAS URI’si.  [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
* **templateArtifactRoot**: Şablonların ve parametrelerin depolandığı Blob kapsayıcısına olan yol uzaklığı. Varsayılan değer: **templates/1.0.0.0**. [Yapıtları hazırlama](#prepare-the-artifacts) bölümünde açıklanan klasör yapısını değiştirmek istemiyorsanız bu değeri değiştirmeyin. Bu öğreticide göreli yollar kullanılır.  Tam yol **artifactSourceSASLocation**, **templateArtifactRoot** ve **templateArtifactSourceRelativePath** (veya **parametersArtifactSourceRelativePath**) birleştirilerek oluşturulur.
* **targetSubscriptionID**: Deployment Manager kaynaklarının dağıtılıp faturalandırılacağı abonelik kimliği. Bu öğreticide abonelik kimliğinizi kullanın.

### <a name="the-variables"></a>Değişkenler

Değişkenler bölümü kaynakların adlarını, iki hizmete yönelik Azure konumlarını: **Hizmet WUS** ve **Hizmet EUS**, ayrıca yapıt yollarını tanımlar:

![Azure Deployment Manager öğreticisi topoloji şablonu değişkenleri](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Yapıt yollarını depolama hesabına yüklediğiniz klasör yapısıyla karşılaştırın. Yapıt yollarının göreli yollar olduğuna dikkat edin. Tam yol **artifactSourceSASLocation**, **templateArtifactRoot** ve **templateArtifactSourceRelativePath** (veya **parametersArtifactSourceRelativePath**) birleştirilerek oluşturulur.

### <a name="the-resources"></a>Kaynaklar

Kök düzeyde tanımlı iki kaynak vardır: *yapıt kaynağı* ve *hizmet topolojisi*.

Yapıt kaynağı tanımı:

![Azure Deployment Manager öğreticisi topoloji şablonu kaynakları yapıt kaynağı](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Aşağıdaki ekran görüntüsünde hizmet topolojisinin, hizmetlerin ve hizmet birimi tanımlarının yalnızca bazı bölümleri gösterilmektedir:

![Azure Deployment Manager öğreticisi topoloji şablonu kaynakları hizmet topolojisi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId**, yapıt kaynağını hizmet topolojisi kaynağıyla ilişkilendirmek için kullanılır.
* **dependsOn**: Tüm hizmet topolojisi kaynakları yapıt kaynağına bağlıdır.
* **artifacts** şablon yapıtlarını işaret eder.  Burada göreli yollar kullanılır. Tam yol artifactSourceSASLocation (yapıt kaynağında tanımlıdır), artifactRoot (yapıt kaynağında tanımlıdır) ve templateArtifactSourceRelativePath (veya parametersArtifactSourceRelativePath) birleştirilerek oluşturulur.

### <a name="topology-parameters-file"></a>Topoloji parametre dosyası

Topoloji şablonuyla kullanılan bir parametre dosyası oluşturursunuz.

1. **\ADMTemplates\CreateADMServiceTopology.Parameters** öğesini Visual Studio Code’da veya herhangi bir metin düzenleyicisinde açın.
2. Parametre değerlerini doldurun:

    * **ProjectName**: 4-5 karakter içeren bir dize girin. Bu ad, benzersiz Azure Kaynak adları oluşturmak için kullanılır.
    * **azureResourceLocation**: Azure konumlarını kullanmaya alışık değilseniz, bu öğreticideki **centralus**’u kullanın.
    * **artifactSourceSASLocation**: Hizmet birimi şablonu ve parametreler dosyalarının dağıtım için depolandığı kök dizine (Blob kapsayıcısı) SAS URI’sini girin.  [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
    * **templateArtifactRoot**: Yapıtların klasör yapısını değiştirmediğiniz sürece bu öğreticideki **templates/1.0.0.0** klasörünü kullanın.

> [!IMPORTANT]
> Topoloji şablonu ve piyasaya çıkarma şablonu bazı ortak parametreleri paylaşır. Bu parametreler aynı değerlere sahip olmalıdır. Bu parametreler şunlardır: **ProjectName**, **AzureResourceLocation**ve **artifactsourcesaslocation** (her iki yapıt kaynağı da bu öğreticide aynı depolama hesabını paylaşır).

## <a name="create-the-rollout-template"></a>Piyasaya çıkarma şablonunu oluşturma

**\ADMTemplates\CreateADMRollout.json** dosyasını açın.

### <a name="the-parameters"></a>Parametreler

Şablon aşağıdaki parametreleri içerir:

![Azure Deployment Manager öğreticisi piyasaya çıkarma şablonu parametreleri](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **ProjectName**: bu ad Dağıtım Yöneticisi kaynaklarının adlarını oluşturmak için kullanılır. Örneğin, "jtikan" ile dağıtım adı, **jtikan**dağıtımı olur.  Adlar şablonun değişkenler bölümünde tanımlanır.
* **azureResourcelocation**: Öğreticiyi basitleştirmek için tüm Deployment Manager kaynakları aksi belirtilmedikçe bu konumu paylaşır. Şu anda Azure Deployment Manager kaynakları yalnızca **Orta ABD** veya **Doğu ABD 2**’de oluşturulabilir.
* **artifactSourceSASLocation**: Hizmet birimi şablonu ve parametreler dosyalarının dağıtım için depolandığı kök dizin (Blob kapsayıcısı) SAS URI’si.  [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
* **binaryArtifactRoot**:  Varsayılan değer: **binaries/1.0.0.0**. [Yapıtları hazırlama](#prepare-the-artifacts) bölümünde açıklanan klasör yapısını değiştirmek istemiyorsanız bu değeri değiştirmeyin. Bu öğreticide göreli yollar kullanılır.  Tam yol, CreateWebApplicationParameters.json dosyasında belirtilen **artifactSourceSASLocation**, **binaryArtifactRoot** ve **deployPackageUri** birleştirilerek oluşturulur.  [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
* **managedIdentityID**: Dağıtım işlemlerini gerçekleştiren, kullanıcı tarafından atanmış yönetilen kimlik. Bkz. [Kullanıcı tarafından atanmış yönetilen kimlik oluşturma](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Değişkenler

Değişkenler bölümü kaynakların adlarını tanımlar. Hizmet topolojisi adının, hizmet adlarının ve hizmet birimi adlarının [topoloji şablonunda](#create-the-service-topology-template) tanımlı adlarla eşleştiğinden emin olun.

![Azure Deployment Manager öğreticisi piyasaya çıkarma şablonu değişkenleri](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Kaynaklar

Kök düzeyde tanımlı üç kaynak vardır: yapıt kaynağı, adım ve piyasaya çıkarma.

Yapıt kaynağı tanımı, topoloji şablonunda tanımlı olanla aynıdır.  Daha fazla bilgi için bkz. [Hizmet topolojisi şablonunu oluşturma](#create-the-service-topology-template).

Aşağıdaki ekran görüntüsünde bekleme adımı tanımı gösterilmektedir:

![Azure Deployment Manager öğreticisi piyasaya çıkarma şablonu kaynakları beklemede adımı](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Süre [ISO 8601 standardını](https://en.wikipedia.org/wiki/ISO_8601#Durations) kullanmaktadır. **PT1M** (büyük harf zorunludur) 1 dakikalık bekleme süresine örnektir.

Aşağıdaki ekran görüntüsünde piyasaya çıkarma tanımının yalnızca bazı bölümleri gösterilmektedir:

![Azure Deployment Manager öğreticisi piyasaya çıkarma şablonu kaynakları piyasaya çıkarma](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**: Piyasaya çıkarma kaynağı yapıt kaynağına ve tanımlanan adımlara bağlıdır.
* **artifactSourceId**: yapıt kaynağını piyasaya çıkarma kaynağıyla ilişkilendirmek için kullanılır.
* **targetServiceTopologyId**: hizmet topolojisi kaynağını piyasaya çıkarma kaynağıyla ilişkilendirmek için kullanılır.
* **deploymentTargetId**: Hizmet topolojisi kaynağının hizmet birimi kaynağı kimliğidir.
* **preDeploymentSteps** ve **postDeploymentSteps**: tüm piyasaya çıkarma kaynaklarını içerir. Şablonda bir bekleme adımı çağrılır.
* **dependsOnStepGroups**: adım grupları arasındaki bağımlılıkları yapılandırır.

### <a name="rollout-parameters-file"></a>Piyasaya çıkarma parametre dosyası

Piyasaya çıkarma şablonuyla kullanılan bir parametre dosyası oluşturursunuz.

1. **\ADMTemplates\CreateADMRollout.Parameters** öğesini Visual Studio Code’da veya herhangi bir metin düzenleyicisinde açın.
2. Parametre değerlerini doldurun:

    * **ProjectName**: 4-5 karakter içeren bir dize girin. Bu ad, benzersiz Azure Kaynak adları oluşturmak için kullanılır.
    * **azureResourceLocation**: Şu anda Azure Deployment Manager kaynakları yalnızca **Orta ABD** veya **Doğu ABD 2**’de oluşturulabilir.
    * **artifactSourceSASLocation**: Hizmet birimi şablonu ve parametreler dosyalarının dağıtım için depolandığı kök dizine (Blob kapsayıcısı) SAS URI’sini girin.  [Yapıtları hazırlama](#prepare-the-artifacts) bölümüne bakın.
    * **binaryArtifactRoot**: Yapıtların klasör yapısını değiştirmediğiniz sürece bu öğreticideki **binaries/1.0.0.0** klasörünü kullanın.
    * **managedIdentityID**: Kullanıcı tarafından atanmış yönetilen kimliği girin. Bkz. [Kullanıcı tarafından atanmış yönetilen kimlik oluşturma](#create-the-user-assigned-managed-identity). Sözdizimi şöyledir:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Topoloji şablonu ve piyasaya çıkarma şablonu bazı ortak parametreleri paylaşır. Bu parametreler aynı değerlere sahip olmalıdır. Bu parametreler şunlardır: **ProjectName**, **AzureResourceLocation**ve **artifactsourcesaslocation** (her iki yapıt kaynağı da bu öğreticide aynı depolama hesabını paylaşır).

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

    Bu betiği, [yapıtları hazırlama](#prepare-the-artifacts) betiğini çalıştırmanızdan farklı bir PowerShell oturumundan çalıştırırsanız, **$resourceGroupName** ve **$FilePath**dahil olmak üzere ilk olarak değişkenleri yeniden doldurmanız gerekir.

    > [!NOTE]
    > `New-AzResourceGroupDeployment` zaman uyumsuz bir çağrıdır. Başarı iletisi yalnızca dağıtımın başarıyla başladığını gösterir. Dağıtımı doğrulamak için, bu yordamın adım 2 ve adım 4 ' e bakın.

2. Azure portalı kullanarak hizmet topolojisinin ve temel kaynakların başarıyla oluşturulduğunu doğrulayın:

    ![Azure Deployment Manager öğreticisi dağıtılan hizmet topolojisi kaynakları](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Kaynakları görmek için **Gizli türleri göster** seçeneği belirlenmelidir.

3. <a id="deploy-the-rollout-template"></a>Dağıtım şablonunu dağıtma:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Aşağıdaki PowerShell betiğini kullanarak piyasaya çıkarma ilerleme durumunu kontrol edin:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Bu cmdlet'in çalıştırılabilmesi için Deployment Manager PowerShell cmdlet'lerinin yüklü olması gerekir. Önkoşulları inceleyin. Tüm çıktıyı görmek için-verbose anahtarı kullanılabilir.

    Aşağıdaki örnekte çalışma durumu gösterilmektedir:

    ```
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

    Piyasaya çıkarma başarıyla dağıtıldıktan sonra, her hizmet için bir adet olmak üzere iki kaynak grubunun daha oluşturulduğunu göreceksiniz.

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

1. [Azure portalı](https://portal.azure.com) açın.
2. Piyasaya çıkarma dağıtımı tarafından oluşturulan yeni kaynak gruplarındaki yeni oluşturulan web uygulamalarına gidin.
3. Web uygulamasını bir web tarayıcıda açın. Index.html dosyasında konumu ve sürümü doğrulayın.

## <a name="deploy-the-revision"></a>Düzeltmeyi dağıtma

Web uygulamasının yeni bir sürümüne (1.0.0.1) sahip olduğunuzda. Web uygulamasını yeniden dağıtmak için aşağıdaki yordamı kullanabilirsiniz.

1. CreateADMRollout.Parameters.json dosyasını açın.
2. **binaryArtifactRoot** öğesini **binaries/1.0.0.1** olarak güncelleştirin.
3. [Şablonları dağıtma](#deploy-the-rollout-template) bölümünde anlatıldığı gibi piyasaya çıkarmayı yeniden dağıtın.
4. [Dağıtımı doğrulama](#verify-the-deployment) bölümünde anlatıldığı gibi dağıtımı doğrulayın. Web sayfası 1.0.0.1 sürümünü gösterir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. Bu öğreticide oluşturulan kaynak gruplarını daraltmak için **Ada göre filtrele** alanını kullanın. 3-4 adet olacaktır:

    * **&lt;projectName > RG**: Dağıtım Yöneticisi kaynaklarını içerir.
    * **&lt;projectName > ServiceWUSrg**: ServiceWUS tarafından tanımlanan kaynakları içerir.
    * **&lt;projectName > ServiceEUSrg**: ServiceEUS tarafından tanımlanan kaynakları içerir.
    * Kullanıcı tanımlı yönetilen kimlik için kaynak grubu.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.
5. Bu öğretici tarafından oluşturulan diğer kaynak gruplarını silmek için son iki adımı tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Deployment Manager'ı kullanmayı öğrendiniz. Azure Dağıtım Yöneticisi sistem durumu izlemeyi tümleştirmek için bkz. [öğretici: Azure 'da sistem durumu denetimi kullanma dağıtım Yöneticisi](./deployment-manager-tutorial-health-check.md).
