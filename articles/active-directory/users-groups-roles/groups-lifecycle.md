---
title: Office 365 grupları için süre sonu ayarla-Azure Active Directory | Microsoft Docs
description: Azure Active Directory 'de Office 365 grupları için süre sonu ayarlama
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 10/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ea0f9e07b476459824c8166781f549d788185ba
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024780"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Office 365 grupları için süre sonu ilkesini yapılandırma

Bu makalede, Office 365 gruplarının yaşam döngüsünü yönetme hakkında size bir süre sonu ilkesi ayarlayarak nasıl yönetileceği açıklanır. Yalnızca Azure Active Directory (Azure AD) içindeki Office 365 grupları için süre sonu ilkesi ayarlayabilirsiniz.

Bir grubu sona ermek üzere ayarladıktan sonra:

- Kullanıcı etkinlikleri olan gruplar, süre sonu yaklaştığında otomatik olarak yenilenir
- Grubun sahipleri, Grup otomatik yenilenmediğinde grubu yenilemek için bilgilendirilir
- Yenilenmeyen tüm gruplar silinir
- Silinen Office 365 Grubu, Grup sahipleri veya yönetici tarafından 30 gün içinde geri yüklenebilir

Bir Azure AD kuruluşunda tüm Office 365 grupları için şu anda yalnızca bir süre sonu ilkesi yapılandırılabilir.

> [!NOTE]
> Office 365 grupları için süre sonu ilkesini yapılandırmak ve kullanmak, zaman aşımı ilkesinin uygulandığı tüm grupların üyeleri için Azure AD Premium lisansları atamanız gerekir.

Azure AD PowerShell cmdlet 'lerini indirme ve yükleme hakkında daha fazla bilgi için bkz. [Graph 2.0.0.137 için Azure Active Directory PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Etkinlik tabanlı otomatik yenileme

Azure AD Intelligence ile gruplar, son kullanılan zamanlarda olup olmadıklarında otomatik olarak yenilenir. Bu özellik, Outlook, SharePoint, takımlar veya Yammer gibi Office 365 Hizmetleri içindeki gruplardaki Kullanıcı etkinliklerini temel alarak, Grup sahiplerine göre el ile gerçekleştirilen eyleme yönelik ihtiyacı ortadan kaldırır. Örneğin, bir sahip veya grup üyesi SharePoint 'te belge yükleme gibi bir şey kullanıyorsa, bir ekip kanalını ziyaret edin veya Outlook 'ta gruba bir e-posta gönderin, Grup otomatik olarak yenilenir ve sahip herhangi bir yenileme bildirimi almaz.

### <a name="activities-that-automatically-renew-group-expiration"></a>Grup süre sonunu otomatik olarak yenileme etkinlikleri

Aşağıdaki Kullanıcı eylemleri otomatik grup yenilemeye neden olur:

- SharePoint: dosyaları görüntüleme, düzenleme, indirme, taşıma, paylaşma veya karşıya yükleme
- Outlook: Grup alanından bir ileti (Outlook Web Erişimi) gibi grup alanından oku/yaz grup iletisini ekleyin
- Takımlar: bir takımlar kanalını ziyaret edin

### <a name="auditing-and-reporting"></a>Denetim ve raporlama

Yöneticiler, Azure AD 'de etkinlik denetim günlüklerinden otomatik olarak yenilenen grupların listesini alabilir.

## <a name="roles-and-permissions"></a>Roller ve izinler

Aşağıda, Azure AD 'de Office 365 grupları için süre sonu yapılandırıp kullanılabilecek roller verilmiştir.

Rol | İzinler
-------- | --------
Genel yönetici veya Kullanıcı Yöneticisi | Office 365 grupları süre sonu ilke ayarlarını oluşturabilir, okuyabilir, güncelleştirebilir veya silebilir<br>Herhangi bir Office 365 grubunu yenileyebilirler
Kullanıcı | Sahip oldukları bir Office 365 grubunu yenileyebilirler<br>Sahip oldukları Office 365 grubunu geri yükleyebilir<br>Süre sonu ilke ayarlarını okuyabilir

Silinen bir grubu geri yükleme izinleri hakkında daha fazla bilgi için, [Azure Active Directory silinen Office 365 grubunu geri yükleme](groups-restore-deleted.md)bölümüne bakın.

## <a name="set-group-expiration"></a>Grup süre sonunu ayarlama

