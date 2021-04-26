---
title: Azure HDInsight 'ta ağ sanal gereç yapılandırma
description: Azure HDInsight 'ta ağ sanal gereç için birkaç ek özellik yapılandırmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 7fe266c3c7b75762133fca4645e0675845c28972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943971"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Azure HDInsight 'ta ağ sanal gereç yapılandırma

> [!Important]
> Aşağıdaki bilgiler **yalnızca** [Azure Güvenlik Duvarı](./hdinsight-restrict-outbound-traffic.md)dışında bir ağ sanal gereci (NVA) yapılandırmak istiyorsanız gereklidir.

Azure Güvenlik Duvarı FQDN etiketi, yaygın olarak karşılaşılan birçok önemli FQDN için trafiğe izin verecek şekilde otomatik olarak yapılandırılır. Başka bir ağ sanal gerecinin kullanılması, birkaç ek özellik yapılandırmanızı gerektirir. Ağ sanal gerecinizi yapılandırırken aşağıdaki faktörleri göz önünde bulundurun:

* Hizmet uç noktası özellikli Hizmetleri, genellikle maliyet veya performans konuları için NVA 'nın atlanmasına neden olan hizmet uç noktaları ile yapılandırılabilir.
* Resourceproviderconnection *giden* olarak ayarlandıysa, depolama ve SQL Server 'lar için özel uç noktaları, meta depolar için kullanabilirsiniz ve bunları NVA 'ya eklemeniz gerekmez.
* IP adresi bağımlılıkları HTTP/sn olmayan trafiğe yöneliktir (TCP ve UDP trafiği).
* FQDN HTTP/HTTPS uç noktaları, NVA cihazınızda onaylanabilir.
* Oluşturduğunuz yol tablosunu HDInsight alt ağına atayın.

## <a name="service-endpoint-capable-dependencies"></a>Hizmet uç noktası özellikli bağımlılıklar

İsteğe bağlı olarak, aşağıdaki hizmet uç noktalarından birini veya daha fazlasını etkinleştirerek, NVA 'yi atlayarak elde edebilirsiniz. Bu seçenek, maliyetten tasarruf etmek için büyük miktarlarda veri aktarımı ve ayrıca performans iyileştirmeleri için yararlı olabilir. 

| **Uç Nokta** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP adresi bağımlılıkları

| **Uç Nokta** | **Ayrıntılar** |
|---|---|
| [Burada](hdinsight-management-ip-addresses.md) Yayınlanan IP 'ler | Bu IP 'Ler HDInsight kaynak sağlayıcısına yöneliktir ve asimetrik yönlendirmeyi önlemek için UDR 'ye eklenmelidir. Bu kural yalnızca ResourceProviderConnection *gelen* olarak ayarlandıysa gereklidir. ResourceProviderConnection *giden* olarak ayarlandıysa, bu IP 'ler UDR 'de gerekli değildir.  |
| AAD-DS özel IP 'Leri | Yalnızca, sanal ağlar eşlenirse, ESP kümeleri için gereklidir.|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS bağımlılıkları

[Bu](https://github.com/Azure-Samples/hdinsight-fqdn-lists/)depoda NVA 'nizi yapılandırmak için FQDN bağımlılıklarının (çoğunlukla Azure Storage ve Azure Service Bus) listesini alabilirsiniz. Bölgesel liste için [buraya](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional)bakın. Bu bağımlılıklar, HDInsight kaynak sağlayıcısı (RP) tarafından kümeleri oluşturmak ve izlemek/yönetmek için kullanılır. Bunlar telemetri/tanılama günlüklerini, sağlama meta verilerini, kümeyle ilgili konfigürasyonları, betikleri vb. içerir. Bu FQDN bağımlılık listesi, gelecekteki HDInsight güncelleştirmeleriyle serbest bırakılması ile değişebilir.

Aşağıdaki liste, küme oluşturulduktan ve küme işlemlerinin ömrü boyunca işletim sistemi ve güvenlik düzeltme ekleri veya *sertifika doğrulamaları için* gerekli olabilecek birkaç FQDN sağlar:

| **Çalışma zamanı bağımlılıkları FQDN 'leri**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Sonraki adımlar

* [Giden trafiği kısıtlamak için güvenlik duvarı kullanma](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight sanal ağ mimarisi](hdinsight-virtual-network-architecture.md)
