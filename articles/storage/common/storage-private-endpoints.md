---
title: Özel uç noktaları kullanma
titleSuffix: Azure Storage
description: Sanal ağlardan depolama hesaplarına güvenli erişim için özel uç noktalara genel bakış.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3fcc58f626622bcc728265e782906226859e1bf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600471"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Azure depolama için özel uç noktaları kullanma

Bir sanal ağdaki (VNet) istemcilerin [özel bir bağlantı](../../private-link/private-link-overview.md)üzerinden güvenli bir şekilde verilere erişmesine izin vermek Için, Azure depolama hesaplarınız için [Özel uç noktaları](../../private-link/private-endpoint-overview.md) kullanabilirsiniz. Özel uç nokta, depolama hesabı hizmetiniz için VNet adres alanından bir IP adresi kullanır. VNet ve depolama hesabı arasındaki ağ trafiği, VNet üzerinden ve Microsoft omurga ağındaki özel bir bağlantı üzerinden, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır.

Depolama hesabınız için özel uç noktalar kullanmak şunları yapmanızı sağlar:

- Depolama hizmeti için genel uç noktasındaki tüm bağlantıları engelleyecek şekilde depolama hesabınızı güvenli hale getirerek depolama hesabınızın güvenliğini sağlayın.
- Sanal ağ (VNet) için güvenliği artırarak VNet 'ten veri alımını engelleyebilirsiniz.
- [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../../expressroute/expressroute-locations.md) Ile özel eşleme ile sanal ağa bağlanan şirket içi ağlardan depolama hesaplarına güvenli bir şekilde bağlanın.

## <a name="conceptual-overview"></a>Kavramsal genel bakış

