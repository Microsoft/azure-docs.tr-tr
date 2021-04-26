---
title: Azure AD kullanarak grup verilerine erişen uygulamalar için Grup sahibi onayını yapılandırma
description: Grup ve takım sahiplerinin, gruba veya ekibin verilerine erişimi olacak uygulamaları kabul edilip edilmeyeceğini yönetme hakkında bilgi edinin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: be28148aacf270f2f3cfabad4cbd5f03afa05d3b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374429"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Grup verilerine erişen uygulamalar için Grup sahibi onayını yapılandırma

Grup ve takım sahipleri, üçüncü taraf satıcılar tarafından yayımlanan uygulamalar gibi, kuruluşunuzun bir grupla ilişkili verilerinize erişebilmesi için uygulamalara yetki verebilir. Örneğin, Microsoft ekipteki bir takım sahibi, bir uygulamanın ekipteki tüm ekip iletilerini okumasına veya bir grubun üyelerinin temel profilini listeliğine izin verebilir. Daha fazla bilgi için bkz. [Microsoft ekiplerde kaynağa özel izin](/microsoftteams/resource-specific-consent) .

## <a name="manage-group-owner-consent-to-apps"></a>Grup sahibi onayını uygulamalarla yönetme

Hangi kullanıcıların gruplara veya ekiplerin verilerine erişen uygulamalara izin verileceğini yapılandırabilir veya bunu tüm kullanıcılar için devre dışı bırakabilirsiniz.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Grup verilerine erişen uygulamalara Grup sahibi onayını yönetmek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) [genel yönetici](../roles/permissions-reference.md#global-administrator)olarak oturum açın.
2. **Azure Active Directory**  >  **Kurumsal uygulamalar**  >  **onay ve izinler**  >  **Kullanıcı izin ayarları**' nı seçin.
3. **Veriye erişen uygulamalar Için Grup sahibi onayı** altında etkinleştirmek istediğiniz seçeneği belirleyin.
4. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

Bu örnekte, tüm Grup sahiplerinin grupların verilerine erişen uygulamalara izin vermesini sağlar:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Grup sahibi izin ayarları":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Grup sahiplerini, sahip oldukları gruplar için kuruluşunuzun verilerine erişen uygulamalara izin verme özelliğini etkinleştirmek veya devre dışı bırakmak için Azure AD PowerShell önizleme modülünü ( [Azureadpreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)) kullanabilirsiniz.

1. [Azureadpreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modülünü kullandığınızdan emin olun. Hem [Azuread](/powershell/module/azuread/) modülünü hem de [azureadpreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modülünü yüklediyseniz bu adım önemlidir.

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

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. Ayar değerlerini anlayın. Bir uygulamanın grubun verilerine erişmesine izin veren kullanıcıları tanımlayan iki ayar değeri vardır:

    | Ayar       | Tür         | Açıklama  |
    | ------------- | ------------ | ------------ |
    | _Enablegroupspecificonayı_   | Boole | Grup sahiplerinin gruba özel izinler verip vermeyeceğini belirten bayrak. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | _Enablegroupspecificonay_ değeri "true" olarak ayarlanırsa ve bu değer bir grubun nesne kimliği olarak ayarlandıysa, tanımlı grubun üyelerinin sahip oldukları gruplara gruba özel izinler verme yetkisine sahip olur. |

1. İstenen yapılandırma için ayar değerlerini güncelleştir:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
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

---

## <a name="next-steps"></a>Sonraki adımlar

Daha fazlasını öğrenin:

* [Kullanıcı izin ayarlarını yapılandırma](configure-user-consent.md)
* [Yönetici onayı iş akışını yapılandırma](configure-admin-consent-workflow.md)
* [Uygulama onayını yönetmeyi ve izin isteklerini değerlendirmeyi öğrenin](manage-consent-requests.md)
* [Uygulamaya kiracı genelinde yönetici onayı verme](grant-admin-consent.md)
* [Microsoft Identity platformunda izinler ve onay](../develop/v2-permissions-and-consent.md)

Yardım almak veya sorularınıza yanıt bulmak için:
* [Microsoft Q&A 'da Azure AD ](/answers/topics/azure-active-directory.html)