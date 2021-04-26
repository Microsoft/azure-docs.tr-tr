---
title: Windows sanal masaüstü (klasik) için GPU 'YU Yapılandırma-Azure
description: Windows sanal masaüstünde (klasik) GPU hızlandırmalı işleme ve kodlamayı etkinleştirme.
author: gundarev
ms.topic: how-to
ms.date: 03/30/2020
ms.author: denisgun
ms.openlocfilehash: fa7b80b021e00d25dea4f96432ae922c15474058
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023063"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-classic"></a>Windows sanal masaüstü için grafik işleme birimi (GPU) hızlandırmasını yapılandırma (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../configure-vm-gpu.md)bakın.

Windows sanal masaüstü, geliştirilmiş uygulama performansı ve ölçeklenebilirliği için GPU hızlandırmalı işleme ve kodlamayı destekler. GPU hızlandırma özellikle grafik yoğun uygulamalar için önemlidir.

GPU ile iyileştirilmiş bir Azure sanal makinesi oluşturmak, konak havuzunuza eklemek ve işleme ve kodlama için GPU hızlandırmasını kullanacak şekilde yapılandırmak için bu makaledeki yönergeleri izleyin. Bu makalede, zaten yapılandırılmış bir Windows sanal masaüstü kiracınız olduğu varsayılır.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>GPU için iyileştirilmiş bir Azure sanal makine boyutu seçin

Azure, çok sayıda [GPU iyileştirilmiş sanal makine boyutu](../../virtual-machines/sizes-gpu.md)sunar. Konak havuzunuzun sağ seçimi, belirli uygulama iş yükleriniz, istenen kullanıcı deneyimi kalitesi ve maliyet dahil olmak üzere çeşitli etkenlere bağlıdır. Genel olarak, daha büyük ve daha yetenekli GPU 'Lar belirli bir kullanıcı yoğunluğu konusunda daha iyi bir kullanıcı deneyimi sunar.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Bir konak havuzu oluşturun, sanal makinenizi sağlayın ve bir uygulama grubu yapılandırın

Seçtiğiniz boyuttaki bir VM 'yi kullanarak yeni bir konak havuzu oluşturun. Yönergeler için bkz. [öğretici: Azure Marketi ile konak havuzu oluşturma](../create-host-pools-azure-marketplace.md).

Windows sanal masaüstü, aşağıdaki işletim sistemlerinde GPU hızlandırmalı işleme ve kodlamayı destekler:

* Windows 10 sürüm 1511 veya daha yenisi
* Windows Server 2016 veya daha yenisi

