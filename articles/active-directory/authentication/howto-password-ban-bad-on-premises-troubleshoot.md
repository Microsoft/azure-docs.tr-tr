---
title: Azure AD parola koruması sorunlarını giderme-Azure Active Directory
description: Azure AD parola koruması genel sorunlarını anlama
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62395b0b6f1ed152292106a774c1e2f7c6d4f11f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893281"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD parola koruması sorunlarını giderme

Azure AD parola koruması 'nın dağıtımı sonrasında, sorun giderme gerekebilir. Bu makale, bazı yaygın sorun giderme adımlarını anlamanıza yardımcı olmak için ayrıntılara gider.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC Aracısı dizinde bir ara sunucu bulamıyor

Bu sorunun ana belirtisi, DC Aracısı Yönetici olay günlüğündeki 30017 olaydır.

Bu sorunun olağan nedeni, bir ara sunucu henüz kaydedilmemiştir. Bir ara sunucu kaydedilmişse, belirli bir DC Aracısı bu proxy 'yi görebilene kadar AD çoğaltma gecikmesi nedeniyle biraz gecikme olabilir.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC Aracısı bir ara sunucu ile iletişim kuramıyor

Bu sorunun ana belirtisi, DC Aracısı Yönetici olay günlüğündeki 30018 olaydır. Bu sorun birkaç olası nedeni olabilir:

1. DC Aracısı ağın yalıtılmış bir bölümünde bulunur ve bu, kayıtlı ara sunucu (lar) a ağ bağlantısına izin vermez. Bu sorun, diğer DC aracılarının Azure 'dan parola ilkelerini indirmek için proxy (ler) ile iletişim kurabildiği sürece zararsız olabilir. İndirildikten sonra, bu ilkeler, SYSVOL paylaşımındaki ilke dosyalarının çoğaltılması yoluyla yalıtılmış DC tarafından elde edilir.

1. Proxy konak makinesi RPC uç nokta Eşleyici uç noktasına erişimi engelliyor (bağlantı noktası 135)

   Azure AD parola koruma proxy yükleyicisi, 135 numaralı bağlantı noktasına erişime izin veren bir Windows Güvenlik Duvarı gelen kuralı otomatik olarak oluşturur. Bu kural daha sonra silinirse veya devre dışıysa, DC aracıları ara sunucu hizmetiyle iletişim kuramaz. Yerleşik Windows Güvenlik Duvarı başka bir güvenlik duvarı ürününün yerine devre dışı bırakılmışsa, bu güvenlik duvarını 135 numaralı bağlantı noktasına erişime izin verecek şekilde yapılandırmanız gerekir.

