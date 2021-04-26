---
title: Bir Windows VM 'ye yönetilen veri diski iliştirme-Azure
description: Azure portal kullanarak bir Windows VM 'ye yönetilen veri diski iliştirme.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8c64b0ff5b7a9abfa58ec17d0ebcabe05b0ed6e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550816"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Azure portal kullanarak bir Windows VM 'sine yönetilen veri diski iliştirme

Bu makalede, Azure portal kullanarak yeni bir yönetilen veri diskini bir Windows sanal makinesine (VM) nasıl ekleyebileceğiniz gösterilmektedir. Sanal makinenin boyutu, ekleyebileceğiniz veri disklerinin sayısını belirler. Daha fazla bilgi için bkz. [sanal makineler Için boyutlar](../sizes.md).


## <a name="add-a-data-disk"></a>Veri diski ekleme

1. Veri diski eklemek için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.
2. Listeden bir sanal makine seçin.
3. **Sanal makine** sayfasında, **diskler**' i seçin.
4. **Diskler** sayfasında, **veri diski Ekle**' yi seçin.
5. Yeni diskin açılan penceresinde **disk oluştur**' u seçin.
6. **Yönetilen disk oluştur** sayfasında, disk için bir ad yazın ve diğer ayarları gerektiği gibi ayarlayın. İşiniz bittiğinde **Oluştur**'u seçin.
7. **Diskler** sayfasında, sanal makine için yeni disk yapılandırmasını kaydetmek üzere **Kaydet** ' i seçin.
8. Azure diski oluşturduktan ve sanal makineye iliştirdikten sonra, yeni disk, **veri diskleri** altındaki sanal makinenin disk ayarlarında listelenir.


## <a name="initialize-a-new-data-disk"></a>Yeni bir veri diski Başlat

1. VM’ye bağlanın.
1. Çalışan VM 'nin içindeki Windows **Başlat** menüsünü seçin ve arama kutusuna **diskmgmt. msc** girin. **Disk yönetimi** konsolu açılır.
2. Disk Yönetimi, yeni, başlatılmamış bir diskiniz olduğunu ve **Diski Başlat** penceresinin göründüğünü algılar.
3. Yeni diskin seçili olduğunu doğrulayın ve ardından başlatmak için **Tamam** ' ı seçin.
4. Yeni disk **ayrılmamış** olarak görünür. Diskte herhangi bir yere sağ tıklayın ve **Yeni basit birim**' i seçin. **Yeni Basit Birim Sihirbazı** penceresi açılır.
5. Sihirbazda ilerleyin, tüm varsayılanları tutarak ve işiniz bittiğinde **son**' u seçin.
6. **Disk yönetimi**'ni kapatın.
7. Bunu kullanabilmeniz için yeni diski biçimlendirmeniz gerektiğini bildiren bir açılır pencere görüntülenir. **Diski Biçimlendir**' i seçin.
8. **Yeni diski Biçimlendir** penceresinde ayarları kontrol edin ve ardından **Başlat**' ı seçin.
9. Disklerin formatlamanın tüm verileri sildiği konusunda sizi bildiren bir uyarı görüntülenir. **Tamam**’ı seçin.
10. Biçimlendirme tamamlandığında **Tamam**' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Ayrıca, [PowerShell kullanarak bir veri diski](attach-disk-ps.md)ekleyebilirsiniz.
- Uygulamanızın verileri depolamak için *D:* sürücüsünü kullanması gerekiyorsa, [Windows geçici diskinin sürücü harfini değiştirebilirsiniz](change-drive-letter.md).