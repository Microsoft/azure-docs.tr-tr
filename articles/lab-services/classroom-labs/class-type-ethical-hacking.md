---
title: Azure Lab Services ile ahlak hacme Laboratuvarı ayarlama | Microsoft Docs
description: Azure Lab Services kullanarak bir laboratuvarı nasıl ayarlayacağınızı öğrenmek için, ahlak hackinin
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133184"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Ahlak hacme sınıfı öğretmek için laboratuvar ayarlama 
Bu makalede, ahlak korsanın adli tarafına odaklanan bir sınıfın nasıl ayarlanacağı gösterilmektedir. Ahlak hacham topluluk tarafından kullanılan bir uygulama olan sızma testi, birisi kötü niyetli bir saldırganın yararlanmasına yönelik güvenlik açıklarını göstermek üzere sisteme veya ağa erişim kazanmayı denediğinde oluşur. 

Ahlak hacklik bir sınıfta öğrenciler, güvenlik açıklarına karşı savunma için modern teknikler öğrenmesini sağlayabilir. Her öğrenci, iki iç içe sanal makineye sahip bir sanal makine olan [Metasploitable3](https://github.com/rapid7/metasploitable3) görüntüsüne sahip bir sanal makine ve [kalı Linux](https://www.kali.org/) görüntüsüne sahip başka bir makine içeren bir Windows Server konak sanal makinesi alır Metasplosever sanal makinesi, daha fazla yararlanmak için kullanılır ve kalı sanal makinesi, adli görevlerini yürütmek için gereken araçlara erişim sağlar.

Bu makalede iki ana bölüm bulunur. Birinci bölüm, sınıf laboratuvarının nasıl oluşturulacağını ele alır. İkinci bölüm, iç içe sanallaştırma etkinleştirilmiş ve gerekli araçlar ve görüntülerle şablon makinenin nasıl oluşturulacağını ele alır. Bu durumda, görüntüleri barındırmak üzere Hyper-V ' y i destekleyen bir makinede Metasplosever görüntüsü ve bir kalı Linux görüntüsü.

## <a name="lab-configuration"></a>Laboratuvar yapılandırması
Bu Laboratuvarı kurmak için başlamak üzere bir Azure aboneliğine sahip olmanız gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. Bir Azure aboneliği aldıktan sonra, Azure Lab Services yeni bir laboratuvar hesabı oluşturabilir veya mevcut bir hesabı kullanabilirsiniz. Yeni bir laboratuvar hesabı oluşturmak için aşağıdaki öğreticiye bakın: [Laboratuvar hesabı ayarlama öğreticisi](tutorial-setup-lab-account.md).

Yeni bir laboratuvar oluşturmak ve ardından aşağıdaki ayarları uygulamak için [Bu öğreticiyi](tutorial-setup-classroom-lab.md) izleyin:

| Sanal makine boyutu | Görüntü |
| -------------------- | ----- | 
| Orta (Iç Içe sanallaştırma) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Şablon makinesi 

Şablon makinesi oluşturulduktan sonra, aşağıdaki üç ana görevi gerçekleştirmek için makineyi başlatın ve bu sunucuya bağlanın. 
 
1. İç içe sanallaştırma için makineyi ayarlayın. Hyper-V gibi uygun Windows özelliklerinin tümünü sağlar ve Hyper-V görüntülerinin ağını, birbirleriyle ve internet ile iletişim kurabilmek üzere ayarlar.
2. [Kalı](https://www.kali.org/) Linux görüntüsünü ayarlayın. Kalı, sızma testi ve güvenlik denetimi için araçlar içeren bir Linux dağıtımıdır.
3. Metasplosever görüntüsünü ayarlayın. Bu örnekte, [Metasploitable3](https://github.com/rapid7/metasploitable3) görüntüsü kullanılacaktır. Bu görüntü, özellikle güvenlik açıklarına karşı bir şekilde oluşturulur.

Yukarıda özetlenen görevleri otomatikleştiren bir betik, [laboratuar hizmetleri ahlak korsanlık betikleri](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)bölümünde mevcuttur.

### <a name="prepare-template-machine-for-nested-virtualization"></a>İç içe sanallaştırma için şablon makinesini hazırlama
Şablon sanal makinenizi iç içe sanallaştırma için hazırlamak üzere [Bu makaledeki](how-to-enable-nested-virtualization-template-vm.md) yönergeleri izleyin. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Kalı Linux görüntüsü ile iç içe bir sanal makine ayarlama
Kalı, sızma testi ve güvenlik denetimi için araçlar içeren bir Linux dağıtımıdır.

1. [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)görüntüyü indirin.  
    1. Hyper-V için **kalı Linux Hyper-v 64 bitini** indirin.
    1. .7z dosyasını ayıklayın.  Henüz 7 zip yoksa, [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)adresinden indirin. Daha sonra gerekli olacak şekilde ayıklanan klasörün konumunu unutmayın.
2. Yönetim araçlarından **Hyper-V Yöneticisi 'ni** açın.
1. **Eylem**' i seçin ve ardından **sanal makineyi içeri aktar**' ı seçin. 
1. **Sanal makine Içeri aktarma** Sihirbazı ' nın **klasörü bul** sayfasında, kalı Linux görüntüsünü tutan ayıklanan klasörün konumunu seçin.

    ![Klasörü bul iletişim kutusu](../media/class-type-ethical-hacking/locate-folder.png)
1. **Sanal makine seç** sayfasında, kalı Linux görüntüsünü seçin.  Bu durumda, görüntü **kalı-Linux-2019,3-hyperv**' dir.

    ![Kalı görüntüsünü seçin](../media/class-type-ethical-hacking/select-kali-image.png)
1.  **Içeri aktarma türünü seçin** sayfasında, **sanal makineyi Kopyala (yenı bir benzersiz kimlik oluştur)** seçeneğini belirleyin.

    ![İçeri aktarma türünü seçin](../media/class-type-ethical-hacking/choose-import-type.png)
1. **Sanal makine dosyaları Için klasörleri seçin** sayfasında varsayılan ayarları kabul edin ve **sanal sabit disk sayfalarını depolamak için klasörler** ' i seçin ve ardından **İleri**' yi seçin.
1. **Ağ bağlantısı** sayfasında, bu makalenin **iç Içe sanallaştırma için şablonu hazırla** bölümünde daha önce oluşturulmuş olan **labservicesswitch** ' i seçin ve ardından **İleri**' yi seçin.

    ![Ağ sayfasına Bağlan](../media/class-type-ethical-hacking/connect-network.png)
1. **Özet** sayfasında **son** ' u seçin. Kopyalama ve içeri aktarma işlemleri tamamlanana kadar bekleyin. Kalı Linux sanal makinesi artık Hyper-V ' d a kullanılabilir olacaktır.
1. **Hyper-V Yöneticisi**'Nde, **Başlat** -> **eylem** ' i seçin, sonra sanal **makineye bağlanmak için -> ** **eylem** ' i seçin.  
12. Varsayılan Kullanıcı adı `root` ve parola `toor`. 

    > [!NOTE]
    > Görüntünün kilidini açmanız gerekiyorsa, CTRL tuşuna basın ve fareyi yukarı doğru sürükleyin.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Metasplosever görüntüsü ile iç içe bir VM ayarlama  
Rapid7 Metasplosever görüntüsü, özellikle güvenlik açıklarına göre yapılandırılmış bir görüntüdür. Bu görüntüyü test etmek ve sorunları bulmak için kullanacaksınız. Aşağıdaki yönergelerde önceden oluşturulmuş bir Metasplosever görüntüsünün nasıl kullanılacağı gösterilmektedir. Ancak, Metasplosever görüntüsünün daha yeni bir sürümü gerekiyorsa bkz. [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3).

1. [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)gidin. Görüntüyü indirmek için formu doldurun ve **Gönder** düğmesini seçin.
1. **Metasplosever şimdi indir** düğmesini seçin.
1. ZIP dosyası indirildikten sonra ZIP dosyasını ayıklayın ve konumu hatırlayın.
1. Ayıklanan VMDK dosyasını Hyper-V ile kullanabilmeniz için bir VHDX dosyasına dönüştürün. Bunu yapmak için, PowerShell 'i yönetici ayrıcalıklarıyla açın ve VMDK dosyasının bulunduğu klasöre gidin ve şu yönergeleri izleyin:
    1. [Microsoft sanal makine dönüştürücüsünü](https://www.microsoft.com/download/details.aspx?id=42497)indirin ve istendiğinde mvmc_setup. msi dosyasını çalıştırın.
    1. PowerShell modülünü içeri aktarın.  Modülün yüklü olduğu varsayılan konum C:\Program Files\Microsoft sanal makine Dönüştürücüsü \ ' dir

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. VMDK 'yi Hyper-V tarafından kullanılabilen bir VHD dosyasına dönüştürün. Bu işlem birkaç dakika sürebilir.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Yeni oluşturulan metasplosever. vhdx 'i C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\dizinine kopyalayın. 
1. Yeni bir Hyper-V sanal makinesi oluşturun.
    1. **Hyper-V Yöneticisi 'ni**açın.
    1. **Yeni** -> **sanal makine** -> **eylem** ' i seçin.
    1. **Yeni sanal makine Sihirbazı**' nın **başlamadan önce** sayfasında **İleri**' ye tıklayın.
    1. **Ad ve konum belirtin** sayfasında, **ad**Için **Metasplosever** yazın ve **İleri**' yi seçin.

        ![Yeni VM görüntüsü Sihirbazı](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. **Üretimi belirle** sayfasında, Varsayılanları kabul edin ve **İleri**' yi seçin.
    1. **Bellek ata** sayfasında, **başlangıç belleğı**için **512 MB** girin ve **İleri**' yi seçin. 

        ![Bellek atama sayfası](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. **Ağı Yapılandır** sayfasında, bağlantıyı **bağlı değil**olarak bırakın. Ağ bağdaştırıcısını daha sonra ayarlayacaksınız.
    1. **Sanal sabit diski bağla** sayfasında, **var olan bir sanal sabit diski kullan**' ı seçin. Önceki adımda oluşturulan **metasplosever. vhdx** dosyasının konumuna gidin ve **İleri**' yi seçin. 

        ![Sanal ağ diski bağlama sayfası](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. **Yeni sanal makine Sihirbazı Tamamlanıyor** sayfasında **son**' u seçin.
    1. Sanal makine oluşturulduktan sonra, Hyper-V Yöneticisi 'nde bunu seçin. Makineyi henüz kapatmayın.  
    1. **Eylem** -> **ayarları**' nı seçin.
    1. **Metasplosever Için ayarlar** Iletişim kutusunda **Donanım Ekle**' yi seçin. 
    1. **Eski ağ bağdaştırıcısı**' nı seçin ve **Ekle**' yi seçin.

        ![Ağ bağdaştırıcısı sayfası](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. **Eski ağ bağdaştırıcısı** sayfasında, **sanal anahtar** ayarı için **labservicesswitch** ' i seçin ve **Tamam**' ı seçin. **Iç Içe sanallaştırma Için şablonu hazırlama** bölümünde Hyper-V için şablon makinesi hazırlanırken LabServicesSwitch oluşturuldu.

        ![Eski ağ bağdaştırıcısı sayfası](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasplosever görüntüsü artık kullanıma hazırdır. **Hyper-V Yöneticisi**'Nde, **Başlat** -> **eylem** ' i seçin, sonra sanal **makineye bağlanmak için -> ** **eylem** ' i seçin.  Varsayılan Kullanıcı adı **msfadmin** ve Password, **msfadmin**' dir. 


Şablon artık güncellenir ve bu durumda, sızan korsanlık bir test sınıfı için gereken görüntüler, sızma testi yapmak için araçlar içeren bir görüntü ve keşfedilecek güvenlik açıklarına sahip başka bir görüntü vardır. Şablon görüntüsü artık sınıfa yayımlanabilir. Şablonu laboratuvarda yayımlamak için şablon sayfasında **Yayımla** düğmesini seçin.
  

## <a name="cost"></a>Maliyet  
Bu laboratuvarın maliyetini tahmin etmek isterseniz, aşağıdaki örneği kullanabilirsiniz: 
 
20 saatlik zamanlanan sınıf süresi ve ev ödevi veya atamalar için 10 saatlik kota içeren 25 öğrenciye sahip bir sınıf için, laboratuvar fiyatı şöyle olacaktır: 

25 öğrenci * (20 + 10) saat * 55 laboratuvar birimi * 0,01 saat başına USD = 412,50 ABD Doları. 

Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sonuç
Bu makale, ahlak hacme sınıfı için Laboratuvar oluşturma adımlarında size kılavuzluk eden adımlar. Bu, penetderecelendirme testi için konak sanal makinesi içinde iki sanal makine oluşturmak üzere iç içe sanallaştırmayı ayarlama adımlarını içerir.

## <a name="next-steps"></a>Sonraki adımlar
Sonraki adımlar herhangi bir laboratuvarı ayarlamak için ortaktır:

- [Kullanıcı ekleme](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Öğrenciler Için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users). 

