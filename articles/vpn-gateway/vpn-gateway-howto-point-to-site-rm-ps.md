---
title: "Bilgisayar-P2S VPN ve yerel Azure sertifika kimlik doğrulamasından VNet 'e bağlanma: PowerShell"
description: P2S ve otomatik olarak imzalanan veya CA tarafından verilen sertifikaları kullanarak Windows ve macOS istemcilerini Azure Virtual Network 'e güvenli bir şekilde bağlayın. Bu makalede PowerShell kullanılmıştır.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed0a60c88c33af70b7d780d6c4735c5f8e65b35b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660415"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Yerel Azure sertifikası kimlik doğrulaması kullanarak bir VNet 'e Noktadan siteye VPN bağlantısı yapılandırma: PowerShell

Bu makale Windows, Linux veya macOS çalıştıran ayrı istemcileri bir Azure VNet 'e güvenli bir şekilde bağlamanıza yardımcı olur. Uçtan Siteye VPN bağlantıları, evden veya bir konferanstan bir konferans yaparken sanal ağınıza uzak bir konumdan bağlanmak istediğinizde faydalıdır. Bir sanal ağa bağlanması gereken yalnızca birkaç istemciniz olduğunda Siteden Siteye VPN yerine P2S’yi de kullanabilirsiniz. Noktadan siteye bağlantılar için bir VPN cihazına veya genel kullanıma yönelik bir IP adresine gerek yoktur. P2S, VPN bağlantısını SSTP (Güvenli Yuva Tünel Protokolü) veya IKEv2 üzerinden oluşturur.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Bir bilgisayardan Azure VNet 'e bağlanma-Noktadan siteye bağlantı diyagramı":::

