---
title: VNET 'te Azure yay bulutu 'nı çalıştıran müşteri sorumlulukları
description: Bu makalede, VNET 'te Azure yay bulutu 'nı çalıştıran müşteri sorumlulukları açıklanmaktadır.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java
ms.openlocfilehash: a6b444092ec4e3588564a3f902b49c4ed3dc5fe5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376792"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>VNET 'te Azure Spring Cloud çalıştırmaya yönelik müşteri sorumlulukları
Bu belge, bir sanal ağda Azure Spring Cloud 'ın kullanımına yönelik belirtimleri içerir.

Azure yay bulutu sanal ağınıza dağıtıldığında, sanal ağın dışındaki hizmetlerde giden bağımlılıklara sahip olur. Yönetim ve operasyonel amaçlar için Azure Spring Cloud, belirli bağlantı noktalarına ve tam etki alanı adlarına (FQDN) erişmelidir. Bu uç noktaların Azure Spring Cloud Management düzlemi ile iletişim kurması ve temel Kubernetes küme bileşenlerini ve güvenlik güncelleştirmelerini indirmesi ve yüklemesi gerekir.

Azure Spring Cloud, varsayılan olarak sınırsız giden (çıkış) internet erişimine sahiptir. Bu ağ erişimi düzeyi, çalıştırdığınız uygulamaların, gerektiğinde dış kaynaklara erişmesine olanak sağlar. Çıkış trafiğini kısıtlamak istiyorsanız, bakım görevleri için sınırlı sayıda bağlantı noktasına ve adrese erişilebilir olması gerekir. Giden adreslerin güvenliğini sağlamaya yönelik en basit çözüm, etki alanı adlarına göre giden trafiği denetleyesağlayan bir güvenlik duvarı cihazının kullanımı. Örneğin, Azure Güvenlik Duvarı, giden HTTP ve HTTPS trafiğini hedefin FQDN 'sine göre kısıtlayabilir. Ayrıca tercih ettiğiniz güvenlik duvarı ve güvenlik kurallarınızı, bu gerekli bağlantı noktalarına ve adreslere izin verecek şekilde yapılandırabilirsiniz.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud kaynak gereksinimleri 

Aşağıda, Azure yay bulut hizmetleri için kaynak gereksinimlerinin bir listesi verilmiştir. Genel bir gereksinim olarak, Azure Spring Cloud ve temel alınan ağ kaynakları tarafından oluşturulan kaynak gruplarını değiştirmemelisiniz.
- Azure Spring Cloud tarafından oluşturulan ve sahip olunan kaynak gruplarını değiştirmeyin.
  - Varsayılan olarak bu kaynak grupları, AP-svc-rt_ [HIZMET-ÖRNEĞI-adı]_[Bölge] * ve AP_[HIZMET-ÖRNEĞI-adı] _ [Bölge] * olarak adlandırılır.
- Azure Spring Cloud tarafından kullanılan alt ağları değiştirmeyin.
- Aynı alt ağda birden fazla Azure yay bulut hizmeti örneği oluşturmayın.
- Trafiği denetlemek için bir güvenlik duvarı kullanırken, hizmet örneğini çalıştıran, bakımını yapan ve destekleyen Azure Spring bulut bileşenlerine yönelik aşağıdaki çıkış *trafiğini engellemez.*

