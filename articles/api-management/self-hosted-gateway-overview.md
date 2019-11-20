---
title: Şirket içinde barındırılan Azure API Management Gateway 'e genel bakış | Microsoft Docs
description: Şirket içinde barındırılan Azure API Management Gateway 'in karma ve çok yüksek ortamlarda API 'Leri yönetmesine nasıl yardımcı olduğunu öğrenin.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513724"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Şirket içinde barındırılan API Management ağ geçidine genel bakış

Bu makalede, şirket içinde barındırılan ağ geçidi özelliğinin karma ve çok bulut API yönetimini nasıl sağladığından, üst düzey mimarisini sunduğu ve temel yeteneklerini vurguladığı açıklanır.

> [!NOTE]
> Şirket içinde barındırılan ağ geçidi özelliği önizlemededir. Önizleme sırasında, şirket içinde barındırılan ağ geçidi, yalnızca geliştirici ve Premium katmanlarda ek ücret ödemeden kullanılabilir. Geliştirici katmanı, tek bir şirket içinde barındırılan ağ geçidi dağıtımıyla sınırlıdır.

## <a name="hybrid-and-multi-cloud-api-management"></a>Karma ve çoklu bulut API Yönetimi

Şirket içinde barındırılan ağ geçidi özelliği, karma ve çok bulut ortamları için API Management destekler ve kuruluşların şirket içinde ve bulutta barındırılan API 'Leri Azure 'daki tek bir API Management hizmetinden verimli ve güvenli bir şekilde yönetmesine olanak sağlar.

Şirket içinde barındırılan ağ geçidiyle, müşteriler, API Management ağ geçidi bileşeninin kapsayıcılı bir sürümünü, API 'Lerini barındırdıkları ortamlara dağıtma esnekliği sunar. Şirket içinde barındırılan tüm ağ geçitleri, tüm iç ve dış API 'Ler genelinde görünürlük ve Birleşik yönetim deneyimi sunarak müşterilere, içinde yer aldığı API Management hizmetten yönetilir. Ağ geçitlerinin API 'lere yerleştirilmesi, müşterilerin API trafik akışlarını iyileştirmelerine ve güvenlik ve uyumluluk gereksinimlerini karşıçalıştırmasına izin verir.

Her bir API Management hizmeti aşağıdaki anahtar bileşenlerinden oluşur:

-   Bir API olarak sunulan yönetim düzlemi, Azure portal, PowerShell ve diğer desteklenen mekanizmalar aracılığıyla hizmeti yapılandırmak için kullanılır.
-   Ağ Geçidi (veya veri düzlemi), API isteklerinin proxy isteklerini, ilkelerin uygulanmasını ve Telemetriyi toplamayı sağlamaktan sorumludur
-   Geliştiriciler tarafından API 'Leri bulmayı, öğrenmeyi ve kullanmayı öğrenmek için kullanılan geliştirici portalı

Varsayılan olarak, tüm bu bileşenler Azure 'da dağıtılır ve API 'Lerin alt uçlarının barındırıldığından bağımsız olarak, tüm API trafiğinin Azure üzerinden akmasını sağlar. Bu modelin operasyonel basitliği, artan gecikme süresi, uyumluluk sorunları ve bazı durumlarda ek veri aktarımı ücretleri üzerinden gelir.

![Kendi kendine barındırılan ağ geçitleri olmadan API trafik akışı](media/self-hosted-gateway-overview/without-gateways.png)

Şirket içinde barındırılan ağ geçitlerini arka uç API uygulamalarıyla aynı ortamlara dağıtmak ve bunları API Management hizmetine eklemek, API trafiğinin doğrudan arka uç API 'Lerine akmasını sağlar, bu da gecikme süresini iyileştirir, veri aktarımı maliyetlerini iyileştirir ve bu sayede uygulamalarının nerede barındırıldığından bağımsız olarak kuruluştaki tüm API 'lerin tek bir yönetim noktası ve keşfedilmesi avantajlarının yanı sıra uyumluluk sağlar.

