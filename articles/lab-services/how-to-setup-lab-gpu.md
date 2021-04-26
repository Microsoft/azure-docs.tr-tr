---
title: Azure Lab Services GPU 'Lar ile laboratuvar ayarlama | Microsoft Docs
description: Grafik işleme birimi (GPU) sanal makinelerle laboratuvar ayarlamayı öğrenin.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 8293ed1bfb53895b9631d9730fb75a2364457180
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452368"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>GPU sanal makinelerle laboratuvar kurma

Bu makalede aşağıdaki görevlerin nasıl yapılacağı gösterilmektedir:

- *Görselleştirme* ve *işlem* grafik Işleme birimleri (GPU 'lar) arasında seçim yapın.
- Uygun GPU sürücülerinin yüklü olduğundan emin olun.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Görselleştirme ve işlem GPU boyutları arasında seçim yapın
Laboratuvar oluşturma Sihirbazı 'nın ilk sayfasında, **hangi sanal makine boyutuna ihtiyacınız var?** açılan listede, sınıfınız Için gereken VM 'lerin boyutunu seçersiniz.  

![VM boyutu seçmek için "yeni laboratuvar" bölmesinin ekran görüntüsü](./media/how-to-setup-gpu/lab-gpu-selection.png)

Bu işlemde, **görselleştirme** ya da **işlem** GPU 'ları seçme seçeneğiniz vardır.  Öğrencilerinizin kullanacağı yazılıma göre GPU türünü seçmeniz önemlidir.  

Aşağıdaki tabloda açıklandığı gibi, bilgi *işlem* GPU boyutu yoğun işlem gücü uygulamalarına yöneliktir.  Örneğin, [doğal dil işleme sınıfı türünde derin öğrenme](./class-type-deep-learning-natural-language-processing.md) **küçük GPU (işlem)** boyutunu kullanır.  Öğrenciler, büyük veri kümeleriyle derin öğrenme modellerini eğmek üzere [veri bilimi sanal makinesi görüntüsü](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) tarafından sunulan derin öğrenme çerçeveleri ve araçları kullandığından, bu tür bir sınıf için işlem GPU 'su uygundur.

| Boyut | Çekirdekler | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Küçük GPU (Işlem) | -&nbsp;6 &nbsp; çekirdek<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NC6](../virtual-machines/nc-series.md) |Bu boyut, yapay zeka (AI) ve derin öğrenme gibi yoğun işlem gücü olan uygulamalar için idealdir. |

*Görselleştirme* GPU boyutları grafik yoğun uygulamalar için tasarlanmıştır.  Örneğin, [Solidçalışmamühendisliği sınıf türü](./class-type-solidworks.md) **küçük GPU (görselleştirme)** boyutunu kullanmayı gösterir.  Öğrenciler, düz nesnelerin modellenmesi ve görselleştirilmesi için SOLIDÇALıŞıYOR 3D bilgisayar destekli tasarım (CAD) ortamıyla etkileşimde bulunduğundan, bu tür bir sınıf için görselleştirme GPU 'SU uygundur.

| Boyut | Çekirdekler | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Küçük GPU (görselleştirme) | -&nbsp;6 &nbsp; çekirdek<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NV6](../virtual-machines/nv-series.md) | Bu boyut, OpenGL ve DirectX gibi çerçeveleri kullanan uzaktan görselleştirme, akış, oyun ve kodlama için idealdir. |
| Orta ölçekli GPU (görselleştirme) | -&nbsp;12 &nbsp; çekirdek<br>-&nbsp;112 &nbsp; GB &nbsp; RAM  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Bu boyut, OpenGL ve DirectX gibi çerçeveleri kullanan uzaktan görselleştirme, akış, oyun ve kodlama için idealdir. |

