---
title: En son API’lerle program aracılığıyla Azure Kurumsal Anlaşma abonelikleri oluşturma
description: REST API, Azure CLı, Azure PowerShell ve Azure Resource Manager şablonlarının en son sürümlerini kullanarak Azure Kurumsal Anlaşma aboneliklerini programlı bir şekilde oluşturmayı öğrenin.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: e57f385dce6446ebb3aa2df0ceb48f97a7e0c2f4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877919"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>En son API’lerle program aracılığıyla Azure Kurumsal Anlaşma abonelikleri oluşturma

Bu makale, en son API sürümlerini kullanarak program aracılığıyla Azure Kurumsal Anlaşma (EA) fatura hesabı için Azure EA abonelikleri oluşturmanıza yardımcı olur. Hala eski önizleme sürümünü kullanıyorsanız [Önizleme API’leriyle program aracılığıyla Azure abonelikleri oluşturma](programmatically-create-subscription-preview.md) başlıklı makaleye bakın. 

Bu makalede Azure Resource Manager'ı kullanarak program aracılığıyla abonelik oluşturmayı öğreneceksiniz.

Program aracılığıyla Azure aboneliği oluşturduğunuzda, bu abonelik Microsoft'tan veya yetkili satıcıdan aldığınız Azure hizmetlerinin tabi olduğu sözleşmeye tabidir. Daha fazla bilgi için bkz. [Microsoft Azure Yasal Bilgiler](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Abonelik oluşturmak için Kayıt Hesabında Sahip rolünüzün olması gerekir. Rolü almanın iki yolu vardır:

