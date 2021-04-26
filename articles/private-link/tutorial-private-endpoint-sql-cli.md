---
title: "Öğretici: Azure özel uç noktası kullanarak Azure SQL Server 'a bağlanma-Azure CLı"
description: Azure CLı kullanarak özel bir uç nokta ile Azure SQL Server oluşturmayı öğrenmek için bu öğreticiyi kullanın
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 5a66749a3e773328e6c6c02375e76998bd6531cd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871966"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Öğretici: Azure özel uç noktası kullanarak Azure SQL Server 'a bağlanma-Azure CLı

Azure özel uç noktası, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel olarak özel bağlantı kaynaklarıyla iletişim kurmasını sağlar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir sanal ağ ve savunma ana bilgisayarı oluşturun.
> * Sanal makine oluşturur.
> * Azure SQL Server ve özel uç nokta oluşturun.
> * SQL Server özel uç noktası ile bağlantıyı test edin.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure portal oturum açın ve ' i çalıştırarak aboneliğinizin etkin olduğunu denetleyin `az login` .
* Çalıştıran bir Terminal veya komut penceresinde Azure CLı sürümünüzü denetleyin `az --version` . En son sürüm için bkz. [en son sürüm notları](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * En son sürüme sahip değilseniz, [işletim sisteminiz veya platformunuzun yükleme kılavuzunu](/cli/azure/install-azure-cli)izleyerek yüklemenizi güncelleştirin.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[Az Group Create](/cli/azure/group#az_group_create)ile bir kaynak grubu oluşturun:

* Adlandırılmış **CreateSQLEndpointTutorial-RG**. 
* **Eastus** konumunda.

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
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
* **CreateSQLEndpointTutorial-RG** kaynak grubunda.
* **Eastus** konumu.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
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
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Savunma konağı için genel bir IP adresi oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) kullanın:

* **Mybastionıp** adlı standart bölge YEDEKLI genel IP adresi oluşturun.
* **CreateSQLEndpointTutorial-RG** içinde.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Bir savunma alt ağı oluşturmak için [az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) kullanın:

* **AzureBastionSubnet** adlı.
* **10.0.1.0/24** adres ön eki.
* Sanal ağ **\** sanal ağı 'nda.
* Kaynak grubu **CreateSQLEndpointTutorial-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Bir savunma konağı oluşturmak için [az Network savunma Create](/cli/azure/network/bastion#az_network_bastion_create) kullanın:

* Adlandırılmış **Mybastionhost**.
* **CreateSQLEndpointTutorial-RG** içinde.
* Genel IP **Mybastionıp** ile ilişkilendirildi.
* Sanal ağ **Myvnet** ile ilişkilendirildi.
* **Eastus** konumunda.

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
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
* **CreateSQLEndpointTutorial-RG** içinde.
* Ağ **Myvnet** içinde.
* Alt ağda **Mybackendsubnet**.
* Sunucu görüntüsü **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-an-azure-sql-server"></a>Azure SQL Server oluşturma

Bu bölümde, bir SQL Server ve veritabanı oluşturacaksınız.

SQL Server oluşturmak için [az SQL Server Create](/cli/azure/sql/server#az_sql_server_create) kullanın:

* **\<sql-server-name>** Benzersiz sunucu adınızla değiştirin.
* **\<your-password>** Parolanızla değiştirin.
* **CreateSQLEndpointTutorial-RG** içinde.
* **Eastus** bölgesinde.

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Veritabanı oluşturmak için [az SQL DB Create](/cli/azure/sql/db#az_sql_db_create) kullanın:

* **MyDatabase** adlı.
* **CreateSQLEndpointTutorial-RG** içinde.
* **\<sql-server-name>** Benzersiz sunucu adınızla değiştirin.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Özel uç nokta oluştur

Bu bölümde, Özel uç nokta oluşturacaksınız.

SQL Server 'ın kaynak KIMLIĞINI bir Shell değişkenine yerleştirmek için [az SQL Server List](/cli/azure/sql/server#az_sql_server_list) kullanın.

Uç nokta ve bağlantı oluşturmak için [az Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) kullanın:

* Adlandırılmış **Myprivateendpoint**.
* Kaynak grubu **CreateSQLEndpointTutorial-RG**.
* Sanal ağ **\** sanal ağı 'nda.
* Alt ağda **Mybackendsubnet**.
* **MyConnection** adlı bağlantı.

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Özel DNS bölgesini yapılandırma

Bu bölümde, [az Network Private-DNS Zone Create](/cli/azure/network/private-dns/zone#az_network_private_dns_zone_create)kullanarak özel DNS bölgesi oluşturacak ve yapılandıracaksınız.  

DNS bölgesine sanal ağ bağlantısı oluşturmak için [az Network Private-DNS link VNET oluştur](/cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create) öğesini kullanacaksınız.

[Az Network Private-Endpoint DNS-Zone-Group Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)komutuyla bir DNS bölgesi grubu oluşturacaksınız.

* **Privatelink.Database.Windows.net** adlı bölge
* Sanal ağ **\** sanal ağı 'nda.
* Kaynak grubu **CreateSQLEndpointTutorial-RG**.
* **Mydnslink** adlı DNS bağlantısı.
* **Myprivateendpoint** ile ilişkili.
* **Myzonegroup** adlı bölge grubu.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Özel uç nokta ile bağlantıyı sına

Bu bölümde, önceki adımda oluşturduğunuz sanal makineyi kullanarak özel uç nokta genelinde SQL Server 'a bağlanırsınız.

1. [Azure portalda](https://portal.azure.com) oturum açma 
 
2. Sol taraftaki Gezinti bölmesinde **kaynak grupları** ' nı seçin.

3. **CreateSQLEndpointTutorial-RG** seçin.

4. **Myvm**' yi seçin.

5. **Myvm** için genel bakış sayfasında **Bağlan** ' ı **seçin.**

6. Mavi **kullanımı** savunma düğmesini seçin.

7. Sanal makine oluşturma sırasında girdiğiniz kullanıcı adını ve parolayı girin.

8. Bağlandıktan sonra sunucuda Windows PowerShell 'i açın.

9. `nslookup <sqlserver-name>.database.windows.net` yazın. **\<sqlserver-name>** Önceki adımlarda oluşturduğunuz SQL Server 'ın adıyla değiştirin.  Aşağıda görüntülene benzer bir ileti alacaksınız:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    SQL Server adı için **10.0.0.5** özel IP adresi döndürülür.  Bu adres, daha önce oluşturduğunuz sanal ağın alt ağıdır.


10. **Myvm**'de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) 'yi yükler.

11. **SQL Server Management Studio** açın.

12. **Sunucuya Bağlan**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sunucu türü | **Veritabanı Altyapısı**’nı seçin.|
    | Sunucu adı | **\<sql-server-name> . Database.Windows.net** girin |
    | Kimlik doğrulaması | **SQL Server Kimlik Doğrulaması**'nı seçin. |
    | Kullanıcı adı | Sunucu oluşturma sırasında girdiğiniz kullanıcı adını girin |
    | Parola | Sunucu oluşturma sırasında girdiğiniz parolayı girin |
    | Parolayı unutmayın | **Evet**’i seçin. |

13. **Bağlan**’ı seçin.

14. Sol menüden veritabanlarına gözatamazsınız.

15. I **Mysqldatabase**'dan bilgi oluşturun veya sorgulayın.

16. **Myvm**'e yönelik savunma bağlantısını kapatın. 

## <a name="clean-up-resources"></a>Kaynakları temizleme 

Özel uç nokta, SQL Server ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunu oluşturdunuz:

* Sanal ağ ve savunma Konağı.
* Sanal makine.
* Özel uç nokta ile Azure SQL Server.

Sanal makineyi, Özel uç nokta genelinde SQL Server ile güvenli bir şekilde test etmek için kullandınız.

Bir sonraki adım olarak, Web uygulamasını, sanal ağın dışında bir Web uygulamasını bir veritabanının özel uç noktasına bağlayan **Azure SQL veritabanı mimarisi senaryosuna özel bağlantı ile** de ilgileniyor olabilirsiniz.
> [!div class="nextstepaction"]
> [Azure SQL veritabanı 'na özel bağlantı içeren Web uygulaması](/azure/architecture/example-scenario/private-web-app/private-web-app)