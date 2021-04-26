---
title: Azure 'dan bir Linux VHD indirin
description: Azure CLı ve Azure portal kullanarak bir Linux VHD 'YI indirin.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 8def06990b72d6e08127e8c4f16e0dfd87905d4f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565194"
---
# <a name="download-a-linux-vhd-from-azure"></a>Azure 'dan bir Linux VHD indirin

Bu makalede, Azure 'dan Azure portal kullanarak bir Linux sanal sabit disk (VHD) dosyası indirmeyi öğreneceksiniz. 

## <a name="stop-the-vm"></a>VM’yi durdurma

Bir VHD, çalışan bir VM 'ye eklenmişse Azure 'dan indirilemiyor. VM 'yi çalışır durumda tutmak istiyorsanız anlık [görüntü oluşturabilir ve ardından anlık görüntüyü indirebilirsiniz](#alternative-snapshot-the-vm-disk).

VM 'yi durdurmak için:

1.  [Azure portalında](https://portal.azure.com/) oturum açın.
2.  Sol taraftaki menüden **sanal makineler**' i seçin.
3.  Listeden VM 'yi seçin.
4.  VM 'nin sayfasında **Durdur**' u seçin.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="VM 'yi durdurmak için menü düğmesini gösterir.":::

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

## <a name="generate-sas-url"></a>SAS URL 'SI oluştur

VHD dosyasını indirmek için, [paylaşılan erişim imzası (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL 'si oluşturmanız gerekir. URL oluşturulduğunda, URL 'ye bir sona erme saati atanır.

1. VM 'nin sayfasının menüsünde **diskler**' i seçin.
2. VM 'nin işletim sistemi diskini seçin ve ardından **disk dışarı aktar**' ı seçin.
1. Gerekirse, indirme işleminin tamamlanabilmesi için yeterli zaman vermesi için URL 'nin değerini **(saniye) cinsinden** güncelleştirin. Varsayılan değer 3600 saniyedir (bir saat).
3. **URL Oluştur**' u seçin.
 
      
## <a name="download-vhd"></a>VHD 'YI indir

1.  Oluşturulan URL altında **VHD dosyasını indir**' i seçin.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="VHD 'YI indirmek için düğmeyi gösterir.":::

2.  İndirmeyi başlatmak için tarayıcıda **Kaydet** ' i seçmeniz gerekebilir. VHD dosyasının varsayılan adı *abcd*' dir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLI ile özel diskten bir LINUX VM 'yi karşıya yükleme ve oluşturma](upload-vhd.md)hakkında bilgi edinin. 
- Azure [disklerini Azure CLI Ile yönetin](tutorial-manage-disks.md).
