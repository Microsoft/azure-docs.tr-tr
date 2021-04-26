---
title: Linux üzerinde Azure dosyaları ile kullanım için Noktadan siteye (P2S) VPN yapılandırma | Microsoft Docs
description: Linux 'ta Azure dosyaları ile kullanım için Noktadan siteye (P2S) VPN yapılandırma
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9608e3bdaab033d58796a3841e8cd92d7a8a81ef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777986"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Linux üzerinde Azure dosyaları ile kullanım için Noktadan siteye (P2S) VPN yapılandırma
Azure dosya paylaşımlarınızı, 445 numaralı bağlantı noktasını açmadan Azure dışından SMB 'ye bağlamak için Noktadan siteye (P2S) VPN bağlantısı kullanabilirsiniz. Noktadan siteye VPN bağlantısı, Azure ile tek bir istemci arasındaki VPN bağlantısıdır. Azure dosyaları ile P2S VPN bağlantısı kullanmak için, bağlanmak isteyen her istemci için bir P2S VPN bağlantısının yapılandırılması gerekir. Şirket içi ağınızdan Azure dosya paylaşımlarınızın bağlanması gereken çok sayıda istemciniz varsa, her istemci için Noktadan siteye bağlantı yerine siteden siteye (S2S) VPN bağlantısı kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure dosyaları ile kullanmak Için siteden sıteye VPN yapılandırma](storage-files-configure-s2s-vpn.md).

