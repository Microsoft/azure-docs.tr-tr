---
title: Azure 'da sanal makineler için gizli bilgi işlem
description: Gizli bilgi işlem iş yüklerinizi etkinleştirmek için Intel SGX donanımı hakkında bilgi edinin.
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: 554260b2a2760380d3bb2d91ee25b4a03bf2f1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551377"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Azure gizli bilgi işlem sanal makineleri (VM) genel bakış


Azure, sanallaştırılmış bir ortamda gizli bilgi işlem olanağı sunan ilk bulut sağlayıcıdır. Donanım ve uygulamanız arasında bir soyutlama katmanı görevi gören sanal makineler geliştirdik. İş yüklerini ölçekli ve artıklık ve kullanılabilirlik seçenekleriyle çalıştırabilirsiniz.  

## <a name="intel-sgx-enabled-virtual-machines"></a>Intel SGX özellikli sanal makineler

Azure gizli bilgi işlem sanal makinelerinde, CPU 'nun bir parçası, uygulamanızdaki kod ve verilerin bir bölümü için ayrılmıştır. Bu kısıtlı bölüm kuşkudır. 

![VM modeli](media/overview/hardware-backed-enclave.png)

Azure gizli bilgi işlem altyapısı şu anda çok sayıda sanal makinenin (VM) bir SKU 'sundan oluşur. Bu VM 'Ler Software Guard uzantısı (Intel SGX) ile Intel işlemcilerde çalışır. [INTEL SGX](https://intel.com/sgx) , gizli bilgi işlem ile daha iyi koruma sağlayan bir bileşendir. 

Günümüzde Azure, donanım tabanlı şifreleme oluşturma için Intel SGX teknolojisini temel alan [DCsv2 serisi](../virtual-machines/dcv2-series.md) sunmaktadır. Uygulama verilerinizi ve kullanımda olan kodları korumak için DCsv2 serisi VM 'lerde çalışacak güvenli şifreleme tabanlı uygulamalar oluşturabilirsiniz. 

Azure gizli bilgi işlem sanal makinelerini donanım tabanlı güvenilen şifrelerle dağıtma hakkında [daha fazla bilgi edinin](virtual-machine-solutions.md) .

## <a name="enclaves"></a>Kuşkukları

Kuşanın güvenliği, donanımın işlemcisinin ve belleğinin güvenli bir kısımındadır. Bir hata ayıklayıcı ile birlikte, şifreleme içinde veri veya kod görüntülemenin bir yolu yoktur. Güvenilmeyen kod, şifreleme belleğindeki içeriği değiştirme girişiminde bulunursa ortam devre dışı bırakılır ve işlemler reddedilir.

Temelde, güvenli bir kutu olarak bir şifreleme düşünün. Şifrelenmiş kod ve verileri kutuya yerleştirebilirsiniz. Kutunun dışından, hiçbir şey göremezsiniz. Şifreleme, verilerin şifresini çözmek için bir anahtar verirsiniz, daha sonra veriler, kuşdan gönderilmeden önce işlenir ve yeniden şifrelenir.

Her bir kuşın, şifreli sayfa önbelleğinin (EPC), her bir kuşın tutabileceğini belirten bellek miktarını belirleyen bir boyut kümesi vardır. Daha büyük DCsv2 sanal makinelerinde daha fazla EPC belleği vardır. VM boyutu başına en fazla EPC için [DCsv2 belirtimleri](../virtual-machines/dcv2-series.md) sayfasını okuyun.



### <a name="developing-applications-to-run-inside-enclaves"></a>Şifreleme içinde çalışacak uygulamalar geliştirme
Uygulama geliştirirken, kodunuzun ve verilerinizin parçalarını kuşkuya içinde korumak için [yazılım araçlarını](application-development.md) kullanabilirsiniz. Bu araçlar, kodunuzun ve verilerinizin güvenilir ortam dışındaki kimseler tarafından görüntülenememesini veya değiştirilmesini güvence altına alacak. 

## <a name="next-steps"></a>Sonraki Adımlar
- Azure gizli bilgi işlem sanal makinelerine çözüm dağıtmaya yönelik [en iyi uygulamaları okuyun](virtual-machine-solutions.md) .
- [DCsv2-Series sanal makinesini dağıtma](quick-create-portal.md)
- OE SDK kullanarak [bir kuşve uyumlu uygulama geliştirme](application-development.md)