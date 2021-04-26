---
title: PowerShell ile paylaşılan bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma
description: Azure Data Factory ' de paylaşılan bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturmayı öğrenin. böylece, tümleştirme çalışma zamanına birden çok veri fabrikası erişebilir.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 16feeb124859fa6199303d9d590fa0a286033ef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389442"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Azure Data Factory içinde, şirket içinde barındırılan, paylaşılan bir tümleştirme çalışma zamanı oluşturma

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu kılavuzda, Azure Data Factory paylaşılan bir şirket içinde barındırılan tümleştirme çalışma zamanının nasıl oluşturulacağı gösterilmektedir. Daha sonra, paylaşılan şirket içinde barındırılan tümleştirme çalışma zamanını başka bir veri fabrikasında kullanabilirsiniz.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Azure Data Factory içinde, şirket içinde barındırılan, paylaşılan bir tümleştirme çalışma zamanı oluşturma

Zaten bir veri fabrikasında ayarlamış olduğunuz mevcut, şirket içinde barındırılan bir tümleştirme çalışma zamanı altyapısını yeniden kullanabilirsiniz. Bu yeniden kullanım, var olan bir paylaşılan şirket içinde barındırılan IR 'ye başvurarak farklı bir veri fabrikasında bağlı bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturmanıza olanak sağlar.

Bu özelliğin bir giriş ve tanıtım durumunu görmek için aşağıdaki 12 dakikalık videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminoloji

- **PAYLAŞıLAN IR**: fiziksel bir altyapı üzerinde çalışan, özgün bir şirket IÇINDE barındırılan IR.  
- **Bağlı IR**: başka BIR paylaşılan IR 'ye başvuran bir IR. Bağlı IR mantıksal bir IR ve diğer bir paylaşılan şirket içinde barındırılan IR altyapısını kullanır.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Azure Data Factory Kullanıcı arabirimini kullanarak, şirket içinde barındırılan, paylaşılan bir IR oluşturma

Azure Data Factory Kullanıcı arabirimini kullanarak, şirket içinde barındırılan, paylaşılan bir IR oluşturmak için aşağıdaki adımları gerçekleştirebilirsiniz:

