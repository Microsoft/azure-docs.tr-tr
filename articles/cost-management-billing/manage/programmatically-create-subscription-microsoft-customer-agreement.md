---
title: En son API’lerle Microsoft Müşteri Sözleşmesi için program aracılığıyla Azure abonelikleri oluşturma
description: REST API, Azure CLı, Azure PowerShell ve Azure Resource Manager şablonlarının en son sürümlerini kullanarak bir Microsoft Müşteri Sözleşmesi için programlı olarak Azure abonelikleri oluşturmayı öğrenin.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 324ca849e0f9c1282dc4b47ceba4654c76c07b35
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870832"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>En son API’lerle Microsoft Müşteri Sözleşmesi için program aracılığıyla Azure abonelikleri oluşturma

Bu makale, en son API sürümlerini kullanarak Microsoft Müşteri Sözleşmesi için program aracılığıyla Azure abonelikleri oluşturmanıza yardımcı olur. Hala eski önizleme sürümünü kullanıyorsanız [Önizleme API’leriyle program aracılığıyla Azure abonelikleri oluşturma](programmatically-create-subscription-preview.md) başlıklı makaleye bakın. 

Bu makalede Azure Resource Manager'ı kullanarak program aracılığıyla abonelik oluşturmayı öğreneceksiniz.

Program aracılığıyla Azure aboneliği oluşturduğunuzda, bu abonelik Microsoft'tan veya yetkili satıcıdan aldığınız Azure hizmetlerinin tabi olduğu sözleşmeye tabidir. Daha fazla bilgi için bkz. [Microsoft Azure Yasal Bilgiler](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

