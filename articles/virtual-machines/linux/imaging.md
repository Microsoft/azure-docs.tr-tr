---
title: Azure için Linux görüntüleri oluşturmaya genel bakış
description: Linux VM görüntülerinizi getirme veya Azure 'da kullanmak üzere yeni görüntüler oluşturma.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: a77c2bc69418b821933c0b62674500f7a32e40a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565215"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Azure 'da Linux görüntülerini getirme ve oluşturma

Bu genel bakışta, görüntüleme ve Azure 'da Linux görüntülerini başarıyla oluşturma ve kullanma hakkında temel kavramlar ele alınmaktadır. Azure 'a özel bir görüntü getirmeden önce, kullanabileceğiniz tür ve seçeneklerin farkında olmanız gerekir.

Bu makale, görüntü karar noktaları ve gereksinimleri hakkında konuşacak, temel kavramları açıklayabilmenizi sağlayacak ve bu sayede kendi özel görüntülerinizi belirtimize oluşturabiliyor.

## <a name="difference-between-managed-disks-and-images"></a>Yönetilen diskler ve görüntüler arasındaki fark


Azure, bir VHD 'yi platforma getirmenize, [yönetilen disk](../faq-for-disks.md#managed-disks)olarak kullanmanıza veya bir görüntü kaynağı olarak kullanmanıza olanak tanır. 

Azure yönetilen diskler tek VHD 'lardır. Mevcut bir VHD 'yi alabilir ve bundan yönetilen bir disk oluşturabilir ya da sıfırdan boş bir yönetilen disk oluşturabilirsiniz. Diski VM 'ye ekleyerek yönetilen disklerden VM 'Ler oluşturabilirsiniz, ancak yalnızca bir VM 'ye sahip bir VHD kullanabilirsiniz. Herhangi bir işletim sistemi özelliğini değiştiremezsiniz; Azure yalnızca VM 'yi açmayı ve bu diski kullanmaya başlamasını dener. 

Azure görüntüleri, birden fazla işletim sistemi diski ve veri diski oluşturabilir. Bir VM oluşturmak için yönetilen bir görüntü kullandığınızda, platform görüntünün bir kopyasını oluşturur ve VM 'yi oluşturmak için bunu kullanır. bu nedenle, yönetilen görüntü desteği birden çok VM için aynı görüntüyü yeniden kullanır. Azure Ayrıca, genel çoğaltma ve [paylaşılan görüntü Galerisi](../shared-image-galleries.md)aracılığıyla sürüm oluşturma gibi görüntüler için gelişmiş yönetim özellikleri de sağlar. 



## <a name="generalized-and-specialized"></a>Genelleştirilmiş ve özelleştirilmiş

Azure, genelleştirilmiş ve özelleştirilmiş iki ana görüntü türü sunar. Genelleştirilmiş ve özelleştirilmiş terimler, Azure 'a geçirilen ilk Windows koşullardır. Bu türler, platformun sanal makineyi açtığında VM 'yi nasıl işleyeceğini tanımlar. Her iki türün avantajları ve dezavantajları ve önkoşulları vardır. Başlamadan önce ihtiyacınız olan görüntü türünü bilmeniz gerekir. Aşağıda, seçmeniz gereken senaryolar ve tür özetlenmektedir:

| Senaryo      | Görüntü türü  | Depolama seçenekleri |
| ------------- |:-------------:| :-------------:| 
| Birden çok VM tarafından kullanılmak üzere yapılandırılabilecek bir görüntü oluşturun ve ana bilgisayar adını ayarlayabilir, yönetici kullanıcı ekleyebilir ve ilk önyükleme sırasında diğer görevleri gerçekleştirebilirsiniz. | Genelleştirilmiş | Paylaşılan görüntü Galerisi veya tek başına yönetilen görüntüler |
| VM anlık görüntüsünden veya bir yedekten görüntü oluşturma | Özelleştirilmiş |Paylaşılan görüntü Galerisi veya yönetilen disk |
| Birden çok VM oluşturmak için yapılandırma gerektirmeyen bir görüntüyü hızlıca oluşturun |Özelleştirilmiş |Paylaşılan Görüntü Galerisi |


### <a name="generalized-images"></a>Genelleştirilmiş görüntüler

Genelleştirilmiş görüntü, kurulumun ilk önyüklemede tamamlanmasını gerektiren bir görüntüdür. Örneğin, ilk önyüklemede ana bilgisayar adı, Yönetici Kullanıcı ve VM 'ye özgü diğer yapılandırma ayarları ayarlanır. Bu, görüntünün birden çok kez yeniden kullanılmasını istediğinizde ve oluşturma sırasında parametreleri geçirmek istediğinizde yararlıdır. Genelleştirilmiş görüntü Azure Aracısı içeriyorsa, aracı parametreleri işleyecek ve ilk yapılandırmanın tamamlandığı platforma geri sinyaline sahip olur. Bu işleme [sağlama](./provisioning.md)adı verilir. 

