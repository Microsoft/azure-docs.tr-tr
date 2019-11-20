---
title: Azure Site Recovery 'de kurtarma planları hakkında
description: Azure Site Recovery hizmetiyle olağanüstü durum kurtarma için kurtarma planlarını kullanma hakkında bilgi edinin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 640f7258821718c5d406dd581d1f9c503e0d41c2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953882"
---
# <a name="about-recovery-plans"></a>Kurtarma planları hakkında

Bu makalede [Azure Site Recovery](site-recovery-overview.md)içindeki kurtarma planları açıklanmaktadır.

Kurtarma planı, makineleri kurtarma gruplarına toplar. Bir planı, bu plana sıra, yönergeler ve görevler ekleyerek özelleştirebilirsiniz. Bir plan tanımlandıktan sonra, üzerinde bir yük devretme çalıştırabilirsiniz.  Daha önce başka bir kurtarma planıyla dağıtılırsa, sonraki planların makinenin dağıtımını/başlangıcını atlayabileceği birden çok kurtarma planında makinelere başvurulabilir.


## <a name="why-use-a-recovery-plan"></a>Neden kurtarma planı kullanılmalıdır?

Kurtarma planı, yük devredebilmeniz gereken küçük bağımsız birimler oluşturarak sistematik bir kurtarma işlemi tanımlamanıza yardımcı olur. Birim genellikle ortamınızdaki bir uygulamayı temsil eder. Kurtarma planı, makinelerin yük devretmesinin nasıl başarısız olduğunu ve yük devretme sonrasında başladıkları sırayı tanımlar. Kurtarma planlarını şu şekilde kullanın:

* Bir uygulamayı bağımlılıkları etrafında modelleyin.
* RTO 'ı azaltmak için kurtarma görevlerini otomatikleştirin.
* Uygulamalarınızın bir kurtarma planının parçası olmasını sağlayarak geçiş veya olağanüstü durum kurtarma için hazırlandığınızı doğrulayın.
* Olağanüstü durum kurtarma veya geçişin beklendiği gibi çalıştığından emin olmak için kurtarma planlarında yük devretme testi çalıştırın.


## <a name="model-apps"></a>Model uygulamaları

> [!NOTE]
> En fazla 100 korumalı örnek, tek bir kurtarma planına eklenebilir.

Uygulamaya özgü özellikleri yakalamak için bir kurtarma grubu planlayabilir ve oluşturabilirsiniz. Örnek olarak, SQL Server arka ucu, ara yazılım ve Web ön ucuna sahip tipik üç katmanlı bir uygulamayı ele alalım. Genellikle kurtarma planını, her katmandaki makinelerin yük devretme sonrasında doğru sırada başlayacağı şekilde özelleştirirsiniz.

- SQL arka ucu önce, sonraki ara yazılım ve son olarak Web ön ucuna başlamalıdır.
- Bu başlangıç siparişi, uygulamanın son makinenin başladığı zamana göre çalışmasını sağlar.
- Bu sipariş, ara yazılım başlatıldığında ve SQL Server katmanına bağlanmaya çalıştığında, SQL Server katmanının zaten çalışmakta olmasını sağlar. 
- Bu sıra, ön uç sunucusunun en son başlamasını sağlamaya yardımcı olur, böylece son kullanıcılar uygulama URL 'sine tüm bileşenler çalışmaya ve çalışmadan önce bağlanmaması ve uygulama istekleri kabul etmeye hazırlanmaya devam eder.

Bu sırayı oluşturmak için, kurtarma grubuna gruplar ekler ve gruplara makineler eklersiniz.
- Order belirtildiğinde sıralama kullanılır. Eylemler, uygulama kurtarma RTO 'ı geliştirmek için uygun olduğunda paralel olarak çalışır.
- Tek bir gruptaki makineler paralel olarak yük devreder.
- Grup 2 makinelerinin yük devretmesini yalnızca Grup 1 ' deki tüm makinelere devreder ve başlatıldıktan sonra başlatabilmesi için, farklı gruplardaki makineler grup sırasında yük devreder.

    ![Örnek kurtarma planı](./media/recovery-plan-overview/rp.png)

