---
title: Azure Lab Services 'de Linux için grafik uzak masaüstünü etkinleştir | Microsoft Docs
description: Azure Lab Services 'deki bir laboratuvarda Linux sanal makineleri için Uzak Masaüstü 'nü etkinleştirmeyi öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 34c940fec388bb0e79ab5e1db9be6d52fb223873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647962"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Azure Lab Services 'da Linux sanal makineleri için grafik uzak masaüstü 'nü etkinleştirme
Bu makalede aşağıdaki görevlerin nasıl yapılacağı gösterilmektedir:

- Linux VM için grafik uzak masaüstü oturumlarını etkinleştirme
- RDP (Uzak Masaüstü Protokolü) veya X2Go uzak masaüstü istemcileri kullanarak bir Linux VM 'ye bağlanma

## <a name="set-up-graphical-remote-desktop-solution"></a>Grafik uzak masaüstü çözümünü ayarlama
Bir **Linux** görüntüsünden laboratuvar oluşturulduğunda, eğitmenin SSH kullanarak komut SATıRıNDAN şablon VM 'ye bağlanabilmesi için **SSH** (Secure Shell) erişimi otomatik olarak yapılandırılır.  Benzer şekilde, şablon VM yayımlandığında, öğrenciler SSH kullanarak VM 'lerine da bağlanabilir.

Bir **GUI** (grafik kullanıcı arabirimi) kullanarak BIR Linux VM 'sine bağlanmak Için, **RDP** veya **X2Go** kullanmanızı öneririz.  Bu seçeneklerin her ikisi de, bir eğitmenin şablon VM üzerinde bazı ek kurulum yapması için gereklidir:

### <a name="rdp-setup"></a>RDP kurulumu
RDP kullanmak için, eğitmenin şunları yapmanız gerekir:
  - Uzak Masaüstü bağlantısı 'nı etkinleştir; Bu özellikle, sanal makinenin RDP için bağlantı noktasını açmak için gereklidir.
  - RDP uzak masaüstü sunucusunu yükler.
  - Linux grafik masaüstü ortamı (MATE, XFCE vb.) yükler.

### <a name="x2go-setup"></a>X2Go kurulumu
X2Go kullanmak için, eğitmenin şunları yapmanız gerekir:
- X2Go uzak masaüstü sunucusunu yükler.
- Linux grafik masaüstü ortamı (MATE, XFCE vb.) yükler.

X2Go, SSH için zaten etkinleştirilmiş olan bağlantı noktasını kullanır.  Sonuç olarak, X2Go için VM 'de bir bağlantı noktasını açmak için ek yapılandırma gerekmez.

> [!NOTE]
> Ubuntu LTS 18,04 gibi bazı durumlarda, X2Go daha iyi performans sağlar.  Grafik Masaüstü ortamıyla etkileşim kurarken RDP ve uyarı gecikme süresi kullanırsanız, performansı iyileştirebilecek olduğundan X2Go kullanmayı deneyin.

