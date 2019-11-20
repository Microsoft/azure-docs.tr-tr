---
title: Azure Resource Manager şablonlarıyla çoklu VM ortamları ve PaaS kaynakları oluşturma | Microsoft Docs
description: Azure Resource Manager şablonundan Azure DevTest Labs çoklu VM ortamları ve PaaS kaynakları oluşturmayı öğrenin
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2019
ms.author: spelluru
ms.openlocfilehash: 51c699f9b392be5f2e2bc16b5729d6567ace7f17
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016183"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarıyla çoklu VM ortamları ve PaaS kaynakları oluşturma

Azure DevTest Labs ortamlar, kullanıcıların karmaşık altyapıları laboratuvarın bir yolu dahilinde tutarlı bir şekilde dağıtmasını sağlar. DevTest Labs içindeki kaynak kümelerine sahip ortamlar oluşturmak için [Azure Resource Manager şablonlarını](../azure-resource-manager/resource-group-authoring-templates.md) kullanabilirsiniz. Bu ortamlar, Kaynak Yöneticisi şablonlarının oluşturabileceğiniz tüm Azure kaynaklarını içerebilir. 

[Azure Portal](https://portal.azure.com)kullanarak bir laboratuvara tek seferde bir [sanal makineyi (VM) kolayca ekleyebilirsiniz](devtest-lab-add-vm.md) . Ancak, çok katmanlı Web uygulamalarına veya SharePoint grubuna benzer senaryolar tek bir adımda birden çok VM oluşturmak için bir mekanizmaya ihtiyaç duyar. Azure Resource Manager şablonları kullanarak Azure çözümünüzün altyapısını ve yapılandırmasını tanımlayabilir ve tutarlı bir durumda birden çok VM 'yi sürekli olarak dağıtabilirsiniz. 

Azure Resource Manager şablonlar aşağıdaki avantajları da sağlar:

- Azure Resource Manager şablonlar doğrudan GitHub veya Azure Repos kaynak denetimi deposundan yüklenir.
- Kullanıcılarınız, Azure portal Azure Resource Manager bir şablonu, diğer türlerdeki [VM tabanlarında](devtest-lab-comparing-vm-base-image-types.md)olduğu gibi seçerek bir ortam oluşturabilir.
- Azure Resource Manager şablondan bir ortamda Azure PaaS kaynaklarını ve IaaS VM 'lerini sağlayabilirsiniz.
- Diğer temel türler tarafından oluşturulan ayrı VM 'lere ek olarak laboratuvardaki ortamların maliyetini izleyebilirsiniz. PaaS kaynakları oluşturulur ve maliyet izlemede görüntülenir. Ancak, sanal makine otomatik kapatması PaaS kaynakları için de geçerlidir.

Tek bir işlemde birçok laboratuvar kaynağını dağıtmak, güncelleştirmek veya silmek için Kaynak Yöneticisi şablonlarını kullanmanın avantajları hakkında daha fazla bilgi edinmek için, bkz. [Kaynak Yöneticisi şablonlarını kullanmanın avantajları](../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> Laboratuvar VM 'Leri oluşturmak için temel olarak bir Kaynak Yöneticisi şablonu kullandığınızda, birden çok VM veya tek bir VM oluşturma arasında bazı farklılıklar vardır. Daha fazla bilgi için bkz. [sanal makinenin Azure Resource Manager şablonunu kullanma](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>DevTest Labs genel ortamlarını kullanın
Azure DevTest Labs, bir dış GitHub kaynağına kendi kendinize bağlanmak zorunda kalmadan ortamlar oluşturmak için kullanabileceğiniz, [ortak Azure Resource Manager şablonlarının bir deposuna](https://github.com/Azure/azure-devtestlab/tree/master/Environments) sahiptir. Bu ortak depo, oluşturduğunuz her laboratuvar için Azure portal mevcut olan yapıtların genel deposuna benzer. Ortam deposu, az sayıda giriş parametresine sahip olan önceden yazılmış ortam şablonlarını hızlıca kullanmaya başlamanızı sağlar. Bu şablonlar, Labs içinde PaaS kaynakları için sorunsuz bir başlangıç deneyimi sağlar. 

Ortak depoda, DevTest Labs ekibi ve diğer kullanıcılar Azure Web Apps, Service Fabric kümesi ve bir geliştirme SharePoint grubu ortamı gibi sık kullanılan şablonları oluşturup paylaşmıştır. Bu şablonları doğrudan kullanabilir veya gereksinimlerinize uyacak şekilde özelleştirebilirsiniz. Daha fazla bilgi için bkz. [DevTest Labs 'de ortak ortamları yapılandırma ve kullanma](devtest-lab-configure-use-public-environments.md). Kendi şablonlarınızı oluşturduktan sonra, bunları başkalarıyla paylaşmak veya kendi git deponuzu ayarlamak için bu depoda saklayabilirsiniz.

<a name="configure-your-own-template-repositories"></a> 
## <a name="create-your-own-template-repositories"></a>Kendi şablon depolarınızı oluşturun

Kod olarak altyapı ve yapılandırma kodu ile en iyi uygulamalardan biri olarak, kaynak denetimindeki ortam şablonlarını yönetmeniz gerekir. Azure DevTest Labs bu uygulamayı izler ve tüm Azure Resource Manager şablonlarını doğrudan GitHub veya Azure Repos depolarınızdan yükler. Sonuç olarak, tüm yayın döngüsünün üzerinde Kaynak Yöneticisi şablonlarını, test ortamından üretim ortamına kullanabilirsiniz.

Azure Resource Manager şablonlarınızı bir depoda düzenlemek için izlenecek birkaç kural vardır:

- Ana şablon dosyası *azuredeploy. JSON*olarak ayarlanmalıdır. 
  
- Parametre dosyasında tanımlanan parametre değerlerini kullanmak istiyorsanız, parametre dosyası *azuredeploy. Parameters. JSON*olarak adlandırılmalıdır.
  
  Parametreleri `_artifactsLocation` ve`_artifactsLocationSasToken` parameterslink URI değerini oluşturmak için, DevTest Labs 'in iç içe şablonları otomatik olarak yönetmesine izin vermek için kullanabilirsiniz. Daha fazla bilgi için bkz. [test ortamları için iç içe Azure Resource Manager şablonlarını dağıtma](deploy-nested-template-environments.md).
  
- Şablon görünen adını ve açıklamasını, *Metadata. JSON*adlı bir dosyada belirtmek için aşağıdaki gibi meta verileri tanımlayabilirsiniz:
  
  ```json
  { 
    "itemDisplayName": "<your template name>", 
    "description": "<description of the template>" 
  }
  ```

![Anahtar Azure Resource Manager şablon dosyaları](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Laboratuvara şablon depoları ekleme

Deponuzu oluşturup yapılandırdıktan sonra, Azure portal kullanarak laboratuvarınızı ekleyebilirsiniz: 

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin.
1. Laboratuvarlar listesinden istediğiniz Laboratuvarı seçin. 
1. Laboratuvarın **genel bakış** bölmesinde **yapılandırma ve ilkeler**' i seçin.
   
   ![Yapılandırma ve ilkeler](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)
   
1. **Yapılandırma ve ilke** ayarları listesinden **depolar**' ı seçin. **Ortak yapıt** depo deposu tüm laboratuvarlar için otomatik olarak oluşturulur ve [DevTest Labs genel GitHub deposuna](https://github.com/Azure/azure-devtestlab)bağlanır.
   
1. Azure Resource Manager şablon deponuzu eklemek için **Ekle**' yi seçin.
   
   ![Genel depo](./media/devtest-lab-create-environment-from-arm/public-repo.png)
   
1. **Depolar** bölmesinde aşağıdaki bilgileri girin:
   
   - **Ad**: Laboratuvarda kullanmak için bir depo adı girin.
   - **Git kopyası URL 'si**: GitHub 'dan veya Azure Repos git HTTPS kopya URL 'sini girin. 
   - **Dal** (isteğe bağlı): Azure Resource Manager şablonu tanımlarınıza erişmek için dal adını girin.
   - **Kişisel erişim belirteci**: Deponuza güvenli bir şekilde erişmek için kullanılan kişisel erişim belirtecini girin.
     - Azure Repos belirtecinizi almak için, profiliniz altında **Kullanıcı ayarları** > **güvenlik** > **kişisel erişim belirteçleri**' ni seçin.
     - GitHub 'dan belirtecinizi almak için, profiliniz altında **Ayarlar** > **Geliştirici ayarları** > **kişisel erişim belirteçleri**' ni seçin.
   - **Klasör yolları**: Yapıt tanımlarınız veya Azure Resource Manager şablon tanımlarınız için git kopya URI 'siyle ilişkili klasör yolunu girin. 
   
1. **Kaydet**’i seçin.
   
   ![Yeni Depo Ekle](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Laboratuvara bir Azure Resource Manager şablonu ekledikten sonra, laboratuar kullanıcılarınız şablonu kullanarak ortamlar oluşturabilir. 

## <a name="configure-access-rights-for-lab-users"></a>Laboratuvar kullanıcıları için erişim haklarını yapılandırma

Laboratuvar kullanıcıları varsayılan olarak **okuyucu** rolüne sahiptir, bu nedenle bir ortam kaynak grubundaki kaynakları değiştiremezler. Örneğin, kaynaklarını durduramazlar veya başlatamaz. 

Laboratuvar kullanıcılarına **katkıda** bulunan rolü sağlamak için, ortamlarında kaynakları düzenleyebilmeleri için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com), laboratuvarınızın **genel bakış** bölmesinde **yapılandırma ve ilkeler**' i seçin ve ardından **Laboratuvar ayarları**' nı seçin.
   
1. Laboratuvar **ayarları** bölmesinde, **katkıda bulunan**' ı seçin ve ardından Laboratuvar kullanıcılarına yazma izinleri vermek için **Kaydet** ' i seçin.
   
   ![Laboratuvar Kullanıcı erişimi haklarını yapılandırma](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

Sonraki bölümde, bir Azure Resource Manager şablonundan ortam oluşturma adımları anlatılmaktadır.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Azure portal şablonlardan ortam oluşturma

Laboratuvara bir Azure Resource Manager şablonu ekledikten sonra, laboratuar kullanıcılarınız aşağıdaki adımları izleyerek Azure portal ortamlar oluşturabilir:

1. [Azure Portal](https://portal.azure.com) oturum açın.
   
1. **Tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin.
   
1. Laboratuvarlar listesinden istediğiniz Laboratuvarı seçin. 
   
1. Laboratuvarın sayfasında **Ekle**' yi seçin.
   
1. **Bir taban Seç** bölmesi, önce listelenen Azure Resource Manager şablonlar ile kullanabileceğiniz temel görüntüleri görüntüler. İstediğiniz Azure Resource Manager şablonunu seçin.
   
   ![Bir temel seçin](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
   
1. **Ekle** bölmesinde, ortam kullanıcılarına göstermek Için bir **ortam adı** değeri girin. 
   
   Azure Resource Manager şablonu, giriş alanlarının geri kalanını tanımlar. Şablon *azuredeploy. Parameter. JSON* dosyası varsayılan değerleri tanımlıyorsa, giriş alanları bu değerleri gösterir. 
   
   *Güvenli dize*türündeki parametreler için Azure Key Vault gizli dizileri kullanabilirsiniz. Gizli dizileri bir anahtar kasasında depolama ve laboratuvar kaynakları oluştururken kullanma hakkında bilgi edinmek için bkz. [Azure Key Vault gizli dizileri depolama](devtest-lab-store-secrets-in-key-vault.md).  
   
   ![Bölme Ekle](./media/devtest-lab-create-environment-from-arm/add.png)
   
   > [!NOTE]
   > Aşağıdaki parametre değerleri, şablon tarafından belirtse bile giriş alanlarında görünmez. Bunun yerine form, laboratuvar kullanıcılarının ortamı oluştururken değer girmesi gereken boş giriş alanlarını gösterir.
   > 
   > - GENEL-BENZERSIZ
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-ANAHTAR
   > - GEN-PASSWORD 
   
1. Ortamı oluşturmak için **Ekle** ' yi seçin. 
   
   Ortam, sağlama, **sanal makinelerim** listesinde görüntülenen durum ile hemen başlatılır. Laboratuvar, Azure Resource Manager şablonunda tanımlanan tüm kaynakları sağlamak üzere otomatik olarak yeni bir kaynak grubu oluşturur.
   
1. Ortam oluşturulduktan sonra, kaynak grubu bölmesini açmak için **sanal makinelerim** listesinden ortamı seçin ve ortamın sağlandığı tüm kaynaklara gidin.
   
   ![Ortam kaynakları](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Ayrıca ortamı genişleterek yalnızca ortamın sağlandığı VM 'lerin listesini görüntüleyebilirsiniz.
   
   ![Sanal makinelerim listesi](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)
   
1. Yapıları uygulama, veri diskleri iliştirme, otomatik kapatılma süresini değiştirme ve daha fazlası gibi kullanılabilir eylemleri görüntülemek için ortamların herhangi birini seçin.
   
   ![Ortam eylemleri](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a> 
## <a name="automate-environment-creation-with-powershell"></a>PowerShell ile ortam oluşturmayı otomatikleştirin

Laboratuvara tek bir ortam eklemek için Azure portal kullanımı uygulanabilir, ancak geliştirme veya test senaryosu birden çok ortam oluştururken otomatik dağıtım daha iyi bir deneyimdir. 

Devam etmeden önce, oluşturulacak kaynakları tanımlayan bir Azure Resource Manager şablonunuz olduğundan emin olun. [Bir git deposuna şablon ekleyip yapılandırın](#configure-your-own-template-repositories)ve [depoyu laboratuvara ekleyin](#add-template-repositories-to-the-lab).

Aşağıdaki örnek betik laboratuvarınızda bir ortam oluşturur. Açıklamalar, betiği daha iyi anlamanıza yardımcı olur. 

1. Aşağıdaki örnek PowerShell betiğini, *deployenv. ps1*olarak sabit sürücünüze kaydedin. 
  
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   ```powershell
   #Requires -Module Az.Resources
   
   [CmdletBinding()]
   
   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,
   
   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,
   
   # Name of the connected repository in the lab 
   [string] [Parameter(Mandatory=$true)] $RepositoryName,
   
   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,
   
   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,
   
   # The parameters to be passed to the template. Each parameter is prefixed with "-param_". 
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName", 
   # the string in $Params will have the form: -param_TestVMName MyVMName. 
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )
   
   # Sign in to Azure. 
   # Comment out the following statement to completely automate the environment creation. 
   Connect-AzAccount
   
   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
   
   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)
           
   # Get information about the lab, such as lab location. 
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
       
   # Get information about the repository in the lab. 
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 
   
   # Get information about the Resource Manager template base for the environment. 
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 
   
   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()
   
   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }
   
   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 
   
   # Now, create or deploy the environment in the lab by using the New-AzResource command. 
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force 
    
   Write-Output "Environment $EnvironmentName completed."
   ```
   
1. Aşağıdaki gibi, SubscriptionID, LabName, ResourceGroupName, Depoadı, TemplateName (git deposunda klasörü) ve EnvironmentName değerlerini kullanarak betiği çalıştırın.
   
   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv" 
   ```

Ayrıca, Kaynak Yöneticisi şablonlarıyla kaynakları dağıtmak için Azure CLı 'yi de kullanabilirsiniz. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure CLI ile kaynak dağıtma](../azure-resource-manager/resource-group-template-deploy-cli.md).

> [!NOTE]
> Yalnızca laboratuvar sahibi izinleri olan bir Kullanıcı, Azure PowerShell kullanarak Kaynak Yöneticisi şablondan VM 'Ler oluşturabilir. VM oluşturmayı Kaynak Yöneticisi şablonu kullanarak otomatikleştirmek istiyorsanız ve yalnızca kullanıcı izinleriniz varsa, [az Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create)CLI komutunu kullanabilirsiniz.

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>DevTest Labs 'de şablon kısıtlamalarını Kaynak Yöneticisi 

DevTest Labs 'de Kaynak Yöneticisi şablonları kullanırken bu sınırlamaları göz önünde bulundurun:

- Kaynak Yöneticisi şablonlar, en fazla mevcut kaynaklara başvuramaz. Yalnızca yeni kaynaklar oluşturabilir. Mevcut kaynaklara başvuran DevTest Labs dışında kullandığınız Kaynak Yöneticisi şablonunuz varsa, bunları DevTest Labs 'de kullanamazsınız. Tek özel durum, var olan bir sanal ağa başvurkabilmeniz için geçerlidir. 
  
- Kaynak Yöneticisi şablonundan oluşturulan laboratuvar VM 'lerinden formüller veya özel görüntüler oluşturamazsınız. 
  
- Kaynak Yöneticisi şablonlarını dağıtırken çoğu ilke değerlendirilmez.
  
  Örneğin, bir kullanıcının yalnızca beş VM oluşturmakta olabileceği bir laboratuvar ilkenize sahip olabilirsiniz. Ancak, bir Kullanıcı, düzinelerce VM 'Ler oluşturan bir Kaynak Yöneticisi şablonu dağıtabilir. Değerlendirilmeyen ilkeler şunları içerir:
  
  - Kullanıcı başına VM sayısı
    
  - Laboratuvar kullanıcısı başına Premium VM sayısı
    
  - Laboratuvar kullanıcısı başına Premium disk sayısı

## <a name="next-steps"></a>Sonraki adımlar
- Bir VM oluşturduktan sonra VM 'nin Yönetim bölmesinde **Bağlan** ' ı seçerek VM 'ye bağlanabilirsiniz.
- Laboratuvarınızda **sanal makinelerim** listesinden ortamı seçerek bir ortamdaki kaynakları görüntüleyin ve yönetin. 
- [Azure hızlı başlangıç şablonu galerisindeki Azure Resource Manager şablonlarını](https://github.com/Azure/azure-quickstart-templates)keşfet.
