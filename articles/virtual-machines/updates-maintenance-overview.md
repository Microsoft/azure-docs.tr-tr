---
title: Güncelleştirmeler ve bakım genel bakış
description: Azure 'daki sanal makinelerle sunulan güncelleştirmeler ve bakım seçenekleri hakkında bilgi edinin
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103022361"
---
# <a name="updates-and-maintenance-overview"></a>Güncelleştirmeler ve bakım genel bakış
Bu makalede, Azure sanal makineleri (VM 'Ler) için çeşitli güncelleştirme ve bakım seçeneklerine genel bakış sunulmaktadır.

## <a name="automatic-os-image-upgrade"></a>Otomatik işletim sistemi görüntüsü yükseltmesi

Ölçek kümesinde [Otomatik işletim sistemi görüntüsü yükseltmelerini](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) etkinleştirmek, güncelleştirme yönetimini güvenle ve ölçek kümesindeki tüm örnekler için işletim sistemi diskini otomatik olarak yükselterek kolaylaştırır.

[Otomatik işletim sistemi yükseltmesi](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) aşağıdaki özelliklere sahiptir:

- Yapılandırıldıktan sonra, görüntü yayımcıları tarafından yayımlanan en son işletim sistemi görüntüsü Kullanıcı müdahalesi olmadan ölçek kümesine otomatik olarak uygulanır.
- Yayımcı tarafından her yeni görüntü yayımlandığında örnek toplu işleri her seferinde bir şekilde yükseltir.
- Uygulama durumu araştırmaları ve [uygulama sistem durumu uzantısı](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context)ile tümleşir.
- Tüm VM boyutları ve hem Windows hem de Linux görüntüleri için geçerlidir.
- Dilediğiniz zaman Otomatik yükseltmeleri devre dışı bırakabilirsiniz (işletim sistemi yükseltmeleri de el ile başlatılabilir).
- Bir sanal makinenin işletim sistemi diski, en son görüntü sürümüyle oluşturulan yeni işletim sistemi diski ile değiştirilmiştir. Yapılandırılmış Uzantılar ve özel veri betikleri çalışır, ancak kalıcı veri diskleri korunur.
- [Uzantı sıralaması](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) destekleniyor.
- Otomatik işletim sistemi görüntüsü yükseltmesi, herhangi bir boyuttaki ölçek kümesinde etkinleştirilebilir.


## <a name="automatic-vm-guest-patching-preview"></a>Otomatik VM Konuk düzeltme eki uygulama (Önizleme)

Azure VM 'leriniz için [Otomatik sanal makine konuk düzeltme eki uygulama](automatic-vm-guest-patching.md) , güvenlik uyumluluğunu sürdürmek üzere güvenli ve otomatik olarak sanal makinelere yama yaparak güncelleştirme yönetimini kolaylaştırır.

[OTOMATIK VM Konuk düzeltme eki uygulama](automatic-vm-guest-patching.md) aşağıdaki özelliklere sahiptir:
- *Kritik* veya *güvenlik* olarak sınıflandırılan yamalar sanal makineye otomatik olarak indirilir ve uygulanır.
- Düzeltme ekleri, VM 'nin saat diliminde yoğun olmayan saatlerde uygulanır.
- Düzeltme Eki düzenleme Azure tarafından yönetilir ve düzeltme ekleri, [kullanılabilirlik-ilk ilkelere](automatic-vm-guest-patching.md#availability-first-patching)göre uygulanır.
- Platform sistem durumu sinyalleriyle belirlenen sanal makine sistem durumu, düzeltme eki uygulama başarısızlıklarını algılamak için izlenir.
- Tüm VM boyutları için geçerlidir.


## <a name="automatic-extension-upgrade-preview"></a>Otomatik uzantı yükseltme (Önizleme)

[Otomatik uzantı yükseltmesi](automatic-extension-upgrade.md) , Azure VM 'Leri ve Azure sanal makine ölçek kümeleri için Önizleme sürümünde kullanılabilir. Bir VM veya ölçek kümesi üzerinde otomatik uzantı yükseltmesi etkinleştirildiğinde, uzantı yayımcısı bu uzantı için yeni bir sürüm yayınlarsa uzantı otomatik olarak yükseltilir.

 Otomatik uzantı yükseltme aşağıdaki özelliklere sahiptir:
- Azure VM 'Leri ve Azure sanal makine ölçek kümeleri için desteklenir. Service Fabric sanal makine ölçek kümeleri Şu anda desteklenmiyor.
- Yükseltmeler, bir kullanılabilirlik-ilk dağıtım modelinde uygulanır.
- Bir sanal makine ölçek kümesi için, ölçek kümesi sanal makinelerinin %20 ' inden fazlası tek bir toplu işte yükseltilmeyecektir. En küçük toplu iş boyutu bir sanal makinedir.
- Tüm VM boyutları ve hem Windows hem de Linux uzantıları için geçerlidir.
- Dilediğiniz zaman Otomatik yükseltmeleri devre dışı bırakabilirsiniz.
- Otomatik uzantı yükseltmesi, her boyuttaki bir sanal makine ölçek kümesinde etkinleştirilebilir.
- Desteklenen her uzantı ayrı ayrı kaydedilir ve hangi uzantıların otomatik olarak yükseltilecek olduğunu seçebilirsiniz.
- Tüm genel bulut bölgelerinde desteklenir.

## <a name="hotpatch"></a>Hotpatch 

[Anında](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) yükleme, yüklemeden sonra yeniden başlatma gerektirmeyen yeni Windows Server Azure sürüm sanal makinelerine (VM 'ler) Güncelleştirme yüklemenin yeni bir yoludur. Windows Server Azure sürüm VM 'Leri için hotpatch aşağıdaki avantajlara sahiptir:

- Daha az yeniden başlatmalar ile alt iş yükü etkisi
- Paketler daha hızlı, daha hızlı bir şekilde yüklenir ve Azure Update Manager ile daha kolay bir düzeltme eki düzenlemesi sağlar
- Hotpatch güncelleştirme paketleri, yeniden başlatmadan daha hızlı yüklenen Windows güvenlik güncelleştirmeleri kapsamında daha iyi koruma sağlar


## <a name="azure-update-management"></a>Azure güncelleştirme yönetimi

Azure Otomasyonu 'ndaki [güncelleştirme yönetimi](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) kullanarak Azure 'daki Windows ve Linux sanal makineleriniz için işletim sistemi güncelleştirmelerini, şirket içi ortamları ve diğer bulut ortamlarında yönetebilirsiniz. Tüm aracı makinelerde kullanılabilir güncelleştirmelerin durumunu hızlı bir şekilde değerlendirebilir ve sunucular için gerekli güncelleştirmeleri yükleme işlemini yönetebilirsiniz.

## <a name="maintenance-control"></a>Bakım denetimi

[Bakım denetimini](maintenance-control.md)kullanarak yeniden başlatma gerektirmeyen platform güncelleştirmelerini yönetin. Azure, güvenilirliği, performansı, güvenliği geliştirmek veya yeni özellikleri başlatmak için altyapısını sıklıkla güncelleştirir. Çoğu güncelleştirme kullanıcılara saydamdır. Oyun, medya akışı ve finans işlemleri gibi bazı hassas iş yükleri, bir VM 'nin bakım için donuyor veya bağlantısı kesilmesinin birkaç saniyesini de kabul edemez. Bakım denetimi, platform güncelleştirmelerini bekleme ve bunları 35 günlük bir sıralı pencere içinde uygulama seçeneği sunar. 

Bakım denetimi, yalıtılmış sanal makinelerinize ve Azure adanmış ana bilgisayarlara güncelleştirmelerin ne zaman uygulanacağına karar vermenizi sağlar.

[Bakım denetimi](maintenance-control.md)ile şunları yapabilirsiniz:
- Toplu güncelleştirmeler tek bir güncelleştirme paketine sahiptir.
- Güncelleştirmelerin uygulanması için 35 güne kadar bekleyin. 
- Bir bakım zamanlaması yapılandırarak veya [Azure işlevleri](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)'ni kullanarak platform güncelleştirmelerini otomatikleştirin.
- Bakım yapılandırması abonelikler ve kaynak grupları arasında çalışır. 


## <a name="scheduled-events"></a>Zamanlanmış olaylar

Zamanlanan Olaylar, sanal makine (VM) bakımına hazırlanmak için uygulamanızın süresini sağlayan bir Azure Metadata Service. Uygulamanızın onlara hazırlanabilmesi ve kesintiye uğramasını önlemek için yaklaşan bakım olayları (örneğin, yeniden başlatma) hakkında bilgi sağlar. Hem Windows hem de Linux 'ta PaaS ve IaaS dahil tüm Azure sanal makine türlerinde kullanılabilir. 

Zamanlanan Olaylar hakkında bilgi için bkz. [Windows VM 'leri için zamanlanan olaylar](./windows/scheduled-events.md) ve [Linux için zamanlanan olaylar](./linux/scheduled-events.md)

## <a name="next-steps"></a>Sonraki adımlar

Uygulamalarınızın ve hizmetlerinizin çalışma süresini artırmanın daha fazla yolu için [kullanılabilirliği ve ölçek](availability.md) belgelerini gözden geçirin. 