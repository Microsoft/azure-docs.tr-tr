---
title: Azure dosyaları ağ uç noktalarını yapılandırma | Microsoft Docs
description: Azure dosya ağı uç noktalarını yapılandırmayı öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 42e83facec7817b6588bf69977fea5ab74b6b10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759888"
---
# <a name="configuring-azure-files-network-endpoints"></a>Azure dosyaları ağ uç noktalarını yapılandırma

Azure dosyaları, Azure dosya paylaşımlarına erişmek için iki ana uç nokta türü sağlar: 
- Genel bir IP adresi olan ve dünyanın herhangi bir yerinden erişilebilen genel uç noktalar.
- Bir sanal ağ içinde bulunan ve bu sanal ağın adres alanından özel bir IP adresine sahip olan özel uç noktalar.

Azure depolama hesabında genel ve özel uç noktalar mevcuttur. Depolama hesabı, birden çok dosya paylaşımını ve BLOB kapsayıcıları ya da kuyrukları gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzunu temsil eden bir yönetim yapısıdır.

Bu makalede, Azure dosya paylaşımında doğrudan erişim için bir depolama hesabının uç noktalarının nasıl yapılandırılacağı ele alınmaktadır. Bu belgede sunulan ayrıntıların çoğu, depolama hesabının genel ve özel uç noktaları ile birlikte Azure Dosya Eşitleme için de geçerlidir. ancak, bir Azure Dosya Eşitleme dağıtımına yönelik ağ hususları hakkında daha fazla bilgi için, bkz [Azure dosya eşitleme proxy ve güvenlik duvarı ayarlarını yapılandırma](../file-sync/file-sync-firewall-and-proxy.md).

Bu nasıl yapılır kılavuzu okumadan önce [Azure dosyaları ağ oluşturma konuları](storage-files-networking-overview.md) okumanız önerilir.

## <a name="prerequisites"></a>Önkoşullar

- Bu makalede, zaten bir Azure aboneliği oluşturmuş olduğunuz varsayılmaktadır. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Bu makalede, Şirket içinden bağlamak istediğiniz bir depolama hesabında bir Azure dosya paylaşımının zaten oluşturulduğunu varsaymış olursunuz. Azure dosya paylaşımının nasıl oluşturulacağını öğrenmek için bkz. [Azure dosya paylaşma oluşturma](storage-how-to-create-file-share.md).
- Azure PowerShell kullanmayı düşünüyorsanız, [en son sürümü yükleyebilirsiniz](/powershell/azure/install-az-ps).
- Azure CLı 'yı kullanmayı planlıyorsanız [en son sürümü yükleyebilirsiniz](/cli/azure/install-azure-cli).

## <a name="endpoint-configurations"></a>Uç nokta yapılandırması

Uç noktalarınızı, depolama hesabınıza yönelik ağ erişimini kısıtlamak için yapılandırabilirsiniz. Bir depolama hesabına erişimi bir sanal ağla kısıtlamak için iki yaklaşım vardır:

