---
title: Azure AD kullanarak son kullanıcıların uygulamalara onay verme şeklini yapılandırma
description: Kullanıcıların kuruluşunuzun verilerine erişimi olacak uygulamaları nasıl ve ne zaman kabul edebilir.
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
ms.openlocfilehash: 95a651f6201c9f60500c9191821edb7eb76b8535
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374446"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Son kullanıcıların uygulamalara onay verme şeklini yapılandırma

Kullanıcılarınızın iş veya okul hesabıyla oturum açmalarına ve zengin veri odaklı deneyimler sunmak üzere kuruluşunuzun verilerine erişmesine izin vermek için uygulamalarınızı Microsoft Identity platformu ile tümleştirebilirsiniz.

Bir uygulamanın kuruluşunuzun verilerine erişebilmesi için, kullanıcının uygulama izinlerini vermesi gerekir. Farklı izinler farklı erişim düzeylerine izin verir. Varsayılan olarak, tüm kullanıcıların, yönetici onayı gerektirmeyen izinler için uygulamalara izin verme izni verilir. Örneğin, varsayılan olarak bir Kullanıcı, bir uygulamanın posta kutularına erişmesine izin verebilir, ancak bir uygulama sınırsız erişimine izin vermeyi, kuruluşunuzdaki tüm dosyalara okuma ve yazma izni veremez.

Kullanıcıların uygulamalara veri erişimi vermesini sağlayarak, kullanıcılar kolayca yararlı uygulamalar alabilir ve üretken olabilirler. Ancak, bazı durumlarda bu yapılandırma izlenmiyor ve denetlenmiyorsa riski temsil edebilir.

> [!IMPORTANT]
> Kötü amaçlı uygulamaların, kullanıcıların kuruluşunuzun verilerine erişim izni vermesini sağlayan riski azaltmak için, yalnızca [doğrulanmış bir yayımcı](../develop/publisher-verification-overview.md)tarafından yayımlanan uygulamalar için Kullanıcı onaylamasına izin vermenizi öneririz.

## <a name="user-consent-settings"></a>Kullanıcı izin ayarları

Uygulama onay ilkeleri, bir uygulamanın uygulamaya alınmadan önce karşılanması gereken koşulları anlatır. Bu ilkeler, uygulamaya erişim isteyen uygulamanın yanı sıra, uygulamanın istediği izinleri içerebilir.

Tüm kullanıcılar için hangi uygulama izin ilkelerinin uygulanacağını seçerek, son kullanıcılara uygulamalara izin verme izni verildiğinde ve yönetici incelemesi ve onayı istemek için ne zaman gerekli olacağı hakkında sınırlar belirleyebilirsiniz:

* **Kullanıcı onayını devre dışı bırak** -kullanıcılar uygulamalara izin veremez. Kullanıcılar, daha önce kendilerine onaylı olan veya kendi adına yöneticiler tarafından onaylanan uygulamalar üzerinde oturum açmaya devam edebilir, ancak yeni izinler veya kendi kendilerine yeni uygulamalar için izin verilmez. Yalnızca izin verme iznini içeren bir dizin rolü verilen kullanıcılar yeni uygulamaları kabul edebilir.

* **Kullanıcılar doğrulanan yayımcıların veya kuruluşunuzun uygulamalarını kabul edebilir, ancak yalnızca seçtiğiniz izinler için** -tüm kullanıcılar yalnızca, kiracınızda kayıtlı olan [doğrulanmış bir yayımcı](../develop/publisher-verification-overview.md) ve uygulamalar tarafından yayımlanan uygulamalara izin verebilir. Kullanıcılar yalnızca "düşük etki" olarak sınıflandırdığınız izinleri kabul edebilir. Kullanıcıların izin verdiği izinleri seçmek için [izinleri sınıflamak](configure-permission-classifications.md) zorundasınız.

* **Kullanıcılar tüm uygulamalara izin verebilir** -Bu seçenek, tüm kullanıcıların herhangi bir uygulama için yönetici onayı gerektirmeyen herhangi bir izni kabul etmesine izin verir.

