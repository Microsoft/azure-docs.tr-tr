---
title: Azure Lab Services ile ahlak hacme Laboratuvarı ayarlama | Microsoft Docs
description: Azure Lab Services kullanarak bir laboratuvarı nasıl ayarlayacağınızı öğrenmek için, ahlak hackinin
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 97cdf13f39cc73ee7f35fb402229469195f1456c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97616431"
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

Bu makalenin geri kalanında, yukarıdaki görevleri tamamlamaya yönelik el ile yapılan adımlar ele alınacaktır.  Alternatif olarak, [Laboratuvar Hizmetleri Hyper-V betikleri](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) ve [Laboratuvar Hizmetleri ahlak özellikli betikleri](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)çalıştırabilirsiniz.

### <a name="prepare-template-machine-for-nested-virtualization"></a>İç içe sanallaştırma için şablon makinesini hazırlama

İç içe sanallaştırma için şablon sanal makinenizi hazırlamak üzere [iç içe sanallaştırmayı etkinleştirmek](how-to-enable-nested-virtualization-template-vm.md) için yönergeleri izleyin.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Kalı Linux görüntüsü ile iç içe bir sanal makine ayarlama

Kalı, sızma testi ve güvenlik denetimi için araçlar içeren bir Linux dağıtımıdır.

1. [Uygunsuz güvenlik kalı LINUX VM görüntülerinden](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)görüntü indirin.  İndirme sayfasında belirtilen varsayılan kullanıcı adını ve parolayı unutmayın.
    1. VMware için **kalı Linux vmware 64-bit (7z)** görüntüsünü indirin.
    1. .7z dosyasını ayıklayın.  Henüz 7 zip yoksa, adresinden indirin [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) . Daha sonra gerekli olacak şekilde ayıklanan klasörün konumunu unutmayın.
