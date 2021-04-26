---
title: Bakım bildirimleri için portalı kullanma
description: Azure 'da çalışan sanal makineler için bakım bildirimlerini görüntüleyin ve portalı kullanarak Self servis bakımı başlatın.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: a13d79a28ac07d736b1eaf0d0e6b7f7b1ba4a4ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557718"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Portalı kullanarak planlı bakım bildirimlerini işleme

**Bu makale hem Linux hem de Windows çalıştıran sanal makineler için geçerlidir.**

Planlı bir [bakım](maintenance-notifications.md) dalgası zamanlandıktan sonra, etkilenen sanal makinelerin listesini kontrol edebilirsiniz. 

Azure portal kullanabilir ve bakım için zamanlanmış VM 'Ler için arama yapabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Sol gezinti bölmesinde, **sanal makineler**' e tıklayın.

3. Kullanılabilir sütunların listesini açmak için sanal makineler bölmesinde **Sütunları Düzenle** düğmesini seçin.

4. Aşağıdaki sütunları seçin ve ekleyin:

   **Bakım durumu**: VM 'nin bakım durumunu gösterir. Olası değerler şunlardır:
      
    | Değer | Açıklama |
    |-------|-------------|
    | Şimdi başlat | VM, bakımı kendiniz başlatabilmenizi sağlayan self servis bakım penceresidir. VM 'niz üzerinde bakım başlatma hakkında bilgi için aşağıya bakın. | 
    | Zamanlanan | Sanal makine bakım için zamanlandı ve bakım başlatmanız gerekmez. Bakım penceresini, bu görünümdeki bakım-zamanlanan pencereyi seçerek veya VM 'ye tıklayarak öğrenebilirsiniz. | 
    | Zaten güncelleştirildi | VM 'niz zaten güncelleştirildi ve şu anda başka bir eylem gerekmiyor. | 
    | Daha sonra yeniden dene | Başarılı olmadan bakım başlattınız. Self Servis Bakım seçeneğini daha sonra kullanabileceğiniz şekilde kullanabileceksiniz. | 
    | Şimdi yeniden dene | Daha önce başarısız olan bir otomatik olarak başlatılan Bakımı yeniden deneyebilirsiniz. | 
    | - | VM 'niz planlı bir bakım dalgasının parçası değil. |

   **Bakım-self servis penceresi**: sanal makinelerinizdeki bakımı kendi başınıza başlatabilmeniz için zaman penceresini gösterir.
   
   **Bakım-zamanlanan pencere**: Azure 'un, bakım işleminin TAMAMLANABILMESI için VM 'nizi korumasıyla zaman penceresini gösterir. 



## <a name="notification-and-alerts-in-the-portal"></a>Portalda bildirim ve uyarılar

Azure, abonelik sahibine ve ortak sahipler grubuna bir e-posta göndererek planlı bakım için bir zamanlama iletişim kurar. Azure etkinlik günlüğü uyarıları oluşturarak bu iletişime ek alıcılar ve kanallar ekleyebilirsiniz. Daha fazla bilgi için bkz. [hizmet bildirimlerinde etkinlik günlüğü uyarıları oluşturma](../service-health/alerts-activity-log-service-notifications-portal.md).

**Olay türünü** **Planlı bakım** olarak ve **Sanal Makine Ölçek Kümeleri** ve/veya **sanal makineler** olarak **Hizmetler** olarak ayarladığınızdan emin olun.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Portaldan VM 'niz üzerinde bakım başlatın

VM ayrıntılarına baktığınızda, daha fazla bakım ile ilgili ayrıntıları görebilirsiniz.  
VM ayrıntıları görünümünün en üstünde, sanal makinenizin planlanmış bir bakım dalgasına dahil olması halinde yeni bir bildirim şeridi eklenecektir. Ayrıca, mümkün olduğunda bakım başlatmak için yeni bir seçenek eklenir. 


Planlanmış bakımla ilgili daha fazla ayrıntı içeren bakım sayfasını görmek için bakım bildirimi ' ne tıklayın. Buradan, VM 'niz üzerinde **bakım başlatabileceksiniz** .

Bakım başladıktan sonra, sanal makineniz korunur ve bakım durumu birkaç dakika içinde sonucu yansıtacak şekilde güncelleştirilir.

Self servis penceresini kaçırdıysanız, VM 'niz Azure tarafından korunuyorsa bu pencereyi yine de görebilirsiniz. 


## <a name="next-steps"></a>Sonraki adımlar

[Azure CLI](maintenance-notifications-cli.md) veya [PowerShell](maintenance-notifications-powershell.md)kullanarak planlı bakım de işleyebilirsiniz.
