---
title: Azure HPC Cache ve Azure NetApp Files kullanın
description: Azure NetApp Files ile depolanan verilere erişimi artırmak için Azure HPC Cache 'i kullanma
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: c6259dabd5ee9c53d37a3396f36832720a103c23
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582163"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Azure NetApp Files ile Azure HPC önbelleği kullanma

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) , Azure HPC önbelleğiniz için bir depolama hedefi olarak kullanabilirsiniz. Bu makalede, iki hizmetin birlikte nasıl çalıştığı açıklanmakta ve bunları ayarlamaya yönelik ipuçları verilmektedir.

Azure NetApp Files, ONTAP işletim sistemini Microsoft Azure ölçeklenebilirlik ve hızına göre birleştirir. Bu birleşim, kullanıcıların kodu yeniden yazmadan buluta iş akışlarını kaydırması için izin verir.

Azure HPC Cache bileşeni eklemek, birden çok Azure NetApp Files birimini toplanmış bir ad alanında sunarak dosya erişimini iyileştirebilir. Farklı bir hizmet bölgesinde bulunan birimler için uç önbelleğe alma özelliği sağlayabilir. Ayrıca, maliyet tasarrufu sağlamak için alt katman hizmet düzeylerinde oluşturulan birimler için isteğe bağlı performansı iyileştirebilir.

## <a name="overview"></a>Genel Bakış

Azure NetApp Files sistemi Azure HPC Cache ile arka uç depolama olarak kullanmak için bu işlemi izleyin.

