---
title: NP serisi-Azure sanal makineleri
description: NP serisi VM 'Ler için belirtim.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 61488b88b00206cb78beed4fe773bf9377848701
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861058"
---
# <a name="np-series"></a>NP serisi 
NP serisi sanal makineler, makine öğrenimi çıkarımı, video dönüştürme kodu ve veritabanı arama & analizi dahil olmak üzere iş yüklerini hızlandırma için [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) fpgile desteklenir. NP serisi VM 'Ler, 3,2 GHz 'nin tüm çekirdek Turbo saat hızına sahip Intel Xeon 8171M (ufuk Gölü) CPU 'Ları tarafından da desteklenir.

[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Dinamik geçiş](maintenance-and-updates.md): desteklenmiyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenmiyor<br>
VM oluşturma desteği: 1. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | FPGA | FPGA belleği: GiB | Maksimum veri diskleri | En fazla NIC/beklenen ağ bant genişliği (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S:** Hangi sürümü kullanmalıyım? 

Y **:** Xılinx, [VIS 2020,2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html) öneriyor


**S:** Çözümmi geliştirmek için NP VM 'Leri kullanmam gerekir mi? 

Y **:** Hayır, şirket içinde geliştirme yapabilir ve buluta dağıtabilirsiniz! Lütfen NP VM 'lerinde dağıtım yapmak için kanıtlama belgelerini izlediğinizden emin olun. 

**S:** Hangi XRT sürümünü kullanmalıyım?

Y **:** xrt_202020.2.8.832 

**S:** Hedef dağıtım platformu nedir?

Y **:** Aşağıdaki platformları kullanın.
- xılinx-U250-gen3x16-xdma-Platform-2.1-3_all
- Xilinx-U250-gen3x16-xdma-validate_2.1-3005608.1 

**S:** Geliştirme için hangi platformu hedefmalıyım?

**A:** Xilinx-U250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**S:** Desteklenen işletim sistemi (Işletim sistemleri) nelerdir? 

Y **:** Xilinx ve Microsoft, Ubuntu 18,04 LTS ve CentOS 7,8 öğesini doğruladı.

 Xilinx, bu VM 'lerin dağıtımını basitleştirmek için aşağıdaki Market görüntülerini oluşturdu. 

[Xılinx Alveo U250 dağıtım VM 'si – Ubuntu 18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xılinx Alveo U250 dağıtım VM 'si – CentOS 7,8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**S:** Kendi Ubuntu/CentOS sanal makinelerinizi dağıtabilir ve XRT/dağıtım hedefi platformunu yükleyebilir miyim? 

**Y:** Evet.

**S:** Kendi Ubuntu 18.04 sanal makinesini dağıttım, gerekli paketler ve adımlar nelerdir?

Y **:** [Xılinx XRT belgeleri](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) başına çekirdek 4.1 x kullanın
       
Aşağıdaki paketleri yükler.
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-XRT. Deb
       
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-Azure. Deb
       
- xılinx-U250-gen3x16-xdma-Platform-2.1-3_all_18.04. Deb. tar. gz
       
- Xilinx-U250-gen3x16-xdma-validate_2.1-3005608.1_all. Deb  

**S:** Ubuntu 'da, VM 'mi yeniden başlattıktan sonra FPGA 'yi bulamıyorum: 

Y **:** Lütfen çekirdeğin yükseltilmediğinden emin olun (uname-a). Bu durumda, lütfen çekirdek 4.1 X ' e düşürme yapın. 

**S:** Kendi CentOS 7,8 sanal makinesini dağıttım, gerekli paketler ve adımlar nelerdir?

Y **:** Çekirdek sürümünü kullan: 3.10.0-1160.15.2.el7.x86_64

 Aşağıdaki paketleri yükler.
   
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-XRT. rpm 
      
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-Azure. rpm 
     
 - Xilinx-U250-gen3x16-xdma-Platform-2.1 -3. noarch. rpm. tar. gz 
      
 - Xilinx-U250-gen3x16-xdma-Validate-2.1-3005608.1. noarch. rpm  

**S:** CentOS üzerinde xbutil Validate çalıştırılırken şu uyarıyı alıyorum: "Uyarı: çekirdek sürümü 3.10.0-1160.15.2.el7.x86_64 resmi olarak desteklenmez. 4.18.0-193 en son desteklenen sürümdür. " 

Y **:** Bu, güvenle yoksayılabilir. 

**S:** XRT ile ilgili olarak Onpred ve NP VM 'Ler arasındaki farklar nelerdir? 

Y **:** Azure 'da, XDMA 2,1 platformu yalnızca Host_Mem (SB) ve DDR veri saklama özelliklerini destekler. 

Host_Mem (SB) (1Gb RAM) etkinleştirmek için: sudo xbutil host_mem--Enable--sıze 1G 

Host_Mem (SB) devre dışı bırakmak için: sudo xbutil host_mem--Disable 

**S:** Xbmgmt komutlarını çalıştırabilir miyim? 

Y **:** Hayır, Azure VM 'lerde doğrudan Azure VM 'den yönetim desteği yoktur. 

 **S:** Bir PLP yükleme yapmam gerekir mi? 

Y **:** Hayır, PLP sizin için otomatik olarak yüklenir, bu nedenle xbmgmt komutları aracılığıyla yüklenmeye gerek yoktur. 

 
**S:** Azure farklı PLPs 'yi destekliyor mu? 

Y **:** Şu anda değil. Yalnızca dağıtım platformu paketlerinde sunulan PLP 'yi destekliyoruz. 

**S:** PLP bilgilerini nasıl sorgularım? 

Y **:** Xbutil sorgusunun çalıştırılması ve alt bölüme bakmamız gerekir. 

**S:** Kendi VM 'im oluşturup XRT 'yi el ile dağıtırsanız ne yapmam gerekir? 

Y **:** /Seçenek/Xilinx/XRT/Setup.exe ' de,/seçenek/Xilinx/XRT/xrt.ini işaret eden XRT_INI_PATH için bir giriş ekleyin

 
/Seçenek/Xilinx/XRT/xrt.ini içeriği şunları içermelidir: <br>
Çalışma zamanı<br>
ert = yanlış <br>

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
