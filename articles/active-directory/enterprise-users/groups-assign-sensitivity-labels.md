---
title: Gruplara duyarlılık etiketleri atama-Azure AD | Microsoft Docs
description: Gruplara duyarlılık etiketleri atamayı öğrenin. Sorun giderme bilgilerini görün ve kullanılabilir ek kaynakları görüntüleyin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a78cf9b7d78078030ac0db8bd2f0fddb93a8dda4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881405"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>Azure Active Directory Microsoft 365 gruplara duyarlılık etiketleri atama

Azure Active Directory (Azure AD), [Microsoft 365 Uyumluluk Merkezi](https://sip.protection.office.com/homepage) tarafından yayımlanan duyarlılık etiketlerini Microsoft 365 gruplarına uygulamayı destekler. Duyarlılık etiketleri Outlook, Microsoft ekipleri ve SharePoint gibi hizmetler arasında grup için geçerlidir. Microsoft 365 Apps desteği hakkında daha fazla bilgi için bkz. [duyarlık etiketleri için Microsoft 365 desteği](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> Bu özelliği yapılandırmak için, Azure AD kuruluşunuzda en az bir etkin Azure Active Directory Premium P1 Lisansı olmalıdır.

## <a name="enable-sensitivity-label-support-in-powershell"></a>PowerShell 'de duyarlılık etiketi desteğini etkinleştir

Yayınlanmış etiketleri gruplara uygulamak için, önce özelliği etkinleştirmeniz gerekir. Bu adımlar, Azure AD 'deki özelliği etkinleştirir.

1. Bilgisayarınızda bir Windows PowerShell penceresi açın. Bunu yükseltilmiş ayrıcalıklar olmadan açabilirsiniz.
1. Ortamı cmdlet'leri çalıştırmaya hazır hale getirmek için aşağıdaki komutları çalıştırın.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    **Hesabınızda oturum açın** sayfasında, hizmetinize bağlanmak için yönetici hesabınızı ve parolanızı girin ve **oturum aç**' ı seçin.
1. Azure AD kuruluşu için geçerli grup ayarlarını getirin.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Bu Azure AD kuruluşu için hiçbir Grup ayarı oluşturulmadıysa, yukarıdaki cmdlet 'te "bağımsız değişken null olduğu için ' ID ' parametresine bağlanamaz" hatasını okuyan bir hata alırsınız. Bu durumda, önce ayarları oluşturmanız gerekir. Bu Azure AD kuruluşunun grup ayarlarını oluşturmak için [Grup ayarlarını yapılandırmak üzere Azure Active Directory cmdlet 'lerinde](../enterprise-users/groups-settings-cmdlets.md) adımları izleyin.

1. Sonra, geçerli grup ayarlarını görüntüleyin.

    ```PowerShell
    $Setting.Values
    ```

1. Sonra özelliği etkinleştirin:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Ardından, değişiklikleri kaydedin ve ayarları uygulayın:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Ayrıca, duyarlılık etiketlerinizi Azure AD ile eşitlemeniz gerekir. Yönergeler için bkz. [kapsayıcılar için duyarlılık etiketlerini etkinleştirme ve etiketleri senkronize etme](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels).

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Azure portal yeni bir gruba etiket atama

