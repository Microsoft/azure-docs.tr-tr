---
title: Konuk Kullanıcı erişim izinlerini kısıtlama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory Azure portal, PowerShell veya Microsoft Graph kullanarak Konuk Kullanıcı erişim izinlerini kısıtlama
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/14/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: df4cb32720d80dd23289be7e760c9934e9a8db8a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501510"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Azure Active Directory 'de Konuk erişim izinlerini (Önizleme) kısıtlama

Azure Active Directory (Azure AD), dış konuk kullanıcıların Azure AD 'de kuruluşlarındaki neleri görebileceğini kısıtlayasağlar. Konuk kullanıcılar varsayılan olarak Azure AD 'de sınırlı bir izin düzeyine ayarlanır, ancak üye kullanıcıları için varsayılan değer varsayılan Kullanıcı izinlerinin tam kümesidir. Bu, daha fazla kısıtlanmış erişim için Azure AD kuruluşunuzun dış işbirliği ayarlarındaki yeni bir Konuk Kullanıcı izin düzeyinin önizlemesidir, bu nedenle artık Konuk erişim seçenekleriniz şunlardır:

İzin düzeyi         | Erişim düzeyi | Değer
----------------         | ------------ | -----
Üye kullanıcılarla aynı     | Konuklar, Azure AD kaynaklarına üye kullanıcıları olarak aynı erişime sahiptir | a0b1b346-4d3e-4e8b-98f8-753987be4970
Sınırlı erişim (varsayılan) | Konuklar, tüm gizli olmayan grupların üyeliğini görebilir | 10dae51f-b6af-4016-8d66-8c2a99b929b3
**Kısıtlı erişim (yeni)**  | **Konuklar herhangi bir grubun üyeliğini göremez** | **2af84b1e-32c8-42b7-82bc-dada82404023b**

Konuk erişimi kısıtlandıktan sonra, konuklar yalnızca kendi kullanıcı profilini görüntüleyebilir. Konuk Kullanıcı asıl adına veya ObjectID 'ye göre arama yapılsa bile diğer kullanıcıları görüntüleme izni verilmez. Kısıtlı erişim, konuk kullanıcıların bulundukları grupların üyeliğini görmesini de kısıtlar. Konuk Kullanıcı izinleri dahil olmak üzere genel varsayılan kullanıcı izinleri hakkında daha fazla bilgi için bkz. [Azure Active Directory varsayılan kullanıcı Izinleri nelerdir?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>İzinler ve lisanslar

Dış işbirliği ayarlarını yapılandırmak için genel yönetici rolünde olmanız gerekir. Konuk erişimini kısıtlamak için ek lisans gereksinimi yoktur.

## <a name="update-in-the-azure-portal"></a>Azure portal güncelleştirme

Mevcut Azure portal denetimlerinde Konuk Kullanıcı izinleri için değişiklikler yaptık.

