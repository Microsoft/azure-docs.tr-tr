---
title: AWS ve diğer platformlardan Azure 'da yönetilen disklere geçiş
description: AWS veya diğer sanallaştırma platformları gibi diğer bulutlardan karşıya yüklenen VHD 'Leri kullanarak Azure 'da VM 'Ler oluşturun ve Azure yönetilen disklerden yararlanın.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dbce2969ccb508c2bf3ee33730d0b112caa45c9e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033057"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Azure 'da Amazon Web Services (AWS) ve diğer platformlardan yönetilen disklere geçirme

Yönetilen disklerden faydalanan VM 'Ler oluşturmak için AWS veya şirket içi sanallaştırma çözümlerinden VHD dosyalarını Azure 'a yükleyebilirsiniz. Azure yönetilen diskler, Azure IaaS VM 'Leri için depolama hesaplarını yönetme ihtiyacını ortadan kaldırır. Yalnızca ihtiyacınız olan diskin türünü (Premium veya standart) ve boyutunu belirtmeniz gerekir ve Azure bu diski sizin için oluşturur ve yönetir. 

Genelleştirilmiş ve özelleştirilmiş VHD 'leri karşıya yükleyebilirsiniz. 
- **GENELLEŞTIRILMIŞ VHD** -Sysprep kullanılarak tüm kişisel hesap bilgileriniz kaldırılmıştır. 
- **ÖZELLEŞTIRILMIŞ VHD** -özgün VM 'nizden Kullanıcı hesaplarını, uygulamaları ve diğer durum verilerini tutar. 

> [!IMPORTANT]
> Herhangi bir VHD 'yi Azure 'a yüklemeden önce, [Azure 'a yüklemek için bir WINDOWS VHD veya vhdx hazırlamanızı](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) izlemeniz gerekir
>
>


| Senaryo                                                                                                                         | Belgeler                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Yönetilen diskleri kullanarak Azure VM 'lerine geçirmek istediğiniz mevcut AWS EC2 örneklerine sahipsiniz                              | [Bir VM 'yi Amazon Web Services (AWS) 'den Azure 'a taşıma](aws-to-azure.md)                           |
| Birden çok Azure VM oluşturmak için görüntü olarak kullanmak istediğiniz başka bir sanallaştırma platformundan bir sanal makinenizin olması gerekir. | [Genelleştirilmiş bir VHD 'YI karşıya yükleyin ve Azure 'da yeni bir sanal makine oluşturmak için kullanın](upload-generalized-managed.md) |
| Azure 'da yeniden oluşturmak istediğiniz benzersiz şekilde özelleştirilmiş bir sanal makine vardır.                                                      | [Özelleştirilmiş bir VHD 'yi Azure 'a yükleyin ve yeni bir VM oluşturun](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Yönetilen disklere genel bakış

