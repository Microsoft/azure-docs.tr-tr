---
title: Konuk kullanıcının kullanım durumunu sıfırlama-Azure AD
description: Azure AD dış kimliklerinizde Azure Active Directory B2B Konuk kullanıcıları için davet kullanım durumunu sıfırlama hakkında bilgi edinin.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5bfce7ef2621cbe3bbbfdd95bf9a75e427c8cbd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531880"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>Konuk Kullanıcı için kullanım durumunu sıfırlama (Önizleme)

Bir Konuk Kullanıcı, B2B işbirliği davetinizi tamamladıktan sonra, oturum açma bilgilerini güncelleştirmeniz gereken zamanlar olabilir, örneğin:

- Kullanıcı farklı bir e-posta ve kimlik sağlayıcısı kullanarak oturum açmak istiyor
- Ana kiracısındaki kullanıcının hesabı silindi ve yeniden oluşturuldu
- Kullanıcı farklı bir şirkete taşınmış, ancak yine de kaynaklarınızla aynı erişime ihtiyaç duyuyor
- Kullanıcının sorumlulukları başka bir kullanıcıya geçirilmiş

Daha önce bu senaryoları yönetmek için, Konuk kullanıcının hesabını dizininizden el ile silip kullanıcıyı yeniden davet etmeniz gerekiyordu. Artık Kullanıcı kullanım durumunu sıfırlamak ve kullanıcının nesne KIMLIĞINI, grup üyeliklerini ve uygulama atamalarını korurken kullanıcıyı yeniden davet etmek için PowerShell veya Microsoft Graph davet API 'sini kullanabilirsiniz. Kullanıcı yeni daveti önceden kullanırken, kullanıcının UPN 'si değişmez, ancak kullanıcının oturum açma adı yeni e-postaya değişir. Kullanıcı daha sonra yeni e-posta veya Kullanıcı nesnesinin özelliğine eklemiş olduğunuz bir e-posta kullanarak oturum açabilir `otherMails` .

## <a name="reset-the-email-address-used-for-sign-in"></a>Oturum açma için kullanılan e-posta adresini sıfırlayın

Kullanıcı farklı bir e-posta kullanarak oturum açmak isterse:

1. Yeni e-posta adresinin `mail` Kullanıcı nesnesinin veya özelliğine eklendiğinden emin olun `otherMails` . 
2.  Özelliğindeki e-posta adresini `InvitedUserEmailAddress` Yeni e-posta adresiyle değiştirin.
3. Kullanıcının kullanım durumunu sıfırlamak için aşağıdaki yöntemlerden birini kullanın.

> [!NOTE]
>Genel Önizleme sırasında iki önerimiz vardır:
>- Kullanıcının e-posta adresini yeni bir adrese sıfırlarken, özelliği ayarlamayı öneririz `mail` . Bu şekilde Kullanıcı, davetteki kullanım bağlantısını kullanmaya ek olarak dizininizde oturum açarak daveti kullanabilir.
>- Bir B2B Konuk kullanıcısının durumunu sıfırlarken, bunu Kullanıcı bağlamı altında gerçekleştirdiğinizden emin olun. Yalnızca uygulama çağrıları şu anda desteklenmiyor.
>
## <a name="use-powershell-to-reset-redemption-status"></a>Kullanım durumunu sıfırlamak için PowerShell 'i kullanma

En son AzureADPreview PowerShell modülünü kurun ve `InvitedUserEmailAddress` Yeni e-posta adresine ayarlı yeni bir davet oluşturun ve `ResetRedemption` olarak ayarlayın `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Kullanım durumunu sıfırlamak için Microsoft Graph API kullanma

[Microsoft Graph davet API](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)'sini kullanarak `resetRedemption` özelliğini olarak ayarlayın `true` ve özelliğindeki yeni e-posta adresini belirtin `invitedUserEmailAddress` .

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell kullanarak Azure Active Directory B2B işbirliği kullanıcıları ekleme](customize-invitation-api.md#powershell)
- [Azure AD B2B Konuk kullanıcısının özellikleri](user-properties.md)
