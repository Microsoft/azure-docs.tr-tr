---
title: Önizleme API’leriyle program aracılığıyla Azure abonelikleri oluşturma
description: REST API, Azure CLI ve Azure PowerShell’in önizleme sürümlerini kullanarak program aracılığıyla ek Azure abonelikleri oluşturmayı öğrenin.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: d3247a86795b9661196c3264c60b06e7c61d6e23
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877921"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>Önizleme API’leriyle program aracılığıyla Azure abonelikleri oluşturma

Bu makale, eski önizleme API’mizi kullanarak program aracılığıyla Azure abonelikleri oluşturmanıza yardımcı olur. Bu makalede Azure Resource Manager'ı kullanarak program aracılığıyla abonelik oluşturmayı öğreneceksiniz.

En son API sürümü için farklı Azure sözleşmesi abonelik türleriyle kullanılmak üzere yeni makalelerimiz var:

- [En son API ile program aracılığıyla EA abonelikleri oluşturma](programmatically-create-subscription-enterprise-agreement.md)
- [En son API ile program aracılığıyla MCA abonelikleri oluşturma](programmatically-create-subscription-microsoft-customer-agreement.md)
- [En son API ile program aracılığıyla MPA abonelikleri oluşturma](Programmatically-create-subscription-microsoft-customer-agreement.md)

Bununla birlikte, en son API sürümünü kullanmak istemeseniz de bu makaledeki bilgilerden yararlanabilirsiniz.

Aşağıdaki sözleşme türlerine yönelik faturalama hesabına sahip Azure müşterileri program aracılığıyla abonelikler oluşturabilir:

- Kurumsal Anlaşma
- Microsoft Müşteri Sözleşmesi (MCA)
- Microsoft İş Ortağı Sözleşmesi (MPA)

