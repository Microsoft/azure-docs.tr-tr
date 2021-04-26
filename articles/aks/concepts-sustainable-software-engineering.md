---
title: Kavramlar-Azure Kubernetes hizmetlerinde (AKS) sürdürülebilirlik Yazılım Mühendisliği
description: Azure Kubernetes Service (AKS) ' de sürdürülebilirlik yazılım mühendisliği hakkında bilgi edinin.
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c43c65dfa2f3930510bd59aaa24c798525bd691b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011500"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde sürdürülebilir Yazılım Mühendisliği ilkeleri

Sürdürülebilir Yazılım Mühendisliği ilkeleri, sürdürülebilir uygulamalar tanımlamanıza, oluşturmanıza ve çalıştırmanıza yardımcı olan bir uzmanlık kümesidir. Genel amaç, uygulamanızın her yönüyle karbon izi azaltmaktır. [Sürdürülebilirlik yazılım mühendisliğinin ilkeleri][principles-sse] , sürdürülebilir yazılım mühendisliğinin ilkelerine genel bakış içerir.

Sürdürülebilir Yazılım Mühendisliği, öncelikler ve odağdaki bir vardiyadır. Çoğu durumda, çoğu yazılımın tasarlanması ve çalıştırılması, hızlı performans ve düşük gecikme süresine göre önemli bir yoldur. Bu sırada, sürdürülebilir yazılım mühendisliği mümkün olduğunca fazla önem duyulmadan daha fazla bilgi vermez. Aşağıdakileri dikkate alın:

* Sürdürülebilir Yazılım Mühendisliği ilkelerini uygulamak, toplam ağ yolculuğu gibi daha hızlı performans veya daha düşük gecikme süresi sağlayabilir. 
* Bilgi azaltma, düşük öncelikli iş yüklerini erteleme gibi yavaş performans veya daha fazla gecikme süresine neden olabilir. 

Uygulamanıza sürdürülebilir Yazılım Mühendisliği ilkelerini uygulamadan önce uygulamanızın önceliklerini, ihtiyaçlarını ve dengeliğini gözden geçirin.

## <a name="measure-and-optimize"></a>Ölçme ve iyileştirme

AKS kümelerinizin boyutunu düşürmek için, kümenizin kaynaklarının nasıl kullanıldığını anlamanız gerekir. [Azure izleyici][azure-monitor] , kümenizin bellek ve CPU kullanımı gibi kaynak kullanımı hakkında ayrıntılı bilgi sağlar. Bu veriler, kümenizin görünümünü azaltma kararına bilgi verir ve değişikliklerinizin etkisini azaltır. 

Ayrıca, tüm Azure kaynaklarınızın boyutunu görmek için [Microsoft Suslebilirliği Hesaplayıcı][sustainability-calculator] ' yı da yükleyebilirsiniz.

## <a name="increase-resource-utilization"></a>Kaynak kullanımını artırma

Karbon parmak izini düşürmek için bir yaklaşım, boşta kalma süresini azaltmaktır. Boşta kalma süresini azaltmak, işlem kaynaklarınızın kullanımını artırmayı içerir. Örnek:
1. Kümenizde dört düğüm vardı, her biri %50 kapasitesinde çalışmaktadır. Bu nedenle, düğümlerinizin dört oranında kullanılmayan %50 kapasite boşta kalır. 
1. Kümenizi, her biri aynı iş yükü ile %67 kapasiteye çalışan üç düğüme azalttı. Kullanılmayan kapasitenizi her düğümde %33 oranında düşürdü ve kullanımınızı artırmış olursunuz.

> [!IMPORTANT]
> Kümenizdeki kaynakların değiştirilmesini düşünürken, [sistem havuzlarınızın][system-pools] , kümenizin çekirdek sistem bileşenlerinin kararlılığını sürdürmek için yeterli kaynaklara sahip olduğunu doğrulayın. Kümenizin kaynaklarını kümenizin kararsız hale gelebileceği noktaya göre **hiçbir şekilde azaltmayın** .

Kümenizin kullanımını inceledikten sonra, [birden çok düğüm havuzu][multiple-node-pools]tarafından sunulan özellikleri kullanmayı göz önünde bulundurun: 

* Düğüm boyutlandırma

    Belirli CPU ve bellek profilleriyle düğüm havuzları tanımlamak için [düğüm boyutlandırmayı][node-sizing] kullanın, böylece düğümlerinizi iş yükü gereksinimlerinize uyarlayabilirsiniz. Düğümlerinizi iş yükü gereksinimlerinize göre boyutlandırarak, daha yüksek bir şekilde birkaç düğüm çalıştırabilirsiniz. 

