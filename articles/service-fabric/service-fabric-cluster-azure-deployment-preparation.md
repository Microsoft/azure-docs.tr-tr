---
title: Azure Service Fabric küme dağıtımını planlayın
description: Azure 'da bir üretim Service Fabric küme dağıtımını planlama ve hazırlama hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 82521487b9a3e9438784e010a32cf6df8e7be2ef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046326"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Küme dağıtımını planlayın ve hazırlayın

Üretim kümesi dağıtımı için planlama ve hazırlanma çok önemlidir.  Göz önünde bulundurulması gereken birçok etken vardır.  Bu makale, küme dağıtımınızı hazırlama adımlarında size yol gösterir.

## <a name="read-the-best-practices-information"></a>En iyi yöntemler bilgilerini okuyun
Azure Service Fabric uygulamalarını ve kümelerini başarıyla yönetmek için, üretim ortamınızın güvenilirliğini iyileştirmek üzere yapmanız gereken işlemler oldukça önerilir.  Daha fazla bilgi için [Service Fabric uygulama ve küme en iyi uygulamalarını](./service-fabric-best-practices-security.md)okuyun.

## <a name="select-the-os-for-the-cluster"></a>Küme için işletim sistemini seçin
Service Fabric, Windows Server veya Linux çalıştıran tüm VM 'lerde veya bilgisayarlarda Service Fabric kümelerinin oluşturulmasına izin verir.  Kümenizi dağıtılmadan önce, işletim sistemi: Windows veya Linux ' u seçmeniz gerekir.  Kümedeki her düğüm (sanal makine) aynı işletim sistemini çalıştırır, aynı kümede Windows ve Linux VM 'Leri karıştıramazsınız.

## <a name="capacity-planning"></a>Kapasite planlaması
Herhangi bir üretim dağıtımı için kapsite planlaması önemli bir adımdır. Bu süreç kapsamında dikkat etmeniz gerekenler şunlardır:

* Kümeniz için ilk düğüm türü sayısı 
* Her düğüm türünün özellikleri (boyut, örnek sayısı, birincil, internet 'e yönelik ve VM sayısı vb.)
* Kümenin güvenilirlik ve dayanıklılık özellikleri

### <a name="select-the-initial-number-of-node-types"></a>Düğüm türlerinin başlangıç sayısını seçin
İlk olarak, oluşturduğunuz kümenin ne için kullanılacağını belirlemeniz gerekir. Bu kümeye dağıtmayı planladığınız uygulama türleri nelerdir? Uygulamanızda birden fazla hizmet var mı ve bunların herkese açık veya internet 'e yönelik olması gerekiyor mu? Hizmetlerinizin (uygulamanızı oluşturan) daha büyük RAM veya daha yüksek CPU döngüleri gibi farklı altyapı gereksinimlerine sahip olması gerekir mi? Service Fabric kümesi birden fazla düğüm türünden oluşabilir: birincil düğüm türü ve bir veya daha fazla birincil düğüm türü. Her düğüm türü bir sanal makine ölçek kümesi ile eşleştirilir. Daha sonra, her düğüm türünün ölçeği birbirinden bağımsız olarak artırılabilir veya azaltılabilir, her düğüm türünde farklı bağlantı noktası kümeleri açık olabilir ve farklı kapasite ölçümleri yapılabilir. Belirli hizmetleri belirli düğüm türlerine kısıtlamak için [düğüm özellikleri ve yerleştirme kısıtlamaları][placementconstraints] ayarlanabilir.  Daha fazla bilgi için bkz. [küme kapasitesi planlamasını Service Fabric](service-fabric-cluster-capacity.md).

### <a name="select-node-properties-for-each-node-type"></a>Her düğüm türü için düğüm özelliklerini seçin
Düğüm türleri, ilişkili ölçek kümesindeki VM 'lerin SKU 'sunu, numarasını ve özelliklerini tanımlar.

Her düğüm türü için en az sanal makine boyutu, düğüm türü için seçtiğiniz [dayanıklılık katmanına][durability] göre belirlenir.

Birincil düğüm türü için en az sanal makine sayısı, seçtiğiniz [güvenilirlik katmanına][reliability] göre belirlenir.

Birincil düğüm [türleri](service-fabric-cluster-capacity.md#primary-node-type)için en düşük önerilere, [birincil olmayan düğüm türlerinde durum bilgisiz iş yüklerine](service-fabric-cluster-capacity.md#stateful-workloads)ve [birincil olmayan düğüm türlerinde durum bilgisiz iş yüklerine](service-fabric-cluster-capacity.md#stateless-workloads)bakın.

Düğüm sayısının alt sınırı, bu düğüm türünde çalıştırmak istediğiniz uygulama/hizmetlerin çoğaltmaları sayısını temel almalıdır.  [Service Fabric uygulamalar Için kapasite planlaması](service-fabric-capacity-planning.md) , uygulamalarınızı çalıştırmak için ihtiyacınız olan kaynakları tahmin etmenize yardımcı olur. Daha sonra değişen uygulama iş yükünü ayarlamak için kümenin ölçeğini değiştirebilir veya azaltabilirsiniz. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri için kısa ömürlü işletim sistemi diskleri kullanın

*Kısa ömürlü işletim sistemi diskleri* , yerel sanal MAKINEDE (VM) oluşturulmuş ve uzak Azure depolama 'ya kaydedilmemiş depolardır. Geleneksel kalıcı işletim sistemi disklerine kıyasla, kısa ömürlü işletim sistemi disklerine kıyasla tüm Service Fabric düğüm türleri (birincil ve Ikincil) için önerilir:

* İşletim sistemi diskine okuma/yazma gecikmesini azaltma
* Daha hızlı sıfırlama/yeniden görüntü düğümü yönetim işlemlerini etkinleştirme
* Genel maliyetleri düşürün (diskler ücretsizdir ve ek depolama maliyeti yoktur)

Kısa ömürlü işletim sistemi diskleri, belirli bir Service Fabric özelliği değildir, ancak Azure *sanal makine ölçek kümelerinin* Service Fabric düğüm türleriyle eşlenmiş bir özelliği değildir. Service Fabric ile birlikte kullanmak, küme Azure Resource Manager şablonunuzda şunları gerektirir:

1. Düğüm türlerinizin, kısa ömürlü işletim sistemi diskleri için [desteklenen Azure VM boyutlarını](../virtual-machines/ephemeral-os-disks.md) BELIRTTIĞINDEN ve VM boyutunun işletim sistemi disk boyutunu desteklemeye yetecek kadar önbellek boyutuna sahip olduğundan emin olun (aşağıdaki *nota* bakın.) Örneğin:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Bir sanal makine boyutunu, sanal makinenin işletim sistemi disk boyutundan büyük veya buna eşit bir önbellek boyutu seçtiğinizden emin olun, aksi takdirde Azure dağıtımınız hataya neden olabilir (başlangıçta kabul edilse bile).

2. Veya üzeri bir sanal makine ölçek kümesi sürümü ( `vmssApiVersion` ) belirtin `2018-06-01` :

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. Dağıtım şablonunuzun sanal makine ölçek kümesi bölümünde, şu `Local` seçeneği belirtin `diffDiskSettings` :

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> İşletim sistemi yükseltmesi durumunda işletim sistemi diski kaybolacağından, kullanıcı uygulamalarının IŞLETIM sistemi diskinde hiçbir bağımlılığı/dosya/yapıtı olmaması gerekir.

> [!NOTE]
> Mevcut kısa ömürlü VMSS 'ler, kısa ömürlü diskler kullanmak için yerinde yükseltilemez.
> Geçiş yapmak için, kullanıcıların kısa ömürlü disklerle yeni bir nodeType [eklemesi](./virtual-machine-scale-set-scale-node-type-scale-out.md) gerekir, iş yüklerini yeni NodeType ile taşıyın & var olan NodeType 'yi [kaldırın](./service-fabric-how-to-remove-node-type.md) .
>

Daha fazla bilgi ve diğer yapılandırma seçenekleri için bkz. [Azure VM 'ler Için kısa ömürlü işletim sistemi diskleri](../virtual-machines/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Küme için dayanıklılık ve güvenilirlik düzeylerini seçin
Dayanıklılık katmanı, sistem sanal makinelerinizin temel alınan Azure altyapısıyla sahip olduğu ayrıcalıkları göstermek için kullanılır. Bu ayrıcalık, birincil düğüm türünde, sistem hizmetleri ve durum bilgisi olan hizmetleriniz için çekirdek gereksinimlerini etkileyen tüm VM düzeyi altyapı isteklerini (VM yeniden başlatma, VM yeniden görüntüsü veya VM geçişi gibi) duraklatmaya olanak Service Fabric tanır. Birincil olmayan düğüm türlerinde, bu ayrıcalık, durum bilgisi olan hizmetlerinizin çekirdek gereksinimlerini etkileyen tüm VM düzeyi altyapı isteklerini (VM yeniden başlatma, VM yeniden görüntüsü ve VM geçişi gibi) duraklatmaya Service Fabric olanak tanır.  Hangi düzeyin kullanılacağı ve ne zaman kullanılacağı ile ilgili farklı düzeylerin ve önerilerin avantajları için, [kümenin dayanıklılık özelliklerine][durability]bakın.

Güvenilirlik katmanı, birincil düğüm türünde bu kümede çalıştırmak istediğiniz sistem hizmetlerinin çoğaltmaları sayısını ayarlamak için kullanılır. Kopyaların ne kadar fazla olması durumunda, sistem hizmetleri kümenizde daha güvenilir.  Hangi düzeyin kullanılacağı ve ne zaman kullanılacağı ile ilgili farklı düzeylerin ve önerilerin avantajları için, [kümenin güvenilirlik özelliklerine][reliability]bakın. 

## <a name="enable-reverse-proxy-andor-dns"></a>Ters proxy ve/veya DNS 'yi etkinleştir
Bir kümedeki düğümler aynı yerel ağda olduğundan, bir küme içindeki birbirlerine bağlanan hizmetler genellikle diğer hizmetlerin uç noktalarına erişebilir. Hizmetler arasında bağlanmayı kolaylaştırmak için Service Fabric ek hizmetler sağlar: bir [DNS hizmeti](service-fabric-dnsservice.md) ve [ters proxy hizmeti](service-fabric-reverseproxy.md).  Bir küme dağıtıldığında her iki hizmet de etkinleştirilebilir.

Birçok hizmet, özellikle Kapsayıcılı hizmetler, mevcut bir URL adına sahip olduğundan, bu işlemi standart DNS Protokolü (Adlandırma Hizmeti protokolü yerine) kullanarak çözebiliyor ve özellikle uygulamanın "yükselt ve Shift" senaryolarında kullanışlıdır. Bu, tam olarak DNS hizmetinin yaptığı şeydir. DNS adlarını bir hizmet adıyla eşlemenizi sağlar ve bu nedenle uç nokta IP adreslerini çözümleyebilir.

Ters proxy, HTTP uç noktalarını (HTTPS dahil) sunan kümedeki hizmetleri ele alınmaktadır. Ters proxy, belirli bir URI biçimi sağlayarak diğer hizmetlerin çağrılmasını büyük ölçüde basitleştirir.  Ters proxy, bir hizmetin birbirleriyle iletişim kurması için gerekli olan Resolve, Connect ve retry adımlarını da işler.

## <a name="prepare-for-disaster-recovery"></a>Olağanüstü durum kurtarmaya hazırlanma
Yüksek kullanılabilirlik sunmaya yönelik kritik bir bölüm, hizmetlerin tüm farklı türdeki hataların varlığını sürdürmesini sağlamaktır. Bu özellikle, denetim için planlanmamış ve dışındaki hatalarda önemlidir. [Olağanüstü durum kurtarma Için hazırlanma](service-fabric-disaster-recovery.md) , Modellenmemiş ve doğru yönetilmiyorsa olağanüstü durumlar olabilecek bazı yaygın hata modlarını açıklar. Ayrıca, olağanüstü bir durum oluşması durumunda yapılacak azaltmaları ve eylemler de anlatılmaktadır.

## <a name="production-readiness-checklist"></a>Üretim hazırlığı denetim listesi
Uygulamanız ve kümeniz üretim trafiği almaya hazırlanıyor mi? Kümenizin üretime dağıtılmasından önce [Üretim hazırlığı denetim listesini](service-fabric-production-readiness-checklist.md)çalıştırın. Bu denetim listesindeki öğeler aracılığıyla çalışarak uygulamanızın ve kümenizin sorunsuz çalışmasını sağlayın. Üretime geçmeden önce bu öğelerin tümünün kullanıma alınmasını önemle öneririz.

## <a name="next-steps"></a>Sonraki adımlar
* [Windows çalıştıran bir Service Fabric kümesi oluşturma](./service-fabric-best-practices-security.md)
* [Linux çalıştıran bir Service Fabric kümesi oluşturma](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster