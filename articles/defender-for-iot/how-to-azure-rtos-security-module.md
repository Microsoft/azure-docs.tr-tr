---
title: Azure RTOS için Defender-IoT-Micro-Agent 'ı yapılandırma ve özelleştirme
description: Azure RTOS için Defender-IoT-mikro-Agent uygulamanızı yapılandırma ve özelleştirme hakkında bilgi edinin.
ms.topic: how-to
ms.date: 03/07/2021
ms.openlocfilehash: afab823b6bb187c9a7b7529f52efc37b20e8c66f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778993"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-preview"></a>Azure RTOS için Defender-IoT-mikro-Agent 'ı yapılandırma ve özelleştirme (Önizleme)

Bu makalede, Azure RTOS cihazınız için Defender-IoT-mikro-Agent ' ı ağınızı, bant genişliğini ve bellek gereksinimlerinizi karşılayacak şekilde yapılandırma açıklanmaktadır.

Dizininden uzantısı olan bir hedef dağıtım dosyası seçmeniz gerekir `*.dist` `netxduo/addons/azure_iot/azure_iot_security_module/configs` .  

CMake derleme ortamı kullanırken, seçilen değer için bir komut satırı parametresi ayarlamanız gerekir `IOT_SECURITY_MODULE_DIST_TARGET` . Örneğin, `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

Bir ıAR veya başka bir CMake derleme ortamında, `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` yolu bilinen herhangi bir dahil edilen yola eklemeniz gerekir. Örneğin, `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Cihazınızın davranışını yapılandırmak için aşağıdaki dosyayı kullanın.

**netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/ \<target distribution> /asc_config. h**

CMake derleme ortamında, dosyayı düzenleyerek varsayılan yapılandırmayı değiştirmeniz gerekir `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` . Aşağıdaki CMake biçimini `set(ASC_XXX ON)` veya `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` diğer tüm ortamlar için aşağıdaki dosyayı kullanın. Örneğin, `#define ASC_XXX`.

Her yapılandırmanın varsayılan davranışı aşağıdaki tablolarda verilmiştir: 

## <a name="general"></a>Genel

| Ad | Tür | Varsayılan | Ayrıntılar |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Dize | Defender-IoT-Micro-Agent | Cihazın benzersiz tanımlayıcısı.  |
| SECURITY_MODULE_VERSION_ (BIRINCIL) (IKINCIL) (DÜZELTME EKI)  | Sayı | 3.2.1 | Sürüm. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Sayı  | 3 | Defender-IoT-mikro-aracısının başarısız olduktan sonra güvenlik iletisini göndermek için gereken süre. (saniye cinsinden) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Sayı | 300 | Defender-IoT-Micro-Agent bekleme süresi (saniye cinsinden). Süre aşılırsa durum askıya al olarak değişir. |

## <a name="collection"></a>Koleksiyon

| Ad | Tür | Varsayılan | Ayrıntılar |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Sayı  | 30  | Toplayıcının başlangıç koleksiyonu Aralık boşluğu. Başlangıç sırasında, birden fazla cihazdan aynı anda ileti gönderilmesini önlemek için bu değer sistem koleksiyonuna eklenir.  |
| ASC_HIGH_PRIORITY_INTERVAL | Sayı | 10 | Toplayıcının yüksek öncelikli Grup aralığı (saniye cinsinden). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Sayı | 30 | Toplayıcının orta öncelikli Grup aralığı (saniye cinsinden). |
| ASC_LOW_PRIORITY_INTERVAL | Sayı | 145.440  | Toplayıcının düşük öncelikli Grup aralığı (saniye cinsinden). |

#### <a name="collector-network-activity"></a>Toplayıcı ağ etkinliği

Toplayıcı ağ etkinlik yapılandırmanızı özelleştirmek için aşağıdakileri kullanın:

| Ad | Tür | Varsayılan | Ayrıntılar |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boole | yanlış | `TCP`Ağ etkinliğini filtreler. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boole | yanlış | `UDP`Ağ etkinlik olaylarını filtreler. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boole | yanlış | `ICMP`Ağ etkinlik olaylarını filtreler. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boole | true | Yalnızca tek noktaya yayın gelen paketleri yakalar. Yanlış olarak ayarlandığında, hem yayını hem de çok noktaya yayını yakalar. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boole  | yanlış  | Toplayıcının boş bir olayını gönderir. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Sayı | 64 | Bellekte depolanacak en fazla IPv4 ağ olayı sayısı. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Sayı | 64  | Bellekte depolanacak en fazla IPv6 ağ olayı sayısı. |

### <a name="collectors"></a>Toplayıcıları
| Ad | Tür | Varsayılan | Ayrıntılar |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Boole | AÇIK | Sinyal toplayıcıyı etkinleştirilir. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Boole | AÇIK | Ağ etkinliği toplayıcıyı sunar. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Boole | AÇIK | Sistem bilgisi toplayıcıyı sunar.  |

Diğer yapılandırmalar bayrakları gelişmiş ve desteklenmeyen özelliklere sahiptir. Bunu değiştirmek veya daha fazla bilgi için desteğe başvurun.
 
## <a name="supported-security-alerts-and-recommendations"></a>Desteklenen güvenlik uyarıları ve önerileri

Azure RTOS için Defender-IoT-Micro-Agent, belirli güvenlik uyarılarını ve önerilerini destekler. Hizmetiniz için [ilgili uyarı ve öneri değerlerini incelediğinizden ve özelleştirdiğinizden](concept-rtos-security-alerts-recommendations.md) emin olun.

## <a name="log-analytics-optional"></a>Log Analytics (isteğe bağlı)

Cihaz olaylarını ve etkinliklerini araştırmak için Log Analytics etkinleştirebilir ve yapılandırabilirsiniz. Kurma hakkında bilgi edinin ve daha fazla bilgi için bkz. [IoT hizmeti Için Defender ile Log Analytics](how-to-security-data-access.md#log-analytics) kullanma. 

## <a name="next-steps"></a>Sonraki adımlar


- Azure RTOS [güvenlik uyarıları ve önerileri](concept-rtos-security-alerts-recommendations.md) için Defender-IoT-mikro-Agent ' ı inceleyin ve özelleştirin
- Gerektiğinde [Azure RTOS API 'si Için Defender-IoT-mikro-Agent](azure-rtos-security-module-api.md) bölümüne bakın.