- [Depolama hesabı için bir veya daha fazla özel uç nokta oluşturun](#create-a-private-endpoint)  ve tüm erişimi genel uç noktaya sınırlayın. Bu, yalnızca istenen sanal ağlardan gelen trafiğin depolama hesabı içindeki Azure dosya paylaşımlarına erişebilmesini sağlar.
- [Genel uç noktayı bir veya daha fazla sanal ağla sınırlayın](#restrict-public-endpoint-access). Bu, *hizmet uç noktaları* adlı sanal ağın bir özelliği kullanılarak işe yarar. Bir hizmet uç noktası aracılığıyla bir depolama hesabıyla trafiği kısıtladığınızda, hala genel IP adresi aracılığıyla depolama hesabına erişiyorsunuz, ancak erişim yalnızca yapılandırmanızda belirttiğiniz konumlardan alınabilir.

### <a name="create-a-private-endpoint"></a>Özel uç nokta oluşturma

Depolama hesabınız için özel bir uç nokta oluşturulması, aşağıdaki Azure kaynaklarının dağıtılmasının oluşmasına neden olur:

- **Özel uç nokta**: depolama hesabının özel uç noktasını temsil eden bir Azure kaynağı. Bunu, depolama hesabını ve ağ arabirimini bağlayan bir kaynak olarak düşünebilirsiniz.
- **Ağ arabirimi (NIC)**: belirtilen sanal ağ/alt ağ içinde özel bir IP adresi tutan ağ arabirimi. Bu, bir sanal makineyi dağıtırken dağıtılan aynı kaynaktır, ancak bir VM 'ye atanmak yerine özel uç noktaya aittir.
- **Özel BIR DNS bölgesi**: daha önce bu sanal ağ için özel bir uç nokta dağıtmadıysanız, sanal ağınız için yeni BIR özel DNS bölgesi dağıtılır. DNS A kaydı, bu DNS bölgesindeki depolama hesabı için de oluşturulacaktır. Bu sanal ağda zaten özel bir uç nokta dağıttıysanız, var olan DNS bölgesine depolama hesabı için yeni bir kayıt eklenecektir. Bir DNS bölgesinin dağıtımı isteğe bağlıdır, ancak Azure dosya paylaşımlarınızı bir AD hizmet sorumlusu ile veya FileREST API 'sini kullanarak oluşturursanız gereklidir.

> [!Note]  
> Bu makalede, Azure ortak bölgeleri için depolama hesabı DNS son eki kullanılmaktadır `core.windows.net` . Bu yorum, Azure ABD kamu bulutu ve Azure Çin bulutu gibi Azure Sogeign bulutları için de geçerlidir. ortamınız için uygun son ekleri yerine koyun. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>Bağlantıyı doğrulama

# <a name="portal"></a>[Portal](#tab/azure-portal)

Sanal ağınızın içinde bir sanal makineniz varsa veya [Azure dosyaları IÇIN DNS Iletmeyi yapılandırma](storage-files-networking-dns.md)bölümünde AÇıKLANDıĞı gibi DNS iletmeyi yapılandırdıysanız, PowerShell, komut satırı veya terminalden (Windows, Linux veya MacOS için çalışır) aşağıdaki komutları çalıştırarak özel uç noktanızın doğru şekilde ayarlandığını test edebilirsiniz. `<storage-account-name>`Uygun depolama hesabı adıyla değiştirmeniz gerekir:

```
nslookup <storage-account-name>.file.core.windows.net
```

Her şey başarıyla çalıştıysa aşağıdaki çıktıyı görmeniz gerekir, burada `192.168.0.5` sanal ağınızdaki özel uç noktanın özel IP adresidir (Windows için gösterilen çıktı):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sanal ağınızın içinde bir sanal makineniz varsa veya [Azure dosyaları IÇIN DNS Iletmeyi yapılandırma](storage-files-networking-dns.md)bölümünde AÇıKLANDıĞı gibi DNS iletmeyi yapılandırdıysanız, Özel uç noktanızın aşağıdaki komutlarla doğru şekilde ayarlandığını test edebilirsiniz:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Her şey başarıyla çalıştıysa aşağıdaki çıktıyı görmeniz gerekir, burada `192.168.0.5` sanal ağınızdaki özel uç noktanın özel IP adresidir:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Sanal ağınızın içinde bir sanal makineniz varsa veya [Azure dosyaları IÇIN DNS Iletmeyi yapılandırma](storage-files-networking-dns.md)bölümünde AÇıKLANDıĞı gibi DNS iletmeyi yapılandırdıysanız, Özel uç noktanızın aşağıdaki komutlarla doğru şekilde ayarlandığını test edebilirsiniz:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Her şey başarıyla çalıştıysa aşağıdaki çıktıyı görmeniz gerekir, burada `192.168.0.5` sanal ağınızdaki özel uç noktanın özel IP adresidir. Yol yerine dosya paylaşımınızı bağlamak için yine de storageaccount.file.core.windows.net kullanmanız gerekir `privatelink` .

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```
---

## <a name="restrict-public-endpoint-access"></a>Genel uç nokta erişimini kısıtlama

Genel uç nokta erişimini sınırlamak için öncelikle genel uç noktaya genel erişimi devre dışı bırakmanız gerekir. Genel uç noktaya erişimi devre dışı bırakmak özel uç noktaları etkilemez. Genel uç nokta devre dışı bırakıldıktan sonra, erişmeye devam edecek belirli ağları veya IP adreslerini seçebilirsiniz. Genellikle, bir depolama hesabı için güvenlik duvarı ilkelerinin çoğu, ağ erişimini bir veya daha fazla sanal ağa kısıtlar.

#### <a name="disable-access-to-the-public-endpoint"></a>Genel uç noktaya erişimi devre dışı bırak

Genel uç noktaya erişim devre dışı bırakıldığında, depolama hesabına hala özel uç noktalar aracılığıyla erişilebilir. Aksi takdirde, [özel olarak izin verilen bir kaynaktan](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks)olmadıkları müddetçe depolama hesabının genel uç noktasına yönelik geçerli istekler reddedilir. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Genel uç noktaya erişimi belirli sanal ağlara kısıtlama

Depolama hesabını belirli sanal ağlarla kısıtladığınızda, belirtilen sanal ağların içinden genel uç noktaya yönelik isteklere izin vermiş olursunuz. Bu, *hizmet uç noktaları* adlı sanal ağın bir özelliği kullanılarak işe yarar. Bu, Özel uç noktalarla veya olmadan kullanılabilir.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Ayrıca bkz.

- [Azure dosyaları ağ iletişimi konuları](storage-files-networking-overview.md)
- [Azure Dosyalar için DNS iletmeyi yapılandırma](storage-files-networking-dns.md)
- [Azure dosyaları için S2S VPN 'i yapılandırma](storage-files-configure-s2s-vpn.md)