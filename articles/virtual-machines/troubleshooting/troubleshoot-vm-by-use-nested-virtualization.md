---
title: Azure 'da iç içe sanallaştırmayı kullanarak sorun giderme Azure VM | Microsoft Docs
description: Azure 'da iç içe sanallaştırmayı kullanarak sorun giderme Azure VM
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: ad359a19cb42bf115189aca7905d1908d0dc5284
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087050"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Azure 'da iç içe sanallaştırmayı kullanarak sorun giderme Azure VM

Bu makalede, Microsoft Azure içinde iç içe bir sanallaştırma ortamının nasıl oluşturulacağı gösterilmektedir. böylece, sorun giderme amacıyla Hyper-V konağında (kurtarma VM) sorun sanal makinesinin diski bağlayabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Sorun sanal makinesini bağlamak için, kurtarma VM 'sinin aşağıdaki önkoşulları karşılaması gerekir:

-   Kurtarma sanal makinesi, sorunlu VM ile aynı konumda olmalıdır.

-   Kurtarma sanal makinesi, sorunlu VM ile aynı kaynak grubunda olmalıdır.

-   Kurtarma VM 'si, sorun sanal makinesi ile aynı depolama hesabı türünü (Standart veya Premium) kullanmalıdır.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>1\. adım: Bir kurtarma VM 'si oluşturun ve Hyper-V rolünü yükler

1.  Yeni bir kurtarma VM 'si oluşturun:

    -  İşletim Sistemi: Windows Server 2016 Datacenter

    -  Boyut: İç içe sanallaştırmayı destekleyen en az iki çekirdekli tüm v3 dizileri. Daha fazla bilgi için bkz. [Yeni Dv3 ve EV3 VM boyutlarını tanıtma](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Sorun VM 'si olarak aynı konum, depolama hesabı ve kaynak grubu.

    -  Sorunlu VM ile aynı depolama türünü seçin (Standart veya Premium).

2.  Kurtarma VM 'si oluşturulduktan sonra, Uzak Masaüstü ' ni kurtarma VM 'sine.

3.  Sunucu Yöneticisi, **Yönet** > **rol ve Özellik Ekle**' yi seçin.

4.  **Yükleme türü** bölümünde **rol tabanlı veya özellik tabanlı yükleme**' yi seçin.

5.  **Hedef sunucuyu Seç** bölümünde, kurtarma sanal makinesinin seçildiğinden emin olun.

6.  **Hyper-V rolü** > **Özellik Ekle**' yi seçin.

7.  **Özellikler** bölümünde **İleri ' yi** seçin.

8.  Kullanılabilir bir sanal anahtar varsa, bunu seçin. Aksi takdirde **İleri ' yi**seçin.

9.  **Geçiş** bölümünde **İleri** ' yi seçin.

10. **Varsayılan depolar** bölümünde, **İleri**' yi seçin.

11. Gerekirse sunucuyu otomatik olarak yeniden başlatmak için kutuyu işaretleyin.

12. **Yükle**’yi seçin.

13. Sunucunun Hyper-V rolünü yüklemesine izin verin. Bu işlem birkaç dakika sürer ve sunucu otomatik olarak yeniden başlatılır.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>2\. adım: Kurtarma VM 'sinin Hyper-V sunucusunda sorun sanal makinesini oluşturma

1.  Sorun sanal makinesine diskin adını kaydedin ve ardından sorun sanal makinesini silin. Tüm bağlı diskleri tutduğunuzdan emin olun. 

2.  Sorunlu VM 'nin işletim sistemi diskini kurtarma sanal makinesinin veri diski olarak ekleyin.

    1.  Sorun sanal makinesi silindikten sonra, kurtarma VM 'sine gidin.

    2.  **Diskler**' i seçin ve ardından **veri diski ekleyin**.

    3.  Sorun VM 'sinin diskini seçin ve ardından **Kaydet**' i seçin.

3.  Disk başarıyla eklendikten sonra, Uzak Masaüstü ' ni kurtarma VM 'sine.

4.  Disk Yönetimi 'ni (Diskmgmt. msc) açın. Sorun sanal makinesinin diskinin **çevrimdışı**olarak ayarlandığından emin olun.

5.  Hyper-V Yöneticisi 'Ni açın: **Sunucu Yöneticisi**, **Hyper-V rolünü**seçin. Sunucuya sağ tıklayın ve ardından **Hyper-V Yöneticisi**' ni seçin.

6.  Hyper-V Yöneticisi 'nde, kurtarma sanal makinesine sağ tıklayın ve ardından **Yeni** > **sanal makine** > **İleri**' yi seçin.

7.  VM için bir ad yazın ve ardından **İleri**' yi seçin.

8.  **1. nesil**' i seçin.

9.  Başlangıç belleğini 1024 MB veya daha fazla olacak şekilde ayarlayın.

10. Geçerliyse, oluşturulan Hyper-V ağ anahtarını seçin. Başka bir sonraki sayfaya geçin.

11. **Daha sonra bir sanal sabit disk Ekle**' yi seçin.

    ![Sanal sabit disk Ekle sonrası seçeneği hakkında resim](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. VM oluşturulduğunda **son** ' u seçin.

13. Oluşturduğunuz sanal makineye sağ tıklayın ve ardından **Ayarlar**' ı seçin.

14. **IDE denetleyicisi 0**' ni seçin, **sabit sürücü**' i seçin ve **Ekle**' ye tıklayın.

    ![Yeni sabit sürücü ekleyen resim](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. **Fiziksel sabit disk**' te, Azure VM 'ye eklediğiniz sorun sanal makinesinin diskini seçin. Listelenen diskleri görmüyorsanız, diskin disk yönetimi kullanılarak çevrimdışı olarak ayarlandığından emin olun.

    ![diski bağlama hakkındaki görüntü](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Seçin **Uygula**ve ardından **Tamam**.

18. VM 'ye çift tıklayın ve ardından başlatın.

19. Artık VM 'de şirket içi VM olarak çalışabilirsiniz. İhtiyacınız olan herhangi bir sorun giderme adımını izleyebilirsiniz.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>3\. adım: Azure VM 'nizi Azure 'da yeniden oluşturun

1.  VM 'yi yeniden çevrimiçi olduktan sonra, Hyper-V Yöneticisi 'nde VM 'yi kapatın.

2.  [Azure Portal](https://portal.azure.com) gidin ve kurtarma VM 'Si > diskler ' i seçin, diskin adını kopyalayın. Bu adı bir sonraki adımda kullanacaksınız. Sabit diski kurtarma VM 'sinden ayırın.

3.  **Tüm kaynaklar**' a gidin, disk adını arayın ve ardından diski seçin.

     ![diskte arama yapmak için görüntü](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. **VM oluştur**' a tıklayın.

     ![ile ilgili görüntü diskten VM oluşturur](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

VM 'yi diskten oluşturmak için Azure PowerShell de kullanabilirsiniz. Daha fazla bilgi için bkz. [PowerShell kullanarak var olan bir diskten yenı VM oluşturma](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Sonraki adımlar

Sanal makinenize bağlanırken sorun yaşıyorsanız bkz. [Azure VM 'ye YÖNELIK RDP bağlantılarında sorun giderme](troubleshoot-rdp-connection.md). VM 'niz üzerinde çalışan uygulamalara erişme sorunları için bkz. [WINDOWS VM 'de uygulama bağlantı sorunlarını giderme](troubleshoot-app-connection.md).
