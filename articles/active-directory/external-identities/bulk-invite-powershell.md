---
title: B2B işbirliği kullanıcılarına toplu davet etme öğreticisi-Azure Active Directory | Microsoft Docs
description: Bu öğreticide, dış Azure AD B2B işbirliği Konuk kullanıcılarına toplu davetiye göndermek için PowerShell ve CSV dosyası kullanmayı öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b4ea9f9f159efe974be54238843f5b9e3faaca
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056700"
---
# <a name="tutorial-use-powershell-to-bulk-invite-azure-ad-b2b-collaboration-users"></a>Öğretici: Azure AD B2B işbirliği kullanıcılarını toplu olarak davet etmek için PowerShell 'i kullanma

Şirket dışındaki ortaklarla çalışmak için Azure Active Directory (Azure AD) B2B işbirliğini kullanıyorsanız, kuruluşunuza aynı anda birden çok konuk kullanıcı davet edebilirsiniz. Bu öğreticide, harici kullanıcılara toplu davet göndermek için PowerShell’in nasıl kullanılacağını öğreneceksiniz. Özellikle aşağıdakileri yapın:

> [!div class="checklist"]
> * Kullanıcı bilgilerini içeren virgülle ayrılmış değer (.csv) dosyasını hazırlama
> * Davet göndermek için PowerShell betiğini çalıştırma
> * Kullanıcıların dizine eklendiğini doğrulama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 

## <a name="prerequisites"></a>Önkoşullar

### <a name="install-the-latest-azureadpreview-module"></a>En son AzureADPreview modülünü yükleme

Graph modülü için Azure AD PowerShell’in en son sürümünü (AzureADPreview) yüklediğinizden emin olun. 

İlk olarak, hangi modülleri yüklediğinizi denetleyin. Windows PowerShell’i yükseltilmiş yönetici olarak açın (Yönetici olarak çalıştırın) ve aşağıdaki komutu çalıştırın:

```powershell
Get-Module -ListAvailable AzureAD*
```

Çıktıya bağlı olarak aşağıdakilerden birini yapın:

- Bir sonuç döndürülmezse, AzureADPreview modülünü yüklemek için aşağıdaki komutu çalıştırın:
  
   ```powershell
   Install-Module AzureADPreview
   ```

- Yalnızca sonuçlarda AzureAD modülü gösteriliyorsa, AzureADPreview modülünü yüklemek için aşağıdaki komutları çalıştırın: 

   ```powershell
   Uninstall-Module AzureAD
   Install-Module AzureADPreview
   ```

- Yalnızca sonuçlarda AzureADPreview modülü gösteriliyorsa, ancak sonraki bir sürümün olduğunu belirten bir ileti alırsanız modülü güncelleştirmek için aşağıdaki komutları çalıştırın:

   ```powershell
   Uninstall-Module AzureADPreview
   Install-Module AzureADPreview
   ```

Modülü güvenilir olmayan bir depodan yüklediğinizi belirten bir istem alabilirsiniz. Önceden PSGallery deposunu güvenilir depo olarak ayarlamadıysanız bu oluşur. Modülü yüklemek için **Y** tuşuna basın.

### <a name="get-test-email-accounts"></a>Test e-posta hesaplarını alma

Davetleri gönderebileceğiniz iki veya daha fazla test e-posta hesabı olması gerekir. Hesaplar, kuruluşunuzun dışından olmalıdır. Gmail.com veya outlook.com adresleri gibi sosyal hesapları içeren herhangi bir hesap türü kullanabilirsiniz.

## <a name="prepare-the-csv-file"></a>CSV dosyasını hazırlama

Microsoft Excel’de davetli kullanıcı adları ve e-posta adresleri listesini içeren bir CSV dosyası oluşturun. **Name** ve **InvitedUserEmailAddress** sütun başlıklarını dahil ettiğinizden emin olun.

Örneğin, aşağıdaki biçimde bir çalışma sayfası oluşturun:

![Kullanıcı kabulünün beklemede olduğunu gösteren PowerShell çıktısı](media/tutorial-bulk-invite/AddUsersExcel.png)

Dosyayı **C:\BulkInvite\Invitations.csv** olarak kaydedin. 

Excel yoksa, Not Defteri gibi herhangi bir metin düzenleyicisinde bir CSV dosyası oluşturabilirsiniz. Her bir değeri virgülle ve her bir satırı yeni satırla ayırın. 

## <a name="sign-in-to-your-tenant"></a>Kiracınızda oturum açma

Kiracı etki alanına bağlanmak için aşağıdaki komutu çalıştırın:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```

Örneğin, `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

İstendiğinde kimlik bilgilerinizi girin.

## <a name="send-bulk-invitations"></a>Toplu davet gönderme

Davet göndermek için aşağıdaki PowerShell betiğini çalıştırın (burada **c:\bulkinvite\invitations.csv**, CSV dosyasının yoludur):

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv

$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo

$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."

foreach ($email in $invitations)
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.microsoft.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```

Betik, Invitations.csv dosyasındaki e-posta adreslerine bir davet gönderir. Her bir kullanıcı için aşağıdakine benzer bir çıktı görmeniz gerekir:

![Bekleyen Kullanıcı kabulü içeren PowerShell çıkışını gösteren ekran görüntüsü.](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Kullanıcıların dizinde mevcut olduğunu doğrulama

Davet edilen kullanıcıların Azure AD'ye eklendiğini doğrulamak için aşağıdaki komutu çalıştırın:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Bir Kullanıcı asıl adı (UPN) ile, davet ettiğiniz kullanıcıları *emadresi*#EXT # etki biçiminde görmeniz gerekir \@ . Örneğin, *lstokes_fabrikam. com # ext # \@ contoso.onmicrosoft.com*, burada contoso.onmicrosoft.com, davetleri gönderdiğiniz kuruluştur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse dizindeki test kullanıcı hesaplarını silebilirsiniz. Kullanıcı hesabını silmek için aşağıdaki komutu çalıştırın:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Örnek: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kuruluşunuz dışındaki konuk kullanıcılara toplu davetler göndereceksiniz. Daha sonra, davet kullanımı işleminin nasıl çalıştığını öğreneceksiniz.

> [!div class="nextstepaction"]
> [Azure AD B2B işbirliği daveti kullanım işlemi hakkında bilgi edinin](redemption-experience.md)
