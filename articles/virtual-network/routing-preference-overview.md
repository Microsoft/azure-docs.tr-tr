---
title: Azure 'da yönlendirme tercihi
description: Yönlendirme tercihi ile trafiğinizi Azure ile Internet arasında nasıl yönlendirdiğini nasıl seçebileceğinizi öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 44edd9273730ff7ba81a3ad5af2408ff6b7b7a5c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060644"
---
# <a name="what-is-routing-preference"></a>Yönlendirme tercihi nedir?

Azure yönlendirme tercihi, trafiğinizin Azure ile Internet arasında nasıl yönlendirdiğini seçmenizi sağlar. Trafiği Microsoft ağı aracılığıyla ya da ISS ağı (genel İnternet) aracılığıyla yönlendirmeyi seçebilirsiniz. Bu seçenekler aynı zamanda *soğuk patates* yönlendirme ve *sık erişimli yönlendirme* olarak da adlandırılır. Çıkış veri aktarımı fiyatı, yönlendirme seçimine göre farklılık gösterir. Genel IP adresi oluştururken yönlendirme seçeneğini belirleyebilirsiniz. Genel IP adresi, sanal makine, sanal makine ölçek kümeleri, internet 'e yönelik yük dengeleyici vb. gibi kaynaklarla ilişkilendirilebilir. Blob, dosya, Web ve Azure Veri Gölü gibi Azure depolama kaynakları için yönlendirme tercihini de ayarlayabilirsiniz. Varsayılan olarak, trafik tüm Azure hizmetleri için Microsoft Global ağı aracılığıyla yönlendirilir.

## <a name="routing-via-microsoft-global-network"></a>Microsoft Global ağı aracılığıyla yönlendirme

Trafiğinizi *Microsoft Global Network* üzerinden yönlendirdiğinizde, trafik 160.000 ' den fazla Fiber ' a yayılan en büyük ağlardan biri üzerinden, 165 ' den fazla iletişim kutusu (pop) ile dağıtılır. Ağ, aşırı yüksek güvenilirlik ve kullanılabilirlik sağlamak için birden fazla yedekli fiber yol ile birlikte sağlanmış olur. Trafik Mühendisliği, trafiğiniz için düşük gecikmeli yol seçimini sağlayan, yazılım tanımlı bir WAN denetleyicisi tarafından yönetilir ve Premium ağ performansı sunar.

![Microsoft Global ağı aracılığıyla yönlendirme](media/routing-preference-overview/route-via-microsoft-global-network.png)

Giriş **trafiği:** Küresel BGP her noktaya yayın duyurusu, giriş trafiğinin kullanıcıya en yakın Microsoft ağını girdiğini sağlar. Örneğin, Singapur 'deki bir Kullanıcı Chicago 'de barındırılan Azure kaynaklarına eriştiğinde, ABD 'de Microsoft Global ağı 'nda Singapur Edge POP 'daki trafik girilir ve Microsoft Network 'e Chicago 'de barındırılan hizmet ile seyahat edilir.

**Çıkış trafiği:** Çıkış trafiği aynı prensibi izler. Trafik, Microsoft küresel ağı 'na ait yolculuğun büyük bir bölümünü hareket eder ve kullanıcıya en yakın çıkış yapar. Örneğin, Azure Chicago 'nin trafiği Singapur 'dan bir kullanıcıya gidiyor ise trafik Microsoft ağı 'na Chicago 'den Singapur 'a, Microsoft ağı ise Singapur Edge POP 'da çıkar.

Hem giriş hem de çıkış trafiği Microsoft Global Network 'teki seyahatin toplu olarak kalır. Bu, *soğuk patates yönlendirme* olarak da bilinir.


## <a name="routing-over-public-internet-isp-network"></a>Ortak Internet üzerinden yönlendirme (ISS ağı)

Yeni yönlendirme seçimi *Internet yönlendirme* , Microsoft Global Network 'teki yolculuğu en aza indirir ve trafiğinizi yönlendirmek IÇIN transit ISS ağını kullanır. Bu maliyet için iyileştirilmiş yönlendirme seçeneği, diğer bulut sağlayıcılarına benzer ağ performansı sunar.

![Genel Internet üzerinden yönlendirme](media/routing-preference-overview/route-via-isp-network.png)

Giriş **trafiği:** Giriş yolu, trafiğin barındırılan hizmet bölgesine en yakın Microsoft ağını girdiği anlamına gelen, *etkin patates yönlendirme* kullanır. Örneğin, Singapur 'deki bir Kullanıcı Chicago 'de barındırılan Azure kaynaklarına eriştiğinde trafik genel İnternet üzerinden geçer ve Microsoft Global ağı Chicago 'ye girer.

**Çıkış trafiği:** Çıkış trafiği aynı prensibi izler. Trafik, hizmetin barındırıldığı bölgedeki Microsoft ağından çıkar. Örneğin, Azure Chicago 'deki hizmetinizin trafiği Singapur 'dan bir kullanıcıya gidiyor ise trafik, Microsoft ağını Chicago 'da çıkar ve genel İnternet üzerinden Singapur 'daki Kullanıcı üzerinden dolaşır.

## <a name="supported-services"></a>Desteklenen hizmetler

"Microsoft Global Network" yönlendirme tercihi seçimine sahip genel IP, herhangi bir Azure hizmeti ile ilişkilendirilebilir. Ancak, yönlendirme tercihi seçimi **Internet** Ile genel IP aşağıdaki Azure kaynaklarıyla ilişkilendirilebilir:

* Sanal makine
* Sanal makine ölçek kümesi
* Azure Kubernetes Service (AKS)
* Internet 'e yönelik yük dengeleyici
* Application Gateway
* Azure Güvenlik Duvarı

Depolama için birincil uç noktalar her zaman **Microsoft Global ağını** kullanır. Trafik yönlendirme için tercih ettiğiniz ikincil uç noktaları **Internet** ile etkinleştirebilirsiniz. Desteklenen depolama hizmetleri şunlardır:

* Bloblar
* Dosyalar
* Web
* Azure DataLake

## <a name="pricing"></a>Fiyatlandırma
Her iki seçenek arasındaki fiyat farkı, Internet çıkış veri aktarımı fiyatlandırmasına yansıtılır. **Microsoft Global Network** veri aktarımı fiyatı üzerinden yönlendirme, geçerli internet çıkış fiyatıyla aynı. En son fiyatlandırma bilgileri için [Azure bant genişliği fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/bandwidth/) ziyaret edin.

## <a name="limitations"></a>Sınırlamalar


* Yönlendirme tercihi yalnızca bölge yedekli standart IP adresi SKU 'SU ile uyumludur. Temel genel IP adresi SKU 'SU desteklenmiyor.
* Yönlendirme tercihi şu anda yalnızca IPv4 Genel IP adreslerini desteklemektedir. IPv6 genel IP adresleri desteklenmez.


## <a name="next-steps"></a>Sonraki adımlar

* [Azure PowerShell kullanarak bir VM için yönlendirme tercihini yapılandırma](configure-routing-preference-virtual-machine-powershell.md)
* [Azure CLı kullanarak bir VM için yönlendirme tercihini yapılandırma](configure-routing-preference-virtual-machine-cli.md)
