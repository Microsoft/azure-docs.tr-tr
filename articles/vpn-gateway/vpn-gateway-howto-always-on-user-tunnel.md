---
title: Her zaman açık VPN Kullanıcı tüneli yapılandırma
titleSuffix: Azure VPN Gateway
description: Bu makalede, VPN ağ geçidiniz için Always on VPN Kullanıcı tünelinin nasıl yapılandırılacağı açıklanmaktadır
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: 099ed3c3c0ac2abe034388849385a45b44b32b34
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145960"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Always On VPN kullanıcı tüneli yapılandırma

Windows 10 VPN istemcisinin yeni bir özelliği olan her zaman açık, bir VPN bağlantısını sürdürme olanağıdır. Her zaman açık olduğunda, etkin VPN profili otomatik olarak bağlanabilir ve Kullanıcı oturum açma, ağ durumu değişikliği veya cihaz ekranı etkin gibi tetikleyicilere göre bağlı kalır.

Azure 'da kalıcı Kullanıcı tünellerini ve cihaz tünellerini oluşturmak için Azure sanal ağ geçitleri 'ni her zaman açık Windows 10 ile kullanabilirsiniz. Bu makale, her zaman açık VPN Kullanıcı tüneli yapılandırmanıza yardımcı olur.

Her zaman VPN bağlantılarında iki tür tünelden biri vardır:

* **Cihaz tüneli**: kullanıcılar cihazda oturum açmadan önce belirtilen VPN sunucularına bağlanır. Oturum açma öncesi bağlantı senaryoları ve cihaz yönetimi bir cihaz tüneli kullanır.

* **Kullanıcı tüneli**: yalnızca kullanıcılar cihazda oturum açtıktan sonra bağlanır. Kullanıcı tünellerini kullanarak, VPN sunucuları üzerinden kuruluş kaynaklarına erişebilirsiniz.

Cihaz tünelleri ve Kullanıcı tünelleri, VPN profillerinin bağımsız olarak çalışır. Bunlar aynı anda birbirine bağlanabilir ve uygun şekilde farklı kimlik doğrulama yöntemleri ve diğer VPN yapılandırma ayarlarını kullanabilirler.

Aşağıdaki bölümlerde, bir VPN ağ geçidi ve Kullanıcı tüneli yapılandırırsınız.

## <a name="step-1-configure-a-vpn-gateway"></a>1\. Adım: VPN Gateway yapılandırma

VPN ağ geçidini, bu [Noktadan siteye](vpn-gateway-howto-point-to-site-resource-manager-portal.md) bu makaledeki yönergeleri izleyerek Ikev2 ve sertifika tabanlı kimlik doğrulaması kullanacak şekilde yapılandırırsınız.

## <a name="step-2-configure-a-user-tunnel"></a>2\. Adım: Kullanıcı tüneli yapılandırma

1. İstemci sertifikalarını, bu [noktadan sıteye VPN istemci](point-to-site-how-to-vpn-client-install-azure-cert.md) makalesinde gösterildiği gibi Windows 10 istemcisine yükler. Sertifika, geçerli kullanıcı deposunda olmalıdır.

1. [Windows 10 Istemci her zaman VPN bağlantılarında yapılandırma](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)bölümündeki yönergeleri izleyerek PowerShell, System Center Configuration Manager veya Intune aracılığıyla Always on VPN istemcisini yapılandırın.

### <a name="example-configuration-for-the-user-tunnel"></a>Kullanıcı tüneli için örnek yapılandırma

Sanal ağ geçidini yapılandırdıktan ve istemci sertifikasını Windows 10 istemcisindeki yerel makine deposuna yükledikten sonra, aşağıdaki örnekleri kullanarak bir istemci cihaz tüneli yapılandırın:

1. Aşağıdaki metni kopyalayın ve *Usercert. ps1*olarak kaydedin:

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
1. Aşağıdaki metni kopyalayın ve *Vpnprofile. xml* olarak *Usercert. ps1*ile aynı klasöre kaydedin. Ortamınıza uyması için aşağıdaki metni düzenleyin:

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. PowerShell 'i yönetici olarak çalıştırın.

1. PowerShell 'de, *Usercert. ps1* ve *vpnprofile. xml* ' nin bulunduğu klasöre geçin ve aşağıdaki komutu çalıştırın:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![Machineccerttest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. **VPN ayarları**altında **usertest** girişini bulun ve sonra **Bağlan**' ı seçin.

1. Bağlantı başarılı olursa, her zaman açık Kullanıcı tüneli başarıyla yapılandırdınız.

## <a name="clean-up-your-resources"></a>Kaynaklarınızı temizleme

Profili kaldırmak için aşağıdakileri yapın:

1. Şu komutu çalıştırın:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Bağlantının bağlantısını kesin ve **otomatik olarak bağlan** onay kutusunu temizleyin.

![Temizleme](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Sonraki adımlar

Oluşabilecek bağlantı sorunlarını gidermek için bkz. [Azure Noktadan siteye bağlantı sorunları](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
