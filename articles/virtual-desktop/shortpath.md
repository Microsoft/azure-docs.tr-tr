---
title: Windows sanal masaüstü RDP ShortPath (Önizleme)
titleSuffix: Azure
description: Windows sanal masaüstü için RDP Shortfılepath (Önizleme) ayarlama.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 295a46f6d1074ddf8422233ea3ccfa4d65c28fd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571585"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows sanal masaüstü RDP ShortPath (Önizleme)

> [!IMPORTANT]
> RDP ShortPath Şu anda genel önizlemededir.
> Bu önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve bunu üretim iş yükleri için kullanmanızı önermiyoruz. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

RDP ShortPath, uzak masaüstü Istemcisi ile oturum ana bilgisayarı arasında doğrudan UDP tabanlı bir aktarım kuran Windows sanal masaüstü özelliğidir. RDP, bu taşımayı, daha iyi güvenilirlik ve tutarlı gecikme sunan uzak masaüstü ve RemoteApp sunmak için kullanır.

## <a name="key-benefits"></a>Önemli avantajlar

* RDP ShortPath taşıması, son derece etkin  [evrensel hız Denetim Protokolü 'nün (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/)üzerine dayalıdır. URCP, ağ koşullarını etkin şekilde izlemeye yönelik UDP 'yi geliştirir ve dengeli ve tam bağlantı kullanımı sağlar. URCP, Uzak Masaüstü için gereken düşük gecikme ve kayıp düzeylerinde çalışır. URCP, ağ parametrelerini dinamik olarak öğrenerek ve bir hız denetim mekanizmasıyla protokol sağlayarak en iyi performansı elde eder.
* RDP ShortPath, uzak masaüstü istemcisi ile oturum ana bilgisayarı arasında doğrudan bağlantı kurar. Doğrudan bağlantı, Windows sanal masaüstü ağ geçitlerinin bağımlılığını azaltır, bağlantının güvenilirliğini geliştirir ve her Kullanıcı oturumu için kullanılabilir bant genişliğini artırır.
* Ek geçiş işleminin kaldırılması, gecikme süresini azaltır, bu da gecikmeli duyarlı uygulamalar ve giriş yöntemleriyle Kullanıcı deneyimini geliştirir.
* RDP ShortPath, farklı hizmetler kod noktası (DSCP) işaretleriyle RDP bağlantıları için [hizmet kalitesi (QoS) önceliğini yapılandırmaya](./rdp-quality-of-service-qos.md) yönelik destek sunar
* RDP ShortPath taşıması, [giden ağ trafiğinin sınırlandırmasını](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) , her oturum için bir kısıtlama oranı belirleyerek sağlar.

## <a name="connection-security"></a>Bağlantı güvenliği

RDP ShortPath, RDP çoklu aktarım yeteneklerini genişletdi. Ters Connect taşımasını değiştirmez ancak tamamlar. Tüm ilk oturum aracı Windows sanal masaüstü altyapısı aracılığıyla yönetilir.

UDP bağlantı noktası 3390 yalnızca, ters bağlantı aktarımı üzerinden kimliği doğrulanan gelen ShortPath trafiği için kullanılır. RDP ShortPath dinleyicisi, ters bağlantı oturumuyla eşleşmedikleri takdirde dinleyiciye yapılan tüm bağlantı girişimlerini yoksayar.

RDP ShortPath, oturum ana bilgisayarının sertifikalarını kullanarak istemci ile oturum ana bilgisayarı arasında bir TLS bağlantısı kullanır. Varsayılan olarak, RDP şifrelemesi için kullanılan sertifika, dağıtım sırasında işletim sistemi tarafından kendi kendine oluşturulmuştur. İsterseniz, müşteriler kuruluş sertifika yetkilisi tarafından verilen merkezi olarak yönetilen sertifikalar dağıtabilir. Sertifika yapılandırma hakkında daha fazla bilgi için bkz. [Windows Server belgeleri](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortfılepath bağlantı sırası

[Ters bağlantı aktarımını](./network-connectivity.md)yükledikten sonra, istemci ve oturum ana bilgisayarı RDP bağlantısı kurar ve çoklu aktarım özelliklerine anlaşma yapar. Aşağıda açıklanan ek adımlar:

1. Oturum Ana bilgisayarı, özel ve genel IPv4 ve IPv6 adreslerinin listesini istemciye gönderir.
2. İstemci, arka plan iş parçacığını, ana bilgisayarın IP adreslerinden birine doğrudan paralel bir UDP tabanlı Aktarım kurmak için başlatır.
3. İstemci, belirtilen IP adreslerini araştırırken, Kullanıcı bağlantısında gecikme olmamasını sağlamak için, geriye doğru bağlama taşıması üzerinde ilk bağlantıyı devam ettirir.
4. İstemci doğrudan bir görüş satırı içeriyorsa ve güvenlik duvarı yapılandırması doğruysa, istemci, oturum ana bilgisayarı ile güvenli bir TLS bağlantısı kurar.
5. ShortPath aktarımını kurduktan sonra RDP, uzak grafikler, giriş ve cihaz yeniden yönlendirmesi dahil olmak üzere tüm dinamik sanal kanalları (DVCs) yeni aktarıma taşıdıkça.
6. Bir güvenlik duvarı veya ağ topolojisi istemcinin doğrudan UDP bağlantısı kurmasını engelliyorsa, RDP ters bağlantı aktarımına devam eder.

Aşağıdaki diyagramda RDP ShortPath ağ bağlantısına ilişkin üst düzey bir genel bakış sağlanır.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="RDP Shortfılepath ağ bağlantılarının diyagramı" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Gereksinimler

RDP Shortfılepath 'i desteklemek için, Windows sanal masaüstü istemcisinin oturum ana bilgisayarına doğrudan bir görüş satırı olması gerekir. Aşağıdaki teknolojilerden birini kullanarak doğrudan bir görüş satırı edinebilirsiniz:

* [ExpressRoute özel eşlemesi](../expressroute/expressroute-circuit-peerings.md)
* [Siteden siteye VPN (IPSec tabanlı)](../vpn-gateway/tutorial-site-to-site-portal.md)
* [Noktadan siteye VPN (IPSec tabanlı)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Genel IP adresi ataması](../virtual-network/virtual-network-public-ip-address.md)

Azure sanal ağına bağlanmak için başka VPN türleri kullanıyorsanız en iyi sonuçlar için UDP tabanlı VPN kullanmanızı öneririz. TCP tabanlı VPN çözümlerinin çoğu UDP dahil olmak üzere tüm IP paketlerini kapsüllekarşın, RDP performansını yavaşlatabilecek, TCP tıkanıklık denetimi 'nin devralınan ek yükünü ekler.

Doğrudan görüş satırı, güvenlik duvarlarının UDP bağlantı noktası 3390 ' i engellemediğinden ve istemcinin oturum ana bilgisayarına doğrudan bağlanabilmediği anlamına gelir.

## <a name="enabling-rdp-shortpath-preview"></a>RDP Shortfılepath önizlemesini etkinleştirme

RDP ShortPath önizlemesine katılmak için oturum konağında RDP Shortfılepath dinleyicisini etkinleştirmeniz gerekir. Ortamınızda kullanılan herhangi bir sayıda oturum konağında RDP Shortfılepath 'i etkinleştirebilirsiniz. Havuzdaki tüm konaklarda RDP Shortfılepath 'i Etkinleştirme gereksinimi yoktur.
ShortPath dinleyicisini etkinleştirmek için aşağıdaki kayıt defteri değerlerini yapılandırmanız gerekir:

> [!WARNING]
> Kayıt Defteri Düzenleyicisi 'Ni kullanarak veya başka bir yöntemi kullanarak kayıt defterini yanlış şekilde değiştirirseniz önemli sorunlar oluşabilir. Bu sorunlar için işletim sisteminizi yeniden yüklemeniz gerekebilir. Microsoft, bu sorunların çözülebileceğini garanti etmez. Kayıt defterini değiştirmek kendi sorumluluğunuzdadır.

1. Oturum ana bilgisayarında Regedit.exe başlatın ve ardından şu konuma gidin:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. **Fuseudpportyeniden yönlendiricisi** adlı yeni bir **DWORD** değeri oluşturun ve bunu **1** (ondalık) olarak ayarlayın
3. **Udpportnumber** adlı yeni bir **DWORD** değeri oluşturun ve bunu **3390** (Decimal) olarak ayarlayın
4. Kayıt Defteri Düzenleyicisi 'nden çıkın.
5. Oturum konağını yeniden Başlat

Bu kayıt defteri değerlerini ayarlamak için, yükseltilmiş bir PowerShell penceresinde aşağıdaki cmdlet 'leri de çalıştırabilirsiniz:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

Grup ilkesini yapılandırmak için PowerShell de kullanabilirsiniz

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Gelişmiş Güvenlik Özellikli Windows Defender güvenlik duvarını yapılandırma

RDP ShortPath için gelen ağ trafiğine izin vermek için, güvenlik duvarı kuralları oluşturmak için grup ilkesi yönetimi MMC ek bileşenindeki Windows Defender güvenlik duvarı 'Nı gelişmiş güvenlik ile birlikte kullanın.

1. [Gelişmiş Güvenlik Özellikli Windows Defender güvenlik duvarı](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security)'na Grup İlkesi Yönetim Konsolu açın.
2. Gezinti bölmesinde **gelen kuralları**' nı seçin.
3. **Eylem**' i seçin ve ardından **Yeni kural**' ı seçin.
4. Yeni gelen kuralı Sihirbazı 'nın **kural türü** sayfasında, **özel**' i seçin ve ardından **İleri**' yi seçin.
5. **Program** sayfasında, **Bu program yolu**' nu seçin ve "% SystemRoot% \system32\svchost.exe" yazın ve ardından **İleri**' yi seçin.
6. **Protokol ve bağlantı noktaları** sayfasında UDP protokol türünü seçin. **Yerel bağlantı noktasında**, "belirli bağlantı noktaları" ı seçin ve 3390 yazın.
7. **Kapsam** sayfasında, kuralın yalnızca bu sayfada girilen IP adreslerinden gelen veya giden ağ trafiği için geçerli olduğunu belirtebilirsiniz. Tasarımınıza uygun şekilde yapılandırın ve ardından **İleri**' yi seçin.
8. **Eylem** sayfasında **bağlantıya izin ver**' i seçin ve ardından **İleri**' yi seçin.
9. **Profil** sayfasında, bu kuralın uygulandığı ağ konumu türlerini seçin ve ardından **İleri**' yi seçin.
10. **Ad** sayfasında, kuralınız için bir ad ve açıklama yazın ve ardından **son**' u seçin.

Yeni kuralın aşağıdaki ekran görüntüleriyle eşleştiğini doğrulayabilirsiniz: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="RDP Shortfılepath ağ bağlantıları Için güvenlik duvarı yapılandırması Genel sekmesinin ekran görüntüsü" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="RDP Shortfılepath ağ bağlantıları için güvenlik duvarı yapılandırması için programlar ve hizmetler sekmesinin ekran görüntüsü" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="RDP Shortfılepath ağ bağlantıları için güvenlik duvarı yapılandırması için protokoller ve bağlantı noktaları sekmesinin ekran görüntüsü" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

Windows Güvenlik Duvarı 'nı yapılandırmak için PowerShell de kullanabilirsiniz:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Windows Defender güvenlik duvarını yapılandırmak için PowerShell kullanma

Grup ilkesini yapılandırmak için PowerShell de kullanabilirsiniz

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Azure ağ güvenlik grubu yapılandırılıyor

Ağ güvenlik sınırları genelinde RDP Shortfılepath dinleyicisine erişime izin vermek için Azure ağ güvenlik grubunu gelen UDP bağlantı noktası 3390 ' e izin verecek şekilde yapılandırmanız gerekir.
Aşağıdaki parametrelerle trafiğe izin veren bir gelen güvenlik kuralı oluşturmak için [ağ güvenlik grubu belgelerini](../virtual-machines/windows/nsg-quickstart-portal.md) izleyin:

* **Kaynak**  -  İstemcilerin bulunduğu **herhangi bir** veya IP aralığı
* **Kaynak bağlantı noktası aralıkları** - **\***
* **Hedef**  -  **Herhangi biri**
* **Hedef bağlantı noktası aralıkları**  -  **3390**
* **Protokol**  -  **UDP**
* **Eylem**  -  **Izin ver**
* İsteğe bağlı olarak **önceliği** değiştirin. Öncelik kuralların uygulanma sırasını etkiler: sayısal değer ne kadar düşükse, kural daha önce uygulanır.
* **Ad** -- **RDP ShortPath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Belirli bir alt ağ için RDP Shortyolunu devre dışı bırakma

Belirli alt ağların RDP ShortPath aktarımını kullanmasını engellemeniz gerekirse, kaynak IP aralıklarını belirten ek ağ güvenlik grupları yapılandırabilirsiniz.

## <a name="verifying-the-connectivity"></a>Bağlantı doğrulanıyor

### <a name="using-connection-information-dialog"></a>Bağlantı bilgilerini kullanma iletişim kutusu

Bağlantıların RDP ShortPath kullandığını doğrulamak için bağlantı araç çubuğundaki anten simgesine tıklayarak "bağlantı bilgileri" iletişim kutusunu açın.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Uzak Masaüstü Bağlantısı çubuğunun görüntüsü":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Uzak Masaüstü Bağlantısı Info iletişim kutusunun görüntüsü":::

### <a name="using-event-logs"></a>Olay günlüklerini kullanma

Oturumun RDP Shortfılepath aktarımını kullandığını doğrulamak için:

1. Windows sanal masaüstü istemcisi 'ni kullanarak VM 'nin masaüstüne bağlanın.
2. Olay Görüntüleyicisi başlatın ve şu düğüme gidin: **uygulama ve hizmet günlükleri > microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/operasyonel**
3. RDP ShortPath aktarımının kullanıldığını anlamak için, olay KIMLIĞI 131 ' i arayın.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>ShortPath bağlantısını doğrulamak için Log Analytics kullanma

[Azure Log Analytics](./diagnostics-log-analytics.md)kullanıyorsanız, [WVDConnections tablosunu](/azure/azure-monitor/reference/tables/wvdconnections)sorgulayarak bağlantıları izleyebilirsiniz. UdpUse adlı bir sütun, Windows sanal masaüstü RDP yığınının geçerli kullanıcı bağlantısında UDP protokolünü kullanıp kullanmadığını belirtir.
Olası değerler şunlardır:

* **0** -Kullanıcı bağlantısı RDP shortfılepath kullanmıyor
* **1** -Kullanıcı bağlantısı RDP shortfılepath kullanıyor
  
Aşağıdaki sorgu listesi, bağlantı bilgilerini incelemenizi sağlar. Bu sorguyu [Log Analytics sorgu düzenleyicisinde](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)çalıştırabilirsiniz. Her sorgu için, `userupn` aramak istediğiniz kullanıcının UPN 'si ile değiştirin.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Sorun giderme

### <a name="verify-shortpath-listener"></a>ShortPath dinleyicisini doğrula

UDP dinleyicisinin etkinleştirildiğini doğrulamak için oturum konağında aşağıdaki PowerShell komutunu kullanın:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Etkinleştirilirse, aşağıdakine benzer bir çıktı görürsünüz

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Bir çakışma varsa, aşağıdaki komutu kullanarak bağlantı noktasını işgal eden işlemi tanımlayabilirsiniz

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>RDP Shortfılepath devre dışı bırakılıyor

Bazı durumlarda, RDP Shortfılepath aktarımını devre dışı bırakmanız gerekebilir. RDP Shortfılepath öğesini Grup İlkesi kullanarak devre dışı bırakabilirsiniz.

### <a name="disabling-rdp-shortpath-on-the-client"></a>İstemcide RDP Shortfılepath devre dışı bırakılıyor

Belirli bir istemci için RDP Shortfılepath 'i devre dışı bırakmak için aşağıdaki grup ilkesi kullanarak UDP desteğini devre dışı bırakabilirsiniz:

1. İstemcisinde **gpedit. msc**' yi çalıştırın.
2. **Windows bileşenleri > Uzak Masaüstü Hizmetleri > istemci > > Yönetim Şablonları ' nın bilgisayar yapılandırması**' na gidin.
3. **"ISTEMCIYE UDP 'yi kapat"** ayarını **etkin** olarak ayarla

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>Oturum konağında RDP Shortfılepath devre dışı bırakılıyor

Belirli bir oturum ana bilgisayarı için RDP Shortyolunu devre dışı bırakmak üzere, UDP desteğini devre dışı bırakmak için aşağıdaki grup ilkesi kullanabilirsiniz:

1. Oturum konağında **gpedit. msc dosyasını** çalıştırın.
2. **Windows bileşenleri > > Uzak Masaüstü Hizmetleri > ana bilgisayar uzak masaüstü bağlantısı bağlantılarını > bilgisayar yapılandırması**' na gidin.
3. **"RDP taşıma protokollerini Seç"** ayarını **yalnızca TCP** olarak ayarlayın

## <a name="public-preview-feedback"></a>Genel Önizleme geri bildirimi

Bu genel önizlemede deneyimlerinizi öğrenmek istiyoruz!
* Sorular, istekler, açıklamalar ve diğer geri bildirimler için [Bu geri bildirim formunu kullanın](https://aka.ms/RDPShortpathFeedback).

## <a name="next-steps"></a>Sonraki adımlar

* Windows Sanal Masaüstü Ağ bağlantısı hakkında bilgi edinmek için bkz. [Windows Sanal Masaüstü Ağ bağlantısını anlama](network-connectivity.md).
* Windows sanal masaüstü için hizmet kalitesi 'ni (QoS) kullanmaya başlamak için bkz. [Windows sanal masaüstü Için hizmet kalitesi (QoS) uygulama](rdp-quality-of-service-qos.md).