![Şirket içinde barındırılan ağ geçitleriyle API trafik akışı](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Paketleme ve Özellikler

Şirket içinde barındırılan ağ geçidi, her API Management hizmetinin bir parçası olarak Azure 'a dağıtılan yönetilen ağ geçidinin Kapsayıcılı, işlevsel olarak eşdeğer bir sürümüdür. Şirket içinde barındırılan ağ geçidi, Microsoft Container Registry Linux tabanlı bir Docker kapsayıcısı olarak kullanılabilir. Docker, Kubernetes veya bir masaüstü, sunucu kümesi veya bulut altyapısında çalışan başka bir kapsayıcı düzenleme çözümüne dağıtılabilir.

> [!IMPORTANT]
> Yönetilen ağ geçidinde bulunan bazı işlevler henüz önizlemede kullanılamıyor. En önemlisi: Olay Hub 'ı ilkesinde oturum açın, Service Fabric tümleştirme, aşağı akış HTTP/2. Şirket içinde barındırılan ağ geçidinde yerleşik bir önbelleğin kullanılabilmesini sağlama planı yoktur.

## <a name="connectivity-to-azure"></a>Azure bağlantısı

Şirket içinde barındırılan ağ geçidi 443 numaralı bağlantı noktasında Azure 'a giden TCP/IP bağlantısı gerektirir. Her şirket içinde barındırılan her ağ geçidinin tek bir API Management hizmetiyle ilişkilendirilmesi ve yönetim düzlemi aracılığıyla yapılandırılması gereklidir. Şirket içinde barındırılan ağ geçidi, için Azure bağlantısını kullanır:

-   Her dakikada sinyal mesajları göndererek durumunu raporlama
-   Düzenli olarak (10 saniyede bir) denetleniyor ve her kullanılabilir olduğunda yapılandırma güncelleştirmeleri uygulanıyor
-   Bu şekilde yapılandırıldıysa, Azure Izleyici 'ye istek günlükleri ve ölçümleri gönderme
-   Application Insights olarak ayarlandıysa olayları gönderme

Azure ile bağlantı kesildiğinde, şirket içinde barındırılan ağ geçidi yapılandırma güncelleştirmelerini alamıyor, durumunu bildiremeyecek veya Telemetriyi karşıya yükleyemeyecektir.

Şirket içinde barındırılan ağ geçidi, "başarısız statik" olarak tasarlanmıştır ve Azure ile geçici bağlantı kaybını da sürdürabilir. Yerel yapılandırma yedeklemesi açık olarak veya olmadan dağıtılabilir. Önceki durumda, şirket içinde barındırılan ağ geçitleri düzenli olarak bir yapılandırmanın yedek kopyasını kapsayıcıya veya Pod 'a bağlı kalıcı bir birime kaydeder.

Yapılandırma Yedeklemesi devre dışı bırakıldığında ve Azure bağlantısı kesintiye uğradığında:

-   Çalıştıran şirket içinde barındırılan ağ geçitleri, yapılandırmanın bellek içi bir kopyasını kullanarak çalışmaya devam edecektir
-   Kendi kendine barındırılan ağ geçitleri başlatılamaz

Yapılandırma Yedeklemesi açık olduğunda ve Azure ile bağlantı kesildiğinde:

-   Çalıştıran şirket içinde barındırılan ağ geçitleri, yapılandırmanın bellek içi bir kopyasını kullanarak çalışmaya devam edecektir
-   Kendi kendine barındırılan ağ geçitleri, yapılandırmanın yedek bir kopyasını kullanmaya başlayacak

Bağlantı geri yüklendiğinde, kesintiden etkilenen her bir şirket içinde barındırılan ağ geçidi, ilişkili API Management hizmetine otomatik olarak yeniden bağlanır ve ağ geçidi "çevrimdışı" iken gerçekleşen tüm yapılandırma güncelleştirmelerini indirir.

## <a name="next-steps"></a>Sonraki adımlar

-   [Bu konuda ek arka plan için bir teknik incelemeyi okuyun](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Şirket içinde barındırılan ağ geçidini Docker 'a dağıtma](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Kubernetes 'e şirket içinde barındırılan ağ geçidi dağıtma](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
