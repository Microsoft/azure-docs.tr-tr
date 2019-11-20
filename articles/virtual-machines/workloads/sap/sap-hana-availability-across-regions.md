---
title: Azure bölgelerinde kullanılabilirlik SAP HANA | Microsoft Docs
description: Birden çok Azure bölgesindeki Azure VM 'lerinde SAP HANA çalıştırırken kullanılabilirlik konularına genel bakış.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a393865038722f2fd7fa5e42334f8d5e760951
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078846"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Azure bölgeleri arasında kullanılabilirlik SAP HANA

Bu makalede, farklı Azure bölgelerinde SAP HANA kullanılabilirliğiyle ilgili senaryolar açıklanmaktadır. Azure bölgeleri arasındaki mesafe nedeniyle, birden fazla Azure bölgesinde SAP HANA kullanılabilirliği ayarlamak özel hususlar içerir.

## <a name="why-deploy-across-multiple-azure-regions"></a>Birden çok Azure bölgesinde dağıtım neden

Azure bölgeleri genellikle büyük uzaklıklarda ayrılır. Geopolitik bölgeye bağlı olarak, Azure bölgeleri arasındaki mesafe, Birleşik Devletler gibi yüzlerce mil veya hatta birkaç bin mil olabilir. Uzaklık nedeniyle, iki farklı Azure bölgesinde dağıtılan varlıklar arasındaki ağ trafiği önemli ölçüde ağ gidiş dönüş gecikmesi deneyimidir. Gecikme süresi, tipik SAP iş yükleri altındaki iki SAP HANA örneği arasında zaman uyumlu veri değişimini dışlayacak kadar önemlidir. 

Diğer yandan kuruluşlar genellikle birincil veri merkezi ve ikincil veri merkezi konumu arasında bir mesafe gereksinimi olur. Bir mesafe gereksinimi, daha geniş bir coğrafi konumda doğal bir olağanüstü durum oluşursa kullanılabilirlik sağlanmasına yardımcı olur. Bu örneklerde, Eylül ve Florida 'daki ve 2017 Ekim ' de yer alan ve Florida 'dan gelen acericanes Kuruluşunuzun en az bir uzaklık gereksinimi olabilir. Çoğu Azure müşterisi için, en küçük Uzaklık tanımı, [Azure bölgeleri](https://azure.microsoft.com/regions/)arasında kullanılabilirlik için tasarım yapmanızı gerektirir. İki Azure bölgesi arasındaki uzaklık HANA zaman uyumlu çoğaltma modunu kullanabilmek için çok büyük olduğundan, RTO ve RPO gereksinimleri, kullanılabilirlik yapılandırmalarının tek bir bölgede dağıtılmasını ve ardından ikinci bir saniye içinde ek dağıtımlarla daha fazla yararlanmasını zorlayabilir. geli.

Bu senaryoda dikkate alınması gereken başka bir değer de yük devretme ve istemci yeniden yönlendirme 'dir. Varsayım, iki farklı Azure bölgesindeki SAP HANA örnekleri arasındaki bir yük devretme işlemi her zaman el ile yük devretmektedir. SAP HANA sistem çoğaltmasının çoğaltma modu zaman uyumsuz olarak ayarlandığından, birincil HANA örneğinde yürütülen verilerin ikincil HANA örneğine henüz gönderilmediği bir olası olabilir. Bu nedenle, otomatik yük devretme çoğaltmanın zaman uyumsuz olduğu yapılandırmalara yönelik bir seçenek değildir. El ile denetlenen yük devretmeyle birlikte, bir yük devretme alıştırmada olduğu gibi, birincil taraftaki tüm kaydedilmiş verilerin diğer Azure bölgesine el ile geçiş yapmadan önce ikincil örneğe yaptığından emin olmak için ölçüler almanız gerekir.
 