1. [Aşağıdaki sisteminizi planlayın](#plan-your-azure-netapp-files-system)bölümündeki kılavuza göre Azure NetApp Files sistemi ve birimleri oluşturun.
1. Dosya erişiminizin gerektiği bölgede Azure HPC önbelleğini oluşturun. ( [Azure HPC önbelleği oluşturma](hpc-cache-create.md)bölümündeki yönergeleri kullanın.)
1. Önbellekte Azure NetApp Files birimlerine işaret eden [depolama hedeflerini tanımlayın](#create-storage-targets-in-the-cache) . Birimlere erişmek için kullanılan her benzersiz IP adresi için bir önbellek depolama hedefi oluşturun.
1. İstemcilerin Azure NetApp Files birimlerini doğrudan bağlamak yerine [Azure HPC önbelleğini](#mount-storage-targets) bağlamasını sağlayabilirsiniz.

## <a name="plan-your-azure-netapp-files-system"></a>Azure NetApp Files sisteminizi planlayın

Azure NetApp Files sisteminizi planlarken, Azure HPC önbelleğiyle sorunsuz bir şekilde tümleştirilediğinizden emin olmak için bu bölümdeki öğelere dikkat edin.

Ayrıca, Azure HPC Cache ile kullanmak üzere birimleri oluşturmadan önce [Azure NetApp Files belgelerini](../azure-netapp-files/index.yml) okuyun.

### <a name="nfs-client-access-only"></a>Yalnızca NFS istemci erişimi

Azure HPC Cache Şu anda yalnızca NFS erişimini destekliyor. SMB ACL veya POSIX modu bit birimleri ile kullanılamaz.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp Files için özel alt ağ

Azure NetApp Files birimleri için tek bir Temsilcili alt ağ kullanır. Bu alt ağı başka hiçbir kaynak kullanamaz. Ayrıca, bir sanal ağdaki yalnızca bir alt ağ, Azure NetApp Files için kullanılabilir. [Azure NetApp Files ağ planlamasına Ilişkin yönergeler](../azure-netapp-files/azure-netapp-files-network-topologies.md)hakkında daha fazla bilgi edinin.

### <a name="delegated-subnet-size"></a>Temsilci alt ağ boyutu

Azure HPC Cache ile kullanmak üzere bir Azure NetApp Files sistemi oluştururken, temsilcili alt ağ için en düşük boyutu kullanın.

Ağ maskesi/28 ile belirtilen en küçük boyut, 16 IP adresi sağlar. Uygulamada Azure NetApp Files, birim erişimi için yalnızca üç IP adresi kullanır. Bu, yalnızca Azure HPC önbelleğinde tüm birimleri kapsayan üç depolama hedefi oluşturmanız gerektiği anlamına gelir.

Temsil edilen alt ağ çok büyükse, Azure NetApp Files birimlerinin tek bir Azure HPC Cache örneğinden işleyebileceği daha fazla IP adresi kullanması mümkündür. Tek bir önbelleğin en fazla on depolama hedefi olabilir.

Azure NetApp Files belgelerdeki hızlı başlangıç örneği, çok büyük bir alt ağ sağlayan, temsilci alt ağı için 10.7.0.0/16 kullanır.

### <a name="capacity-pool-service-level"></a>Kapasite havuzu hizmet düzeyi

Kapasite havuzunuz için hizmet düzeyini seçerken iş akışınızı değerlendirin. Azure NetApp Files birime sık sık veri yazarsanız, geri yazma süresi yavaşsa önbelleğin performansı kısıtlanabilir. Sık yazma olacak birimler için yüksek bir hizmet düzeyi seçin.

Düşük hizmet düzeylerine sahip birimler aynı zamanda bir görevin başlangıcında bir gecikme gösterebilir ve bu da önbellek ön doldurma içeriğidir. Önbellek çalışır duruma geçtikten sonra iyi bir çalışma dosya kümesiyle çalıştıktan sonra gecikme, dikkat edilmelidir.

Oluşturma işleminden sonra değiştirilemediğinden, kapasite havuzu hizmet düzeyini zaman önünde planlamanız önemlidir. Yeni bir birimin farklı bir kapasite havuzunda oluşturulması ve verilerin üzerine kopyalanması gerekir.

Bir birimin depolama kotasını ve kapasite havuzunun boyutunu erişimi kesintiye uğratmadan değiştirebileceğinizi unutmayın.

## <a name="create-storage-targets-in-the-cache"></a>Önbellekte depolama hedefleri oluşturma

Azure NetApp Files sisteminiz kurulduktan ve Azure HPC önbelleği oluşturulduktan sonra, önbellekte dosya sistemi birimlerine işaret eden depolama hedeflerini tanımlayın.

Azure NetApp Files birimleriniz tarafından kullanılan her IP adresi için bir depolama hedefi oluşturun. IP adresi birimin Mount yönergeler sayfasında listelenir.

Birden çok birim aynı IP adresini paylaşıyorsa, bunların hepsi için bir depolama hedefi kullanabilirsiniz.  

Kullanılacak IP adreslerini bulmak için [Azure NetApp Files belgelerindeki bağlama yönergelerini](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) izleyin.

Ayrıca, Azure CLı ile IP adreslerini de bulabilirsiniz:

```bash
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp Files sistemdeki dışa aktarma adları tek bir yol bileşenine sahiptir. Azure NetApp Files içinde kök dışarı aktarma ``/`` için bir depolama hedefi oluşturmayı denemeyin, çünkü bu dışarı aktarma dosya erişimi sağlamıyor.

Bu depolama hedefleri için sanal ad alanı yollarında özel kısıtlama yoktur.

## <a name="mount-storage-targets"></a>Bağlama depolama hedefleri

İstemci makineler Azure NetApp Files birimlerini doğrudan bağlamak yerine önbelleği bağlamamalıdır. [Azure HPC önbelleğini bağlama](hpc-cache-mount.md)bölümündeki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure NetApp Files](../azure-netapp-files/index.yml) ayarlama ve kullanma hakkında daha fazla bilgi edinin
* Azure NetApp Files kullanmak için Azure HPC önbellek sisteminizi planlama ve ayarlama konusunda yardım için [desteğe başvurun](hpc-cache-support-ticket.md).
