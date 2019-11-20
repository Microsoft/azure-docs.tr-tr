---
title: Azure 'dan bir Windows VHD indirin
description: Azure portal kullanarak bir Windows VHD 'YI indirin.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: c5891d7ea2b53ab3524cfff267e71b4f05779cfc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033601"
---
# <a name="download-a-windows-vhd-from-azure"></a>Azure 'dan bir Windows VHD indirin

Bu makalede, Azure 'dan Azure portal kullanarak bir Windows sanal sabit disk (VHD) dosyası indirmeyi öğreneceksiniz.

## <a name="stop-the-vm"></a>VM’yi durdurma

Bir VHD, çalışan bir VM 'ye eklenmişse Azure 'dan indirilemiyor. Bir VHD 'YI indirmek için VM 'yi durdurmanız gerekir. Yeni disklerle diğer VM 'Ler oluşturmak için bir VHD 'YI [görüntü](tutorial-custom-images.md) olarak kullanmak istiyorsanız, dosyadaki işletim sistemini genelleştirmek Için [Sysprep 'i](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) kullanın ve ardından VM 'yi durdurun. VHD 'yi, var olan bir VM 'nin veya veri diskinin yeni bir örneği için disk olarak kullanmak üzere yalnızca VM 'yi durdurup serbest bırakma yeterlidir.

VHD 'YI başka VM 'Ler oluşturmak üzere bir görüntü olarak kullanmak için şu adımları izleyin:

1.  Önceden yapmadıysanız, [Azure portal](https://portal.azure.com/)da oturum açın
2.  [VM 'ye bağlanın](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  VM 'de yönetici olarak komut Istemi penceresini açın.
4.  Dizini *%windir%\system32\sysprep* olarak değiştirin ve Sysprep. exe ' yi çalıştırın.
5.  Sistem Hazırlama Aracı iletişim kutusunda, **sistem kutudan çıkar deneyimi (OOBE)** seçeneğini belirleyin ve **genelleştirilemedi** ' ın seçili olduğundan emin olun.
6.  Kapalı Seçenekler ' de, **kapatın**' i seçin ve ardından **Tamam**' a tıklayın. 

VHD 'YI, var olan bir VM veya veri diskinin yeni bir örneği için disk olarak kullanmak üzere aşağıdaki adımları izleyin:

1.  Azure portal hub menüsünde **sanal makineler**' e tıklayın.
2.  Listeden VM 'yi seçin.
3.  VM 'nin dikey penceresinde **Durdur**' a tıklayın.

    ![VM 'yi durdur](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL 'SI oluştur

VHD dosyasını indirmek için, [paylaşılan erişim imzası (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL 'si oluşturmanız gerekir. URL oluşturulduğunda, URL 'ye bir sona erme saati atanır.

1.  VM 'nin dikey penceresinin menüsünde **diskler**' e tıklayın.
2.  VM 'nin işletim sistemi diskini seçin ve ardından **disk dışarı aktar**' a tıklayın.
3.  URL 'nin süre sonu saatini *36000*olarak ayarlayın.
4.  **URL Oluştur**' a tıklayın.

    ![URL Oluştur](./media/download-vhd/export-generate-new.png)

> [!NOTE]
> Son kullanma süresi, Windows Server işletim sistemi için büyük VHD dosyasını indirmek üzere yeterli zaman sağlamak üzere varsayılan değer olarak artar. Windows Server işletim sistemini içeren bir VHD dosyasının, bağlantınıza bağlı olarak birkaç saat sürer. Bir veri diski için VHD 'yi İndiriyor olmanız durumunda varsayılan süre yeterlidir. 
> 
> 

## <a name="download-vhd"></a>VHD 'YI indir

1.  Oluşturulan URL altında, VHD dosyasını Indir ' e tıklayın.

    ![VHD 'YI indir](./media/download-vhd/export-download.png)

2.  İndirmeyi başlatmak için tarayıcıda **Kaydet** ' e tıklamanız gerekebilir. VHD dosyasının varsayılan adı *abcd*' dir.

    ![Tarayıcıda Kaydet ' e tıklayın](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Sonraki adımlar

- [BIR VHD dosyasını Azure 'a yüklemeyi](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)öğrenin. 
- [Bir depolama hesabındaki yönetilmeyen disklerden yönetilen diskler oluşturun](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Azure disklerini PowerShell Ile yönetin](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

