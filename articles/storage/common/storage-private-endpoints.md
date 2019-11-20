---
title: Azure Storage ile özel uç noktaları kullanma | Microsoft Docs
description: Sanal ağlardan depolama hesaplarına güvenli erişim için özel uç noktalara genel bakış.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb1f8a1d1f8e1ebbaf3e0e9fe96e3c1bf0ba9ba6
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078764"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Azure depolama için özel uç noktaları kullanma (Önizleme)

Bir sanal ağdaki (VNet) istemcilerin [özel bir bağlantı](../../private-link/private-link-overview.md)üzerinden güvenli bir şekilde verilere erişmesine izin vermek Için, Azure depolama hesaplarınız Için [Özel uç noktaları](../../private-link/private-endpoint-overview.md) kullanabilirsiniz. Özel uç nokta, depolama hesabı hizmetiniz için VNet adres alanından bir IP adresi kullanır. VNet ve depolama hesabı arasındaki ağ trafiği, VNet üzerinden ve Microsoft omurga ağındaki özel bir bağlantı üzerinden, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır.

Depolama hesabınız için özel uç noktalar kullanmak şunları yapmanızı sağlar:
- Depolama hizmeti için genel uç noktasındaki tüm bağlantıları engelleyecek şekilde depolama hesabınızı güvenli hale getirerek depolama hesabınızın güvenliğini sağlayın.
- Sanal ağ (VNet) için güvenliği artırarak VNet 'ten veri alımını engelleyebilirsiniz.
- [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../../expressroute/expressroute-locations.md) Ile özel eşleme ile sanal ağa bağlanan şirket içi ağlardan depolama hesaplarına güvenli bir şekilde bağlanın.