* Küme ölçeklendirme

    Kümenizin [ölçeklendirme][scale]şeklini yapılandırın. Yapılandırmanızı temel alarak kümenizi otomatik olarak ölçeklendirmek için [yatay Pod otomatik Scaler][scale-horizontal] ve [cluster otomatik Scaler][scale-auto] ' nı kullanın. Kümenizin, kümenizin iş yükündeki değişikliklerle eşitlenmiş durumdayken, tüm düğümlerinizi yüksek bir şekilde çalışır durumda tutmak için kümenizin nasıl ölçeklendirileolduğunu denetleyin. 

* Spot havuzlar

    Bir iş yükünün ani kesintiler veya sonlandırmalar için dayanıklı olduğu durumlarda, [spot havuzlar][spot-pools]kullanabilirsiniz. Spot havuzlar Azure 'daki boş kapasiteden yararlanır. Örneğin, spot havuzlar toplu işler veya geliştirme ortamları için iyi çalışabilir.

> [!NOTE]
>Kullanım artırma, [her düğümdeki kaynak ayırmaları][resource-reservations]tarafından tüketilen enerji miktarını azaltan fazla düğümleri de azaltabilir.

Son olarak, uygulamalarınızın Kubernetes bildirimlerinde CPU ve bellek *isteklerini* ve *sınırlarını* gözden geçirin. 
* Bellek ve CPU değerlerini düşürdükçe, diğer iş yüklerini çalıştırmak için kümede daha fazla bellek ve CPU vardır. 
* Daha düşük CPU ve bellekle daha fazla iş yükü çalıştırırken, kümeniz daha seyrek ayrılmış hale gelir ve bu da kullanımınızı artırır. 

Uygulamalarınız için CPU ve bellek azaltıldığında, CPU ve bellek değerlerini çok düşük olarak ayarlarsanız uygulamalarınızın davranışı düşebilir veya kararsız hale gelebilir. CPU ve bellek *isteklerini* ve *sınırlarını* değiştirmeden önce, değerlerin uygun şekilde ayarlandığından emin olmak için bazı benchsýnamalar çalıştırın. Bu değerleri hiçbir şekilde uygulama dengesizme noktasına düşürmeyin.

## <a name="reduce-network-travel"></a>Ağ yolculuğu azaltma

Kümelerinize ve bu kaynaklardan gelen uzaklığı azaltarak, ağ cihazlarına göre bilgi ve elektrik tüketimini azaltabilirsiniz. Ağ trafiğinizi inceledikten sonra, ağ trafiğinizin kaynağına yakın [bölgelerde][regions] kümeler oluşturmayı göz önünde bulundurun. [Azure Traffic Manager][azure-traffic-manager] kullanarak trafiği en yakın kümeye ve [yakınlık yerleştirme gruplarına][proiximity-placement-groups] yönlendirebilir ve Azure kaynakları arasındaki mesafeyi azaltabilirsiniz.

> [!IMPORTANT]
> Kümenizin ağ üzerinde değişiklik yapmayı düşünürken, ağ yolculuğu 'nı toplantı iş yükü gereksinimlerinin maliyetiyle hiçbir zaman azaltmayın. Örneğin, [kullanılabilirlik alanları][availability-zones] kullanılırken, kümenizde daha fazla ağ yolculuğu olur, iş yükü gereksinimlerinin işlenmesi için kullanılabilirlik alanları gerekli olabilir.

## <a name="demand-shaping"></a>Talep şekillendirme

Mümkün olduğunda, kümenizin kaynaklarına yönelik talebi, fazla kapasiteyi kullanabileceğiniz saatlere veya bölgelere değiştirmeyi göz önünde bulundurun. Örneğin, şunları göz önünde bulundurun:
* Bir toplu işin çalıştırılacağı saat veya bölgeyi değiştirme.
* [Spot havuzları][spot-pools]kullanma. 
* Anında işleme gerektirmeyen çalışan iş yüklerini erteleyin için uygulamanızı bir kuyruk kullanacak şekilde yeniden düzenleme.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bahsedilen AKS özellikleri hakkında daha fazla bilgi edinin:

* [Birden çok düğüm havuzu][multiple-node-pools]
* [Düğüm boyutlandırma][node-sizing]
* [Küme ölçekleme][scale]
* [Yatay pod otomatik ölçeklendiricisi][scale-horizontal]
* [Küme otomatik ölçeklendiricisi][scale-auto]
* [Spot havuzlar][spot-pools]
* [Sistem havuzları][system-pools]
* [Kaynak ayırmaları][resource-reservations]
* [Yakınlık yerleştirme grupları][proiximity-placement-groups]
* [Kullanılabilirlik Alanları][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/