![Azure depolama için özel uç noktalara genel bakış](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Özel uç nokta, [sanal ağınızdaki](../../virtual-network/virtual-networks-overview.md) (VNet) bir Azure hizmeti için özel bir ağ arabirimidir. Depolama hesabınız için özel bir uç nokta oluşturduğunuzda, VNet 'iniz ve depolama ağınızdaki istemciler arasında güvenli bağlantı sağlar. Özel uç noktaya sanal Ağınızın IP adresi aralığından bir IP adresi atanır. Özel uç nokta ve depolama hizmeti arasındaki bağlantı güvenli bir özel bağlantı kullanır.

VNet 'teki uygulamalar, **diğer durumlarda kullandıkları aynı bağlantı dizelerini ve yetkilendirme mekanizmalarını kullanarak**, Özel uç nokta üzerinden depolama hizmetine sorunsuz bir şekilde bağlanabilir. Özel uç noktalar, REST ve SMB dahil olmak üzere depolama hesabı tarafından desteklenen tüm protokollerle kullanılabilir.

Özel uç noktalar, [hizmet uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md)kullanan alt ağlarda oluşturulabilir. Bu nedenle, bir alt ağdaki istemciler, hizmet uç noktalarını kullanarak diğerlerine erişmek için özel uç nokta kullanarak bir depolama hesabına bağlanabilir.

Sanal ağınızda depolama hizmeti için bir özel uç nokta oluşturduğunuzda depolama hesabı sahibine onaylaması için bir onay isteği gönderilir. Özel uç noktanın oluşturulmasını isteyen kullanıcı aynı zamanda depolama hesabının sahibiyseniz, bu onay isteği otomatik olarak onaylanır.

Depolama hesabı sahipleri, [Azure Portal](https://portal.azure.com)depolama hesabı Için '*Özel uç noktalar*' sekmesi aracılığıyla izin isteklerini ve özel uç noktaları yönetebilir.

> [!TIP]
> Yalnızca özel uç nokta aracılığıyla depolama hesabınıza erişimi kısıtlamak istiyorsanız, depolama güvenlik duvarını genel uç nokta üzerinden erişimi reddedecek veya denetlemeye yönelik şekilde yapılandırın.

Depolama hesabınızı, varsayılan olarak genel bitiş noktası üzerinden erişimi reddedecek şekilde [yapılandırarak](storage-network-security.md#change-the-default-network-access-rule) , depolama hesabınızı yalnızca sanal ağınızdan gelen bağlantıları kabul edecek şekilde güvenli hale getirebilirsiniz. Depolama güvenlik duvarı yalnızca genel uç nokta üzerinden erişimi denetlemediğinden, özel bir uç noktası olan VNet 'ten gelen trafiğe izin vermek için bir güvenlik duvarı kuralına gerek yoktur. Bunun yerine özel uç noktalar, depolama hizmetine alt ağ erişimi sağlamak için izin akışını kullanır.

> [!NOTE]
> Blob 'ları depolama hesapları arasında kopyalarken, istemciniz her iki hesaba da ağ erişimine sahip olmalıdır. Bu nedenle, yalnızca bir hesap (kaynak veya hedef) için özel bir bağlantı kullanmayı tercih ederseniz, istemcinizin diğer hesaba ağ erişimi olduğundan emin olun. Ağ erişimini yapılandırmanın diğer yolları hakkında bilgi edinmek için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](storage-network-security.md?toc=/azure/storage/blobs/toc.json). 

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>Özel uç nokta oluşturma

Azure portalını kullanarak özel bir uç nokta oluşturmak için, bkz. [Azure Portal depolama hesabı deneyiminden bir depolama hesabına özel olarak bağlanma](../../private-link/tutorial-private-endpoint-storage-portal.md).

PowerShell veya Azure CLı kullanarak özel bir uç nokta oluşturmak için, Bu makalelerden birine bakın. Her ikisi de hedef hizmet olarak bir Azure Web uygulaması özelliğine sahiptir, ancak bir Azure depolama hesabı için özel bir bağlantı oluşturma adımları aynıdır.

- [Azure CLı kullanarak özel uç nokta oluşturma](../../private-link/create-private-endpoint-cli.md)

- [Azure PowerShell kullanarak özel uç nokta oluşturma](../../private-link/create-private-endpoint-powershell.md)



Özel bir uç nokta oluşturduğunuzda, bağlandığı depolama hesabı ve depolama hizmetini belirtmeniz gerekir. 

Erişmeniz gereken her depolama kaynağı için [BLOB](../blobs/storage-blobs-overview.md), [Data Lake Storage 2.](../blobs/data-lake-storage-introduction.md), [Dosya](../files/storage-files-introduction.md), [kuyruk](../queues/storage-queues-introduction.md), [tablo](../tables/table-storage-overview.md)veya [statik Web siteleri](../blobs/storage-blob-static-website.md)için ayrı bir özel uç nokta gerekir. Özel uç noktada, bu depolama hizmetleri ilişkili depolama hesabının **hedef alt kaynağı** olarak tanımlanmıştır. 

Data Lake Storage 2. depolama kaynağı için özel bir uç nokta oluşturursanız, blob Storage kaynağı için de bir tane oluşturmanız gerekir. Bunun nedeni, Data Lake Storage 2. uç noktasını hedefleyen işlemler blob uç noktasına yeniden yönlendirilebilir. Her iki kaynak için de özel bir uç nokta oluşturarak işlemlerin başarıyla tamamlanmasını sağlayabilirsiniz.

> [!TIP]
> RA-GRS hesaplarında daha iyi okuma performansı için depolama hizmetinin ikincil örneği için ayrı bir özel uç nokta oluşturun.
> Genel amaçlı v2 (Standart veya Premium) depolama hesabı oluşturduğunuzdan emin olun.

Coğrafi olarak yedekli depolama için yapılandırılmış bir depolama hesabıyla ikincil bölgeye okuma erişimi için, hizmetin birincil ve ikincil örnekleri için ayrı özel uç noktalara ihtiyacınız vardır. **Yük devretme** için ikincil örnek için özel bir uç nokta oluşturmanız gerekmez. Özel uç nokta, yük devretmeden sonra otomatik olarak yeni birincil örneğe bağlanır. Depolama artıklığı seçenekleri hakkında daha fazla bilgi için bkz. [Azure depolama artıklığı](storage-redundancy.md).

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>Özel bir uç noktaya bağlanma

Özel uç nokta kullanan bir sanal ağdaki istemciler, genel uç noktaya bağlanan istemciler olarak depolama hesabı için aynı bağlantı dizesini kullanmalıdır. VNet 'ten gelen bağlantıları özel bir bağlantı üzerinden depolama hesabına otomatik olarak yönlendirmek için DNS çözümünden yararlanıyoruz.

> [!IMPORTANT]
> Başka türlü kullandığınızda, Özel uç noktaları kullanarak depolama hesabına bağlanmak için aynı bağlantı dizesini kullanın. Lütfen alt `privatelink` etki alanı URL 'sini kullanarak depolama hesabına bağlanmayın.

Varsayılan olarak, Özel uç noktalara yönelik gerekli güncelleştirmelerle VNet 'e bağlı [Özel BIR DNS bölgesi](../../dns/private-dns-overview.md) oluşturacağız. Ancak, kendi DNS sunucunuzu kullanıyorsanız, DNS yapılandırmanızda ek değişiklikler yapmanız gerekebilir. Aşağıdaki [DNS değişikliklerinin](#dns-changes-for-private-endpoints) bölümünde, Özel uç noktalar için gereken güncelleştirmeler açıklanmaktadır.

## <a name="dns-changes-for-private-endpoints"></a>Özel uç noktalar için DNS değişiklikleri

Özel bir uç nokta oluşturduğunuzda, depolama hesabı için DNS CNAME kaynak kaydı, ön ek içeren bir alt etki alanındaki diğer ada güncelleştirilir `privatelink` . Varsayılan olarak, [](../../dns/private-dns-overview.md) `privatelink` Özel uç noktalar için DNS a kaynak kayıtlarıyla birlikte alt etki alanına karşılık gelen özel bir DNS bölgesi de oluşturacağız.

Depolama uç noktası URL 'sini VNet dışından özel uç noktayla çözdüğünde, depolama hizmetinin genel uç noktasına dönüşür. Özel uç noktayı barındıran VNet 'ten çözümlendiğinde, depolama uç noktası URL 'SI özel uç noktanın IP adresine çözümlenir.

Yukarıdaki gösterilen örnek için, Özel uç noktayı barındıran VNet dışından çözümlendiğinde ' StorageAccountA ' depolama hesabı için DNS kaynak kayıtları şu şekilde olur:

| Ad                                                  | Tür  | Değer                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

Daha önce belirtildiği gibi, depolama güvenlik duvarını kullanarak genel uç nokta aracılığıyla VNet dışındaki istemciler için erişimi reddedebilir veya kontrol edebilirsiniz.

Özel uç noktasını barındıran VNet 'teki bir istemci tarafından çözümlendiğinde StorageAccountA için DNS kaynak kayıtları şu şekilde olur:

| Ad                                                  | Tür  | Değer                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Bu yaklaşım, Özel uç noktaları barındıran VNet 'teki istemciler ve VNet dışındaki istemciler için **aynı bağlantı dizesini kullanarak** depolama hesabına erişim sağlar.

Ağınızda özel bir DNS sunucusu kullanıyorsanız, istemciler depolama hesabı uç noktası için FQDN 'yi özel uç nokta IP adresine çözümleyebilmelidir. DNS sunucunuzu özel bağlantı alt etki alanınızı, sanal ağın özel DNS bölgesine devretmek üzere yapılandırmanız veya '*StorageAccountA.Privatelink.blob.Core.Windows.net*' için bir kayıtları özel uç nokta IP adresi ile yapılandırmanız gerekir.

> [!TIP]
> Özel veya şirket içi bir DNS sunucusu kullanırken, DNS sunucunuzu, alt etki alanındaki depolama hesabı adını `privatelink` Özel uç nokta IP adresine çözümlemek üzere yapılandırmalısınız. Bunu, alt `privatelink` etki alanını VNET 'in özel DNS bölgesine devredererek veya DNS SUNUCUNUZDA DNS bölgesini yapılandırarak ve DNS A kayıtlarını ekleyerek yapabilirsiniz.

Depolama Hizmetleri için özel uç noktalar için önerilen DNS bölge adları ve ilişkili uç nokta hedefi alt kaynakları şunlardır:

| Depolama hizmeti        | Hedef alt kaynak | Bölge adı                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Blob hizmeti           | blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage 2. Nesil | f                 | `privatelink.dfs.core.windows.net`   |
| Dosya hizmeti           |  dosyası                | `privatelink.file.core.windows.net`  |
| Kuyruk hizmeti          | kuyruk               | `privatelink.queue.core.windows.net` |
| Tablo hizmeti          | tablo               | `privatelink.table.core.windows.net` |
| Statik Web siteleri        | web                 | `privatelink.web.core.windows.net`   |

Kendi DNS sunucunuzu özel uç noktaları destekleyecek şekilde yapılandırma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure sanal ağlarındaki kaynaklar için ad çözümlemesi](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Özel uç noktalar için DNS yapılandırması](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>Fiyatlandırma

Fiyatlandırma ayrıntıları için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Bilinen Sorunlar

Azure depolama için özel uç noktalar hakkında aşağıdaki bilinen sorunları göz önünde bulundurun.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Özel uç noktalarla VNET 'lerdeki istemciler için depolama erişimi kısıtlamaları

Özel uç noktaları olan diğer depolama hesaplarına erişirken, mevcut özel uç noktalar ile VNET 'lerdeki istemciler. Örneğin, VNet N1 'ın blob depolaması için a1 depolama hesabı için özel bir uç noktası olduğunu varsayalım. A2 depolama hesabı, blob depolaması için bir sanal ağ için özel bir uç nokta içeriyorsa, VNet N1 içindeki istemciler de özel bir uç nokta kullanılarak a2 hesabındaki blob depolamaya da erişemez. A2 depolama hesabı, BLOB depolama için özel uç nokta içermiyorsa, VNet N1 içindeki istemciler bu hesaptaki blob depolamaya özel bir uç nokta olmadan erişebilir.

Bu kısıtlama, a2 hesabı özel bir uç nokta oluşturduğunda yapılan DNS değişikliklerinin bir sonucudur.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Özel uç noktaları olan alt ağlar için Ağ Güvenlik Grubu kuralları

Şu anda, [ağ güvenlik grubu](../../virtual-network/network-security-groups-overview.md) (NSG) kurallarını ve özel uç noktalar için Kullanıcı tanımlı yolları yapılandıramazsınız. Özel uç noktasını barındıran alt ağa uygulanan NSG kuralları özel uç noktaya uygulanmaz. Bunlar yalnızca diğer uç noktalara uygulanır (örneğin: ağ arabirim denetleyicileri). Bu sorun için sınırlı bir geçici çözüm, kaynak alt ağlardaki özel uç noktalar için erişim kurallarınızı uygulamaktır, ancak bu yaklaşım daha yüksek bir yönetim yükü gerektirebilir.

### <a name="copying-blobs-between-storage-accounts"></a>Blob 'ları depolama hesapları arasında kopyalama

Yalnızca Azure REST API veya REST API kullanan araçları kullandığınızda özel uç noktaları kullanarak Blobları depolama hesapları arasında kopyalayabilirsiniz. Bu araçlar AzCopy, Depolama Gezgini, Azure PowerShell, Azure CLı ve Azure Blob Storage SDK 'larını içerir. 

Yalnızca BLOB depolama kaynağını hedefleyen özel uç noktalar desteklenir. Data Lake Storage 2. veya dosya kaynağını hedefleyen özel uç noktalar henüz desteklenmiyor. Ayrıca, ağ dosya sistemi (NFS) protokolü kullanılarak depolama hesapları arasında kopyalama henüz desteklenmemektedir. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](storage-network-security.md)
- [BLOB depolama için güvenlik önerileri](../blobs/security-recommendations.md)
