---
title: Şirket içi Azure AD parola koruması sorunlarını giderme
description: Şirket içi Active Directory Domain Services ortamı için Azure AD parola koruması sorunlarını giderme hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2bbc1c555824d4c632c5bf85a9cd0aa83087fc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648735"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Sorun giderme: şirket içi Azure AD parola koruması

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

   Azure AD parola koruma proxy yükleyicisi, Azure AD parola koruma proxy hizmeti tarafından listelenen tüm gelen bağlantı noktalarına erişime izin veren bir Windows Güvenlik Duvarı gelen kuralı otomatik olarak oluşturur. Bu kural daha sonra silinirse veya devre dışıysa, DC aracıları ara sunucu hizmetiyle iletişim kuramaz. Yerleşik Windows Güvenlik Duvarı başka bir güvenlik duvarı ürününün yerine devre dışı bırakılmışsa, bu güvenlik duvarını Azure AD parola koruma proxy hizmeti tarafından listelenen tüm gelen bağlantı noktalarına erişime izin verecek şekilde yapılandırmanız gerekir. Bu yapılandırma, proxy hizmeti belirli bir statik RPC bağlantı noktasını dinlemek üzere yapılandırılmışsa (cmdlet 'ini kullanarak) daha belirgin hale getirilebilir `Set-AzureADPasswordProtectionProxyConfiguration` .

1. Proxy konak makinesi, etki alanı denetleyicilerinin makinede oturum açmasına izin verecek şekilde yapılandırılmamış. Bu davranış, "Bu bilgisayara ağ üzerinden eriş" Kullanıcı ayrıcalık ataması aracılığıyla denetlenir. Ormandaki tüm etki alanlarında bulunan tüm etki alanı denetleyicilerine bu ayrıcalık verilmelidir. Bu ayar genellikle daha büyük ağ sağlamlaştırma çabalarının bir parçası olarak sınırlandırılır.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxy hizmeti Azure ile iletişim kuramıyor

1. Proxy makinenin, [dağıtım gereksinimlerinde](howto-password-ban-bad-on-premises-deploy.md)listelenen uç noktalara bağlantısı olduğundan emin olun.

1. Ormanın ve tüm proxy sunucularının aynı Azure kiracısına karşı kaydedildiğinden emin olun.

   Ve PowerShell cmdlet 'lerini çalıştırarak bu gereksinimi denetleyebilir  `Get-AzureADPasswordProtectionProxy` ve `Get-AzureADPasswordProtectionDCAgent` ardından `AzureTenant` döndürülen her bir öğenin özelliğini karşılaştırabilirsiniz. Doğru işlem için, bildirilen kiracı adı tüm DC aracıları ve proxy sunucuları genelinde aynı olmalıdır.

   Bir Azure kiracı kaydı uyuşmazlığı koşulu varsa, bu sorun `Register-AzureADPasswordProtectionProxy` ve/veya `Register-AzureADPasswordProtectionForest` PowerShell cmdlet 'leri gerektiği şekilde çalıştırılarak düzeltilebilir ve tüm kayıtlar Için aynı Azure kiracısındaki kimlik bilgilerini kullandığınızdan emin olabilirsiniz.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC Aracısı, parola ilkesi dosyalarını şifreleyemedi veya şifresini çözemedi

Azure AD parola koruması, Microsoft anahtar dağıtım hizmeti tarafından sağlanan şifreleme ve şifre çözme işlevlerine kritik bir bağımlılığa sahiptir. Şifreleme veya şifre çözme sorunları çeşitli belirtilerle bildirimde bulunabilir ve birçok olası nedeni olabilir.

1. KDS hizmetinin, bir etki alanındaki tüm Windows Server 2012 ve üzeri etki alanı denetleyicilerinde etkin ve çalışır durumda olduğundan emin olun.

   Varsayılan olarak, KDS hizmetinin hizmet başlatma modu El Ile (tetikleyici başlatma) olarak yapılandırılmıştır. Bu yapılandırma, bir istemcinin hizmeti ilk kez kullanmaya çalıştığı, isteğe bağlı olarak başlatıldığı anlamına gelir. Bu varsayılan hizmet başlatma modu, Azure AD parola korumasının çalışması için kabul edilebilir.

   KDS hizmeti başlatma modu devre dışı olarak yapılandırıldıysa, Azure AD parola koruması düzgün çalışmadan önce bu yapılandırma düzeltilmelidir.

   Bu sorunun basit bir testi, hizmet yönetimi MMC konsolu aracılığıyla veya diğer yönetim araçlarını kullanarak KDS hizmetini el ile başlatmak (örneğin, bir komut istemi konsolundan "net start kdssvc" komutunu çalıştırmak). KDS hizmetinin başarıyla başlaması bekleniyordu ve çalışır durumda kalır.

   KDS hizmetinin başlatılamamasına neden olan en yaygın kök nedeni, Active Directory etki alanı denetleyicisi nesnesinin varsayılan etki alanı denetleyicileri OU 'su dışında konumlandırılabilileridir. Bu yapılandırma KDS hizmeti tarafından desteklenmiyor ve Azure AD parola koruması tarafından uygulanan bir kısıtlama değil. Bu koşulun düzeltilmesi, etki alanı denetleyicisi nesnesini varsayılan etki alanı denetleyicileri OU 'su altındaki bir konuma taşımadır.

1. Windows Server 2012 R2 'den Windows Server 2016 ' ye uyumsuz KDS şifreli arabellek biçimi değişikliği

   Windows Server 2016 ' de, KDS şifreli arabelleklerin biçimini değiştiren bir KDS Güvenlik onarımı eklenmiştir; Bu arabellekler bazen Windows Server 2012 ve Windows Server 2012 R2 'de şifre çözme işlemi başarısız olur. Ters yön, Windows Server 2012 ' de KDS ile şifrelenen ve Windows Server 2012 R2 'nin her zaman Windows Server 2016 ve sonrasında şifresini başarıyla çözmesini sağlayan, sorunsuz bir yönlerdir. Active Directory etki alanlarınızda etki alanı denetleyicileri bu işletim sistemlerinin bir karışımını çalıştırıyorsa, zaman zaman Azure AD parola koruması şifre çözme hatalarının bildirilmesi gerekebilir. Bu hataların zamanlama veya belirtilerini güvenlik düzeltmesinin doğası halinde doğru bir şekilde tahmin etmek ve etki alanı denetleyicisinin belirli bir zamanda verileri hangi Azure AD parolasıyla koruma DC Aracısı tarafından şifreleneceğini belirleyici olmaması mümkün değildir.

   Active Directory etki alanında bu uyumsuz işletim sistemlerinin karışımını çalıştırmayan dışında, bu sorun için geçici çözüm yoktur. Diğer bir deyişle, yalnızca Windows Server 2012 ve Windows Server 2012 R2 etki alanı denetleyicilerini çalıştırmalısınız veya yalnızca Windows Server 2016 ve etki alanı denetleyicilerini çalıştırmalısınız.

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>DC Aracısı ormanın kaydolmadı

Bu sorunun belirtisi, bölümünde yer alan DC yönetim kanalında oturum açan 30016 olaydır:

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

Bu sorunun iki olası nedeni vardır.

1. Orman gerçekten kaydedilmemiş. Sorunu çözmek için lütfen [dağıtım gereksinimleri](howto-password-ban-bad-on-premises-deploy.md)bölümünde açıklandığı gibi Register-AzureADPasswordProtectionForest komutunu çalıştırın.
1. Orman kaydettirildi, ancak DC Aracısı orman kayıt verilerinin şifresini çözemez. Bu durum, yukarıda listelenen #2 sorun temel nedenine sahiptir ve DC Aracısı altında listelenen, [parola ilkesi dosyalarını şifreleyemez veya şifresini çözemez](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files). Bu teorik 'i doğrulamanın kolay bir yolu, bu hatayı yalnızca Windows Server 2012 veya Windows Server 2012 R2 2 etki alanı denetleyicileri üzerinde çalışan DC aracılarında, Windows Server 2016 ve üzeri etki alanı denetleyicileri üzerinde çalışan DC aracılarında görecağından emin olun. Geçici çözüm de aynıdır: tüm etki alanı denetleyicilerini Windows Server 2016 veya sonraki bir sürüme yükseltin.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Zayıf parolalar kabul ediliyor ancak olmamalıdır

Bu sorunun çeşitli nedenleri olabilir.

1. DC aracılarınız, süresi biten bir genel önizleme yazılım sürümünü çalıştırıyor. Bkz. [genel ÖNIZLEME DC Aracısı yazılımının süresi doldu](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. DC aracılarınız bir ilkeyi indiremez veya mevcut ilkelerin şifresini çözemiyor. Yukarıdaki konularda olası nedenleri kontrol edin.

1. Parola ilkesi zorla modu hala denetim olarak ayarlanmıştır. Bu yapılandırma etkin ise, Azure AD parola koruma portalı 'nı kullanmaya zorlamak için yeniden yapılandırın. Daha fazla bilgi için bkz. [Işlem modları](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Parola ilkesi devre dışı bırakıldı. Bu yapılandırma etkin ise, Azure AD parola koruma portalı 'nı kullanarak etkin olarak yeniden yapılandırın. Daha fazla bilgi için bkz. [Işlem modları](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Etki alanındaki tüm etki alanı denetleyicilerine DC Aracısı yazılımını yüklememedi. Bu durumda, uzak Windows istemcilerinin parola değiştirme işlemi sırasında belirli bir etki alanı denetleyicisini hedeflemesini sağlamak zordur. DC Aracısı yazılımının yüklü olduğu belirli bir DC 'yi başarıyla hedeflediğinizi düşünüyorsanız, DC Aracısı Yönetici olay günlüğü ' ne iki kez göz ayırarak doğrulayabilirsiniz: sonuca bakılmaksızın, parola doğrulamanın sonucunu belgelemek için en az bir olay olacaktır. Parolası değiştirilen Kullanıcı için bir olay yoksa, parola değişikliği muhtemelen farklı bir etki alanı denetleyicisi tarafından işlenir.

   Alternatif bir test olarak, DC Aracısı yazılımının yüklü olduğu bir DC 'ye doğrudan oturum açarken, parolaları settingın olarak değiştirmeyi deneyin. Bu teknik, üretim Active Directory etki alanları için önerilmez.

   Bu sınırlamalara bağlı olarak, DC Aracısı yazılımının artımlı dağıtımı desteklenirken, Microsoft, DC Aracısı yazılımının bir etki alanındaki tüm etki alanı denetleyicilerine en kısa sürede yüklenmesini özellikle önerir.

1. Parola doğrulama algoritması aslında beklendiği gibi çalışıyor olabilir. [Parolaların nasıl değerlendirildiğini](concept-password-ban-bad.md#how-are-passwords-evaluated)görün.

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe zayıf bir DSRM parolası ayarlayamazsa

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

Etki alanı denetleyicisi dizin hizmetleri onarım modunda önbaşlatıldıysa, DC aracısı parola filtresi dll 'si bu durumu algılar ve şu anda etkin olan ilke yapılandırmasından bağımsız olarak tüm parola doğrulama veya zorlama etkinliklerinin devre dışı olmasına neden olur. DC aracı parola filtresi dll 'si, bir 10023 uyarı olayını yönetici olay günlüğüne kaydeder, örneğin:

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

Ortamınızda yükseltilmesi gereken DC aracılarını bulmayı kolay bir yöntem cmdlet 'i çalıştırıyor `Get-AzureADPasswordProtectionDCAgent` , örneğin:

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

Azure AD parola koruma proxy yazılımı, hiçbir sürümde zaman sınırlı değildir. Microsoft, hem DC 'nin hem de ara sunucu aracılarının yayımlandıklarında en son sürümlere yükseltilmesini öneriyor. `Get-AzureADPasswordProtectionProxy`Cmdlet 'i, DC aracıları için yukarıdaki örneğe benzer şekilde yükseltmeleri gerektiren proxy aracılarını bulmak için kullanılabilir.

Belirli yükseltme yordamları hakkında daha fazla bilgi için [, DC aracısını yükseltme](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) ve [proxy hizmetini yükseltme](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) bölümüne bakın.

## <a name="emergency-remediation"></a>Acil durum Düzeltme

DC Aracısı hizmetinin soruna neden olduğu bir durum oluşursa, DC Aracısı hizmeti hemen kapatılabilir. DC aracısı parola filtresi dll 'si hala çalışır durumda olmayan hizmeti çağırmaya çalışır ve uyarı olaylarını günlüğe kaydeder (10012, 10013), ancak tüm gelen parolalar bu süre içinde kabul edilir. Daha sonra DC Aracısı hizmeti, gerektiğinde "devre dışı" başlatma türü ile Windows hizmet denetimi Yöneticisi aracılığıyla da yapılandırılabilir.

Başka bir düzeltme ölçüsü, etkinleştirme modunu Azure AD parola koruma portalında Hayır olarak ayarlamak olacaktır. Güncelleştirilmiş ilke indirildikten sonra, her DC Aracısı hizmeti, tüm parolaların olduğu gibi kabul edildiği bir quiescent moduna geçer. Daha fazla bilgi için bkz. [Işlem modları](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

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

   Komut aracılığıyla bulunan sonuç nesneleri, `Get-ADObject` daha sonra `Remove-ADObject` el ile veya silinmiş olabilir.

4. Her bir etki alanı adlandırma bağlamındaki tüm DC Aracısı bağlantı noktalarını el ile kaldırın. Yazılımın ne kadar büyük ölçüde dağıtıldığını bağlı olarak, ormanda etki alanı denetleyicisi başına bu nesnelerden biri olabilir. Bu nesnenin konumu aşağıdaki Active Directory PowerShell komutuyla bulunabilir:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Komut aracılığıyla bulunan sonuç nesneleri, `Get-ADObject` daha sonra `Remove-ADObject` el ile veya silinmiş olabilir.

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

## <a name="health-testing-with-powershell-cmdlets"></a>PowerShell cmdlet 'leri ile durum testi

AzureADPasswordProtection PowerShell modülü, yazılımın yüklü ve çalışır olduğu temel doğrulamayı gerçekleştiren, sistem durumu ile ilgili iki cmdlet içerir. Bu cmdlet 'leri yeni bir dağıtım kurulduktan sonra, düzenli aralıklarla ve bir sorun araştırıldıktan sonra çalıştırmak iyi bir fikirdir.

Her bir tek sistem durumu testi, temel bir başarılı veya başarısız sonucu döndürür ve hata durumunda isteğe bağlı bir ileti döndürür. Hatanın nedeninin açık olmaması durumunda, hatayı açıklayan hata olay günlüğü iletileri olup olmadığına bakın. Metin günlüğü iletilerinin etkinleştirilmesi da yararlı olabilir. Daha fazla ayrıntı için lütfen bkz. [Azure AD parola korumasını izleme](howto-password-ban-bad-on-premises-monitor.md).

## <a name="proxy-health-testing"></a>Proxy sistem durumu testi

Test-AzureADPasswordProtectionProxyHealth cmdlet 'i tek tek çalıştırılabilen iki sistem durumu testini destekler. Üçüncü bir mod, herhangi bir parametre girişi gerektirmeyen tüm testlerin çalışmasına izin verir.

### <a name="proxy-registration-verification"></a>Proxy kayıt doğrulaması

Bu test, proxy aracısının Azure 'a doğru şekilde kaydedildiğini ve Azure 'da kimlik doğrulaması yapabildiğini doğrular. Başarılı bir çalıştırma şöyle görünür:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Passed
```

Bir hata algılanırsa, test başarısız bir sonuç ve isteğe bağlı bir hata mesajı döndürür. Olası bir hata örneğidir:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Failed No proxy certificates were found - please run the Register-AzureADPasswordProtectionProxy cmdlet to register the proxy.
```

### <a name="proxy-verification-of-end-to-end-azure-connectivity"></a>Uçtan uca Azure bağlantısının proxy doğrulaması

Bu test,-VerifyProxyRegistration testinin bir üst kümesidir. Proxy aracısının Azure 'a doğru şekilde kaydedilmesini, Azure 'da kimlik doğrulaması yapabilmesini ve son olarak Azure 'a bir iletinin başarıyla gönderilebileceği bir denetim eklerse, tam uçtan uca iletişimin çalıştığını doğrulayabilirsiniz.

Başarılı bir çalıştırma şöyle görünür:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyAzureConnectivity

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyAzureConnectivity Passed
```

### <a name="proxy-verification-of-all-tests"></a>Tüm testlerin proxy doğrulaması

Bu mod, parametre girişi gerektirmeyen cmdlet tarafından desteklenen tüm testlerin toplu çalışmasına izin verir. Başarılı bir çalıştırma şöyle görünür:

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -TestAll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyTLSConfiguration  Passed
VerifyProxyRegistration Passed
VerifyAzureConnectivity Passed
```

## <a name="dc-agent-health-testing"></a>DC Aracısı sistem durumu testi

Test-AzureADPasswordProtectionDCAgentHealth cmdlet 'i, tek tek çalıştırılabilen çeşitli sistem durumu testlerini destekler. Üçüncü bir mod, herhangi bir parametre girişi gerektirmeyen tüm testlerin çalışmasına izin verir.

### <a name="basic-dc-agent-health-tests"></a>Temel DC Aracısı sistem durumu testleri

Aşağıdaki testlerin hepsi tek tek çalıştırılabilir ve kabul edilmez. Kısa bir açıklama

|DC Aracısı sistem durumu sınaması|Description|
| --- | :---: |
|-VerifyPasswordFilterDll|Parola filtresi dll 'sinin Şu anda yüklü olduğunu ve DC Aracısı hizmetini çağırabildiğini doğrular|
|-VerifyForestRegistration|Ormanın Şu anda kayıtlı olduğunu doğrular|
|-Doğrulamaları Yencryptionşifre çözme|Temel şifrelemenin ve şifre şifresinin Microsoft KDS hizmeti kullanılarak çalıştığını doğrular|
|-VerifyDomainIsUsingDFSR|Geçerli etki alanının SYSVOL çoğaltması için DFSR kullandığını doğrular|
|-Doğrulama Yazureconnectivity|Azure ile uçtan uca iletişimi doğrular tüm kullanılabilir proxy kullanarak çalışır|

-VerifyPasswordFilterDll test geçirmenin bir örneği aşağıda verilmiştir; diğer testler başarı durumunda şuna benzer görünecektir:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyPasswordFilterDll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyPasswordFilterDll Passed
```

### <a name="dc-agent-verification-of-all-tests"></a>Tüm testlerin DC Aracısı doğrulaması

Bu mod, parametre girişi gerektirmeyen cmdlet tarafından desteklenen tüm testlerin toplu çalışmasına izin verir. Başarılı bir çalıştırma şöyle görünür:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -TestAll

DiagnosticName             Result AdditionalInfo
--------------             ------ --------------
VerifyPasswordFilterDll    Passed
VerifyForestRegistration   Passed
VerifyEncryptionDecryption Passed
VerifyDomainIsUsingDFSR    Passed
VerifyAzureConnectivity    Passed
```

### <a name="connectivity-testing-using-specific-proxy-servers"></a>Belirli proxy sunucularını kullanarak bağlantı testi

Birçok sorun giderme durumu, DC aracıları ve proxy 'ler arasındaki ağ bağlantısını araştırmakta içerir. Özellikle bu tür sorunlara odaklanmak için kullanabileceğiniz iki sistem durumu testi vardır. Bu sınamalar belirli bir proxy sunucusunun belirtilmesini gerektirir.

#### <a name="verifying-connectivity-between-a-dc-agent-and-a-specific-proxy"></a>DC Aracısı ve belirli bir ara sunucu arasındaki bağlantıyı doğrulama

Bu test, DC aracısındaki ilk iletişim üzerinden proxy 'ye bağlantıyı doğrular. Proxy 'nin çağrıyı aldığını doğrular, ancak Azure ile hiçbir iletişim dahil değildir. Başarılı bir çalıştırma şöyle görünür:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Passed
```

Hedef sunucuda çalışan Proxy hizmetinin durdurulduğu bir hata koşulu aşağıda verilmiştir:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Failed The RPC endpoint mapper on the specified proxy returned no results; please check that the proxy service is running on that server.
```

#### <a name="verifying-connectivity-between-a-dc-agent-and-azure-using-a-specific-proxy"></a>DC Aracısı ve Azure arasındaki bağlantıyı doğrulama (belirli bir proxy kullanarak)

Bu test, belirli bir proxy kullanarak bir DC Aracısı ve Azure arasındaki uçtan uca tam bağlantıyı doğrular. Başarılı bir çalıştırma şöyle görünür:

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyAzureConnectivityViaSpecificProxy bpl2.bpl.com

DiagnosticName                          Result AdditionalInfo
--------------                          ------ --------------
VerifyAzureConnectivityViaSpecificProxy Passed
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD parola koruması hakkında sık sorulan sorular](howto-password-ban-bad-on-premises-faq.md)

Genel ve özel yasaklanmış parola listeleri hakkında daha fazla bilgi için bkz. [Hatalı parolalar](concept-password-ban-bad.md)
