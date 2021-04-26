---
title: Bir müşteriyi Azure Lighthouse’a ekleme
description: Bir müşteriyi Azure Mathouse 'a eklemeyi öğrenin. böylece, kaynakları Azure tarafından atanan kaynak yönetimi kullanılarak kendi kiracınız aracılığıyla erişilebilir ve yönetilebilir.
ms.date: 03/29/2021
ms.topic: how-to
ms.openlocfilehash: d8ad448ac022b07ecdea6b68c4544b8c955814b1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497974"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Bir müşteriyi Azure Lighthouse’a ekleme

Bu makalede, bir hizmet sağlayıcı olarak, bir müşteriyi Azure açık Thouse 'a nasıl oluşturabileceğiniz açıklanmaktadır. Bunu yaptığınızda, müşterinin Azure Active Directory (Azure AD) kiracısında atanan kaynaklar (abonelikler ve/veya kaynak grupları), [Azure tarafından atanan kaynak yönetimi](../concepts/azure-delegated-resource-management.md)kullanılarak kendi kiracınız aracılığıyla yönetilebilir.

> [!TIP]
> Bu konudaki hizmet sağlayıcılarına ve müşterilere başvuruyoruz, ancak [birden çok kiracıyı yöneten kuruluşlar](../concepts/enterprise.md) , Azure açık thouse 'u ayarlamak ve yönetim deneyimlerini birleştirmek için aynı süreci kullanabilir.

Birden çok müşteri için ekleme işlemini yineleyebilirsiniz. Uygun izinlere sahip bir Kullanıcı, yönetim kiracınızda oturum açtığında, bu kullanıcıya, her bir müşteri kiracısında oturum açmaya gerek kalmadan, yönetim işlemlerini gerçekleştirmek üzere müşteri kiracı kapsamları genelinde yetki atanabilir.

Müşteri görevlendirmeleri genelinde etkileri izlemek ve tanıma almak için, Microsoft İş Ortağı Ağı (MPN) KIMLIĞINIZI eklendi aboneliklerinizin her birine erişimi olan en az bir kullanıcı hesabıyla ilişkilendirin. Bu ilişkilendirmeyi hizmet sağlayıcı kiracınızda gerçekleştirmeniz gerekir. Kiracınızda MPN KIMLIĞINIZLE ilişkilendirilen bir hizmet sorumlusu hesabı oluşturmanızı ve sonra bu hizmet sorumlusunu bir müşteriyi her açışınızda dahil edilmesini öneririz. Daha fazla bilgi için bkz. [temsilci atanan kaynaklarda iş ortağı kazanılmış krediyi sağlamak için iş ortağı kimliğinizi bağlama](partner-earned-credit.md).

> [!NOTE]
> Müşteriler, [Azure Market](publish-managed-services-offers.md)' te yayımladığınız yönetilen bir hizmet teklifi (genel veya özel) satın alırken Azure 'un açık eklendi bir şekilde kullanıma sunulmasını sağlar. Burada açıklanan ekleme sürecini Azure Marketi 'Nde yayınlanan tekliflerle birlikte kullanabilirsiniz.

Ekleme işlemi, eylemlerin hem hizmet sağlayıcının kiracısından hem de müşterinin kiracısından alınması gerekir. Bu adımların tümü bu makalede açıklanmıştır.

## <a name="gather-tenant-and-subscription-details"></a>Kiracı ve abonelik ayrıntılarını toplayın

Bir müşterinin kiracısını eklemek için etkin bir Azure aboneliğine sahip olması gerekir. Şunları bilmeniz gerekir:

- Hizmet sağlayıcısının kiracının kiracı KIMLIĞI (müşterinin kaynaklarını yönettiğiniz yer)
- Müşterinin kiracının kiracı KIMLIĞI (hizmet sağlayıcısı tarafından yönetilen kaynaklar olacaktır)
- Müşterinin kiracısındaki, hizmet sağlayıcısı tarafından yönetilecek (veya hizmet sağlayıcısı tarafından yönetilecek kaynak gruplarını içeren) her bir abonelik için abonelik kimlikleri.

Bu KIMLIK değerleri zaten yoksa, bunları aşağıdaki yollarla alabilirsiniz. Dağıtımınızda bu tam değerleri kullandığınızdan emin olun ve bunları kullanın.

