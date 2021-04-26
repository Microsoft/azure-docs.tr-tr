---
title: Windows için Azure N serisi AMD GPU sürücü kurulumu
description: Azure 'da Windows Server veya Windows çalıştıran N serisi VM 'Ler için AMD GPU sürücülerini ayarlama
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 62723a0fee6a3f696c517bc642fdac8cfa80a6b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557429"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Windows çalıştıran N serisi VM 'Lere AMD GPU sürücülerini yükler

Windows çalıştıran yeni Azure NVv4 serisi VM 'lerinin GPU Özellikleri avantajlarından yararlanmak için, AMD GPU sürücüleri yüklenmelidir. [AMD GPU sürücü uzantısı](../extensions/hpccompute-amd-gpu-windows.md) , NVv4 SERISI bir VM 'ye AMD GPU sürücülerini kurar. Azure portal veya Azure PowerShell veya Azure Resource Manager şablonları gibi araçları kullanarak uzantıyı yükler veya yönetir. Desteklenen işletim sistemleri ve dağıtım adımları için [AMD GPU sürücü uzantısı belgelerine](../extensions/hpccompute-amd-gpu-windows.md) bakın.

AMD GPU sürücülerini el ile yüklemeyi seçerseniz, bu makale desteklenen işletim sistemleri, sürücüler ve yükleme ve doğrulama adımları sağlar.

NVv4 VM 'lerinde yalnızca Microsoft tarafından yayımlanan GPU sürücüleri desteklenir. Lütfen GPU sürücülerini başka bir kaynaktan yüklemeyin.

Temel özellikler, depolama kapasiteleri ve disk ayrıntıları için bkz. [GPU WINDOWS VM boyutları](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Desteklenen işletim sistemleri ve sürücüler

| İşletim Sistemi | Sürücü |
| -------- |------------- |
| Windows 10 Enterprise çoklu oturum-derleme 1909 <br/><br/>Windows 10-derleme 1909<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. S4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exe) (. exe) |

 > [!NOTE]
   >  Build 1903/1909 kullanıyorsanız, en iyi performans için aşağıdaki Grup ilkesini güncelleştirmeniz gerekebilir. Bu değişiklikler diğer Windows derlemeleri için gerekli değildir.
   >  
   >  [Bilgisayar yapılandırma->Ilkeleri-Windows ayarlarını >->Yönetim Şablonları->Windows bileşenleri->Uzak Masaüstü Hizmetleri->Uzak Masaüstü Oturumu Ana Bilgisayarı->uzak oturum ortamı], Ilkeyi [Uzak Masaüstü bağlantıları için WDDM grafik görüntü sürücüsünü kullanın] devre dışı olarak ayarlayın.
   >  


## <a name="driver-installation"></a>Sürücü yükleme

1. Uzak Masaüstü ile her NVv4 serisi VM 'ye bağlanın.

2. Önceki sürücü sürümünü kaldırmanız gerekiyorsa, daha sonra AMD Temizleme yardımcı programını indirin lütfen sürücünün önceki sürümüyle birlikte gelen [yardımcı programını kullanmayın](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) .

3. En son sürücüyü indirip yükleyin.

4. VM 'yi yeniden başlatın.

## <a name="verify-driver-installation"></a>Sürücü yüklemeyi doğrulama

Device Manager ' de sürücü yüklemeyi doğrulayabilirsiniz. Aşağıdaki örnekte, bir Azure NVv4 VM üzerinde Radeon Instinct MI25 kartının başarıyla yapılandırılması gösterilmektedir.
<br />

![Azure NVv4 VM üzerinde Radeon Instinct MI25 kartının başarıyla yapılandırılmasını gösteren ekran görüntüsü.](./media/n-series-amd-driver-setup/device-manager.png)

Video RAM dahil GPU görüntüleme özelliklerini doğrulamak için dxdiag 'ı kullanabilirsiniz. Aşağıdaki örnekte, bir Azure NVv4 VM üzerinde Radeon Instinct MI25 kartının 1/2 bölümü gösterilmektedir.
<br />
![Azure NVv4 VM üzerinde Radeon Instinct MI25 kartının 1/2 bölümünü gösteren ekran görüntüsü.](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Windows 10 derleme 1903 veya sonraki bir sürümü çalıştırıyorsanız, dxdiag ' görüntü ' sekmesinde hiçbir bilgi göstermez. Lütfen en alttaki ' tüm bilgileri Kaydet ' seçeneğini kullanın ve çıkış dosyasında AMD MI25 GPU ile ilgili bilgiler gösterilir.

![GPU sürücüsü özellikleri](./media/n-series-amd-driver-setup/dxdiag-details.png)