Bu özelleştirmeyle birlikte, kurtarma planında bir yük devretme çalıştırdığınızda ne olur: 

1. Kapatma adımı, şirket içi makineleri kapatmaya çalışır. Özel durum, bir yük devretme testi çalıştırırsanız, bu durumda birincil site çalışmaya devam eder. 
2. Bu oturum, kurtarma planındaki tüm makinelerin paralel yük devretmesini tetikler.
3. Yük devretme, çoğaltılan verileri kullanarak sanal makine disklerini hazırlar.
4. Başlangıç grupları sırayla çalışır ve makineler her grupta başlatılır. Birincisi, 1. Grup çalışır, sonra Grup 2 ve son olarak, Grup 3. Herhangi bir grupta birden fazla makine varsa, tüm makineler paralel olarak başlar.


## <a name="automate-tasks"></a>Görevleri otomatikleştirme

Büyük uygulamaları kurtarmak karmaşık bir görev olabilir. El ile yapılan adımlar işlemi hataya açıktır ve yük devretmeyi çalıştıran kişi tüm uygulama farkınmallarını bilmeyebilir. Azure 'da yük devretme için Azure Otomasyonu runbook 'larını kullanarak her adımda sipariş getirmek ve gereken eylemleri otomatikleştirmek için bir kurtarma planı kullanabilirsiniz. Otomatikleştirilen görevler için kurtarma planlarına el ile eylemler için duraklamalar ekleyebilirsiniz. Yapılandırabilmeniz için kullanabileceğiniz birkaç görev türü vardır:

* **Yük devretmeden sonra Azure VM 'Deki görevler**: Azure 'a yük devrettikten sonra, yük DEVRETMEDEN sonra VM 'ye bağlanabilmeniz için genellikle eylemler gerçekleştirmeniz gerekir. Örneğin: 
    * Azure VM 'de genel IP adresi oluşturun.
    * Azure VM 'nin ağ bağdaştırıcısına bir ağ güvenlik grubu atayın.
    * Bir kullanılabilirlik kümesine yük dengeleyici ekleyin.
* **Yük devretme SONRASıNDA VM Içindeki görevler**: Bu görevler genellikle makinede çalışan uygulamayı yeniden yapılandırarak yeni ortamda düzgün çalışmaya devam eder. Örneğin:
    * Makinenin içindeki veritabanı bağlantı dizesini değiştirin.
    * Web sunucusu yapılandırmasını veya kurallarını değiştirin.


## <a name="test-failover"></a>Yük devretme testi

Yük devretme testi tetiklemesi için bir kurtarma planı kullanabilirsiniz. Aşağıdaki en iyi yöntemleri kullanın:

- Tam yük devretme çalıştırmadan önce, bir uygulamadaki yük devretme testini her zaman doldurun. Yük devretme testi, uygulamanın kurtarma sitesinde bulunup bulunmadığını kontrol etmenize yardımcı olur.
- Bir şeyin eksik olduğunu fark ederseniz, bir temizleme tetikleyin ve sonra yük devretme testini yeniden çalıştırın. 
- Uygulamanın sorunsuz bir şekilde kurtarıp kurtarana kadar bir test yük devretmesini birden çok kez çalıştırın.
- Her uygulama benzersiz olduğundan, her bir uygulama için özelleştirilmiş kurtarma planları oluşturmanız ve her biri üzerinde yük devretme testi çalıştırmanız gerekir.
- Uygulamalar ve bağımlılıkları sıklıkla değişir. Kurtarma planlarının güncel olduğundan emin olmak için her ay her bir uygulama için bir yük devretme testi çalıştırın.

    ![Site Recovery bir örnek test kurtarma planının ekran görüntüsü](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Videoyu izleme

İki katmanlı bir WordPress uygulaması için bir tıklama yük devretmesinin gösterildiği hızlı örnek bir video izleyin.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Sonraki adımlar

- Kurtarma planı [oluşturun](site-recovery-create-recovery-plans.md) .
- Yük devretme [çalıştırma](site-recovery-failover.md)hakkında bilgi edinin.  
