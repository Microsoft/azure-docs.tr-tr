---
title: Azure Marketi için Microsoft Azure barındırılan VM 'yi yapılandırma
description: Azure 'da barındırılan bir VM 'nin nasıl boyutlandırılacağını, güncelleştireceğinizi ve genelleştiriceğinizi açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: ce7fe49b07dc250a9f56ff73229e347b997f0cc0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824488"
---
# <a name="configure-the-azure-hosted-vm"></a>Azure 'da barındırılan VM 'yi yapılandırma

Bu makalede, Azure 'da barındırılan bir sanal makinenin (VM) nasıl boyutlandırılacağını, güncelleştireceğinizi ve genelleştirdiği açıklanmaktadır.  Bu adımlar, sanal makinenizin Azure Marketi 'nden dağıtılmasını hazırlamak için gereklidir.


## <a name="sizing-the-vhds"></a>VHD 'leri boyutlandırma

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Bir işletim sistemi (ve isteğe bağlı olarak ek hizmetler) ile önceden yapılandırılmış VM 'lerden birini seçtiyseniz, [sanal makine SKU 'ları sekmesinde](./cpp-skus-tab.md)açıklandığı gibi, zaten standart BIR Azure VM boyutu seçmiş olursunuz.  Çözümünüzü önceden yapılandırılmış bir işletim sistemi ile başlatmak önerilen yaklaşımdır.  Ancak, bir işletim sistemini el ile yüklüyorsanız, birincil VHD 'nizi VM yansımanıza göre boyutlandırmanız gerekir:

- Windows için, işletim sistemi VHD 'si 127-128 GB sabit biçimli VHD olarak oluşturulmalıdır. 
- Linux için, bu VHD 30-50 GB sabit biçimli VHD olarak oluşturulmalıdır.

Fiziksel boyut 127-128 GB 'den küçükse, VHD seyrek olmalıdır. Belirtilen temel pencereler ve SQL Server görüntüleri zaten bu gereksinimleri karşıladık, bu nedenle elde edilen VHD 'nin biçimini veya boyutunu değiştirmeyin. 

Veri diskleri 1 TB kadar büyük olabilir. Boyutlarına karar verirken, müşterilerin dağıtım sırasında bir görüntü içindeki VHD 'leri yeniden boyutlandıramayacağını unutmayın. Veri diski VHD 'leri sabit biçimli VHD 'ler olarak oluşturulmalıdır. Aynı zamanda seyrek olmalıdır. Veri diskleri başlangıçta boş olabilir veya veri içerebilir.


## <a name="install-the-most-current-updates"></a>En güncel güncelleştirmeleri yükler

İşletim sistemi VM 'lerinin temel görüntüleri, Yayımlanma tarihine kadar olan en son güncelleştirmeleri içerir. Oluşturduğunuz işletim sistemi VHD 'sini yayımlamadan önce, IŞLETIM sistemini ve yüklü tüm hizmetleri en son güvenlik ve bakım düzeltme ekleriyle güncelleştirdiğinizden emin olun.