## <a name="conceptual-overview"></a>Kavramsal genel bakış
![Azure depolama için özel uç noktalara genel bakış](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Özel uç nokta, [sanal ağınızdaki](../../virtual-network/virtual-networks-overview.md) (VNet) bir Azure hizmeti için özel bir ağ arabirimidir. Depolama hesabınız için özel bir uç nokta oluşturduğunuzda, VNet 'iniz ve depolama ağınızdaki istemciler arasında güvenli bağlantı sağlar. Özel uç noktaya sanal Ağınızın IP adresi aralığından bir IP adresi atanır. Özel uç nokta ve depolama hizmeti arasındaki bağlantı güvenli bir özel bağlantı kullanır.

VNet 'teki uygulamalar, **diğer durumlarda kullandıkları aynı bağlantı dizelerini ve yetkilendirme mekanizmalarını kullanarak**, Özel uç nokta üzerinden depolama hizmetine sorunsuz bir şekilde bağlanabilir. Özel uç noktalar, REST ve SMB dahil olmak üzere depolama hesabı tarafından desteklenen tüm protokollerle kullanılabilir.

VNet 'iniz içindeki bir depolama hizmeti için özel bir uç nokta oluşturduğunuzda, depolama hesabı sahibine onay için bir izin isteği gönderilir. Özel uç noktanın oluşturulmasını isteyen kullanıcı aynı zamanda depolama hesabının sahibiyseniz, bu onay isteği otomatik olarak onaylanır.

Depolama hesabı sahipleri, [Azure Portal](https://portal.azure.com)depolama hesabı Için '*Özel uç noktalar*' sekmesi aracılığıyla izin isteklerini ve özel uç noktaları yönetebilir.

> [!TIP]
> Yalnızca özel uç nokta aracılığıyla depolama hesabınıza erişimi kısıtlamak istiyorsanız, depolama güvenlik duvarını genel uç nokta aracılığıyla tüm erişimi reddedecek şekilde yapılandırın.

Depolama hesabınızı, varsayılan olarak genel bitiş noktası üzerinden erişimi reddedecek şekilde [yapılandırarak](storage-network-security.md#change-the-default-network-access-rule) , depolama hesabınızı yalnızca sanal ağınızdan gelen bağlantıları kabul edecek şekilde güvenli hale getirebilirsiniz. Depolama güvenlik duvarı yalnızca genel uç nokta üzerinden erişimi denetlemediğinden, özel bir uç noktası olan VNet 'ten gelen trafiğe izin vermek için bir güvenlik duvarı kuralına gerek yoktur. Bunun yerine özel uç noktalar, depolama hizmetine alt ağ erişimi sağlamak için izin akışını kullanır.

### <a name="private-endpoints-for-storage-service"></a>Depolama hizmeti için özel uç noktalar

Özel uç nokta oluştururken, bağlandığı depolama hesabını ve depolama hizmetini belirtmeniz gerekir. Her depolama hizmeti için, [BLOB](../blobs/storage-blobs-overview.md), [Data Lake Storage 2.](../blobs/data-lake-storage-introduction.md), [Dosya](../files/storage-files-introduction.md), [kuyruk](../queues/storage-queues-introduction.md), [tablo](../tables/table-storage-overview.md)veya [statik Web siteleri](../blobs/storage-blob-static-website.md)olmak üzere erişmeniz gereken bir depolama hesabında ayrı bir özel uç noktaya ihtiyacınız vardır.

> [!TIP]
> RA-GRS hesaplarında daha iyi okuma performansı için depolama hizmetinin ikincil örneği için ayrı bir özel uç nokta oluşturun.

[Okuma Erişimli Coğrafi olarak yedekli depolama hesabında](storage-redundancy-grs.md#read-access-geo-redundant-storage)okuma kullanılabilirliği için, hizmetin birincil ve ikincil örneklerinde ayrı özel uç noktalara ihtiyacınız vardır. **Yük devretme**için ikincil örnek için özel bir uç nokta oluşturmanız gerekmez. Özel uç nokta, yük devretmeden sonra otomatik olarak yeni birincil örneğe bağlanır.

#### <a name="resources"></a>Kaynaklar

Depolama hesabınız için özel bir uç nokta oluşturma hakkında daha ayrıntılı bilgi için aşağıdaki makalelere bakın:

- [Azure portal depolama hesabı deneyiminden bir depolama hesabına özel olarak bağlanma](../../private-link/create-private-endpoint-storage-portal.md)
- [Azure portal özel bağlantı merkezini kullanarak özel bir uç nokta oluşturma](../../private-link/create-private-endpoint-portal.md)
- [Azure CLı kullanarak özel uç nokta oluşturma](../../private-link/create-private-endpoint-cli.md)
- [Azure PowerShell kullanarak özel uç nokta oluşturma](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>Özel uç noktalar için DNS değişiklikleri

Bir sanal ağ üzerindeki istemcilerin, özel bir uç nokta kullanılırken bile depolama hesabı için aynı bağlantı dizesini kullanması gerekir.

Özel bir uç nokta oluşturduğunuzda, bu depolama uç noktası için DNS CNAME kaynak kaydını, '*Privatelink*' önekine sahip bir alt etki alanındaki diğer adla güncelleştiririz. Varsayılan olarak, sanal ağa bağlı [Özel BIR DNS bölgesi](../../dns/private-dns-overview.md) de oluşturacağız. Bu özel DNS bölgesi, '*Privatelink*' ön ekine sahip alt etki alanına karşılık gelir ve özel uç noktalar Için DNS A kaynak kayıtlarını içerir.

Depolama uç noktası URL 'sini VNet dışından özel uç noktayla çözdüğünde, depolama hizmetinin genel uç noktasına dönüşür. Özel uç noktayı barındıran VNet 'ten çözümlendiğinde, depolama uç noktası URL 'SI özel uç noktanın IP adresine çözümlenir.

Yukarıdaki gösterilen örnek için, Özel uç noktayı barındıran VNet dışından çözümlendiğinde ' StorageAccountA ' depolama hesabı için DNS kaynak kayıtları şu şekilde olur:

| Ad                                                  | Tür  | Değer                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<depolama hizmeti genel uç noktası\>                   |
| \<depolama hizmeti genel uç noktası\>                   | A     | \<depolama hizmeti genel IP adresi\>                 |

Daha önce belirtildiği gibi, depolama güvenlik duvarını kullanarak genel uç nokta aracılığıyla tüm erişimi reddedebilirsiniz.

Özel uç noktasını barındıran VNet 'teki bir istemci tarafından çözümlendiğinde StorageAccountA için DNS kaynak kayıtları şu şekilde olur:

| Ad                                                  | Tür  | Değer                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Bu yaklaşım, Özel uç noktaları barındıran VNet 'ten **aynı bağlantı dizesinin** yanı sıra VNET dışındaki istemcileri kullanarak depolama hesabına erişim sağlar. VNet dışındaki tüm istemcilere erişimi reddetmek için depolama güvenlik duvarını kullanabilirsiniz.

> [!IMPORTANT]
> Diğer durumlarda kullandığınız gibi, Özel uç noktalar üzerinden depolama hesabına bağlanmak için aynı bağlantı dizesini kullanın. Lütfen '*Privatelink*' alt etki alanı URL 'sini kullanarak depolama hesabına bağlanmayın.

> [!TIP]
> Özel veya şirket içi bir DNS sunucusu kullanırken, depolama hizmetinin ' privatelınk ' alt etki alanına karşılık gelen bir DNS bölgesindeki Özel uç noktalar için DNS kaynak kayıtlarını yapılandırmalısınız.

Depolama Hizmetleri için özel uç noktalar için önerilen DNS bölge adları şunlardır:

| Depolama hizmeti        | Bölge adı                            |
| :--------------------- | :----------------------------------- |
| Blob hizmeti           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Dosya hizmeti           | `privatelink.file.core.windows.net`  |
| Kuyruk hizmeti          | `privatelink.queue.core.windows.net` |
| Tablo hizmeti          | `privatelink.table.core.windows.net` |
| Statik Web siteleri        | `privatelink.web.core.windows.net`   |

## <a name="pricing"></a>Fiyatlandırma

Fiyatlandırma ayrıntıları için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Bilinen Sorunlar

### <a name="copy-blob-support"></a>Blob 'U kopyalama desteği

Önizleme sırasında, kaynak depolama hesabı bir güvenlik duvarı tarafından korunduğunda özel uç noktalar aracılığıyla erişilen depolama hesaplarına verilen [kopyalama blobu](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) komutlarını desteklemiyoruz.

### <a name="subnets-with-service-endpoints"></a>Hizmet uç noktaları içeren alt ağlar
Şu anda, hizmet uç noktalarına sahip bir alt ağda özel bir uç nokta oluşturamazsınız. Geçici bir çözüm olarak, hizmet uç noktaları ve özel uç noktalar için aynı VNet 'te ayrı alt ağlar oluşturabilirsiniz.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Özel uç noktalarla VNET 'lerdeki istemciler için depolama erişimi kısıtlamaları

Özel uç noktaları olan diğer depolama hesaplarına erişirken, mevcut özel uç noktalar ile VNET 'lerdeki istemciler. Örneğin, bir VNet N1 'nin bir depolama hesabı için a1, blob hizmeti için özel bir uç noktası olduğunu varsayalım. A2 depolama hesabı, blob hizmeti için bir VNet için bir özel uç nokta içeriyorsa, VNet N1 içindeki istemciler de özel bir uç nokta kullanarak a2 hesabının blob hizmetine da erişir. A2 depolama hesabı, blob hizmeti için özel uç nokta içermiyorsa, VNet N1 içindeki istemciler blob hizmetine özel bir uç nokta olmadan erişebilir.

Bu kısıtlama, a2 hesabı özel bir uç nokta oluşturduğunda yapılan DNS değişikliklerinin bir sonucudur.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Özel uç noktaları olan alt ağlar için ağ güvenlik grubu kuralları

Şu anda, Özel uç noktaları olan alt ağlar için [ağ güvenlik grubu](../../virtual-network/security-overview.md) (NSG) kurallarını yapılandıramazsınız. Bu sorun için sınırlı bir geçici çözüm, kaynak alt ağlardaki özel uç noktalar için erişim kurallarınızı uygulamaktır, ancak bu yaklaşım daha yüksek bir yönetim yükü gerektirebilir.
