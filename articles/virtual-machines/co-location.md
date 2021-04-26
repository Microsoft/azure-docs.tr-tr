---
title: Yakınlık yerleştirme grupları
description: Azure 'da yakınlık yerleşimi gruplarını kullanma hakkında bilgi edinin.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: conceptual
ms.date: 3/07/2021
ms.reviewer: zivr
ms.openlocfilehash: 1a65a1e4ecd989f3a7c4968c424472c3c6dfe472
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559085"
---
# <a name="proximity-placement-groups"></a>Yakınlık yerleştirme grupları

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

Ayrıca, mevcut bir kaynağı bir yakınlık yerleşimi grubuna taşıyabilirsiniz. Bir kaynağı bir yakınlık yerleşimi grubuna taşırken, bir varlık büyük olasılıkla birlikte bulundurma kısıtlamasını karşılamak için bölgedeki farklı bir veri merkezine yeniden dağıtılmasından bu yana varlığı durdurmanız (serbest bırakma) gerekir. 

Kullanılabilirlik kümeleri ve sanal makine ölçek kümeleri söz konusu olduğunda, yakınlık yerleşimi grubunu ayrı sanal makineler yerine kaynak düzeyinde ayarlamanız gerekir. 

Yakınlık yerleştirme grubu, sabitleme mekanizması yerine bir ortak konum kısıtlamasıdır. Onu kullanmak için ilk kaynağın dağıtımıyla belirli bir veri merkezine sabitlenmiştir. Yakınlık yerleşimi grubunu kullanan tüm kaynaklar durdurulduktan (serbest bırakıldığında) veya silindikten sonra artık sabitlenemez. Bu nedenle, birden çok VM serisine sahip bir yakınlık yerleşimi grubu kullanırken, mümkün olduğunda bir şablonda gerekli türlerin tümünü belirtmek önemlidir veya dağıtım sırasını izleyerek başarılı bir dağıtıma yönelik olma olasılığını artırır. Dağıtımınız başarısız olursa, dağıtımı ilk boyut olarak başarısız olan VM boyutuyla birlikte yeniden başlatın.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Yakınlık yerleşimi grupları kullanılırken beklenmeniz gerekenler 
Yakınlık yerleşimi grupları aynı veri merkezinde birlikte bulundurulan bir konum sunar. Bununla birlikte, yakınlık yerleşimi grupları ek bir dağıtım kısıtlamasını temsil ettiğinden, ayırma arızaları meydana gelebilir. Yakınlık yerleşimi gruplarını kullanırken ayırma başarısızlıklarını görebileceğiniz birkaç kullanım durumu vardır:

- Yakınlık yerleşimi grubundaki ilk sanal makineyi sorduğunuz zaman, veri merkezi otomatik olarak seçilir. Bazı durumlarda, farklı bir sanal makine SKU 'SU için ikinci bir istek, bu veri merkezinde yoksa başarısız olabilir. Bu durumda, bir **OverconstrainedAllocationRequest** hatası döndürülür. Bunu önlemek için, SKU 'larınızı dağıttığınız sırayı değiştirmeyi deneyin veya her iki kaynağın tek bir ARM şablonu kullanılarak dağıtılmasını sağlayabilirsiniz.
-   Sanal makine örneklerini eklediğiniz ve kaldırdığınız elastik iş yükleri söz konusu olduğunda, dağıtımınızda bir yakınlık yerleşimi grubu kısıtlamasının olması, bu isteğin karşılamamasından kaynaklanan, **Allocationfailure** hatasına neden olabilir. 
- Gerekli olduğu gibi sanal makinelerinizin durdurulması (serbest bırakma) ve başlatılması, esneklik elde etmenin başka bir yoludur. Bir VM 'yi durdurup (serbest bırakma) bir VM 'yi durdurup yeniden başlatmak, bir **Allocationfailure** hatasına neden olabilir.

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Planlı bakım ve yakınlık yerleştirme grupları

Azure veri merkezinde donanımla yetki alma gibi planlı bakım olayları, yakınlık yerleştirme gruplarındaki kaynakların hizalamasını etkileyebilir. Kaynaklar farklı bir veri merkezine taşınabilir ve bu, yakınlık yerleşimi grubuyla ilişkili birlikte bulundurma ve gecikme beklentilerini kesintiye uğratmadan olabilir.

### <a name="check-the-alignment-status"></a>Hizalama durumunu denetleme

Yakınlık yerleştirme gruplarınızın hizalama durumunu denetlemek için aşağıdakileri yapabilirsiniz.


