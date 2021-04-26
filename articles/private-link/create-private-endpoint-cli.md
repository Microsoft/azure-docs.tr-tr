---
title: Hızlı başlangıç-Azure CLı kullanarak Azure özel uç noktası oluşturma
description: Azure CLı kullanarak özel uç nokta oluşturmayı öğrenmek için bu hızlı başlangıcı kullanın.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 036052dc45b8d029dac6e137b3a878b75e6e015c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873406"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak özel uç nokta oluşturma

Azure Web uygulamasına güvenli bir şekilde bağlanmak için özel bir uç nokta kullanarak Azure özel bağlantısı ile çalışmaya başlayın.

Bu hızlı başlangıçta, bir Azure Web uygulaması için özel bir uç nokta oluşturacak ve özel bağlantıyı test etmek için bir sanal makine dağıtacaksınız.  

Özel uç noktalar Azure SQL ve Azure depolama gibi farklı türlerde Azure hizmetleri için oluşturulabilir.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure aboneliğinizde dağıtılan **PremiumV2 katmanı** veya daha yüksek bir App Service planına sahip bir Azure Web uygulaması.  
    * Daha fazla bilgi ve bir örnek için bkz. [hızlı başlangıç: Azure 'da ASP.NET Core Web uygulaması oluşturma](../app-service/quickstart-dotnetcore.md). 
    * Web uygulaması ve uç nokta oluşturma hakkında ayrıntılı bir öğretici için bkz. [öğretici: Azure özel uç nokta kullanarak bir Web uygulamasına bağlanma](tutorial-private-endpoint-webapp-portal.md).
* Azure portal oturum açın ve ' i çalıştırarak aboneliğinizin etkin olduğunu denetleyin `az login` .
* Çalıştıran bir Terminal veya komut penceresinde Azure CLı sürümünüzü denetleyin `az --version` . En son sürüm için bkz. [en son sürüm notları](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * En son sürüme sahip değilseniz, [işletim sisteminiz veya platformunuzun yükleme kılavuzunu](/cli/azure/install-azure-cli)izleyerek yüklemenizi güncelleştirin.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[Az Group Create](/cli/azure/group#az_group_create)ile bir kaynak grubu oluşturun:

* Adlandırılmış **Createprivateendpointqs-RG**. 
* **Eastus** konumunda.

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Sanal ağ ve savunma Konağı oluşturma

Bu bölümde, bir sanal ağ, alt ağ ve savunma ana bilgisayarı oluşturacaksınız. 

Savunma Konağı, Özel uç noktasını test etmek üzere sanal makineye güvenli bir şekilde bağlanmak için kullanılacaktır.

[Az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create) ile bir sanal ağ oluşturun

* **Myvnet** adında.
* **10.0.0.0/16** adres ön eki.
* **Mybackendsubnet** adlı alt ağ.
* **10.0.0.0/24** alt ağ öneki.
* **Createprivateendpointqs-RG** kaynak grubunda.
* **Eastus** konumu.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

[Az Network VNET subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)ile özel uç nokta için özel uç nokta ağ ilkelerini devre dışı bırakacak şekilde alt ağı güncelleştirin:

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Savunma konağı için genel bir IP adresi oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) kullanın:

* **Mybastionıp** adlı standart bölge YEDEKLI genel IP adresi oluşturun.
* **Createprivateendpointqs-RG** içinde.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Bir savunma alt ağı oluşturmak için [az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) kullanın:

* **AzureBastionSubnet** adlı.
* **10.0.1.0/24** adres ön eki.
* Sanal ağ **\** sanal ağı 'nda.
* **Createprivateendpointqs-RG** kaynak grubunda.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Bir savunma konağı oluşturmak için [az Network savunma Create](/cli/azure/network/bastion#az_network_bastion_create) kullanın:

* Adlandırılmış **Mybastionhost**.
* **Createprivateendpointqs-RG** içinde.
* Genel IP **Mybastionıp** ile ilişkilendirildi.
* Sanal ağ **Myvnet** ile ilişkilendirildi.
* **Eastus** konumunda.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure savunma konağının dağıtılması birkaç dakika sürebilir.

## <a name="create-test-virtual-machine"></a>Test sanal makinesi oluştur

Bu bölümde, Özel uç noktayı test etmek için kullanılacak bir sanal makine oluşturacaksınız.

 [Az VM Create](/cli/azure/vm#az_vm_create)Ile bir VM oluşturun. İstendiğinde, VM için kimlik bilgileri olarak kullanılacak bir parola girin:

* **Myvm** adı.
* **Createprivateendpointqs-RG** içinde.
* Ağ **Myvnet** içinde.
* Alt ağda **Mybackendsubnet**.
* Sunucu görüntüsü **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-private-endpoint"></a>Özel uç nokta oluştur

Bu bölümde, Özel uç nokta oluşturacaksınız.

Daha önce oluşturduğunuz Web uygulamasının kaynak KIMLIĞINI bir Shell değişkenine yerleştirmek için [az WebApp List](/cli/azure/webapp#az_webapp_list) kullanın.

Uç nokta ve bağlantı oluşturmak için [az Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) kullanın:

* Adlandırılmış **Myprivateendpoint**.
* **Createprivateendpointqs-RG** kaynak grubunda.
* Sanal ağ **\** sanal ağı 'nda.
* Alt ağda **Mybackendsubnet**.
* **MyConnection** adlı bağlantı.
* WEBAPP **\<webapp-resource-group-name>** .

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Özel DNS bölgesini yapılandırma

Bu bölümde, [az Network Private-DNS Zone Create](/cli/azure/network/private-dns/zone#az_network_private_dns_zone_create)kullanarak özel DNS bölgesi oluşturacak ve yapılandıracaksınız.  

DNS bölgesine sanal ağ bağlantısı oluşturmak için [az Network Private-DNS link VNET oluştur](/cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create) öğesini kullanacaksınız.

[Az Network Private-Endpoint DNS-Zone-Group Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)komutuyla bir DNS bölgesi grubu oluşturacaksınız.

* **Privatelink.azurewebsites.net** adlı bölge
* Sanal ağ **\** sanal ağı 'nda.
* **Createprivateendpointqs-RG** kaynak grubunda.
* **Mydnslink** adlı DNS bağlantısı.
* **Myprivateendpoint** ile ilişkili.
* **Myzonegroup** adlı bölge grubu.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>Özel uç nokta ile bağlantıyı sına

Bu bölümde, önceki adımda oluşturduğunuz sanal makineyi kullanarak özel uç nokta genelinde SQL Server 'a bağlanırsınız.

1. [Azure portalda](https://portal.azure.com) oturum açma 
 
2. Sol taraftaki Gezinti bölmesinde **kaynak grupları** ' nı seçin.

3. **Createprivateendpointqs-RG** öğesini seçin.

4. **Myvm**' yi seçin.

5. **Myvm** için genel bakış sayfasında **Bağlan** ' ı **seçin.**

6. Mavi **kullanımı** savunma düğmesini seçin.

7. Sanal makine oluşturma sırasında girdiğiniz kullanıcı adını ve parolayı girin.

8. Bağlandıktan sonra sunucuda Windows PowerShell 'i açın.

9. `nslookup <your-webapp-name>.azurewebsites.net` yazın. **\<your-webapp-name>** Önceki adımlarda oluşturduğunuz Web uygulamasının adıyla değiştirin.  Aşağıda görüntülene benzer bir ileti alacaksınız:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Web uygulaması adı için **10.0.0.5** özel IP adresi döndürülür.  Bu adres, daha önce oluşturduğunuz sanal ağın alt ağıdır.

10. **Myvm**'e yönelik savunma bağlantısı ' nda Internet Explorer 'ı açın.

11. Web uygulamanızın URL 'sini girin, **https:// \<your-webapp-name> . azurewebsites.net**.

12. Uygulamanız dağıtılmamışsa varsayılan Web uygulaması sayfasını alacaksınız:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Varsayılan Web uygulaması sayfası." border="true":::

13. **Myvm** bağlantısını kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme 
Özel uç nokta ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları kaldırmak için [az Group Delete](/cli/azure/group#az_group_delete) kullanın:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunu oluşturdunuz:

* Sanal ağ ve savunma Konağı.
* Sanal makine.
* Bir Azure Web uygulaması için özel uç nokta.

Özel uç nokta genelinde Web uygulamasıyla güvenli bir şekilde bağlantıyı test etmek için sanal makineyi kullandınız.

Özel bir uç noktayı destekleyen hizmetler hakkında daha fazla bilgi için bkz.:
> [!div class="nextstepaction"]
> [Özel bağlantı kullanılabilirliği](private-link-overview.md#availability)