Sağlama, görüntüye bir hazırlayıcı eklenmesini gerektirir. İki hazıriyer vardır:
- [Azure Linux Aracısı](../extensions/agent-linux.md)
- [kabuğunuzda Cloud-init](./using-cloud-init.md)

Bunlar, görüntü oluşturmaya yönelik [önkoşullardır](./create-upload-generic.md) .


### <a name="specialized-images"></a>Özel görüntüler
Bunlar tamamen yapılandırılmış ve VM ve özel parametre gerektirmeyen görüntülerdir, platform yalnızca VM 'yi açık duruma getirin, VM 'nin aynı VNET 'te DNS çakışmalarını önlemek için konak içinde benzersizlik tanıtıcısı gerekir. 

Bu görüntüler için sağlama aracıları gerekli değildir, ancak uzantı işleme özelliklerine sahip olmak isteyebilirsiniz. Linux aracısını yükleyebilir, ancak sağlama seçeneğini devre dışı bırakabilirsiniz. Bir sağlama aracısına gerek duymasa bile, görüntü Azure görüntüleri için [önkoşulları](./create-upload-generic.md)  karşılamalıdır.


## <a name="image-storage-options"></a>Görüntü depolama seçenekleri
Linux görüntünüzü getirirken iki seçeneğiniz vardır:

- Geliştirme ve test ortamında basit VM oluşturmak için yönetilen görüntüler.
- Görüntü oluşturma ve resim paylaşma için [paylaşılan görüntü Galerisi](../shared-image-galleries.md) .


### <a name="managed-images"></a>Yönetilen görüntüler

Yönetilen görüntüler birden çok VM oluşturmak için kullanılabilir, ancak birçok sınırlaması vardır. Yönetilen görüntüler, yalnızca genelleştirilmiş bir kaynaktan (VM veya VHD) oluşturulabilir. Yalnızca aynı bölgedeki sanal makineler oluşturmak için kullanılabilir ve abonelikler ve kiracılar arasında paylaştırılamaz.

Yönetilen görüntüler, tek bir bölge ve abonelik içinde kullanmak üzere birkaç basit Genelleştirilmiş görüntü gereken geliştirme ve test ortamları için kullanılabilir. 

### <a name="azure-shared-image-gallery-sig"></a>Azure Paylaşılan görüntü Galerisi (SıG)

[Paylaşılan görüntü galerileri](../shared-image-galleries.md) , görüntü oluşturmak, yönetmek ve ölçeği ölçeklenebilir şekilde paylaşmak için önerilir. Paylaşılan görüntü galerileri, görüntülerinizin etrafında yapı ve kuruluş oluşturmanıza yardımcı olur.  

- Hem Genelleştirilmiş hem de özelleştirilmiş görüntüler için destek.
- Hem 1. nesil hem de 2 görüntü için destek.
- Görüntülerin genel çoğaltması.
- Daha kolay yönetim için görüntülerin sürümü oluşturma ve gruplama.
- Kullanılabilirlik Alanları destekleyen bölgelerde, bölge yedekli depolama (ZRS) ile yüksek oranda kullanılabilir görüntüler. ZRS, ZGen hatalarıyla karşı daha iyi esnekliği sunar.
- Azure RBAC kullanarak abonelikler arasında ve hatta Active Directory (AD) kiracılar arasında paylaşım yapın.
- Her bölgedeki görüntü çoğaltmalarıyla dağıtımlarınızı ölçeklendirin.

Yüksek düzeyde bir SıG oluşturursunuz ve şu konumda oluşturulur:
- Görüntü tanımları-Bunlar, görüntü gruplarını tutan kapsayıcılardır.
- Görüntü sürümleri-bunlar gerçek görüntülerdir



## <a name="hyper-v-generation"></a>Hyper-V oluşturma

Azure; Hyper-V oluşturma 1 (Gen1) ve 2. nesil (Gen2), Gen2 en son kuşak ve Gen1 üzerinde ek işlevsellik sunmaktadır. Örneğin: daha fazla bellek, Intel Software Guard uzantıları (Intel SGX) ve sanallaştırılmış kalıcı bellek (vPMEM). Şirket içinde çalışan 2. nesil VM 'lerde, henüz Azure 'da desteklenmeyen bazı özellikler var. Daha fazla bilgi için özellikler ve yetenekler bölümüne bakın. Daha fazla bilgi için bu [makaleye](../generation-2.md)bakın. Ek işlevselliğe ihtiyacınız varsa Gen2 görüntüleri oluşturun.

Hala kendi görüntünüzü oluşturmanız gerekiyorsa, [görüntü önkoşullarını](./create-upload-generic.md)karşıladığından emin olun ve Azure 'a yükleyin. Dağıtıma özgü gereksinimler:


- [CentOS Tabanlı Dağıtımlar](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES ve openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Sonraki adımlar

[Paylaşılan görüntü Galerisi](tutorial-custom-images.md)oluşturmayı öğrenin.