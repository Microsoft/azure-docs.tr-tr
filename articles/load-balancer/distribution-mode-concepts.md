---
title: Azure Load Balancer Dağıtım modları
description: Azure Load Balancer farklı dağıtım modları hakkında öğrenmeye başlayın.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99551446"
---
# <a name="azure-load-balancer-distribution-modes"></a>Azure Load Balancer Dağıtım modları

Azure Load Balancer, yük dengeli uygulamanıza yönlendirme bağlantıları için iki dağıtım modunu destekler:

* Karma tabanlı
* Kaynak IP benzeşimi

## <a name="hash-based"></a>Karma tabanlı

Azure Load Balancer’ın varsayılan dağıtım modu beşli tanımlama grubu olan karmadır. 

Tanımlama grubu şunlardan oluşur:
* **Kaynak IP**
* **Kaynak bağlantı noktası**
* **Hedef IP**
* **Hedef bağlantı noktası**
* **Protokol türü**

Karma, trafiği kullanılabilir sunucularla eşlemek için kullanılır. Algoritma yalnızca bir aktarım oturumunda bir veya daha fazla sürekliliği sağlar. Aynı oturumdaki paketler, yük dengeli uç noktanın arkasındaki veri merkezi IP 'ye yönlendirilir. İstemci aynı kaynak IP 'den yeni bir oturum başlattığında, kaynak bağlantı noktası değişir ve trafiğin farklı bir veri merkezi uç noktasına geçmesine neden olur.

![Beş demet temelli karma tabanlı dağıtım modu](./media/distribution-mode-concepts/load-balancer-distribution.png)

Karma tabanlı modun bir yapılandırma türü vardır:

* **Hiçbiri (karma tabanlı)** -aynı istemciden gelen ardışık isteklerin herhangi bir sanal makine tarafından işlenebileceğini belirtir.

## <a name="source-ip-affinity"></a>Kaynak IP benzeşimi

Bu dağıtım modu oturum benzeşimi veya istemci IP’si benzeşimi olarak da bilinir. Bu mod, trafiği kullanılabilir sunucularla eşlemek için iki demet (kaynak IP ve hedef IP) ya da üç demet (kaynak IP, hedef IP ve protokol türü) karmasını kullanır. 

Kaynak IP benzeşimini kullanarak, aynı istemci bilgisayarından başlatılan bağlantılar aynı veri merkezi uç noktasına gider.

Aşağıdaki şekilde iki demet yapılandırması gösterilmektedir. İki demet, yük dengeleyici üzerinden sanal makine 1 ' e (VM1) nasıl çalışdığına dikkat edin. VM1 daha sonra VM2 ve VM3 tarafından yedeklenir.

![İki demet oturum benzeşimi dağıtım modu](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Kaynak IP benzeşimi modunun iki yapılandırma türü vardır:

* **ISTEMCI IP (kaynak IP benzeşimi 2-kayıt düzeni)** -aynı istemci IP adresinden gelen isteklerin aynı sanal makine tarafından işleneceğini belirtir.
* **İstemci IP 'si ve Protokolü (kaynak IP benzeşimi 3-kayıt)** -aynı istemci IP adresi ve protokol birleşimlerinden gelen ardışık isteklerin aynı sanal makine tarafından işleneceğini belirtir.

## <a name="use-cases"></a>Uygulama alanları

İstemci IP 'si ve Protokolü (kaynak IP benzeşimi 3-kayıt) ile kaynak IP benzeşimi, Azure Load Balancer ile Uzak Masaüstü Ağ Geçidi arasında bir uyumsuzluk çözer (RD Ağ Geçidi). 

Başka bir kullanım örneği senaryosu medya karşıya yükleme 'dir. Karşıya veri yükleme işlemi UDP üzerinden yapılır, ancak denetim düzlemi TCP aracılığıyla gerçekleştirilir:

* İstemci, yük dengeli ortak adrese bir TCP oturumu başlatır ve belirli bir DIP 'ye yönlendirilir. Kanal, bağlantı durumunu izlemek için etkin bırakılır.
* Aynı istemci bilgisayardan gelen yeni bir UDP oturumu aynı yük dengeli ortak uç noktaya başlatılır. Bağlantı, önceki TCP bağlantısıyla aynı DIP uç noktasına yönlendirilir. Medya karşıya yüklemesi, TCP üzerinden bir denetim kanalını koruyarak yüksek aktarım düzeyinde yürütülebilir.

> [!NOTE]
> Bir sanal makineyi kaldırarak veya ekleyerek yük dengeli bir küme değiştirildiğinde, istemci isteklerinin dağıtımı yeniden hesaplanır. Mevcut istemcilerden gelen yeni bağlantılara, aynı sunucuda son vermek için bağlı yapamazsınız. Ayrıca, kaynak IP benzeşimi Dağıtım modunu kullanmak trafiğin eşit bir şekilde dağıtımına neden olabilir. Proxy 'nin arkasında çalışan istemciler tek bir benzersiz istemci uygulaması olarak görünebilir.


## <a name="next-steps"></a>Sonraki adımlar

Azure Load Balancer Dağıtım modunu yapılandırma hakkında daha fazla bilgi için bkz. [Azure Load Balancer için Dağıtım modunu yapılandırma](load-balancer-distribution-mode.md).

