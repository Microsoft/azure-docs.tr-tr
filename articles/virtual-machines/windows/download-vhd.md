---
title: Azure 'dan bir Windows VHD indirin
description: Azure portal kullanarak bir Windows VHD 'YI indirin.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 32b9753b79273ce747d00cba077dd8a5ee6d724d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565296"
---
# <a name="download-a-windows-vhd-from-azure"></a>Azure 'dan bir Windows VHD indirin

Bu makalede, Azure 'dan Azure portal kullanarak bir Windows sanal sabit disk (VHD) dosyası indirmeyi öğreneceksiniz.

## <a name="optional-generalize-the-vm"></a>İsteğe bağlı: VM 'yi Genelleştirme

VHD 'YI başka VM 'Ler oluşturmak için [görüntü](tutorial-custom-images.md) olarak kullanmak istiyorsanız, işletim sistemini genelleştirmek Için [Sysprep 'i](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) kullanmanız gerekir. Aksi takdirde, oluşturmak istediğiniz her sanal makine için diski bir kopya yapmanız gerekir.

VHD 'yi başka VM 'Ler oluşturmak için görüntü olarak kullanmak için VM 'yi genelleştirin.

1. Önceden yapmadıysanız, [Azure portal](https://portal.azure.com/)da oturum açın
2. [VM 'ye bağlanın](connect-logon.md). 
3. VM 'de yönetici olarak komut Istemi penceresini açın.
4. Dizini *%windir%\system32\sysprep* olarak değiştirin ve sysprep.exe çalıştırın.
5. Sistem Hazırlama Aracı iletişim kutusunda, **sistem kutudan çıkar deneyimi (OOBE)** seçeneğini belirleyin ve **genelleştirilemedi** ' ın seçili olduğundan emin olun.
6. Kapalı Seçenekler ' de, **kapatın**' i seçin ve ardından **Tamam**' a tıklayın. 

Geçerli sanal makineyi genelleştirmek istemiyorsanız, önce [işletim sistemi diskinin anlık görüntüsünü yaparak](#alternative-snapshot-the-vm-disk), anlık görüntüden yenı bir VM oluşturarak ve sonra kopyayı genelleştirerek genelleştirilmiş bir görüntü oluşturabilirsiniz.

## <a name="stop-the-vm"></a>VM’yi durdurma

Bir VHD, çalışan bir VM 'ye eklenmişse Azure 'dan indirilemiyor. VM 'yi çalışır durumda tutmak istiyorsanız anlık [görüntü oluşturabilir ve ardından anlık görüntüyü indirebilirsiniz](#alternative-snapshot-the-vm-disk).

1. Azure portal hub menüsünde **sanal makineler**' e tıklayın.
1. Listeden VM 'yi seçin.
1. VM 'nin dikey penceresinde **Durdur**' a tıklayın.

### <a name="alternative-snapshot-the-vm-disk"></a>Alternatif: VM diskinin anlık görüntüsü

İndirmek için diskin anlık görüntüsünü alın.

1. [Portalda](https://portal.azure.com)VM 'yi seçin.
2. Sol taraftaki menüden **diskler** ' i seçin ve ardından anlık görüntü eklemek istediğiniz diski seçin. Diskin ayrıntıları görüntülenir.  
3. Sayfanın üst kısmındaki menüden **anlık görüntü oluştur** ' u seçin. **Anlık görüntü oluştur** sayfası açılır.
4. **Ad** alanına anlık görüntü için bir ad yazın. 
5. **Anlık görüntü türü** için **tam** veya **artımlı** seçeneğini belirleyin.
6. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin.

Anlık görüntü kısa bir süre içinde oluşturulacaktır ve daha sonra ' den başka bir VM 'yi indirmek veya oluşturmak için kullanılabilir.

> [!NOTE]
> Önce VM 'yi durdurmazsanız, anlık görüntü temizolmaz. Anlık görüntü, sanal makinenin güç olarak açılıp kilitlenmiş olması ile aynı durumda olacaktır.  Genellikle güvenli olsa da, bir süre çalışan çalışan uygulamalar kilitlenme dayanıklı değilse sorun oluşmasına neden olabilir.
>  
> Bu yöntem yalnızca tek bir işletim sistemi diski olan VM 'Ler için önerilir. Bir veya daha fazla veri diskine sahip VM 'Ler indirilmadan önce veya işletim sistemi diski ve her veri diski için bir anlık görüntü oluşturulmadan önce durdurulmalıdır.

## <a name="generate-download-url"></a>İndirme URL 'SI oluştur

VHD dosyasını indirmek için, [paylaşılan erişim imzası (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL 'si oluşturmanız gerekir. URL oluşturulduğunda, URL 'ye bir sona erme saati atanır.

1. VM 'nin sayfasında, sol taraftaki menüde **diskler** ' e tıklayın.
1. VM için işletim sistemi diskini seçin.
1. Disk sayfasında, sol menüden **disk dışarı aktar** ' ı seçin.
1. URL 'nin varsayılan sona erme saati *3600* saniyedir (bir saat). Windows işletim sistemi diskleri veya büyük veri diskleri için bunu artırmanız gerekebilir. **36000** saniye (10 saat) genellikle yeterlidir.
1. **URL Oluştur**' a tıklayın.

> [!NOTE]
> Son kullanma süresi, Windows Server işletim sistemi için büyük VHD dosyasını indirmek üzere yeterli zaman sağlamak üzere varsayılan değer olarak artar. Büyük VHD 'Lerin, bağlantınıza ve VM 'nin boyutuna bağlı olarak, indirmesi birkaç saate kadar sürebilir. 
> 
> 

## <a name="download-vhd"></a>VHD 'YI indir

1. Oluşturulan URL altında, VHD dosyasını Indir ' e tıklayın.
1. İndirmeyi başlatmak için tarayıcınızda **Kaydet** ' e tıklamanız gerekebilir. VHD dosyasının varsayılan adı *abcd*' dir.

## <a name="next-steps"></a>Sonraki adımlar

- [BIR VHD dosyasını Azure 'a yüklemeyi](upload-generalized-managed.md)öğrenin. 
- [Bir depolama hesabındaki yönetilmeyen disklerden yönetilen diskler oluşturun](attach-disk-ps.md).
- [Azure disklerini PowerShell Ile yönetin](tutorial-manage-data-disk.md).
