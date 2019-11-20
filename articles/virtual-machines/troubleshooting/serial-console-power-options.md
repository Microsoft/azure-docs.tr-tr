---
title: Azure seri konsol güç seçenekleri | Microsoft Docs
description: Azure seri konsolu 'nda bulunan VM güç seçenekleri
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: f17b96ad880742cf1232b074e4398f3b1d15e5ba
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129581"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Azure seri konsolundan kullanılabilen güç seçenekleri

Azure seri konsolu, VM 'niz veya sanal makine ölçek kümesi üzerinde güç yönetimi için çeşitli güçlü araçlar sağlar. Bu güç yönetimi seçeneklerinin bazıları kafa karıştırıcı olabilir, bu nedenle bu belge her bir araca ve onun amaçlanan kullanım durumuna genel bir bakış sağlar.

Seri konsol özelliği | Açıklama | Kullanım Örneği
:----------------------|:------------|:---------
VM'yi Yeniden Başlat | VM veya sanal makine ölçek kümesi örneğinizi düzgün bir şekilde yeniden başlatın. Bu işlem, Genel Bakış sayfasında bulunan yeniden başlatma özelliğini çağırma ile aynıdır. | Çoğu durumda, sanal makineyi yeniden başlatmaya çalışırken bu seçenek ilk aracınız olmalıdır. Seri konsol bağlantınızda kısa bir kesinti yaşanacaktır ve VM yeniden başlatıldıktan hemen sonra otomatik olarak devam eder.
VM'yi Sıfırla | VM 'nizin veya Azure platformu tarafından ayarlanan sanal makine ölçek kümesinin zorla bir güç döngüsüdür. | Bu seçenek, geçerli durumundan bağımsız olarak işletim sisteminizi hemen yeniden başlatmak için kullanılır. Bu işlem düzgün çalışmadığından veri kaybı veya bozulması riski vardır. Seri konsol bağlantısında, önyükleme zamanında (örneğin, bir Linux VM 'de GRUB 'ye veya bir Windows VM 'de güvenli moda alma) daha önce komut göndermek için yararlı olabilecek bir kesinti yoktur.
SysRq-Reboot (b) | Konuk yeniden başlatmayı zorlamak için bir sistem isteği. | Bu özellik yalnızca Linux işletim sistemleri için geçerlidir ve işletim sisteminde [SySRq 'ın etkinleştirilmesini](./serial-console-nmi-sysrq.md#system-request-sysrq) gerektirir. İşletim sistemi SysRq için düzgün yapılandırıldıysa, bu komut IŞLETIM sisteminin yeniden başlatılmasına neden olur.
NMI (maskelenemeyen kesme) | İşletim sistemine teslim edilecek bir kesme komutu | Bu işlem hem [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) hem de [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) VM 'leri için kullanılabilir ve NMI 'nin etkinleştirilmesini gerektirir. NMI 'nin gönderilmesi genellikle işletim sisteminizin kilitlenmesine neden olur. İşletim sisteminizi bir döküm dosyası oluşturacak şekilde yapılandırabilir ve ardından, alt düzey hata ayıklama sırasında yararlı olabilecek NMI 'yi almaya sonra yeniden başlatabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* [Linux VM 'leri Için Azure seri konsolu](./serial-console-linux.md) hakkında daha fazla bilgi edinin
* [Windows VM 'leri Için Azure seri konsolu](./serial-console-windows.md) hakkında daha fazla bilgi edinin