- Yakınlık yerleşimi grubu birlikte bulundurma durumu Portal, CLı ve PowerShell kullanılarak görüntülenebilir.

    -   PowerShell kullanılırken, birlikte bulunan '-eklenebilir Cationstatus ' parametresi dahil olmak üzere Get-AzProximityPlacementGroup cmdlet 'i kullanılarak ortak konum durumu elde edilebilir.

    -   CLı kullanırken, birlikte `az ppg show` bulundurulan durum '--include-birlikte bulundurma-Status ' parametresi eklenerek kullanılarak elde edilebilir.

- Her yakınlık yerleşimi grubu için, bir birlikte bulundurma **durumu** özelliği, gruplandırılmış kaynakların geçerli hizalama durumu özetini sağlar. 

    - **Hizalanmış**: kaynak, yakınlık yerleşimi grubunun gecikme süresi içinde.

    - **Bilinmiyor**: VM kaynaklarından en az biri serbest bırakıldı. Başarılı bir şekilde yeniden başlattıktan sonra durum **hizalı** öğesine geri döner.

    - **Hizalanmamış**: en az bir VM kaynağı yakınlık yerleşimi grubuyla hizalı değil. Hizalı olmayan belirli kaynaklar, üyelik bölümünde ayrı olarak da çağrılacaktır

- Kullanılabilirlik kümeleri için, kullanılabilirlik kümesi Genel Bakış sayfasında tek tek VM 'lerin hizalaması hakkındaki bilgileri görebilirsiniz.

- Ölçek Kümeleri için, ayrı örneklerin hizalaması hakkındaki bilgiler, ölçek kümesinin **genel bakış** sayfasının **örnekler** sekmesinde görülebilir. 


### <a name="re-align-resources"></a>Kaynakları yeniden Hizala 

Bir yakınlık yerleşimi grubu ise `Not Aligned` , etkilenen kaynakları başlatabilir ve sonra da yeniden başlatabilirsiniz. VM bir kullanılabilirlik kümesi veya ölçek kümesi içinde ise, kullanılabilirlik kümesindeki veya ölçek kümesindeki tüm VM 'Lerin yeniden başlatılmadan önce durdurulmaları gerekir.

Dağıtım kısıtlamalarından dolayı bir ayırma hatası varsa, etkilenen yakınlık yerleşimi grubundaki tüm kaynakları (hizalanmış kaynaklar dahil) ilk olarak iptal etmeniz ve sonra hizalamayı geri yüklemek için yeniden başlatmanız gerekebilir.

## <a name="best-practices"></a>En iyi uygulamalar 
- En düşük gecikme süresi için, hızlandırılmış ağlarla birlikte yakınlık yerleştirme gruplarını kullanın. Daha fazla bilgi için bkz. [hızlandırılmış ağ Ile Linux sanal makinesi oluşturma](../virtual-network/create-vm-accelerated-networking-cli.md) veya [hızlandırılmış ağ ile Windows sanal makinesi oluşturma](../virtual-network/create-vm-accelerated-networking-powershell.md).
- Tüm VM boyutlarını tek bir şablonda dağıtın. İhtiyaç duyduğunuz tüm VM SKU 'Larını ve boyutlarını desteklemeyen donanımlarda sahanın önüne geçmek için, tüm uygulama katmanlarını tek bir şablona dahil edin, böylece bunların hepsi aynı anda dağıtılacaktır.
- Dağıtımınızı PowerShell, CLı veya SDK kullanarak yaparsanız, bir ayırma hatası alabilirsiniz `OverconstrainedAllocationRequest` . Bu durumda, mevcut tüm VM 'Leri durdurup serbest bırakabilir ve dağıtım betiğindeki sırayı, başarısız olan VM SKU 'SU/boyutlarına başlayacak şekilde değiştirmelisiniz. 
- VM 'lerden silinen mevcut bir yerleştirme grubunu yeniden kullandığınızda, VM 'Leri eklemeden önce silme işleminin tam tamamlanmasını bekleyin.
- Gecikme süresi ilk önceliğiniz ise, VM 'Leri bir yakınlık yerleşimi grubuna ve çözümün tamamına bir kullanılabilirlik alanına yerleştirin. Ancak dayanıklılık en üst önceliğiniz ise, örneklerinizi birden çok kullanılabilirlik alanına (tek bir yakınlık yerleştirme grubu bölgelere yayılamaz) yayın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure CLI](./linux/proximity-placement-groups.md) veya [PowerShell](./windows/proximity-placement-groups.md) kullanarak BIR yakınlık yerleşimi grubuna VM dağıtma

[Ağ gecikmesini test](../virtual-network/virtual-network-test-latency.md)etme hakkında bilgi edinin.

[Ağ aktarım hızını en uygun hale getirmeyi](../virtual-network/virtual-network-optimize-network-bandwidth.md)öğrenin.  

[SAP uygulamalarıyla yakınlık yerleştirme gruplarını nasıl kullanacağınızı](./workloads/sap/sap-proximity-placement-scenarios.md)öğrenin.