Azure dosyaları için kullanılabilir ağ seçenekleri hakkında ayrıntılı bir tartışmaya devam etmeden önce [Azure dosyaları ağ genel bakış ' ı](storage-files-networking-overview.md) okumanızı kesinlikle öneririz.

Makalesinde, Linux üzerinde Noktadan siteye VPN yapılandırma adımları ayrıntılı olarak Azure dosya paylaşımlarını doğrudan şirket içinde bağlayacaksınız. VPN üzerinden Azure Dosya Eşitleme trafiği yönlendirmek istiyorsanız lütfen bkz. [Azure dosya eşitleme proxy ve güvenlik duvarı ayarlarını yapılandırma](../file-sync/file-sync-firewall-and-proxy.md).

## <a name="prerequisites"></a>Önkoşullar
- Azure CLı 'nın en son sürümü. Azure CLı 'nın nasıl yükleneceği hakkında daha fazla bilgi için bkz. [Azure POWERSHELL CLI 'Yı yüklemek](/cli/azure/install-azure-cli) ve işletim sisteminizi seçmek. Linux üzerinde Azure PowerShell modülünü kullanmayı tercih ederseniz, Azure CLı için aşağıdaki yönergeler sunulmaktadır.

- Şirket içinde bağlamak istediğiniz bir Azure dosya paylaşımıdır. Azure dosya paylaşımları, birden çok dosya paylaşımı dağıtabileceğiniz ve BLOB kapsayıcıları ya da kuyrukları gibi diğer depolama kaynaklarına ek olarak, bir paylaşılan depolama havuzunu temsil eden yönetim yapıları içindeki depolama hesaplarında dağıtılır. Azure dosya paylaşımları ve depolama hesaplarını [Azure dosya paylaşımı oluşturma](storage-how-to-create-file-share.md)bölümünde dağıtma hakkında daha fazla bilgi edinebilirsiniz.

- Şirket içinde bağlamak istediğiniz Azure dosya paylaşımının bulunduğu depolama hesabı için özel bir uç nokta. Özel uç nokta oluşturma hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları ağ uç noktalarını yapılandırma](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Gerekli yazılımları yükler
Azure sanal ağ geçidi, IPSec ve OpenVPN gibi çeşitli VPN protokollerini kullanarak VPN bağlantıları sağlayabilir. Bu kılavuzda, IPSec 'in nasıl kullanılacağı ve Linux üzerinde destek sağlamak için Strongswa paketinin nasıl kullanıldığı gösterilmektedir. 

> Ubuntu 18,10 ile doğrulandı.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Sanal ağ dağıtma 
Azure dosya paylaşımınıza ve diğer Azure kaynaklarına Noktadan siteye VPN aracılığıyla Şirket içinden erişmek için bir sanal ağ veya VNet oluşturmanız gerekir. Otomatik olarak oluşturacağınız P2S VPN bağlantısı, şirket içi Linux makineniz ile bu Azure sanal ağı arasında bir köprüdir.

Aşağıdaki betik, üç alt ağı olan bir Azure sanal ağı oluşturacak: bir depolama hesabınızın özel uç noktası için bir tane, bir depolama hesabının özel uç noktası için, bir tane, değişebilir depolama hesabının genel IP 'si için özel yönlendirme oluşturmadan ve VPN hizmetini sağlayan sanal ağ geçidiniz için özel yönlendirme oluşturmadan şirket içi depolama hesabına erişmek için gereklidir. 

`<region>`, `<resource-group>` Ve `<desired-vnet-name>` değerlerini, ortamınız için uygun değerlerle değiştirmeyi unutmayın.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>VPN kimlik doğrulaması için sertifika oluşturma
Şirket içi Linux makinelerinizin sanal ağınıza erişmesi için kimlik doğrulamasından geçmek üzere, iki sertifika oluşturmanız gerekir: sanal makine ağ geçidine sağlanacak bir kök sertifika ve kök sertifikayla imzalanacak bir istemci sertifikası. Aşağıdaki betik gerekli sertifikaları oluşturur.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Sanal ağ geçidini dağıtma
Azure sanal ağ geçidi, şirket içi Linux makinelerinizin bağlanacağı hizmettir. Bu hizmeti dağıtmak için iki temel bileşen gerekir: dünyanın her yerinden ve daha önce oluşturduğunuz bir kök sertifikaya ve istemcilerinizin kimliğini doğrulamak için kullanılacak bir kök sertifikaya sahip olan, istemcileriniz için ağ geçidini tanımlayacak genel bir IP.

`<desired-vpn-name-here>`Bu kaynaklar için istediğiniz adla değiştirmeyi unutmayın.

> [!Note]  
> Azure sanal ağ geçidi dağıtımı en fazla 45 dakika sürebilir. Bu kaynak dağıtılırken, bu Bash betik betiği dağıtımın tamamlanmasını engelleyecek.
>
> **Temel** SKU Ile P2S Ikev2/OpenVPN bağlantıları desteklenmez. Bu betik, uygun şekilde sanal ağ geçidi için **VpnGw1** SKU 'su kullanır.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>VPN istemcisini yapılandırma
Azure sanal ağ geçidi, şirket içi Linux makinenizde VPN bağlantısını başlatmak için gereken yapılandırma dosyaları içeren indirilebilir bir paket oluşturur. Aşağıdaki betik, oluşturduğunuz sertifikaları doğru bir yere yerleştirir ve `ipsec.conf` dosyayı indirilebilir paketteki yapılandırma dosyasından doğru değerlerle yapılandıracaktır.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Azure dosya paylaşımından bağlama
Noktadan siteye VPN 'yi ayarladığınıza göre, Azure dosya paylaşımınızı bağlayabilirsiniz. Aşağıdaki örnek, paylaşımdan kalıcı olmayan şekilde bağlayacaktır. Kalıcı olarak bağlamak için bkz. [Linux Ile Azure dosya paylaşma kullanma](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Ayrıca bkz.
- [Azure dosyaları ağlarına genel bakış](storage-files-networking-overview.md)
- [Azure dosyaları ile kullanmak üzere Windows üzerinde Noktadan siteye (P2S) VPN yapılandırma](storage-files-configure-p2s-vpn-windows.md)
- [Azure dosyaları ile kullanmak için siteden siteye (S2S) VPN yapılandırma](storage-files-configure-s2s-vpn.md)