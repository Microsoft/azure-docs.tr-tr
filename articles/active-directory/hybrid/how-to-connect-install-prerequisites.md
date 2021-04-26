---
title: 'Azure AD Connect: Önkoşullar ve donanım | Microsoft Docs'
description: Bu makalede, Azure AD Connect önkoşulları ve donanım gereksinimleri açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 108f81be678eb666b6f79ebbecc93f7bc88dc1d6
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107920"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect Önkoşulları
Bu makalede Azure Active Directory (Azure AD) Connect için Önkoşullar ve donanım gereksinimleri açıklanmaktadır.

## <a name="before-you-install-azure-ad-connect"></a>Azure AD Connect yüklemeden önce
Azure AD Connect yüklemeden önce, ihtiyacınız olan birkaç şey vardır.

### <a name="azure-ad"></a>Azure AD
* Bir Azure AD kiracının olması gerekir. [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)ile bir tane alırsınız. Azure AD Connect yönetmek için aşağıdaki portallardan birini kullanabilirsiniz:
  * [Azure Portal](https://portal.azure.com).
  * [Office portalı](https://portal.office.com).
* Azure AD 'de kullanmayı planladığınız [etki alanını ekleyin ve doğrulayın](../fundamentals/add-custom-domain.md) . Örneğin, kullanıcılarınız için contoso.com kullanmayı planlıyorsanız, bu etki alanının doğrulandığından ve yalnızca contoso.onmicrosoft.com varsayılan etki alanını kullanmadığınız emin olun.
* Bir Azure AD kiracısı, varsayılan olarak 50.000 nesnelerine izin verir. Etki alanınızı doğruladıktan sonra sınır 300.000 nesne artar. Azure AD 'de daha fazla nesne gerekiyorsa, sınırın daha da arttığı bir destek talebi açın. 500.000 ' den fazla nesneye ihtiyacınız varsa, Microsoft 365, Azure AD Premium veya Enterprise Mobility + Security gibi bir lisansa sahip olmanız gerekir.

### <a name="prepare-your-on-premises-data"></a>Şirket içi verilerinizi hazırlama
* [Azure AD ve Microsoft 365 eşitlemeden](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac)önce dizininizde yinelenen öğeler ve biçimlendirme sorunları gibi hataları belirlemek Için [ıddüzeltmesini](https://github.com/Microsoft/idfix) kullanın.
* [Azure AD 'de etkinleştirebileceğiniz isteğe bağlı eşitleme özelliklerini](how-to-connect-syncservice-features.md)gözden geçirin ve hangi özellikleri etkinleştirmeniz gerektiğini değerlendirin.

### <a name="on-premises-active-directory"></a>Şirket içi Active Directory
* Active Directory şeması sürümü ve orman işlev düzeyi Windows Server 2003 veya sonraki bir sürümü olmalıdır. Şema sürümü ve Orman düzeyi gereksinimleri karşılandığı sürece etki alanı denetleyicileri herhangi bir sürümü çalıştırabilir.
* Özellik *parola geri yazma* özelliğini kullanmayı planlıyorsanız, etki alanı denetleyicilerinin Windows Server 2012 veya sonraki bir sürümde olması gerekir.
* Azure AD tarafından kullanılan etki alanı denetleyicisi yazılabilir olmalıdır. Salt bir okuma etki alanı denetleyicisi (RODC) kullanılması *desteklenmez* ve Azure AD Connect herhangi bir yazma yeniden yönlendirmeyi izlemiyor.
* "Noktalı" kullanarak şirket içi ormanları veya etki alanlarını kullanma (ad bir nokta içerir ".") NetBIOS adları *desteklenmez*.
* [Active Directory geri dönüşüm kutusu 'nu etkinleştirmenizi](how-to-connect-sync-recycle-bin.md)öneririz.

### <a name="powershell-execution-policy"></a>PowerShell yürütme ilkesi
Azure Active Directory Connect, yüklemesinin bir parçası olarak imzalı PowerShell betikleri çalıştırır. PowerShell yürütme ilkesinin betikleri çalıştırmaya izin verdiğinden emin olun.

Yükleme sırasında önerilen yürütme ilkesi "RemoteSigned" dır.

PowerShell yürütme ilkesini ayarlama hakkında daha fazla bilgi için bkz. [set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).


### <a name="azure-ad-connect-server"></a>Azure AD Connect sunucusu
Azure AD Connect sunucusu kritik kimlik verileri içerir. Bu sunucuya yönetim erişiminin düzgün bir şekilde güvenliğinin sağlanması önemlidir. [Ayrıcalıklı erişimin güvenliğini sağlama](/windows-server/identity/securing-privileged-access/securing-privileged-access)konusundaki yönergeleri izleyin. 

Azure AD Connect sunucusu, [Active Directory Yönetim Katmanı modelinde](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) belgelendiği şekilde katman 0 bileşeni olarak değerlendirilmelidir 

Active Directory ortamınızı güvenli hale getirmek hakkında daha fazla bilgi için bkz. [Active Directory Için en iyi uygulamalar](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Yükleme önkoşulları

- Azure AD Connect, etki alanına katılmış bir Windows Server 2012 veya sonraki bir sürümü üzerinde yüklü olmalıdır. 
- Azure AD Connect, 2019 öncesi Small Business Server veya Windows Server Essentials üzerine yüklenemez (Windows Server Essentials 2019 desteklenir). Sunucu, Windows Server Standard veya daha iyi bir using olmalıdır. 
- Azure AD Connect sunucuda tam GUI yüklü olmalıdır. Windows Server Core üzerinde Azure AD Connect yükleme desteklenmez. 
- Active Directory Federasyon Hizmetleri (AD FS) (AD FS) yapılandırmasını yönetmek için Azure AD Connect Sihirbazı 'nı kullanıyorsanız, Azure AD Connect sunucuda PowerShell döküm grup ilkesi etkinleştirilmemiş olmalıdır. Eşitleme yapılandırmasını yönetmek için Azure AD Connect Sihirbazı 'nı kullanırsanız PowerShell dökümünü etkinleştirebilirsiniz. 
- AD FS dağıtılmışsa: 
    - AD FS veya Web uygulaması proxy 'Sinin yüklü olduğu sunucular Windows Server 2012 R2 veya üzeri olmalıdır. Uzaktan Yükleme için bu sunucularda Windows Uzaktan Yönetimi etkinleştirilmelidir. 
    - TLS/SSL sertifikalarını yapılandırmanız gerekir. Daha fazla bilgi için bkz. AD FS SSL [/TLS protokollerini ve şifre paketlerini yönetme](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) ve [ssl sertifikalarını yönetme](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap)AD FS.
    - Ad çözümlemesi yapılandırmanız gerekir. 
- Genel yöneticileriniz MFA etkinleştirilmişse, URL https://secure.aadcdn.microsoftonline-p.com  güvenilen siteler listesinde olmalıdır. MFA sınaması istendiğinde bu siteyi Güvenilen siteler listesine eklemeniz istenir ve daha önce eklenmemiştir. Internet Explorer 'ı, güvenilen sitelerinize eklemek için kullanabilirsiniz.
- Eşitleme için Azure AD Connect Health kullanmayı planlıyorsanız, Azure AD Connect Health önkoşullarının de karşılandığından emin olun. Daha fazla bilgi için bkz. [Aracı yüklemesi Azure AD Connect Health](how-to-connect-health-agent-install.md).

#### <a name="harden-your-azure-ad-connect-server"></a>Azure AD Connect sunucunuzu Harden 
BT ortamınızın bu kritik bileşeninin güvenlik saldırısı yüzeyini azaltmak için Azure AD Connect sunucunuzu çok fazla kullanmanızı öneririz. Bu önerilerin ardından, kuruluşunuzun bazı güvenlik risklerini azaltmaya yardımcı olur.

- Azure AD Connect etki alanı denetleyicisi ve diğer katman 0 kaynaklarıyla aynı şekilde değerlendirin. Daha fazla bilgi için bkz. [Yönetim Katmanı modeli Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Azure AD Connect sunucusuna yönetici erişimini yalnızca etki alanı yöneticileri veya diğer sıkı kontrollü güvenlik grupları ile sınırlayın.
- [Ayrıcalıklı erişime sahip tüm personel için adanmış bir hesap](/windows-server/identity/securing-privileged-access/securing-privileged-access)oluşturun. Yöneticiler web 'e göz atmamalıdır, e-postalarını kontrol etmemeli ve yüksek ayrıcalıklı hesaplar ile gündelik üretkenlik görevleri yapmaktan önce.
- [Ayrıcalıklı erişimin güvenliğini sağlama](/windows-server/identity/securing-privileged-access/securing-privileged-access)bölümünde sunulan yönergeleri izleyin. 
- AADConnect sunucusu ile NTLM kimlik doğrulamasının kullanımını reddetme. Bunu yapmak için bazı yollar şunlardır: [AADConnect sunucusunda NTLM 'Yi kısıtlama](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) ve [BIR etki alanında NTLM 'yi kısıtlama](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Her makinenin benzersiz bir yerel yönetici parolası olduğundan emin olun. Daha fazla bilgi için, bkz. [yerel yönetici parolası çözümü (LAPS)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) , her iş istasyonunda benzersiz rastgele parolalar yapılandırabilir ve bir ACL tarafından korunan Active Directory sunucuda depolar. Yalnızca uygun yetkili kullanıcılar bu yerel yönetici hesabı parolalarını sıfırlayabilir veya sıfırlamayı isteyebilir. [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=46899)' nden iş istasyonlarında ve sunucularda kullanım için laps 'leri edinebilirsiniz. LAPS ve ayrıcalıklı erişim iş istasyonları (Paw 'lar) içeren bir ortamı çalıştırmak için ek yönergeler, [Temizleme kaynak ilkesine dayalı işlem standartları](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle)içinde bulunabilir. 
- Kuruluşunuzun bilgi sistemlerine ayrıcalıklı erişimi olan tüm personel için adanmış [ayrıcalıklı erişim iş istasyonları](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) uygulayın. 
- Active Directory ortamınızın saldırı yüzeyini azaltmak için bu [ek yönergeleri](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) izleyin.


### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect tarafından kullanılan SQL Server
* Azure AD Connect’e kimlik verilerini depolamak için bir SQL Server veritabanı gerekiyor. Varsayılan olarak, bir SQL Server 2012 Express LocalDB (SQL Server Express bir açık sürümü) yüklenir. SQL Server Express, yaklaşık 100.000 nesneyi yönetmenizi sağlayan 10 GB boyutlu bir boyut sınırına sahiptir. Daha yüksek bir dizin nesneleri birimini yönetmeniz gerekiyorsa, Yükleme Sihirbazı 'nı farklı bir SQL Server yüklemesine işaret edin. SQL Server yüklemesinin türü [Azure AD Connect performansını](./plan-connect-performance-factors.md#sql-database-factors)etkileyebilir.
* Farklı bir SQL Server yüklemesi kullanıyorsanız, bu gereksinimler geçerlidir:
  * Azure AD Connect, 2012 (en son hizmet paketiyle) SQL Server tüm sürümlerini SQL Server 2019 ' e destekler. Azure SQL veritabanı, veritabanı olarak *desteklenmez* .
  * Büyük/küçük harfe duyarsız bir SQL harmanlaması kullanmanız gerekir. Bu harmanlamalar, adında bir \_ CI_ tanımlanır. Adında CS_ tarafından tanımlanan, büyük/küçük harfe duyarlı harmanlama kullanılması \_ *desteklenmez*.
  * SQL örneği başına yalnızca bir eşitleme motoruna sahip olabilirsiniz. Bir SQL örneğinin FIM/MıM Sync, DirSync veya Azure AD Eşitleme ile paylaşılması *desteklenmez*.

### <a name="accounts"></a>Hesaplar
* Tümleştirmek istediğiniz Azure AD kiracısı için bir Azure AD Genel Yönetici hesabınızın olması gerekir. Bu hesabın bir *okul veya kuruluş hesabı* olması ve bir *Microsoft hesabı* olamaz.
* [Hızlı ayarları](reference-connect-accounts-permissions.md#express-settings-installation) kullanıyorsanız veya DirSync 'ten yükseltme yaparsanız, şirket içi Active Directory Için bir Kurumsal Yönetici hesabınızın olması gerekir.
* Özel ayarlar yükleme yolunu kullanırsanız, daha fazla seçeneğiniz vardır. Daha fazla bilgi için bkz. [özel yükleme ayarları](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Bağlantı
* Azure AD Connect sunucusu hem intranet hem de internet için DNS çözümüne ihtiyaç duyuyor. DNS sunucusu, adları hem şirket içi Active Directory hem de Azure AD uç noktalarına çözümleyebilmelidir.
* Azure AD Connect, yapılandırılan tüm etki alanlarına ağ bağlantısı gerektirir
* İntranetinizde güvenlik duvarları varsa ve Azure AD Connect sunucuları ile etki alanı denetleyicileriniz arasında bağlantı noktaları açmanız gerekiyorsa, daha fazla bilgi için bkz. [Azure AD Connect bağlantı noktaları](reference-connect-ports.md) .
* Proxy 'niz veya güvenlik duvarınız hangi URL 'Lere erişilebileceğini sınırlarıyorsa, [Office 365 URL 'lerinde ve IP adresi aralıklarında](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) belgelenen URL 'lerin açılması gerekir. Ayrıca bkz. [güvenlik duvarınızdaki veya proxy sunucunuzdaki Azure Portal URL 'leri](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud).
  * Almanya veya Microsoft Azure Kamu Bulutu 'nda Microsoft bulutu kullanıyorsanız, URL 'Ler için [Azure AD Connect eşitleme hizmeti örneklerine göz](reference-connect-instances.md) atın.
* Azure AD Connect (sürüm 1.1.614.0 ve sonrası), eşitleme altyapısı ile Azure AD arasındaki iletişimi şifrelemek için varsayılan olarak TLS 1,2 kullanır. TLS 1,2, temel işletim sisteminde kullanılabilir değilse, Azure AD Connect daha eski protokollere (TLS 1,1 ve TLS 1,0) artımlı olarak geri döner.
* Sürüm 1.1.614.0 ' den önce, eşitleme altyapısı ve Azure AD arasındaki iletişimi şifrelemek için varsayılan olarak Azure AD Connect TLS 1,0 kullanır. TLS 1,2 ' ye geçmek için [Azure AD Connect IÇIN tls 1,2](#enable-tls-12-for-azure-ad-connect)' yi etkinleştirme ' deki adımları izleyin.
* İnternet 'e bağlanmak için bir giden Proxy kullanıyorsanız, Yükleme Sihirbazı için **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** dosyasında aşağıdaki ayar eklenmelidir ve Internet ve Azure AD 'ye bağlanabilmeleri için eşitlemenin Azure AD Connect gerekir. Bu metin, dosyanın alt kısmına girilmelidir. Bu kodda, *&lt; PROXYADDRESS &gt;* gerçek Proxy IP adresini veya ana bilgisayar adını temsil eder.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Proxy sunucunuz kimlik doğrulaması gerektiriyorsa, [hizmet hesabı](reference-connect-accounts-permissions.md#adsync-service-account) etki alanında bulunmalıdır. [Özel bir hizmet hesabı](how-to-connect-install-custom.md#install-required-components)belirtmek için özelleştirilmiş ayarlar yükleme yolunu kullanın. machine.config için de farklı bir değişiklik yapmanız gerekir. machine.config sürümünde bu değişiklik ile, Yükleme Sihirbazı ve eşitleme altyapısı, proxy sunucusundan gelen kimlik doğrulama isteklerine yanıt verir. **Yapılandırma** sayfası hariç olmak üzere tüm Yükleme Sihirbazı sayfalarında, oturum açmış kullanıcının kimlik bilgileri kullanılır. Yükleme sihirbazının sonundaki **Yapılandır** sayfasında, bağlam oluşturduğunuz [hizmet hesabına](reference-connect-accounts-permissions.md#adsync-service-account) geçiş yapılır. machine.config bölümü şöyle görünmelidir:

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Proxy yapılandırması mevcut bir kurulumda yapılamakta ise, Azure AD Connect ara sunucu yapılandırmasını okuyabilmesi ve davranışı güncelleştirmesi için **Microsoft Azure AD Eşitleme hizmetinin** yeniden başlatılması gerekir. 
* Azure AD Connect, Dizin eşitlemesinin bir parçası olarak Azure AD 'ye bir Web isteği gönderdiğinde, Azure AD 'nin yanıtlaması 5 dakika sürebilir. Proxy sunucularda bağlantı boşta kalma zaman aşımı yapılandırması olması yaygındır. Yapılandırmanın en az 6 dakika veya daha fazla ayarlandığından emin olun.

Daha fazla bilgi için bkz. [varsayılan proxy öğesi](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)hakkında MSDN.
Bağlantı sorunlarıyla karşılaşırsanız daha fazla bilgi için bkz. [bağlantı sorunlarını giderme](tshoot-connect-connectivity.md).

### <a name="other"></a>Diğer
İsteğe bağlı: eşitlemeyi doğrulamak için bir test Kullanıcı hesabı kullanın.

## <a name="component-prerequisites"></a>Bileşen önkoşulları
### <a name="powershell-and-net-framework"></a>PowerShell ve .NET Framework
Azure AD Connect, Microsoft PowerShell ve .NET Framework 4.5.1 bağımlıdır. Sunucunuzda bu sürüm veya sonraki bir sürümün yüklü olması gerekir. Windows Server sürümünüze bağlı olarak, aşağıdaki işlemleri gerçekleştirin:

* Windows Server 2012 R2
  * Microsoft PowerShell varsayılan olarak yüklüdür. İşlem yapmanız gerekmez.
  * .NET Framework 4.5.1 ve üzeri sürümler Windows Update aracılığıyla sunulur. Denetim Masası 'nda Windows Server 'a en son güncelleştirmeleri yüklediğinizden emin olun.
* Windows Server 2012
  * Microsoft PowerShell 'in en son sürümü, [Microsoft Indirme merkezi](https://www.microsoft.com/downloads)'Nde bulunan Windows Management Framework 4,0 ' de bulunmaktadır.
  * .NET Framework 4.5.1 ve üzeri sürümler, [Microsoft Indirme merkezi](https://www.microsoft.com/downloads)' nde bulunabilir.


### <a name="enable-tls-12-for-azure-ad-connect"></a>Azure AD Connect için TLS 1,2 'yi etkinleştirin
Sürüm 1.1.614.0 ' den önce, eşitleme altyapısı sunucusu ile Azure AD arasındaki iletişimi şifrelemek için varsayılan olarak Azure AD Connect TLS 1,0 kullanır. .NET uygulamalarını, sunucuda varsayılan olarak TLS 1,2 kullanacak şekilde yapılandırabilirsiniz. TLS 1,2 hakkında daha fazla bilgi için bkz. [Microsoft Güvenlik Danışmanlığı 2960358](/security-updates/SecurityAdvisories/2015/2960358).

1. İşletim sisteminiz için .NET 4.5.1 düzeltmesinin yüklü olduğundan emin olun. Daha fazla bilgi için bkz. [Microsoft Güvenlik Danışmanlığı 2960358](/security-updates/SecurityAdvisories/2015/2960358). Bu düzeltme veya daha sonraki bir sürümü sunucunuzda zaten yüklü olabilir.

1. Tüm işletim sistemleri için, bu kayıt defteri anahtarını ayarlayın ve sunucuyu yeniden başlatın.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Eşitleme altyapısı sunucusu ve uzak SQL Server arasında TLS 1,2 ' i etkinleştirmek istiyorsanız, [Microsoft SQL Server Için tls 1,2 desteği](https://support.microsoft.com/kb/3135244)için gerekli sürümlerin yüklü olduğundan emin olun.

### <a name="dcom-prerequisites-on-the-synchronization-server"></a>Eşitleme sunucusunda DCOM önkoşulları
Eşitleme hizmetinin yüklenmesi sırasında, Azure AD Connect aşağıdaki kayıt defteri anahtarının varlığını denetler:

- HKEY_LOCAL_MACHINE: Software\Microsoft\Ole

Bu kayıt defteri anahtarı altında Azure AD Connect aşağıdaki değerlerin mevcut ve bozuk olup olmadığını kontrol eder: 

- [MachineAccessRestriction](/windows/win32/com/machineaccessrestriction)
- [MachineLaunchRestriction](/windows/win32/com/machinelaunchrestriction)
- [DefaultLaunchPermission](/windows/win32/com/defaultlaunchpermission)

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Federasyon yükleme ve yapılandırma önkoşulları
### <a name="windows-remote-management"></a>Windows Uzaktan Yönetimi
AD FS veya Web uygulaması ara sunucusu (WAP) dağıtmak için Azure AD Connect kullandığınızda, aşağıdaki gereksinimleri kontrol edin:

* Hedef sunucu etki alanına katılmış ise, Windows Uzaktan Yönetimi 'nin etkinleştirildiğinden emin olun.
  * Yükseltilmiş bir PowerShell komut penceresinde komutunu kullanın `Enable-PSRemoting –force` .
* Hedef sunucu, etki alanına katılmamış bir WAP makinesi ise, birkaç ek gereksinim vardır:
  * Hedef makinede (WAP makinesi):
    * Windows Uzaktan Yönetimi/WS-Management (WinRM) hizmetinin Hizmetler ek bileşeni aracılığıyla çalıştığından emin olun.
    * Yükseltilmiş bir PowerShell komut penceresinde komutunu kullanın `Enable-PSRemoting –force` .
  * Sihirbazın çalıştığı makinede (hedef makine etki alanına katılmamış veya güvenilmeyen bir etki alanı ise):
    * Yükseltilmiş bir PowerShell komut penceresinde komutunu kullanın `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` .
    * Sunucu Yöneticisi 'nde:
      * Makine havuzuna bir DMZ WAP Konağı ekleyin. Sunucu Yöneticisi 'nde **Yönet**  >  **Sunucu Ekle**' yi seçin ve sonra **DNS** sekmesini kullanın.
      * **Tüm sunucuları Sunucu Yöneticisi** SEKMESINDE, WAP sunucusuna sağ tıklayın ve **Farklı Yönet**' i seçin. WAP makinesi için yerel (etki alanı değil) kimlik bilgilerini girin.
      * Uzak PowerShell bağlantısını doğrulamak için, **tüm sunucular sunucu yöneticisi** SEKMESINDE, WAP sunucusuna sağ tıklayıp **Windows PowerShell**' i seçin. Uzak PowerShell oturumlarının kurulabilmek için uzak bir PowerShell oturumunun açılması gerekir.

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL sertifikası gereksinimleri
* AD FS grubunuzun ve tüm Web uygulaması ara sunucularının tüm düğümlerinde aynı TLS/SSL sertifikasını kullanmanızı öneririz.
* Sertifika bir x509 sertifikası olmalıdır.
* Bir test laboratuvarı ortamında, Federasyon sunucuları üzerinde otomatik olarak imzalanan bir sertifika kullanabilirsiniz. Bir üretim ortamında, sertifikayı bir genel sertifika yetkilisinden edinmenizi öneririz.
  * Genel olarak güvenilen bir sertifika kullanıyorsanız, her Web uygulaması ara sunucusu üzerinde yüklü sertifikanın hem yerel sunucuda hem de tüm Federasyon sunucularında güvenilir olduğundan emin olun.
* Sertifikanın kimliği, Federasyon Hizmeti adı ile eşleşmelidir (örneğin, sts.contoso.com).
  * Kimlik, dNSName türünde bir konu alternatif adı (SAN) uzantısı ya da SAN girişi yoksa, konu adı ortak bir ad olarak belirtilir.
  * Sertifikada, Federasyon Hizmeti adıyla eşleşen birden çok SAN girişi bulunabilir.
  * Workplace Join kullanmayı planlıyorsanız, enterpriseregistration değeri ile ek bir SAN gerekir **.** ardından, kuruluşunuzun Kullanıcı asıl adı (UPN) sonekini (örneğin, enterpriseregistration.contoso.com) takip edin.
* CryptoAPI yeni nesil (CNG) anahtarları ve anahtar depolama sağlayıcıları (KSPs) tabanlı sertifikalar desteklenmez. Sonuç olarak, bir şifreleme hizmeti sağlayıcısı (CSP) temel alınarak bir sertifika kullanmanız gerekir.
* Joker kart sertifikaları desteklenir.

### <a name="name-resolution-for-federation-servers"></a>Federasyon sunucuları için ad çözümlemesi
* İntranet (iç DNS sunucunuz) ve extranet (etki alanı kaydediciniz aracılığıyla genel DNS) için AD FS adı (örneğin, sts.contoso.com) için DNS kayıtları ayarlayın. İntranet DNS kaydı için CNAME kayıtları olmayan bir kayıt kullandığınızdan emin olun. Windows kimlik doğrulamasının etki alanına katılmış makinenizden düzgün çalışması için bir kayıt kullanılması gerekir.
* Birden fazla AD FS sunucusu veya Web uygulaması ara sunucusu dağıtıyorsanız, yük dengeleyiciyi yapılandırdığınızdan ve AD FS adı (örneğin, sts.contoso.com) için DNS kayıtlarının yük dengeleyiciye işaret ettikten emin olun.
* Windows tümleşik kimlik doğrulamasının intranetinizdeki Internet Explorer 'ı kullanarak tarayıcı uygulamaları için çalışması için, AD FS adının (örneğin, sts.contoso.com) Internet Explorer 'daki intranet bölgesine eklendiğinden emin olun. Bu gereksinim, grup ilkesi aracılığıyla denetlenebilir ve etki alanına katılmış tüm bilgisayarlarınıza dağıtılabilir.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect destekleyici bileşenleri
Azure AD Connect, Azure AD Connect yüklendiği sunucuya aşağıdaki bileşenleri yüklüyor. Bu liste temel bir hızlı yüklemeye yöneliktir. **Eşitleme Hizmetleri yükleme** sayfasında farklı bir SQL Server kullanmayı SEÇERSENIZ, SQL Express LocalDB yerel olarak yüklenmez.

* Azure AD Connect Health
* Microsoft SQL Server 2012 komut satırı yardımcı programları
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 yeniden dağıtım paketi

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect için donanım gereksinimleri
Aşağıdaki tabloda Azure AD Connect eşitleme bilgisayarı için en düşük gereksinimler gösterilmektedir.

| Active Directory nesne sayısı | CPU | Bellek | Sabit sürücü boyutu |
| --- | --- | --- | --- |
| 10.000 'den az |1,6 GHz |4 GB |70 GB |
| 10000 – 50000 |1,6 GHz |4 GB |70 GB |
| 50000 – 100000 |1,6 GHz |16 GB |100 GB |
| 100.000 veya daha fazla nesne için SQL Server tam sürümü gereklidir | | | |
| 100000 – 300000 |1,6 GHz |32 GB |300 GB |
| 300000 – 600000 |1,6 GHz |32 GB |450 GB |
| 600.000 'den fazla |1,6 GHz |32 GB |500 GB |

AD FS veya Web uygulaması proxy sunucuları çalıştıran bilgisayarlar için en düşük gereksinimler şunlardır:

* CPU: çift çekirdekli 1,6 GHz veya üzeri
* Bellek: 2 GB veya üzeri
* Azure VM: a2 yapılandırması veya üzeri

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
