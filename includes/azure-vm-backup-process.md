---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716758"
---
1. Yedekleme için seçilen Azure VM 'Leri için Azure Backup, belirttiğiniz yedekleme zamanlaması doğrultusunda bir yedekleme işi başlatır.
1. İlk yedekleme sırasında VM çalışıyorsa VM 'ye bir yedekleme uzantısı yüklenir.
    - Windows VM 'Leri için [VMSnapshot uzantısı](../articles/virtual-machines/extensions/vmsnapshot-windows.md) yüklenir.
    - Linux VM 'Leri için [VMSnapshotLinux uzantısı](../articles/virtual-machines/extensions/vmsnapshot-linux.md) yüklenir.
1. Çalıştıran Windows VM 'Leri için, VM 'nin uygulamayla tutarlı bir anlık görüntüsünü almak üzere Windows Birim Gölge Kopyası Hizmeti (VSS) ile yedekleme koordinatları.
    - Varsayılan olarak, yedekleme tam VSS yedeklemeleri alır.
    - Yedekleme uygulamayla tutarlı bir anlık görüntü alalamazsa, temeldeki depolamanın dosya ile tutarlı bir anlık görüntüsünü alır (VM durdurulduğunda hiçbir uygulama yazma işlemi gerçekleşmediğinden).
1. Linux VM 'Leri için yedekleme, dosya ile tutarlı bir yedekleme gerçekleştirir. Uygulamayla tutarlı anlık görüntüler için, ön/son betikleri el ile özelleştirmeniz gerekir.
1. Yedekleme, anlık görüntüyü aldıktan sonra verileri kasaya aktarır.
    - VM diskleri paralel olarak yedeklenerek yedekleme işlemi iyileştirilir.
    - Azure Backup, yedeklenen her disk için disk üzerindeki blokları okur ve yalnızca bir önceki yedekleme işleminden sonra değiştirilmiş olan veri bloklarını (delta) aktarır.
    - Anlık görüntü verileri kasaya hemen kopyalanmayabilir. Yoğun zamanlarda birkaç saat sürebilir. Bir VM için toplam yedekleme süresi, günlük yedekleme ilkeleri için 24 saatten az olacaktır.
1. Azure Backup etkinleştirildikten sonra bir Windows sanal makinesinde yapılan değişiklikler şunlardır:
    - Microsoft Visual C++ 2013 Redistributable (x64)-12.0.40660, VM 'ye yüklenir
    - Birim gölge kopyası hizmeti 'nin (VSS) başlangıç türü el ile otomatik olarak değiştirildi
    - Iaasvmprovider Windows hizmeti eklendi

1. Veri aktarımı tamamlandığında, anlık görüntü kaldırılır ve bir kurtarma noktası oluşturulur.

![Azure sanal makine yedekleme mimarisi](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