* Kaydınızın Kuruluş Yöneticisi [sizi Hesap Sahibi yapabilir](https://ea.azure.com/helpdocs/addNewAccount) (oturum açma gereklidir) ve bu durumda Kayıt Hesabının Sahibi olursunuz.
* Kayıt Hesabının mevcut Sahibi [size erişim verebilir](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Benzer şekilde, EA aboneliğini oluştururken bir hizmet sorumlusu kullanmak için [söz konusu hizmet sorumlusuna abonelik oluşturabilme becerisi](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) vermelisiniz.  
    Abonelik oluşturmak için bir SPN kullanıyorsanız, [Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) veya [Azure CLı](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_list)kullanarak Azure AD uygulama kaydının ObjectID 'sini hizmet sorumlusu ObjectID olarak kullanın.
  > [!NOTE]
  > Kayıt hesabı sahip izinleri vermek için doğru API sürümünü kullandığınızdan emin olun. Bu makale ve makalede belgelenen API'ler için [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) API'sini kullanın. Daha yeni API'leri kullanmaya geçiyorsanız [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) kullanarak yeniden sahip izni vermeniz gerekir. [2015-07-01 version](grant-access-to-create-subscription.md) ile yapılmış olan önceki yapılandırmanız daha yeni API'lerle çalışacak şekilde otomatik olarak dönüştürülmez.

## <a name="find-accounts-you-have-access-to"></a>Erişiminiz olan hesapları bulma

Siz Hesap Sahibi ile ilişkilendirilmiş bir Kayıt Hesabına eklendikten sonra, Azure hesabın kayıtla ilişkisini kullanarak abonelik ücretlerinin nereye faturalanacağını saptar. Hesap kapsamında oluşturulan tüm abonelikler hesabın içinde yer aldığı EA kaydına faturalanır. Abonelikleri oluşturmak için, aboneliğin sahibi olacak kayıt hesabı hakkındaki değerleri ve kullanıcı asıl adlarını geçirmeniz gerekir.

Aşağıdaki komutları çalıştırmak için Hesap Sahibinin *giriş dizininde* oturum açmalısınız; bu, varsayılan olarak aboneliklerin oluşturulduğu dizindir.

### <a name="rest"></a>[REST](#tab/rest)

Erişiminiz olan tüm kayıt hesaplarını listeleme isteği:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API yanıtında erişiminiz olan tüm kayıt hesapları listelenir:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

Bir faturalandırma kapsamının değerleri ve `id` aynı şeydir. Kayıt hesabınızın `id` değeri, abonelik isteğinin başlatıldığı faturalama kapsamıdır. ID değerini bilmek önemlidir çünkü bu değer daha sonra makalede abonelik oluşturmak için kullanacağınız gerekli bir parametredir.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Lütfen bu değeri almak için Azure CLı veya REST API kullanın.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Erişiminiz olan tüm kayıt hesaplarını listeleme isteği:

```azurecli-interactive
> az billing account list
```

Yanıt, erişiminiz olan tüm kayıt hesaplarını listeler

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

Bir faturalandırma kapsamının değerleri ve `id` aynı şeydir. Kayıt hesabınızın `id` değeri, abonelik isteğinin başlatıldığı faturalama kapsamıdır. ID değerini bilmek önemlidir çünkü bu değer daha sonra makalede abonelik oluşturmak için kullanacağınız gerekli bir parametredir.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Belirli bir kayıt hesabı altında abonelik oluşturma

Aşağıdaki örnekte, önceki adımda seçilen kayıt hesabında *Dev Team Subscription* adlı bir abonelik oluşturulur. 

### <a name="rest"></a>[REST](#tab/rest)

Abone oluşturma isteği/diğer adı oluşturmak için PUT API’yi çağırın.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

İstek gövdesinde, `enrollmentAccounts` hesaplarınızın birindeki `id` bilgisini `billingScope` olarak sağlayın.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

`Workload` için izin verilen değerler `Production` ve `DevTest` değerleridir.

#### <a name="response"></a>Yanıt

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

İsteğin durumunu almak için aynı URL’de bir GET işlemi de yapabilirsiniz.

### <a name="request"></a>İstek

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Yanıt

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

Sürüyor durumu, `provisioningState` altında `Accepted` durumu olarak döndürülür.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Modülün `New-AzSubscriptionAlias` cmdlet'ini içeren en son sürümünü yüklemek için `Install-Module Az.Subscription` çalıştırın. PowerShellGet'in yeni bir sürümünü yüklemek için [Get PowerShellGet Modülü](/powershell/scripting/gallery/installing-psget)'ne bakın.

`"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"` faturalama kapsamını kullanarak aşağıdaki [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) komutunu çalıştırın. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

Komutun yanıtının bir parçası olarak subscriptionId bilgisini alırsınız.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

İlk olarak, `az extension add --name account` ve `az extension add --name alias` komutlarını çalıştırarak uzantıyı yükleyin.

Aşağıdaki [az account alias create](/cli/azure/account/alias#az_account_alias_create) komutunu çalıştırıp `enrollmentAccounts` hesaplarınızdan birindeki `billing-scope` ve `id` bilgilerini sağlayın. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

Komutun yanıtının bir parçası olarak subscriptionId bilgisini alırsınız.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="use-arm-template"></a>ARM şablonu kullanma

Önceki bölümde PowerShell, CLı veya REST API nasıl bir abonelik oluşturulacağı gösterildi. Abonelik oluşturmayı otomatikleştirmeniz gerekiyorsa Azure Resource Manager şablonu kullanmayı düşünün (ARM şablonu).

Aşağıdaki şablon bir abonelik oluşturur. İçin `billingScope` , kayıt hesap kimliğini belirtin. İçin `targetManagementGroup` , aboneliğini oluşturmak istediğiniz yönetim grubunu belirtin.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        },
        "targetManagementGroup": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the target management group to place the subscription."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]",
                "managementGroupId": "[tenantResourceId('Microsoft.Management/managementGroups/', parameters('targetManagementGroup'))]"
            }
        }
    ],
    "outputs": {}
}
```

Şablonu [Yönetim grubu düzeyinde](../../azure-resource-manager/templates/deploy-to-management-group.md)dağıtın.

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

İstek gövdesi ile:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      },
      "targetManagementGroup": {
        "value": "mg2"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321' targetManagementGroup=mg2
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure Kurumsal aboneliği oluşturma API'sinin sınırlamaları

- Bu API kullanılarak yalnızca Azure Kurumsal abonelikleri oluşturulabilir.
- Kayıt hesabı başına 2000 abonelik sınırı vardır. Hesap için bu sayıyı aşan abonelikler yalnızca Azure portalda oluşturulabilir. API aracılığıyla daha fazla abonelik oluşturmak için başka bir kayıt hesabı oluşturun. İptal edilen, silinen ve aktarılan aboneliklerin sayısı en fazla 2000 olabilir.
- Hesap Sahibi olmayan ama Azure RBAC yoluyla kayıt hesabına eklenmiş olan kullanıcılar Azure portalda abonelik oluşturamaz.
- İçinde aboneliğin oluşturulacağı kiracıyı seçemezsiniz. Abonelik her zaman Hesap Sahibinin giriş kiracısında oluşturulur. Aboneliği farklı bir kiracılığa taşımak için [abonelik kiracısını değiştirme](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) konusuna bakın.


## <a name="next-steps"></a>Sonraki adımlar

* Artık aboneliği oluşturduğunuza göre, bu beceriyi diğer kullanıcılara ve hizmet sorumlularına verebilirsiniz. Daha fazla bilgi için bkz. [Azure Kurumsal abonelikleri oluşturma erişimi verme (önizlemi)](grant-access-to-create-subscription.md).
* Yönetim gruplarını kullanarak çok fazla sayıda aboneliği yönetme hakkında daha fazla bilgi için bkz. [Kaynaklarınızı Azure yönetim gruplarıyla düzenleme](../../governance/management-groups/overview.md).
* Bir aboneliğin yönetim grubunu değiştirmek için bkz. [abonelikleri taşıma](../../governance/management-groups/manage.md#move-subscriptions).
