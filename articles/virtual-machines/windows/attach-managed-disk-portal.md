---
title: Bir Windows VM 'ye yönetilen veri diski iliştirme-Azure
description: Azure portalını kullanarak bir Windows VM'ye yönetilen bir veri diski ekleme yapma.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d98fdab6dcd0abb70967d1e17d138a860a31da5f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039313"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Azure portalını kullanarak bir Windows VM'ye yönetilen bir veri diski ekleme

Bu makalede, Azure portalını kullanarak bir Windows sanal makinesi (VM) için yeni bir yönetilen veri diski ekleme işlemini göstermektedir. VM boyutunu, iliştirebilirsiniz kaç veri diskinin belirler. Daha fazla bilgi için [sanal makine boyutları](sizes.md).


## <a name="add-a-data-disk"></a>Veri diski ekleme

1. İçinde [Azure portalında](https://portal.azure.com), sol taraftaki menüden **sanal makineler**.
2. Listeden bir sanal makine seçin.
3. Üzerinde **sanal makine** sayfasında **diskleri**.
4. Üzerinde **diskleri** sayfasında **Ekle veri diski**.
5. Açılan menü için yeni disk, seçin **Oluştur disk**.
6. İçinde **yönetilen disk oluşturma** sayfasında, disk için bir ad yazın ve diğer ayarları gerektiği gibi ayarlayın. İşiniz bittiğinde **Oluştur**’u seçin.
7. İçinde **diskleri** sayfasında **Kaydet** VM için yeni disk yapılandırmasını kaydetmek için.
8. Azure disk oluşturur ve sanal makineye iliştirir sonra yeni disk sanal makinenin disk ayarları altında listelenir **veri diskleri**.


## <a name="initialize-a-new-data-disk"></a>Yeni bir veri diski başlatın

1. VM’ye bağlanın.
1. Windows seçin **Başlat** çalışan VM içindeki menü girin **diskmgmt.msc** arama kutusuna. **Disk Yönetimi** konsolu açılır.
2. Disk Yönetimi tanıdığı yeni, başlatılmamış bir disk olması ve **diski başlatma** penceresi görüntülenir.
3. Yeni disk, seçilen ve ardından doğrulamak **Tamam** başlatmak üzere.
4. Yeni disk olarak görünür **ayrılmamış**. Herhangi bir yere sağ tıklatın ve disk **yeni basit birim**. **Yeni Basit Birim Sihirbazı** penceresi açılır.
5. Tüm Varsayılanları, tutma sihirbazdaki adımları izleyin ve select bitirdiğinizde **son**.
6. Kapat **Disk Yönetimi**.
7. Kullanabilmeniz için önce yeni diski biçimlendirmek için gereken bildiren bir açılır pencere görüntülenir. Seçin **diski Biçimlendir**.
8. İçinde **yeni diski Biçimlendir** penceresinde ayarlarını kontrol edin ve ardından **Başlat**.
9. Diskleri biçimlendirme tüm verileri siler olduğunu bildiren bir uyarı görüntülenir. **Tamam**’ı seçin.
10. Biçimlendirme tamamlandıktan sonra seçin **Tamam**.

## <a name="next-steps"></a>Sonraki adımlar

- Ayrıca [PowerShell kullanarak veri diski ekleme](attach-disk-ps.md).
- Uygulamanızın kullanması gerekirse *D:* verileri depolamak için sürücü, yapabilecekleriniz [Windows geçici diskinin sürücü harfini değiştirme](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