## <a name="azure-spring-cloud-network-requirements"></a>Azure yay bulut ağı gereksinimleri

  | Hedef uç nokta | Bağlantı noktası | Kullanın | Not |
  |------|------|------|------|
  | *: 1194 *veya* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -azurecyüksek: 1194 | UDP: 1194 | Temel Kubernetes küme yönetimi. | |
  | *: 443 *veya* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -azurecyüksek: 443 | TCP: 443 | Azure yay bulut hizmeti yönetimi. | "RequiredTraffics" hizmet örneğinin bilgileri, kaynak yükünde "networkProfile" bölümünde bilinirdi. |
  | *: 9000 *veya* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -azurecyüksek: 9000 | TCP: 9000 | Temel Kubernetes küme yönetimi. |
  | *: 123 *veya* NTP.Ubuntu.com:123 | UDP: 123 | Linux düğümlerinde NTP zaman eşitlemesi. | |
  | *. azure.io:443 *veya* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Azure Container Registry. | ,  [Sanal ağda Azure Container Registry hizmet uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md)etkinleştirilerek değiştirilebilir. |
  | *. core.windows.net:443 ve *. core.windows.net:445 *veya* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -Storage: 443 ve Storage: 445 | TCP: 443, TCP: 445 | Azure Dosya Depolama | , Sanal ağda *Azure Storage* [hizmet uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md)etkinleştirilerek değiştirilebilir. |
  | *. servicebus.windows.net:443 *veya* [servicetag](../virtual-network/service-tags-overview.md#available-service-tags) -EventHub: 443 | TCP: 443 | Azure Olay Hub 'ı. | , Sanal ağda *Azure Event Hubs* [hizmet uç noktası](../virtual-network/virtual-network-service-endpoints-overview.md)etkinleştirilerek değiştirilebilir. |
  

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Azure yay bulutu FQDN gereksinimleri/uygulama kuralları

Azure Güvenlik Duvarı, aşağıdaki yapılandırmaların basitleşmesi için **AZUREKUBERNETESSERVICE** FQDN etiketini sağlar:

  | Hedef FQDN | Bağlantı noktası | Kullanın |
  |------|------|------|
  | *. azmk8s.io | HTTPS: 443 | Temel Kubernetes küme yönetimi. |
  | <i>mcr.microsoft.com</i> | HTTPS: 443 | Microsoft Container Registry (MCR). |
  | *. cdn.mscr.io | HTTPS: 443 | Azure CDN tarafından desteklenen MCR Storage. |
  | *. data.mcr.microsoft.com | HTTPS: 443 | Azure CDN tarafından desteklenen MCR Storage. |
  | <i>management.azure.com</i> | HTTPS: 443 | Temel Kubernetes küme yönetimi. |
  | <i>* login.microsoftonline.com</i> | HTTPS: 443 | Azure Active Directory kimlik doğrulaması. |
  | <i>* login.microsoft.com</i> | HTTPS: 443 | Azure Active Directory kimlik doğrulaması. |
  |<i>packages.microsoft.com</i>    | HTTPS: 443 | Microsoft paketleri deposu. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS: 443 | Kubernetes kullanan ve Azure CNı gibi gerekli ikilileri yüklemek için depo gereklidir. |
  | *mscrl.microsoft.com* | HTTPS: 80 | Gerekli Microsoft sertifika zinciri yolları. |
  | *crl.microsoft.com* | HTTPS: 80 | Gerekli Microsoft sertifika zinciri yolları. |
  | *crl3.digicert.com* | HTTPS: 80 | 3. taraf SSL sertifika zinciri yolları. |
  
## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>Azure Spring Cloud, üçüncü taraf uygulama performansı yönetimi için isteğe bağlı FQDN

Azure Güvenlik Duvarı, aşağıdaki yapılandırmaların basitleşmesi için **AZUREKUBERNETESSERVICE** FQDN etiketini sağlar:

  | Hedef FQDN | Bağlantı noktası | Kullanın                                                          |
  | ---------------- | ---- | ------------------------------------------------------------ |
  | Toplayıcı *. newrelik. com | TCP: 443/80 | Yeni relik APM aracılarının ABD bölgesinden gerekli ağları, Ayrıca bkz. [APM aracıları ağları](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
  | Collector *. eu01. NR-data.net | TCP: 443/80 | Yeni relik APM aracılarının, AB bölgesindeki gerekli ağları da bkz. [APM aracıları ağları](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |

## <a name="see-also"></a>Ayrıca bkz.
* [Uygulamanıza özel bir ağda erişin](access-app-virtual-network.md)
* [Application Gateway ve Azure Güvenlik Duvarı kullanarak uygulamaları kullanıma sunma](expose-apps-gateway-azure-firewall.md)
