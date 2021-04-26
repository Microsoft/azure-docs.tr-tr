---
title: Azure VMware çözümü için platform güncelleştirmeleri
description: Azure VMware çözümüne yönelik platform güncelleştirmeleri hakkında bilgi edinin.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045221"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware çözümü için platform güncelleştirmeleri

Azure VMware çözümü, Mart 2021 ' den itibaren önemli güncelleştirmeleri uygular. Azure hizmet durumu ile bakım zaman çizelgesini içeren bir bildirim alırsınız. Daha fazla bilgi için bkz. [Azure VMware çözümü özel bulut güncelleştirmeleri ve yükseltmeleri](concepts-upgrades.md).

## <a name="march-24-2021"></a>24 Mart 2021
Tüm yeni Azure VMware çözümü özel bulutları, VMware vCenter Version 6.7 U3l ve NSX-T Version 3.1.1 ile dağıtılır. Var olan tüm özel bulutlar güncelleştirilir ve yukarıda bahsedilen sürümlere **2021 Haziran 'dan** yükseltilir.

Planlı bakım tarihi ve saatine sahip bir e-posta alacaksınız. Bir yükseltmeyi yeniden zamanlayabilirsiniz. E-posta Ayrıca yükseltilen bileşen, iş yükleri üzerindeki etkisi, özel bulut erişimi ve diğer Azure hizmetleri için de ayrıntılar sağlar.  Yükseltmeden önceki bir saat, bir bildirim ve sonra tamamlandığında yeniden gönderilir.

## <a name="march-15-2021"></a>15 Mart 2021 

- Azure VMware çözüm hizmeti, özel bulutunuzda vCenter Server 'ı vCenter Server 6,7 güncelleştirme 3l sürümüne güncelleştirmek için **19 mart 2021 ' e kadar** bakım işi yapacaktır.

- VMware vCenter, bu süre içinde kullanılamayacak.  Bu nedenle, VM 'lerinizi (durdurma, başlatma, oluşturma, silme) veya özel bulut ölçeklendirmeyi (sunucu ve küme ekleme/kaldırma) yönetemeyeceksiniz. Ancak, VMware yüksek kullanılabilirlik (HA), mevcut VM 'Leri korumak için çalışmaya devam edecektir. 
 
Bu vCenter sürümü hakkında daha fazla bilgi için bkz. [VMware vCenter Server 6,7 güncelleştirme 3l sürüm notları](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 Mart 2021

- Azure VMware çözümü, **15 mart 2021 ' e kadar** [6,7 VMware ESXi, düzeltme eki ESXi670-202011002 sürümünü](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) mevcut Privates uygulayacak.

- VSphere Stack için, [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)tarihine kadar belgelenen geçici çözümler, **15 Mart 2021** tarihine kadar de uygulanır.

>[!NOTE]
>Bu, kesintiye uğramamış ve Azure VMware hizmetleri 'ni veya iş yüklerini etkilemeyecek olmalıdır. Bakım sırasında, dvports üzerinde _kayıp ağ bağlantısı_ ve _dvports üzerinde kayıp yukarı_ bağlantı gibi çeşitli VMware uyarıları, vCenter 'da görünür ve bakım ilerledikçe otomatik olarak temizlenir.

## <a name="post-update"></a>Güncelleştirme sonrası
Tamamlandıktan sonra, VMware bileşenlerinin daha yeni sürümleri görüntülenir. Herhangi bir sorunla karşılaşırsanız veya herhangi bir sorunuz olursa destek bileti açarak destek ekibimize başvurun.