Abonelikleri oluşturmak için bir fatura bölümünde sahip, katkıda bulunan veya Azure abonelik oluşturucu rolünüz ya da faturalama profili veya faturalama hesabında sahip veya katkıda bulunan rolünüz olmalıdır. Aynı rolü bir hizmet asıl adına (SPN) de verebilirsiniz. Roller ve bunlara izin atama hakkında daha fazla bilgi için bkz. [Abonelik Faturalandırma rolleri ve görevleri](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Abonelik oluşturmak için bir SPN kullanıyorsanız, [Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) veya [Azure CLı](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_list)kullanarak Azure AD uygulama kaydının ObjectID 'sini hizmet sorumlusu ObjectID olarak kullanın. 

Bir Microsoft Müşteri Sözleşmesi hesabına erişiminizin olup olmadığını bilmiyorsanız bkz. [Microsoft Müşteri Sözleşmesi’ne erişimi denetleme](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

## <a name="find-billing-accounts-that-you-have-access-to"></a>Erişiminiz olan fatura hesaplarını bulma

Tüm faturalama hesaplarını listelemek için aşağıdaki isteği gönderin.

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
API yanıtında erişiminiz olan fatura hesapları listelenir.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Abonelikleri oluşturmak istediğiniz fatura hesabını belirlemek için `displayName` özelliğini kullanın. Hesabın agreementType ayarının *MicrosoftCustomerAgreement* olduğundan emin olun. Hesabın `name` değerini kopyalayın.  Örneğin, `Contoso` faturalama hesabı için bir abonelik oluşturmak amacıyla `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` değerini kopyalayın. Bir sonraki adımda kullanabilmeniz için bu değeri bir yere yapıştırın.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzBillingAccount
```
Erişiminiz olan tüm faturalandırma hesaplarının bir listesini geri alacaksınız 

```json
Name          : 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
DisplayName   : Contoso
AccountStatus : Active
AccountType   : Enterprise
AgreementType : MicrosoftCustomerAgreement
HasReadAccess : True
```
Abonelikleri oluşturmak istediğiniz fatura hesabını belirlemek için `displayName` özelliğini kullanın. Hesabın agreementType ayarının *MicrosoftCustomerAgreement* olduğundan emin olun. Hesabın `name` değerini kopyalayın.  Örneğin, `Contoso` faturalama hesabı için bir abonelik oluşturmak amacıyla `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` değerini kopyalayın. Bir sonraki adımda kullanabilmeniz için bu değeri bir yere yapıştırın.


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing account list
```
Erişiminiz olan tüm faturalandırma hesaplarının bir listesini geri alacaksınız 

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Enterprise",
    "agreementType": "MicrosoftCustomerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

Abonelikleri oluşturmak istediğiniz fatura hesabını belirlemek için `displayName` özelliğini kullanın. Hesabın agreementType ayarının *MicrosoftCustomerAgreement* olduğundan emin olun. Hesabın `name` değerini kopyalayın.  Örneğin, `Contoso` faturalama hesabı için bir abonelik oluşturmak amacıyla `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` değerini kopyalayın. Bir sonraki adımda kullanabilmeniz için bu değeri bir yere yapıştırın.

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Abonelik oluşturmak için faturalama profillerini ve fatura bölümlerini bulma

Aboneliğinizin ücretleri, faturalama profilinin faturasının bir bölümünde gösterilir. Azure aboneliklerini oluşturma izniniz olan faturalama profillerinin ve fatura bölümlerinin listesini almak için aşağıdaki API’yi kullanın.

İlk olarak, erişiminiz olan faturalandırma hesabı altında faturalandırma profillerinin listesini alırsınız ( `name` önceki adımdan aldığınız ' i kullanın)

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
API yanıtında, abonelik oluşturma erişiminiz olan tüm faturalama profilleri listelenir:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 Daha sonra, faturalama profilinin altındaki fatura bölümlerini belirlemek için `id` değerini kopyalayın. Örneğin, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` değerini kopyalayın ve aşağıdaki API’yi çağırın.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>Yanıt

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

Abonelikleri oluşturmak istediğiniz fatura bölümünü belirlemek için `id` özelliğini kullanın. Dizenin tamamını kopyalayın. Örneğin, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzBillingProfile -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

Yanıtın bir parçası olarak bu hesap altında faturalandırma profillerinin listesini alacaksınız.

```json
Name              : AW4F-xxxx-xxx-xxx
DisplayName       : Contoso Billing Profile
Currency          : USD
InvoiceDay        : 5
InvoiceEmailOptIn : True
SpendingLimit     : Off
Status            : Active
EnabledAzurePlans : {0002, 0001}
HasReadAccess     : True
BillTo            :
CompanyName       : Contoso
AddressLine1      : One Microsoft Way
AddressLine2      : 
City              : Redmond
Region            : WA
Country           : US
PostalCode        : 98052
```

`name`Yukarıdaki yanıtın fatura profili ' ne göz önünde. Sonraki adım, bu fatura profilinin altına erişim sahibi olduğunuz fatura bölümünü edinirsiniz. `name`Faturalandırma hesabı ve faturalandırma profilinin olması gerekir

```azurepowershell
Get-AzInvoiceSection -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx -BillingProfileName AW4F-xxxx-xxx-xxx
```

Fatura bölümünün geri döndürüldüğünü alacaksınız

```json
Name        : SH3V-xxxx-xxx-xxx
DisplayName : Development
```

`name`Yukarıdaki, altında bir abonelik oluşturmanız için gereken fatura bölümü adıdır. "/Providers/Microsoft.Billing/billingAccounts/ <BillingAccountName> /Billingprofiles/ <BillingProfileName> /ınvoicesections/" biçimini kullanarak faturalandırma kapsamınızı oluşturun <InvoiceSectionName> . Bu örnekte, bu değer ile eşolur `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"` .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing profile list --account-name "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --expand "InvoiceSections"
```

Bu API, belirtilen faturalandırma hesabı altında faturalandırma profilleri ve fatura bölümlerinin listesini döndürür.

```json
[
  {
    "billTo": {
      "addressLine1": "One Microsoft Way",
      "addressLine2": "",
      "addressLine3": null,
      "city": "Redmond",
      "companyName": "Contoso",
      "country": "US",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": "98052",
      "region": "WA"
    },
    "billingRelationshipType": "Direct",
    "currency": "USD",
    "displayName": "Contoso Billing Profile",
    "enabledAzurePlans": [
      {
        "skuDescription": "Microsoft Azure Plan for DevTest",
        "skuId": "0002"
      },
      {
        "skuDescription": "Microsoft Azure Plan",
        "skuId": "0001"
      }
    ],
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
    "indirectRelationshipInfo": null,
    "invoiceDay": 5,
    "invoiceEmailOptIn": true,
    "invoiceSections": {
      "hasMoreResults": false,
      "value": [
        {
          "displayName": "Field_Led_Test_Ace",
          "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
          "labels": null,
          "name": "SH3V-xxxx-xxx-xxx",
          "state": "Active",
          "systemId": "SH3V-xxxx-xxx-xxx",
          "targetCloud": null,
          "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
        }
      ]
    },
    "name": "AW4F-xxxx-xxx-xxx",
    "poNumber": null,
    "spendingLimit": "Off",
    "status": "Warned",
    "statusReasonCode": "PastDue",
    "systemId": "AW4F-xxxx-xxx-xxx",
    "targetClouds": [],
    "type": "Microsoft.Billing/billingAccounts/billingProfiles"
  }
]
```
`id`Abonelik oluşturmak istediğiniz fatura bölümünü belirlemek için fatura bölüm nesnesi altındaki özelliğini kullanın. Dizenin tamamını kopyalayın. Örneğin,/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/ınvoicesections/SH3V-xxxx-xxx-xxx.

---

## <a name="create-a-subscription-for-an-invoice-section"></a>Fatura bölümü için aboneliği oluşturma

Aşağıdaki örnekte, *Geliştirme* fatura bölümü için *Geliştirme Ekibi aboneliği* adlı bir abonelik oluşturulur. Abonelik *Contoso Faturalama Profiline* faturalanır ve faturanın *Geliştirme* bölümünde görüntülenir. Önceki adımda kopyalanan faturalama kapsamını kullanırsınız: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>İstek gövdesi

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```

### <a name="response"></a>Yanıt

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

`"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"` faturalama kapsamını ve aşağıdaki [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) komutunu çalıştırın. 

```azurepowershell
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload 'Production"
```

Komutun yanıtının bir parçası olarak subscriptionId bilgisini alırsınız.

```json
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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

İlk olarak, `az extension add --name account` ve `az extension add --name alias` komutlarını çalıştırarak uzantıyı yükleyin.

Aşağıdaki [az account alias create](/cli/azure/account/alias#az_account_alias_create) komutunu çalıştırın.

```azurecli
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
```

Komutun yanıtının bir parçası olarak subscriptionId bilgisini alırsınız.

```json
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

Aşağıdaki şablon bir abonelik oluşturur. İçin `billingScope` Fatura bölüm kimliğini belirtin. İçin `targetManagementGroup` , aboneliğini oluşturmak istediğiniz yönetim grubunu belirtin.

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
        "value": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
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

```azurepowershell
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx' targetManagementGroup=mg2
```

---

## <a name="next-steps"></a>Sonraki adımlar

* Artık aboneliği oluşturduğunuza göre, bu beceriyi diğer kullanıcılara ve hizmet sorumlularına verebilirsiniz. Daha fazla bilgi için bkz. [Azure Kurumsal abonelikleri oluşturma erişimi verme (önizlemi)](grant-access-to-create-subscription.md).
* Yönetim gruplarını kullanarak çok fazla sayıda aboneliği yönetme hakkında daha fazla bilgi için bkz. [Kaynaklarınızı Azure yönetim gruplarıyla düzenleme](../../governance/management-groups/overview.md).
* Bir aboneliğin yönetim grubunu değiştirmek için bkz. [abonelikleri taşıma](../../governance/management-groups/manage.md#move-subscriptions).