1. Azure AD [Yönetim merkezini](https://aad.portal.azure.com) Azure AD kuruluşunuzda genel yönetici olan bir hesapla açın.

2. **Grupları**seçin, sonra süre sonu ayarlarını açmak Için **süre sonu** ' nu seçin.
  
   ![Gruplar için süre sonu ayarları](./media/groups-lifecycle/expiration-settings.png)

3. **Süre sonu** sayfasında şunları yapabilirsiniz:

    - Grup ömrünü gün cinsinden ayarlayın. Önceden ayarlanmış değerlerden birini veya özel bir değeri seçebilirsiniz (31 gün veya daha fazla olmalıdır).
    - Bir grubun sahibi olmadığında yenileme ve sona erme bildirimlerinin gönderilmesi gereken bir e-posta adresi belirtin.
    - Hangi Office 365 gruplarının sona ereceğini seçin. Süre sonunu şu şekilde ayarlayabilirsiniz:
      - **Tümü** Office 365 grupları
      - **Seçili** Office 365 gruplarının listesi
      - Tüm gruplar için süre sonunu kısıtlamak için **yok**
    - **Kaydet**' i seçerek yaptığınız ayarları kaydedin.

> [!NOTE]
> Son tarihi ayarladığınızda, süre sonu aralığından eski olan tüm gruplar, Grup otomatik olarak yenilenmediği veya sahip onu yenilemediği sürece süresi dolmadan 35 gün olarak ayarlanır.
>
> Dinamik bir grup silinip geri yüklendiğinde, yeni bir grup olarak görülür ve kurala göre yeniden doldurulur. Bu işlem, 24 saate kadar sürebilir.
>
> Ekiplerde kullanılan gruplar için süre sonu bildirimleri takımlar sahipleri akışında görüntülenir.

## <a name="email-notifications"></a>E-posta bildirimleri

Gruplar otomatik olarak yenilenmezse, bu gibi e-posta bildirimleri Office 365 grup sahiplerine 30 gün, 15 gün ve grubun süresi dolmadan 1 gün önce gönderilir. E-postanın dili, grupların sahibinin tercih ettiği dil veya Azure AD dil ayarı tarafından belirlenir. Grup sahibi tercih edilen bir dil tanımlıysa veya birden çok sahibi aynı tercih edilen dile sahipse, bu dil kullanılır. Diğer tüm durumlarda Azure AD dil ayarı kullanılır.

![Sona erme e-posta bildirimleri](./media/groups-lifecycle/expiration-notification.png)

Grup sahipleri, **yenileme grubu** bildirim e-postasında, erişim panelindeki Grup ayrıntıları sayfasına doğrudan erişebilir. Burada, kullanıcılar grup hakkında, açıklama, son yenilenme tarihi ve ayrıca grubu yenileme özelliği gibi daha fazla bilgi alabilir. Grup ayrıntıları sayfası artık Office 365 grup kaynaklarına bağlantıları da içerir, böylece Grup sahibi, gruptaki içeriği ve etkinlikleri kolayca görüntüleyebilir.

Bir grubun süresi dolduğunda, Grup sona erme tarihinden bir gün sonra silinir. Bu gibi bir e-posta bildirimi, Office 365 grup sahiplerine gönderilir ve bu, Office 365 grubunun son kullanma tarihi ve sonraki silmesi hakkında bilgi sağlar.

![Grup silme e-posta bildirimleri](./media/groups-lifecycle/deletion-notification.png)

Grup, [Azure Active Directory silinen Office 365 grubunu geri yükleme](groups-restore-deleted.md)' de açıklandığı gibi, silme **işlemini veya PowerShell** cmdlet 'lerini kullanarak silme işleminin 30 gün içinde geri yüklenebilir. 30 günlük grup geri yükleme döneminin özelleştirilemez olduğunu lütfen unutmayın.

Geri yüklemekte olduğunuz grup belgeler, SharePoint siteleri veya diğer kalıcı nesneler içeriyorsa, grubu ve içeriğini tamamen geri yüklemek 24 saate kadar sürebilir.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Office 365 grubu sona erme tarihi nasıl alınır?

Kullanıcıların, sona erme tarihi ve son yenilenme tarihi dahil olmak üzere Grup ayrıntılarını görüntüleyebilecekleri erişim paneline ek olarak, bir Office 365 grubunun sona erme tarihi Microsoft Graph REST API Beta 'dan alınabilir. bir grup özelliği olarak expirationDateTime, Microsoft Graph Beta 'da etkinleştirildi. Bu, bir GET isteğiyle alınabilir. Daha fazla ayrıntı için lütfen [Bu örneğe](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example)bakın.

> [!NOTE]
> Erişim panelinde grup üyeliklerini yönetmek için, "erişim panelindeki gruplara erişimi kısıtla" ayarı, Azure Active Directory gruplar genel ayarında "Hayır" olarak ayarlanmalıdır.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Office 365 Grubu süre sonu, yasal saklama durumunda bir posta kutusuyla nasıl kullanılır

Bir grubun süresi dolmuşsa ve silindiğinde, grup verileri planlayıcısı, siteler veya takımlar gibi uygulamalardan silindikten sonraki 30 gün sonra kalıcı olarak silinir, ancak yasal saklama alanındaki grup posta kutusu korunur ve kalıcı olarak silinmez. Yönetici, verileri getirmek üzere posta kutusunu geri yüklemek için Exchange cmdlet 'lerini kullanabilir.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Office 365 Grubu süre sonu, bekletme ilkesiyle nasıl kullanılır?

Bekletme ilkesi, güvenlik ve Uyumluluk Merkezi yoluyla yapılandırılır. Office 365 grupları için bir bekletme ilkesi ayarladıysanız, bir grubun süresi dolarsa ve silindiğinde, grup posta kutusundaki ve grup sitesindeki dosyalardaki grup konuşmaları, bekletme kapsayıcısında, bekletmede tanımlanan belirli gün sayısı için tutulur ilkesinin. Kullanıcılar, süresi dolduktan sonra grubu veya içeriğini görmez, ancak site ve posta kutusu verilerini e-bulma yoluyla kurtarabilir.

## <a name="powershell-examples"></a>PowerShell örnekleri

Azure AD Kuruluşunuzda Office 365 grupları için süre sonu ayarlarını yapılandırmak üzere PowerShell cmdlet 'lerini nasıl kullanabileceğinizi gösteren örnekler aşağıda verilmiştir:

1. PowerShell v 2.0 modülünü yükleyip PowerShell komut isteminde oturum açın:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Süre sonu ayarlarını yapılandırma Azure AD kuruluşundaki tüm Office 365 gruplarının ömrünü 365 güne ayarlamak için New-AzureADMSGroupLifecyclePolicy cmdlet 'ini kullanın. Sahipleri olmayan Office 365 grupları için yenileme bildirimleri 'emailaddress@contoso.com' öğesine gönderilir
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Mevcut ilkeyi alma Get-AzureADMSGroupLifecyclePolicy: Bu cmdlet, yapılandırılmış olan geçerli Office 365 grup süre sonu ayarlarını alır. Bu örnekte şunları görebilirsiniz:

   - İlke KIMLIĞI
   - Azure AD kuruluşundaki tüm Office 365 gruplarının ömrü 365 gün olarak ayarlanır
   - Sahipleri olmayan Office 365 grupları için yenileme bildirimleri 'emailaddress@contoso.com' öğesine gönderilir.
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Mevcut ilke kümesini güncelleştirin-AzureADMSGroupLifecyclePolicy: Bu cmdlet, mevcut bir ilkeyi güncelleştirmek için kullanılır. Aşağıdaki örnekte, mevcut ilkedeki grup ömrü 365 gün ile 180 gün arasında değişir.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Add-AzureADMSLifecyclePolicyGroup ilkesine belirli gruplar ekleyin: Bu cmdlet yaşam döngüsü ilkesine bir grup ekler. Örneğin:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Var olan Ilkeyi kaldır Remove-AzureADMSGroupLifecyclePolicy: Bu cmdlet, Office 365 grup süre sonu ayarlarını siler, ancak ilke KIMLIĞINI gerektirir. Bu cmdlet, Office 365 grupları için süre sonunu devre dışı bırakır.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
İlkeyi daha ayrıntılı şekilde yapılandırmak için aşağıdaki cmdlet 'ler kullanılabilir. Daha fazla bilgi için bkz. [PowerShell belgeleri](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Sonraki adımlar

Bu makaleler, Azure AD grupları hakkında ek bilgiler sağlar.

- [Var olan grupları görme](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Bir grubun ayarlarını yönetme](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Bir grubun üyelerini yönetme](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Bir grubun üyeliklerini yönetme](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](groups-dynamic-membership.md)