Program aracılığıyla Azure aboneliği oluşturduğunuzda, bu abonelik Microsoft’tan veya yetkili satıcıdan aldığınız Azure hizmetlerinin tabi olduğu sözleşmeye tabidir. Daha fazla bilgi için bkz. [Microsoft Azure Yasal Bilgiler](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>EA fatura hesabı için abonelik oluşturma

EA abonelikleri oluşturmak için aşağıdaki bölümlerdeki bilgilerden yararlanır.

### <a name="prerequisites"></a>Önkoşullar

Abonelik oluşturmak için Kayıt Hesabında Sahip rolünüzün olması gerekir. Rolü almanın iki yolu vardır:

* Kaydınızın Kuruluş Yöneticisi [sizi Hesap Sahibi yapabilir](https://ea.azure.com/helpdocs/addNewAccount) (ve oturum açma gereklidir) ve bu durumda Kayıt Hesabının Sahibi olursunuz.
* Kayıt Hesabının mevcut Sahibi [size erişim verebilir](grant-access-to-create-subscription.md). Benzer şekilde, EA aboneliğini oluştururken bir hizmet sorumlusu kullanmak için [söz konusu hizmet sorumlusuna abonelik oluşturabilme becerisi](grant-access-to-create-subscription.md) vermelisiniz.

### <a name="find-accounts-you-have-access-to"></a>Erişiminiz olan hesapları bulma

Siz Hesap Sahibi ile ilişkilendirilmiş bir Kayıt Hesabına eklendikten sonra, Azure hesabın kayıtla ilişkisini kullanarak abonelik ücretlerinin nereye faturalanacağını saptar. Hesap kapsamında oluşturulan tüm abonelikler hesabın içinde yer aldığı EA kaydına faturalanır. Abonelikleri oluşturmak için, aboneliğin sahibi olacak kayıt hesabı hakkındaki değerleri ve kullanıcı asıl adlarını geçirmeniz gerekir.

Aşağıdaki komutları çalıştırmak için Hesap Sahibinin *giriş dizininde* oturum açmalısınız. Abonelikler varsayılan olarak bu dizinde oluşturulur.

### <a name="rest"></a>[REST](#tab/rest)

Erişiminiz olan tüm kayıt hesaplarını listeleme isteği:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

API yanıtında erişiminiz olan tüm kayıt hesapları listelenir:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

`principalName` özelliğini kullanarak aboneliklerin faturalanmasını istediğiniz hesabı belirleyin. Bu hesabın `name` değerini kopyalayın. Örneğin, SignUpEngineering@contoso.com kayıt hesabı altında abonelik oluşturmak için ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` değerini kopyalayın. Bu tanımlayıcı kayıt hesabının nesne kimliğidir. Bir sonraki adımda `enrollmentAccountObjectId` olarak kullanabilmeniz için bu değeri bir yere yapıştırın.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Azure Cloud Shell](https://shell.azure.com/)'i açın ve PowerShell'i seçin.

[Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet'ini kullanarak erişiminiz olan tüm kayıt hesaplarını listeleyin.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure erişiminiz olan kayıt hesaplarını listeleyerek yanıt verir:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` özelliğini kullanarak aboneliklerin faturalanmasını istediğiniz hesabı belirleyin. Bu hesabın `ObjectId` değerini kopyalayın. Örneğin, SignUpEngineering@contoso.com kayıt hesabı altında abonelik oluşturmak için ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` değerini kopyalayın. Bir sonraki adımda `enrollmentAccountObjectId` olarak kullanabilmeniz için nesne kimliğini bir yere yapıştırın.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Erişiminiz olan tüm kayıt hesaplarını listelemek için [az billing enrollment-account list](/cli/azure/billing) komutunu kullanın.

```azurecli-interactive
az billing enrollment-account list
```

Azure erişiminiz olan kayıt hesaplarını listeleyerek yanıt verir:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

`principalName` özelliğini kullanarak aboneliklerin faturalanmasını istediğiniz hesabı belirleyin. Bu hesabın `name` değerini kopyalayın. Örneğin, SignUpEngineering@contoso.com kayıt hesabı altında abonelik oluşturmak için ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` değerini kopyalayın. Bu tanımlayıcı kayıt hesabının nesne kimliğidir. Bir sonraki adımda `enrollmentAccountObjectId` olarak kullanabilmeniz için bu değeri bir yere yapıştırın.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Belirli bir kayıt hesabı altında abonelik oluşturma

Aşağıdaki örnekte, önceki adımda seçilen kayıt hesabında *Dev Team Subscription* adlı bir abonelik oluşturulur. Abonelik teklifi *MS-AZR-0017P*'dir (normal Microsoft Kurumsal Anlaşması). Ayrıca isteğe bağlı olarak iki kullanıcıyı abonelik için Azure RBAC Sahibi olarak ekler.

### <a name="rest"></a>[REST](#tab/rest)

`<enrollmentAccountObjectId>` değerini, ilk adımda (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kopyaladığınız `name` ile değiştirerek aşağıdaki istekte bulunun. Sahipleri belirtmek için bkz. [kullanıcı nesnesi kimliklerini alma](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Öğe Adı  | Gerekli | Tür   | Açıklama                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Hayır      | Dize | Aboneliğin görünen adı. Belirtilmezse, teklifin adına (örneğin "Microsoft Azure Kurumsal") ayarlanır.                                 |
| `offerType`   | Evet      | Dize | Abonelik teklifi. EA için iki seçenek [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (üretim kullanımı) ve[MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)'dir (geliştirme/test, [EA portalı kullanılarak açılmalıdır](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Hayır       | Dize | Abonelik oluşturulurken aboneliğe Azure RBAC Sahibi olarak eklemek istediğiniz kullanıcının Nesne Kimliği.  |

Yanıtta `Location` üst bilgisinin bir parçası olarak, abonelik oluşturma işleminin durumunu sorgulayabileceğiniz bir url alırsınız. Abonelik oluşturma işlemi tamamlandığında `Location` url'si üzerinde GET kullanarak abonelik kimliğini içeren `subscriptionLink` nesnesi döndürülür. Diğer ayrıntılar için [Abonelik API'si belgelerine](/rest/api/subscription/) bakın

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Modülün `New-AzSubscription` cmdlet'ini içeren en son sürümünü yüklemek için `Install-Module Az.Subscription` çalıştırın. PowerShellGet'in yeni bir sürümünü yüklemek için [Get PowerShellGet Modülü](/powershell/scripting/gallery/installing-psget)'ne bakın.

Aşağıdaki [New-AzSubscription](/powershell/module/az.subscription) komutunu çalıştırın; `<enrollmentAccountObjectId>` öğesini ilk adımda toplanan `ObjectId` ile değiştirin (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sahipleri belirtmek için bkz. [kullanıcı nesnesi kimliklerini alma](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Öğe Adı  | Gerekli | Tür   | Açıklama |
|---------------|----------|--------|----|
| `Name` | Hayır      | Dize | Aboneliğin görünen adı. Belirtilmezse, teklifin adına (örneğin *Microsoft Azure Kurumsal*) ayarlanır. |
| `OfferType`   | Evet      | Dize | Abonelik teklifi. EA için iki seçenek [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (üretim kullanımı) ve[MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)'dir (geliştirme/test, [EA portalı kullanılarak açılmalıdır](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Evet       | Dize | Altında aboneliğin oluşturulduğu ve faturalandığı kayıt hesabının Nesne Kimliği. Bu değer, `Get-AzEnrollmentAccount` komutundan aldığınız GUID'dir. |
| `OwnerObjectId`      | Hayır       | Dize | Abonelik oluşturulurken aboneliğe Azure RBAC Sahibi olarak eklenecek kullanıcının Nesne Kimliği.  |
| `OwnerSignInName`    | Hayır       | Dize | Abonelik oluşturulurken aboneliğe Azure RBAC Sahibi olarak eklenecek kullanıcının e-posta adresi. `OwnerObjectId` yerine bu parametreyi kullanabilirsiniz.|
| `OwnerApplicationId` | Hayır       | Dize | Abonelik oluşturulurken aboneliğe Azure RBAC Sahibi olarak eklenecek hizmet sorumlusunun uygulama kimliği. `OwnerObjectId` yerine bu parametreyi kullanabilirsiniz. Bu parametre kullanıldığında hizmet sorumlusunun [dizine okuma erişimi](/powershell/azure/active-directory/signing-in-service-principal#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole) olmalıdır.|

Tüm parametrelerin listesini görmek için bkz. [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

İlk olarak `az extension add --name subscription` komutunu çalıştırarak önizleme uzantısını yükleyin.

Aşağıdaki [az account create](/cli/azure/account#-ext-subscription-az-account-create) komutunu çalıştırın; `<enrollmentAccountObjectId>` öğesini ilk adımda kopyaladığınız `name` ile değiştirin (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sahipleri belirtmek için bkz. [kullanıcı nesnesi kimliklerini alma](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Öğe Adı  | Gerekli | Tür   | Açıklama |
|---------------|----------|--------|------------|
| `display-name` | Hayır      | Dize | Aboneliğin görünen adı. Belirtilmezse, teklifin adına (örneğin *Microsoft Azure Kurumsal*) ayarlanır.|
| `offer-type`   | Evet      | Dize | Abonelik teklifi. EA için iki seçenek [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (üretim kullanımı) ve[MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)'dir (geliştirme/test, [EA portalı kullanılarak açılmalıdır](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Evet       | Dize | Altında aboneliğin oluşturulduğu ve faturalandığı kayıt hesabının Nesne Kimliği. Bu değer, `az billing enrollment-account list` komutundan aldığınız GUID'dir. |
| `owner-object-id`      | Hayır       | Dize | Abonelik oluşturulurken aboneliğe Azure RBAC Sahibi olarak eklenecek kullanıcının Nesne Kimliği.  |
| `owner-upn`    | Hayır       | Dize | Abonelik oluşturulurken aboneliğe Azure RBAC Sahibi olarak eklenecek kullanıcının e-posta adresi. `owner-object-id` yerine bu parametreyi kullanabilirsiniz.|
| `owner-spn` | Hayır       | Dize | Abonelik oluşturulurken aboneliğe Azure RBAC Sahibi olarak eklenecek hizmet sorumlusunun uygulama kimliği. `owner-object-id` yerine bu parametreyi kullanabilirsiniz. Bu parametre kullanıldığında hizmet sorumlusunun [dizine okuma erişimi](/powershell/azure/active-directory/signing-in-service-principal#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole) olmalıdır.|

Tüm parametrelerin listesini görmek için bkz. [az account create](/cli/azure/account#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure Kurumsal aboneliği oluşturma API'sinin sınırlamaları

- Bu API kullanılarak yalnızca Azure Kurumsal abonelikleri oluşturulabilir.
- Kayıt hesabı başına 2000 abonelik sınırı vardır. Hesap için bu sayıyı aşan abonelikler yalnızca Azure portalda oluşturulabilir. API aracılığıyla daha fazla abonelik oluşturmak istiyorsanız başka bir kayıt hesabı oluşturun. İptal edilen, silinen ve aktarılan aboneliklerin sayısı en fazla 2000 olabilir.
- Hesap Sahibi olmayan ama Azure RBAC ile kayıt hesabına eklenmiş olan kullanıcılar Azure portalda abonelik oluşturamaz.
- İçinde aboneliğin oluşturulacağı kiracıyı seçemezsiniz. Abonelik her zaman Hesap Sahibinin giriş kiracısında oluşturulur. Aboneliği farklı bir kiracılığa taşımak için [abonelik kiracısını değiştirme](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) konusuna bakın.


## <a name="create-subscriptions-for-an-mca-account"></a>MCA hesabı için abonelik oluşturma

MCA hesabına yönelik abonelikler oluşturmak için aşağıdaki bölümlerdeki bilgilerden yararlanır.

### <a name="prerequisites"></a>Ön koşullar

Abonelikleri oluşturmak için bir fatura bölümünde sahip, katkıda bulunan veya Azure abonelik oluşturucu rolünüz ya da faturalama profili veya faturalama hesabında sahip veya katkıda bulunan rolünüz olmalıdır. Daha fazla bilgi için bkz. [Abonelik faturalama rolleri ve görevleri](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Aşağıdaki örneklerde REST API’leri kullanılmaktadır. Şu anda PowerShell ve Azure CLI desteklenmemektedir.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Erişiminiz olan fatura hesaplarını bulma

Tüm faturalama hesaplarını listelemek için aşağıdaki isteği gönderin.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API yanıtında erişiminiz olan fatura hesapları listelenir.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Abonelikleri oluşturmak istediğiniz fatura hesabını belirlemek için `displayName` özelliğini kullanın. Hesabın agreementType ayarının *MicrosoftCustomerAgreement* olduğundan emin olun. Hesabın `name` değerini kopyalayın.  Örneğin, `Contoso` faturalama hesabı için bir abonelik oluşturmak amacıyla `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` değerini kopyalayın. Bir sonraki adımda kullanabilmeniz için bu değeri bir yere yapıştırın.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Abonelikleri oluşturmak için fatura bölümlerini bulma

Aboneliğinizin ücretleri, faturalama profilinin faturasının bir bölümünde gösterilir. Azure aboneliklerini oluşturma izniniz olan fatura bölümlerinin ve faturalama profillerinin listesini almak için aşağıdaki API'yi kullanın.

`<billingAccountName>` değerini, ilk adımda (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) kopyaladığınız `name` ile değiştirerek aşağıdaki istekte bulunun.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

API yanıtında, abonelik oluşturmak için erişiminiz olan tüm fatura bölümleri ve bunların faturalama profilleri listelenir:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Abonelikleri oluşturmak istediğiniz fatura bölümünü belirlemek için `invoiceSectionDisplayName` özelliğini kullanın. Fatura bölümü için `invoiceSectionId` ve `billingProfileId` değerleriyle birlikte `skuId` hesaplarından birini kopyalayın. Örneğin `Development` fatura bölümü için `Microsoft Azure plan` türünde bir abonelik oluşturmak üzere `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` ve `0001` değerlerini kopyalayın. Bir sonraki adımda kullanabilmeniz için bu değerleri bir yere yapıştırın.

### <a name="create-a-subscription-for-an-invoice-section"></a>Fatura bölümü için aboneliği oluşturma

Aşağıdaki örnekte *Development* fatura bölümü için *Microsoft Azure Plan* türünde *Dev Team subscription* adlı bir abonelik oluşturulur. Abonelik *Contoso finans* fatura profiline faturalanır ve faturanın *Geliştirme* bölümünde görüntülenir.

`<invoiceSectionId>` değerini, ikinci adımda (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```) kopyaladığınız `invoiceSectionId` ile değiştirerek aşağıdaki istekte bulunun. İkinci adımda kopyaladığınız `billingProfileId` ve `skuId` değerlerini API’nin istek parametrelerine geçirin. Sahipleri belirtmek için bkz. [kullanıcı nesnesi kimliklerini alma](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Öğe Adı  | Gerekli | Tür   | Açıklama                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Evet      | Dize | Aboneliğin görünen adı.|
| `billingProfileId`   | Evet      | Dize | Aboneliğin ücretleri için faturalanacak fatura profilinin kimliği.  |
| `skuId` | Evet      | Dize | Azure planının türünü belirleyen sku kimliği. |
| `owners`      | Hayır       | Dize | Abonelik oluşturulurken aboneliğe Azure RBAC Sahibi olarak eklenecek kullanıcının veya hizmet sorumlusunun Nesne Kimliği.  |
| `costCenter` | Hayır      | Dize | Abonelikle ilişkilendirilmiş maliyet merkezi. Kullanım CSV dosyasında gösterilir. |
| `managementGroupId` | Hayır      | Dize | Aboneliğin ekleneceği yönetim grubunun kimliği. Yönetim gruplarının listesini almak için bkz. [Yönetim Grupları - Liste API'si](/rest/api/managementgroups/entities/list). API'den yönetim grubunun kimliğini kullanın. |

Yanıtta, izleme için bir `subscriptionCreationResult` nesnesi alırsınız. Abonelik oluşturma işlemi tamamlandığında `subscriptionCreationResult` nesnesi abonelik kimliğini içeren `subscriptionLink` nesnesini döndürür.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>MPA fatura hesabı için abonelik oluşturma

MPA faturalama hesabına yönelik abonelikler oluşturmak için aşağıdaki bölümlerdeki bilgilerden yararlanır.

### <a name="prerequisites"></a>Önkoşullar

Fatura hesabınıza abonelik oluşturmak için kuruluşunuzun Bulut Çözümü Sağlayıcısı hesabında Genel Yönetici veya Yönetici Aracısı rolünüz olmalıdır. Daha fazla bilgi için bkz. [İş Ortağı Merkezi - Kullanıcı rollerini ve izinleri atama](/partner-center/permissions-overview).

Aşağıdaki örneklerde REST API’leri kullanılmaktadır. Şu anda PowerShell ve Azure CLI desteklenmemektedir.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Erişiminiz olan fatura hesaplarını bulma

Erişiminiz olan tüm fatura hesaplarını listelemek için aşağıdaki istekte bulunun.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

API yanıtında fatura hesapları listelenir.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Abonelikleri oluşturmak istediğiniz fatura hesabını belirlemek için `displayName` özelliğini kullanın. Hesabın agreementType ayarının *MicrosoftPartnerAgreement* olduğundan emin olun. Hesap için `name` değerini kopyalayın. Örneğin `Contoso` faturalama hesabı için bir abonelik oluşturmak amacıyla `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` değerini kopyalayın. Bir sonraki adımda kullanabilmeniz için bu değeri bir yere yapıştırın.

### <a name="find-customers-that-have-azure-plans"></a>Azure planları olan müşterileri bulma

Azure abonelikleri oluşturabileceğiniz fatura hesabındaki tüm kullanıcıları listelemek için `<billingAccountName>` değerini ilk adımda kopyaladığınız `name` ile değiştirerek (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) aşağıdaki istekte bulunun.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API yanıtında, fatura hesabında Azure planları olan müşteriler listelenir. Müşteriler için abonelik oluşturabilirsiniz.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Abonelikleri oluşturmak istediğiniz müşteriyi belirlemek için `displayName` özelliğini kullanın. Müşteri için `id` değerini kopyalayın. Örneğin, `Fabrikam toys` için bir abonelik oluşturmak amacıyla `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx` değerini kopyalayın. Sonraki adımlarda kullanabilmeniz için bu değeri yapıştırın.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Dolaylı sağlayıcılar için isteğe bağlı: Müşterinin kurumsal bayilerini alma

CSP iki katmanlı modelinde bir Dolaylı sağlayıcıysanız, müşteriler için abonelik oluştururken bir kurumsal bayi belirtebilirsiniz.

Müşterinin kullanılabilir tüm kurumsal bayilerini listelemek için, `<customerId>` değerini ikinci adımda kopyaladığınız `id` ile değiştirerek(```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) aşağıdaki istekte bulunun.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API yanıtında müşterinin kurumsal bayileri listelenir:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```

Abonelikle ilişkili kurumsal bayiyi belirlemek için `description` özelliğini kullanın. Kurumsal bayi için `resellerId` değerini kopyalayın. Örneğin, `Wingtip` adlı bayiyi ilişkilendirmek için `3xxxxx` değerini kopyalayın. Bir sonraki adımda kullanabilmeniz için bu değeri bir yere yapıştırın.

### <a name="create-a-subscription-for-a-customer"></a>Müşteriden abonelik oluşturma

Aşağıdaki örnekte *Fabrikam toys* için *Geliştirme Ekibi aboneliği* adlı bir abonelik oluşturulur ve *Wingtip* kurumsal bayisi abonelikle ilişkilendirilir. 

`<customerId>` değerini, ikinci adımda (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kopyaladığınız `id` ile değiştirerek aşağıdaki istekte bulunun. İkinci adımda kopyaladığınız isteğe bağlı *resellerId* değerini API'nin istek parametrelerine geçirin.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Öğe Adı  | Gerekli | Tür   | Açıklama                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Evet      | Dize | Aboneliğin görünen adı.|
| `skuId` | Evet      | Dize | Azure planının sku kimliği. Microsoft Azure Plan türündeki abonelikler için *0001* kullanın |
| `resellerId`      | Hayır       | Dize | Abonelikle ilişkilendirilecek kurumsal bayinin MPN Kimliği.  |

Yanıtta, izleme için bir `subscriptionCreationResult` nesnesi alırsınız. Abonelik oluşturma işlemi tamamlandığında `subscriptionCreationResult` nesnesi, `subscriptionLink` nesnesini döndürür. Abonelik kimliğini içerir.

## <a name="next-steps"></a>Sonraki adımlar

- .NET kullanarak Kurumsal Anlaşma (EA) aboneliği oluşturma işlemini ve örneğini görüntülemek için [GitHub’da örnek koda](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core) bakın.
* Artık aboneliği oluşturduğunuza göre, bu beceriyi diğer kullanıcılara ve hizmet sorumlularına verebilirsiniz. Daha fazla bilgi için bkz. [Azure Kurumsal abonelikleri oluşturma erişimi verme (önizlemi)](grant-access-to-create-subscription.md).
* Yönetim gruplarını kullanarak çok fazla sayıda aboneliği yönetme hakkında daha fazla bilgi için bkz. [Kaynaklarınızı Azure yönetim gruplarıyla düzenleme](../../governance/management-groups/overview.md).
