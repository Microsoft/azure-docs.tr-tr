---
title: Azure AD SSPR ve Multi-Factor Authentication (Önizleme) için Birleşik kayıt sorunlarını giderme-Azure Active Directory
description: Azure AD Multi-Factor Authentication ve self servis parola sıfırlama Birleşik kayıt sorunlarını giderme (Önizleme)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e586105d8b2ec85e4ebd85046185ddc21112f0e0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167813"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Birleşik güvenlik bilgileri kaydı sorunlarını giderme (Önizleme)

Bu makaledeki bilgiler, birleştirilmiş kayıt deneyiminin kullanıcıları tarafından raporlanan sorunları gidermeye yönelik yöneticilere kılavuzluk edilir.

|     |
| --- |
| Azure Multi-Factor Authentication ve Azure Active Directory (Azure AD) self servis parola sıfırlama için Birleşik güvenlik bilgileri kaydı, Azure AD 'nin genel önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Denetim günlükleri

Birleşik kayıt için günlüğe kaydedilen olaylar, Azure AD denetim günlüklerinde kimlik doğrulama yöntemleri kategorisinde bulunur.

![Kayıt olaylarını gösteren Azure AD denetim günlükleri arabirimi](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Aşağıdaki tabloda, Birleşik kayıt tarafından oluşturulan tüm denetim olayları listelenmektedir:

| Etkinlik | Durum | Neden | Açıklama |
| --- | --- | --- | --- |
| Kullanıcı tüm gerekli güvenlik bilgilerini kaydetti | Başarılı | Kullanıcı tüm gerekli güvenlik bilgilerini kaydetti. | Bu olay, bir kullanıcı kaydı başarıyla tamamladığında oluşur.|
| Kullanıcı tüm gerekli güvenlik bilgilerini kaydetti | Hata | Kullanıcı güvenlik bilgileri kaydını iptal etti. | Bu olay, bir Kullanıcı kesme modundan kaydı iptal ettiğinde meydana gelir.|
| Kullanıcı kayıtlı güvenlik bilgileri | Başarılı | Kullanıcı kayıtlı *yöntemi*. | Bu olay, bir kullanıcı tek bir yöntemi kaydettiğinde oluşur. *Yöntem* kimlik doğrulayıcı uygulaması, telefon, e-posta, güvenlik soruları, uygulama parolası, alternatif telefon vb. olabilir.| 
| Kullanıcı tarafından gözden geçirilmiş güvenlik bilgileri | Başarılı | Kullanıcı, güvenlik bilgilerini başarıyla incelendi. | Bu olay, bir kullanıcı seçildiğinde güvenlik bilgileri incelemesi sayfasında **Iyi görünüyor** durumunda meydana gelir.|
| Kullanıcı tarafından gözden geçirilmiş güvenlik bilgileri | Hata | Kullanıcı güvenlik bilgilerini inceleyemedi. | Bu olay, bir kullanıcı seçimi, güvenlik bilgileri incelemesi sayfasında **Iyi görünüyor** , ancak arka uçta bir hata oluşmadığında oluşur.|
| Kullanıcı tarafından silinen güvenlik bilgileri | Başarılı | Kullanıcı tarafından silinen *Yöntem*. | Bu olay, bir kullanıcı tek bir yöntemi sildiğinde oluşur. *Yöntem* kimlik doğrulayıcı uygulaması, telefon, e-posta, güvenlik soruları, uygulama parolası, alternatif telefon vb. olabilir.|
| Kullanıcı tarafından silinen güvenlik bilgileri | Hata | Kullanıcı *yöntemi*silemedi. | Bu olay, bir Kullanıcı bir yöntemi silmeye çalıştığında oluşur ancak deneme bir nedenden dolayı başarısız olur. *Yöntem* kimlik doğrulayıcı uygulaması, telefon, e-posta, güvenlik soruları, uygulama parolası, alternatif telefon vb. olabilir.|
| Kullanıcı varsayılan güvenlik bilgilerini değiştirdi | Başarılı | Kullanıcı, *yöntemi*için varsayılan güvenlik bilgilerini değiştirdi. | Bu olay, bir Kullanıcı varsayılan yöntemi değiştirdiğinde oluşur. *Yöntem* , kimlik doğrulayıcı uygulama bildirimi, kimlik doğrulayıcı uygulamanızdan veya belirteçten bir kod, + x xxxxxxxxxx, bir kodu + x xxxxxxxxx ve benzeri gibi olabilir.|
| Kullanıcı varsayılan güvenlik bilgilerini değiştirdi | Hata | Kullanıcı, *yöntemi*için varsayılan güvenlik bilgilerini değiştiremedi. | Bu olay, bir Kullanıcı varsayılan yöntemi değiştirmeyi denediğinde oluşur ancak deneme bir nedenden dolayı başarısız olur. *Yöntem* , kimlik doğrulayıcı uygulama bildirimi, kimlik doğrulayıcı uygulamanızdan veya belirteçten bir kod, + x xxxxxxxxxx, bir kodu + x xxxxxxxxx ve benzeri gibi olabilir.|

## <a name="troubleshooting-interrupt-mode"></a>Kesme modunda sorun giderme

| Belirti | Sorun giderme adımları |
| --- | --- |
| Görmem beklenen yöntemleri görmüyorum. | 1. kullanıcının bir Azure AD yönetici rolüne sahip olup olmadığını denetleyin. Yanıt Evet ise, SSPR yönetici ilkesi farklılıklarını görüntüleyin. <br> 2. Multi-Factor Authentication kayıt zorlaması veya SSPR kayıt zorlaması nedeniyle kullanıcının kesintiye uğratılmadığını belirleme. Hangi yöntemlerin gösterilmesi gerektiğini öğrenmek için "Birleşik Kayıt modları" altındaki [Akış Çizelgesine](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) bakın. <br> 3. Multi-Factor Authentication veya SSPR ilkesinin son değiştirilme sıklığını belirleme. Değişiklik son zamanlarda, güncelleştirilmiş ilkenin yayılması biraz zaman alabilir.|

## <a name="troubleshooting-manage-mode"></a>Yönetme modu sorunlarını giderme

| Belirti | Sorun giderme adımları |
| --- | --- |
| Belirli bir yöntemi ekleme seçeneği yok. | 1. metodun Multi-Factor Authentication için mi yoksa SSPR için mi etkinleştirildiğini belirleme. <br> 2. Yöntem etkinleştirilirse, ilkeleri yeniden kaydedin ve test etmeden önce 1-2 saat bekleyin. <br> 3. Yöntem etkinleştirilirse, kullanıcının ayarlamaya izin verilen en fazla yöntem sayısını zaten ayarlamadığından emin olun.|

## <a name="disable-combined-registration"></a>Birleşik kaydı devre dışı bırak

Bir Kullanıcı yeni Birleşik deneyimde bir telefon numarası ve/veya mobil uygulama kaydettiğinde, hizmetimiz bu kullanıcı için bu yöntemler için bir bayrak kümesini (StrongAuthenticationMethods) damgalar. Bu işlevsellik, Multi-Factor Authentication gerektiğinde kullanıcının bu yöntemlerle Multi-Factor Authentication gerçekleştirmesini sağlar.

Bir yönetici önizlemeyi etkinleştirirse, kullanıcılar yeni deneyimle kaydedilir ve ardından Yönetici önizlemeyi devre dışı bırakır, kullanıcılar Multi-Factor Authentication için de kayıt yaptırmayı geri alabilir.

Birleştirilmiş kayıt işlemini tamamlamış bir Kullanıcı [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)' de geçerli self servis parola sıfırlama (SSPR) kayıt sayfasına gittiğinde, bu sayfaya erişebilmek için kullanıcıdan Multi-Factor Authentication gerçekleştirmesi istenir. Bu adım teknik bir bakış için beklenmektedir, ancak daha önce yalnızca SSPR için kaydolan kullanıcılar için yenidir. Bu ek adım, farklı bir güvenlik düzeyi sağlayarak kullanıcının güvenlik duruşunu iyileştirse de, Yöneticiler Multi-Factor Authentication yapabilmeleri için kullanıcılarını geri almak isteyebilir.  

### <a name="how-to-roll-back-users"></a>Kullanıcıları geri alma

Yönetici olarak, bir kullanıcının Multi-Factor Authentication ayarlarını sıfırlamak istiyorsanız, sonraki bölümde sağlanmış olan PowerShell betiğini kullanabilirsiniz. Betik, bir kullanıcının mobil uygulaması ve/veya telefon numarası için StrongAuthenticationMethods özelliğini temizler. Kullanıcılarınız için bu betiği çalıştırırsanız, ihtiyaç duyduklarında Multi-Factor Authentication için yeniden kaydolmaları gerekir. Etkilenen tüm kullanıcıları geri almadan önce bir veya iki kullanıcıyla geri alma sınamasını öneririz.

Aşağıdaki adımlar, bir kullanıcıyı veya kullanıcı grubunu geri almanıza yardımcı olur.

#### <a name="prerequisites"></a>Önkoşullar

1. Uygun Azure AD PowerShell modüllerini yükler. Bir PowerShell penceresinde, modülleri yüklemek için şu komutları çalıştırın:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Etkilenen Kullanıcı nesne kimliklerinin listesini, satır başına bir KIMLIĞE sahip bir metin dosyası olarak bilgisayarınıza kaydedin. Dosyanın konumunu unutmayın.
1. Aşağıdaki betiği bilgisayarınıza kaydedin ve komut dosyasının konumunu aklınızda olun:

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>Geri alma

Bir PowerShell penceresinde, komut dosyası ve Kullanıcı dosyası konumlarını sağlayarak aşağıdaki komutu çalıştırın. İstendiğinde genel yönetici kimlik bilgilerini girin. Komut dosyası her bir Kullanıcı güncelleştirme işleminin sonucunu çıktı olarak dolacak.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Önizleme deneyimini devre dışı bırakma

Kullanıcılarınızın önizleme deneyimini devre dışı bırakmak için şu adımları izleyin:

1. Azure portal Kullanıcı Yöneticisi olarak oturum açın.
2. **Azure Active Directory** > **Kullanıcı ayarları** ' na gidin > **erişim paneli Önizleme özellikleri ayarlarını yönetin**.
3. **Kullanıcılar altında güvenlik bilgilerini kaydetmek ve yönetmek için Önizleme özelliklerini kullanabilir**, seçiciyi **none**olarak ayarlayabilir ve ardından **Kaydet**' i seçin.

Kullanıcılardan artık önizleme deneyimi kullanılarak kaydolması istenmez.

## <a name="next-steps"></a>Sonraki adımlar

* [Self servis parola sıfırlama ve Azure Multi-Factor Authentication için Birleşik kaydın genel önizlemesi hakkında daha fazla bilgi edinin](concept-registration-mfa-sspr-combined.md)
