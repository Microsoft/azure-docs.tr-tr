---
title: Özel bağlantı-Azure CLı-MariaDB için Azure veritabanı
description: Azure CLı 'dan MariaDB için Azure veritabanı için özel bağlantıyı yapılandırma hakkında bilgi edinin
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4d2a77a1e1079a7f9b013ad4cf0810989835ed73
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790579"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>CLı kullanarak MariaDB için Azure veritabanı için özel bağlantı oluşturma ve yönetme

Özel uç nokta, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel bağlantı kaynaklarıyla özel olarak iletişim kurmasına olanak sağlar. Bu makalede, Azure CLı 'yı kullanarak Azure sanal ağında bir VM oluşturma ve Azure özel uç noktası ile MariaDB sunucusu için Azure veritabanı oluşturma hakkında bilgi edineceksiniz.

> [!NOTE]
> Özel bağlantı özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında bulunan MariaDB sunucuları için Azure veritabanı 'nda kullanılabilir. Veritabanı sunucusunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- [MariaDB sunucusu Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-cli.md)gerekir. 

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Herhangi bir kaynak oluşturabilmeniz için önce sanal ağı barındırmak üzere bir kaynak grubu oluşturmanız gerekir. [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Bu örnek *westeurope* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Sanal Ağ Oluşturma
[Az Network VNET Create](/cli/azure/network/vnet)komutuyla bir sanal ağ oluşturun. Bu örnek, *Mysubnet* adlı bir alt ağ ile *myVirtualNetwork* adlı varsayılan bir sanal ağ oluşturur:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Alt ağ özel uç nokta ilkelerini devre dışı bırak 
Azure, bir sanal ağ içindeki bir alt ağa kaynak dağıtır, bu nedenle özel uç nokta [ağ ilkelerini](../private-link/disable-private-endpoint-network-policy.md)devre dışı bırakmak için alt ağ oluşturmanız veya güncelleştirmeniz gerekir. [Az Network VNET subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)Ile *mysubnet* adlı bir alt ağ yapılandırmasını güncelleştirin:

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Sanal makineyi oluşturma 
Az VM Create ile bir VM oluşturun. İstendiğinde, sanal makine için oturum açma kimlik bilgileri olarak kullanılacak bir parola girin. Bu örnek, *myvm* ADLı bir VM oluşturur: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 VM 'nin genel IP adresini aklınızda edin. Sonraki adımda İnternet 'ten VM 'ye bağlanmak için bu adresi kullanacaksınız.

## <a name="create-an-azure-database-for-mariadb-server"></a>MariaDB için Azure Veritabanı sunucusu oluşturma 
Az MariaDB Server Create komutuyla bir MariaDB için Azure veritabanı oluşturun. MariaDB sunucunuzun adının Azure genelinde benzersiz olması gerektiğini unutmayın. bu nedenle, yer tutucu değerini köşeli ayraç içinde kendi benzersiz bir değer ile değiştirin: 

```azurecli-interactive
# Create a server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> Bazı durumlarda, MariaDB için Azure veritabanı ve sanal ağ alt ağı farklı aboneliklerdedir. Bu durumlarda, aşağıdaki yapılandırmalardan emin olmanız gerekir:
> - Her iki abonelikte da **Microsoft. Dbformarıdb** kaynak sağlayıcısının kayıtlı olduğundan emin olun. Daha fazla bilgi için [Resource-Manager-kayıt][resource-manager-portal] bölümüne bakın

## <a name="create-the-private-endpoint"></a>Özel uç nokta oluşturma 
Sanal ağınızdaki MariaDB sunucusu için özel bir uç nokta oluşturun: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMariaDB/servers" --query "id" -o tsv) \    
    --group-id mariadbServer \  
    --connection-name myConnection  
 ```


## <a name="configure-the-private-dns-zone"></a>Özel DNS bölgesini yapılandırma 
Marıdb sunucu etki alanı için bir Özel DNS bölgesi oluşturun ve sanal ağla bir ilişki bağlantısı oluşturun. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Müşterinin DNS ayarındaki FQDN, yapılandırılmış özel IP'ye çözümlenmiyor. [Burada](../dns/dns-operations-recordsets-portal.md)gösterildiği gibi, yapılandırılmış FQDN IÇIN bir DNS bölgesi oluşturmanız gerekir.

## <a name="connect-to-a-vm-from-the-internet"></a>İnternet'ten bir sanal makineye bağlanma

Aşağıdaki gibi, internet *'ten gelen VM VM* 'sine bağlanın:

1. Portalın arama çubuğuna *myVm* değerini girin.

1. **Bağlan** düğmesini seçin. **Bağlan** düğmesini seçtikten sonra **sanal makineye bağlan** açılır.

1. **RDP Dosyasını İndir**’i seçin. Azure bir Uzak Masaüstü Protokolü (*. rdp*) dosyası oluşturur ve bilgisayarınıza indirir.

1. *İndirilen. rdp* dosyasını açın.

    1. İstendiğinde **Bağlan**’ı seçin.

    1. VM oluştururken belirttiğiniz kullanıcı adını ve parolayı girin.

        > [!NOTE]
        >   >  VM oluştururken girdiğiniz kimlik bilgilerini belirtmek için **farklı bir hesap kullan**' ı seçmeniz gerekebilir.

1. **Tamam**’ı seçin.

1. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bir sertifika uyarısı alırsanız **Evet**’i veya **Devam**’ı seçin.

1. VM masaüstü seçildikten sonra, bunu yerel masaüstünüze geri dönmek için simge durumuna küçültün.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>MariaDB sunucusuna VM 'den özel olarak erişin

1.  *Myvm* uzak masaüstünde PowerShell ' i açın.

2.  `nslookup mydemoserver.privatelink.mariadb.database.azure.com` yazın. 

    Şuna benzer bir ileti alırsınız:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Kullanılabilir herhangi bir istemciyi kullanarak MariaDB sunucusu için özel bağlantı bağlantısını test edin. Aşağıdaki örnekte, işlemi yapmak için [MySQL çalışma ekranı](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) kullandım.

4. **Yeni bağlantı**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Bağlantı Adı| Seçtiğiniz bağlantı adını seçin.|
    | Konak adı | *Mydemoserver.Privatelink.MariaDB.Database.Azure.com* seçin |
    | Kullanıcı adı | *username@servername* MariaDB sunucu oluşturma sırasında belirtilen kullanıcı adını girin. |
    | Parola | MariaDB sunucu oluşturma sırasında bir parola girin. |
    ||

5. **Bağlantıyı Sına** veya **Tamam ' ı** seçin.

6. I Sol menüden veritabanlarına gözatıp MariaDB veritabanından bilgi oluşturma veya sorgulama

8. MyVm ile uzak masaüstü bağlantısını kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme 
Artık gerekli değilse, az Group DELETE ' i kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Sonraki adımlar
[Azure özel uç noktası nedir?](../private-link/private-endpoint-overview.md) hakkında daha fazla bilgi edinin

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