> [!NOTE]
> Bir sınıf Laboratuvarı oluştururken listede bu sanal makine boyutlarından bazılarını görmeyebilirsiniz. Liste, laboratuvarın konumunun geçerli kapasitesine göre doldurulur. Laboratuvar hesabı Oluşturucusu laboratuvar [oluşturucularının laboratuvar için bir konum seçmesine izin veriyorsa](allow-lab-creator-pick-lab-location.md), laboratuvar için farklı bir konum seçmeyi DENEYEBILIR ve VM boyutunun kullanılabilir olup olmadığını görebilirsiniz. VM 'lerin kullanılabilirliği için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/regions/services/?products=virtual-machines).

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Uygun GPU sürücülerinin yüklü olduğundan emin olun
Laboratuvar sanal makinelerinizin GPU yeteneklerini avantajlarından yararlanmak için uygun GPU sürücülerinin yüklü olduğundan emin olun.  Laboratuvar oluşturma sihirbazında, bir GPU VM boyutu seçtiğinizde **GPU sürücülerini yüklemeyi** seçin seçeneğini belirleyebilirsiniz.  

!["GPU sürücülerini yükler" seçeneğinin gösterildiği "yeni laboratuvar" ekran görüntüsü](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Önceki görüntüde gösterildiği gibi, bu seçenek varsayılan olarak etkindir ve seçtiğiniz GPU ve görüntü türü için *en son* sürücülerin yüklü olmasını sağlar.
- Bir *işlem* GPU boyutu seçtiğinizde, laboratuvar sanal makinelerinizin [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU 'su tarafından gücü vardır.  Bu durumda, yüksek performanslı bilgi işlem sağlayan en son [Işlem Birleşik cihaz mimarisi (CUDA)](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) sürücüleri yüklenir.
- *Görselleştirme* GPU boyutunu seçtiğinizde, laboratuvar sanal makineleriniz [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU ve [kılavuz teknolojisi](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf)tarafından desteklenir.  Bu durumda, en son KıLAVUZ sürücüleri yüklenir ve böylece grafik yoğun uygulamaların kullanımı sağlanır.

### <a name="install-the-drivers-manually"></a>Sürücüleri el ile yükler
En son sürümden farklı bir sürücü sürümü yüklemeniz gerekebilir.  Bu bölümde, bir *işlem* GPU 'su mi yoksa bir *görselleştirme* GPU mı kullandığınıza bağlı olarak uygun sürücülerin el ile nasıl yükleneceği gösterilmektedir.

#### <a name="install-the-compute-gpu-drivers"></a>İşlem GPU sürücülerini yükler

İşlem GPU boyutu için sürücüleri el ile yüklemek için aşağıdakileri yapın:

1. Laboratuvar oluşturma sihirbazında, [laboratuvarınızı oluştururken](./how-to-manage-classroom-labs.md) **GPU sürücülerini yükler** ayarını devre dışı bırakın.

1. Laboratuvarınız oluşturulduktan sonra, uygun sürücüleri yüklemek için şablon VM 'ye bağlanın.

   ![NVıDıA sürücü Indirmeleri sayfasının ekran görüntüsü](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. Bir tarayıcıda, [NVIDIA sürücü indirmeleri sayfasına](https://www.nvidia.com/Download/index.aspx)gidin.  
   b. **Ürün türünü** **Tesla** olarak ayarlayın.  
   c. **Ürün serisini** **K serisi** olarak ayarlayın.  
   d. Laboratuvarınızı oluştururken seçtiğiniz temel görüntünün türüne göre **Işletim sistemini** ayarlayın.  
   e. **CUDA araç takımını** , ihtiyacınız olan CUDA sürücüsünün sürümüne ayarlayın.  
   f. Sürücülerinizi aramak için **Ara** ' yı seçin.  
   örneğin: Yükleyiciyi indirmek için **İndir** ' i seçin.  
   h. Sürücüleri şablon VM 'de yüklü olacak şekilde yükleyiciyi çalıştırın.  
1. [Yüklü sürücüleri doğrulama](how-to-setup-lab-gpu.md#validate-the-installed-drivers) bölümünde yer alan yönergeleri izleyerek sürücülerin doğru şekilde yüklendiğini doğrulayın. 
1. Sınıfınız için gereken sürücüleri ve diğer yazılımları yükledikten sonra öğrencilerinizi sanal makinelerinizi oluşturmak için **Yayımla** ' yı seçin.

> [!NOTE]
> Linux görüntüsü kullanıyorsanız, yükleyiciyi indirdikten sonra [Linux üzerinde CUDA sürücülerini yükleme](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#install-cuda-drivers-on-n-series-vms)bölümündeki yönergeleri izleyerek sürücüleri yükleyebilirsiniz.

#### <a name="install-the-visualization-gpu-drivers"></a>Görselleştirme GPU sürücülerini yükler

Görselleştirme GPU boyutu için sürücüleri el ile yüklemek için aşağıdakileri yapın:

1. Laboratuvar oluşturma sihirbazında, [laboratuvarınızı oluştururken](./how-to-manage-classroom-labs.md) **GPU sürücülerini yükler** ayarını devre dışı bırakın.
1. Laboratuvarınız oluşturulduktan sonra, uygun sürücüleri yüklemek için şablon VM 'ye bağlanın.
1. İşletim sisteminiz için yönergeleri izleyerek, Microsoft tarafından şablon VM üzerinde sunulan KıLAVUZ sürücülerini yükler:
   -  [Windows NVıDıA KıLAVUZ sürücüleri](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)
   -  [Linux NVıDıA KıLAVUZ sürücüleri](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#nvidia-grid-drivers)
  
1. Şablon VM 'yi yeniden başlatın.
1. [Yüklü sürücüleri doğrulama](how-to-setup-lab-gpu.md#validate-the-installed-drivers) bölümünde yer alan yönergeleri izleyerek sürücülerin doğru şekilde yüklendiğini doğrulayın.
1. Sınıfınız için gereken sürücüleri ve diğer yazılımları yükledikten sonra öğrencilerinizi sanal makinelerinizi oluşturmak için **Yayımla** ' yı seçin.

### <a name="validate-the-installed-drivers"></a>Yüklü sürücüleri doğrulama
Bu bölümde, GPU sürücülerinizin düzgün şekilde yüklendiği nasıl doğrulanacağı açıklanmaktadır.

#### <a name="windows-images"></a>Windows görüntüleri
1.  [Windows çalıştıran N serisi VM 'LERE NVıDıA GPU sürücülerini yükleme](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation)konusunun "Sürücü yüklemesini doğrulama" bölümünde yer alan yönergeleri izleyin.
1.  Bir *görselleştirme* GPU kullanıyorsanız, şunları da yapabilirsiniz:
    - NVıDıA denetim masasında GPU ayarlarınızı görüntüleyin ve ayarlayın. Bunu yapmak için, **Windows Denetim Masası**'nda **donanım**' ı seçin ve ardından **NVIDIA Denetim Masası**' nı seçin.

      ![NVıDıA denetim masası bağlantısını gösteren Windows Denetim Masası 'nın ekran görüntüsü](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - **Görev Yöneticisi 'ni** kullanarak GPU performansınızı görüntüleyin.  Bunu yapmak için, **performans** sekmesini seçin ve ardından **GPU** seçeneğini belirleyin.

       ![Görev Yöneticisi GPU performansı sekmesini gösteren ekran görüntüsü](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > NVıDıA Denetim Masası ayarlarına yalnızca *görselleştirme* GPU 'ları için erişilebilir.  Bir işlem GPU 'SU için NVıDıA denetim masasını açmaya çalışırsanız, şu hatayı alırsınız: "NVıDıA görüntüleme ayarları kullanılamıyor.  Şu anda bir NVıDıA GPU 'ya bağlı olan bir görüntü kullanmamaktadır. "  Benzer şekilde, Görev Yöneticisi 'ndeki GPU performans bilgileri yalnızca görselleştirme GPU 'ları için sağlanır.

#### <a name="linux-images"></a>Linux görüntüleri
[Linux çalıştıran N serisi VM 'LERE NVıDıA GPU sürücülerini yükleme](../virtual-machines/linux/n-series-driver-setup.md#verify-driver-installation)konusunun "Sürücü yüklemesini doğrulama" bölümünde yer alan yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Laboratuvar oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [SOLIDÇALıŞMALAR bilgisayar destekli tasarım (CAD) sınıfı türü](class-type-solidworks.md)
- [MATLAB (matris Laboratuvarı) sınıf türü](class-type-matlab.md)