1. [Azure AD Yönetim merkezinde](https://aad.portal.azure.com)oturum açın.
1. **Gruplar**' ı seçin ve ardından **Yeni Grup**' u seçin.
1. **Yeni Grup** sayfasında **Office 365**' i seçin ve ardından yeni grup için gerekli bilgileri doldurun ve listeden bir duyarlılık etiketi seçin.

   ![Yeni Gruplar sayfasında duyarlılık etiketi atama](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Değişikliklerinizi kaydedin ve **Oluştur**' u seçin.

Grubunuz oluşturulur ve seçili etiketle ilişkili site ve grup ayarları otomatik olarak zorlanır.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Azure portal var olan bir gruba etiket atama

1. [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) gruplar yönetici hesabıyla veya grup sahibi olarak oturum açın.
1. **Grupları** seçin.
1. **Tüm gruplar** sayfasında, etiketlemek istediğiniz grubu seçin.
1. Seçili grubun sayfasında, **Özellikler** ' i seçin ve listeden bir duyarlılık etiketi seçin.

   ![Bir grup için genel bakış sayfasında duyarlılık etiketi atama](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Değişikliklerinizi kaydetmek için **Kaydet** seçeneğini belirleyin.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Azure portal var olan bir gruptan bir etiketi kaldır

1. [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) genel yönetici veya gruplar yönetici hesabıyla veya grup sahibi olarak oturum açın.
1. **Grupları** seçin.
1. **Tüm gruplar** sayfasında, etiketi kaldırmak istediğiniz grubu seçin.
1. **Grup** sayfasında, **Özellikler**' i seçin.
1. **Kaldır**' ı seçin.
1. Değişikliklerinizi uygulamak için **Kaydet**’i seçin.

## <a name="using-classic-azure-ad-classifications"></a>Klasik Azure AD sınıflandırmalarını kullanma

Bu özelliği etkinleştirdikten sonra, gruplar için "klasik" sınıflandırmalar yalnızca var olan gruplar ve siteler görünür ve yalnızca duyarlılık etiketlerini desteklemeyen uygulamalarda gruplar oluşturuyorsanız bu grupları yeni gruplar için kullanmanız gerekir. Yöneticiniz, gerekirse daha sonra duyarlık etiketlerine dönüştürebilir. Klasik sınıflandırmalar, `ClassificationList` Azure AD PowerShell 'de ayar için değerleri tanımlayarak ayarladığınız eski sınıflandırmalardır. Bu özellik etkinleştirildiğinde, bu sınıflandırmalar gruplara uygulanmaz.

## <a name="troubleshooting-issues"></a>Sorun giderme

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Duyarlılık etiketleri bir grupta atama için kullanılamaz

Duyarlılık etiketi seçeneği yalnızca aşağıdaki koşulların tümü karşılandığında gruplar için görüntülenir:

1. Etiketler, bu Azure AD kuruluşu için Microsoft 365 Uyumluluk Merkezi 'nde yayımlanır.
1. Özellik etkin, Azure AD PowerShell modülündeki Enablemıplas ' de true olarak ayarlandı.
1. LABLES, güvenlik & uyumluluğu PowerShell modülündeki Execute-AzureAdLabelSync cmdlet 'i ile Azure AD ile eşitlenir.
1. Grup bir Microsoft 365 grubudur.
1. Kuruluşun etkin bir Azure Active Directory Premium P1 lisansı vardır.
1. Geçerli oturum açan kullanıcının etiket atamak için yeterli ayrıcalıkları vardır. Kullanıcının bir genel yönetici, Grup Yöneticisi veya grup sahibi olması gerekir.

Bir gruba etiket atamak için lütfen tüm koşulların karşılandığından emin olun.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Atamak istediğim etiket listede değil

Aradığınız etiket listede yoksa, bu durum aşağıdaki nedenlerden biri olabilir:

- Etiket Microsoft 365 Uyumluluk Merkezi 'nde yayımlanmayabilir. Bu, artık yayımlanmayacak Etiketler için de uygulanabilir. Daha fazla bilgi için lütfen yöneticinize başvurun.
- Etiket yayımlanmayabilir, ancak oturum açmış olan kullanıcı tarafından kullanılamaz. Etikete erişim alma hakkında daha fazla bilgi için lütfen yöneticinize başvurun.

### <a name="how-to-change-the-label-on-a-group"></a>Bir gruptaki etiketi değiştirme

Etiketler, mevcut bir gruba etiket atama ile aynı adımları kullanarak herhangi bir zamanda değiştirilebilir:

1. Küresel veya grup yöneticisi hesabı veya grup sahibi olarak [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
1. **Grupları** seçin.
1. **Tüm gruplar** sayfasında, etiketlemek istediğiniz grubu seçin.
1. Seçili grubun sayfasında, **Özellikler** ' i seçin ve listeden yeni bir duyarlılık etiketi seçin.
1. **Kaydet**’i seçin.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Yayınlanmış etiketlere yapılan grup ayarı değişiklikleri gruplar üzerinde güncelleştirilmiyor

En iyi uygulama olarak, etiket gruplara uygulandıktan sonra bir etiketin grup ayarlarını değiştirmenizi önermiyoruz. [Microsoft 365 Uyumluluk Merkezi](https://sip.protection.office.com/homepage)'nde yayımlanan etiketlerle ilişkili Grup ayarlarında değişiklik yaptığınızda, bu ilke değişiklikleri etkilenen gruplara otomatik olarak uygulanmaz.

Değişiklik yapmanız gerekiyorsa, etkilenen gruplara el ile güncelleştirmeleri uygulamak için bir [Azure AD PowerShell betiği](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) kullanın. Bu yöntem, var olan tüm grupların yeni ayarı zorlayacağı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft ekipleri, Microsoft 365 grupları ve SharePoint siteleri ile duyarlılık etiketleri kullanma](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Azure AD PowerShell betiği ile etiket ilkesi el ile değiştirildikten sonra grupları güncelleştirme](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Grup ayarlarınızı düzenleme](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [PowerShell komutlarını kullanarak grupları yönetme](../enterprise-users/groups-settings-v2-cmdlets.md)
