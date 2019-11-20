---
title: Azure’da Linux VM’nizi iyileştirme
description: Azure 'da en iyi performansı elde etmek için Linux VM 'nizi ayarladığınızdan emin olmak için bazı iyileştirme ipuçlarını öğrenin
keywords: Linux sanal makinesi, sanal makine Linux, Ubuntu sanal makinesi
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: ea0d284b8220e4f8bc7bc1b91684654b32da7065
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035381"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Azure’da Linux VM’nizi iyileştirme
Bir Linux sanal makinesi (VM) oluşturmak, komut satırından veya portaldan kolayca yapılır. Bu öğreticide, Microsoft Azure platformunda performansını en iyi duruma getirecek şekilde ayarlamış olduğunuzdan emin olmanız gösterilmektedir. Bu konu, Ubuntu sunucu sanal makinesini kullanır, ancak aynı zamanda [kendi görüntülerinizi şablon olarak](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)kullanarak Linux sanal makinesi de oluşturabilirsiniz.  

## <a name="prerequisites"></a>Önkoşullar
Bu konu, zaten çalışan bir Azure aboneliğiniz olduğunu varsayar ([ücretsiz deneme kaydı](https://azure.microsoft.com/pricing/free-trial/)) ve Azure aboneliğinizde zaten bir VM sağladınız. [VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)oluşturmadan önce [az oturum açma](/cli/azure/reference-index) ile Azure ABONELIĞINIZDE en son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü olduğundan ve oturum açmış olduğunuzdan emin olun.

## <a name="azure-os-disk"></a>Azure işletim sistemi diski
Azure 'da bir Linux VM oluşturduktan sonra, onunla ilişkili iki disk vardır. **/dev/sda** , işletim sistemi diskinizdeki **/dev/sdb** geçici diskdedir.  Ana IŞLETIM sistemi diski ( **/dev/sda**), hızlı VM önyükleme süresi için en iyi duruma getirilmiş ve iş yükleriniz için iyi performans sağlamayan bir şey için kullanmayın. Verilerinize yönelik kalıcı ve iyileştirilmiş depolama alanı almak için sanal makinenize bir veya daha fazla disk eklemek istiyorsunuz. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Boyut ve performans hedefleri için disk ekleme
VM boyutuna bağlı olarak, bir G serisi makinede 32 bir D serisi ve 64 diskte 16 adede kadar ek disk (her biri 1 TB 'a kadar) ekleyebilirsiniz. Alanınız ve IOPS gereksinimlerinize göre gereken sayıda ek disk eklersiniz. Her diskin standart depolama için 500 IOPS ve Premium Depolama için disk başına en fazla 5000 IOPS performans hedefi vardır.

Önbellek ayarlarının **ReadOnly** veya **none**olarak ayarlandığı Premium Depolama disklerinde en yüksek IOPS 'yi başarmak Için, dosya sistemini Linux 'a bağlama sırasında **engelleri** devre dışı bırakmanız gerekir. Premium Depolama ile desteklenen disklere yazma işlemleri bu önbellek ayarları için dayanıklı olduğundan, engelleri gerekmez.

* **Reıfs**kullanıyorsanız, bağlama seçeneği `barrier=none` kullanarak engelleri devre dışı bırakın (engelleri etkinleştirmek için `barrier=flush`kullanın)
* **Ext3/ext4**kullanırsanız, bağlama seçeneği `barrier=0` kullanarak engelleri devre dışı bırakın (engelleri etkinleştirmek için `barrier=1`kullanın)
* **XFS**kullanıyorsanız, bağlama seçeneği `nobarrier` kullanarak engelleri devre dışı bırakın (engelleri etkinleştirmek için `barrier`seçeneğini kullanın)

## <a name="unmanaged-storage-account-considerations"></a>Yönetilmeyen depolama hesabı konuları
Azure CLı ile bir VM oluşturduğunuzda varsayılan eylem, Azure yönetilen disklerini kullanmaktır.  Bu diskler Azure platformu tarafından işlenir ve bunları depolamak için herhangi bir hazırlık veya konum gerektirmez.  Yönetilmeyen diskler için bir depolama hesabı gerekir ve bazı ek performans konuları vardır.  Yönetilen diskler hakkında daha fazla bilgi için bkz. [Azure Yönetilen Disklere genel bakış](../windows/managed-disks-overview.md).  Aşağıdaki bölümde, yalnızca yönetilmeyen diskleri kullandığınızda performans konuları özetlenmektedir.  Yine, varsayılan ve önerilen depolama çözümü yönetilen diskleri kullanmaktır.

Yönetilmeyen diskler içeren bir sanal makine oluşturursanız, yakınlık kapanışını sağlamak ve ağ gecikmesini en aza indirmek için VM 'niz ile aynı bölgede bulunan depolama hesaplarından disk iliştirdiğinizden emin olun.  Her standart depolama hesabında en fazla 20 k IOPS ve 500 TB boyutunda kapasite vardır.  Bu sınır, hem işletim sistemi diski hem de oluşturduğunuz tüm veri diskleri dahil olmak üzere yaklaşık 40 fazla kullanılan disk için geçerlidir. Premium Depolama hesapları için Maksimum IOPS sınırı yoktur, ancak 32 TB boyutunda bir sınır vardır. 

Yüksek IOPS iş yükleri ile ilgilenirken ve diskleriniz için standart depolama seçtiğinizde, standart depolama hesapları için 20.000 IOPS sınırına ulaştığınızdan emin olmak için diskleri birden çok depolama hesabında bölmeniz gerekebilir. VM 'niz, en iyi yapılandırmanızı elde etmek için farklı depolama hesapları ve depolama hesabı türlerindeki disklerin bir karışımını içerebilir.
 

## <a name="your-vm-temporary-drive"></a>VM geçici sürücünüz
Varsayılan olarak, bir VM oluşturduğunuzda, Azure size bir işletim sistemi diski ( **/dev/sda**) ve geçici bir disk ( **/dev/sdb**) sağlar.  Eklediğiniz tüm ek diskler **/dev/SDC**, **/dev/sdd**, **/dev/SDE** vb. olarak gösterilir. Geçici diskinizdeki tüm veriler ( **/dev/sdb**) dayanıklı değildir ve VM yeniden boyutlandırma, yeniden dağıtma veya bakım gibi belırlı olaylar sanal makinenizin yeniden başlatılmasını zorlarsa kaybolabilir.  Geçici diskinizin boyutu ve türü, dağıtım zamanında seçtiğiniz VM boyutuyla ilgilidir. Tüm Premium boyut sanal makineleri (DS, G ve DS_V2 serisi) geçici sürücü, 48k IOPS 'nin ek performansı için yerel bir SSD tarafından desteklenir. 

## <a name="linux-swap-partition"></a>Linux takas bölümü
Azure VM 'niz bir Ubuntu veya CoreOS görüntüsünden ise, CustomData kullanarak Cloud-init ' e Cloud-config gönderebilirsiniz. Cloud-init kullanan [özel bir Linux görüntüsünü karşıya](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) yüklediyseniz, Cloud-init kullanarak takas bölümlerini de yapılandırırsınız.

Ubuntu bulut görüntülerinde, takas bölümünü yapılandırmak için Cloud-init ' i kullanmanız gerekir. Daha fazla bilgi için bkz. [Azureswappartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Cloud-init desteği olmayan görüntüler için, Azure Marketi 'nden dağıtılan VM görüntülerinin IŞLETIM sistemiyle tümleştirilmiş bir VM Linux Aracısı vardır. Bu aracı, sanal makinenin çeşitli Azure hizmetleriyle etkileşime geçmesini sağlar. Azure Marketi 'nden standart bir görüntü dağıttığınız varsayılarak, Linux takas dosyası ayarlarınızı doğru şekilde yapılandırmak için aşağıdakileri yapmanız gerekir:

**/Etc/waagent.exe** dosyasındaki iki girişi bulun ve değiştirin. Özel bir takas dosyasının ve takas dosyasının boyutunun varlığını denetler. Doğrulamanız gereken parametreler `ResourceDisk.EnableSwap` ve `ResourceDisk.SwapSizeMB` 

Düzgün şekilde etkinleştirilmiş bir diski ve bağlı takas dosyasını etkinleştirmek için, parametrelerin aşağıdaki ayarlara sahip olduğundan emin olun:

* ResourceDisk. EnableSwap = Y
* ResourceDisk. SwapSizeMB = {gereksinimlerinizi karşılayacak şekilde MB olarak boyut} 

Değişikliği yaptıktan sonra, bu değişiklikleri yansıtmak için waagent 'ı yeniden başlatmanız veya Linux VM 'nizi yeniden başlatmanız gerekir.  Boş alanı görüntülemek için `free` komutunu kullandığınızda değişikliklerin uygulandığını ve bir takas dosyası oluşturulduğunu bilirsiniz. Aşağıdaki örnekte, **waagent. conf** dosyasını değiştirmenin sonucu olarak bir 512MB takas dosyası oluşturuldu:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Premium Depolama için g/ç zamanlama algoritması
2\.6.18 Linux çekirdeğiyle, varsayılan g/ç zamanlama algoritması son tarihten sonra CFQ (tamamen dengeli kuyruk algoritması) olarak değiştirilmiştir. Rastgele erişim g/ç desenleri için, CFQ ve son tarih arasındaki performans farklılıklarına göz ardı edilebilir bir farklılık vardır.  Disk g/ç deseninin ağırlıklı sıralı olduğu SSD tabanlı diskler için, NOOP veya son tarih algoritmasına geri dönmek daha iyi g/ç performansı elde edebilir.

### <a name="view-the-current-io-scheduler"></a>Geçerli g/ç zamanlayıcısını görüntüleme
Aşağıdaki komutu kullanın:  

```bash
cat /sys/block/sda/queue/scheduler
```

Geçerli zamanlayıcıyı belirten aşağıdaki çıktıyı görürsünüz.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>G/ç zamanlama algoritmasının geçerli cihazını (/dev/sda) değiştirin
Aşağıdaki komutları kullanın:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Bu ayarın tek başına **/dev/sda** için uygulanması yararlı değildir. Sıralı g/ç 'nin g/ç düzenlerini birbirinden ayıran tüm veri disklerinde ayarlanır.  

**Grub. cfg** dosyasının başarıyla yeniden yapıldığını ve varsayılan ZAMANLAYıCıNıN noop olarak güncelleştirildiğini belirten aşağıdaki çıktıyı görmeniz gerekir.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Red Hat dağıtım ailesi için yalnızca aşağıdaki komuta ihtiyacınız vardır:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Daha yüksek g/Ops elde etmek için yazılım RAID kullanma
İş yükleriniz tek bir diskin sağlayabileceğinden daha fazla IOPS gerektiriyorsa, birden çok diskin yazılım RAID yapılandırmasını kullanmanız gerekir. Azure, yerel yapı katmanında zaten disk esnekliği gerçekleştirdiğinden, RAID-0 dizme yapılandırmasından en yüksek düzeyde performans elde edersiniz.  Azure ortamında diskler sağlayın ve oluşturun ve sürücüleri bölümlemeden, biçimlendirmeden ve bağlamadan önce Linux sanal makinenize ekleyin.  Azure 'da Linux sanal makinenizde yazılım RAID kurulumunu yapılandırma hakkında daha fazla bilgi için, **[Linux 'Ta yazılım yapılandırma](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** belgesinde bulunabilir.

Geleneksel RAID yapılandırmasına alternatif olarak, bir dizi fiziksel diski tek bir şeritli mantıksal depolama biriminde yapılandırmak için mantıksal birim Yöneticisi 'Ni (LVM) yüklemeyi de tercih edebilirsiniz. Bu yapılandırmada, okuma ve yazma işlemleri, birim grubunda bulunan birden çok diske dağıtılır (RAID0 benzer). Performans nedenleriyle, büyük olasılıkla mantıksal birimlerinizi, okuma ve yazma işlemlerinin tüm ekli veri disklerinizi kullanmasını sağlayacak şekilde eklemek isteyeceksiniz.  Azure 'daki Linux VM 'niz üzerinde bir şeritli mantıksal birim yapılandırma hakkında daha fazla ayrıntı, **[Azure belgesindeki bir LINUX sanal makinesinde bulunan LVM yapılandırma](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** bölümünde bulunabilir.

## <a name="next-steps"></a>Sonraki Adımlar
Tüm iyileştirme tartışmalarında olduğu gibi, değişikliğin etkisini ölçmek için her değişiklikten önce ve sonra testler gerçekleştirmeniz gerektiğini unutmayın.  İyileştirme, ortamınızdaki farklı makinelerde farklı sonuçlar içeren adım adım bir işlemdir.  Bir yapılandırma için ne işe yarar, diğerleri için çalışmayabilir.

Ek kaynaklara yönelik bazı yararlı bağlantılar:

* [Azure Linux Aracısı Kullanıcı Kılavuzu](../extensions/agent-linux.md)
* [Azure Linux VM 'lerinde MySQL performansını iyileştirme](classic/optimize-mysql.md)
* [Linux 'ta yazılım RAID yapılandırma](configure-raid.md)