Azure sanal ağ, farklı bir IP adresi aralığı kullanır. IP adresleri ikinci Azure bölgesinde dağıtılır. Bu nedenle, SAP HANA istemci yapılandırmasını değiştirmeniz ya da tercihen ad çözümlemesini değiştirmek için adımlar oluşturmanız gerekir. Bu şekilde, istemciler yeni ikincil sitenin sunucu IP adresine yönlendirilir. Daha fazla bilgi için, bkz. SAP makalesi [istemci bağlantı kurtarma,, devralındıktan sonra](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>İki Azure bölgesi arasında basit kullanılabilirlik

Tek bir bölge içinde herhangi bir kullanılabilirlik yapılandırması yerleştirmemelidir, ancak yine de bir olağanüstü durum oluşursa iş yüküne sahip olan talebe sahip olmaya devam edebilirsiniz. Bu senaryoların tipik örnekleri üretim dışı sistemlerdir. Sistemin bir gün boyunca veya bir gün boyunca yavaşlayamaz olsa da, sistemin 48 saat veya daha fazlası için kullanılamaz durumda olmasına izin verilmez. Kurulumu daha düşük maliyetli hale getirmek için VM 'de daha az önemli olan başka bir sistem çalıştırın. Diğer sistem bir hedef olarak çalışır. İkincil bölgedeki VM 'yi daha küçük olacak şekilde boyutlandırabilir ve verilerin önyükleme seçeneğini belirleyebilirsiniz. Yük devretme işlemi manuel olduğundan ve tüm uygulama yığınında yük devretmeye yönelik daha birçok adım sağladığından, VM 'yi kapatmak için ek süre, yeniden boyutlandırılması ve sonra sanal makineyi yeniden başlatmanız kabul edilebilir.

DR hedefini bir sanal makinede QA sistemiyle paylaşma senaryosunu kullanıyorsanız şu noktaları hesaba getirmeniz gerekir:

- Delta_datashipping ve logreplay ile bu tür bir senaryo için kullanılabilen iki [işlem modu](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) vardır
- Her iki işlem modu, verileri önceden yüklemeden farklı bellek gereksinimlerine sahiptir
- Delta_datashipping, logreplay 'un önyükleme seçeneği olmadan büyük ölçüde daha az bellek gerektirebilir. [SAP HANA Için sistem çoğaltmasını gerçekleştirme](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf) belgesine 4,3 bakın.
- Logreplay işlem modunun önyükleme olmadan bellek gereksinimi belirleyici değildir ve yüklenen columnstore yapılarına bağımlıdır. Olağanüstü durumlarda, birincil örnek için belleğin% 50 olması gerekebilir. Logreplay işlem modu için bellek, verilerin önceden yüklenmiş olmasını seçtiğinizden bağımsız olarak ayarlanmalıdır.


![İki bölge üzerinde iki VM 'nin diyagramı](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Bu yapılandırmada, HANA sistem çoğaltma modlarınızın zaman uyumsuz olduğu için bir RPO = 0 sağlayamıyoruz. RPO = 0 sağlamanız gerekiyorsa, bu yapılandırma seçeneğin yapılandırması değildir.

Yapılandırmada yapabileceğiniz küçük bir değişiklik, verileri önceden yükleme olarak yapılandırmak olabilir. Ancak, yük devretme el ile doğacak ve uygulama katmanlarının ikinci bölgeye taşınması gereken olgunun yanı sıra, verilerin önyük devretmesinin mantıklı olmaması olabilir. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kullanılabilirliği tek bir bölgede ve bölgeler arasında birleştirin 

Bölgeler içinde ve arasında kullanılabilirlik birleşimi şu etkenlere göre yönetilebilir:

- Bir Azure bölgesi içinde RPO = 0 gereksinimi.
- Kuruluş, daha büyük bir bölgeyi etkileyen ana doğal bir felaketler etkilenen genel işlemlere sahip değildir veya bu işlemleri yapabilir. Bu durum, son birkaç yıl içinde Ipçilere gelen bazı acericanlar için büyük bir durumdur.
- Azure kullanılabilirlik bölgelerinin sağlayabildiğinin açıkça ötesinde, birincil ve ikincil siteler arasında talep edilen düzenlemeler.

Bu durumlarda, HANA sistem çoğaltmasını kullanarak [SAP HANA çok katmanlı bir sistem çoğaltma yapılandırması](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) çağıran SAP 'yi ayarlayabilirsiniz. Mimari şöyle görünür:

![İki bölgenin üzerinde üç sanal makine diyagramı](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP, HANA 2,0 SPS3 ile [çok hedef sistem çoğaltması](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) sunmuştur. Çoklu hedef sistem çoğaltma, güncelleştirme senaryolarında bazı avantajlar sunar. Örneğin, kurtarma sitesi (bölge 2), ikincil HA sitesi bakım veya güncelleştirme için kapalıysa etkilenmez. HANA çoklu hedef sistem çoğaltma hakkında daha fazla bilgiyi [buradan](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html)edinebilirsiniz.
Çoklu hedef çoğaltma ile olası mimari şöyle görünür:

![İki bölgedeki üç sanal makine diyagramı, milti-Target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Kuruluşun, ikinci (DR) Azure bölgesinde yüksek kullanılabilirliğe sahip olma gereksinimleri varsa, mimari şöyle görünür:

![İki bölgedeki üç sanal makine diyagramı, milti-Target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


İşlem modu olarak logreplay kullanarak, bu yapılandırma birincil bölge içinde düşük RTO ile bir RPO = 0 sağlar. Ayrıca, ikinci bölgeye bir taşıma söz konusu olduğunda yapılandırma de yaprak RPO sağlar. İkinci bölgedeki RTO zamanları verilerin önceden yüklenmiş olup olmadığına bağlıdır. Birçok müşteri, bir test sistemini çalıştırmak için ikincil bölgedeki VM 'yi kullanır. Bu kullanım durumunda, veriler önceden yüklenemez.

> [!IMPORTANT]
> Farklı katmanlar arasındaki işlem modlarının homojen olması gerekir. Logreply 'ı, katman 1 ile katman 2 ile delta_datashipping arasında, sağlama katmanı 3 ' e kadar bir işlem modu olarak kullanamazsınız. Yalnızca bir veya tüm katmanlar için tutarlı olması gereken diğer işlem modunu seçebilirsiniz. Delta_datashipping, size bir RPO = 0 sağlamak için uygun olmadığından, çok katmanlı bir yapılandırma için tek makul işlem modu logreplay olarak kalır. İşlem modları ve bazı kısıtlamalar hakkında daha fazla bilgi için bkz. [SAP HANA sistem çoğaltması IÇIN SAP makalesi işlem modları](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da bu yapılandırmaların ayarlanmasına yönelik adım adım yönergeler için, bkz.:

- [Azure VM 'lerinde SAP HANA sistem çoğaltması ayarlama](sap-hana-high-availability.md)
- [Sistem çoğaltması kullanılarak SAP HANA için yüksek kullanılabilirlik](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
