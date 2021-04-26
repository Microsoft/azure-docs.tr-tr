---
title: Azure dosyaları ile kullanmak üzere Windows üzerinde Noktadan siteye (P2S) VPN yapılandırma | Microsoft Docs
description: Azure dosyaları ile kullanılmak üzere Windows üzerinde Noktadan siteye (P2S) VPN yapılandırma
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: de342267292c6a93c4a1ba2eae232403ccaf9514
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785280"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Azure dosyaları ile kullanmak üzere Windows üzerinde Noktadan siteye (P2S) VPN yapılandırma
Azure dosya paylaşımlarınızı, 445 numaralı bağlantı noktasını açmadan Azure dışından SMB 'ye bağlamak için Noktadan siteye (P2S) VPN bağlantısı kullanabilirsiniz. Noktadan siteye VPN bağlantısı, Azure ile tek bir istemci arasındaki VPN bağlantısıdır. Azure dosyaları ile P2S VPN bağlantısı kullanmak için, bağlanmak isteyen her istemci için bir P2S VPN bağlantısının yapılandırılması gerekir. Şirket içi ağınızdan Azure dosya paylaşımlarınızın bağlanması gereken çok sayıda istemciniz varsa, her istemci için Noktadan siteye bağlantı yerine siteden siteye (S2S) VPN bağlantısı kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure dosyaları ile kullanmak Için siteden sıteye VPN yapılandırma](storage-files-configure-s2s-vpn.md).

Azure dosyaları için kullanılabilir ağ seçenekleri hakkında ayrıntılı bir tartışmaya devam etmeden önce, bu nasıl yapılır makalesine geçmeden önce [doğrudan Azure dosya paylaşımının erişimine yönelik ağ konularını](storage-files-networking-overview.md) okumanızı öneririz.

Makalede, Windows 'da (Windows istemcisi ve Windows Server) Noktadan siteye VPN 'yi yapılandırmaya yönelik adımlar, Azure dosya paylaşımlarını doğrudan şirket içine bağlamak için ayrıntılı olarak açıklanır. VPN üzerinden Azure Dosya Eşitleme trafiği yönlendirmek istiyorsanız lütfen bkz. [Azure dosya eşitleme proxy ve güvenlik duvarı ayarlarını yapılandırma](../file-sync/file-sync-firewall-and-proxy.md).

## <a name="prerequisites"></a>Önkoşullar
- Azure PowerShell modülünün en son sürümü. Azure PowerShell nasıl yükleneceğine ilişkin daha fazla bilgi için, bkz. [Azure PowerShell modülünü yüklemek](/powershell/azure/install-az-ps) ve işletim sisteminizi seçmek. Windows 'da Azure CLı 'yı kullanmayı tercih ediyorsanız, aşağıdaki yönergeler Azure PowerShell için sunulmuştur.

- Şirket içinde bağlamak istediğiniz bir Azure dosya paylaşımıdır. Azure dosya paylaşımları, birden çok dosya paylaşımı dağıtabileceğiniz ve BLOB kapsayıcıları ya da kuyrukları gibi diğer depolama kaynaklarına ek olarak, bir paylaşılan depolama havuzunu temsil eden yönetim yapıları içindeki depolama hesaplarında dağıtılır. Azure dosya paylaşımları ve depolama hesaplarını [Azure dosya paylaşımı oluşturma](storage-how-to-create-file-share.md)bölümünde dağıtma hakkında daha fazla bilgi edinebilirsiniz.