Azure yönetilen diskler, depolama hesaplarını yönetme gereksinimini ortadan kaldırarak VM yönetimini basitleştirir. Yönetilen diskler, bir kullanılabilirlik kümesindeki VM 'lerin daha iyi güvenilirliğinden de yararlanır. Tek bir başarısızlık noktasını önlemek için, bir kullanılabilirlik kümesindeki farklı VM 'Lerin disklerinin birbirinden yeterince yalıtılmasını sağlar. Farklı sanal makinelerin disklerini, farklı depolama ölçek birimlerindeki (damgalar), donanım ve yazılım hatalarından kaynaklanan tek bir depolama ölçek birimi hatalarının etkisini sınırlayan bir kullanılabilirlik kümesine otomatik olarak koyar.
Gereksinimlerinize göre dört tür depolama seçeneği arasından seçim yapabilirsiniz. Kullanılabilir disk türleri hakkında bilgi edinmek için bkz. makalemiz [bir disk türü seçin](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Yönetilen disklere geçişi planlayın

Bu bölüm, VM ve disk türleri üzerinde en iyi kararı vermenize yardımcı olur.

Yönetilmeyen disklerden yönetilen disklere geçiş yapmak için planlama yapıyorsanız, [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolüne sahip olan kullanıcıların VM boyutunu değiştiremeyeceği (ön dönüştürme işlemleri gibi) fark etmeniz gerekir. Bunun nedeni, yönetilen diskleri olan VM 'Lerin, kullanıcının işletim sistemi disklerinde Microsoft. COMPUTE/diskler/Write iznine sahip olmasını gerektirir.

### <a name="location"></a>Konum

Azure yönetilen disklerinin kullanılabildiği bir konum seçin. Premium yönetilen disklere geçiş yapıyorsanız Premium depolamanın, geçirmeyi planladığınız bölgede kullanılabilir olduğundan da emin olun. Kullanılabilir konumlara ilişkin güncel bilgiler için bkz. [bölgeye göre Azure hizmetleri](https://azure.microsoft.com/regions/#services) .

### <a name="vm-sizes"></a>VM boyutları

Premium yönetilen disklere geçiş yapıyorsanız, VM 'nin boyutunu VM 'nin bulunduğu bölgede kullanılabilir olan Premium depolama özellikli boyuta güncelleştirmeniz gerekir. Premium depolama özellikli VM boyutlarını gözden geçirin. Azure VM boyutu belirtimleri, [sanal makineler Için boyutlar](sizes.md)bölümünde listelenmiştir.
Premium depolamayla çalışan sanal makinelerin performans özelliklerini gözden geçirin ve iş yükünüze en uygun VM boyutunu seçin. Disk trafiğini yönlendirmek için sanal makinenizde yeterli kullanılabilir bant genişliği olduğundan emin olun.

### <a name="disk-sizes"></a>Disk boyutları

**Premium yönetilen diskler**

VM 'niz ile kullanılabilecek yedi tür Premium yönetilen disk vardır ve her biri belirli IOPS ve aktarım hızı sınırlarına sahiptir. Kapasite, performans, ölçeklenebilirlik ve en yoğun yük bakımından uygulamanızın gereksinimlerine bağlı olarak, VM 'niz için Premium disk türünü seçerken bu limitleri dikkate alın.

| Premium diskler türü  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Disk boyutu           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Disk başına IOPS       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Disk başına aktarım hızı | saniyede 25 MB  | saniyede 50 MB  | saniyede 100 MB | saniyede 125 MB |saniyede 150 MB | saniyede 200 MB | saniyede 250 MB | saniyede 250 MB |

**Standart yönetilen diskler**

VM 'niz ile kullanılabilecek yedi tür Standart yönetilen disk vardır. Her birinin farklı kapasitesi vardır ancak aynı ıOPS ve aktarım hızı limitlerini vardır. Uygulamanızın kapasite ihtiyaçlarına bağlı olarak standart yönetilen disklerin türünü seçin.

| Standart Disk Türü  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Disk boyutu           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| Disk başına IOPS       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Disk başına aktarım hızı | saniyede 60 MB | saniyede 60 MB | saniyede 60 MB | saniyede 60 MB |saniyede 60 MB | saniyede 60 MB | saniyede 60 MB | saniyede 60 MB | 

### <a name="disk-caching-policy"></a>Disk önbelleğe alma ilkesi 

**Premium yönetilen diskler**

Varsayılan olarak, disk önbelleğe alma ilkesi tüm Premium veri diskleri için *salt okunurdur* ve VM 'ye bağlı olan Premium işletim sistemi diski Için *okuma yazma* işlemi yapılır. Bu yapılandırma ayarı, uygulamanızın IOs için en iyi performansı elde etmek için önerilir. Daha iyi uygulama performansı elde edebilmeniz için, yazma ağır veya salt yazılır veri diskleri (örneğin, SQL Server günlük dosyaları) için disk önbelleğe almayı devre dışı bırakın.

### <a name="pricing"></a>Fiyatlandırma

[Yönetilen diskler için fiyatlandırmayı](https://azure.microsoft.com/pricing/details/managed-disks/)gözden geçirin. Premium yönetilen disklerin fiyatlandırması, Premium yönetilmeyen disklerle aynıdır. Ancak standart yönetilen diskler fiyatlandırması standart yönetilmeyen disklerden farklıdır.


## <a name="next-steps"></a>Sonraki Adımlar

- Herhangi bir VHD 'yi Azure 'a yüklemeden önce, [Azure 'a yüklemek için bir WINDOWS VHD veya vhdx hazırlamanızı](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) izlemeniz gerekir
