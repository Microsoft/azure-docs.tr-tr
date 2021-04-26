---
title: Azure AD 'de uygulama onay ilkelerini yönetme
description: İzin verilebileceğini denetlemek için yerleşik ve özel uygulama onay ilkelerini yönetmeyi öğrenin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 44299fadd17d1acfa292dd88bd57c8be4a44be36
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375704"
---
# <a name="manage-app-consent-policies"></a>Uygulama onayı ilkelerini yönetme

Azure AD PowerShell ile uygulama onay ilkelerini görüntüleyebilir ve yönetebilirsiniz.

Bir uygulama onay ilkesi, bir veya daha fazla "içerir" koşul kümesi ve sıfır veya daha fazla "hariç" koşul kümesi içerir. Bir olayın bir uygulama onay ilkesinde kabul edilmesi için, *en az* bir "içerme" koşulu *kümesiyle eşleşmesi ve "* dışladığı" koşul kümesiyle eşleşmemesi gerekir.

Her koşul kümesi birkaç koşuldan oluşur. Bir olayın bir koşul kümesiyle eşleşmesi için, koşul kümesindeki *Tüm* koşulların karşılanması gerekir.

KIMLIğIN "Microsoft-" ile başladığı uygulama onay ilkeleri yerleşik ilkelerdir. Bu yerleşik ilkelerden bazıları mevcut yerleşik Dizin rollerinde kullanılır. Örneğin, uygulama `microsoft-application-admin` onayı ilkesi, uygulama yöneticisinin ve bulut uygulaması Yönetici rollerinin kiracı genelinde yönetici izni verme izni verilen koşullarını açıklar. Yerleşik ilkeler özel dizin rollerinde kullanılabilir ve Kullanıcı izin ayarlarını yapılandırabilir, ancak düzenlenemez veya silinemez.

## <a name="pre-requisites"></a>Ön koşullar

1. [Azureadpreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modülünü kullandığınızdan emin olun. Hem [Azuread](/powershell/module/azuread/) modülünü hem de [azureadpreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modülünü yüklediyseniz bu adım önemlidir.

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Azure AD PowerShell 'e bağlanın.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Mevcut uygulama onay ilkelerini listeleyin

Kuruluşunuzda var olan uygulama izin ilkelerini öğrenerek başlamak iyi bir fikirdir:

1. Tüm uygulama onay ilkelerini Listele:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Bir ilkenin "içerir" koşul kümelerini görüntüleyin:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. "Hariç tutar" koşul kümelerini görüntüle:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Özel uygulama onay ilkesi oluşturma

Özel bir uygulama onay ilkesi oluşturmak için şu adımları izleyin:

1. Yeni bir boş uygulama onay ilkesi oluşturun.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. "Dahildir" koşul kümelerini ekleyin.

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Ek "Ekle" koşul kümeleri eklemek için bu adımı tekrarlayın.

1. İsteğe bağlı olarak, "dışlarsa" koşul kümelerini ekleyin.

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Ek "dışlama" koşulu kümeleri eklemek için bu adımı tekrarlayın.

Uygulama izni ilkesi oluşturulduktan sonra, bu ilkeye yönelik [Kullanıcı onaylamasına izin](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) verebilirsiniz.

## <a name="delete-a-custom-app-consent-policy"></a>Özel bir uygulama onay ilkesini silme

1. Aşağıda, özel bir uygulama onay ilkesini nasıl silebilmeniz gösterilmektedir. **Bu eylem geri alınamaz.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Silinen uygulama onay ilkeleri geri yüklenemez. Yanlışlıkla özel bir uygulama onay ilkesini silerseniz, ilkeyi yeniden oluşturmanız gerekir.

---

### <a name="supported-conditions"></a>Desteklenen koşullar

Aşağıdaki tabloda, uygulama izin ilkeleri için desteklenen koşulların listesi verilmiştir.

| Koşul | Açıklama|
|:---------------|:----------|
| PermissionClassification | Verilen iznin [izin sınıflandırması](configure-permission-classifications.md) veya "tümü", herhangi bir izin sınıflandırmasıyla (sınıflandırılmamış izinler dahil) eşleşir. Varsayılan değer "All" dır. |
| PermissionType | Verilen iznin izin türü. Temsilci izinleri için uygulama izinleri (örn. uygulama rolleri) veya "temsilci" için "uygulama" kullanın. <br><br>**Not**: "delegatedUserConsentable" DEĞERI, API yayımcısı tarafından yönetici onayı gerektirecek şekilde yapılandırılmamış izin verilen izinleri belirtir — bu değer yerleşik izin verme ilkelerinde kullanılabilir, ancak özel izin verme ilkelerinde kullanılamaz. Gereklidir. |
| ResourceApplication | Bir iznin verildiği kaynak uygulamanın (ör. API) veya herhangi bir kaynak uygulamasıyla veya API ile eşleşmek için "Any" **AppID** 'si. Varsayılan değer "Any" dır. |
| İzinler | Eşleşen belirli izinler için izin kimliklerinin listesi veya herhangi bir izinle eşleşecek şekilde tek değeri "tümü" olan bir liste. Varsayılan, tek "All" değeridir. <ul><li>Temsilci izin kimlikleri, API 'nin ServicePrincipal nesnesinin **OAuth2Permissions** özelliğinde bulunabilir.</li><li>Uygulama izin kimlikleri, API 'nin ServicePrincipal nesnesinin **Approles** özelliğinde bulunabilir.</li></ol> |
| Clientapplicationıds | İstemci uygulamalarının eşleşmesi için **AppID** değerlerinin listesi veya herhangi bir istemci uygulamasıyla eşleşmesi için tek değeri "tümü" olan bir liste. Varsayılan, tek "All" değeridir. |
| ClientApplicationTenantIds | İstemci uygulamasının kaydedildiği Azure Active Directory kiracı kimliklerinin bir listesi veya herhangi bir kiracıya kayıtlı istemci uygulamalarıyla eşleşmesi için tek değerli "tümü" olan bir liste. Varsayılan, tek "All" değeridir. |
| Clientapplicationpublisherıds | İstemci uygulamasının [doğrulanmış yayımcılarına](../develop/publisher-verification-overview.md) yönelik Microsoft iş ortağı ağı (MPN) kimliklerinin listesi veya herhangi bir yayımcının istemci uygulamalarıyla eşleşmesi için "All" tek değerli bir liste. Varsayılan, tek "All" değeridir. |
| ClientApplicationsFromVerifiedPublisherOnly | `$true`Yalnızca [doğrulanan yayımcılarla](../develop/publisher-verification-overview.md)istemci uygulamalarında eşleşecek şekilde ayarlanır. `$false`Doğrulanmış bir yayımcısı olmasa bile, herhangi bir istemci uygulamasında eşleşecek şekilde ayarlanır. `$false` varsayılan değerdir. |

## <a name="next-steps"></a>Sonraki adımlar

Daha fazlasını öğrenin:

* [Kullanıcı izin ayarlarını yapılandırma](configure-user-consent.md)
* [Yönetici onayı iş akışını yapılandırma](configure-admin-consent-workflow.md)
* [Uygulama onayını yönetmeyi ve izin isteklerini değerlendirmeyi öğrenin](manage-consent-requests.md)
* [Uygulamaya kiracı genelinde yönetici onayı verme](grant-admin-consent.md)
* [Microsoft Identity platformunda izinler ve onay](../develop/v2-permissions-and-consent.md)

Yardım almak veya sorularınıza yanıt bulmak için:
* [Microsoft Q&A 'da Azure AD](/answers/products/)