> [!IMPORTANT]
>  Bazı Market görüntülerinin zaten bir grafik masaüstü ortamı ve uzak masaüstü sunucusu yüklü.  Örneğin, [Linux için veri bilimi sanal makinesi (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) zaten [Xfce ve X2Go sunucusu yüklenmiş ve istemci bağlantılarını kabul edecek şekilde yapılandırılmış](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>RDP için Uzak Masaüstü bağlantısı 'nı etkinleştir

Bu adım yalnızca RDP kullanarak bağlanmak için gereklidir.  Bunun yerine X2Go kullanmayı planlıyorsanız, X2Go SSH bağlantı noktasını kullandığından sonraki bölüme atlayabilirsiniz.

1.  Laboratuvar oluşturma sırasında, eğitmenin **Uzak Masaüstü bağlantısı etkinleştirme** seçeneği vardır.  Eğitmen, Linux VM üzerinde bir RDP uzak masaüstü oturumu için gereken bağlantı noktasını açmak için bu seçeneği **etkinleştirmelidir** .  Aksi takdirde, bu seçenek **devre dışı** BıRAKıLıRSA yalnızca SSH bağlantı noktası açılır.
  
    !["Uzak Masaüstü Bağlantısı etkinleştir" seçeneği ile "yeni laboratuvar" penceresini gösteren ekran görüntüsü.](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. **Uzak Masaüstü bağlantısı etkinleştirme** Iletisi kutusunda **Uzak Masaüstü ile devam et**' i seçin. 

    ![Linux görüntüsü için Uzak Masaüstü bağlantısını etkinleştirme](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>RDP veya X2Go 'i yükler

Laboratuvar oluşturulduktan sonra, eğitmenin bir grafik masaüstü ortamının ve uzak masaüstü sunucusunun şablon VM 'de yüklü olduğundan emin olması gerekir.  Eğitmenler, paketleri yüklemek için önce SSH kullanarak şablon VM 'sine bağlanmalıdır:
- RDP ya da X2Go uzak masaüstü sunucusu.
- MATE, XFCE vb. gibi bir grafik masaüstü ortamı.

Bu ayarlandıktan sonra, eğitmen, **Microsoft Uzak Masaüstü (RDP)** Istemcisi veya **X2Go** istemcisini kullanarak şablon VM 'ye bağlanabilir.

Şablon VM 'yi ayarlamak için aşağıdaki adımları izleyin:

1. Araç çubuğunda **şablonu Özelleştir** ' i görürseniz, bunu seçin. Ardından, **şablonu Özelleştir** Iletişim kutusunda **devam** ' ı seçin. Bu eylem, şablon VM 'yi başlatır.  

    ![Şablonu Özelleştir](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Şablon VM 'si başlatıldıktan sonra, **şablonu bağla** ' yı seçip araç çubuğunda **SSH aracılığıyla bağlanabilirsiniz** . 

    ![Laboratuvar oluşturulduktan sonra RDP aracılığıyla şablona bağlanma](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Aşağıdaki **Sanal makinenize Bağlan** iletişim kutusunu görürsünüz. Panoya kopyalamak için metin kutusunun yanındaki **Kopyala** düğmesini seçin. SSH bağlantı bilgilerini kaydedin. Sanal makineye bağlanmak için bir SSH terminalinde ( [Putty](https://www.putty.org/)gibi) Bu bağlantı bilgilerini kullanın.
 
    ![SSH bağlantı dizesi](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Tercih ettiğiniz grafik masaüstü ortamı ile birlikte RDP veya X2Go 'i de yükleyebilir.  Aşağıdaki yönergelere bakın:
    - [RDP 'yi yükleyip yapılandırma](../virtual-machines/linux/use-remote-desktop.md)
    - [X2Go 'yi yükleyip yapılandırın](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>GUI aracılığıyla şablon VM 'sine bağlanma

Şablon VM kurulduktan sonra, eğitmen, **Microsoft Uzak Masaüstü (RDP)** Istemcisi veya **X2GO** istemcisini kullanarak GUI aracılığıyla bağlanabilir.  Kullandığınız istemci, RDP veya X2Go 'nin şablon VM 'de uzak masaüstü sunucusu olarak yapılandırılıp yapılandırılmadığını bağlıdır.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Microsoft Uzak Masaüstü (RDP) istemcisi

Microsoft Uzak Masaüstü (RDP) istemcisi, RDP yapılandırılmış bir şablon sanal makinesine bağlanmak için kullanılır.  Uzak Masaüstü istemcisi Windows, Bermebook, Mac ve daha fazlası üzerinde kullanılabilir.  Daha fazla ayrıntı için [Uzak Masaüstü istemcileri](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) makalesine başvurun.

Şablon sanal makinesine bağlanmak için kullanılan bilgisayar türüne göre aşağıdaki adımları izleyin:

- Windows
  1. Laboratuvarın araç çubuğunda **şablona Bağlan** ' a tıklayın ve şablon VM 'sine bağlanmak için **RDP aracılığıyla Bağlan** ' ı seçin. 
  1. RDP dosyasını kaydedin ve uzak masaüstü istemcisi 'ni kullanarak şablon VM 'sine bağlanmak için kullanın. 
  1. Genellikle, uzak masaüstü istemcisi Windows üzerinde zaten yüklenmiş ve yapılandırılmıştır.  Sonuç olarak, tek yapmanız gereken, RDP dosyasına tıklayarak açmak ve uzak oturumu başlatmak için gereklidir.

- Mac
  1. Laboratuvarın araç çubuğunda **şablona Bağlan** ' a tıklayın ve RDP dosyasını kaydetmek için **RDP aracılığıyla Bağlan** ' ı seçin.  
  1. Ardından, [Mac ÜZERINDE RDP kullanarak BIR VM 'ye bağlanma](connect-virtual-machine-mac-remote-desktop.md)hakkında nasıl yapılır makalesine başvurun.

- Kmebook
  1. Laboratuvarın araç çubuğunda **şablona Bağlan** ' a tıklayın ve RDP dosyasını kaydetmek için **RDP aracılığıyla Bağlan** ' ı seçin.  
  1. Daha sonra, [bir Kmebook 'TA RDP kullanarak VM 'ye bağlanma](connect-virtual-machine-chromebook-remote-desktop.md)hakkında nasıl yapılır makalesine başvurun.

### <a name="x2go-client"></a>X2Go istemcisi

X2Go istemcisi, X2Go yapılandırılmış bir şablon sanal makinesine bağlanmak için kullanılır.  Şablon VM 'nin SSH bağlantı bilgilerini kullanarak [X2Go kullanarak BIR VM 'ye bağlanma](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go)hakkında nasıl yapılır makalesindeki adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar
Bir eğitmen, şablon sanal makinelerinde ve yayınladığında RDP veya X2Go ayarladıktan sonra, sanal makinelere GUI uzak masaüstü veya SSH aracılığıyla bağlanabilir.

Daha fazla bilgi için bkz.
 - [Linux VM'ye bağlanma](how-to-use-remote-desktop-linux-student.md)