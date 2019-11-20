---
title: Azure Service Fabric küme dağıtımını planlayın | Microsoft Docs
description: Azure 'da bir üretim Service Fabric küme dağıtımını planlama ve hazırlama hakkında bilgi edinin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: atsenthi
ms.openlocfilehash: a130e9bc8859360704c9be1c0a7fe066d2ed4567
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600008"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Küme dağıtımını planlayın ve hazırlayın

Üretim kümesi dağıtımı için planlama ve hazırlanma çok önemlidir.  Göz önünde bulundurulması gereken birçok etken vardır.  Bu makale, küme dağıtımınızı hazırlama adımlarında size yol gösterir.

## <a name="read-the-best-practices-information"></a>En iyi yöntemler bilgilerini okuyun
Azure Service Fabric uygulamalarını ve kümelerini başarıyla yönetmek için, üretim ortamınızın güvenilirliğini iyileştirmek üzere yapmanız gereken işlemler oldukça önerilir.  Daha fazla bilgi için [Service Fabric uygulama ve küme en iyi uygulamalarını](service-fabric-best-practices-overview.md)okuyun.

## <a name="select-the-os-for-the-cluster"></a>Küme için işletim sistemini seçin
Service Fabric, Windows Server veya Linux çalıştıran tüm VM 'lerde veya bilgisayarlarda Service Fabric kümelerinin oluşturulmasına izin verir.  Kümenizi dağıtılmadan önce işletim sistemini seçmeniz gerekir:  Windows veya Linux.  Kümedeki her düğüm (sanal makine) aynı işletim sistemini çalıştırır, aynı kümede Windows ve Linux VM 'Leri karıştıramazsınız.

## <a name="capacity-planning"></a>Kapasite planlaması
Herhangi bir üretim dağıtımı için kapasite planlaması önemli bir adımdır. Bu süreç kapsamında dikkat etmeniz gerekenler şunlardır:

* Kümeniz için ilk düğüm türü sayısı 
* Her düğüm türünün özellikleri (boyut, örnek sayısı, birincil, internet 'e yönelik ve VM sayısı vb.)
* Kümenin güvenilirlik ve dayanıklılık özellikleri

### <a name="select-the-initial-number-of-node-types"></a>Düğüm türlerinin başlangıç sayısını seçin
İlk olarak, oluşturduğunuz kümenin ne için kullanılacağını belirlemeniz gerekir. Bu kümeye dağıtmayı planladığınız uygulama türleri nelerdir? Uygulamanızda birden fazla hizmet var mı ve bunların herkese açık veya internet 'e yönelik olması gerekiyor mu? Hizmetlerinizin (uygulamanızı oluşturan) daha büyük RAM veya daha yüksek CPU döngüleri gibi farklı altyapı gereksinimlerine sahip olması gerekir mi? Service Fabric kümesi birden fazla düğüm türünden oluşabilir: birincil düğüm türü ve bir veya daha fazla birincil düğüm türü. Her düğüm türü bir sanal makine ölçek kümesi ile eşleştirilir. Daha sonra, her düğüm türünün ölçeği birbirinden bağımsız olarak artırılabilir veya azaltılabilir, her düğüm türünde farklı bağlantı noktası kümeleri açık olabilir ve farklı kapasite ölçümleri yapılabilir. Belirli hizmetleri belirli düğüm türlerine kısıtlamak için [düğüm özellikleri ve yerleştirme kısıtlamaları][placementconstraints] ayarlanabilir.  Daha fazla bilgi için, [kümenizin başlamak için gereken düğüm türlerinin sayısını](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)okuyun.

### <a name="select-node-properties-for-each-node-type"></a>Her düğüm türü için düğüm özelliklerini seçin
Düğüm türleri, ilişkili ölçek kümesindeki VM 'lerin SKU 'sunu, numarasını ve özelliklerini tanımlar.

Her düğüm türü için en az sanal makine boyutu, düğüm türü için seçtiğiniz [dayanıklılık katmanına][durability] göre belirlenir.

Birincil düğüm türü için en az sanal makine sayısı, seçtiğiniz [güvenilirlik katmanına][reliability] göre belirlenir.

Birincil düğüm [türleri](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)için en düşük önerilere, [birincil olmayan düğüm türlerinde durum](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)bilgisiz iş yüklerine ve [birincil olmayan düğüm türlerinde durum bilgisiz iş yüklerine](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)bakın. 

Düğüm sayısının alt sınırı, bu düğüm türünde çalıştırmak istediğiniz uygulama/hizmetlerin çoğaltmaları sayısını temel almalıdır.  [Service Fabric uygulamalar Için kapasite planlaması](service-fabric-capacity-planning.md) , uygulamalarınızı çalıştırmak için ihtiyacınız olan kaynakları tahmin etmenize yardımcı olur. Daha sonra değişen uygulama iş yükünü ayarlamak için kümenin ölçeğini değiştirebilir veya azaltabilirsiniz. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Küme için dayanıklılık ve güvenilirlik düzeylerini seçin
Dayanıklılık katmanı, sistem sanal makinelerinizin temel alınan Azure altyapısıyla sahip olduğu ayrıcalıkları göstermek için kullanılır. Bu ayrıcalık, birincil düğüm türünde, sistem hizmetleri ve durum bilgisi olan hizmetleriniz için çekirdek gereksinimlerini etkileyen tüm VM düzeyi altyapı isteklerini (VM yeniden başlatma, VM yeniden görüntüsü veya VM geçişi gibi) duraklatmaya olanak Service Fabric tanır. Birincil olmayan düğüm türlerinde, bu ayrıcalık, durum bilgisi olan hizmetlerinizin çekirdek gereksinimlerini etkileyen tüm VM düzeyi altyapı isteklerini (VM yeniden başlatma, VM yeniden görüntüsü ve VM geçişi gibi) duraklatmaya Service Fabric olanak tanır.  Hangi düzeyin kullanılacağı ve ne zaman kullanılacağı ile ilgili farklı düzeylerin ve önerilerin avantajları için, [kümenin dayanıklılık özelliklerine][durability]bakın.

Güvenilirlik katmanı, birincil düğüm türünde bu kümede çalıştırmak istediğiniz sistem hizmetlerinin çoğaltmaları sayısını ayarlamak için kullanılır. Kopyaların ne kadar fazla olması durumunda, sistem hizmetleri kümenizde daha güvenilir.  Hangi düzeyin kullanılacağı ve ne zaman kullanılacağı ile ilgili farklı düzeylerin ve önerilerin avantajları için, [kümenin güvenilirlik özelliklerine][reliability]bakın. 

## <a name="enable-reverse-proxy-andor-dns"></a>Ters proxy ve/veya DNS 'yi etkinleştir
Bir kümedeki düğümler aynı yerel ağda olduğundan, bir küme içindeki birbirlerine bağlanan hizmetler genellikle diğer hizmetlerin uç noktalarına erişebilir. Hizmetler arasında bağlanmayı kolaylaştırmak için Service Fabric ek hizmetler sağlar: Bir [DNS hizmeti](service-fabric-dnsservice.md) ve [ters proxy hizmeti](service-fabric-reverseproxy.md).  Bir küme dağıtıldığında her iki hizmet de etkinleştirilebilir.

Birçok hizmet, özellikle Kapsayıcılı hizmetler, mevcut bir URL adına sahip olduğundan, bu işlemi standart DNS Protokolü (Adlandırma Hizmeti protokolü yerine) kullanarak çözebiliyor ve özellikle uygulamanın "yükselt ve Shift" senaryolarında kullanışlıdır. Bu, tam olarak DNS hizmetinin yaptığı şeydir. DNS adlarını bir hizmet adıyla eşlemenizi sağlar ve bu nedenle uç nokta IP adreslerini çözümleyebilir.

Ters proxy, HTTP uç noktalarını (HTTPS dahil) sunan kümedeki hizmetleri ele alınmaktadır. Ters proxy, belirli bir URI biçimi sağlayarak diğer hizmetlerin çağrılmasını büyük ölçüde basitleştirir.  Ters proxy, bir hizmetin birbirleriyle iletişim kurması için gerekli olan Resolve, Connect ve retry adımlarını da işler.

## <a name="prepare-for-disaster-recovery"></a>Olağanüstü Durum Kurtarmaya Hazırlanma
Yüksek kullanılabilirlik sunmaya yönelik kritik bir bölüm, hizmetlerin tüm farklı türdeki hataların varlığını sürdürmesini sağlamaktır. Bu özellikle, denetim için planlanmamış ve dışındaki hatalarda önemlidir. [Olağanüstü durum kurtarma Için hazırlanma](service-fabric-disaster-recovery.md) , Modellenmemiş ve doğru yönetilmiyorsa olağanüstü durumlar olabilecek bazı yaygın hata modlarını açıklar. Ayrıca, olağanüstü bir durum oluşması durumunda yapılacak azaltmaları ve eylemler de anlatılmaktadır.

## <a name="production-readiness-checklist"></a>Üretim hazırlığı denetim listesi
Uygulamanız ve kümeniz üretim trafiği almaya hazırlanıyor mi? Kümenizin üretime dağıtılmasından önce [Üretim hazırlığı denetim listesini](service-fabric-production-readiness-checklist.md)çalıştırın. Bu denetim listesindeki öğeler aracılığıyla çalışarak uygulamanızın ve kümenizin sorunsuz çalışmasını sağlayın. Üretime geçmeden önce bu öğelerin tümünün kullanıma alınmasını önemle öneririz.

## <a name="next-steps"></a>Sonraki adımlar
* [Windows çalıştıran bir Service Fabric kümesi oluşturma](service-fabric-best-practices-overview.md)
* [Linux çalıştıran bir Service Fabric kümesi oluşturma](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster