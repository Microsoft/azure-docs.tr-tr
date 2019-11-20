---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171121"
---
VM 'Lerin tek bir bölgeye yerleştirilmesi, örnekler arasındaki fiziksel mesafeyi azaltır. Bunları tek bir kullanılabilirlik alanına koymak, bunları fiziksel olarak bir araya getirir. Ancak, Azure ayak izi büyüdükçe, tek bir kullanılabilirlik alanı birden fazla fiziksel veri merkezine yayılabilir ve bu da uygulamanızı etkileyen bir ağ gecikme süresi oluşmasına neden olabilir. 

Olası en düşük gecikme süresini elde etmek üzere VM 'Leri olabildiğince yakın bir şekilde almak için, bunları bir yakınlık yerleşimi grubu içinde dağıtmanız gerekir.

Yakınlık yerleşimi grubu, Azure işlem kaynaklarının fiziksel olarak birbirlerine yakın bir yerde bulunduğundan emin olmak için kullanılan mantıksal bir gruplandırmadır. Yakınlık yerleşimi grupları, düşük gecikme süresinin gereksinim olduğu iş yükleri için faydalıdır.


- Tek başına VM 'Ler arasında düşük gecikme süresi.
- Tek bir kullanılabilirlik kümesindeki veya bir sanal makine ölçek kümesindeki VM 'Ler arasında düşük gecikme süresi. 
- Tek başına VM 'Ler, birden çok kullanılabilirlik kümesindeki VM 'Ler veya birden çok ölçek kümesi arasında düşük gecikme süresi. Çok katmanlı bir uygulamayı bir araya getirmek için tek bir yerleştirme grubunda birden çok işlem kaynağınız olabilir. 
- Farklı donanım türleri kullanan birden çok uygulama katmanı arasında düşük gecikme süresi. Örneğin, arka ucu bir kullanılabilirlik kümesinde M serisi ile ve bir bir D serisi örneğinde, bir ölçek kümesinde, tek bir yakınlık yerleşimi grubunda ön uç ile çalışır.


![Yakınlık yerleşimi grupları için grafik](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Yakınlık yerleştirme gruplarını kullanma 

Yakınlık yerleşimi grubu, Azure 'da yeni bir kaynak türüdür. Diğer kaynaklarla kullanmadan önce bir tane oluşturmanız gerekir. Oluşturulduktan sonra sanal makineler, kullanılabilirlik kümeleri veya sanal makine ölçek kümeleri ile birlikte kullanılabilir. Yakınlık yerleşimi grubu KIMLIĞINI sağlayan işlem kaynaklarını oluştururken bir yakınlık yerleşimi grubu belirtirsiniz. 

Ayrıca, mevcut bir kaynağı bir yakınlık yerleşimi grubuna taşıyabilirsiniz. Bir kaynağı bir yakınlık yerleşimi grubuna taşırken, bir varlık potansiyel olarak bölgede farklı bir veri merkezine dağıtıldıktan sonra, birlikte bulundurma kısıtlamasını karşılamadan önce varlığı durdurmanız (serbest bırakma) gerekir. 

Kullanılabilirlik kümeleri ve sanal makine ölçek kümeleri söz konusu olduğunda, yakınlık yerleşimi grubunu ayrı sanal makineler yerine kaynak düzeyinde ayarlamanız gerekir. 

Yakınlık yerleşimi grubu, sabitleme mekanizması yerine bir birlikte bulundurma kısıtlamasıdır. Onu kullanmak için ilk kaynağın dağıtımıyla belirli bir veri merkezine sabitlenmiştir. Yakınlık yerleşimi grubunu kullanan tüm kaynaklar durdurulduktan (serbest bırakıldığında) veya silindikten sonra artık sabitlenemez. Bu nedenle, birden çok VM serisine sahip bir yakınlık yerleşimi grubu kullanırken, mümkün olduğunda bir şablonda gerekli türlerin tümünü belirtmek önemlidir veya dağıtım sırasını izleyerek başarılı bir dağıtıma yönelik olma olasılığını artırır. Dağıtımınız başarısız olursa, dağıtımı ilk boyut olarak başarısız olan VM boyutuyla birlikte yeniden başlatın.


## <a name="best-practices"></a>En iyi uygulamalar 
- En düşük gecikme süresi için, hızlandırılmış ağlarla birlikte yakınlık yerleştirme gruplarını kullanın. Daha fazla bilgi için bkz. [hızlandırılmış ağ Ile Linux sanal makinesi oluşturma](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veya [hızlandırılmış ağ ile Windows sanal makinesi oluşturma](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Tüm VM boyutlarını tek bir şablonda dağıtın. İhtiyaç duyduğunuz tüm VM SKU 'Larını ve boyutlarını desteklemeyen donanımlarda sahanın önüne geçmek için, tüm uygulama katmanlarını tek bir şablona dahil edin, böylece bunların hepsi aynı anda dağıtılacaktır.
- Dağıtımınızı PowerShell, CLı veya SDK kullanarak yaparsanız, `OverconstrainedAllocationRequest`bir ayırma hatası alabilirsiniz. Bu durumda, mevcut tüm VM 'Leri durdurup serbest bırakabilir ve dağıtım betiğindeki sırayı, başarısız olan VM SKU 'SU/boyutlarına başlayacak şekilde değiştirmelisiniz. 
- VM 'lerden silinen mevcut bir yerleştirme grubunu yeniden kullandığınızda, VM 'Leri eklemeden önce silme işleminin tam tamamlanmasını bekleyin.
- Gecikme süresi ilk önceliğiniz ise, VM 'Leri bir yakınlık yerleşimi grubuna ve çözümün tamamına bir kullanılabilirlik alanına yerleştirin. Ancak dayanıklılık en üst önceliğiniz ise, örneklerinizi birden çok kullanılabilirlik alanına (tek bir yakınlık yerleştirme grubu bölgelere yayılamaz) yayın.