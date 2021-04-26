---
title: Azure Data Box Gateway'i Hyper-V'de sağlama öğreticisi | Microsoft Docs
description: İkinci Azure Data Box Gateway dağıtma öğreticisinde Hyper-V'de sanal cihaz sağlama adımları anlatılmaktadır.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 2e54b8e58e8139c46639187957906790ffb0fe54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727694"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>Öğretici: Hyper-V ' d a Azure Data Box Gateway sağlama

## <a name="overview"></a>Genel Bakış

Bu öğreticide Data Box Gateway'i Windows Server 2016, Windows Server 2012 R2 veya Windows Server 2012'de Hyper-V üzerinde çalışan bir ana bilgisayarda sağlama adımları anlatılmaktadır.

Sanal cihaz sağlamak ve yapılandırmak için yönetici ayrıcalıklarına sahip olmanız gerekir. Sağlama ve ilk kurulum adımlarını tamamlamak yaklaşık 10 dakika sürecektir. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Ana bilgisayarın minimum cihaz gereksinimlerini karşıladığından emin olma
> * Hiper yöneticide bir sanal cihaz sağlama
> * Sanal cihazı başlatma ve IP adresini alma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Windows Server 2016 veya Windows Server 2012 R2 üzerinde Hyper-V çalıştıran ana bilgisayar sisteminde sanal cihaz sağlama önkoşulları aşağıda belirtilmiştir.

### <a name="for-the-data-box-gateway-resource"></a>Box Gateway kaynağı için

Başlamadan önce aşağıdakilerden emin olun:

* [Portalı Data Box Gateway için hazırlama](data-box-gateway-deploy-prep.md) adımlarını tamamladınız.
* [Portalı Data Box Gateway için hazırlama](data-box-gateway-deploy-prep.md) bölümünde anlatılan şekilde Azure portaldan Hyper-V sanal cihaz görüntüsünü indirdiniz.

  > [!IMPORTANT]
  > Data Box Gateway üzerinde çalıştırılan yazılım yalnızca Data Box Gateway kaynağıyla kullanılabilir.

### <a name="for-the-data-box-gateway-virtual-device"></a>Data Box Gateway sanal cihazı için

Cihazı dağıtmadan önce şunlardan emin olun:

* Windows Server 2012 R2 veya sonraki bir sürümü üzerinde Hyper-V çalıştıran ve cihaz sağlamak için kullanılabilecek bir ana bilgisayar sistemine erişiminiz var.
* Ana bilgisayar sistemi sanal cihazınızı sağlamak için aşağıdaki kaynakları ayırabiliyor:

  * En az 4 çekirdek.
  * En az 8 GB RAM. En az 16 GB RAM kullanmanızı öneririz.
  * Bir ağ arabirimi.
  * Bir 250 GB işletim sistemi diski.
  * Veriler için 2 TB sanal disk.

### <a name="for-the-network-in-the-datacenter"></a>Veri merkezindeki ağ için

Başlamadan önce:

* Data Box Gateway dağıtma ağ gereksinimlerini gözden geçirin ve veri merkezi ağını gereksinimlere göre yapılandırın. Daha fazla bilgi için bkz. [Data Box Gateway ağ gereksinimleri](data-box-gateway-system-requirements.md#networking-port-requirements).
* Cihazın en iyi şekilde çalışması için Internet bant genişliğinin en az 20 Mb/sn olduğundan emin olun.

## <a name="check-the-host-system"></a>Ana bilgisayar sistemini denetleyin

Sanal cihaz oluşturmak için şunlara ihtiyacınız vardır:

* Windows Server 2016, Windows Server 2012 R2 veya Windows Server 2012'de yüklü Hyper-V rolü.
* Ana bilgisayara bağlı Microsoft Windows istemcisine yüklenmiş Microsoft Hyper-V Yöneticisi.
* Sanal cihazı oluşturduğunuz donanımın (ana bilgisayar sisteminin) sanal cihaza aşağıdaki kaynakları ayırabildiğinden emin olun:

  * En az 4 sanal işlemci.
  * En az 8 GB RAM.
  * İnternet trafiği için ağa bağlı bir ağ arabirimi.
  * Bir 250 GB işletim sistemi diski.
  * Sistem verileri için 2 TB sanal disk.

## <a name="bitlocker-considerations"></a>BitLocker konuları

* Data Box Gateway sanal makinenizde BitLocker 'ı etkinleştirmenizi öneririz. Varsayılan olarak, BitLocker etkin değildir. Daha fazla bilgi için bkz.
  * [Hyper-V Yöneticisi 'nde şifreleme desteği ayarları](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [Sanal makinede BitLocker desteği](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>Hiper yöneticide bir sanal cihaz sağlama

Hiper yöneticinizde cihaz sağlamak için aşağıdaki adımları gerçekleştirin.

1. Windows Server ana bilgisayarınızda sanal cihaz görüntüsünü yerel sürücüye kopyalayın. Bu VHDX görüntüsünü Azure portaldan indirmiştiniz. Bu görüntüyü yordamın ilerleyen bölümlerinde kullanacağınız için kopyaladığınız konumu not edin.

2. **Sunucu Yöneticisi**'ni açın. Sağ üst köşede **Araçlar** ' a tıklayın ve **Hyper-V Yöneticisi**' ni seçin.

    ![Sunucu Yöneticisi 'de Hyper-V Yöneticisi 'Ni seçin](./media/data-box-gateway-deploy-provision-hyperv/image1.png)

3. **Hyper-V Yöneticisi**'nin kapsam bölmesinde sistem düğümünüze sağ tıklayarak bağlam menüsünü açın ve **Yeni** > **Sanal Makine**'ye tıklayın.

   ![Hyper-V Yöneticisi 'nde yeni sanal makine oluştur](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. Yeni Sanal Makine Sihirbazı'nın **Başlamadan önce** sayfasında **İleri**'ye tıklayın.
5. **Ad ve konum belirtin** sayfasında sanal cihazınız için bir **Ad** girin. **İleri**’ye tıklayın.

   ![Ad ve konum sayfasını belirtin](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. **Nesli belirtin** sayfasında .vhdx görüntü türü için **2. Nesil**'i seçin ve **İleri**'ye tıklayın.    

   ![Oluşturma sayfasını belirtin](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. **Bellek ata** sayfasında **Başlangıç belleği** değerini en az **8192 MB** yapın, dinamik bellek özelliğini etkinleştirmeyin ve **İleri**'ye tıklayın.

   ![Bellek atama sayfası](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. **Ağı yapılandır** sayfasında İnternete bağlı olan sanal anahtarı belirtin ve **İleri**'ye tıklayın.

   ![Ağ sayfasını Yapılandır](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. **Sanal sabit disk bağla** sayfasında **Var olan bir sanal sabit disk kullan**'ı seçin, sanal cihaz görüntüsünün konumunu belirtin ve **İleri**'ye tıklayın.

   ![Sanal sabit disk sayfasına Bağlan](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. **Özet** sayfasını gözden geçirin ve **Son**'a tıklayarak sanal makineyi oluşturun.

    ![Yeni sanal makine Sihirbazı sayfası Tamamlanıyor](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. En düşük gereksinimleri karşılamak için dört sanal işlemciye ihtiyacınız vardır. Dört sanal işlemci eklemek için, **Hyper-V Yöneticisi** penceresinde ana bilgisayar sisteminizi seçin. Sağ tarafta, **Sanal Makineler** listesinin altında bulunan bölmede az önce oluşturduğunuz sanal makineyi bulun. Makine adına sağ tıklayın ve **Ayarlar**'ı seçin.

    ![Sanal makine ayarları](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. **Ayarlar** sayfasında sol taraftaki bölmeden **İşlemci**'yi seçin. Sağ taraftaki bölmede **sanal işlemci sayısını** 4 (veya üzeri) olarak ayarlayın. **Uygula**’ya tıklayın.

    ![Ayarlar sayfasındaki sanal işlemcilerin sayısını ayarla](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. En düşük gereksinimleri karşılamak için 2 TB 'lık bir sanal veri diski de eklemeniz gerekir. **Ayarlar** sayfasında:

    1. Sol taraftaki bölmede **SCSI Denetleyicisi**'ni seçin.
    2. Sağ taraftaki bölmede **Sabit Sürücü**'yü seçin ve **Ekle**'ye tıklayın.

    ![Ayarlar sayfasında sabit sürücü ekleme](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. **Sabit sürücü** sayfasında **Sanal sabit disk** seçeneğini belirleyin ve **Yeni**'ye tıklayın. **Yeni Sanal Sabit Disk Sihirbazı** açılır.

    ![Yeni sanal sabit disk Sihirbazı](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. Yeni Sanal Sabit Disk Sihirbazı'nın **Başlamadan önce** sayfasında **İleri**'ye tıklayın.
16. **Disk Biçimini Seç** sayfasında varsayılan seçenek olan **VHDX** biçimini kabul edin. **İleri**’ye tıklayın.
17. **Disk Türünü Seç** sayfasında sanal sabit disk türünü **Dinamik olarak genişletilen** (önerilen) olarak ayarlayın. **Sabit boyutlu** diski de seçebilirsiniz ancak daha uzun süre beklemeniz gerekebilir. **Fark kayıt** seçeneğini kullanmamanızı öneririz. **İleri**’ye tıklayın.

    ![Disk türü seçin sayfası](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. **Ad ve konum belirtin** sayfasında, veri diski için bir **ad** ve **konum** (birine gidebilirsiniz) sağlayın. **İleri**’ye tıklayın.

    ![Ad ve konum sayfasını belirtin](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. **Diski Yapılandır** sayfasında, **Yeni boş bir sanal sabit disk oluştur** seçeneğini BELIRLEYIN ve boyutu **2 TB** (veya daha fazla) olarak belirtin.

    2 TB minimum gereksinimdir ancak isterseniz daha büyük bir disk de sağlayabilirsiniz. Sağlandıktan sonra diski daraltamazsınız. Diskin küçültülmeye çalışılması, cihazdaki tüm yerel verilerin kaybedilmesine neden olur. Veri diskinin genişletilmesi desteklenmiyor. **İleri**’ye tıklayın.

    ![Disk sayfasını yapılandırma](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. **Özet** sayfasında sanal veri diskinizin ayrıntılarını gözden geçirin ve her şey yolunda görünüyorsa **Son**'a tıklayarak diski oluşturun. Sihirbaz kapanır ve makinenize bir sanal sabit disk eklenir.

    ![Yeni sanal sabit disk Sihirbazı sayfası Tamamlanıyor](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. **Ayarlar** sayfasına geri dönün. **Tamam**'a tıklayarak **Ayarlar** sayfasını kapatın ve Hyper-V Yöneticisi penceresine dönün.

    ![Ayarlar sayfası](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

Sanal makineniz artık tam olarak yapılandırılmıştır.

> [!NOTE]
> Yapılandırılmış VHD 'nizi kopyalayarak yeni bir Data Box Gateway sağlayamazsınız. Her yeni Data Box Gateway sanal cihazın, Azure portal indirilen Hyper-V için bir sanal cihaz görüntüsünden sağlanması gerekir.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Sanal cihazı başlatma ve IP adresini alma

Sanal cihazınızı başlatmak ve bağlantı kurmak için aşağıdaki adımları izleyin.

#### <a name="to-start-the-virtual-device"></a>Sanal cihazı başlatmak için

1. Sanal cihazı başlatın.

   ![Sanal cihazı Başlat](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. Cihaz çalışmaya başladıktan sonra cihazı ve **Bağlan**'ı seçin.

3. Cihazın hazır olması için 10-15 dakika beklemeniz gerekebilir. Konsolda ilerleme durumunu gösteren bir durum iletisi görüntülenir. Cihaz hazır olduktan sonra **Eylem** bölümüne gidin. `Ctrl + Alt + Delete`Sanal cihazda oturum açmak için tuşuna basın. Varsayılan kullanıcı *EdgeUser*, varsayılan parola ise *Password1* şeklindedir.

   ![Sanal cihazda oturum açın](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. Adım 5-7 yalnızca DHCP bulunmayan bir ortamdaki önyükleme süreci için geçerlidir. DHCP ortamındaysanız bu adımları atlayabilirsiniz. Cihazınızı DHCP olmayan bir ortamda çalıştırdıysanız bunu belirten bir ileti açılacaktır.

5. Ağı yapılandırmak için `Get-HcsIpAddress` komutunu kullanarak sanal cihazınızda etkinleştirilmiş olan ağ arabirimlerini listeleyin. Cihazınızda tek bir ağ arabirimi varsa `Ethernet` varsayılan adı atanır.

6. Ağı yapılandırmak için `Set-HcsIpAddress` cmdlet'ini kullanın. Aşağıdaki örneğe bakın:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. İlk kurulum işlemleri tamamlandıktan ve cihaz önyüklendikten sonra cihaz başlık metnini görürsünüz. Cihazı yönetmek için başlık metninde görüntülenen IP adresini ve URL'yi not edin. Bu IP adresini kullanarak sanal cihazınızın web arabirimine bağlanıp yerel kurulum ve etkinleştirme işlemlerini gerçekleştirebilirsiniz.

   ![IP adresi ve bağlantı URL 'SI olan sanal cihaz başlığı](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

Cihazınız minimum yapılandırma gereksinimlerini karşılamıyorsa başlık metninde hata iletisi görüntülenir. Cihaz yapılandırmasını minimum gereksinimleri karşılayacak şekilde değiştirin. Ardından cihazı yeniden başlatıp bağlantı kurabilirsiniz. En düşük yapılandırma gereksinimleri için bkz. [Konağın en düşük cihaz gereksinimlerini karşıladığından emin olma](#check-the-host-system).

Yerel Web Kullanıcı arabirimini kullanarak ilk yapılandırma sırasında başka bir hata varsa, aşağıdaki iş akışlarına başvurun:

* [Web UI kurulumu sorunlarını gidermek için tanılama testlerini çalıştırın](data-box-gateway-troubleshoot.md#run-diagnostics).
* [Günlük paketi oluşturun ve günlük dosyalarını görüntüleyin](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki Data Box Gateway konularını öğrendiniz:

> [!div class="checklist"]
> * Ana bilgisayarın minimum cihaz gereksinimlerini karşıladığından emin olma
> * Hiper yöneticide bir sanal cihaz sağlama
> * Sanal cihazı başlatma ve IP adresini alma

Sanal cihazınıza bağlanma, kurulumunu yapma ve etkinleştirme adımları için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Data Box Gateway cihazınıza bağlanma ve kurulumunu yapma](./data-box-gateway-deploy-connect-setup-activate.md)
