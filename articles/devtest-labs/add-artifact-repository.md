---
title: Azure DevTest Labs | laboratuvarınızda bir yapıt deposu ekleyin | Microsoft Docs
description: Ortak yapıt deposunda kullanılabilir olmayan depolama araçlarını Azure DevTest Labs ' de laboratuvarınız için kendi yapıt deponuzu belirtmeyi öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da4e345b18a46226853d71bbf66af0487f1a761f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502204"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>DevTest Labs 'de laboratuvarınızda yapıt deposu ekleme
DevTest Labs, VM oluşturma sırasında veya VM oluşturulduktan sonra VM 'ye eklenecek bir yapıt belirtmenize olanak tanır. Bu yapıt, VM 'ye yüklemek istediğiniz bir araç veya uygulama olabilir. Yapıtlar, GitHub veya Azure DevOps git deposundan yüklenen bir JSON dosyasında tanımlanır.

DevTest Labs tarafından tutulan [ortak yapıt deposu](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), hem Windows hem de Linux için birçok ortak araç sağlar. Bu depoya yönelik bir bağlantı, laboratuvarınızda otomatik olarak eklenir. Ortak yapıt deposunda kullanılamayan belirli araçlarla kendi yapıt deponuzu oluşturabilirsiniz. Özel yapıtlar oluşturma hakkında bilgi edinmek için bkz. [özel yapılar oluşturma](devtest-lab-artifact-author.md).

Bu makalede, Azure portal, Azure Kaynak Yönetimi şablonlarını ve Azure PowerShell kullanarak özel yapıt deponuzu ekleme hakkında bilgi sağlanır. PowerShell veya CLı betikleri yazarak bir laboratuvara yapıt deposu eklemeyi otomatik hale getirebilirsiniz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar
Laboratuvarınıza bir depo eklemek için önce deponuzdan anahtar bilgileri alın. Aşağıdaki bölümlerde **GitHub** veya **Azure DevOps** üzerinde barındırılan depolarda gerekli bilgilerin nasıl alınacağı açıklanır.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub deposu kopya URL 'sini ve kişisel erişim belirtecini al

1. Yapıt veya Kaynak Yöneticisi Şablon tanımlarını içeren GitHub deposunun ana sayfasına gidin.
2. **Clone or download**'u (Kopyala veya indir) seçin.
3. URL 'YI panoya kopyalamak için **https kopya URL 'si** düğmesini seçin. URL 'YI daha sonra kullanmak üzere kaydedin.
4. GitHub ' ın sağ üst köşesinde profil görüntüsünü seçin ve ardından **Ayarlar**' ı seçin.
5. Soldaki **kişisel ayarlar** menüsünde, **Geliştirici ayarları**' nı seçin.
6. Sol taraftaki menüden **kişisel erişim belirteçleri** ' ni seçin.
7. **Yeni belirteç oluştur**' u seçin.
8. **Yeni kişisel erişim belirteci** sayfasında, **belirteç açıklaması** altında bir açıklama girin. **Kapsamları Seç** altında varsayılan öğeleri kabul edin ve ardından **belirteç oluştur**' u seçin.
9. Oluşturulan belirteci kaydedin. Belirteci daha sonra kullanırsınız.
10. GitHub 'ı kapatın.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Azure Repos kopya URL 'sini ve kişisel erişim belirtecini alın
1. Ekip koleksiyonunuzun ana sayfasına gidin (örneğin, `https://contoso-web-team.visualstudio.com` ) ve ardından projenizi seçin.
2. Proje giriş sayfasında **kod**' u seçin.
3. Kopya URL 'sini görüntülemek için, proje **kodu** sayfasında, **Kopyala**' yı seçin.
4. URL 'YI kaydedin. URL 'YI daha sonra kullanırsınız.
5. Kişisel erişim belirteci oluşturmak için, Kullanıcı hesabı açılan menüsünde **profilimi** seçin.
6. Profil bilgileri sayfasında **güvenlik**' i seçin.
7. **Güvenlik > kişisel erişim belirteçleri** sekmesinde **+ Yeni belirteç**' ı seçin.
8. **Yeni kişisel erişim belirteci oluştur** sayfasında:
   1. Belirteç için bir **ad** girin.
   2. **Kuruluş** listesinde, **tüm erişilebilir kuruluşlar**' ı seçin.
   3. **Süre sonu (UTC)** listesinde, **90 gün** veya özel bir tanımlı süre sonu dönemi ' ni seçin.
   4. Kapsamlar için **tam erişim** seçeneğini belirleyin.
   5. **Oluştur**’u seçin.
9. Yeni belirteç, **kişisel erişim belirteçleri** listesinde görünür. **Belirteci Kopyala**' yı seçin ve ardından daha sonra kullanmak üzere belirteç değerini kaydedin.
10. Laboratuvarınızı depoya bağlama bölümüne devam edin.

