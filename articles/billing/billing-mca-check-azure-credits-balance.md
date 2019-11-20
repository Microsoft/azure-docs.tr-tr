---
title: Microsoft Müşteri Sözleşmesi için Azure kredi bakiyesini izleme
description: Microsoft Müşteri Sözleşmesi için Azure kredi bakiyesinin nasıl denetleneceğini öğrenin.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375574"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Microsoft Müşteri Sözleşmesi Azure kredi bakiyesini izleme

Azure portalında Microsoft Müşteri Sözleşmesi için ödeme hesabınızın Azure kredi bakiyesini denetleyebilirsiniz. 

Kredilere uygun olan ücretleri ödemek için kredileri kullanırsınız. Kredilere uygun olmayan ürünleri kullandığınızda veya kullanımınız kredi bakiyenizi aştığında ücretlendirilirsiniz. Daha fazla bilgi için bkz. [Azure kredileri kapsamında olmayan ürünler](#products-that-arent-covered-by-azure-credits).

Bir Microsoft Müşteri Sözleşmesi ödeme hesabında, krediler bir faturalama profiline atanır. Her faturalama profilinin kendi kredileri vardır. Bir faturalama profilinin Azure kredisi bakiyesini görüntülemek için faturalama profilinde sahip, katkıda bulunan, okuyucu veya fatura yöneticisi rolüne ya da ödeme hesabında sahip, katkıda bulunan veya okuyucu rolüne sahip olmanız gerekir. Roller hakkında daha fazla bilgi almak için bkz. [Azure’daki Microsoft Müşteri Sözleşmesi yönetici rollerini anlama](billing-understand-mca-roles.md).

Bu makale, Microsoft Müşteri Sözleşmesi’ne ilişkin ödeme hesabı için geçerlidir. [Microsoft Müşteri Sözleşmesi’ne erişiminiz olup olmadığını denetleyin](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Azure portalında kredi bakiyenizi denetleme

1. [Azure Portal]( https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

    ![Portalda maliyet yönetimi + faturalama aramasını gösteren ekran görüntüsü](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Soldan **Azure kredileri**’ni seçin. Erişiminize bağlı olarak, bir faturalama hesabı veya faturalama profili seçmeniz ve sonra **Azure kredileri**’ni seçmeniz gerekebilir.

4. Azure kredileri sayfasında aşağıdaki bilgiler görüntülenir:

   ![Bir faturalama profili için kredi bakiyesinin ve işlemlerin ekran görüntüsü](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Sözleşme Dönemi               | Tanım                           |
   |--------------------|--------------------------------------------------------|
   | Tahmini bakiye  | Tüm faturalanan ve beklemedeki işlemler dikkate alındıktan sonra sahip olduğunuz tahmini kredi tutarı |
   | Geçerli bakiye    | Son faturanızdan itibaren kredi tutarı. Beklemedeki işlemleri içermez |
   | İşlemler       | Azure kredi bakiyenizi etkileyen faturalama işlemleri |

   Tahmini bakiyeniz 0’a indiğinde, kredilere uygun olan ürünler de dahil olmak üzere tüm kullanımınız için ücret yansıtılır.

6. Faturalama profilinize ilişkin kredilerin listesini görüntülemek için **Kredi listesi**’ni seçin. Kredi listesi şu bilgileri sağlar:

   ![Faturalama profili için kredi listelerinin ekran görüntüsü](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Sözleşme Dönemi | Tanım |
   |---|---|
   | Kaynak | Kredinin alım kaynağı |
   | Başlangıç tarihi | Krediyi aldığınız tarih |
   | Son kullanma tarihi | Kredi süresinin dolacağı tarih |
   | Geçerli bakiye | Son faturanızdaki bakiye |
   | Orijinal tutar | Orijinal kredi tutarı |
   | Durum | Kredinin geçerli durumu. Durum etkin, kullanıldı, süresi doldu veya süresi doluyor olabilir |

## <a name="check-your-credit-balance-programmatically"></a>Kredi bakiyenizi programlama yoluyla denetleme

Faturalandırma hesabınıza ait kredi bakiyesini programlama yoluyla almak için [Azure Faturalama](https://docs.microsoft.com/rest/api/billing/) ve [Tüketim](https://docs.microsoft.com/rest/api/consumption/) API'lerini kullanabilirsiniz.

Aşağıda gösterilen örnekler REST API'lerini kullanır. Şu anda PowerShell ve Azure CLI desteklenmemektedir.

### <a name="find-billing-profiles-you-have-access-to"></a>Erişiminizin olduğu faturalama profillerini bulma

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
API yanıtı, ödeme hesapları ile faturalama profillerinin bir listesini döndürür.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Kredi bakiyesini denetlemek istediğiniz faturalama profilini belirlemek için faturalama profilinin `displayName` özelliğini kullanın. Faturalama profilinin `id` değerini kopyalayın. Örneğin, **Geliştirme** faturalama profilinin kredi bakiyesini denetlemek istiyorsanız ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx``` değerini kopyalayın. Bir sonraki adımda kullanabilmeniz için bu değeri bir yere yapıştırın.

### <a name="get-azure-credit-balance"></a>Azure kredi bakiyesini alma 

`<billingProfileId>` değerini, ilk adımda (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```) kopyaladığınız `id` ile değiştirerek aşağıdaki istekte bulunun. 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

API yanıtı, faturalama profili için tahmini ve geçerli bakiyeyi döndürür.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Öğe adı  | Açıklama                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Tüm faturalanan ve beklemedeki işlemler dikkate alındıktan sonra sahip olduğunuz tahmini kredi tutarı. |
| `currentBalance`   | Son faturanızdan itibaren kredi tutarı. Beklemedeki işlemleri içermez.    |
| `pendingCreditAdjustments`      | Henüz faturalandırılmayan para iadeleri gibi düzeltmeler.  |
| `expiredCredit`      |  Son faturanızdan bu yana süresi dolmuş kredi.  |
| `pendingEligibleCharges`  | Henüz faturalandırılmayan, krediye uygun ücretler.   |

### <a name="get-list-of-credits"></a>Kredi listesini alma

`<billingProfileId>` değerini, ilk adımda (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```) kopyaladığınız `id` ile değiştirerek aşağıdaki istekte bulunun. 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
API yanıtı, bir faturalama profiline ait Azure kredilerinin listesini döndürür.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Öğe adı  | Açıklama                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Kredinin başlangıçtaki miktarı. |
| `closedBalance`   | Son faturadaki bakiye.    |
| `source`      | Krediyi kimin, nasıl edindiğini tanımlayan kaynak. |
| `startDate`      |  Kredinin etkin olduğu tarih.  |
| `expirationDate`  | Kredi süresinin dolacağı tarih.   |
| `poNumber`  | Kredi faturasındaki satın alma siparişi numarası.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Kredi bakiyenizi etkileyen işlemleri alma

`<billingProfileId>` değerini, ilk adımda (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```) kopyaladığınız `id` ile değiştirerek aşağıdaki istekte bulunun. İstediğiniz döneme ait işlemleri almak için bir **startDate** ve bir **endDate** geçirmeniz gerekir.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
API yanıtı, faturalama profilinizin kredi bakiyesini etkileyen tüm işlemleri döndürür.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Öğe adı  | Açıklama                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | İşlemin gerçekleştiği tarih. |
| `description` | İşlemin açıklaması. |
| `adjustments`   | İşlem için kredi düzeltmeleri.    |
| `creditExpired`      | Süresi dolmuş kredi miktarı. |
| `charges`      |  İşlemin ücretleri.  |
| `closedBalance`  | İşlemden sonraki bakiye.   |
| `eventType`  | İşlem türü.   |
| `invoiceNumber`  | İşlem faturasının fatura numarası. Beklemedeki işlem için boş olacaktır.   |

## <a name="how-credits-are-used"></a>Kredilerin kullanılma şekli

Bir Microsoft müşteri sözleşmesi için ödeme hesabında, faturalarınızı ve ödeme yöntemlerinizi yönetmek için faturalama profillerini kullanırsınız. Her faturalama profili için aylık fatura oluşturulur ve faturayı ödemek için ödeme yöntemlerini kullanırsınız.

Edindiğiniz kredileri bir faturalama profiline atarsınız. Faturalama profili için bir fatura oluşturulduğunda krediler, ödemeniz gereken tutarı hesaplamak için toplam ücretlere otomatik olarak eklenir. Kalan tutarı, çek/havale veya kredi kartı gibi ödeme yöntemlerinizle ödersiniz.

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure kredileri kapsamında olmayan ürünler

 Aşağıdaki ürünler, Azure kredileriniz kapsamında değildir. Kredi bakiyenize bakılmaksızın bu ürünlerin kullanımı için ücretlendirilirsiniz:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Kayıtlı Kullanıcı
- Openlogic
- Uzaktan Erişim Hakları XenApp Essentials Kayıtlı Kullanıcı
- Ubuntu Advantage
- Visual Studio Enterprise (Aylık)
- Visual Studio Enterprise (Yıllık)
- Visual Studio Professional (Aylık)
- Visual Studio Professional (Yıllık)
- Azure Market ürünleri
- Azure destek planları

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun.

Yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Müşteri Sözleşmesi için ödeme hesabını anlama](billing-mca-overview.md)
- [Microsoft Müşteri Sözleşmesi faturanızdaki terimleri anlama](billing-mca-understand-your-invoice.md)