1. Ayıklanan VMDK dosyasını Hyper-V ile kullanabilmek için VHDX dosyasına dönüştürün. VMware görüntülerini Hyper-V görüntülerine dönüştürmek için kullanabileceğiniz çeşitli araçlar vardır.  [StarWind V2V dönüştürücüsünü](https://www.starwindsoftware.com/starwind-v2v-converter)kullanacağız.  İndirmek için, bkz. [StarWind V2V Converter indirme sayfası](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. **StarWind V2V dönüştürücüsünü** başlatın.
    1. **Dönüştürülecek görüntünün konumunu seçin** sayfasında, **yerel dosya**' yı seçin.  **İleri**’yi seçin.
    1. **Kaynak görüntü** sayfasında, ' a gidin ve **dosya adı** ayarı Için önceki adımda ayıklanan kalı Linux VMDK dosyasını seçin.  Dosya kalı-Linux-{Version}-VMware-AMD64. vmdk biçiminde olacaktır.  **İleri**’yi seçin.
    1. **Hedef görüntünün seçim Konumu** üzerinde **yerel dosya**' yı seçin.  **İleri**’yi seçin.
    1. **Hedef görüntü biçimini seçin** sayfasında, **VHD/VHDX**' i seçin.  **İleri**’yi seçin.
    1. **VHD/VHDX görüntü biçimi Için Seç seçeneği** sayfasında **VHDX growable Image**' i seçin.  **İleri**’yi seçin.
    1. **Hedef dosya adını seçin** sayfasında, varsayılan dosya adını kabul edin.  **Dönüştür**' ü seçin.
    1. **Dönüştürme** sayfasında görüntünün dönüştürülmesini bekleyin.  Bu işlem birkaç dakika sürebilir.  Dönüştürme tamamlandığında **son** ' u seçin.
1. Yeni bir Hyper-V sanal makinesi oluşturun.
    1. **Hyper-V Yöneticisi 'ni** açın.
    1. **Eylem**  ->  **Yeni**  ->  **sanal makine**' yi seçin.
    1. **Yeni sanal makine Sihirbazı**' nın **başlamadan önce** sayfasında **İleri**' yi seçin.
    1. **Ad ve konum belirtin** sayfasında, **ad** Için **kalı-Linux** girin ve **İleri**' yi seçin.
    1. **Üretimi belirle** sayfasında, Varsayılanları kabul edin ve **İleri**' yi seçin.
    1. **Bellek ata** sayfasında, **başlangıç belleğı** için **2048 MB** girin ve **İleri**' yi seçin.
    1. **Ağı Yapılandır** sayfasında, bağlantıyı **bağlı değil** olarak bırakın. Ağ bağdaştırıcısını daha sonra ayarlayacaksınız.
    1. **Sanal sabit diski bağla** sayfasında, **var olan bir sanal sabit diski kullan**' ı seçin. Önceki adımda oluşturulan **kalı-Linux-{Version}-VMware-AMD64. vmdk** dosyasının konumuna gidin ve **İleri**' yi seçin.
    1. **Yeni sanal makine Sihirbazı Tamamlanıyor** sayfasında **son**' u seçin.
    1. Sanal makine oluşturulduktan sonra, Hyper-V Yöneticisi 'nde bunu seçin. Makineyi henüz kapatmayın.  
    1. **Eylem**  ->  **ayarları**' nı seçin.
    1. İçin **kalı-Linux ayarları** Iletişim kutusunda **Donanım Ekle**' yi seçin.
    1. **Eski ağ bağdaştırıcısı**' nı seçin ve **Ekle**' yi seçin.
    1. **Eski ağ bağdaştırıcısı** sayfasında, **sanal anahtar** ayarı için **labservicesswitch** ' i seçin ve **Tamam**' ı seçin. **Iç Içe sanallaştırma Için şablonu hazırlama** bölümünde Hyper-V için şablon makinesi hazırlanırken LabServicesSwitch oluşturuldu.
    1. Kali-Linux resim artık kullanıma hazırdır. **Hyper-V Yöneticisi**'nde, **eylem**  ->  **Başlat**' ı ve ardından   ->  sanal makineye bağlanmak için eylem **Bağlan** ' ı seçin.  Varsayılan Kullanıcı adı **Kali** 'dir ve parola **kalı** olur.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Metasplosever görüntüsü ile iç içe bir VM ayarlama  

Rapid7 Metasplosever görüntüsü, özellikle güvenlik açıklarına göre yapılandırılmış bir görüntüdür. Bu görüntüyü test etmek ve sorunları bulmak için kullanacaksınız. Aşağıdaki yönergelerde önceden oluşturulmuş bir Metasplosever görüntüsünün nasıl kullanılacağı gösterilmektedir. Ancak, Metasplosever görüntüsünün daha yeni bir sürümü gerekiyorsa, bkz [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) ..

1. Metasplosever görüntüsünü indirin.
    1. Öğesine gidin [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html) . Görüntüyü indirmek için formu doldurun ve **Gönder** düğmesini seçin.
    2. **Metasplosever şimdi indir** düğmesini seçin.
    3. ZIP dosyası indirildiyse, ZIP dosyasını ayıklayın ve Metasplosever. vmdk dosyasının konumunu unutmayın.
1. Ayıklanan VMDK dosyasını Hyper-V ile kullanabilmek için VHDX dosyasına dönüştürün. VMware görüntülerini Hyper-V görüntülerine dönüştürmek için kullanabileceğiniz çeşitli araçlar vardır.  [StarWind V2V dönüştürücüsünü](https://www.starwindsoftware.com/starwind-v2v-converter) yeniden kullanacağız.  İndirmek için, bkz. [StarWind V2V Converter indirme sayfası](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. **StarWind V2V dönüştürücüsünü** başlatın.
    1. **Dönüştürülecek görüntünün konumunu seçin** sayfasında, **yerel dosya**' yı seçin.  **İleri**’yi seçin.
    1. **Kaynak görüntü** sayfasında, ' a gidin ve **dosya adı** ayarı Için önceki adımda ayıklanan metasplosever. vmdk ' yi seçin.  **İleri**’yi seçin.
    1. **Hedef görüntünün seçim Konumu** üzerinde **yerel dosya**' yı seçin.  **İleri**’yi seçin.
    1. **Hedef görüntü biçimini seçin** sayfasında, **VHD/VHDX**' i seçin.  **İleri**’yi seçin.
    1. **VHD/VHDX görüntü biçimi Için Seç seçeneği** sayfasında **VHDX growable Image**' i seçin.  **İleri**’yi seçin.
    1. **Hedef dosya adını seçin** sayfasında, varsayılan dosya adını kabul edin.  **Dönüştür**' ü seçin.
    1. **Dönüştürme** sayfasında görüntünün dönüştürülmesini bekleyin.  Bu işlem birkaç dakika sürebilir.  Dönüştürme tamamlandığında **son** ' u seçin.
1. Yeni bir Hyper-V sanal makinesi oluşturun.
    1. **Hyper-V Yöneticisi 'ni** açın.
    1. **Eylem**  ->  **Yeni**  ->  **sanal makine**' yi seçin.
    1. **Yeni sanal makine Sihirbazı**' nın **başlamadan önce** sayfasında **İleri**' yi seçin.
    1. **Ad ve konum belirtin** sayfasında, **ad** Için **Metasplosever** yazın ve **İleri**' yi seçin.

        ![Yeni VM görüntüsü Sihirbazı](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. **Üretimi belirle** sayfasında, Varsayılanları kabul edin ve **İleri**' yi seçin.
    1. **Bellek ata** sayfasında, **başlangıç belleğı** için **512 MB** girin ve **İleri**' yi seçin.

        ![Bellek atama sayfası](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. **Ağı Yapılandır** sayfasında, bağlantıyı **bağlı değil** olarak bırakın. Ağ bağdaştırıcısını daha sonra ayarlayacaksınız.
    1. **Sanal sabit diski bağla** sayfasında, **var olan bir sanal sabit diski kullan**' ı seçin. Önceki adımda oluşturulan **metasplosever. vhdx** dosyasının konumuna gidin ve **İleri**' yi seçin.

        ![Sanal ağ diski bağlama sayfası](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. **Yeni sanal makine Sihirbazı Tamamlanıyor** sayfasında **son**' u seçin.
    1. Sanal makine oluşturulduktan sonra, Hyper-V Yöneticisi 'nde bunu seçin. Makineyi henüz kapatmayın.  
    1. **Eylem**  ->  **ayarları**' nı seçin.
    1. **Metasplosever Için ayarlar** Iletişim kutusunda **Donanım Ekle**' yi seçin.
    1. **Eski ağ bağdaştırıcısı**' nı seçin ve **Ekle**' yi seçin.

        ![Ağ bağdaştırıcısı sayfası](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. **Eski ağ bağdaştırıcısı** sayfasında, **sanal anahtar** ayarı için **labservicesswitch** ' i seçin ve **Tamam**' ı seçin. **Iç Içe sanallaştırma Için şablonu hazırlama** bölümünde Hyper-V için şablon makinesi hazırlanırken LabServicesSwitch oluşturuldu.

        ![Eski ağ bağdaştırıcısı sayfası](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasplosever görüntüsü artık kullanıma hazırdır. **Hyper-V Yöneticisi**'nde, **eylem**  ->  **Başlat**' ı ve ardından   ->  sanal makineye bağlanmak için eylem **Bağlan** ' ı seçin.  Varsayılan Kullanıcı adı **msfadmin** ve Password, **msfadmin**' dir.

Şablon artık güncellenir ve bu durumda, sızan korsanlık bir test sınıfı için gereken görüntüler, sızma testi yapmak için araçlar içeren bir görüntü ve keşfedilecek güvenlik açıklarına sahip başka bir görüntü vardır. Şablon görüntüsü artık sınıfa yayımlanabilir. Şablonu laboratuvarda yayımlamak için şablon sayfasında **Yayımla** düğmesini seçin.

## <a name="cost"></a>Maliyet  

Bu laboratuvarın maliyetini tahmin etmek isterseniz, aşağıdaki örneği kullanabilirsiniz:

20 saatlik zamanlanan sınıf süresi ve ev ödevi veya atamalar için 10 saatlik kota içeren 25 öğrenciye sahip bir sınıf için, laboratuvar fiyatı şöyle olacaktır:

25 öğrenci \* (20 + 10) saat \* 55 laboratuvar birimi \* 0,01 saat başına abd doları = 412,50 ABD Doları

>[!IMPORTANT]
>Maliyet tahmini yalnızca örnek amaçlıdır. Fiyatlandırma hakkındaki güncel Ayrıntılar için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sonuç

Bu makale, ahlak hacme sınıfı için Laboratuvar oluşturma adımlarında size kılavuzluk eden adımlar. Bu, penetderecelendirme testi için konak sanal makinesi içinde iki sanal makine oluşturmak üzere iç içe sanallaştırmayı ayarlama adımlarını içerir.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adımlar herhangi bir laboratuvarı ayarlamak için ortaktır:

- [Kullanıcı ekle](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Öğrenciler Için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users).