Ayrıca, yeni bir konak havuzu oluştururken otomatik olarak oluşturulan varsayılan masaüstü uygulama grubunu ("Masaüstü uygulama grubu" adlı) kullanmanız gerekir. Yönergeler için bkz. [öğretici: Windows sanal masaüstü için uygulama gruplarını yönetme](../manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Sanal makinenize desteklenen grafik sürücülerini yükler

Windows sanal masaüstündeki Azure N serisi VM 'lerin GPU yetilerinden yararlanmak için uygun grafik sürücülerini yüklemelisiniz. Uygun grafik satıcısından sürücüleri el ile veya bir Azure VM uzantısı kullanarak yüklemek için [desteklenen işletim sistemleri ve sürücüler](../../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) bölümündeki yönergeleri izleyin.

Windows sanal masaüstü için yalnızca Azure tarafından dağıtılan sürücüler desteklenir. Additionaly, NVıDıA GPU 'lara sahip Azure VM 'Leri için yalnızca [NVıDıA kılavuz sürücüleri](../../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers) Windows sanal masaüstü için desteklenir.

Sürücü yüklemesinden sonra, bir VM yeniden başlatması gerekir. Grafik sürücülerinin başarıyla yüklendiğini doğrulamak için yukarıdaki yönergelerdeki doğrulama adımlarını kullanın.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU hızlandırmalı uygulama işlemeyi yapılandırma

Varsayılan olarak, çoklu oturum yapılandırmalarında çalışan uygulamalar ve masaüstleri CPU ile işlenir ve işleme için kullanılabilir GPU 'ları kullanmaz. GPU hızlandırmalı işlemeyi etkinleştirmek için oturum ana bilgisayarı için grup ilkesi yapılandırın:

1. Yerel yönetici ayrıcalıklarına sahip bir hesap kullanarak VM 'nin masaüstüne bağlanın.
2. Başlat menüsünü açın ve grup ilkesi düzenleyicisini açmak için "gpedit. msc" yazın.
3.   >  **Yönetim Şablonları**,  >    >    >    >  **uzak oturum ortamı** Uzak Masaüstü oturumu ana bilgisayarı Windows bileşenleri Uzak Masaüstü Hizmetleri Bilgisayar Yapılandırması ' na gidin.
4. İlke ' yi seçin **tüm Uzak Masaüstü Hizmetleri oturumları için donanım varsayılan grafik bağdaştırıcısını kullanın** ve bu ilkeyi, uzak oturumda GPU oluşturmayı etkinleştirmek için **etkin** olarak ayarlayın.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU hızlandırmalı çerçeve kodlamasını yapılandırma

Uzak Masaüstü, uzak masaüstü istemcilerine iletilmek üzere uygulamalar ve masaüstü bilgisayarlar tarafından oluşturulan tüm grafikleri (GPU ile veya CPU ile işlenmeksizin) kodlar. Varsayılan olarak, uzak masaüstü bu kodlama için kullanılabilir GPU 'lara ait değildir. GPU hızlandırmalı çerçeve kodlamasını etkinleştirmek için oturum ana bilgisayarı için grup ilkesi yapılandırın. Yukarıdaki adımlara devam edin:

1. **Uzak Masaüstü bağlantıları için Ilke öncelik atama H.,/avc 444 grafik modu** ' nu seçin ve bu ilkeyi uzak oturumda h., ve AVC 444 codec bileşenini zorlamak için **etkin** olarak ayarlayın.
2. **Uzak Masaüstü bağlantıları için, Ilke yapılandırma H. ıfer/AVC donanım kodlamasını** seçin ve bu ilkeyi uzak oturumda AVC/H. IBU için donanım kodlamayı etkinleştirmek üzere **etkin** olarak ayarlayın.

    >[!NOTE]
    >Windows Server 2016 ' de, **her zaman denemek** Için **AVC donanım kodlamasını tercih et** seçeneğini belirleyin.

3. Artık grup ilkeleri düzenlenmişse, bir grup ilkesi güncelleştirmesini zorlayın. Komut Istemi ' ni açın ve şunu yazın:

    ```batch
    gpupdate.exe /force
    ```

4. Uzak Masaüstü oturumunda oturumu kapatın.

## <a name="verify-gpu-accelerated-app-rendering"></a>GPU hızlandırmalı uygulama işlemeyi doğrulama

Uygulamaların işleme için GPU 'YU kullandığını doğrulamak için aşağıdakilerden birini deneyin:

* NVıDıA GPU ile Azure VM 'Leri için, `nvidia-smi` uygulamalarınızı ÇALıŞTıRıRKEN GPU kullanımını denetlemek üzere [Sürücü yüklemesini doğrulama](../../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) bölümünde açıklandığı gibi yardımcı programını kullanın.
* Desteklenen işletim sistemi sürümlerinde, Görev Yöneticisi 'Ni kullanarak GPU kullanımını kontrol edebilirsiniz. Uygulamaların GPU 'YU kullanıp kullanmadığını görmek için "performans" sekmesindeki GPU 'YU seçin.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU hızlandırmalı çerçeve kodlamasını doğrulama

Uzak Masaüstü 'Nün GPU hızlandırmalı kodlama kullandığını doğrulamak için:

1. Windows sanal masaüstü istemcisi 'ni kullanarak VM 'nin masaüstüne bağlanın.
2. Olay Görüntüleyicisi başlatın ve şu düğüme gidin: **uygulamalar ve hizmetler günlükleri**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-rdpcorecdv**  >  **operasyonel**
3. GPU hızlandırmalı kodlamanın kullanıldığını anlamak için, olay KIMLIĞI 170 ' i arayın. "AVC donanım Kodlayıcısı etkin: 1" görürseniz, GPU kodlaması kullanılır.
4. AVC 444 modunun kullanıldığını anlamak için, olay KIMLIĞI 162 ' yi arayın. "AVC kullanılabilir: 1 başlangıç profili: 2048" görürseniz, AVC 444 kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu yönergelerin tek bir oturum konağında (bir VM) GPU hızlandırmalı ve üzerinde çalışıyor olması gerekir. Daha büyük bir konak havuzu genelinde GPU hızlandırmayı etkinleştirmeye yönelik bazı ek noktalar:

* Bir dizi sanal makinede sürücü yüklemeyi ve güncelleştirmeleri basitleştirmek için bir [VM Uzantısı](../../virtual-machines/extensions/overview.md) kullanmayı düşünün. NVıDıA GPU 'Lar içeren VM 'Ler için [NVıDıA GPU sürücü uzantısını](../../virtual-machines/extensions/hpccompute-gpu-windows.md) kullanın ve AMD GPU 'Lar Ile VM 'ler IÇIN AMD GPU sürücü uzantısını kullanın.
* Bir dizi sanal makine genelinde Grup ilkesi yapılandırmasını basitleştirmek için Active Directory grup ilkesi kullanmayı düşünün. Active Directory etki alanında grup ilkesi dağıtma hakkında daha fazla bilgi için bkz. [Grup İlkesi nesneleriyle çalışma](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)).