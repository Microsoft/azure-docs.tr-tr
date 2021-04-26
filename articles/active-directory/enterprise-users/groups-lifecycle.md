---
title: Microsoft 365 grupları için süre sonunu ayarlama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory Microsoft 365 grupları için süre sonu ayarlama
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8af1a5e73592dc1c3392f0bc1fecfe6139a54710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529843"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Microsoft 365 grupları için süre sonu ilkesini yapılandırma

Bu makalede, kendileri için bir süre sonu ilkesi ayarlayarak Microsoft 365 gruplarının yaşam döngüsünü nasıl yöneteceğiniz açıklanmaktadır. Süre sonu ilkesini, yalnızca Azure Active Directory (Azure AD) Microsoft 365 Gruplar için ayarlayabilirsiniz.

Bir grubu sona ermek üzere ayarladıktan sonra:

- Kullanıcı etkinlikleri olan gruplar, süre sonu yaklaştığında otomatik olarak yenilenir.
- Grubun sahipleri, Grup otomatik yenilenmediğinde grubu yenilemek için bilgilendirilir.
- Yenilenmeyen tüm gruplar silinir.
- Silinen tüm Microsoft 365 grupları, Grup sahipleri veya yönetici tarafından 30 gün içinde geri yüklenebilir.

Şu anda, bir Azure AD kuruluşundaki tüm Microsoft 365 grupları için yalnızca bir süre sonu ilkesi yapılandırılabilir.

> [!NOTE]
> Microsoft 365 grupları için süre sonu ilkesini yapılandırmak ve kullanmak için, süre sonu ilkesinin uygulandığı tüm grupların üyeleri için Azure AD Premium lisansları atamanız gerekir.

Azure AD PowerShell cmdlet 'lerini indirme ve yükleme hakkında daha fazla bilgi için bkz. [Graph 2.0.0.137 için Azure Active Directory PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Etkinlik tabanlı otomatik yenileme

Azure AD Intelligence ile gruplar, son zamanlarda kullanılıp kullanıldıklarından bağımsız olarak otomatik olarak yenilenir. Bu özellik, Outlook, SharePoint veya takımlar gibi Microsoft 365 hizmetler genelinde gruplar içindeki kullanıcı etkinliklerini temel aldığı için, Grup sahiplerine göre el ile işlem gereksinimini ortadan kaldırır. Örneğin, bir sahip veya grup üyesi SharePoint 'te belge yükleme gibi bir şey kullanıyorsa, bir ekip kanalını ziyaret edin veya Outlook 'ta gruba bir e-posta gönderirseniz, Grup süresi dolmadan ve sahip herhangi bir yenileme bildirimi almadan, Grup otomatik olarak 35 gün içinde yenilenir.

### <a name="activities-that-automatically-renew-group-expiration"></a>Grup süre sonunu otomatik olarak yenileme etkinlikleri

Aşağıdaki Kullanıcı eylemleri otomatik grup yenilemeye neden olur:

- SharePoint: dosyaları görüntüleme, düzenleme, indirme, taşıma, paylaşma veya karşıya yükleme
- Outlook: Grup alanından bir ileti (Outlook Web Erişimi) gibi grup alanından oku/yaz grup iletisini ekleyin
- Takımlar: bir takımlar kanalını ziyaret edin

### <a name="auditing-and-reporting"></a>Denetim ve raporlama

Yöneticiler, Azure AD 'de etkinlik denetim günlüklerinden otomatik olarak yenilenen grupların listesini alabilir.