## <a name="use-azure-portal"></a>Azure portalı kullanma
Bu bölümde, Azure portal bir laboratuvara yapıt deposu ekleme adımları sağlanmaktadır.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Diğer hizmetler**' i seçin ve ardından hizmetler listesinden **DevTest Labs** ' i seçin.
3. Laboratuvarlar listesinden laboratuvarınızı seçin.
4. Sol taraftaki menüden **yapılandırma ve ilkeler** ' i seçin.
5. Sol taraftaki menüde **dış kaynaklar** bölümünde bulunan **depolar** ' ı seçin.
6. Araç çubuğunda **+ Ekle** ' yi seçin.

    ![Depo Ekle düğmesi](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. **Depolar** sayfasında, aşağıdaki bilgileri belirtin:
   1. **Ad**. Depo için bir ad girin.
   2. **Url git clone**. Daha önce GitHub veya Azure DevOps Services kopyaladığınız git HTTPS kopya URL 'sini girin.
   3. **Dalı**. Tanımlarınızı almak için dalı girin.
   4. **Kişisel erişim belirteci**. Daha önce GitHub veya Azure DevOps Services aldığınız kişisel erişim belirtecini girin.
   5. **Klasör yolları**. Yapıtı veya Kaynak Yöneticisi şablonu tanımlarınızı içeren kopya URL 'sine göre en az bir klasör yolu girin. Bir alt dizin belirttiğinizde, klasör yolunda eğik çizgi eklediğinizden emin olun.

        ![Depolar alanı](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. **Kaydet**’i seçin.

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullan
Azure Kaynak Yönetimi (Azure Resource Manager) şablonları, Azure 'da oluşturmak istediğiniz kaynakları tanımlayan JSON dosyalarıdır. Bu şablonlar hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma](../azure-resource-manager/templates/template-syntax.md).

Bu bölümde, bir Azure Resource Manager şablonu kullanarak bir laboratuvara yapıt deposu ekleme adımları sağlanmaktadır.  Şablon zaten yoksa Laboratuvarı oluşturur.

### <a name="template"></a>Şablon
Bu makalede kullanılan örnek şablon, parametreler aracılığıyla aşağıdaki bilgileri toplar. Parametrelerin çoğunda akıllı varsayılanlar vardır, ancak belirtilmesi gereken birkaç değer vardır. Laboratuvar adını, yapıt deposunun URI 'sini ve Deponun güvenlik belirtecini belirtmeniz gerekir.

- Laboratuvar adı.
- Yapı deposunun DevTest Labs Kullanıcı arabiriminde (UI) görünen adı. Varsayılan değer: `Team Repository` .
- Depoya URI (örnek: `https://github.com/<myteam>/<nameofrepo>.git` veya `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .
- Yapıtları içeren depodaki dal. Varsayılan değer: `master` .
- Yapıtları içeren klasörün adı. Varsayılan değer: `/Artifacts` .
- Deponun türü. İzin verilen değerler `VsoGit` veya `GitHub` .
- Depo için erişim belirteci.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Şablonu dağıtma
Şablonu Azure 'a dağıtmanın birkaç yolu vardır ve kaynak yoksa, varsa, bu kaynağı oluşturmuş veya güncelleştirilemez. Ayrıntılar için aşağıdaki makalelere bakın:

- [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../azure-resource-manager/templates/deploy-powershell.md)
- [Kaynakları Resource Manager şablonları ve Azure CLI ile dağıtma](../azure-resource-manager/templates/deploy-cli.md)
- [Kaynakları Resource Manager şablonları ve Azure portalı ile dağıtma](../azure-resource-manager/templates/deploy-portal.md)
- [Kaynakları Resource Manager şablonları ve Resource Manager REST API’si ile dağıtma](../azure-resource-manager/templates/deploy-rest.md)

Şimdi de bir şablonu PowerShell 'de dağıtmayı görelim. Şablonu dağıtmak için kullanılan cmdlet 'ler içeriğe özgüdür, bu nedenle geçerli kiracı ve geçerli abonelik kullanılır. Gerekirse, bağlamı değiştirmek için şablonu dağıtılmadan önce [set-AzContext](/powershell/module/az.accounts/set-azcontext) komutunu kullanın.

İlk olarak, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)kullanarak bir kaynak grubu oluşturun. Kullanmak istediğiniz kaynak grubu zaten varsa, bu adımı atlayın.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Ardından, [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)kullanarak kaynak grubuna bir dağıtım oluşturun. Bu cmdlet, kaynak değişikliklerini Azure 'a uygular. Belirli bir kaynak grubuna birkaç kaynak dağıtımı yapılabilir. Aynı kaynak grubuna birkaç kez dağıtıyorsanız, her dağıtımın adının benzersiz olduğundan emin olun.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

New-AzResourceGroupDeployment başarılı bir şekilde çalıştıktan sonra, komut sağlama durumu (başarılı olmalıdır) ve şablon için tüm çıktılar gibi önemli bilgileri çıktı olarak verir.

## <a name="use-azure-powershell"></a>Azure PowerShell kullanma
Bu bölüm, bir laboratuvara yapıt deposu eklemek için kullanılabilecek bir örnek PowerShell betiği sağlar. Azure PowerShell yoksa, yüklemek için ayrıntılı yönergeler için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/) .

### <a name="full-script"></a>Tam betik
Bazı ayrıntılı iletiler ve açıklamalar dahil olmak üzere tam komut dosyası aşağıda verilmiştir:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the repository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>PowerShell betiğini çalıştırma
Aşağıdaki örnek, komut dosyasının nasıl çalıştırılacağını göstermektedir:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parametreler
Bu makaledeki örnek PowerShell betiği aşağıdaki parametreleri alır:

| Parametre | Açıklama |
| --------- | ----------- |
| LabName | Laboratuvarın adı. |
| ArtifactRepositoryName | Yeni yapıt deposunun adı. Komut dosyası belirtilmemişse depo için rastgele bir ad oluşturur. |
| ArtifactRepositoryDisplayName | Yapıt deposunun görünen adı. Bu, Azure portal gösteren addır ( https://portal.azure.com) bir laboratuvarın tüm yapıt depoları görüntülenirken). |
| Depotoruri | Depoya URI. Örnekler: `https://github.com/<myteam>/<nameofrepo>.git` veya `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .|
| Depodalı | Yapıt dosyalarının bulunduğu dal. Varsayılan olarak ' Master ' olur. |
| FolderPath | Yapıtların bulunduğu klasör. Varsayılan değer '/yapıtlar ' |
| PersonalAccessToken | GitHub veya VSOGit deposuna erişmek için güvenlik belirteci. Kişisel erişim belirteci almak için yönergeler için Önkoşullar bölümüne bakın |
| KaynakTürü | Yapıtın VSOGit veya GitHub deposu olup olmadığı. |

Deponun, kimlik için bir iç ada ihtiyacı vardır ve bu, Azure portal görülen görünen ada göre farklılık gösterir. Azure portal kullanarak dahili adı görmezsiniz, ancak Azure REST API 'Lerini veya Azure PowerShell kullanırken bunu görürsünüz. Betik, betiğimizin kullanıcısı tarafından belirtilmemişse, bir ad sağlar.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Betikte kullanılan PowerShell komutları

| PowerShell komutu | Notlar |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Bu komut, kendi konumu gibi laboratuvarın ayrıntılarını almak için kullanılır. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Yapıt depoları eklemek için belirli bir komut yoktur. Genel [New-AzResource](/powershell/module/az.resources/new-azresource) cmdlet 'i işi yapar. Bu cmdlet 'in oluşturulacak kaynak türünü bilmemiz için **RESOURCEID** veya **resourceName** ve **ResourceType** çiftine ihtiyacı vardır. Bu örnek betik, kaynak adı ve kaynak türü çiftini kullanır. <br/><br/>Yapıt depo kaynağını aynı konumda ve laboratuvarla aynı kaynak grubunda oluştururuz olun.|

Betik geçerli aboneliğe yeni bir kaynak ekler. Bu bilgileri görmek için [Get-AzContext](/powershell/module/az.accounts/get-azcontext) kullanın. Geçerli kiracıyı ve aboneliği ayarlamak için [set-AzContext](/powershell/module/az.accounts/set-azcontext) komutunu kullanın.

Kaynak adı ve kaynak türü bilgilerini öğrenmenin en iyi yolu, [Azure REST API 'leri Için test sürücüsü](https://azure.github.io/projects/apis/) Web sitesini kullanmaktır. DevTest Labs sağlayıcısı için kullanılabilir REST API 'Leri görmek için [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) sağlayıcısına göz atın. Komut dosyası, aşağıdaki kaynak KIMLIĞINI kullanıcılarına göre yapılır.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Kaynak türü, küme ayraçları içinde listelenen öğeler hariç, URI 'de ' Providers ' öğesinden sonra listelenen her şeydir. Kaynak adı, küme ayraçları içinde görülen her şeydir. Kaynak adı için birden fazla öğe bekleniyorsa, yaptığımız gibi her bir öğeyi eğik çizgiyle ayırın.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Sonraki adımlar
- [Azure DevTest Labs ' de laboratuvarınız için zorunlu yapıtlar belirtin](devtest-lab-mandatory-artifacts.md)
- [DevTest Labs sanal makineniz için özel yapılar oluşturma](devtest-lab-artifact-author.md)
- [Laboratuvardaki yapıt başarısızlıklarını tanılama](devtest-lab-troubleshoot-artifact-failure.md)
