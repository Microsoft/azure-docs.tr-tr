---
title: Azure 'da alt ağ uzantısı | Microsoft Docs
description: Azure 'da alt ağ uzantısı hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587517"
---
# <a name="subnet-extension"></a>Alt ağ uzantısı
Ortak buluta iş yükü geçişi dikkatli bir planlama ve koordinasyon gerektirir. Önemli önemli noktalar, IP adreslerinizi saklama özelliği olabilir. Özellikle uygulamalarınızın IP adresi bağımlılığı varsa veya belirli IP adreslerini kullanmak için uyumluluk gereksinimleriniz varsa, bu önemli olabilir. Azure sanal ağ, tercih ettiğiniz bir IP adresi aralığını kullanarak VNet ve alt ağlar oluşturmanıza izin vererek bu sorunu sizin için çözer.

Yukarıdaki gereksinim, bazı uygulamaların şirket içinde tutulması için ek bir gereksinimle birlikte kullanıldığında geçişler biraz zor olabilir. Bir durum gibi, her iki taraftaki IP adreslerini yeniden numaralandırmadan, uygulamaları Azure ile şirket içi arasında bölmeniz gerekir. Ayrıca, uygulamaların aynı ağda olduklarından farklı iletişim kurmasına izin vermeniz gerekir.

Yukarıdaki soruna yönelik bir çözüm alt ağ uzantısıdır. Bir ağı genişletmek, uygulamaların farklı fiziksel konumlarda bulunduklarında aynı yayın etki alanında konuşmasına olanak tanır ve ağ topolojinizi yeniden mimarmanıza gerek kalmaz. 

Ağınızı uzatmanın genel olarak iyi bir uygulama olmamasına karşın, kullanım örnekleri aşağıdaki durumlarda gerekli olabilir.

- **Aşamalı geçiş**: en yaygın senaryo geçişinizi aşamalandırmak istiyoruz. Daha önce birkaç uygulamayı ilk kez getirmek istediğinizde uygulamaların geri kalanını Azure 'a geçirin.
- **Gecikme süresi**: düşük gecikme süresi gereksinimleri, veri merkezinize olabildiğince yakın olduklarından emin olmak için bazı uygulamaların şirket içinde tutulması için başka bir neden olabilir.
- **Uyumluluk**: başka bir kullanım durumu, bazı uygulamalarınızı şirket içinde tutmaya yönelik uyumluluk gereksinimlerinize sahip olabilirsiniz.
 
> [!NOTE] 
> Gerekli olmadığı takdirde alt ağlarınızı genişletmemelisiniz. Alt ağlarınızı genişletmenin bir ara adım olmasını denemeniz gerekir. Zamanla, şirket içi ağınızda uygulamaları yeniden numaralandırmak ve bunları Azure 'a geçirmeniz gerekir.

Sonraki bölümde, alt ağlarınızı Azure 'a nasıl genişletebileceğinizi tartışacağız.


## <a name="extend-your-subnet-to-azure"></a>Alt ağlarınızı Azure 'a genişletin
 Şirket içi alt ağlarınızı, katman 3 yer paylaşımı ağ tabanlı bir çözüm kullanarak Azure 'a genişletebilirsiniz. Çoğu çözüm, katman 3 bir kaplama ağını kullanarak katman 2 ağını genişletmek için VXLAN gibi bir kaplama teknolojisini kullanır. Aşağıdaki diyagramda genelleştirilmiş bir çözüm gösterilmektedir. Bu çözümde, Azure ve şirket içi her iki tarafta da aynı alt ağ bulunur. 

![Alt ağ uzantısı örneği](./media/subnet-extension/subnet-extension.png)

Alt ağdaki IP adresleri Azure ve şirket içi VM 'lere atanır. Hem Azure hem de şirket içi, ağlarına takılan bir NVA 'ya sahiptir. Azure 'daki bir VM, şirket içi ağdaki bir VM ile iletişim kurmaya çalıştığında, Azure NVA paketi yakalar, kapsüller ve şirket içi ağa VPN/Express Route üzerinden gönderir. Şirket içi NVA, paketi alır, decapsulates ve ağ üzerinden amaçlanan alıcıya iletir. Dönüş trafiği benzer bir yol ve mantık kullanır.

Yukarıdaki örnekte, Azure NVA ve şirket içi NVA, her birinin arkasındaki IP adreslerini iletişim kurar ve öğrenirsiniz. Daha karmaşık ağlarda Ayrıca, NVA 'lar ve IP adresleri arasındaki eşlemeyi tutan bir eşleme hizmeti de olabilir. Bir NVA bir paket aldığında, hedef IP adresinin arkasındaki NVA 'nın adresini bulmak için eşleme hizmetini sorgular.

Sonraki bölümde, Azure 'da test ettiğimiz alt ağ uzantısı çözümlerinde Ayrıntılar bulacaksınız.

## <a name="next-steps"></a>Sonraki adımlar 
[Satıcı çözümlerini kullanarak alt ağlarınızı Azure 'a genişletin.](https://github.com/microsoft/Azure-LISP)