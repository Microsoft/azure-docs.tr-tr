---
title: Azure sanal makinelerinde SAP HANA yüklemesi | Microsoft Docs '
description: Azure VM 'lerine SAP HANA Yükleme Kılavuzu
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 54bfd7c6e691efbd754e9ea2b804e16b3e07ebbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670961"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Azure sanal makinelerinde SAP HANA yüklemesi
## <a name="introduction"></a>Giriş
Bu kılavuz, Azure sanal makinelerinde HANA 'yı başarıyla dağıtmak için doğru kaynaklara işaret etmenize yardımcı olur. Bu kılavuz, bir Azure VM 'ye SAP HANA yüklemeden önce denetlemeniz gereken belge kaynaklarına işaret ediyor. Bu nedenle, Azure VM 'lerinde SAP HANA desteklenen bir yapılandırmayla sona erdirmek için doğru adımları gerçekleştirebileceksiniz.  

> [!NOTE]
> Bu kılavuzda, Azure VM 'lerine SAP HANA dağıtımları açıklanmaktadır. SAP HANA HANA büyük örneklerine dağıtma hakkında daha fazla bilgi için bkz. [Azure 'da SAP HANA (büyük örnekler) nasıl yüklenir ve yapılandırılır](./hana-installation.md).
 
## <a name="prerequisites"></a>Önkoşullar
Bu kılavuzda, hakkında bilgi sahibi olduğunuz varsayılır:
* SAP HANA ve SAP NetWeaver ve bunların Şirket içinde nasıl yükleneceği.
* Azure 'da SAP HANA ve SAP uygulama örnekleri yüklemek ve çalıştırmak.
* ' De belgelenen kavramlar ve yordamlar:
   * Azure sanal ağ planlaması ve Azure depolama kullanımını içeren Azure 'da SAP dağıtımını planlama. Bkz. [Azure sanal makineler 'de SAP NetWeaver-planlama ve uygulama kılavuzu](./planning-guide.md)
   * Azure 'da VM dağıtmanın yolları ve dağıtım ilkeleri. Bkz. [SAP Için Azure sanal makineleri dağıtımı](./deployment-guide.md)
   * [Azure sanal makineler için yüksek kullanılabilirlik SAP HANA](./sap-hana-availability-overview.md) belgelenen SAP HANA için yüksek kullanılabilirlik kavramları

## <a name="step-by-step-before-deploying"></a>Dağıtılmadan önce adım adım
Bu bölümde, Azure sanal makinesine SAP HANA yüklemesine başlamadan önce gerçekleştirmeniz gereken farklı adımlar listelenmiştir. Sıra numaralandırılır ve şu şekilde numaralandırılmalıdır:

1. Tüm olası dağıtım senaryoları Azure 'da desteklenmez. Bu nedenle, SAP HANA dağıtımınızla birlikte aklınızda bulundurmanız gereken senaryoya yönelik [Azure sanal makinesi desteklenen senaryolarında belge SAP iş yükünü](./sap-planning-supported-configurations.md) denetlemeniz gerekir. Senaryo listede yoksa, test edilmemiş olduğunu ve bir sonuç olarak desteklenmediğini varsaymak gerekir
2. SAP HANA dağıtımınız için bellek gereksiniminiz hakkında kaba bir fikriniz olduğunu varsayarsak, bir Azure VM 'ye bir sığdırma eklemeniz gerekir. SAP NetWeaver için sertifikalı tüm VM 'Ler, [sap destek not #1928533](https://launchpad.support.sap.com/#/notes/1928533)bölümünde belgelendiği gibi, SAP HANA sertifikalı değildir. SAP HANA sertifikalı Azure VM 'lerinin Truth kaynağı, Web sitesi [SAP HANA donanım dizinidir](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). **S** ile başlayan birimler, Azure VM 'leri değil, [Hana büyük örnek](./hana-overview-architecture.md) birimleridir.
3. Farklı Azure VM türlerinde SUSE Linux veya Red Hat Linux için farklı en düşük işletim sistemi sürümleri vardır. Web sitesi [SAP HANA donanım dizini](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)' nde, bu birimin ayrıntılı verilerini almak için SAP HANA sertifikalı birimler listesindeki bir girişe tıklamanız gerekir. Desteklenen HANA iş yükünün yanı sıra, SAP HANA için bu birimlerle desteklenen işletim sistemi sürümleri listelenir
4. İşletim sistemi sürümleri itibariyle, bazı en düşük çekirdek sürümlerini göz önünde bulundurmanız gerekir. Bu en düşük yayınlar, bu SAP destek notlarında belgelenmiştir:
    - [SAP destek notuna #2814271 SAP HANA yedekleme, sağlama hatası ile Azure 'da başarısız oluyor](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP destek notuna #2753418 süreölçer geri dönüşü nedeniyle olası performans düşüşü](https://launchpad.support.sap.com/#/notes/2753418)
    - [Azure 'da Hyper-V Için eksik VDSO desteği nedeniyle SAP destek notuna #2791572 performans düşüşü](https://launchpad.support.sap.com/#/notes/2791572)
4. Tercih edilen sanal makine türü için desteklenen IŞLETIM sistemi sürümüne bağlı olarak, istediğiniz SAP HANA sürümünün bu işletim sistemi sürümünde desteklenip desteklenmediğini denetlemeniz gerekir. Farklı Işletim sistemi sürümleri ile SAP HANA sürümlerinin destek matrisi için [#2235581 sap destek dekontunu](https://launchpad.support.sap.com/#/notes/2235581) okuyun.
5. Azure VM türü, işletim sistemi sürümü ve SAP HANA sürümünün geçerli bir birleşimini bullemeyebilirsiniz, SAP ürün kullanılabilirliği matrisini iade etmeniz gerekir. SAP kullanılabilirliği matrisinde, SAP HANA veritabanınıza karşı çalıştırmak istediğiniz SAP ürününün desteklenip desteklenmediğini öğrenebilirsiniz.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Adım adım VM dağıtımı ve konuk işletim sistemi konuları
Bu aşamada, sanal makineleri yüklemek için VM 'leri dağıtmaya yönelik adımları uygulamanız gerekir ve son olarak, yüklemeden sonra seçilen işletim sistemini en iyileştirmelisiniz.

1. Azure Galerisi 'nden temel görüntüyü seçin. SAP HANA için kendi işletim sistemi görüntünüzü derlemek istiyorsanız, başarılı bir SAP HANA yüklemesi için gerekli olan tüm farklı paketleri bilmeniz gerekir. Aksi takdirde, SAP için SUSE ve Red Hat görüntülerinin Azure görüntü Galerisi 'nden SAP HANA kullanılması önerilir. Bu görüntüler, başarılı bir HANA yüklemesi için gereken paketleri içerir. İşletim sistemi sağlayıcısıyla destek sözleşmeniz temelinde, kendi lisansınızı getiren bir görüntü seçmeniz gerekir. Ya da destek içeren bir işletim sistemi görüntüsü seçin
2. Kendi lisansınızı yapmanızı gerektiren bir konuk işletim sistemi görüntüsü seçtiyseniz, en son düzeltme eklerini indirebilmeniz ve uygulayabilmeniz için işletim sistemi görüntüsünü aboneliğinize kaydetmeniz gerekir. Bu adım, genel internet erişimi gerektirir. Özel örneğinizi (örneğin, Azure 'da bir SMT sunucusu) ayarlamadığınız müddetçe.
3. VM 'nin ağ yapılandırmasına karar verin. [Azure 'da altyapı yapılandırmalarının ve işlemlerinin SAP HANA](./hana-vm-operations.md)belgede daha fazla bilgi edinebilirsiniz. Azure 'daki sanal ağ kartlarına atayabileceğiniz hiçbir ağ işleme kotası olmadığını aklınızda bulundurun. Sonuç olarak, trafiği farklı vNIC 'ler aracılığıyla yönlendirmeye yönelik tek amaç, güvenlik konularını temel alır. Çoklu sanal NIC 'ler aracılığıyla trafik yönlendirme karmaşıklığı ve güvenlik yönleri tarafından zorlanan gereksinimler arasında desteklenebilir uzlaşması bulmanıza güveniyoruz.
3. VM dağıtıldıktan ve kaydedildikten sonra işletim sistemine en son düzeltme eklerini uygulayın. Kendi aboneliğiniz ile kaydedilir. Ya da işletim sistemi desteği içeren bir görüntü seçtiğinizde, sanal makinenin zaten bu düzeltme eklerine erişimi olması gerekir. 
4. SAP HANA için gereken uyarlayan 'yi uygulayın. Bu tunlar, bu SAP destek notlarında listelenmiştir:

    - [SAP destek notuna #2694118-Red Hat Enterprise Linux HA Add-On Azure 'da](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP destek notu #1984787-SUSE LINUX Enterprise Server 12: yükleme notları](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP destek notunun #2578899-SUSE Linux Enterprise Server 15: yükleme notunun](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP destek notunun #2002167-Red Hat Enterprise Linux 7. x: yükleme ve yükseltme](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP destek notunun #2292690-SAP HANA DB: RHEL 7 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP destek notunun #2772999-Red Hat Enterprise Linux 8. x: yükleme ve yapılandırma](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP destek notunun #2777782-SAP HANA DB: RHEL 8 için önerilen işletim sistemi ayarları](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP destek notunun #2455582-Linux: GCC 6. x ile derlenen SAP uygulamaları çalıştırma](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP destek notunun #2382421-ağ yapılandırmasını HANA ve işletim sistemi düzeyinde Iyileştirme](https://launchpad.support.sap.com/#/notes/2382421)

1. SAP HANA için Azure depolama türünü seçin. Bu adımda, SAP HANA yüklemesi için depolama düzenine karar vermeniz gerekir. Bağlı Azure disklerini ya da yerel Azure NFS paylaşımlarını kullanacaksınız. Azure depolama, kullanılabilir olan veya desteklenen farklı Azure Depolama türleri bileşimleri, [SAP HANA Azure sanal makine depolama yapılandırmalarında](./hana-vm-operations-storage.md)belgelenmiştir. Başlangıç noktası olarak belgelenen konfigürasyonları alın. Üretim dışı sistemler için, daha düşük aktarım hızı veya ıOPS yapılandırabilirsiniz. Üretim amacıyla, biraz daha fazla üretilen iş ve ıOPS yapılandırmanız gerekebilir.
2. [Azure yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md) 'YI, DBMS işlem günlüklerini içeren birimleriniz için yapılandırdığınızdan emin olun veya M serisi ya da Mv2-Series VM 'leri kullanırken günlükleri yineleyin. Yazma Hızlandırıcısı açıklanan sınırlamalara göz önünde bulundurun.
2. Dağıtılan VM 'ler üzerinde [Azure hızlandırılmış ağ oluşturma](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) özelliğinin etkinleştirilip etkinleştirilmediğini denetleyin.

> [!NOTE]
> Farklı SAP-ayarla profillerindeki veya notlarda açıklanan komutların tamamı Azure 'da başarıyla çalıştırılabilir. Temel alınan Azure ana bilgisayar donanımının güç modu işlenemediğinden, VM 'lerin güç modunu işleyen komutlar genellikle bir hatayla birlikte döndürülür.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Azure sanal makinelerine özgü adım adım hazırlıklar
Azure özelliklerinden biri, SAP konak aracısına ilişkin izleme verilerini sağlayan bir Azure VM uzantısının yüklemesidir. Bu izleme uzantısının yüklenmesiyle ilgili ayrıntılar şu şekilde belgelenmiştir:

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) , Azure 'Da Linux VM 'lerle SAP gelişmiş izlemeyi tartışır 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) , LINUX 'TA SAPOSCOL hakkında bilgi açıklar 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) MICROSOFT Azure üzerinde SAP için önemli izleme ölçümlerini açıklar
-  [SAP NetWeaver için Azure sanal makineler dağıtımı](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA yükleme
Dağıtılan Azure sanal makineleri ve işletim sistemleri kayıtlı ve yapılandırılmışsa, SAP HANA SAP yüklemesine göre yükleyebilirsiniz. Bu belgeleri kullanmaya başlamak iyi bir başlangıç olarak, bu SAP web sitesi [Hana kaynaklarıyla](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd) başlayın

Azure Premium Depolama veya ultra disk 'in doğrudan bağlı disklerini kullanan SAP HANA genişleme yapılandırması için, [Azure 'da altyapı yapılandırmalarının ve işlemlerinin SAP HANA](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out) belgedeki özellikleri okuyun


## <a name="additional-resources-for-sap-hana-backup"></a>SAP HANA yedekleme için ek kaynaklar
Azure VM 'lerinde SAP HANA veritabanlarının yedeklenmesi hakkında daha fazla bilgi için, bkz.:
* [Azure sanal makinelerinde SAP HANA için yedekleme Kılavuzu](./sap-hana-backup-guide.md)
* [Dosya düzeyinde Azure Backup SAP HANA](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>Sonraki adımlar
Belgeleri okuyun:

- [Azure'da SAP HANA altyapı yapılandırmaları ve işlemleri](./hana-vm-operations.md)
- [SAP HANA Azure sanal makine depolama alanı yapılandırmaları](./hana-vm-operations-storage.md)
