---
title: Özel bağlantı ile özel uç nokta ayarla
description: Bir kapsayıcı kayıt defterinde özel bir uç nokta ayarlayın ve yerel bir sanal ağdaki özel bir bağlantı üzerinden erişimi etkinleştirin. Özel bağlantı erişimi, Premium hizmet katmanının bir özelliğidir.
ms.topic: article
ms.date: 03/31/2021
ms.openlocfilehash: d3c7c573b0ffc08a85f5cbe5cc62d3f7c052f0af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781442"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Azure özel bağlantısını kullanarak bir Azure Container Registry 'ye özel olarak bağlanma


Kayıt noktalarına sanal ağ özel IP adresleri atayarak ve [Azure özel bağlantısı](../private-link/private-link-overview.md)' nı kullanarak bir kayıt defterine erişimi sınırlayın. Sanal ağdaki istemciler ve kayıt defterinin özel uç noktaları arasındaki ağ trafiği, genel İnternet 'ten etkilenme olasılığını ortadan kaldıran sanal ağ ve Microsoft omurga ağındaki özel bir bağlantı ile ilgilidir. Özel bağlantı Ayrıca [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) özel eşlemesi veya bir [VPN ağ geçidi](../vpn-gateway/vpn-gateway-about-vpngateways.md)aracılığıyla Şirket içinden özel kayıt defteri erişimi de sunar.