1. Şirket içinde barındırılan IR 'de, **başka bir veri fabrikasına Izin ver** ' i seçin ve "tümleştirme çalışma zamanı kurulumu" sayfasında, bağlı IR oluşturmak istediğiniz veri fabrikasını seçin.
      
    ![Paylaşım sekmesinde izin verme düğmesi](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. ' İ ve şirket içinde barındırılan IR 'nin "kaynak KIMLIĞI" ni paylaşılan bir yere kopyalayın.
         
3. İzinlerin verildiği veri fabrikasında, yeni bir kendinden konak IR (bağlantılı) oluşturun ve kaynak KIMLIĞINI girin.
      
    ![Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma düğmesi](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![Bağlı bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma düğmesi](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Ad ve kaynak KIMLIĞI için kutular](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Azure PowerShell kullanarak, şirket içinde barındırılan, paylaşılan bir IR oluşturma

Azure PowerShell kullanarak, şirket içinde barındırılan paylaşılan bir IR oluşturmak için aşağıdaki adımları gerçekleştirebilirsiniz: 
1. Veri fabrikası oluşturma. 
1. Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma.
1. Şirket içinde barındırılan tümleştirme çalışma zamanını diğer veri fabrikaları ile paylaşma.
1. Bağlı bir tümleştirme çalışma zamanı oluşturun.
1. Paylaşımı iptal edin.

### <a name="prerequisites"></a>Önkoşullar 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/). 

- **Azure PowerShell**. [PowerShellGet Ile Windows üzerinde Azure PowerShell Install](/powershell/azure/install-az-ps)içindeki yönergeleri izleyin. Diğer veri fabrikaları ile paylaşılabilen şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmak için bir betiği çalıştırmak üzere PowerShell 'i kullanırsınız. 

> [!NOTE]  
> Data Factory Şu anda kullanılabildiği Azure bölgelerinin bir listesi için,  [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)hakkında sizi ilgilendiren bölgeleri seçin.

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Windows PowerShell Tümleşik Komut Dosyası Ortamı’nı (ISE) başlatın.

1. Değişken oluşturun. Aşağıdaki betiği kopyalayıp yapıştırın. **SubscriptionName** ve **resourcegroupname** gibi değişkenleri gerçek değerlerle değiştirin: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Oturum açın ve bir abonelik seçin. Oturum açmak ve Azure aboneliğinizi seçmek için betiğe aşağıdaki kodu ekleyin:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Bir kaynak grubu ve bir veri fabrikası oluşturun.

    > [!NOTE]  
    > Bu adım isteğe bağlıdır. Zaten bir veri fabrikanızı varsa, bu adımı atlayın. 

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek WestEurope konumunda adlı bir kaynak grubu oluşturur `myResourceGroup` : 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Veri fabrikası oluşturmak için aşağıdaki komutu çalıştırın: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma

> [!NOTE]  
> Bu adım isteğe bağlıdır. Diğer veri fabrikaları ile paylaşmak istediğiniz şirket içinde barındırılan tümleştirme çalışma zamanı zaten varsa, bu adımı atlayın.

Şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmak için aşağıdaki komutu çalıştırın:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Tümleştirme çalışma zamanı kimlik doğrulama anahtarını al ve bir düğümü Kaydet

Şirket içinde barındırılan tümleştirme çalışma zamanının kimlik doğrulama anahtarını almak için aşağıdaki komutu çalıştırın:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Yanıt, bu şirket içinde barındırılan tümleştirme çalışma zamanı için kimlik doğrulama anahtarını içerir. Tümleştirme çalışma zamanı düğümünü kaydettiğinizde bu anahtarı kullanırsınız.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanını yükleyip kaydetme

1. Şirket içinde barındırılan tümleştirme çalışma zamanı yükleyicisini [Azure Data Factory Integration Runtime](https://aka.ms/dmg)indirin.

2. Şirket içinde barındırılan tümleştirmeyi yerel bir bilgisayara yüklemek için yükleyiciyi çalıştırın.

3. Şirket içinde barındırılan yeni tümleştirmeyi, önceki adımda aldığınız kimlik doğrulama anahtarıyla kaydedin.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Şirket içinde barındırılan tümleştirme çalışma zamanını başka bir veri fabrikası ile paylaşma

#### <a name="create-another-data-factory"></a>Başka bir veri fabrikası oluşturma

> [!NOTE]  
> Bu adım isteğe bağlıdır. Paylaşmak istediğiniz Data Factory zaten varsa, bu adımı atlayın. Ancak başka veri fabrikasına rol atamaları eklemek veya kaldırmak için, `Microsoft.Authorization/roleAssignments/write` `Microsoft.Authorization/roleAssignments/delete` [Kullanıcı erişimi Yöneticisi](../role-based-access-control/built-in-roles.md#user-access-administrator) veya [sahibi](../role-based-access-control/built-in-roles.md#owner)gibi izinlerinizin olması gerekir.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>İzin ver

Oluşturduğunuz ve kaydettiğiniz şirket içinde barındırılan tümleştirme çalışma zamanına erişmesi gereken veri fabrikasına izin verin.

> [!IMPORTANT]  
> Bu adımı atlayın!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Bağlı bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma

Bağlı bir şirket içinde barındırılan tümleştirme çalışma zamanı oluşturmak için aşağıdaki komutu çalıştırın:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Artık bağlı bir hizmette bu bağlı tümleştirme çalışma zamanını kullanabilirsiniz. Bağlantılı tümleştirme çalışma zamanı, etkinlikleri çalıştırmak için paylaşılan tümleştirme çalışma zamanını kullanır.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Veri fabrikasından tümleştirme çalışma zamanı paylaşımını iptal etme

Paylaşılan tümleştirme çalışma zamanından bir veri fabrikasının erişimini iptal etmek için aşağıdaki komutu çalıştırın:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Mevcut bağlı tümleştirme çalışma zamanını kaldırmak için, paylaşılan tümleştirme çalışma zamanına karşı aşağıdaki komutu çalıştırın:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="monitoring"></a>İzleme

#### <a name="shared-ir"></a>Paylaşılan IR

![Paylaşılan tümleştirme çalışma zamanı bulma seçimleri](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Paylaşılan tümleştirme çalışma zamanını izleme](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Bağlı IR

![Bağlantılı tümleştirme çalışma zamanı bulma seçimleri](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Bağlantılı tümleştirme çalışma zamanını izleme](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)


### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Şirket içinde barındırılan IR paylaşımının bilinen sınırlamaları

* Bağlı bir IR 'nin oluşturulduğu veri fabrikası, [yönetilen bir kimliğe](../active-directory/managed-identities-azure-resources/overview.md)sahip olmalıdır. Varsayılan olarak, Azure portal veya PowerShell cmdlet 'lerinde oluşturulan veri fabrikaları örtük olarak oluşturulmuş bir yönetilen kimliğe sahiptir. Ancak, bir veri fabrikası Azure Resource Manager şablonu veya SDK aracılığıyla oluşturulduğunda, **kimlik** özelliğini açıkça ayarlamanız gerekir. Bu ayar Kaynak Yöneticisi, yönetilen bir kimlik içeren bir veri fabrikası oluşturmayı sağlar.

* Bu özelliği destekleyen Data Factory .NET SDK sürümü 1.1.0 veya üzeri olmalıdır.

* İzin vermek için, paylaşılan IR 'nin bulunduğu veri fabrikasında sahip rolü veya devralınan sahip rolü gerekir.

* Paylaşma özelliği yalnızca aynı Azure AD kiracısındaki veri fabrikaları için geçerlidir.

* Azure AD [Konuk kullanıcıları](../active-directory/governance/manage-guest-access-with-access-reviews.md)için, bir arama anahtar sözcüğü kullanarak tüm veri fabrikalarını LISTELEYEN Kullanıcı arabirimindeki arama işlevleri çalışmaz. Ancak Konuk Kullanıcı veri fabrikasının sahibi olduğu sürece, arama işlevi olmadan IR 'yi paylaşabilirsiniz. IR paylaşması gereken veri fabrikasının yönetilen kimliği için, **ata izin** kutusuna bu yönetilen kimliği girin ve Data Factory Kullanıcı arabiriminde **Ekle** ' yi seçin.

  > [!NOTE]
  > Bu özellik yalnızca Data Factory v2 sürümünde kullanılabilir.


### <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory tümleştirme çalışma zamanı kavramlarını](./concepts-integration-runtime.md)gözden geçirin.

- [Azure Portal şirket içinde barındırılan tümleştirme çalışma zamanı oluşturmayı](./create-self-hosted-integration-runtime.md)öğrenin.