Windows Server 2016 için, **Güncelleştirmeleri denetle** komutunu çalıştırın.  Aksi halde, önceki Windows sürümleri için bkz. [Windows Update aracılığıyla güncelleştirme alma](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows Update, en son kritik ve önemli güvenlik güncelleştirmelerini otomatik olarak yükler.

Linux dağıtımları için güncelleştirmeler genellikle bir komut satırı aracı veya bir grafik yardımcı programıyla indirilir ve yüklenir.  Örneğin Ubuntu Linux, işletim sistemini güncelleştirmek için [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) komutunu ve [Güncelleştirme Yöneticisi](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) aracını sağlar.


## <a name="perform-additional-security-checks"></a>Ek güvenlik denetimleri gerçekleştirme

Azure Marketi 'nde çözüm görüntüleriniz için yüksek düzeyde güvenlik korumanız gerekir.  Aşağıdaki makalede, bu hedefte size yardımcı olacak güvenlik yapılandırmalarının ve yordamlarının bir listesini bulabilirsiniz: [Azure Market görüntüleri Için güvenlik önerileri](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Bu önerilerin bazıları Linux tabanlı görüntülere özgüdür, ancak çoğu sanal makine görüntüsü için geçerlidir. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Özel yapılandırma ve zamanlanmış görevler gerçekleştirme

Ek yapılandırma gerekliyse, önerilen yaklaşım, sanal makinede dağıtıldıktan sonra son değişiklikler yapmak için başlangıçta çalışan zamanlanmış bir görev kullanmaktır.  Ayrıca aşağıdaki önerileri göz önünde bulundurun:
- Bir bir kez çalıştır görevi ise, görevin başarıyla tamamlandıktan sonra kendisini silmesi önerilir.
- Yalnızca her zaman garanti edilen bu iki sürücü olduğundan, konfigürasyonlar C veya D dışındaki sürücülere güvenmemelidir. C sürücüsü işletim sistemi diskdedir ve D sürücüsü geçici yerel disktir.

Linux özelleştirmeleri hakkında daha fazla bilgi için bkz. [Linux Için sanal makine uzantıları ve özellikleri](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Görüntüyü genelleştirme

Azure Marketi 'ndeki tüm görüntülerin genel bir biçimde yeniden kullanılabilir olması gerekir. Bu yeniden kullanılabilirlik elde etmek için, işletim sistemi VHD 'SI, bir VM 'den örneğe özgü tüm tanımlayıcıları ve yazılım sürücülerini kaldıran bir işlem olan *genelleştirilmelidir*.

### <a name="windows"></a>Windows

Windows işletim sistemi diskleri, [Sysprep aracıyla](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)genelleştirilmiştir. İşletim sistemini daha sonra güncelleştirip yeniden yapılandırırsanız, Sysprep 'i yeniden çalıştırmanız gerekir. 

> [!WARNING]
>  Güncelleştirmeler otomatik olarak çalıştırılabileceğinden, Sysprep 'i çalıştırdığınızda sanal makineyi dağıtana kadar kapatmanız gerekir.  Bu kapatılma, sonraki güncelleştirmelerin VHD işletim sisteminde veya yüklü hizmetlerde örneğe özgü değişiklikler yapmasını önler.

Sysprep 'i çalıştırma hakkında daha fazla bilgi için bkz. [BIR VHD Genelleştirme adımları](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

Aşağıdaki iki adımlı işlem, bir Linux sanal makinesini genelleştirir ve ayrı bir VM olarak yeniden dağıtır. Bu iki adım yalnızca işlemin temel sayısıdır. Bu iki adım hakkında daha fazla bilgi ve bunların oluşturulması gereken işlemler hakkında daha fazla bilgi için bkz. [sanal makine veya VHD 'nin görüntüsünü oluşturma](../../../virtual-machines/linux/capture-image.md). Azure Market teklifiniz için VHD oluşturma amacıyla, "yakalanan görüntüden VM oluşturma" bölümüne ulaştığınızda durulabiliyor olabilirsiniz.

#### <a name="remove-the-azure-linux-agent"></a>Azure Linux aracısını kaldırma
1.  SSH istemcisi kullanarak Linux sanal makinenize bağlanın.
2.  SSH penceresinde, aşağıdaki komutu yazın: <br/>
    `sudo waagent -deprovision+user`
3.  Devam etmek için `y` yazın. (`-force` parametresini önceki komuta ekleyebilirsiniz, bu onay adımından kaçının.)
4.  Komut tamamlandıktan sonra SSH istemcisini kapatmak için `exit` yazın.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Görüntüyü yakala
1.  Azure portal gidin, kaynak grubunuzu (RG) seçin ve VM 'yi serbest olarak ayırın.
2.  VHD 'niz şimdi genelleştirilmiştir ve bu VHD 'YI kullanarak yeni bir VM oluşturabilirsiniz.


## <a name="create-one-or-more-copies"></a>Bir veya daha fazla kopya oluşturun

VM kopyalarının oluşturulması genellikle yedekleme, test, özelleştirilmiş yük devretme veya yük dengeleme için, bir çözümün farklı yapılandırmalarının sunulmasını sağlamak için yararlıdır. Birincil VHD 'yi yineleme ve indirme hakkında daha fazla bilgi için, yönetilmeyen bir kopya oluşturmak üzere, bkz.:

- Linux VM: [Azure 'dan bir LINUX VHD indirin](../../../virtual-machines/linux/download-vhd.md)
- Windows VM: [Azure 'dan bir WINDOWS VHD indirin](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Sonraki adımlar

VM 'nin genelleştirilmesi, Serbest bırakılmışsa ve VM 'nin bir görüntüsünü oluşturduysanız sanal bir [sabit diskten bir sanal makine dağıtmaya](./cpp-deploy-vm-vhd.md)hazırsınızdır.
