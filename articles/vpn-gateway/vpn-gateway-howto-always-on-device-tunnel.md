---
title: Her zaman açık VPN tüneli yapılandırma
titleSuffix: Azure VPN Gateway
description: VPN Gateway için Always on VPN tünelini yapılandırma adımları
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: f22b29cfcaf1d4c4ce28b2b0557d70b281b6891f
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146385"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Always On VPN cihazı tüneli yapılandırma

Windows 10 sanal özel ağ (VPN) istemcisinin yeni özelliklerinden biri, VPN bağlantısını sürdürme olanağıdır. Her zaman açık, etkin VPN profilinin otomatik olarak bağlanmasını ve tetikleyicilere göre bağlı kalmasını sağlayan bir Windows 10 özelliğidir; yani Kullanıcı oturum açma, ağ durumu değişikliği veya cihaz ekranı etkin olur.

Azure sanal ağ geçitleri, kalıcı Kullanıcı tünellerinin yanı sıra Azure 'a yönelik cihaz tünellerini oluşturmak için Windows 10 her zaman açık ile kullanılabilir. Bu makale, her zaman açık bir VPN cihaz tüneli yapılandırmanıza yardımcı olur.

Her zaman VPN bağlantılarında iki tür tünel vardır:

* **Cihaz tüneli** , kullanıcılar cihazda oturum açmadan önce belirtilen VPN sunucularına bağlanır. Oturum açma öncesi bağlantı senaryoları ve cihaz yönetimi amaçları cihaz tüneli kullanır.

* **Kullanıcı tüneli** yalnızca, bir Kullanıcı cihazda oturum açtıktan sonra bağlanır. Kullanıcı tüneli, kullanıcıların VPN sunucuları aracılığıyla kuruluş kaynaklarına erişmesini sağlar.

Hem cihaz tüneli hem de Kullanıcı tüneli VPN profilleriyle bağımsız olarak çalışır. Bunlar aynı anda birbirine bağlanabilir ve uygun şekilde farklı kimlik doğrulama yöntemleri ve diğer VPN yapılandırma ayarlarını kullanabilir.

## <a name="1-configure-the-gateway"></a>1. ağ geçidini yapılandırma

VPN ağ geçidini, bu [Noktadan siteye bu makaleyi](vpn-gateway-howto-point-to-site-resource-manager-portal.md)kullanarak Ikev2 ve sertifika tabanlı kimlik doğrulaması kullanacak şekilde yapılandırın.

## <a name="2-configure-the-device-tunnel"></a>2. cihaz tüneli yapılandırma

Bir cihaz tünelini başarıyla kurmak için aşağıdaki gereksinimlerin karşılanması gerekir:

* Cihaz, Windows 10 Enterprise veya eğitim sürüm 1709 veya üstünü çalıştıran etki alanına katılmış bir bilgisayar olmalıdır.
* Tünel yalnızca Windows yerleşik VPN çözümü için yapılandırılabilir ve bilgisayar sertifikası kimlik doğrulamasıyla Ikev2 kullanılarak oluşturulmuştur. 
* Her cihaz için yalnızca bir cihaz tüneli yapılandırılabilir.

1. İstemci sertifikalarını, bu [noktadan sıteye VPN istemci makalesinde](point-to-site-how-to-vpn-client-install-azure-cert.md)gösterildiği gibi Windows 10 istemcisine yükler. Sertifikanın yerel makine deposunda olması gerekir.
1. [Bu yönergeleri](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) kullanarak bir VPN profili oluşturun ve cihaz TÜNELINI yerel sistem hesabı bağlamında yapılandırın.

### <a name="configuration-example-for-device-tunnel"></a>Cihaz tüneli için yapılandırma örneği

Sanal ağ geçidini yapılandırdıktan ve istemci sertifikasını Windows 10 istemcisindeki yerel makine deposuna yükledikten sonra, bir istemci cihaz tüneli yapılandırmak için aşağıdaki örnekleri kullanın.

1. Aşağıdaki metni kopyalayın ve ***devicecert. ps1***olarak kaydedin.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Aşağıdaki metni kopyalayın ve ***Vpnprofile. xml*** olarak, **devecert. ps1**ile aynı klasöre kaydedin. Ortamınıza uyması için aşağıdaki metni düzenleyin.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) 'Dan **PsExec** indirin ve dosyaları **c:\padstools dizinine**ayıklayın.
1. Yönetici CMD isteminde şunu çalıştırarak PowerShell 'i başlatın:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. PowerShell 'de, **devicecert. ps1** ve **vpnprofile. xml** ' nin bulunduğu klasöre geçin ve aşağıdaki komutu çalıştırın:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![Machineccerttest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. **RASPHONE**çalıştırın.

   ![belirlen](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. **Machineccerttest** girişini bulun ve **Bağlan**' a tıklayın.

   ![Bağlan](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Bağlantı başarılı olursa, bilgisayarı yeniden başlatın. Tünel otomatik olarak bağlanır.

## <a name="cleanup"></a>Temizleme

Profili kaldırmak için aşağıdaki komutu çalıştırın:

![Temizleme](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Sonraki adımlar

Sorun giderme için bkz. [Azure Noktadan siteye bağlantı sorunları](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
