---
title: 'Azure AD Connect: Önkoşullar ve donanım | Microsoft Docs'
description: Bu konu, Azure AD Connect için önkoşulları ve donanım gereksinimlerini açıklamaktadır.
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
ms.topic: conceptual
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a1906b8ac281620381176a2a11dff3841069f69
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062668"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect önkoşulları
Bu konu, Azure AD Connect için önkoşulları ve donanım gereksinimlerini açıklamaktadır.

## <a name="before-you-install-azure-ad-connect"></a>Azure AD Connect yüklemeden önce
Azure AD Connect yüklemeden önce, ihtiyacınız olan birkaç şey vardır.

### <a name="azure-ad"></a>Azure AD
* Azure AD kiracısı. [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)ile bir tane alırsınız. Azure AD Connect yönetmek için aşağıdaki portallardan birini kullanabilirsiniz:
  * [Azure Portal](https://portal.azure.com).
  * [Office portalı](https://portal.office.com).  
* Azure AD 'de kullanmayı planladığınız [etki alanını ekleyin ve doğrulayın](../active-directory-domains-add-azure-portal.md) . Örneğin, kullanıcılarınız için contoso.com kullanmayı planlıyorsanız, bu etki alanının doğrulandığından ve yalnızca contoso.onmicrosoft.com varsayılan etki alanını kullandığınızdan emin olun.
* Bir Azure AD kiracısı varsayılan 50.000 nesnelerine izin verir. Etki alanınızı doğruladıktan sonra sınır, 300k nesnelerine yükseltilir. Azure AD 'de daha fazla nesne gerekiyorsa, sınırın daha da artması için bir destek talebi açmanız gerekir. 500 ' den fazla nesne gerekiyorsa, Office 365, Azure AD Temel, Azure AD Premium veya Enterprise Mobility ve Security gibi bir lisansa sahip olmanız gerekir.

### <a name="prepare-your-on-premises-data"></a>Şirket içi verilerinizi hazırlama
* Azure AD ve Office 365 ile eşitlemeden önce dizininizde bulunan yinelemeler ve biçimlendirme sorunları gibi hataları belirlemek için [ıddüzeltmesini](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) kullanın.
* [Azure AD 'de etkinleştirebileceğiniz isteğe bağlı eşitleme özelliklerini](how-to-connect-syncservice-features.md) gözden geçirin ve hangi özellikleri etkinleştirmeniz gerektiğini değerlendirin.

### <a name="on-premises-active-directory"></a>Şirket içi Active Directory
* AD şeması sürümü ve orman işlev düzeyi Windows Server 2003 veya sonraki bir sürümü olmalıdır. Şema ve Orman düzeyi gereksinimleri karşılandığı sürece etki alanı denetleyicileri herhangi bir sürümü çalıştırabilir.
* Özellik **parola geri yazma**özelliğini kullanmayı planlıyorsanız, etki alanı denetleyicilerinin Windows Server 2008 R2 veya sonraki bir sürümde olması gerekir.
* Azure AD tarafından kullanılan etki alanı denetleyicisi yazılabilir olmalıdır. RODC (salt okuma etki alanı denetleyicisi) kullanılması **desteklenmez** ve Azure AD Connect herhangi bir yazma yeniden yönlendirmelerini izlemez.
* Şirket içi ormanlarda/etki alanlarının "noktalı" kullanılarak kullanılması **desteklenmez** (ad bir nokta içerir ".") NetBIOS adları.
* [Active Directory geri dönüşüm kutusunun etkinleştirilmesi](how-to-connect-sync-recycle-bin.md)önerilir.

### <a name="azure-ad-connect-server"></a>Azure AD Connect sunucusu
>[!IMPORTANT]
>Azure AD Connect sunucusu kritik kimlik verileri içerir ve [Active Directory Yönetim Katmanı modelinde](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) belgelendiği şekilde katman 0 bileşeni olarak değerlendirilmelidir

* Azure AD Connect, 2019 öncesi Small Business Server veya Windows Server Essentials üzerine yüklenemez (Windows Server Essentials 2019 desteklenir). Sunucu, Windows Server Standard veya daha iyi bir using olmalıdır.
* Azure AD Connect doğru yüklenmesini engelleyebilecek güvenlik uygulamaları ve daha kısıtlayıcı ayarlar nedeniyle Azure AD Connect bir etki alanı denetleyicisine yüklenmesi önerilmez.
* Azure AD Connect sunucuda tam GUI yüklü olmalıdır. Sunucu Çekirdeği üzerine yüklenmesi **desteklenmez** .
>[!IMPORTANT]
>Small Business Server, Server Essentials veya Server Core üzerinde Azure AD Connect yükleme desteklenmez.

* Azure AD Connect Windows Server 2008 R2 veya sonraki bir sürümde yüklü olmalıdır. Bu sunucu etki alanına katılmış olmalıdır ve bir etki alanı denetleyicisi veya üye sunucu olabilir.
* Azure AD Connect Windows Server 2008 R2 üzerine yüklerseniz, Windows Update en son düzeltmeleri uyguladığınızdan emin olun. Yükleme, düzeltme eki yüklenmemiş bir sunucuyla başlayamaz.
* Özellik **parola eşitlemesini**kullanmayı planlıyorsanız, Azure AD Connect sunucusu Windows Server 2008 R2 SP1 veya sonraki bir sürümde olmalıdır.
* **Grup tarafından yönetilen bir hizmet hesabı**kullanmayı planlıyorsanız, Azure AD Connect sunucusu Windows Server 2012 veya sonraki bir sürümde olmalıdır.
* Azure AD Connect sunucuda [.NET Framework 4.5.1](#component-prerequisites) veya üzeri ve [Microsoft PowerShell 3,0](#component-prerequisites) veya üzeri yüklü olmalıdır.
* Azure AD Connect sunucuda PowerShell döküm grup ilkesi etkin olmamalıdır.
* Active Directory Federasyon Hizmetleri (AD FS) dağıtılırsa, AD FS veya Web uygulaması proxy 'Sinin yüklü olduğu sunucular Windows Server 2012 R2 veya üzeri olmalıdır. Uzaktan Yükleme için bu sunucularda [Windows Uzaktan Yönetimi](#windows-remote-management) etkinleştirilmelidir.
* Active Directory Federasyon Hizmetleri (AD FS) dağıtılmışsa, [SSL sertifikalarına](#ssl-certificate-requirements)ihtiyacınız vardır.
* Active Directory Federasyon Hizmetleri (AD FS) dağıtılmışsa, [ad çözümlemesi](#name-resolution-for-federation-servers)yapılandırmanız gerekir.
* Genel yöneticileriniz MFA etkinleştirilmişse, URL **https://secure.aadcdn.microsoftonline-p.com** güvenilen siteler listesinde olmalıdır. MFA sınaması istendiğinde bu siteyi Güvenilen siteler listesine eklemeniz istenir ve daha önce eklenmediyse. Internet Explorer 'ı, güvenilen sitelerinize eklemek için kullanabilirsiniz.
* Microsoft, BT ortamınızın bu kritik bileşeninin güvenlik saldırısı yüzeyini azaltmak için Azure AD Connect sunucunuzu sağlamlaştırmanızı önerir.  Aşağıdaki önerilerin ardından kuruluşunuza yönelik güvenlik riskleri azalır.

* Etki alanına katılmış bir sunucuda Azure AD Connect dağıtın ve etki alanı yöneticilerine veya daha sıkı denetlenen güvenlik gruplarına yönetici erişimini kısıtlayın.

Daha fazla bilgi için bkz: 

* [Yöneticiler gruplarının güvenliğini sağlama](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Yerleşik yönetici hesaplarının güvenliğini sağlama](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Saldırı yüzeylerini azaltarak güvenlik iyileştirmesi ve sürdürülebilirlik](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Active Directory saldırı yüzeyini azaltma](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect tarafından kullanılan SQL Server
* Azure AD Connect’e kimlik verilerini depolamak için bir SQL Server veritabanı gerekiyor. Varsayılan olarak bir SQL Server 2012 Express LocalDB (SQL Server Express bir açık sürümü) yüklenir. SQL Server Express, yaklaşık 100.000 nesneyi yönetmenizi sağlayan 10 GB boyut sınırına sahiptir. Dizin nesnelerinin daha yüksek bir birimini yönetmeniz gerekiyorsa, Yükleme Sihirbazı 'nı farklı bir SQL Server yüklemesine işaret etmeniz gerekir. SQL Server yüklemesinin türü [Azure AD Connect performansını](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)etkileyebilir.
* Farklı bir SQL Server yüklemesi kullanıyorsanız, bu gereksinimler geçerlidir:
  * Azure AD Connect, 2008 R2 'den (en son hizmet paketiyle) Microsoft SQL Server tüm sürümlerini SQL Server 2019 ' e destekler. Microsoft Azure SQL Veritabanı veritabanı olarak **desteklenmez** .
  * Büyük/küçük harfe duyarsız bir SQL harmanlaması kullanmanız gerekir. Bu harmanlamalar, adında bir \_CI_ ile tanımlanır. \_CS_ tarafından tanımlanan, büyük/küçük harfe duyarlı harmanlama kullanılması **desteklenmez** .
  * SQL örneği başına yalnızca bir eşitleme motoruna sahip olabilirsiniz. Bir SQL örneğinin FIM/MıM Sync, DirSync veya Azure AD Eşitleme paylaşılması **desteklenmez** .

### <a name="accounts"></a>Hesaplar
* Tümleştirmek istediğiniz Azure AD kiracısı için bir Azure AD Genel yönetici hesabı. Bu hesabın bir **okul veya kuruluş hesabı** olması ve bir **Microsoft hesabı**olamaz.
* Hızlı ayarları kullanıyorsanız veya DirSync 'ten yükseltirseniz, şirket içi Active Directory için bir kuruluş yöneticisi hesabınız olmalıdır.
* Şirket içi Active Directory için özel ayarlar yükleme yolunu veya bir kuruluş yöneticisi hesabını kullanırsanız [Active Directory hesaplar](reference-connect-accounts-permissions.md) .

### <a name="connectivity"></a>Bağlantı
* Azure AD Connect sunucusu hem intranet hem de internet için DNS çözümüne ihtiyaç duyuyor. DNS sunucusu, adları hem şirket içi Active Directory hem de Azure AD uç noktalarına çözümleyebilmelidir.
* Intranetinizde güvenlik duvarları varsa ve Azure AD Connect sunucuları ile etki alanı denetleyicileriniz arasında bağlantı noktaları açmanız gerekiyorsa, daha fazla bilgi için [Azure AD Connect bağlantı noktaları](reference-connect-ports.md) bölümüne bakın.
* Proxy 'niz veya güvenlik duvarınız hangi URL 'Lere erişilebileceğini sınırlarıyorsa, [Office 365 URL 'lerinde ve IP adresi aralıklarında](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) belgelenen URL 'lerin açılması gerekir.
  * Almanya veya Microsoft Azure Kamu Bulutu 'nda Microsoft Bulut kullanıyorsanız, URL 'Ler için [Azure AD Connect eşitleme hizmeti örneklerine göz](reference-connect-instances.md) atın bölümüne bakın.
* Azure AD Connect (sürüm 1.1.614.0 ve sonrası), eşitleme altyapısı ile Azure AD arasındaki iletişimi şifrelemek için varsayılan olarak TLS 1,2 kullanır. TLS 1,2, temel işletim sisteminde kullanılabilir değilse, Azure AD Connect daha eski protokollere (TLS 1,1 ve TLS 1,0) artımlı olarak geri döner.
* Sürüm 1.1.614.0 ' den önce, eşitleme altyapısı ve Azure AD arasındaki iletişimi şifrelemek için varsayılan olarak Azure AD Connect TLS 1,0 kullanır. TLS 1,2 ' ye geçmek için [Azure AD Connect IÇIN tls 1,2](#enable-tls-12-for-azure-ad-connect)' yi etkinleştirme ' deki adımları izleyin.
* Internet 'e bağlanmak için bir giden Proxy kullanıyorsanız, Yükleme Sihirbazı için **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** dosyasındaki aşağıdaki ayar eklenmelidir ve eşitleme Azure AD Connect Internet ve Azure AD 'ye bağlanmak için. Bu metin, dosyanın alt kısmına girilmelidir. Bu kodda, &lt;PROXYADDRESS &gt; gerçek Proxy IP adresini veya ana bilgisayar adını temsil eder.

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

* Proxy sunucunuz kimlik doğrulaması gerektiriyorsa, [hizmet hesabı](reference-connect-accounts-permissions.md#adsync-service-account) etki alanında yer almalıdır ve [özel bir hizmet hesabı](how-to-connect-install-custom.md#install-required-components)belirtmek için özelleştirilmiş ayarlar yükleme yolunu kullanmanız gerekir. Ayrıca Machine. config dosyasında farklı bir değişikliğe de ihtiyacınız vardır. Machine. config dosyasında bu değişiklik ile Yükleme Sihirbazı ve eşitleme altyapısı, proxy sunucusundan gelen kimlik doğrulama isteklerine yanıt verir. Tüm Yükleme Sihirbazı sayfalarında, **Yapılandır** sayfası hariç, oturum açan kullanıcının kimlik bilgileri kullanılır. Yükleme sihirbazının sonundaki **Yapılandır** sayfasında, bağlam sizin oluşturduğunuz [hizmet hesabına](reference-connect-accounts-permissions.md#adsync-service-account) geçiş yapılır. Machine. config bölümü şöyle görünmelidir.

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

* Azure AD Connect, Dizin eşitlemesinin bir parçası olarak Azure AD 'ye bir Web isteği gönderdiğinde, Azure AD 'nin yanıtlaması 5 dakika sürebilir. Proxy sunucularda bağlantı boşta kalma zaman aşımı yapılandırması olması yaygındır. Lütfen yapılandırmanın en az 6 dakika veya daha fazla ayarlandığından emin olun.

Daha fazla bilgi için bkz. [varsayılan proxy öğesi](https://msdn.microsoft.com/library/kd3cf2ex.aspx)hakkında MSDN.  
Bağlantı sorunlarıyla karşılaşırsanız daha fazla bilgi için bkz. [bağlantı sorunlarını giderme](tshoot-connect-connectivity.md).

### <a name="other"></a>Diğer
* İsteğe bağlı: eşitlemeyi doğrulamak için bir test Kullanıcı hesabı.

## <a name="component-prerequisites"></a>Bileşen önkoşulları
### <a name="powershell-and-net-framework"></a>PowerShell ve .NET Framework
Azure AD Connect, Microsoft PowerShell ve .NET Framework 4.5.1 bağımlıdır. Sunucunuzda bu sürüm veya sonraki bir sürümün yüklü olması gerekir. Windows Server sürümünüze bağlı olarak şunları yapın:

* Windows Server 2012R2
  * Microsoft PowerShell varsayılan olarak yüklüdür. İşlem yapmanız gerekmez.
  * .NET Framework 4.5.1 ve üzeri sürümler Windows Update aracılığıyla sunulur. Denetim Masası 'nda Windows Server için en son güncelleştirmeleri yüklediğinizden emin olun.
* Windows Server 2008 R2 ve Windows Server 2012
  * Microsoft PowerShell 'in en son sürümü, [Microsoft Indirme merkezi](https://www.microsoft.com/downloads)'Nde bulunan **Windows Management Framework 4,0**' de kullanılabilir.
  * .NET Framework 4.5.1 ve üzeri sürümler [Microsoft Indirme merkezi](https://www.microsoft.com/downloads)' nde bulunabilir.


### <a name="enable-tls-12-for-azure-ad-connect"></a>Azure AD Connect için TLS 1,2 'yi etkinleştirin
Sürüm 1.1.614.0 ' den önce, eşitleme altyapısı sunucusu ile Azure AD arasındaki iletişimi şifrelemek için varsayılan olarak Azure AD Connect TLS 1,0 kullanır. Bunu, .NET uygulamalarını sunucuda varsayılan olarak TLS 1,2 kullanacak şekilde yapılandırarak değiştirebilirsiniz. TLS 1,2 hakkında daha fazla bilgi için [Microsoft Güvenlik Danışmanlığı 2960358](https://technet.microsoft.com/security/advisory/2960358)' de bulabilirsiniz.

1. TLS 1,2, Windows Server 2008 R2 veya önceki sürümlerden önce etkinleştirilemez. İşletim sisteminiz için .NET 4.5.1 düzeltmesinin yüklü olduğundan emin olun, bkz. [Microsoft Güvenlik Danışmanlığı 2960358](https://technet.microsoft.com/security/advisory/2960358). Bu düzeltme veya daha sonraki bir sürümü sunucunuzda zaten yüklü olabilir.
2. Windows Server 2008 R2 kullanıyorsanız, TLS 1,2 ' nin etkinleştirildiğinden emin olun. Windows Server 2012 Server ve sonraki sürümlerinde TLS 1,2 zaten etkinleştirilmelidir.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. Tüm işletim sistemleri için, bu kayıt defteri anahtarını ayarlayın ve sunucuyu yeniden başlatın.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Ayrıca, eşitleme altyapısı sunucusu ile uzak SQL Server arasında TLS 1,2 ' i etkinleştirmek istiyorsanız, [Microsoft SQL Server Için tls 1,2 desteği](https://support.microsoft.com/kb/3135244)için gerekli sürümlerin yüklü olduğundan emin olun.

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Federasyon yükleme ve yapılandırma önkoşulları
### <a name="windows-remote-management"></a>Windows Uzaktan Yönetimi
Active Directory Federasyon Hizmetleri (AD FS) veya Web uygulaması ara sunucusu dağıtmak için Azure AD Connect kullanırken, şu gereksinimleri kontrol edin:

* Hedef sunucu etki alanına katılmış ise, Windows Uzaktan Yönetimi 'nin etkinleştirildiğinden emin olun
  * Yükseltilmiş bir PSH komut penceresinde, komut `Enable-PSRemoting –force` kullanın
* Hedef sunucu, etki alanına katılmış bir WAP makinesi ise, birkaç ek gereksinim daha vardır
  * Hedef makinede (WAP makinesi):
    * WinRM (Windows Uzaktan Yönetimi/WS-Management) hizmetinin Hizmetler ek bileşeni aracılığıyla çalıştığından emin olun
    * Yükseltilmiş bir PSH komut penceresinde, komut `Enable-PSRemoting –force` kullanın
  * Sihirbazın üzerinde çalıştığı makinede (hedef makine etki alanına katılmış veya güvenilmeyen etki alanı ise):
    * Yükseltilmiş bir PSH komut penceresinde, komutunu kullanın `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * Sunucu Yöneticisi:
      * DMZ WAP konağını makine havuzuna Ekle (Sunucu Yöneticisi-> Yönet-> Sunucu Ekle... DNS sekmesini kullan)
      * Tüm sunucular sekmesine Sunucu Yöneticisi: WAP sunucusu ' na sağ tıklayın ve farklı Yönet... seçeneğini belirleyin, WAP makinesi için yerel (etki alanı değil) kimlik bilgilerini girin
      * Uzak PSH bağlantısını doğrulamak için, Sunucu Yöneticisi tüm sunucular sekmesinde: WAP sunucusu ' na sağ tıklayıp Windows PowerShell ' i seçin. Uzak PowerShell oturumlarının kurulabilmek için uzak bir PSH oturumunun açılması gerekir.

### <a name="ssl-certificate-requirements"></a>SSL sertifikası gereksinimleri
* AD FS grubunuzun ve tüm Web uygulaması ara sunucularının tüm düğümlerinde aynı SSL sertifikasını kullanmanız önemle önerilir.
* Sertifika bir x509 sertifikası olmalıdır.
* Bir test laboratuvarı ortamında, Federasyon sunucuları üzerinde otomatik olarak imzalanan bir sertifika kullanabilirsiniz. Ancak, bir üretim ortamı için sertifikayı genel bir CA 'dan edinmeniz önerilir.
  * Genel olarak güvenilen bir sertifika kullanıyorsanız, her Web uygulaması ara sunucusu üzerinde yüklü sertifikanın hem yerel sunucuda hem de tüm Federasyon sunucularında güvenilir olduğundan emin olun
* Sertifikanın kimliği, Federasyon Hizmeti adı ile eşleşmelidir (örneğin, sts.contoso.com).
  * Kimlik, dNSName türünde bir konu alternatif adı (SAN) uzantısı ya da SAN girişi yoksa ortak ad olarak belirtilen konu adı.  
  * Sertifikada birden çok SAN girişi bulunabilir, bu, bunlardan biri Federasyon Hizmeti adıyla eşleşir.
  * Workplace Join kullanmayı planlıyorsanız, enterpriseregistration değeri ile ek bir SAN gerekir **.** ardından, kuruluşunuzun Kullanıcı asıl adı (UPN) sonekini (örneğin, **enterpriseregistration.contoso.com**) takip edin.
* CryptoAPI yeni nesil (CNG) anahtarları ve anahtar depolama sağlayıcılarına dayalı sertifikalar desteklenmez. Bu, KSP (anahtar depolama sağlayıcısı) değil, CSP 'yi (şifreleme hizmeti sağlayıcısı) temel alan bir sertifika kullanmanız gerektiği anlamına gelir.
* Joker kart sertifikaları desteklenir.

### <a name="name-resolution-for-federation-servers"></a>Federasyon sunucuları için ad çözümlemesi
* İntranet (iç DNS sunucunuz) ve extranet (etki alanı kaydediciniz aracılığıyla genel DNS) için AD FS Federasyon Hizmeti adı (örneğin, sts.contoso.com) için DNS kayıtları ayarlayın. İntranet DNS kaydı için CNAME kayıtları olmayan bir kayıt kullandığınızdan emin olun. Bu, Windows kimlik doğrulamasının etki alanına katılmış makineden doğru şekilde çalışması için gereklidir.
* Birden fazla AD FS sunucusu veya Web uygulaması ara sunucusu dağıtıyorsanız, yük dengeleyiciyi yapılandırdığınızdan ve AD FS Federasyon Hizmeti adı (örneğin sts.contoso.com) için DNS kayıtlarının yük dengeleyiciye işaret aldığından emin olun.
* Windows tümleşik kimlik doğrulamasının intranetinizdeki Internet Explorer 'ı kullanarak tarayıcı uygulamaları için çalışması için, AD FS Federasyon Hizmeti adının (örneğin sts.contoso.com) IE 'deki intranet bölgesine eklendiğinden emin olun. Bu, Grup İlkesi aracılığıyla denetlenebilir ve etki alanına katılmış tüm bilgisayarlarınıza dağıtılabilir.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect destekleyici bileşenleri
Aşağıda, Azure AD Connect yüklendiği sunucuya Azure AD Connect yüklediği bileşenlerin bir listesi verilmiştir. Bu liste temel bir hızlı yüklemeye yöneliktir. Eşitleme Hizmetleri yükleme sayfasında farklı bir SQL Server kullanmayı seçerseniz, SQL Express LocalDB yerel olarak yüklenmez.

* Azure AD Connect Health
* Microsoft SQL Server 2012 komut satırı yardımcı programları
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 yeniden dağıtım paketi

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect için donanım gereksinimleri
Aşağıdaki tabloda Azure AD Connect eşitleme bilgisayarı için en düşük gereksinimler gösterilmektedir.

| Active Directory nesne sayısı | CPU | Hafıza | Sabit sürücü boyutu |
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
* BELLEK: 2 GB veya üzeri
* Azure VM: a2 yapılandırması veya üzeri

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
