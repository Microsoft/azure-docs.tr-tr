---
title: Windows sanal masaüstü için GPU 'YU Yapılandırma-Azure
description: Windows sanal masaüstü 'nde GPU hızlandırmalı işleme ve kodlamayı etkinleştirme.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: f95b9c1615cc58d9cc0589bad98c7315e571686e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709472"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Windows Sanal Masaüstü için grafik işlem birimi (GPU) hızlandırmasını yapılandırma

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md)bakın.

Windows sanal masaüstü, geliştirilmiş uygulama performansı ve ölçeklenebilirliği için GPU hızlandırmalı işleme ve kodlamayı destekler. GPU hızlandırma özellikle grafik yoğun uygulamalar için önemlidir.

GPU ile iyileştirilmiş bir Azure sanal makinesi oluşturmak, konak havuzunuza eklemek ve işleme ve kodlama için GPU hızlandırmasını kullanacak şekilde yapılandırmak için bu makaledeki yönergeleri izleyin. Bu makalede, zaten yapılandırılmış bir Windows sanal masaüstü kiracınız olduğu varsayılır.

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>Uygun bir GPU iyileştirilmiş Azure sanal makine boyutu seçin

Azure 'un [NV serisi](../virtual-machines/nv-series.md), [NVv3-Series](../virtual-machines/nvv3-series.md)veya [NVv4 serisi](../virtual-machines/nvv4-series.md) VM boyutlarından birini seçin. Bunlar, uygulama ve masaüstü sanallaştırma için tasarlanmıştır ve çoğu uygulama ve Windows Kullanıcı arabiriminin GPU hızlandırmalı olmasını sağlar. Konak havuzunuzun sağ seçimi, belirli uygulama iş yükleriniz, istenen kullanıcı deneyimi kalitesi ve maliyet dahil olmak üzere çeşitli etkenlere bağlıdır. Genel olarak, daha büyük ve daha yetenekli GPU 'Lar belirli bir kullanıcı yoğunluğu üzerinde daha iyi bir kullanıcı deneyimi sunar. daha küçük ve kısmi GPU boyutları, maliyet ve kalite üzerinde daha ayrıntılı denetim sağlar.

>[!NOTE]
>Azure 'un NC, NCv2, NCv3, ND ve NDv2 serisi VM 'Leri, Windows sanal masaüstü oturumu konakları için genellikle uygun değildir. Bu VM 'Ler, NVıDıA CUDA ile oluşturulan özel, yüksek performanslı bilgi işlem veya makine öğrenimi araçları için tasarlanmıştır. Çoğu uygulama veya Windows Kullanıcı arabirimi için GPU hızlandırmasını desteklemez.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Bir konak havuzu oluşturun, sanal makinenizi sağlayın ve bir uygulama grubu yapılandırın

Seçtiğiniz boyuttaki bir VM 'yi kullanarak yeni bir konak havuzu oluşturun. Yönergeler için bkz. [öğretici: Azure Portal bir konak havuzu oluşturma](./create-host-pools-azure-marketplace.md).

Windows sanal masaüstü, aşağıdaki işletim sistemlerinde GPU hızlandırmalı işleme ve kodlamayı destekler:

* Windows 10 sürüm 1511 veya daha yenisi
* Windows Server 2016 veya daha yenisi