### <a name="azure-portal"></a>Azure portalı

Kiracı KIMLIĞINIZ, Azure portal sağ üst tarafındaki hesap adınızın üzerine gelerek veya **Dizin Değiştir**' i seçerek görülebilir. Kiracı KIMLIĞINIZI seçmek ve kopyalamak için Portal içinden "Azure Active Directory" araması yapın, ardından **Özellikler** ' i seçin ve **dizin kimliği** alanında gösterilen değeri kopyalayın. Müşteri kiracısında abonelik kimliğini bulmak için "Abonelikler" araması yapın ve ilgili abonelik kimliğini seçin.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Burada açıklanan işlemi kullanarak abonelik (veya bir abonelik içindeki bir veya daha fazla kaynak grubu) ekleme sırasında, **Microsoft. ManagedServices** kaynak sağlayıcısı bu abonelik için kaydedilir.

## <a name="define-roles-and-permissions"></a>Rolleri ve izinleri tanımlama

Hizmet sağlayıcı olarak, farklı kapsamlar için farklı erişim gerektiren tek bir müşteri için birden çok görev gerçekleştirmek isteyebilirsiniz. Uygun [Azure yerleşik rollerinin](../../role-based-access-control/built-in-roles.md)atanması için ihtiyacınız olan sayıda yetkilendirmeler tanımlayabilirsiniz. Her yetkilendirme, yönetim kiracısındaki bir Azure AD kullanıcısına, grubuna veya hizmet sorumlusuna başvuran bir **PrincipalId** içerir.

> [!NOTE]
> Açıkça belirtilmedikçe, Azure Mathouse belgelerindeki bir "kullanıcıya" başvurular bir Azure AD kullanıcısına, grubuna veya hizmet sorumlusuna bir yetkilendirmede uygulanabilir.

Yönetimi kolaylaştırmak için, bireysel kullanıcılar yerine her bir rol için Azure AD Kullanıcı gruplarını kullanmanızı öneririz. Bu sayede, Kullanıcı değişikliği yapmak için ekleme işlemini tekrarlamanız gerekmiyorsa, erişimi olan gruba bireysel kullanıcı ekleme veya kaldırma esnekliği sağlar. Ayrıca, Otomasyon senaryolarında yararlı olabilecek bir hizmet sorumlusuna roller atayabilirsiniz.