![Etkinliğe göre grupların otomatik yenilenmesi](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Roller ve izinler

Aşağıda, Azure AD 'de Microsoft 365 grupları için süre sonu yapılandırıp kullanabileceğiniz roller verilmiştir.

Rol | İzinler
-------- | --------
Genel yönetici, Grup Yöneticisi veya Kullanıcı Yöneticisi | Microsoft 365 grupları için süre sonu ilke ayarlarını oluşturabilir, okuyabilir, güncelleştirebilir veya silebilir<br>Herhangi bir Microsoft 365 grubunu yenileyebilirler
Kullanıcı | Sahip oldukları bir Microsoft 365 grubunu yenileyebilirler<br>Sahip oldukları bir Microsoft 365 grubunu geri yükleyebilir<br>Süre sonu ilke ayarlarını okuyabilir

Silinen bir grubu geri yükleme izinleri hakkında daha fazla bilgi için, [Azure Active Directory içindeki silinen Microsoft 365 grubunu geri yükleme](groups-restore-deleted.md)bölümüne bakın.

## <a name="set-group-expiration"></a>Grup süre sonunu ayarlama

1. Azure AD [Yönetim merkezini](https://aad.portal.azure.com) Azure AD kuruluşunuzda genel yönetici olan bir hesapla açın.

2. **Grupları** seçin, sonra süre sonu ayarlarını açmak Için **süre sonu** ' nu seçin.
  
   ![Gruplar için süre sonu ayarları](./media/groups-lifecycle/expiration-settings.png)

3. **Süre sonu** sayfasında şunları yapabilirsiniz:

    - Grup ömrünü gün cinsinden ayarlayın. Önceden ayarlanmış değerlerden birini veya özel bir değeri seçebilirsiniz (30 gün veya daha fazla olmalıdır).
    - Bir grubun sahibi olmadığında yenileme ve sona erme bildirimlerinin gönderilmesi gereken bir e-posta adresi belirtin.
    - Hangi Microsoft 365 gruplarının sona ereceğini seçin. Süre sonunu şu şekilde ayarlayabilirsiniz:
      - **Tümü** Microsoft 365 grupları
      - **Seçili** Microsoft 365 gruplarının listesi
      - Tüm gruplar için süre sonunu kısıtlamak için **yok**
    - **Kaydet**' i seçerek yaptığınız ayarları kaydedin.

> [!NOTE]
> - Son tarihi ayarladığınızda, süre sonu aralığından eski olan tüm gruplar, Grup otomatik olarak yenilenmediği veya sahip onu yenilemediği sürece süresi dolmadan 35 gün olarak ayarlanır.
> - Dinamik bir grup silinip geri yüklendiğinde, yeni bir grup olarak görülür ve kurala göre yeniden doldurulur. Bu işlem, 24 saate kadar sürebilir.
> - Ekiplerde kullanılan gruplar için süre sonu bildirimleri takımlar sahipleri akışında görüntülenir.
> - Seçili gruplar için süre sonunu etkinleştirdiğinizde, listeye en fazla 500 grup ekleyebilirsiniz. 500 taneden fazla grup eklemeniz gerekiyorsa, tüm gruplarınız için süre sonunu etkinleştirebilirsiniz. Bu senaryoda, 500 Grup sınırlaması uygulanmaz.

## <a name="email-notifications"></a>E-posta bildirimleri

Gruplar otomatik olarak yenilenmezse, bu gibi e-posta bildirimleri, Grup sona ermeden 30 gün, 15 gün ve 1 gün önce Microsoft 365 grup sahibine gönderilir. E-postanın dili, grupların sahibinin tercih ettiği dil veya Azure AD dil ayarı tarafından belirlenir. Grup sahibi tercih edilen bir dil tanımlıysa veya birden çok sahibi aynı tercih edilen dile sahipse, bu dil kullanılır. Diğer tüm durumlarda Azure AD dil ayarı kullanılır.

![Sona erme e-posta bildirimleri](./media/groups-lifecycle/expiration-notification.png)

Grup sahipleri, **yenileme grubu** bildirim e-postasında, [erişim panelindeki](https://account.activedirectory.windowsazure.com/r#/applications)Grup ayrıntıları sayfasına doğrudan erişebilir. Burada, kullanıcılar grup hakkında, açıklama, son yenilenme tarihi ve ayrıca grubu yenileme özelliği gibi daha fazla bilgi alabilir. Grup ayrıntıları sayfası artık Microsoft 365 grup kaynaklarına bağlantılar da içerir, böylece Grup sahibi, gruptaki içeriği ve etkinlikleri kolayca görüntüleyebilmesini sağlar.

Bir grubun süresi dolduğunda, Grup sona erme tarihinden bir gün sonra silinir. Bunun gibi bir e-posta bildirimi, bu, Microsoft 365 grubunun son kullanma ve sonraki silme bilgilerini bildiren Microsoft 365 grup sahiplerine gönderilir.

![Grup silme e-posta bildirimleri](./media/groups-lifecycle/deletion-notification.png)

Grup, [Azure Active Directory silinen bir Microsoft 365 grubunu geri](groups-restore-deleted.md)yükle bölümünde açıklandığı gibi, **geri yükleme grubunu** seçerek veya PowerShell cmdlet 'lerini kullanarak silme işleminin 30 gün içinde geri yüklenebilir. 30 günlük grup geri yükleme döneminin özelleştirilemez olduğunu lütfen unutmayın.

Geri yüklemekte olduğunuz grup belgeler, SharePoint siteleri veya diğer kalıcı nesneler içeriyorsa, grubu ve içeriğini tamamen geri yüklemek 24 saate kadar sürebilir.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Microsoft 365 grubu sona erme tarihi alma

Kullanıcıların, sona erme tarihi ve son yenilenme tarihi de dahil olmak üzere Grup ayrıntılarını görüntüleyebilecekleri erişim paneline ek olarak, bir Microsoft 365 grubunun sona erme tarihi Microsoft Graph REST API Beta 'dan alınabilir. bir grup özelliği olarak expirationDateTime, Microsoft Graph Beta 'da etkinleştirildi. Bu, bir GET isteğiyle alınabilir. Daha fazla ayrıntı için lütfen [Bu örneğe](/graph/api/group-get?view=graph-rest-beta#example&preserve-view=true)bakın.

> [!NOTE]
> Erişim panelinde grup üyeliklerini yönetmek için, "erişim panelindeki gruplara erişimi kısıtla" ayarı, Azure Active Directory gruplar genel ayarında "Hayır" olarak ayarlanmalıdır.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Microsoft 365 Grubu süre sonu, yasal tutulan bir posta kutusuyla nasıl kullanılır

Bir grubun süresi dolmuşsa ve silindiğinde, grup verileri planlayıcısı, siteler veya takımlar gibi uygulamalardan silindikten sonraki 30 gün sonra kalıcı olarak silinir, ancak yasal saklama alanındaki grup posta kutusu korunur ve kalıcı olarak silinmez. Yönetici, verileri getirmek üzere posta kutusunu geri yüklemek için Exchange cmdlet 'lerini kullanabilir.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Microsoft 365 Grubu süre sonu bekletme ilkesiyle nasıl kullanılır

Bekletme ilkesi, güvenlik ve Uyumluluk Merkezi yoluyla yapılandırılır. Microsoft 365 grupları için bir bekletme ilkesi ayarladıysanız, bir grubun süresi dolarsa ve silindiğinde, grup posta kutusundaki ve grup sitesindeki dosyalardaki grup konuşmaları, bekletme ilkesinde tanımlanan belirli gün sayısı için bekletme kapsayıcısında tutulur. Kullanıcılar, süresi dolduktan sonra grubu veya içeriğini görmez, ancak site ve posta kutusu verilerini e-bulma yoluyla kurtarabilir.

## <a name="powershell-examples"></a>PowerShell örnekleri

Azure AD kuruluşunuzda Microsoft 365 gruplarının süre sonu ayarlarını yapılandırmak için PowerShell cmdlet 'lerini nasıl kullanabileceğinizi gösteren örnekler aşağıda verilmiştir:

1. PowerShell v 2.0 modülünü yükleyip PowerShell komut isteminde oturum açın:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Süre sonu ayarlarını yapılandırma Azure AD kuruluşundaki tüm Microsoft 365 gruplarının yaşam süresini 365 gün olarak ayarlamak için New-AzureADMSGroupLifecyclePolicy cmdlet 'ini kullanın. Sahipleri olmayan Microsoft 365 grupları için yenileme bildirimleri ' ' öğesine gönderilir emailaddress@contoso.com
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Mevcut ilkeyi alma Get-AzureADMSGroupLifecyclePolicy: Bu cmdlet, yapılandırılmış geçerli Microsoft 365 grup süre sonu ayarlarını alır. Bu örnekte şunları görebilirsiniz:

   - İlke KIMLIĞI
   - Azure AD kuruluşundaki tüm Microsoft 365 gruplarının yaşam süresi 365 gün olarak ayarlanmıştır
   - Sahipleri olmayan Microsoft 365 grupları için yenileme bildirimleri '. ' öğesine gönderilir emailaddress@contoso.com
  
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
  
1. Var olan Ilkeyi kaldır Remove-AzureADMSGroupLifecyclePolicy: Bu cmdlet Microsoft 365 Grubu süre sonu ayarlarını siler, ancak ilke KIMLIĞINI gerektirir. Bu cmdlet Microsoft 365 grupları için süre sonunu devre dışı bırakır.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
İlkeyi daha ayrıntılı şekilde yapılandırmak için aşağıdaki cmdlet 'ler kullanılabilir. Daha fazla bilgi için bkz. [PowerShell belgeleri](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
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