1. [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) genel yönetici izinleriyle oturum açın.
1. Kuruluşunuzun **Azure Active Directory** Genel Bakış sayfasında, **Kullanıcı ayarları**' nı seçin.
1. **Dış kullanıcılar** altında, **dış işbirliği ayarlarını yönet**' i seçin.
1. **Dış işbirliği ayarları** sayfasında, **Konuk Kullanıcı erişimi, kendi dizin nesnelerinin özellikleri ve üyelikleri ile kısıtlıdır** seçeneğini belirleyin.

    ![Azure AD dış işbirliği ayarları sayfası](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. **Kaydet**’i seçin. Konuk kullanıcılar için değişikliklerin etkili olması 15 dakika kadar sürebilir.

## <a name="update-with-the-microsoft-graph-api"></a>Microsoft Graph API 'siyle güncelleştirme

Azure AD kuruluşunuzda Konuk izinlerini yapılandırmak için yeni bir Microsoft Graph API ekledik. Herhangi bir izin düzeyi atamak için aşağıdaki API çağrıları yapılabilir. Burada kullanılan Guestuserroleıd değeri, en kısıtlı Konuk Kullanıcı ayarını gösterir. Konuk izinlerini ayarlamak için Microsoft Graph kullanma hakkında daha fazla bilgi için bkz. [Authorizationpolicy kaynak türü](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>İlk kez yapılandırma

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Yanıt başarılı 204 olmalıdır.

### <a name="updating-the-existing-value"></a>Mevcut değer güncelleştiriliyor

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Yanıt başarılı 204 olmalıdır.

### <a name="view-the-current-value"></a>Geçerli değeri görüntüle

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Örnek yanıt:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>PowerShell cmdlet 'leriyle güncelleştirme

Bu özellikle, PowerShell V2 cmdlet 'leri aracılığıyla sınırlı izinleri yapılandırma özelliği ekledik. PowerShell cmdlet 'lerini get ve set, 2.0.2.85 sürümünde yayımlandı.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Komutu al: Get-AzureADMSAuthorizationPolicy

Örnek:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set komutu: Set-AzureADMSAuthorizationPolicy

Örnek:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> İstendiğinde, KIMLIK olarak authorizationPolicy girmeniz gerekir.

## <a name="supported-microsoft-365-services"></a>Desteklenen Microsoft 365 hizmetleri

### <a name="supported-services"></a>Desteklenen hizmetler

Destekleniyorsa, deneyimin beklendiği gibi olduğu anlamına gelir; Özellikle, geçerli Konuk deneyimiyle aynı.

- Teams
- Outlook (OWA)
- SharePoint
- Ekiplerde Planner
- Planner Web uygulaması

### <a name="services-currently-not-supported"></a>Hizmetler şu anda desteklenmiyor

Geçerli destek olmadan hizmet yeni Konuk kısıtlama ayarıyla uyumluluk sorunlarına sahip olabilir.

- Formlar
- Planner mobil uygulaması
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Sık sorulan sorular (SSS)

Soru | Yanıt
-------- | ------
Bu izinler nereye uygulanır? | Bu dizin düzeyi izinleri Microsoft Graph, PowerShell V2, Azure portal ve uygulamalarım portalı dahil olmak üzere Azure AD Hizmetleri ve portalları arasında zorlanır. İşbirliği senaryolarına yönelik Microsoft 365 grupları kullanan Microsoft 365 Hizmetleri, özellikle de Outlook, Microsoft ekipleri ve SharePoint 'i de etkilemiştir.
Kısıtlı izinler konukları hangi grupların görebileceği şekilde etkiler? | Varsayılan veya kısıtlı Konuk izinlerinden bağımsız olarak, konuklar grup veya Kullanıcı listesini numaralandıramaz. Konuklar izinlere bağlı olarak hem Azure portal hem de My Apps portalında üye oldukları grupları görebilir:<li>**Varsayılan izinler**: Azure Portal üye oldukları grupları bulmak için, Konuk, **tüm kullanıcılar** listesinde kendi nesne kimliklerini aramalıdır ve ardından **gruplar**' ı seçer. Burada ad, e-posta vb. dahil olmak üzere tüm grup ayrıntıları dahil olmak üzere üye oldukları grupların listesini görebilirler. Uygulamalarım portalında, sahip oldukları grupların bir listesini ve üyesi oldukları grupları görebilirler.</li><li>**Kısıtlı Konuk izinleri**: Azure Portal, tüm kullanıcılar listesinde kendı nesne kimliklerini arayarak üye oldukları grupların listesini yine de bulabilir ve ardından Gruplar ' ı seçin. Bunlar, özellikle de nesne KIMLIĞI olan grup hakkında çok sınırlı ayrıntıları görebilirler. Tasarım, ad ve e-posta sütunları boş ve Grup türü tanınmıyor. Uygulamalarım portalında, sahip oldukları grupların listesine veya üyesi oldukları gruplara erişemez.</li><br>Graph API gelen dizin izinlerinin daha ayrıntılı bir karşılaştırması için bkz. [Varsayılan Kullanıcı izinleri](../fundamentals/users-default-permissions.md#member-and-guest-users).
Uygulamamın portalının hangi parçaları bu özellik tarafından etkilenecek? | Uygulamalarım portalındaki gruplar işlevi bu yeni izinleri kabul eder. Bu, gruplar listesini ve grup üyeliklerini uygulamamda görüntülemek için tüm yolları içerir. Grup kutucuğu kullanılabilirliğine hiçbir değişiklik yapılmadı. Grup kutucuğu kullanılabilirliği hala Azure portal var olan Grup ayarı tarafından denetlenmektedir.
Bu izinler SharePoint veya Microsoft ekipleri Konuk ayarlarını geçersiz kılar mi? | Hayır. Mevcut ayarlar, bu uygulamalardaki deneyimi ve erişimi yine de denetler. Örneğin, SharePoint 'te sorunlar görürseniz, dış paylaşım ayarlarınızı iki kez kontrol edin.
Planner ve Yammer 'daki bilinen uyumluluk sorunları nelerdir? | <li>İzinler ' Restricted ' olarak ayarlandığında, Planner mobil uygulamasında oturum açan konuklara planlara veya herhangi bir göreve erişemeyecektir.<li>İzinler ' Restricted ' olarak ayarlandığında, Yammer 'da oturum açan konuklar gruptan ayrılamayacaktır.
Kiracımda var olan konuk izinlerim değiştirilsin mi? | Geçerli ayarlarınızda değişiklik yapılmadı. Mevcut ayarlarınızla geriye dönük uyumluluk sağlıyoruz. Ne zaman değişiklik yapmak istediğinize karar verirsiniz.
Bu izinler varsayılan olarak ayarlanacaktır mi? | Hayır. Mevcut varsayılan izinler değişmeden kalır. İsteğe bağlı olarak izinleri daha kısıtlayıcı olarak ayarlayabilirsiniz.
Bu özellik için herhangi bir lisans gereksinimi var mı? | Hayır, bu özellikle yeni lisanslama gereksinimi yoktur.

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD 'de mevcut Konuk izinleri hakkında daha fazla bilgi edinmek için bkz [. Azure Active Directory varsayılan kullanıcı Izinleri nelerdir?](../fundamentals/users-default-permissions.md)
- Konuk erişimini kısıtlamak için Microsoft Graph API yöntemlerini görmek için bkz. [Authorizationpolicy kaynak türü](/graph/api/resources/authorizationpolicy)
- Bir kullanıcının tüm erişimini iptal etmek için bkz. [Azure AD 'de Kullanıcı erişimini Iptal etme](users-revoke-access.md)