Ayarların kayıt defterinin ayrılmış özel IP adresine çözümlenmesi için, kayıt defterinin özel uç noktaları için [DNS ayarlarını yapılandırabilirsiniz](../private-link/private-endpoint-overview.md#dns-configuration) . DNS yapılandırması ile ağdaki istemciler ve hizmetler, kayıt defterine *myregistry.azurecr.io* gibi tam etki alanı adı ile erişmeye devam edebilir. 

Bu özellik **Premium** kapsayıcı kayıt defteri hizmet katmanında kullanılabilir. Şu anda bir kayıt defteri için en fazla 10 özel uç nokta ayarlanabilir. Kayıt defteri hizmeti katmanları ve limitleri hakkında bilgi için bkz. [Azure Container Registry katmanları](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bu makalede Azure CLı adımlarını kullanmak için, Azure CLı sürüm 2.6.0 veya sonraki bir sürümü önerilir. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli]. Veya [Azure Cloud Shell](../cloud-shell/quickstart.md)içinde çalıştırın.
* Zaten bir kapsayıcı kayıt defteriniz yoksa, bir tane oluşturun (Premium katman gereklidir) ve Microsoft Container Registry gibi örnek bir ortak görüntü [içeri aktarın](container-registry-import-images.md) `mcr.microsoft.com/hello-world` . Örneğin, [Azure Portal][quickstart-portal] veya [Azure CLI][quickstart-cli] kullanarak bir kayıt defteri oluşturun.
* Farklı bir Azure aboneliğinde özel bir bağlantı kullanarak kayıt defteri erişimini yapılandırmak için bu abonelikte Azure Container Registry kaynak sağlayıcısını kaydetmeniz gerekir. Örnek:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Bu makaledeki Azure CLı örnekleri aşağıdaki ortam değişkenlerini kullanır. Ortamınız için uygun değerleri değiştirin. Bash kabuğu için tüm örnekler biçimlendirilir:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Özel bağlantı ayarlama-CLı

### <a name="get-network-and-subnet-names"></a>Ağ ve alt ağ adlarını al

Zaten yoksa, bir özel bağlantı kurmak için bir sanal ağ ve alt ağ adları gerekir. Bu örnekte, VM ve kayıt defterinin özel uç noktası için aynı alt ağ kullanılır. Ancak birçok senaryoda, uç noktayı ayrı bir alt ağda ayarlarsınız. 

Bir VM oluşturduğunuzda, Azure varsayılan olarak aynı kaynak grubunda bir sanal ağ oluşturur. Sanal ağın adı, sanal makinenin adını temel alır. Örneğin, *Mydockervm* sanal makinenizi adlandırdıysanız, varsayılan sanal ağ adı Mydockervmvnet adlı bir alt ağ ile Mydockervmvnet *olur.*  Bu değerleri, [az Network VNET List][az-network-vnet-list] komutunu çalıştırarak ortam değişkenlerinde ayarlayın:

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Alt ağda ağ ilkelerini devre dışı bırak

Özel uç nokta için alt ağdaki ağ güvenlik grupları gibi [ağ Ilkelerini devre dışı bırakın](../private-link/disable-private-endpoint-network-policy.md) . Alt ağ yapılandırmanızı [az Network VNET subnet Update][az-network-vnet-subnet-update]ile güncelleştirin:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Özel DNS bölgesini yapılandırma

Özel Azure Container Registry etki alanı için [özel bir Azure DNS bölgesi](../dns/private-dns-privatednszone.md) oluşturun. Sonraki adımlarda, bu DNS bölgesindeki kayıt defteri etki alanınız için DNS kayıtları oluşturursunuz. Daha fazla bilgi için bu makalenin devamındaki [DNS yapılandırma seçenekleri](#dns-configuration-options)bölümüne bakın.

Azure Container Registry 'niz için varsayılan DNS çözümlemesini geçersiz kılmak üzere özel bir bölge kullanmak için, bölgenin **Privatelink.azurecr.io** olarak adlandırılması gerekir. Özel bölgeyi oluşturmak için aşağıdaki [az Network Private-DNS Zone Create][az-network-private-dns-zone-create] komutunu çalıştırın:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>İlişki bağlantısı oluşturma

Özel bölgenizi sanal ağla ilişkilendirmek için [az Network Private-DNS link VNET Create][az-network-private-dns-link-vnet-create] ' i çalıştırın. Bu örnek *Mydnslink* adlı bir bağlantı oluşturur.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Özel kayıt defteri uç noktası oluşturma

Bu bölümde, sanal ağda kayıt defterinin özel uç noktasını oluşturun. İlk olarak, kayıt defterinizin kaynak KIMLIĞINI alın:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Kayıt defterinin özel uç noktasını oluşturmak için [az Network Private-ENDPOINT Create][az-network-private-endpoint-create] komutunu çalıştırın.

Aşağıdaki örnek, *Myprivateendpoint* ve hizmet bağlantısı *myConnection* uç noktasını oluşturur. Uç nokta için bir kapsayıcı kayıt defteri kaynağı belirtmek için şu geçişi yapın `--group-ids registry` :

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-endpoint-ip-configuration"></a>Uç nokta IP yapılandırmasını al

DNS kayıtlarını yapılandırmak için özel uç noktanın IP yapılandırmasını alın. Özel uç noktanın ağ arabirimiyle ilişkili olan bu örnekteki kapsayıcı kayıt defteri için iki özel IP adresi vardır: biri kayıt defterinin kendisi için ve diğeri kayıt defterinin veri uç noktası için. 

İlk olarak, ağ arabirimi KIMLIĞI için özel uç noktayı sorgulamak için [az Network Private-Endpoint Show][az-network-private-endpoint-show] ' ı çalıştırın:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Aşağıdaki [az Network NIC][az-network-nic-show] , kapsayıcı kayıt defteri ve kayıt defteri 'nin veri uç noktası IÇIN özel IP adreslerini Al komutlarını göstermektedir:

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```

> [!NOTE]
> Kayıt defteriniz coğrafi olarak [çoğaltılırsa](container-registry-geo-replication.md), her kayıt defteri çoğaltması için ek veri uç noktası için sorgu sorgulayın.

### <a name="create-dns-records-in-the-private-zone"></a>Özel bölgede DNS kayıtları oluşturma

Aşağıdaki komutlar, kayıt defteri uç noktası ve veri uç noktası için özel bölgede DNS kayıtları oluşturur. Örneğin, *westeurope* bölgesinde *myregistry* adlı bir kayıt defteriniz varsa, uç nokta adları ve ' dir `myregistry.azurecr.io` `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Kayıt defteriniz coğrafi olarak [çoğaltılırsa](container-registry-geo-replication.md), her bir çoğaltmanın veri uç noktası IP 'si için ek ton DNS kayıtları oluşturun.

İlk çalıştırma [az Network Private-DNS Record-][az-network-private-dns-record-set-a-create] kayıt defteri uç noktası ve veri uç noktası Için boş bir kayıt kümesi oluşturmak üzere Create a Create a set:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Kayıt defteri uç noktası ve veri uç noktası için bir kayıt oluşturmak üzere [az Network Private-DNS Record-set a Add-Record][az-network-private-dns-record-set-a-add-record] komutunu çalıştırın:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $REGISTRY_PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

Özel bağlantı artık yapılandırıldı ve kullanıma hazır.

## <a name="set-up-private-link---portal"></a>Özel bağlantı ayarlama-Portal

Bir kayıt defteri oluşturduğunuzda özel bir bağlantı kurun veya var olan bir kayıt defterine özel bir bağlantı ekleyin. Aşağıdaki adımlarda, test için bir sanal ağınız ve alt ağınız zaten bir VM ile ayarlanmış olduğunu varsayar. Ayrıca, [Yeni bir sanal ağ ve alt ağ oluşturabilirsiniz](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Özel uç nokta oluşturma-yeni kayıt defteri

1. Portalda bir kayıt defteri oluştururken, **temel kavramlar** sekmesinde, **SKU**' da **Premium**' u seçin.
1. **Ağ** sekmesini seçin.
1. **Ağ bağlantısı**' nda **Özel uç nokta**  >  **+ Ekle**' yi seçin.
1. Aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | Mevcut bir grubun adını girin veya yeni bir grup oluşturun.|
    | Name | Benzersiz bir ad girin. |
    | Alt kaynak |**Kayıt defteri** seçin|
    | **Ağ** | |
    | Sanal ağ| Sanal makinenizin dağıtıldığı sanal ağı ( *Mydockervmvnet* gibi) seçin. |
    | Alt ağ | Sanal makinenizin dağıtıldığı *Mydockervmsubnet* gibi bir alt ağ seçin. |
    |**Özel DNS tümleştirme**||
    |Özel DNS bölgesi ile tümleştirme |**Evet**’i seçin. |
    |Özel DNS Bölgesi |Seç *(yeni) Privatelink.azurecr.io* |
    |||
1. Kalan kayıt defteri ayarlarını yapılandırın ve ardından **gözden geçir + oluştur**' u seçin.

  ![Özel uç nokta ile kayıt defteri oluşturma](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Özel uç nokta oluşturma-mevcut kayıt defteri

1. Portalda kapsayıcı Kayıt defterinize gidin.
1. **Ayarlar** altında **ağ**' ı seçin.
1. **Özel uç noktalar** sekmesinde **+ Özel uç nokta**' ı seçin.
1. **Temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | Mevcut bir grubun adını girin veya yeni bir grup oluşturun.|
    | **Örnek ayrıntıları** |  |
    | Name | Bir ad girin. |
    |Region|Bir bölge seçin.|
    |||
5. **Sonraki: kaynak**' ı seçin.
6. Aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |Bağlantı yöntemi  | **Dizinimde bir Azure kaynağına bağlan '** ı seçin.|
    | Abonelik| Aboneliğinizi seçin. |
    | Kaynak türü | **Microsoft. ContainerRegistry/kayıt defterleri**' ni seçin. |
    | Kaynak |Kayıt defterinizin adını seçin|
    |Hedef alt kaynak |**Kayıt defteri** seçin|
    |||
7. Ileri 'yi seçin **: yapılandırma**.
8. Bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |**Ağ**| |
    | Sanal ağ| Sanal makinenizin dağıtıldığı sanal ağı ( *Mydockervmvnet* gibi) seçin. |
    | Alt ağ | Sanal makinenizin dağıtıldığı *Mydockervmsubnet* gibi bir alt ağ seçin. |
    |**Özel DNS tümleştirme**||
    |Özel DNS bölgesi ile tümleştirme |**Evet**’i seçin. |
    |Özel DNS Bölgesi |Seç *(yeni) Privatelink.azurecr.io* |
    |||

1. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz. 
2. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin.

Özel uç nokta oluşturulduktan sonra, özel bölgedeki DNS ayarları portaldaki **Özel uç noktalar** sayfasında görünür:

1. Portalda kapsayıcı Kayıt defterinize gidin ve **ayarlar > ağ**' ı seçin.
1. **Özel uç noktalar** sekmesinde, oluşturduğunuz özel uç noktayı seçin.
1. **Genel bakış** sayfasında bağlantı ayarlarını ve özel DNS ayarlarını gözden geçirin.

  ![Uç nokta DNS ayarları](./media/container-registry-private-link/private-endpoint-overview.png)

Özel bağlantınız artık yapılandırıldı ve kullanıma hazır.

## <a name="disable-public-access"></a>Genel erişimi devre dışı bırak

Birçok senaryo için, Ortak ağlardan kayıt defteri erişimini devre dışı bırakın. Bu yapılandırma, sanal ağın dışındaki istemcilerin kayıt defteri uç noktalarına ulaşmasını engeller. 

### <a name="disable-public-access---cli"></a>Genel erişimi devre dışı bırak-CLı

Azure CLı kullanarak genel erişimi devre dışı bırakmak için [az ACR Update][az-acr-update] çalıştırın ve `--public-network-enabled` olarak ayarlayın `false` . 

> [!NOTE]
> `public-network-enabled`Bağımsız değişken Azure CLI 2.6.0 veya üstünü gerektirir. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Genel erişimi devre dışı bırak-Portal

1. Portalda kapsayıcı Kayıt defterinize gidin ve **ayarlar > ağ**' ı seçin.
1. **Genel erişim** sekmesinde, **genel ağ erişimine izin ver**' in altında **devre dışı**' yı seçin. Sonra **Kaydet**'i seçin.

## <a name="validate-private-link-connection"></a>Özel bağlantı bağlantısını doğrula

Özel uç noktanın alt ağı içindeki kaynakların, özel bir IP adresi üzerinden kayıt defterinize bağlanıp doğru özel DNS bölge tümleştirmesine sahip olduğunu doğrulamanız gerekir.

Özel bağlantı bağlantısını doğrulamak için sanal ağda ayarladığınız sanal makineye SSH.

`nslookup` `dig` Özel bağlantı üzerinden kayıt defterinizin IP adresini aramak için veya gibi bir yardımcı program çalıştırın. Örnek:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Örnek çıkış, alt ağın adres alanındaki kayıt defterinin IP adresini gösterir:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Bu sonucu `dig` , genel bir uç nokta üzerinden aynı kayıt defteri için çıkışdaki genel IP adresiyle karşılaştırın:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Özel bağlantı üzerinden kayıt defteri işlemleri

Ayrıca, alt ağdaki sanal makineden kayıt defteri işlemleri gerçekleştirebildiğinizi doğrulayın. Sanal makinenize bir SSH bağlantısı oluşturun ve kayıt defterinizde oturum açmak için [az ACR LOGIN][az-acr-login] ' i çalıştırın. VM yapılandırmanıza bağlı olarak, ile aşağıdaki komutları ön eki uygulamanız gerekebilir `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

`docker pull`Kayıt defterinden örnek bir görüntü çekmek için gibi kayıt defteri işlemleri gerçekleştirin. Kayıt defteriniz `hello-world:v1` için uygun olan bir görüntüyle ve etiketle değiştirin ve kayıt defteri oturum açma sunucusu adı (tümü küçük harf) öneki:

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker görüntüyü sanal makineye başarıyla çeker.

## <a name="manage-private-endpoint-connections"></a>Özel uç nokta bağlantılarını yönetme

Azure portal kullanarak veya [az ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] komut grubundaki komutları kullanarak bir kayıt defterinin özel uç nokta bağlantılarını yönetin. İşlemler, bir kayıt defterinin özel uç nokta bağlantılarının onaylama, silme, listeleme, reddetme veya ayrıntılarını içerir.

Örneğin, bir kayıt defterinin özel uç nokta bağlantılarını listelemek için [az ACR Private-Endpoint-Connection List][az-acr-private-endpoint-connection-list] komutunu çalıştırın. Örnek:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Bu makaledeki adımları kullanarak özel bir uç nokta bağlantısı ayarladığınızda, kayıt defteri, kayıt defterinde Azure RBAC izinleri olan istemcilerden ve hizmetlerden gelen bağlantıları otomatik olarak kabul eder. Uç noktayı bağlantıların el ile onayını gerektirecek şekilde ayarlayabilirsiniz. Özel uç nokta bağlantılarını onaylama ve reddetme hakkında daha fazla bilgi için bkz. [Özel uç nokta bağlantısını yönetme](../private-link/manage-private-endpoint.md).

> [!IMPORTANT]
> Şu anda bir özel uç noktayı bir kayıt defterinden silerseniz, sanal ağın özel bölge bağlantısını da silmeniz gerekebilir. Bağlantı silinmemişse şuna benzer bir hata görebilirsiniz: `unresolvable host` .

## <a name="dns-configuration-options"></a>DNS yapılandırma seçenekleri

Bu örnekteki özel uç nokta, temel bir sanal ağla ilişkili özel bir DNS bölgesi ile tümleşir. Bu kurulum, kayıt defterinin genel FQDN 'sini sanal ağdaki özel IP adreslerine çözümlemek için doğrudan Azure tarafından sağlanmış DNS hizmetini kullanır. 

Özel bağlantı özel DNS çözümleriyle birlikte özel bölgeyi kullanan ek DNS yapılandırma senaryolarını destekler. Örneğin, sanal ağda veya şirket içinde dağıtılan özel bir DNS çözümünüz bir VPN ağ geçidi veya Azure ExpressRoute kullanarak sanal ağa bağlanırsınız. 

Kayıt defterinin genel FQDN 'sini bu senaryolardaki özel IP adresine çözümlemek için, Azure DNS hizmetine sunucu düzeyinde bir iletici yapılandırmanız gerekir (168.63.129.16). Tam yapılandırma seçenekleri ve adımlar, mevcut ağlarınıza ve DNS 'ye bağlıdır. Örnekler için bkz. [Azure özel uç nokta DNS yapılandırması](../private-link/private-endpoint-dns.md).

> [!IMPORTANT]
> Çok sayıda bölgede özel uç noktalar oluşturduğunuz için yüksek kullanılabilirlik varsa, her bölgede ayrı bir kaynak grubu kullanmanızı ve sanal ağı ve ilişkili özel DNS bölgesini buna yerleştirmeniz önerilir. Bu yapılandırma aynı zamanda aynı özel DNS bölgesinin paylaşılarak öngörülemeyen DNS çözümlemesini de engeller.

### <a name="manually-configure-dns-records"></a>DNS kayıtlarını el ile yapılandır

Bazı senaryolarda, Azure tarafından sağlanmış özel bölgeyi kullanmak yerine, özel bir bölgedeki DNS kayıtlarını el ile yapılandırmanız gerekebilir. Aşağıdaki uç noktaların her biri için kayıt oluşturmayı unutmayın: kayıt defteri uç noktası, kayıt defteri 'nin veri uç noktası ve ek bölgesel çoğaltma için veri uç noktası. Tüm kayıtlar yapılandırılmamışsa, kayıt defterine ulaşılamıyor olabilir.

> [!IMPORTANT]
> Daha sonra yeni bir çoğaltma eklerseniz, bu bölgedeki veri uç noktası için el ile yeni bir DNS kaydı eklemeniz gerekir. Örneğin, northeurope konumunda *myregistry* 'nin bir çoğaltmasını oluşturursanız, için bir kayıt ekleyin `myregistry.northeurope.data.azurecr.io` .

DNS kayıtları oluşturmak için gereken FQDN 'Ler ve özel IP adresleri, Özel uç noktanın ağ arabirimiyle ilişkilendirilir. Bu bilgileri Azure CLı kullanarak veya portaldan edinebilirsiniz:

* Azure CLı 'yı kullanarak [az Network Nic Show][az-network-nic-show] komutunu çalıştırın. Örneğin, bu makalenin önceki bölümlerinde bulunan [ENDPOINT IP yapılandırmasını al](#get-endpoint-ip-configuration)bölümüne bakın.

* Portalda özel uç noktanıza gidin ve **DNS yapılandırması**' nı seçin.

DNS kayıtları oluşturduktan sonra, kayıt defteri FQDN 'Lerinin ilgili özel IP adreslerine doğru şekilde çözümlendiğinizden emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Tüm Azure kaynaklarını aynı kaynak grubunda oluşturduysanız ve artık gerekmiyorsa, tek bir [az Group Delete](/cli/azure/group) komutu kullanarak kaynakları isteğe bağlı olarak silebilirsiniz:

```azurecli
az group delete --name $RESOURCE_GROUP
```

Portalda kaynaklarınızı temizlemek için kaynak grubunuza gidin. Kaynak grubu yüklendikten sonra kaynak grubunu **Sil** ' e tıklayarak kaynak grubunu ve burada depolanan kaynakları kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

* Özel bağlantı hakkında daha fazla bilgi edinmek için bkz. [Azure özel bağlantı](../private-link/private-link-overview.md) belgeleri.

* Bir istemci güvenlik duvarının ardında kayıt defteri erişim kuralları ayarlamanız gerekiyorsa, bkz. [güvenlik duvarı arkasındaki bir Azure Container Registry 'ye erişmek için kuralları yapılandırma](container-registry-firewall-access-rules.md).

* [Azure Özel Uç Nokta bağlantı sorunlarını giderme](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az_acr_private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az_acr_private_endpoint_connection_approve
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az_network_private_endpoint_create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az_network_private_endpoint_show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az_network_private_dns_zone_create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_add_record
[az-network-nic-show]: /cli/azure/network/nic#az_network_nic_show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
