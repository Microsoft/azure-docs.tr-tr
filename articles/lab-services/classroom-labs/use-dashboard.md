---
title: Azure Lab Services bir sınıf Laboratuvarı için Pano kullanma | Microsoft Docs
description: Azure Lab Services bir sınıf Laboratuvarı için panoyu nasıl kullanacağınızı öğrenin.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: d1e34a493b747383ce479bcad638098b41e59d2b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588193"
---
# <a name="dashboard-for-classroom-labs"></a>Sınıf laboratuvarları panosu
Bu makalede, Azure Lab Services bir sınıf laboratuvarının pano görünümü açıklanmaktadır. 

![Pano](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Maliyetler ve faturalandırma kutucuğu
Bu kutucuk, aşağıdaki maliyet tahmini ayrıntılarını sağlar:

| Ayar | Değer | 
| ------- | ----- | 
| Kota saatleri | Kullanıcının VM 'yi zamanlanan saatlerin dışında kullanabileceği en fazla saat sayısı. |
| Zamanlanan saatler | Laboratuvarda ayarlanan zamanlama temelinde tahakkuk edilecek saatler. Bu değer yalnızca tüm zamanlama olaylarında bir Kimden/bitiş tarihi ayarlanırsa kullanılabilir. |
| Saatler/Kullanıcı | Kota saatlerinin ve zamanlanan saatlerin toplamı. |
| Maksimum Kullanıcı sayısı | Talep edilecek tüm sanal makineleri temel alan laboratuvardaki en fazla kullanıcı sayısı. |
| Saat x Kullanıcı | Saatler/Kullanıcı Kullanıcı sayısına göre çarpılır. |
| Ayarlanmış kota | Belirli kullanıcılara eklenen kota saatlerinin toplamı. |
| Toplam saat * $/saat | Seçilen VM boyutuna bağlı olarak saat başına maliyet. Bu, Kullandıkça Öde fiyatı üzerinden normal ücret ödemenize dayanır. |
| Toplam tahmini maliyet | Bu, geçerli ayarlara bağlı olarak bu laboratuvar için en yüksek fiyata yöneliktir. |

## <a name="template-tile"></a>Şablon kutucuğu
Bu kutucukta aşağıdaki bilgileri görürsünüz:

- Şablonun oluşturulduğu tarih 
- Şablonun en son yayımlandığı tarih 

Ayrıca, sınıf için [şablon VM 'yi yönetebileceğiniz](how-to-create-manage-template.md) **şablon** sayfasına gitmek için bir bağlantıya sahiptir. 

## <a name="virtual-machine-pool-tile"></a>Sanal makine havuzu kutucuğu

Bu kutucukta aşağıdaki bilgileri görürsünüz:

- Öğrencilere atanan sanal makine sayısı (kullanıcılar)
- Henüz öğrencilerine atanmamış sanal makine sayısı

Ayrıca, laboratuvardaki [sanal makine havuzunu yönetebileceğiniz](how-to-set-virtual-machine-passwords.md) **sanal makine havuzu** sayfasına gitmek için bir bağlantıya sahiptir. 

## <a name="users-tile"></a>Kullanıcılar kutucuğu

Bu kutucukta aşağıdaki bilgileri görürsünüz:

- Sınıfa kayıtlı Kullanıcı sayısı
- Laboratuvara eklenen ancak sınıfa kaydedilmeyen Kullanıcı sayısı 

Ayrıca, kullanıcıları laboratuvar için [yönetebileceğiniz](how-to-configure-student-usage.md) **Kullanıcılar** sayfasına gitmek için bir bağlantıya sahiptir. 

## <a name="schedules-tile"></a>Zamanlamalar bölmesi
Kutucukta laboratuvar için geçerli zamanlanmış olayları görürsünüz. Ayrıca, [zamanlamaları oluşturabileceğiniz ve yönetebileceğiniz](how-to-create-schedules.md) **zamanlama** sayfasına gitmek için bir bağlantıya sahiptir. Kutucukta yalnızca iki zamanlanmış olay ve laboratuvar için kalan zamanlanmış olay sayısı için ayrıntılar gösterilir. 

![Zamanlanmış olaylar](../media/use-dashboard/scheduled-events.png)