Ayrıca, yeni bir konak havuzu oluştururken otomatik olarak oluşturulan varsayılan masaüstü uygulama grubunu ("Masaüstü uygulama grubu" adlı) kullanmanız gerekir. Yönergeler için bkz. [öğretici: Windows sanal masaüstü için uygulama gruplarını yönetme](./manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Sanal makinenize desteklenen grafik sürücülerini yükler

Windows sanal masaüstündeki Azure N serisi VM 'lerin GPU yetilerinden yararlanmak için uygun grafik sürücülerini yüklemelisiniz. Sürücüleri yüklemek için [desteklenen işletim sistemleri ve sürücüler](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) bölümündeki yönergeleri izleyin. Yalnızca Azure tarafından dağıtılan sürücüler desteklenir.

* Azure NV-Series veya NVv3 serisi VM 'Ler için, NVıDıA CUDA sürücüleri değil yalnızca NVıDıA KıLAVUZ sürücüleri, çoğu uygulama ve Windows Kullanıcı arabirimi için GPU hızlandırmasını destekler. Sürücüleri el ile yüklemeyi seçerseniz, KıLAVUZ sürücülerini yüklediğinizden emin olun. Azure VM uzantısını kullanarak sürücüleri yüklemeyi seçerseniz, bu VM boyutları için KıLAVUZ sürücüleri otomatik olarak yüklenir.
* Azure NVv4 serisi VM 'Ler için, Azure tarafından sunulan AMD sürücülerini yükler. Azure VM uzantısını kullanarak bunları otomatik olarak yükleyebilirsiniz veya el ile yükleyebilirsiniz.

Sürücü yüklemesinden sonra, bir VM yeniden başlatması gerekir. Grafik sürücülerinin başarıyla yüklendiğini doğrulamak için yukarıdaki yönergelerdeki doğrulama adımlarını kullanın.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU hızlandırmalı uygulama işlemeyi yapılandırma

Varsayılan olarak, çoklu oturum yapılandırmalarında çalışan uygulamalar ve masaüstleri CPU ile işlenir ve işleme için kullanılabilir GPU 'ları kullanmaz. GPU hızlandırmalı işlemeyi etkinleştirmek için oturum ana bilgisayarı için grup ilkesi yapılandırın:

1. Yerel yönetici ayrıcalıklarına sahip bir hesap kullanarak VM 'nin masaüstüne bağlanın.
2. Başlat menüsünü açın ve grup ilkesi düzenleyicisini açmak için "gpedit. msc" yazın.
3.   >  **Yönetim Şablonları**,  >    >    >    >  **uzak oturum ortamı** Uzak Masaüstü oturumu ana bilgisayarı Windows bileşenleri Uzak Masaüstü Hizmetleri Bilgisayar Yapılandırması ' na gidin.
4. İlke ' yi seçin **tüm Uzak Masaüstü Hizmetleri oturumları için donanım grafik bağdaştırıcılarını kullanın** ve bu ilkeyi, uzak oturumda GPU oluşturmayı etkinleştirmek için **etkin** olarak ayarlayın.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU hızlandırmalı çerçeve kodlamasını yapılandırma

Uzak Masaüstü, uzak masaüstü istemcilerine iletilmek üzere uygulamalar ve masaüstü bilgisayarlar tarafından oluşturulan tüm grafikleri (GPU ile veya CPU ile işlenmeksizin) kodlar. Ekranın bir kısmı sıklıkla güncelleniyorsa, ekranın bu bölümü bir video codec bileşeniyle (H. bir/AVC) kodlanır. Varsayılan olarak, uzak masaüstü bu kodlama için kullanılabilir GPU 'lara ait değildir. GPU hızlandırmalı çerçeve kodlamasını etkinleştirmek için oturum ana bilgisayarı için grup ilkesi yapılandırın. Yukarıdaki adımlara devam edin:

>[!NOTE]
>GPU hızlandırmalı çerçeve kodlaması NVv4 serisi VM 'lerde kullanılamaz.

1. **Uzak Masaüstü bağlantıları için, Ilke yapılandırma H. ıfer/AVC donanım kodlamasını** seçin ve bu ilkeyi uzak oturumda AVC/H. IBU için donanım kodlamayı etkinleştirmek üzere **etkin** olarak ayarlayın.

    >[!NOTE]
    >Windows Server 2016 ' de, **her zaman denemek** Için **AVC donanım kodlamasını tercih et** seçeneğini belirleyin.

2. Artık grup ilkeleri düzenlenmişse, bir grup ilkesi güncelleştirmesini zorlayın. Komut Istemi ' ni açın ve şunu yazın:

    ```cmd
    gpupdate.exe /force
    ```

3. Uzak Masaüstü oturumunda oturumu kapatın.

## <a name="configure-fullscreen-video-encoding"></a>Tam ekran video kodlamasını yapılandırma

Genellikle 3B modelleme, CAD/CAM ve video uygulamaları gibi yüksek kare hızlı bir içerik üreten uygulamalar kullanıyorsanız, uzak bir oturum için tam ekran olarak bir video kodlaması etkinleştirmeyi seçebilirsiniz. Tam ekran olarak video profili, ağ bant genişliği ve hem oturum ana bilgisayarı hem de istemci kaynakları için daha yüksek bir kare hızı ve daha iyi kullanıcı deneyimi sağlar. Tam ekran video kodlaması için GPU hızlandırmalı çerçeve kodlamasının kullanılması önerilir. Tam ekran video kodlamasını etkinleştirmek için oturum ana bilgisayarı için grup ilkesi yapılandırın. Yukarıdaki adımlara devam edin:

1. **Uzak Masaüstü bağlantıları için Ilke öncelik atama H.,/avc 444 grafik modu** ' nu seçin ve bu ilkeyi uzak oturumda h., ve AVC 444 codec bileşenini zorlamak için **etkin** olarak ayarlayın.
2. Artık grup ilkeleri düzenlenmişse, bir grup ilkesi güncelleştirmesini zorlayın. Komut Istemi ' ni açın ve şunu yazın:

    ```cmd
    gpupdate.exe /force
    ```

3. Uzak Masaüstü oturumunda oturumu kapatın.
## <a name="verify-gpu-accelerated-app-rendering"></a>GPU hızlandırmalı uygulama işlemeyi doğrulama

Uygulamaların işleme için GPU 'YU kullandığını doğrulamak için aşağıdakilerden birini deneyin:

* NVıDıA GPU ile Azure VM 'Leri için, `nvidia-smi` uygulamalarınızı ÇALıŞTıRıRKEN GPU kullanımını denetlemek üzere [Sürücü yüklemesini doğrulama](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) bölümünde açıklandığı gibi yardımcı programını kullanın.
* Desteklenen işletim sistemi sürümlerinde, Görev Yöneticisi 'Ni kullanarak GPU kullanımını kontrol edebilirsiniz. Uygulamaların GPU 'YU kullanıp kullanmadığını görmek için "performans" sekmesindeki GPU 'YU seçin.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU hızlandırmalı çerçeve kodlamasını doğrulama

Uzak Masaüstü 'Nün GPU hızlandırmalı kodlama kullandığını doğrulamak için:

1. Windows sanal masaüstü istemcisi 'ni kullanarak VM 'nin masaüstüne bağlanın.
2. Olay Görüntüleyicisi başlatın ve şu düğüme gidin: **uygulamalar ve hizmetler günlükleri**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-rdpcorecdv**  >  **operasyonel**
3. GPU hızlandırmalı kodlamanın kullanıldığını anlamak için, olay KIMLIĞI 170 ' i arayın. "AVC donanım Kodlayıcısı etkin: 1" görürseniz, GPU kodlaması kullanılır.

## <a name="verify-fullscreen-video-encoding"></a>Tam ekran video kodlamasının doğrulanması

Uzak Masaüstü 'Nün tam ekran ekran kodlaması kullandığını doğrulamak için:

1. Windows sanal masaüstü istemcisi 'ni kullanarak VM 'nin masaüstüne bağlanın.
2. Olay Görüntüleyicisi başlatın ve şu düğüme gidin: **uygulamalar ve hizmetler günlükleri**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-rdpcorecdv**  >  **operasyonel**
3. Tam ekran video kodlamasının kullanıldığını anlamak için, olay KIMLIĞI 162 ' i arayın. "AVC kullanılabilir: 1 başlangıç profili: 2048" görürseniz, AVC 444 kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu yönergelerin tek bir oturum konağında (bir VM) GPU hızlandırmalı ve üzerinde çalışıyor olması gerekir. Daha büyük bir konak havuzu genelinde GPU hızlandırmayı etkinleştirmeye yönelik bazı ek noktalar:

* Bir dizi sanal makinede sürücü yüklemeyi ve güncelleştirmeleri basitleştirmek için bir [VM Uzantısı](../virtual-machines/extensions/overview.md) kullanmayı düşünün. NVıDıA GPU 'Lar içeren VM 'Ler için [NVıDıA GPU sürücü uzantısını](../virtual-machines/extensions/hpccompute-gpu-windows.md) kullanın ve AMD GPU 'Lar Ile VM 'Ler IÇIN [AMD GPU sürücü uzantısını](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md) kullanın.
* Bir dizi sanal makine genelinde Grup ilkesi yapılandırmasını basitleştirmek için Active Directory grup ilkesi kullanmayı düşünün. Active Directory etki alanında grup ilkesi dağıtma hakkında daha fazla bilgi için bkz. [Grup İlkesi nesneleriyle çalışma](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)).