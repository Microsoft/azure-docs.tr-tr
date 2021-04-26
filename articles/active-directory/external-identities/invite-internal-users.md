---
title: Dahili kullanıcıları B2B işbirliğiyle davet etme-Azure AD
description: İş ortakları, dağıtıcılar, tedarikçiler, satıcılar ve diğer konuklar için dahili kullanıcı hesaplarınız varsa, kendi dış kimlik bilgileriyle veya oturum açmayla oturum açmak üzere davet ederek Azure AD B2B işbirliğiyle geçiş yapabilirsiniz. PowerShell veya Microsoft Graph davet API 'sini kullanın.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576439"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Dahili kullanıcıları B2B işbirliğiyle davet etme

Kuruluşlar, Azure AD B2B işbirliğinin kullanılabilirliğine başlamadan önce dağıtıcılar, tedarikçiler, satıcılar ve diğer konuk kullanıcılarla işbirliği yaparak bunlar için iç kimlik bilgilerini ayarlayabilir. Bunlar gibi iç Konuk kullanıcılarınız varsa, bunları B2B işbirliği kullanacak şekilde davet edebilirsiniz. Bu B2B Konuk kullanıcıları, oturum açmak için kendi kimliklerini ve kimlik bilgilerini kullanabiliyor ve parola korumanız veya hesap yaşam döngülerini yönetmeniz gerekmez.

Mevcut bir iç hesaba davetiye göndermek, bu kullanıcının nesne KIMLIĞI, UPN, grup üyelikleri ve uygulama atamalarını korumanızı sağlar. Kullanıcıyı el ile silip yeniden davet etmeniz veya kaynakları yeniden atamanız gerekmez. Kullanıcıyı davet etmek için davet API 'sini kullanarak hem iç Kullanıcı nesnesini hem de Konuk kullanıcının e-posta adresini daveti ile birlikte geçirebilirsiniz. Kullanıcı daveti kabul ettiğinde, B2B hizmeti mevcut iç Kullanıcı nesnesini bir B2B kullanıcısına değiştirir. Bundan sonra Kullanıcı, B2B kimlik bilgilerini kullanarak bulut kaynakları hizmetlerinde oturum açması gerekir.

## <a name="things-to-consider"></a>Dikkate alınması gereken noktalar

- Şirket **içi kaynaklara erişim**: Kullanıcı B2B işbirliği 'ya davet edildikten sonra şirket içi kaynaklara erişmek için iç kimlik bilgilerini kullanmaya devam edebilirler. İç hesaptaki parolayı sıfırlayarak veya değiştirerek bunu önleyebilirsiniz. Özel durum, [e-posta bir kerelik geçiş kodu kimlik doğrulamadır](one-time-passcode.md); kullanıcının kimlik doğrulama yöntemi bir kerelik geçiş kodu olarak değiştirilirse, iç kimlik bilgilerini artık kullanamazlar.

- **Faturalandırma**: Bu özellik Kullanıcı Için UserType değerini değiştirmez, bu nedenle kullanıcının faturalandırma modelini otomatik olarak [dış kimlikler aylık etkin kullanıcı (Mau) fiyatlandırmasına](external-identities-pricing.md)geçmez. Kullanıcının MAU fiyatlandırmasını etkinleştirmek için Kullanıcı için UserType değerini olarak değiştirin `guest` . Ayrıca, MAU faturalandırmayı etkinleştirmek için Azure AD kiracınızın [bir Azure aboneliğine bağlı](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) olması gerektiğini unutmayın.

- **Davet tek yönlü**: DAHILI kullanıcıları B2B işbirliğinin kullanımına davet edebilirsiniz, ancak B2B kimlik bilgilerini eklendikten sonra kaldıramazsınız. Kullanıcıyı yalnızca dahili bir kullanıcıya dönüştürmek için Kullanıcı nesnesini silip yeni bir tane oluşturmanız gerekir.

- **Takımlar**: Kullanıcı, dış kimlik bilgilerini kullanarak takımlara eriştiğinde, kiracının Ilk olarak takımlar kiracı seçicisinde kullanılabilir olmayacaktır. Kullanıcı, kiracı bağlamını içeren bir URL kullanarak takımlara erişebilir, örneğin: `https://team.microsoft.com/?tenantId=<TenantId>` . Bundan sonra kiracı, takımlar kiracı seçicisinde kullanılabilir hale gelir.

- Şirket **içi eşitlenmiş kullanıcılar**: şirket içi ve bulut arasında eşitlenen Kullanıcı hesapları için şirket içi DIZIN, B2B işbirliği kullanım için davet edildikten sonra yetkili kaynağı olarak kalır. Şirket içi hesapta yaptığınız tüm değişiklikler, hesabı devre dışı bırakma veya silme dahil olmak üzere bulut hesabıyla eşitlenir. Bu nedenle, kullanıcının şirket içi hesabında oturum açmasını engellemez, ancak şirket içi hesabı silerek bulut hesabını korur. Bunun yerine, şirket içi hesap parolasını rastgele bir GUID veya diğer bilinmeyen bir değer olarak ayarlayabilirsiniz.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Dahili kullanıcıları B2B işbirliğiyle davet etme

İç kullanıcıya bir B2B davetiyesi göndermek için PowerShell veya davet API 'sini kullanabilirsiniz. Davet için kullanmak istediğiniz e-posta adresinin iç Kullanıcı nesnesinde dış e-posta adresi olarak ayarlandığından emin olun.

- Davet için Kullanıcı. posta özelliğindeki e-posta adresini kullanmanız gerekir.
- Kullanıcının posta özelliğindeki etki alanının, oturum açmak için kullandıkları hesapla eşleşmesi gerekir. Aksi takdirde, takımlar gibi bazı hizmetler kullanıcının kimliğini doğrulayamayacaktır.

Varsayılan olarak, davet kullanıcıya davet edildiklerini bildiren bir e-posta gönderir, ancak bunun yerine bu e-postayı engelleyebilir ve kendi kendinize gönderebilirsiniz.

> [!NOTE]
> Kendi e-postanızı veya başka bir iletişim göndermek için ile kullanarak `New-AzureADMSInvitation` `-SendInvitationMessage:$false` kullanıcıları sessizce davet edebilir ve ardından kendi e-posta iletinizi dönüştürülen kullanıcıya gönderebilirsiniz. Bkz. [Azure AD B2B işbirliği API 'si ve özelleştirmesi](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>B2B davetiyesi göndermek için PowerShell 'i kullanma

Azure AD PowerShell modülü sürüm 2.0.2.130 veya sonraki bir sürümü gerekir. En son AzureAD PowerShell modülünü güncelleştirmek için aşağıdaki komutu kullanın ve dahili kullanıcıyı B2B işbirliği sürümüne davet edin:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Bir B2B davetiyesi göndermek için davet API 'sini kullanma

Aşağıdaki örnek, bir iç kullanıcıyı B2B kullanıcısı olarak davet etmek için davet API 'sinin nasıl çağrılacağını gösterir.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Yeni bir konuk kullanıcıyı dizine davet ettiğinizde, API 'ye yönelik yanıt aldığınız yanıttır.
## <a name="next-steps"></a>Sonraki adımlar

- [B2B işbirliği daveti satın alma](redemption-experience.md)
