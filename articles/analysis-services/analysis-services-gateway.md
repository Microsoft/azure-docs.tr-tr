---
title: Azure Analysis Services için şirket içi veri ağ geçidi | Microsoft Docs
description: Azure 'daki Analysis Services sunucunuz şirket içi veri kaynaklarına bağlanabiliyor ise şirket içi ağ geçidi gereklidir.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6ca96f76287482a445d8a9a1cdc441333b36efbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97739612"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Şirket içi veri ağ geçidi ile şirket içi veri kaynaklarına bağlanma

Şirket içi veri ağ geçidi, bulutta şirket içi veri kaynakları ve Azure Analysis Services sunucularınız arasında güvenli veri aktarımı sağlar. Aynı bölgedeki birden çok Azure Analysis Services sunucusu ile çalışmanın yanı sıra, ağ geçidinin en son sürümü de Azure Logic Apps, Power BI, güç uygulamaları ve güç otomatikleştir ile de çalışır. Yüklediğiniz ağ geçidi bu hizmetlerin tümünde aynı olduğundan, Azure Analysis Services ve Logic Apps bazı ek adımlara sahiptir.

Burada sunulan bilgiler, Azure Analysis Services şirket içi veri ağ geçidiyle nasıl çalıştığına özgüdür. Genel olarak ağ geçidi ve diğer hizmetlerle nasıl çalıştığı hakkında daha fazla bilgi edinmek için bkz. [Şirket içi veri ağ geçidi nedir?](/data-integration/gateway/service-gateway-onprem).

Azure Analysis Services için, ilk kez ağ geçidi ile kurulum almak dört bölümden oluşan bir işlemdir:

- **Kurulumu indir ve Çalıştır** -Bu adım, kuruluşunuzdaki bir bilgisayara ağ geçidi hizmeti yükler. Ayrıca [kiracınızın](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure AD 'deki bir hesabı kullanarak Azure 'da oturum açın. Azure B2B (konuk) hesapları desteklenmez.

- **Ağ geçidinizin kaydetme** -Bu adımda, ağ geçidiniz için bir ad ve kurtarma anahtarı belirtip ağ geçidinizi ağ geçidi bulut hizmetine kaydederek bir bölge seçin. Ağ Geçidi kaynağınız herhangi bir bölgeye kaydedilebilir, ancak Analysis Services sunucularınız ile aynı bölgede olması önerilir. 

- **Azure 'da ağ geçidi kaynağı oluşturma** -Bu adımda, Azure 'da bir ağ geçidi kaynağı oluşturursunuz.

- **Ağ geçidi kaynağını sunuculara bağlama** -bir ağ geçidi kaynağınız varsa, sunucuları buna bağlamaya başlayabilirsiniz. Aynı bölgede olduklarından, birden çok sunucuyu ve diğer kaynakları bağlayabilirsiniz.

## <a name="installing"></a>Yükleme

Azure Analysis Services ortamı için yükleme yaparken, [Azure Analysis Services için şirket içi veri ağ geçidini yükleme ve yapılandırma](analysis-services-gateway-install.md)bölümünde açıklanan adımları izlemeniz önemlidir. Bu makale Azure Analysis Services özeldir. Azure 'da şirket içi veri ağ geçidi kaynağı kurmak ve Azure Analysis Services sunucunuzu kaynağa bağlamak için gereken ek adımları içerir.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Farklı bir abonelikte ağ geçidi kaynağına bağlanma

Azure ağ geçidi kaynağınızı, sunucunuz ile aynı abonelikte oluşturmanız önerilir. Ancak, sunucularınızı başka bir abonelikteki bir ağ geçidi kaynağına bağlanacak şekilde yapılandırabilirsiniz. Mevcut sunucu ayarlarını yapılandırırken veya portalda yeni bir sunucu oluşturulduğunda, ancak PowerShell kullanılarak yapılandırılabilirler, başka bir abonelikte ağ geçidi kaynağına bağlanma desteklenmez. Daha fazla bilgi için bkz. [ağ geçidi kaynağını sunucuya bağlama](analysis-services-gateway-install.md#connect-gateway-resource-to-server).

## <a name="ports-and-communication-settings"></a>Bağlantı noktaları ve iletişim ayarları

Ağ geçidi, Azure Service Bus'a yönelik bir giden bağlantı oluşturur. Şu giden bağlantı noktaları üzerinden iletişim kurar: TCP 443 (varsayılan), 5671, 5672, 9350 ila 9354.  Ağ geçidi için gelen bağlantı noktaları gerekli değildir.

Güvenlik duvarınızdaki veri bölgenizin IP adreslerini eklemeniz gerekebilir. [Microsoft Azure Veri Merkezi IP listesini](https://www.microsoft.com/download/details.aspx?id=56519) indirebilirsiniz. Bu liste haftalık olarak güncelleştirilir. Azure Veri Merkezi IP listesindeki adresler, CIDR gösteriminde listelenir. Daha fazla bilgi için bkz. [sınıfsız Inter-Domain yönlendirme](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Ağ Geçidi tarafından kullanılan tam etki alanı adları aşağıda verilmiştir.

| Etki alanı adları | Giden bağlantı noktaları | Description |
| --- | --- | --- |
| *.powerbi.com |80 |Yükleyiciyi indirmek için kullanılan HTTP. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Gelişmiş İleti Sıraya Alma Protokolü (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |TCP üzerinden Service Bus Geçişi'ndeki dinleyiciler (Erişim Denetimi belirtecinin alınması için 443 gerekir) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |80 |Power BI hizmeti tarafından ağ geçidine erişilemiyorsa İnternet bağlantısını test etmek için kullanılır. |
| *.microsoftonline-p.com |443 |Yapılandırmaya bağlı olarak kimlik doğrulaması için kullanılır. |
| dc.services.visualstudio.com    |443 |Appınsights tarafından telemetri toplamak için kullanılır. |

## <a name="next-steps"></a>Sonraki adımlar 

Aşağıdaki makaleler, ağ geçidinin desteklediği tüm hizmetler için geçerli olan şirket içi veri ağ geçidi genel içeriğine eklenmiştir:

* [Şirket içi veri ağ geçidi hakkında SSS](/data-integration/gateway/service-gateway-onprem-faq)   
* [Şirket içi veri ağ geçidi uygulamasını kullanma](/data-integration/gateway/service-gateway-app)   
* [Kiracı düzeyinde yönetim](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Ara sunucu ayarlarını yapılandırma](/data-integration/gateway/service-gateway-proxy)   
* [İletişim ayarlarını değiştirme](/data-integration/gateway/service-gateway-communication)   
* [Günlük dosyalarını yapılandırma](/data-integration/gateway/service-gateway-log-files)   
* [Sorun giderme](/data-integration/gateway/service-gateway-tshoot)
* [Ağ geçidi performansını izleme ve en iyi duruma getirme](/data-integration/gateway/service-gateway-performance)
