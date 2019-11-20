---
title: Azure Site Recovery yapılandırma/işlem/ana hedef sunucuları hakkında
description: Bu makalede, Azure Site Recovery ile şirket içi VMware VM 'lerinin olağanüstü durum kurtarması ayarlanırken yapılandırma, süreç ve ana hedef sunuculara genel bir bakış sunulmaktadır
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ad816f39dd4182dfa41fca975c99824a5d77f860
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961317"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Site Recovery bileşenleri (yapılandırma, işlem, ana hedef) hakkında

Bu makalede, [Site Recovery](site-recovery-overview.md) hizmetiyle VMware VM 'leri ve fiziksel sunucuları Azure 'a çoğaltırken kullanılan yapılandırma, süreç ve ana hedef sunucuları açıklanmaktadır.

## <a name="configuration-server"></a>Yapılandırma Sunucusu

Şirket içi VMware VM 'Leri ve fiziksel sunucular için olağanüstü durum kurtarma için şirket içinde dağıtılan bir Site Recovery yapılandırma sunucusuna ihtiyacınız vardır.

**Ayar** | **Ayrıntılar** | **Köprü**
--- | --- | ---
**Bileşenleri**  | Yapılandırma sunucusu makinesi, yapılandırma sunucusu, işlem sunucusu ve ana hedef sunucu dahil olmak üzere tüm şirket içi Site Recovery bileşenlerini çalıştırır.<br/><br/> Yapılandırma sunucusunu ayarlarken, tüm bileşenler otomatik olarak yüklenir. | Yapılandırma sunucusu SSS [makalesini okuyun](vmware-azure-common-questions.md#configuration-server) .
**Rol** | Yapılandırma sunucusu yerinde bileşenler ile Azure arasındaki iletişimi düzenler ve veri çoğaltma işlemlerini yönetir. | Azure 'da [VMware](vmware-azure-architecture.md) ve [fiziksel sunucu](physical-azure-architecture.md) olağanüstü durum kurtarma mimarisi hakkında daha fazla bilgi edinin.
**VMware gereksinimleri** | Şirket içi VMware VM 'lerinin olağanüstü durum kurtarması için yapılandırma sunucusunu şirket içi, yüksek oranda kullanılabilir bir VMware VM 'si olarak yükleyip çalıştırmanız gerekir. | Önkoşullar [hakkında bilgi edinin](vmware-azure-deploy-configuration-server.md#prerequisites) .
**VMware dağıtımı** | Yapılandırma sunucusunu indirilen bir OVA şablonu kullanarak dağıtmanızı öneririz. Bu yöntem, tüm gereksinimleri ve önkoşulları karşılayan bir yapılandırma sunucusu kurmak için basit bir yol sağlar.<br/><br/> Bir nedenden dolayı bir OVA şablonu kullanarak bir VMware VM 'yi dağıtamazsınız, yapılandırma sunucusu makinelerini fiziksel makine olağanüstü durum kurtarma için aşağıda açıklandığı gibi el ile ayarlayabilirsiniz. | Bir OVA şablonuyla [dağıtın](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) .
**Fiziksel sunucu gereksinimleri** | Şirket içi fiziksel sunucularda olağanüstü durum kurtarma için yapılandırma sunucusunu el ile dağıtırsınız. | Önkoşullar [hakkında bilgi edinin](physical-azure-set-up-source.md#prerequisites) .
**Fiziksel sunucu dağıtımı** | VMware VM olarak yüklenebir fiziksel sunucuya yükleyebilirsiniz. | Yapılandırma sunucusunu el ile [dağıtın](physical-azure-set-up-source.md#set-up-the-source-environment) .


## <a name="process-server"></a>İşlem sunucusu

**Ayar** | **Ayrıntılar** | **Köprü**
--- | --- | ---
**Dağıtım**  | Olağanüstü durum kurtarma ve şirket içi VMware VM 'Leri ve fiziksel sunucuları çoğaltma için şirket içi bir işlem sunucusuna ihtiyacınız vardır. Varsayılan olarak, işlem sunucusu, dağıtırken yapılandırma sunucusuna yüklenir. | [Daha fazla bilgi edinin](vmware-azure-architecture.md?#architectural-components).
**Rol (Şirket içi** | -Çoğaltma için etkinleştirilen makinelerden çoğaltma verileri alır.<br/> -Önbelleğe alma, sıkıştırma ve şifreleme ile çoğaltma verilerini iyileştirir ve Azure depolama 'ya gönderir.<br/> -Şirket içi VMware VM 'lerinde ve çoğaltmak istediğiniz fiziksel sunucularda Site Recovery Mobility hizmetinin göndererek yüklemesini gerçekleştirir.<br/> -Şirket içi makinelerin otomatik olarak bulunmasını gerçekleştirir. | [Daha fazla bilgi edinin](vmware-physical-azure-config-process-server-overview.md#process-server). 
**Rol (Azure 'dan yeniden çalışma)** | Şirket içi sitenizden yük devretmeden sonra, şirket içi konumunuza yeniden çalışmayı işlemek için Azure sanal makinesi olarak Azure 'da bir işlem sunucusu ayarlarsınız.<br/><br/> Azure 'daki işlem sunucusu geçicidir. Yeniden çalışma yapıldıktan sonra Azure VM silinebilir. | [Daha fazla bilgi edinin](vmware-azure-set-up-process-server-azure.md).
**Ölçeklendirme** | Daha büyük dağıtımlar için, şirket içi ek, genişleme işlem sunucuları ayarlayabilirsiniz. Ek sunucular, daha fazla sayıda çoğaltma makinesini ve daha büyük depolama kapasitesi trafiğini işleyerek kapasiteyi ölçeklendirir.<br/><br/> Çoğaltma trafiğinin yükünü dengelemek için makineleri iki işlem sunucusu arasında taşıyabilirsiniz. | [Daha fazla bilgi](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>Ana hedef sunucu

Ana hedef sunucu, Azure'dan yeniden çalışma sırasında çoğaltma verilerini işler.

- Yapılandırma sunucusunda varsayılan olarak yüklenir.
- Büyük dağıtımlar için yeniden çalışma için ek ve ayrı bir ana hedef sunucu ekleyebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
- VMware VM 'Leri ve fiziksel sunucular için olağanüstü durum kurtarma [mimarisini](vmware-azure-architecture.md) gözden geçirin.
- VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarmaya yönelik [gereksinimleri ve önkoşulları](vmware-physical-azure-support-matrix.md) gözden geçirin. 