1. Proxy konak makinesi, proxy hizmeti tarafından dinlenen RPC uç noktasına (dinamik veya statik) erişimi engelliyor

   Azure AD parola koruma proxy yükleyicisi, Azure AD parola koruma proxy hizmeti tarafından listelenen tüm gelen bağlantı noktalarına erişime izin veren bir Windows Güvenlik Duvarı gelen kuralı otomatik olarak oluşturur. Bu kural daha sonra silinirse veya devre dışıysa, DC aracıları ara sunucu hizmetiyle iletişim kuramaz. Yerleşik Windows Güvenlik Duvarı başka bir güvenlik duvarı ürününün yerine devre dışı bırakılmışsa, bu güvenlik duvarını Azure AD parola koruma proxy hizmeti tarafından listelenen tüm gelen bağlantı noktalarına erişime izin verecek şekilde yapılandırmanız gerekir. Bu yapılandırma, proxy hizmeti belirli bir statik RPC bağlantı noktasını dinlemek üzere yapılandırıldıysa (`Set-AzureADPasswordProtectionProxyConfiguration` cmdlet 'ini kullanarak) daha belirgin hale getirilebilir.

1. Proxy konak makinesi, etki alanı denetleyicilerinin makinede oturum açmasına izin verecek şekilde yapılandırılmamış. Bu davranış, "Bu bilgisayara ağ üzerinden eriş" Kullanıcı ayrıcalık ataması aracılığıyla denetlenir. Ormandaki tüm etki alanlarında bulunan tüm etki alanı denetleyicilerine bu ayrıcalık verilmelidir. Bu ayar genellikle daha büyük ağ sağlamlaştırma çabalarının bir parçası olarak sınırlandırılır.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxy hizmeti Azure ile iletişim kuramıyor

1. Proxy makinenin, [dağıtım gereksinimlerinde](howto-password-ban-bad-on-premises-deploy.md)listelenen uç noktalara bağlantısı olduğundan emin olun.

1. Ormanın ve tüm proxy sunucularının aynı Azure kiracısına karşı kaydedildiğinden emin olun.

   `Get-AzureADPasswordProtectionProxy` ve `Get-AzureADPasswordProtectionDCAgent` PowerShell cmdlet 'lerini çalıştırıp, ardından döndürülen her öğenin `AzureTenant` özelliğini karşılaştıran bu gereksinimi kontrol edebilirsiniz. Doğru işlem için, bildirilen kiracı adı tüm DC aracıları ve proxy sunucuları genelinde aynı olmalıdır.

   Bir Azure kiracı kaydı uyuşmazlığı koşulu varsa, bu sorun, gerekli olduğu gibi `Register-AzureADPasswordProtectionProxy` ve/veya `Register-AzureADPasswordProtectionForest` PowerShell cmdlet 'leri çalıştırılarak düzeltilebilir ve tüm kayıtlar için aynı Azure kiracısındaki kimlik bilgilerini kullandığınızdan emin olabilir.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC Aracısı, parola ilkesi dosyalarını şifreleyemedi veya şifresini çözemedi

Azure AD parola koruması, Microsoft anahtar dağıtım hizmeti tarafından sağlanan şifreleme ve şifre çözme işlevlerine kritik bir bağımlılığa sahiptir. Şifreleme veya şifre çözme sorunları çeşitli belirtilerle bildirimde bulunabilir ve birçok olası nedeni olabilir.

1. KDS hizmetinin, bir etki alanındaki tüm Windows Server 2012 ve üzeri etki alanı denetleyicilerinde etkin ve çalışır durumda olduğundan emin olun.

   Varsayılan olarak, KDS hizmetinin hizmet başlatma modu El Ile (tetikleyici başlatma) olarak yapılandırılmıştır. Bu yapılandırma, bir istemcinin hizmeti ilk kez kullanmaya çalıştığı, isteğe bağlı olarak başlatıldığı anlamına gelir. Bu varsayılan hizmet başlatma modu, Azure AD parola korumasının çalışması için kabul edilebilir.

   KDS hizmeti başlatma modu devre dışı olarak yapılandırıldıysa, Azure AD parola koruması düzgün çalışmadan önce bu yapılandırma düzeltilmelidir.

   Bu sorunun basit bir testi, hizmet yönetimi MMC konsolu aracılığıyla veya diğer yönetim araçlarını kullanarak KDS hizmetini el ile başlatmak (örneğin, bir komut istemi konsolundan "net start kdssvc" komutunu çalıştırmak). KDS hizmetinin başarıyla başlaması bekleniyordu ve çalışır durumda kalır.

   KDS hizmetinin başlatılamamasına neden olan en yaygın kök nedeni, Active Directory etki alanı denetleyicisi nesnesinin varsayılan etki alanı denetleyicileri OU 'su dışında konumlandırılabilileridir. Bu yapılandırma KDS hizmeti tarafından desteklenmiyor ve Azure AD parola koruması tarafından uygulanan bir kısıtlama değil. Bu koşulun düzeltilmesi, etki alanı denetleyicisi nesnesini varsayılan etki alanı denetleyicileri OU 'su altındaki bir konuma taşımadır.

1. Windows Server 2012 R2 'den Windows Server 2016 ' ye uyumsuz KDS şifreli arabellek biçimi değişikliği

   Windows Server 2016 ' de, KDS şifreli arabelleklerin biçimini değiştiren bir KDS Güvenlik onarımı eklenmiştir; Bu arabellekler bazen Windows Server 2012 ve Windows Server 2012 R2 'de şifre çözme işlemi başarısız olur. Ters yön, Windows Server 2012 ' de KDS ile şifrelenen ve Windows Server 2012 R2 'nin her zaman Windows Server 2016 ve sonrasında şifresini başarıyla çözmesini sağlayan, sorunsuz bir yönlerdir. Active Directory etki alanlarınızda etki alanı denetleyicileri bu işletim sistemlerinin bir karışımını çalıştırıyorsa, zaman zaman Azure AD parola koruması şifre çözme hatalarının bildirilmesi gerekebilir. Bu hataların zamanlama veya belirtilerini güvenlik düzeltmesinin doğası halinde doğru bir şekilde tahmin etmek ve etki alanı denetleyicisinin belirli bir zamanda verileri hangi Azure AD parolasıyla koruma DC Aracısı tarafından şifreleneceğini belirleyici olmaması mümkün değildir.

   Microsoft bu soruna yönelik bir sorunu araştırmaktadır, ancak henüz hiç ETA yok. Bu sırada, Active Directory etki alanında bu uyumsuz işletim sistemlerinin bir karışımını çalıştırmayan dışında, bu sorun için geçici çözüm yoktur. Diğer bir deyişle, yalnızca Windows Server 2012 ve Windows Server 2012 R2 etki alanı denetleyicilerini çalıştırmalısınız veya yalnızca Windows Server 2016 ve etki alanı denetleyicilerini çalıştırmalısınız.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Zayıf parolalar kabul ediliyor ancak olmamalıdır

Bu sorunun çeşitli nedenleri olabilir.

1. DC aracılarınız, süresi biten bir genel önizleme yazılım sürümünü çalıştırıyor. Bkz. [genel ÖNIZLEME DC Aracısı yazılımının süresi doldu](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. DC aracılarınız bir ilkeyi indiremez veya mevcut ilkelerin şifresini çözemiyor. Yukarıdaki konularda olası nedenleri kontrol edin.

1. Parola ilkesi zorla modu hala denetim olarak ayarlanmıştır. Bu yapılandırma etkin ise, Azure AD parola koruma portalı 'nı kullanmaya zorlamak için yeniden yapılandırın. Bkz. [parola korumasını etkinleştirme](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Parola ilkesi devre dışı bırakıldı. Bu yapılandırma etkin ise, Azure AD parola koruma portalı 'nı kullanarak etkin olarak yeniden yapılandırın. Bkz. [parola korumasını etkinleştirme](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Etki alanındaki tüm etki alanı denetleyicilerine DC Aracısı yazılımını yüklememedi. Bu durumda, uzak Windows istemcilerinin parola değiştirme işlemi sırasında belirli bir etki alanı denetleyicisini hedeflemesini sağlamak zordur. DC Aracısı yazılımının yüklü olduğu belirli bir DC 'yi başarıyla hedeflediğinizi düşünüyorsanız, DC Aracısı Yönetici olay günlüğü ' ne iki kez göz ayırarak doğrulayabilirsiniz: sonuca bakılmaksızın, parolanın sonucunu belgelemek için en az bir olay olacaktır doğrulamasına. Parolası değiştirilen Kullanıcı için bir olay yoksa, parola değişikliği muhtemelen farklı bir etki alanı denetleyicisi tarafından işlenir.

   Alternatif bir test olarak, DC Aracısı yazılımının yüklü olduğu bir DC 'ye doğrudan oturum açarken, parolaları settingın olarak değiştirmeyi deneyin. Bu teknik, üretim Active Directory etki alanları için önerilmez.

   Bu sınırlamalara bağlı olarak, DC Aracısı yazılımının artımlı dağıtımı desteklenirken, Microsoft, DC Aracısı yazılımının bir etki alanındaki tüm etki alanı denetleyicilerine en kısa sürede yüklenmesini özellikle önerir.

1. Parola doğrulama algoritması aslında beklendiği gibi çalışıyor olabilir. [Parolaların nasıl değerlendirildiğini](concept-password-ban-bad.md#how-are-passwords-evaluated)görün.

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil. exe zayıf bir DSRM parolası ayarlayamazsa

Active Directory, etki alanının parola karmaşıklığı gereksinimlerini karşıladığından emin olmak için her zaman yeni bir dizin hizmetleri onarım modu parolasını doğrular; Bu doğrulama Ayrıca Azure AD parola koruması gibi parola filtre dll 'lerine da çağrı yapılır. Yeni DSRM parolası reddedilirse aşağıdaki hata iletisi oluşur:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Azure AD parola koruması, Active Directory DSRM parolası için parola doğrulama olay günlüğü olaylarını günlüğe kaydettiği zaman, olay günlüğü iletilerinin bir Kullanıcı adı içermeyecektir. Bu davranış, DSRM hesabının gerçek Active Directory etki alanının parçası olmayan bir yerel hesap olduğu için oluşur.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Etki alanı denetleyicisi çoğaltma yükseltmesi zayıf bir DSRM parolası nedeniyle başarısız oluyor

DC yükseltme işlemi sırasında, yeni Dizin Hizmetleri onarım modu parolası, doğrulama için etki alanında var olan bir DC 'ye gönderilir. Yeni DSRM parolası reddedilirse aşağıdaki hata iletisi oluşur:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Yukarıdaki sorun içinde olduğu gibi, tüm Azure AD parola koruması parola doğrulama sonucu olayları bu senaryo için boş kullanıcı adlarına sahip olur.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Etki alanı denetleyicisi indirgeme zayıf bir yerel yönetici parolası nedeniyle başarısız oluyor

Hala DC Aracısı yazılımını çalıştıran bir etki alanı denetleyicisini indirgemek için desteklenir. Yöneticiler, indirgeme yordamı sırasında DC Aracısı yazılımının geçerli parola ilkesini zorlamaya devam ettiğinden haberdar edilmelidir. Yeni yerel yönetici hesabı parolası (indirgeme işleminin parçası olarak belirtilir), diğer tüm parolalar gibi onaylanır. Microsoft, DC indirgeme yordamının bir parçası olarak yerel yönetici hesapları için güvenli parolaların seçilmesini önerir.

İndirgeme başarılı olduktan ve etki alanı denetleyicisi yeniden başlatıldığında ve bir normal üye sunucu olarak çalıştırıldığında, DC Aracısı yazılımı pasif modda çalışmaya geri döner. Daha sonra herhangi bir zamanda kaldırılabilir.

## <a name="booting-into-directory-services-repair-mode"></a>Dizin Hizmetleri onarım modunda önyükleme

Etki alanı denetleyicisi dizin hizmetleri onarım modunda önbaşlatıldıysa, DC aracısı parola filtresi dll 'si bu durumu algılar ve şu anda etkin olan ilkeden bağımsız olarak tüm parola doğrulama veya zorlama etkinliklerinin devre dışı olmasına neden olur yapılandırmada. DC aracı parola filtresi dll 'si, bir 10023 uyarı olayını yönetici olay günlüğüne kaydeder, örneğin:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Genel Önizleme DC Aracısı yazılımının süresi doldu

Azure AD parola koruması genel önizleme dönemi boyunca, DC Aracısı yazılımı aşağıdaki tarihlerde parola doğrulama isteklerini işlemeyi durduracak biçimde sabit olarak kodlanmıştır:

* Sürüm 1.2.65.0, Eylül 1 2019 ' de parola doğrulama isteklerini işlemeyi durduracak.
* Sürüm 1.2.25.0 ve önceki sürümleri, 1 2019 Temmuz 'da parola doğrulama isteklerini işlemeyi durdurdu.

Son Tarih yaklaşımının ardından, tüm zaman sınırlı DC Aracısı sürümleri, aşağıdaki gibi görünen önyükleme zamanında DC Aracısı Yönetici olay günlüğüne bir 10021 olayı yayacaktır:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Son tarih geçtikten sonra, tüm zaman sınırlı DC Aracısı sürümleri, aşağıdaki gibi görünen önyükleme zamanında DC Aracısı Yönetici olay günlüğüne bir 10022 olayı yayar:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Son Tarih yalnızca ilk önyüklemede denetlendiğinden, takvim son tarihi geçtikten sonra bu olayları göremeyebilirsiniz. Son Tarih tanındıktan sonra, etki alanı denetleyicisi veya daha büyük ortam üzerinde hiçbir olumsuz efekt, tüm parolaların otomatik olarak onaylanacaktır.

> [!IMPORTANT]
> Microsoft, zaman aşımına uğradı genel önizleme DC aracılarının en son sürüme hemen yükseltilmesini önerir.

Ortamınızdaki, yükseltilmesi gereken DC aracılarını bulmayı kolaylaştıran kolay bir yol `Get-AzureADPasswordProtectionDCAgent` cmdlet 'ini çalıştırmalıdır, örneğin:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

Bu konu için, SoftwareVersion alanı, göz atmak için önemli bir özelliktir. PowerShell filtrelemesini Ayrıca, gerekli temel sürümde zaten veya üzerinde olan DC aracılarını filtrelemek için de kullanabilirsiniz; örneğin:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD parola koruma proxy yazılımı, hiçbir sürümde zaman sınırlı değildir. Microsoft, hem DC 'nin hem de ara sunucu aracılarının yayımlandıklarında en son sürümlere yükseltilmesini öneriyor. `Get-AzureADPasswordProtectionProxy` cmdlet 'i, DC aracıları için yukarıdaki örneğe benzer şekilde yükseltmeler gerektiren proxy aracılarını bulmak için kullanılabilir.

Belirli yükseltme yordamları hakkında daha fazla bilgi için [DC aracısını yükseltme](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) ve [proxy aracısını yükseltme](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-agent) bölümüne bakın.

## <a name="emergency-remediation"></a>Acil durum Düzeltme

DC Aracısı hizmetinin soruna neden olduğu bir durum oluşursa, DC Aracısı hizmeti hemen kapatılabilir. DC aracısı parola filtresi dll 'si hala çalışır durumda olmayan hizmeti çağırmaya çalışır ve uyarı olaylarını günlüğe kaydeder (10012, 10013), ancak tüm gelen parolalar bu süre içinde kabul edilir. Daha sonra DC Aracısı hizmeti, gerektiğinde "devre dışı" başlatma türü ile Windows hizmet denetimi Yöneticisi aracılığıyla da yapılandırılabilir.

Başka bir düzeltme ölçüsü, etkinleştirme modunu Azure AD parola koruma portalında Hayır olarak ayarlamak olacaktır. Güncelleştirilmiş ilke indirildikten sonra, her DC Aracısı hizmeti, tüm parolaların olduğu gibi kabul edildiği bir quiescent moduna geçer. Daha fazla bilgi için bkz. [zorla modu](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="removal"></a>Kaldırılmasını

Azure AD parola koruma yazılımını kaldırmaya ve etki alanından ve ormandan ilgili tüm durumu temizlemesine karar verdiyseniz, bu görev aşağıdaki adımlar kullanılarak gerçekleştirilebilir:

> [!IMPORTANT]
> Bu adımları sırasıyla gerçekleştirmek önemlidir. Proxy hizmeti 'nin herhangi bir örneği çalışmaya ayrıldıysa, Service ConnectionPoint nesnesini düzenli olarak yeniden oluşturur. DC Aracısı hizmeti 'nin herhangi bir örneği çalışmaya ayrıldıysa, serviceConnectionPoint nesnesini ve SYSVOL durumunu düzenli olarak yeniden oluşturur.

1. Tüm makinelerden proxy yazılımını kaldırın. Bu adım için yeniden **başlatma gerekmez.**
2. DC Aracısı yazılımını tüm etki alanı denetleyicilerinden kaldırın. Bu adım **için** yeniden başlatma gerekir.
3. Her bir etki alanı adlandırma bağlamındaki tüm proxy hizmeti bağlantı noktalarını el ile kaldırın. Bu nesnelerin konumu aşağıdaki Active Directory PowerShell komutuyla bulunabilir:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   $Keywords değişken değerinin sonundaki yıldız işaretini ("*") atlayın.

   `Get-ADObject` komutu aracılığıyla bulunan elde edilen nesneler, `Remove-ADObject`veya el ile silinmiş olabilir.

4. Her bir etki alanı adlandırma bağlamındaki tüm DC Aracısı bağlantı noktalarını el ile kaldırın. Yazılımın ne kadar büyük ölçüde dağıtıldığını bağlı olarak, ormanda etki alanı denetleyicisi başına bu nesnelerden biri olabilir. Bu nesnenin konumu aşağıdaki Active Directory PowerShell komutuyla bulunabilir:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   `Get-ADObject` komutu aracılığıyla bulunan elde edilen nesneler, `Remove-ADObject`veya el ile silinmiş olabilir.

   $Keywords değişken değerinin sonundaki yıldız işaretini ("*") atlayın.

5. Orman düzeyindeki yapılandırma durumunu el ile kaldırın. Orman yapılandırma durumu, Active Directory yapılandırma adlandırma bağlamındaki bir kapsayıcıda tutulur. Bulunabilir ve aşağıdaki gibi silinebilir:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Aşağıdaki klasörü ve tüm içeriğini el ile silerek tüm SYSVOL ilgili durumlarını el ile kaldırın:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Gerekirse, bu yola belirli bir etki alanı denetleyicisinde yerel olarak de erişilebilir. Varsayılan konum aşağıdaki yola benzer bir şeydir:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   SYSVOL paylaşımının varsayılan olmayan bir konumda yapılandırılmış olması durumunda bu yol farklıdır.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD parola koruması hakkında sık sorulan sorular](howto-password-ban-bad-on-premises-faq.md)

Genel ve özel yasaklanmış parola listeleri hakkında daha fazla bilgi için bkz. [Hatalı parolalar](concept-password-ban-bad.md)