Noktadan siteye VPN hakkında daha fazla bilgi için bkz. [noktadan sıteye VPN hakkında](point-to-site-about.md). Azure portal kullanarak bu yapılandırmayı oluşturmak için, bkz. [Azure Portal kullanarak noktadan sıteye VPN yapılandırma](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> Bu makaledeki adımların birçoğu Azure Cloud Shell kullanabilir. Ancak, sertifika oluşturmak için Cloud Shell kullanamazsınız. Ayrıca, kök sertifika ortak anahtarını karşıya yüklemek için Azure PowerShell yerel olarak veya Azure portal kullanmanız gerekir.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. oturum aç

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. değişkenleri bildirin

Bu makalenin değişkenlerini, örnekleri değiştirmek zorunda kalmadan kendi ortamınıza uygulanacak şekilde kolayca değiştirebilmeniz için kullanabilirsiniz. Kullanmak istediğiniz değişkenleri bildirin. Gerektiğinde, değerlerini kendi değerlerinizle değiştirerek aşağıdaki örneği kullanabilirsiniz. Alıştırma sırasında herhangi bir noktada PowerShell/Cloud Shell oturumunuzu kapatırsanız, değişkenleri yeniden bildirmek için değerleri kopyalamanız ve yeniden yapıştırmanız yeterlidir.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. VNet yapılandırma

1. Bir kaynak grubu oluşturun.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Sanal ağ için alt ağ yapılandırması oluşturun ve bunları *ön uç* ve *gatewaysubnet* olarak adlandırın. Bu ön ekler bildirdiğiniz sanal adres alanının parçası olmalıdır.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Sanal ağı oluşturun.

   Bu örnekte, -DnsServer sunucu parametresi isteğe bağlıdır. Bir değer belirtildiğinde yeni bir DNS sunucusu oluşturulmaz. Belirttiğiniz DNS sunucusu IP adresi, sanal ağınızdan bağlandığınız kaynakların adlarını çözümleyebilen bir DNS sunucusu olmalıdır. Bu örnek için özel bir IP adresi kullanıldı, ancak bu, DNS sunucunuzun IP adresi olmayabilir. Kendi değerlerinizi kullandığınızdan emin olun. Belirttiğiniz değer, P2S bağlantısı veya VPN istemcisi tarafından değil, sanal ağa dağıttığınız kaynaklar tarafından kullanılıyor.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Oluşturduğunuz sanal ağa ilişkin değişkenleri belirtin.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. Bir VPN ağ geçidinin genel bir IP adresi olmalıdır. İlk olarak IP adresi kaynağını istemeniz, sonra sanal ağ geçidinizi oluştururken bu kaynağa başvurmanız gerekir. VPN ağ geçidi oluşturulurken, IP adresi kaynağa dinamik olarak atanır. VPN Gateway hizmeti şu anda yalnızca *Dinamik* Genel IP adresi ayırmayı desteklemektedir. Statik bir Genel IP adresi ataması isteğinde bulunamazsınız. Ancak, bu durum IP adresinin VPN ağ geçidinize atandıktan sonra değiştiği anlamına gelmez. Genel IP adresi, yalnızca ağ geçidi silinip yeniden oluşturulduğunda değişir. VPN ağ geçidiniz üzerinde gerçekleştirilen yeniden boyutlandırma, sıfırlama veya diğer iç bakım/yükseltme işlemleri sırasında değişmez.

   Dinamik olarak atanan bir genel IP adresi isteyin.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. VPN Gateway oluşturma

Bu adımda, VNet 'iniz için sanal ağ geçidini yapılandırır ve oluşturursunuz.

* -GatewayType değeri **Vpn** ve -VpnType değeri **RouteBased** olmalıdır.
* -VpnClientProtocol, etkinleştirmek istediğiniz tünel türlerini belirtmek için kullanılır. Tünel seçenekleri **OpenVPN, SSTP** ve **Ikev2**' dir. Bunlardan birini veya desteklenen birleşimi etkinleştirmeyi seçebilirsiniz. Birden çok türü etkinleştirmek istiyorsanız, adları virgülle ayırarak belirtin. OpenVPN ve SSTP birlikte etkinleştirilemez. Android ve Linux üzerindeki strongSwan istemcisi ile iOS ve OSX üzerindeki yerel IKEv2 VPN istemcisi, bağlanmak için yalnızca IKEv2 tünelini kullanır. Windows istemcileri önce IKEv2’yi dener ve bağlanamazsa SSTP’ye döner. OpenVPN istemcisini kullanarak OpenVPN tünel türüne bağlanabilirsiniz.
* Sanal ağ geçidi ' temel ' SKU 'SU Ikev2, OpenVPN veya RADIUS kimlik doğrulamasını desteklemez. Mac istemcilerinin sanal ağınıza bağlanmasını planlıyorsanız, temel SKU 'YU kullanmayın.
* Bir VPN ağ geçidi işleminin tamamlanması, seçtiğiniz [ağ geçidi sku'suna](vpn-gateway-about-vpn-gateway-settings.md) bağlı olarak 45 dakikaya kadar sürebilir. Bu örnekte IKEv2 kullanılmıştır.

1. VNet'iniz için sanal ağ geçidini yapılandırın ve oluşturun. Ağ geçidinin oluşturulması yaklaşık 45 dakika sürer.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. Ağ geçidinizin oluşturulduktan sonra, aşağıdaki örneği kullanarak görüntüleyebilirsiniz. PowerShell 'i kapattıysanız veya ağ geçidiniz oluşturulurken zaman aşımına uğradıysanız, [değişkenlerinizi yeniden bildirebilirsiniz](#declare) .

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. VPN istemcisi adres havuzunu ekleme

VPN ağ geçidi oluşturduktan sonra VPN istemcisi adres havuzunu ekleyebilirsiniz. VPN istemcisi adres havuzu, VPN istemcilerinin bağlanırken bir IP adresi aldığı aralıktır. Bağlandığınız şirket içi konum veya bağlanmak istediğiniz sanal ağ ile çakışmayan özel bir IP adresi aralığı kullanın.

Bu örnekte, VPN istemci adres havuzu önceki bir adımda [değişken](#declare) olarak bildirilmiştir.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. sertifika oluşturma

>[!IMPORTANT]
> Azure Cloud Shell kullanarak sertifika oluşturamazsınız. Bu bölümde özetlenen yöntemlerden birini kullanmalısınız. PowerShell 'i kullanmak istiyorsanız, yerel olarak kurmanız gerekir.
>

Sertifikalar, Noktadan siteye VPN 'Ler için VPN istemcilerinin kimliğini doğrulamak üzere Azure tarafından kullanılır. Kök sertifikanın ortak anahtar bilgilerini Azure'a yükleyin. Bundan sonra ortak anahtar, 'güvenilir' olarak kabul edilir. Güvenilir kök sertifikadan istemci sertifikaları oluşturulmalı ve sonra Sertifikalar-Geçerli Kullanıcı/Kişisel sertifika deposundaki her bir istemci bilgisayara yüklenmelidir. Sertifika, sanal ağ ile bağlantı başlattığında istemcinin kimliğini doğrulamak için kullanılır. 

Otomatik olarak imzalanan sertifikalar kullanıyorsanız bu sertifikaların belirli parametreler kullanılarak oluşturulması gerekir. [PowerShell ve Windows 10](vpn-gateway-certificates-point-to-site.md)'a yönelik yönergeler yardımıyla, otomatik olarak imzalanan sertifika oluşturabilir veya Windows 10'a sahip değilseniz [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) kullanabilirsiniz. Otomatik olarak imzalanan kök sertifika ve istemci sertifikası oluştururken yönergelerdeki adımları uygulamanız önemlidir. Aksi halde, oluşturduğunuz sertifikalar P2S bağlantılarıyla uyumlu olmaz ve bağlantı hatası alırsınız.

### <a name="root-certificate"></a><a name="cer"></a>Kök sertifika

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. Kök sertifikayı oluşturduktan sonra, ortak sertifika verilerini (özel anahtarı değil) Base64 kodlamalı X. 509.952. cer dosyası olarak [dışarı aktarın](vpn-gateway-certificates-point-to-site.md#cer) .

### <a name="client-certificate"></a><a name="generate"></a>İstemci sertifikası

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. İstemci sertifikası oluşturduktan sonra [dışarı aktarın](vpn-gateway-certificates-point-to-site.md#clientexport) . İstemci sertifikası bağlanacak istemci bilgisayarlara dağıtılır.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. kök sertifika ortak anahtar bilgilerini karşıya yükleyin

VPN ağ geçidini oluşturma işleminin tamamlandığını doğrulayın. İşlem tamamlandıktan sonra, güvenilen kök sertifikanın .cer dosyasını (ortak anahtar bilgilerini içerir) Azure’a yükleyebilirsiniz. Bir .cer dosyası karşıya yüklendikten sonra Azure, güvenilir kök sertifikadan oluşturulmuş bir istemci sertifikasının yüklü olduğu istemcilerin kimliklerini doğrulamak için bu dosyayı kullanabilir. Daha sonra gerekirse, toplam 20 adede kadar güvenilir kök sertifika dosyasını karşıya yükleyebilirsiniz.

>[!NOTE]
> Azure Cloud Shell kullanarak. cer dosyasını karşıya yükleyemezsiniz. PowerShell 'i bilgisayarınızda yerel olarak kullanabilir veya [Azure Portal adımları](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)kullanabilirsiniz.
>

1. Sertifika adınızın değişkenini tanımlamak için değeri kendi değerinizle değiştirin.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Dosya yolunu kendi dosya yolunuzla değiştirin ve ardından cmdlet'leri çalıştırın.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Ortak anahtar bilgilerini Azure'a yükleyin. Sertifika bilgileri karşıya yüklendikten sonra, Azure bunu güvenilen bir kök sertifika olarak kabul eder. Karşıya yüklerken, PowerShell 'i bilgisayarınızda yerel olarak çalıştırdığınızdan emin olun veya bunun yerine [Azure Portal adımları](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)kullanabilirsiniz. Azure Cloud Shell kullanarak karşıya yükleyemezsiniz.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. bir içe aktarılmış istemci sertifikası yükler

Aşağıdaki adımlar bir Windows istemcisine yüklemenize yardımcı olur. Ek istemciler ve daha fazla bilgi için bkz. [istemci sertifikası yüklemesi](point-to-site-how-to-vpn-client-install-azure-cert.md).

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

İstemci sertifikasının tüm sertifika zinciriyle birlikte .pfx dosyası olarak (varsayılan seçenek) dışarı aktarılmadığından emin olun. Aksi takdirde kök sertifika bilgileri istemci bilgisayarda bulunmaz ve istemci, kimliğini düzgün bir biçimde doğrulayamaz.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. VPN istemcisini yapılandırma

Bu bölümde, bilgisayarınızın yerel istemcisini sanal ağ geçidine bağlanacak şekilde yapılandırırsınız. Örneğin, Windows bilgisayarınızdaki VPN ayarlarına gittiğinizde VPN bağlantıları ekleyebilirsiniz. Noktadan siteye bağlantı, belirli yapılandırma ayarları gerektirir. Bu adımlar, yerel VPN istemcinizin bir noktadan siteye bağlantı üzerinden sanal ağa bağlanabilmesi için gereken belirli ayarlarla bir paket oluşturmanıza yardımcı olur.

İstemci yapılandırma paketini oluşturmak ve yüklemek için aşağıdaki hızlı örnekleri kullanabilirsiniz. Paket içerikleri ve VPN istemci yapılandırma dosyalarını oluşturma ve yüklemeye ilişkin ek yönergeler hakkında daha fazla bilgi için bkz. [VPN istemcisi yapılandırma dosyalarını oluşturma ve yüklemeyi](point-to-site-vpn-client-configuration-azure-cert.md).

Değişkenlerinizi tekrar bildirmeniz gerekiyorsa, [burada](#declare)bulabilirsiniz.

### <a name="to-generate-configuration-files"></a>Yapılandırma dosyaları oluşturmak için

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>İstemci yapılandırma paketini yüklemek için

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Azure 'a bağlanma

### <a name="windows-vpn-client"></a>Windows VPN istemcisi

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Mac VPN istemcisi

Ağ iletişim kutusunda kullanmak istediğiniz istemci profilini bulup **Bağlan**’a tıklayın.
Ayrıntılı yönergeler için [Install-Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) ' i işaretleyin. Bağlanmayla ilgili sorun yaşıyorsanız, sanal ağ geçidinin temel bir SKU kullanmadığından emin olun. Temel SKU, Mac istemcileri için desteklenmez.

  ![Mac bağlantısı](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>Bir bağlantıyı doğrulamak için

Bu yönergeler, Windows istemcileri için geçerlidir.

1. VPN bağlantınızın etkin olduğunu doğrulamak için, yükseltilmiş bir komut istemi açın ve *ipconfig/all* komutunu çalıştırın.
2. Sonuçlara bakın. Aldığınız IP adresinin, yapılandırmanızda belirttiğiniz Noktadan siteye VPN Istemci adres havuzu içindeki adreslerden biri olduğunu unutmayın. Sonuçları şu örneğe benzer:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Sanal makineye bağlanma

Bu yönergeler, Windows istemcileri için geçerlidir.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Sanal ağ için DNS sunucusu IP adresleri belirtildikten sonra VPN istemci yapılandırma paketinin oluşturulduğunu doğrulayın. DNS sunucusu IP adreslerini güncelleştirdiyseniz, yeni bir VPN istemci yapılandırma paketi oluşturup yükleyin.

* 'ipconfig' seçeneğini kullanarak bağlantıyı kurduğunuz bilgisayardaki Ethernet bağdaştırıcısına atanmış IPv4 adresini denetleyin. IP adresi bağlanacağınız sanal ağın adres aralığında veya VPNClientAddressPool adres aralığında ise, bu durum çakışan bir adres alanı olarak adlandırılır. Adres alanınız bu şekilde çakıştığında, ağ trafiği Azure’a ulaşmaz ve yerel ağda kalır.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Kök sertifika eklemek veya kaldırmak için

Azure’da güvenilen kök sertifikayı ekleyebilir veya kaldırabilirsiniz. Bir kök sertifikayı kaldırdığınızda, o kökten oluşturulmuş bir sertifikaya sahip istemciler kimlik doğrulaması yapamaz ve bağlantı kuramaz. Bir istemcinin kimlik doğrulaması yapmasını ve bağlanmasını istiyorsanız, Azure’da güvenilen (karşıya yüklenmiş) bir kök sertifikadan oluşturulmuş yeni bir istemci sertifikası yüklemeniz gerekir. Bu adımlar, bilgisayarınızda yerel olarak yüklü Azure PowerShell cmdlet 'lerinin (Azure Cloud Shell değil) kullanılmasını gerektirir. Kök sertifika eklemek için Azure portal de kullanabilirsiniz.

**Şunu eklemek için:**

Azure'a en fazla 20 kök sertifika .cer dosyası ekleyebilirsiniz. Aşağıdaki adımlar bir kök sertifika eklemenize yardımcı olur. 

1. Karşıya yüklenecek .cer dosyasını hazırlayın:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Dosyayı karşıya yükleyin. Aynı anda yalnızca bir dosya yükleyebilirsiniz.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. Sertifika dosyasının karşıya yüklendiğini doğrulamak için:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Kaldırmak için:**

1. Değişkenleri bildirin. Örnekteki değişkenleri kaldırmak istediğiniz sertifikayla eşleşecek şekilde değiştirin.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Sertifikayı kaldırın.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. Sertifikanın başarıyla kaldırıldığını doğrulamak için aşağıdaki örneği kullanın.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>Bir istemci sertifikasını iptal etmek veya yeniden devreye sokmak için

İstemci sertifikalarını iptal edebilirsiniz. Sertifika iptal listesi, tek tek istemci sertifikalarına göre Noktadan siteye bağlantıyı seçmeli olarak reddetmenizi sağlar. Bu, güvenilen kök sertifika kaldırma işleminden farklıdır. Azure’dan güvenilen kök sertifika .cer dosyasını kaldırırsanız iptal edilen kök sertifika tarafından oluşturulan/imzalanan tüm istemci sertifikaları reddedilir. Kök sertifika yerine istemci sertifikasını iptal etmek, kök sertifikadan oluşturulan diğer sertifikaların kimlik doğrulaması amacıyla kullanılmaya devam edilmesine olanak sağlar.

Genellikle ekip ve kuruluş düzeylerinde erişimi yönetmek için kök sertifika kullanılırken ayrı kullanıcılar üzerinde ayrıntılı erişim denetimi için iptal edilen istemci sertifikaları kullanılır.

**İptal etmek için:**

1. İstemci sertifikasının parmak izini alın. Daha fazla bilgi için bkz. [bir sertifikanın parmak izini alma](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).

1. Bilgileri bir metin düzenleyicisine kopyalayın ve sürekli bir dize haline getirmek için tüm boşlukları kaldırın. Bu dize sonraki adımda bir değişken olarak bildirilir.

1. Değişkenleri bildirin. Önceki adımda alınan parmak izini bildirdiğinizden emin olun.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Parmak izini, iptal edilen sertifika listesine ekleyin. Parmak izi eklendiğinde “Başarılı” ifadesini görürsünüz.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Parmak izinin, sertifika iptal listesine eklendiğini doğrulayın.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. Parmak izi eklendikten sonra sertifika artık bağlanmak için kullanılamaz. Bu sertifikayı kullanarak bağlanmaya çalışan istemciler sertifikanın artık geçerli olmadığını belirten bir ileti alır.

**Yeniden devreye geçmek için:**

Parmak izini, iptal edilen istemci sertifikaları listesinden kaldırarak bir istemci sertifikasını yeniden devreye sokabilirsiniz.

1. Değişkenleri bildirin. Yeniden devreye sokmak istediğiniz sertifika için doğru parmak izini bildirdiğinizden emin olun.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Sertifika parmak izini sertifika iptal listesinden kaldırın.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Parmak izinin, iptal edilen parmak izi listesinden kaldırılıp kaldırılmadığını kontrol edin.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Noktadan siteye hakkında SSS

Ek Noktadan siteye bilgi için [VPN Gateway Noktadan siteye hakkında SSS](vpn-gateway-vpn-faq.md#P2S) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](../index.yml). Ağ ve sanal makineler hakkında daha fazla bilgi edinmek için, bkz. [Azure ve Linux VM ağına genel bakış](../virtual-machines/network-overview.md).

P2S sorun giderme bilgileri için [Sorun giderme: Azure noktadan siteye bağlantısı sorunları](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).