- Şirket içinde bağlamak istediğiniz Azure dosya paylaşımının bulunduğu depolama hesabı için özel bir uç nokta. Özel uç nokta oluşturma hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları ağ uç noktalarını yapılandırma](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Sanal ağ dağıtma
Azure dosya paylaşımınıza ve diğer Azure kaynaklarına Noktadan siteye VPN aracılığıyla Şirket içinden erişmek için bir sanal ağ veya VNet oluşturmanız gerekir. Otomatik olarak oluşturacağınız P2S VPN bağlantısı, şirket içi Windows makineniz ve bu Azure sanal ağı arasında bir köprüdir.

Aşağıdaki PowerShell üç alt ağı olan bir Azure sanal ağı oluşturacak: bir depolama hesabınızın özel uç noktası için bir tane olmak üzere, bir depolama hesabının özel uç noktası için, bir tane, değişebilir depolama hesabının genel IP 'si için özel yönlendirme oluşturmadan ve VPN hizmetini sağlayan sanal ağ geçidiniz için bir tane olmak üzere, şirket içi depolama hesabına erişmek için gereklidir. 

`<region>`, `<resource-group>` Ve `<desired-vnet-name>` değerlerini, ortamınız için uygun değerlerle değiştirmeyi unutmayın.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>VPN kimlik doğrulaması için kök sertifika oluştur
Şirket içi Windows makinelerinizin sanal ağınıza erişim için kimlik doğrulamasından geçen VPN bağlantılarının doğrulanması için, iki sertifika oluşturmanız gerekir: sanal makine ağ geçidine sağlanacak bir kök sertifika ve kök sertifikayla imzalanacak bir istemci sertifikası. Aşağıdaki PowerShell kök sertifikayı oluşturur; istemci sertifikası, ağ geçidinden alınan bilgilerle Azure sanal ağ geçidi oluşturulduktan sonra oluşturulur. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Sanal ağ geçidini dağıtma
Azure sanal ağ geçidi, şirket içi Windows makinelerinizin bağlanacağı hizmettir. Bu hizmeti dağıtmak için iki temel bileşen gerekir: dünyanın her yerinden ve daha önce oluşturduğunuz bir kök sertifikaya ve istemcilerinizin kimliğini doğrulamak için kullanılacak olan ağ geçidini tanımlayacak genel bir IP.

`<desired-vpn-name-here>`Bu kaynaklar için istediğiniz adla değiştirmeyi unutmayın.

> [!Note]  
> Azure sanal ağ geçidi dağıtımı en fazla 45 dakika sürebilir. Bu kaynak dağıtılırken, bu PowerShell betiği dağıtımın tamamlanmasını engelleyecek. Bu beklenen bir durumdur.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>İstemci sertifikası oluştur
İstemci sertifikası, sanal ağ geçidinin URI 'siyle oluşturulur. Bu sertifika, daha önce oluşturduğunuz kök sertifika ile imzalanmıştır.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>VPN istemcisini yapılandırma
Azure sanal ağ geçidi, şirket içi Windows makinenizde VPN bağlantısını başlatmak için gereken yapılandırma dosyaları içeren indirilebilir bir paket oluşturur. VPN bağlantısını, Windows 10/Windows Server 2016 + [' nın Always on VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) özelliğini kullanarak yapılandıracağız. Bu paket, istenirse, eski Windows VPN istemcisini yapılandıracak yürütülebilir paketleri de içerir. Bu kılavuz, her zaman VPN istemcisi, son kullanıcıların makine üzerinde yönetici izinlerine sahip olmadan Azure VPN 'ye bağlanmasına/bağlantıyı kesmeye izin verdiği için, eski Windows VPN istemcisi yerine her zaman VPN 'Yi kullanır. 

Aşağıdaki betik, sanal ağ geçidine yönelik kimlik doğrulaması için gereken istemci sertifikasını yükler, indir ve VPN paketini yükler. `<computer1>`Ve ' nin `<computer2>` istenen bilgisayarlarla değiştirilmesini unutmayın. Bu betiği, diziye daha fazla PowerShell oturumu ekleyerek istediğiniz sayıda makine üzerinde çalıştırabilirsiniz `$sessions` . Kullanım hesabınız, bu makinelerin her birinde bir yönetici olmalıdır. Bu makinelerden biri içinden betiği çalıştırdığınız yerel makinedir, betiği yükseltilmiş bir PowerShell oturumundan çalıştırmanız gerekir. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Azure dosya paylaşımından bağlama
Noktadan siteye VPN 'yi ayarladığınıza göre, PowerShell aracılığıyla kurulum yaptığınız bilgisayarlara Azure dosya paylaşımından bağlamak için kullanabilirsiniz. Aşağıdaki örnek, paylaşımını bağlayacaktır, paylaşımın gerçekten bağlandığını kanıtlamak için paylaşımın kök dizinini listeler ve paylaşımın bağlantısını çıkarın. Ne yazık ki, PowerShell uzaktan iletişim üzerinden paylaşımın kalıcı olarak bağlanması mümkün değildir. Kalıcı olarak bağlamak için bkz. [Windows Ile Azure dosya paylaşma kullanma](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Ayrıca bkz.
- [Doğrudan Azure dosya paylaşma erişimi için ağ oluşturma konuları](storage-files-networking-overview.md)
- [Linux üzerinde Azure dosyaları ile kullanım için Noktadan siteye (P2S) VPN yapılandırma](storage-files-configure-p2s-vpn-linux.md)
- [Azure dosyaları ile kullanmak için siteden siteye (S2S) VPN yapılandırma](storage-files-configure-s2s-vpn.md)