> [!IMPORTANT]
> Bir Azure AD grubu için izinler eklemek üzere, **Grup türü** **güvenlik** olarak ayarlanmalıdır. Grup oluşturulduğunda bu seçenek seçilidir. Daha fazla bilgi için bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Yetkilendirmeleri tanımlarken, kullanıcıların yalnızca işlerini tamamlaması için gerekli izinlere sahip olması için en az ayrıcalık ilkesini izlediğinizden emin olun. Desteklenen roller ve en iyi uygulamalar hakkında daha fazla bilgi için bkz. [Azure 'Da kiracılar, kullanıcılar ve roller](../concepts/tenants-users-roles.md).

Yetkilendirmeleri tanımlamak için, erişim vermek istediğiniz hizmet sağlayıcı kiracısında her bir Kullanıcı, Kullanıcı grubu veya hizmet sorumlusu için KIMLIK değerlerini bilmeniz gerekir. Ayrıca, atamak istediğiniz her bir yerleşik rol için rol tanımı KIMLIĞI gerekir. Daha önceden sahip değilseniz, hizmet sağlayıcı kiracısı içinden aşağıdaki komutları çalıştırarak bunları alabilirsiniz.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> Bir müşteriyi eklerken [yönetilen hizmetler kayıt ataması silme rolünü](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) atamanız önerilir, böylece kiracınızdaki kullanıcılar gerekirse daha sonra [temsilciye erişimi kaldırabilirler](remove-delegation.md) . Bu rol atanmamışsa, atanan kaynaklar yalnızca müşterinin kiracısındaki bir kullanıcı tarafından kaldırılabilir.

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu oluşturma

Müşterinizi eklemek için aşağıdaki bilgileri ekleyerek teklifinize ait bir [Azure Resource Manager](../../azure-resource-manager/index.yml) şablonu oluşturmanız gerekir. **Mspoffername** ve **mspofferdescription** değerleri, Azure Portal [hizmet sağlayıcıları sayfasında](view-manage-service-providers.md) müşteri tarafından görülebilir.

|Alan  |Tanım  |
|---------|---------|
|**mspOfferName**     |Bu tanımı açıklayan bir ad. Bu değer, teklifin başlığı olarak müşteriye görüntülenir ve benzersiz bir değer olmalıdır.        |
|**mspOfferDescription**     |Teklifinizin kısa bir açıklaması (örneğin, "contoso VM yönetimi teklifi").      |
|**managedByTenantId**     |Kiracı KIMLIĞINIZ.          |
|**yetkilendirmeleri**     |Kiracınızdaki kullanıcılar/gruplar/SPN 'Ler için **PrincipalId** değerleri, müşterinizin yetkilendirmesinin amacını anlamasına **yardımcı olmak ve** erişim düzeyini belirtmek Için yerleşik bir **roledefinitionıd** değeri ile eşleştirilir.      |

Ekleme işlemi, bir Azure Resource Manager şablonu ( [örnek](https://github.com/Azure/Azure-Lighthouse-samples/)depolarımızda sağlanan) ve yapılandırmanızla eşleşecek şekilde değiştirdiğiniz karşılık gelen bir parametre dosyası gerektirir ve yetkilendirmeleri tanımlar.

> [!IMPORTANT]
> Burada açıklanan işlem, aynı müşteri kiracısına abonelik ekleme olsanız bile, her abonelik için eklendi olan ayrı bir dağıtım gerektirir. Aynı müşteri kiracısında farklı abonelikler içinde birden fazla kaynak grubu eklediyseniz ayrı dağıtımlar da gereklidir. Ancak, tek bir abonelik içinde birden çok kaynak grubu ekleme işlemi tek bir dağıtımda yapılabilir.
>
> Aynı aboneliğe (veya bir abonelik içindeki kaynak gruplarına) uygulanan birden çok teklif için ayrı dağıtımlar da gerekir. Uygulanan her teklifin farklı bir **Mspoffername** kullanması gerekir.

Seçtiğiniz şablon, bir aboneliğin tüm aboneliğini, kaynak grubunu veya birden çok kaynak grubunu mı, yoksa bir abonelik içinde mi sundığınıza bağlıdır. Ayrıca, aboneliklerini bu şekilde eklemek isterseniz, Azure Marketi 'Nde yayımladığınız yönetilen hizmet teklifini satın alan müşteriler için kullanılabilecek bir şablon sunuyoruz.

|Bunu eklemek için  |Bu Azure Resource Manager şablonunu kullan  |Ve bu parametre dosyasını Değiştir |
|---------|---------|---------|
|Abonelik   |[ ÜzerindedelegatedResourceManagement.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[ ÜzerindedelegatedResourceManagement.parameters.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Kaynak grubu   |[ ÜzerindergDelegatedResourceManagement.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[ ÜzerindergDelegatedResourceManagement.parameters.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Bir abonelikte birden fazla kaynak grubu   |[ ÜzerindemultipleRgDelegatedResourceManagement.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[ ÜzerindemultipleRgDelegatedResourceManagement.parameters.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Abonelik (Azure Marketi 'Nde yayınlanan bir teklifi kullanırken)   |[ ÜzerindemarketplaceDelegatedResourceManagement.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[ ÜzerindemarketplaceDelegatedResourceManagement.parameters.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Bir yönetim grubunun tamamını tek bir dağıtımda eklemenize karşın, [bir ilkeyi yönetim grubu düzeyinde dağıtabilirsiniz](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups). İlke, yönetim grubundaki her bir aboneliğin belirtilen yönetim kiracısına devredildi olup olmadığını denetlemek için [Deployifnotexists efektini](../../governance/policy/concepts/effects.md#deployifnotexists) kullanır ve aksi takdirde, sağladığınız değerlere göre atamayı oluşturacaktır. Daha sonra, yönetim grubundaki tüm aboneliklere erişim sahibi olacaksınız, ancak bunlar üzerinde ayrı abonelikler (bir bütün olarak eylemler gerçekleştirmek yerine) üzerinde çalışmanız gerekir.

Aşağıdaki örnek, bir aboneliği eklemek için kullanılabilecek **delegatedResourceManagement.parameters.js** değiştirilmiş bir dosya gösterir. Kaynak grubu parametre dosyaları ( [RG-Temsilcili-kaynak-yönetim](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) klasöründe bulunur) benzerdir, ancak aynı zamanda eklendi olacak belirli kaynak gruplarını belirlemek Için bir **RgName** parametresi de içerir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

Yukarıdaki örnekteki en son yetkilendirme, Kullanıcı erişimi yönetici rolü (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) ile bir **PrincipalId** ekliyor. Bu rolü atarken, **Delegatedrotadefinitionıds** özelliğini ve desteklenen bir veya daha fazla Azure yerleşik rolünü eklemeniz gerekir. Bu yetkilendirmede oluşturulan kullanıcı, bu rolleri, [düzeltilen ilkeleri dağıtmak](deploy-policy-remediation.md)için gerekli olan müşteri kiracısında [yönetilen kimliklere](../../active-directory/managed-identities-azure-resources/overview.md) atayabilecektir.  Kullanıcı da destek olayları oluşturabilir. Normalde Kullanıcı erişimi Yöneticisi rolüyle ilişkili başka hiçbir izin bu **PrincipalId** için geçerlidir.

## <a name="deploy-the-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarını dağıtma

Parametre dosyanızı güncelleştirdikten sonra, müşterinin kiracısındaki bir kullanıcının kiracı içinde Azure Resource Manager şablonu dağıtmaları gerekir. Eklemek istediğiniz her abonelik için ayrı bir dağıtım gerekir (veya eklemek istediğiniz kaynak gruplarını içeren her bir abonelik için).

> [!IMPORTANT]
> Bu dağıtım, bir rolün `Microsoft.Authorization/roleAssignments/write` eklendi olması gibi (veya eklendi olan kaynak gruplarını içeren) bir role sahip olan, müşterinin kiracısında Konuk olmayan [](../../role-based-access-control/built-in-roles.md#owner)bir hesap tarafından yapılmalıdır. Aboneliği temsil edebilen kullanıcıları bulmak için, müşterinin kiracısındaki bir Kullanıcı Azure portal aboneliği seçebilir, **erişim denetimini (IAM)** açabilir ve [sahip rolüne sahip tüm kullanıcıları görüntüleyebilir](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription). 
>
> Abonelik, [bulut çözümü sağlayıcısı (CSP) programı](../concepts/cloud-solution-provider.md)aracılığıyla oluşturulduysa, hizmet sağlayıcı kiracınızda [yönetici Aracısı](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) rolüne sahip olan tüm kullanıcılar dağıtımı gerçekleştirebilir.

Dağıtım Azure portal, PowerShell kullanılarak veya aşağıda gösterildiği gibi Azure CLı kullanılarak yapılabilir.

### <a name="azure-portal"></a>Azure portalı

1. [GitHub](https://github.com/Azure/Azure-Lighthouse-samples/)deponuzda, kullanmak istediğiniz şablonun yanında gösterilen **Azure 'a dağıt** düğmesini seçin. Şablon Azure portalda açılır.
1. **MSP teklif adı**, **MSP teklif açıklaması**, **Kiracı kimliği tarafından yönetilen** ve **yetkilendirmeler** için değerlerinizi girin. İsterseniz **parametreleri Düzenle** ' yi seçerek `mspOfferName` parametre dosyasında,,, `mspOfferDescription` `managedbyTenantId` ve doğrudan değerlerini girebilirsiniz `authorizations` . Şablondaki varsayılan değerleri kullanmak yerine bu değerleri güncelleştirdiğinizden emin olun.
1. **Gözden geçir ve oluştur**' u seçin ve **Oluştur**' u seçin.

Birkaç dakika sonra dağıtımın tamamlandığını belirten bir bildirim görmeniz gerekir.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>Başarılı ekleme Onayla

Bir müşteri aboneliğinin Azure Mathouse 'a başarıyla eklendi, hizmet sağlayıcının kiracısındaki kullanıcılar aboneliği ve kaynaklarını görebilir (tek tek veya bir Azure AD grubunun bir üyesi olarak, uygun izinlere sahip bir Azure AD grubuna erişim izni verildiyse). Bunu onaylamak için, aboneliğin aşağıdaki yollarla göründüğünden emin olun.  

### <a name="azure-portal"></a>Azure portalı

Hizmet sağlayıcısının kiracısında:

1. [Müşterilerimiz sayfasına](view-manage-customers.md)gidin.
2. **Müşteriler**’i seçin.
3. Kaynak Yöneticisi şablonunda verdiğiniz teklif adı ile abonelik (ler) i görmek istediğinizi onaylayın.

> [!IMPORTANT]
> [Müşterilerimde](view-manage-customers.md)Temsilcili aboneliği görmek için, hizmet sağlayıcısının kiracısındaki kullanıcılara, abonelik eklendi olduğunda [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü (veya okuyucu erişimi içeren başka bir yerleşik rol) verilmiş olması gerekir.

Müşterinin kiracısında:

1. [Hizmet sağlayıcıları sayfasına](view-manage-service-providers.md)gidin.
2. **Hizmet sağlayıcısı teklifleri**’ni seçin.
3. Kaynak Yöneticisi şablonunda verdiğiniz teklif adı ile abonelik (ler) i görmek istediğinizi onaylayın.

> [!NOTE]
> Güncelleştirmeler Azure portal yansıtılmadan önce dağıtımınızın tamamlanması 15 dakika kadar sürebilir. Tarayıcıyı yenileyerek, oturum açıp kapatarak veya yeni bir belirteç isteyerek Azure Resource Manager belirtecinizi güncelleştirirseniz güncelleştirmeleri daha çabuk görebilirsiniz.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

Müşteri eklendi sonra değişiklik yapmanız gerekirse, [temsilciyi güncelleştirebilirsiniz](update-delegation.md). Ayrıca [, temsilciye erişimi tamamen kaldırabilirsiniz](remove-delegation.md) .

## <a name="troubleshooting"></a>Sorun giderme

Müşterinizin başarıyla sunulabileceği takdirde veya kullanıcılarınız Temsilcili kaynaklara erişirken sorun yaşıyorsa, aşağıdaki ipuçları ve gereksinimleri denetleyip yeniden deneyin.

- `managedbyTenantId`Değer, eklendi olan aboneliğin KIRACı kimliğiyle aynı olmamalıdır.
- Aynı kapsamda aynı kapsamda birden çok atama olamaz `mspOfferName` .
- Atanmış abonelik için **Microsoft. ManagedServices** kaynak sağlayıcısının kayıtlı olması gerekir. Bu, dağıtım sırasında otomatik olarak gerçekleşmelidir, ancak yoksa [el ile kaydedebilirsiniz](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).
- Yetkilendirmeler, [sahip](../../role-based-access-control/built-in-roles.md#owner) yerleşik rolüne sahip herhangi bir kullanıcı veya [dataactions](../../role-based-access-control/role-definitions.md#dataactions)içeren yerleşik roller içermemelidir.
- Gruplar, **Microsoft 365** değil, [**Grup türü**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) **güvenlik** olarak ayarlanmış olmalıdır.
- [İç içe gruplar](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)için erişim etkinleştirilmeden önce ek bir gecikme olabilir.
- Azure portal kaynakları görüntülemesi gereken kullanıcıların [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolüne (veya okuyucu erişimi de içeren başka bir yerleşik Role) sahip olması gerekir.
- Yetkilendirmelere dahil ettiğiniz [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md) , kullanım dışı bırakılmış roller içermemelidir. Azure yerleşik rolü kullanım dışı olursa, bu rolle eklendi olan tüm kullanıcılar erişimi kaybeder ve ek temsilciler yükleyemezsiniz. Bu işlemi onarmak için, şablonunuzu yalnızca desteklenen yerleşik rolleri kullanacak şekilde güncelleştirin ve ardından yeni bir dağıtım gerçekleştirin.

## <a name="next-steps"></a>Sonraki adımlar

- [Çapraz kiracı yönetim deneyimleri](../concepts/cross-tenant-management-experience.md)hakkında bilgi edinin.
- Azure portal **müşterilerime** giderek [müşterileri görüntüleyin ve yönetin](view-manage-customers.md) .
- Bir temsilciyi [güncelleştirmeyi](update-delegation.md) veya [kaldırmayı](remove-delegation.md) öğrenin.