* **Özel uygulama onayı ilkesi** -kullanıcının izin vermediği durumlar üzerinde daha fazla seçenek için, [özel uygulama onayı ilkesi oluşturabilir](manage-app-consent-policies.md#create-a-custom-app-consent-policy)ve bunları Kullanıcı izni için geçerli olacak şekilde yapılandırabilirsiniz.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal aracılığıyla Kullanıcı izin ayarlarını yapılandırmak için:

1. [Azure Portal](https://portal.azure.com) [genel yönetici](../roles/permissions-reference.md#global-administrator)olarak oturum açın.
1. **Azure Active Directory**  >  **Kurumsal uygulamalar**  >  **onay ve izinler**  >  **Kullanıcı izin ayarları**' nı seçin.
1. **Uygulamalar Için kullanıcı onayı** altında, tüm kullanıcılar için yapılandırmak istediğiniz izin ayarını seçin.
1. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Kullanıcı izin ayarları":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Uygulamalar için Kullanıcı onayını hangi uygulama onay ilkesinin yöneteceğini seçmek için, [Azureadpreview](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)' ı en son Azure AD PowerShell önizleme modülünü kullanabilirsiniz.

#### <a name="disable-user-consent"></a>Kullanıcı onayını devre dışı bırak

Kullanıcı onayını devre dışı bırakmak için, Kullanıcı Onayı ' nı boş olacak şekilde belirleyen onay ilkelerini ayarlayın:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Uygulama onay ilkesine Kullanıcı onayı konusuna izin ver

Kullanıcı onaylamasına izin vermek için, uygulamalara izin vermek üzere kullanıcıların yetkilendirmesini hangi uygulama onay ilkesinin yönetmesinin gerektiğini seçin:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

`{consent-policy-id}`Uygulamak istediğiniz ILKENIN kimliğiyle değiştirin. Oluşturduğunuz [özel bir uygulama onay ilkesini](manage-app-consent-policies.md#create-a-custom-app-consent-policy) seçebilir veya aşağıdaki yerleşik ilkelerden seçebilirsiniz:

| ID | Açıklama |
|:---|:------------|
| Microsoft-Kullanıcı-varsayılan-düşük | **Seçili izinler için doğrulanan yayımcıların uygulamalara yönelik Kullanıcı onaylamasına izin ver**<br /> Yalnızca, kiracınızda kayıtlı Yayımcılar ve uygulamalar için sınırlı kullanıcı onayı ve yalnızca "düşük etki" olarak sınıflandırdığınız izinler için izin verin. (Kullanıcıların izin verdiği izinleri seçmek için [izinleri sınıflandırmaya](configure-permission-classifications.md) unutmayın.) |
| Microsoft-Kullanıcı-varsayılan-eski | **Uygulamalar için Kullanıcı onaylamasına izin ver**<br /> Bu seçenek, tüm kullanıcıların herhangi bir uygulama için yönetici onayı gerektirmeyen tüm izinlere izin vermesini sağlar |
  
Örneğin, yerleşik ilkeye Kullanıcı onay konusunu etkinleştirmek için `microsoft-user-default-low` :

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> Kullanıcıların, kullanıcının izin vermediği bir uygulamayı (örneğin, kullanıcı onayı devre dışı bırakıldığında veya bir uygulama kullanıcının izin vermediği izinleri istemesi durumunda) izin vermesi için yönetici [onayı iş akışını etkinleştirin](configure-admin-consent-workflow.md) .

## <a name="risk-based-step-up-consent"></a>Risk tabanlı adım adım onayı

Risk tabanlı adım adım onayı, Kullanıcı tarafından [izin istekleri](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)yapan kötü amaçlı uygulamalara yönelik pozlandırmayı azaltmaya yardımcı olur. Microsoft riskli bir Son Kullanıcı onay isteği algılarsa, isteğin yerine yönetici onayı için "adım adım" gerekir. Bu özellik varsayılan olarak etkindir, ancak son kullanıcı onayı etkinleştirildiğinde yalnızca bir davranış değişikliğine neden olur.

Riskli onay isteği algılandığında, onay istemi yönetici onayının gerekli olduğunu belirten bir ileti görüntüler. [Yönetici onayı isteği iş akışı](configure-admin-consent-workflow.md) etkinleştirilirse, Kullanıcı doğrudan onay isteminden daha fazla gözden geçirmek için isteği bir yöneticiye gönderebilir. Etkin değilse, aşağıdaki ileti görüntülenir:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; , kuruluşunuzda yalnızca bir yöneticinin verebileceği kaynaklara erişmek için izne sahip olmalıdır. Kullanabilmek için önce lütfen yöneticiden bu uygulamaya izin vermesini isteyin.

Bu durumda, bir denetim olayı "ApplicationManagement" kategorisiyle, etkinlik türü "uygulamaya onay" ve "riskli uygulama algılandı" durum nedeni ile de günlüğe kaydedilir.

> [!IMPORTANT]
> Yöneticiler, özellikle Microsoft 'un risk algıladığında, isteği onaylamadan önce [tüm izin isteklerini dikkatle değerlendirmelidir](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) .

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>PowerShell kullanarak risk tabanlı adım onayını devre dışı bırakma veya yeniden etkinleştirme

Microsoft 'un riski algıladığı veya daha önce devre dışı bırakılmışsa yeniden etkinleştirebileceği durumlarda yönetici onayını gerekli hale getirebilirsiniz. Azure AD PowerShell önizleme modülünü [Azureadpreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)' ı kullanarak devre dışı bırakabilirsiniz.

1. [Azureadpreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modülünü kullandığınızdan emin olun. Hem [Azuread](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) modülünü hem de [azureadpreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modülünü yüklediyseniz bu adım önemlidir.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Azure AD PowerShell 'e bağlanın.

   ```powershell
   Connect-AzureAD
   ```

1. Kiracınızdaki **Izin Ilkesi ayarları** dizin ayarları için geçerli değeri alın. Bunun için, bu özelliğin Dizin ayarlarının oluşturulup oluşturulmadığından ve ilgili dizin ayarları şablonundaki değerler kullanılarak denetlenmesi gerekir.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Ayarlar değerini anlayın:

    | Ayar       | Tür         | Açıklama  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boole |  Riskli bir istek algılandığında Kullanıcı izninin engellenip engellenmediğini belirten bayrak. |

1. İstenen yapılandırma için güncelleştirme ayarları değeri:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

1. Ayarlarınızı kaydedin.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazlasını öğrenin:

* [Kullanıcı izin ayarlarını yapılandırma](configure-user-consent.md)
* [Uygulama onayı ilkelerini yönetme](manage-app-consent-policies.md)
* [Yönetici onayı iş akışını yapılandırma](configure-admin-consent-workflow.md)
* [Uygulama onayını yönetmeyi ve izin isteklerini değerlendirmeyi öğrenin](manage-consent-requests.md)
* [Uygulamaya kiracı genelinde yönetici onayı verme](grant-admin-consent.md)
* [Microsoft Identity platformunda izinler ve onay](../develop/v2-permissions-and-consent.md)

Yardım almak veya sorularınıza yanıt bulmak için:
* [Microsoft Q&bir Azure AD.](/answers/topics/azure-active-directory.html)