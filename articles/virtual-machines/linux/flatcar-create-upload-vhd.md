---
title: Azure 'da kullanmak üzere bir Yataykapsayıcı Linux VHD 'SI oluşturma ve karşıya yükleme
description: Bir Yataykapsayıcı Linux işletim sistemi içeren bir VHD oluşturmayı ve yüklemeyi öğrenin.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 5d8be9493b7a312270301e3520f301f797fe2167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565300"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Azure için önceden oluşturulmuş bir Yatayaraç görüntüsü kullanma

Her bir Jcar kapsayıcı Linux 'un önceden oluşturulmuş Azure sanal sabit disk görüntülerini, her yatayda desteklenen kanalın her biri için indirebilirsiniz:

- [kararlı](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [Alfa](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [kenarını](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Bu görüntü zaten tam olarak ayarlandı ve Azure 'da çalışacak şekilde iyileştirildi. Yalnızca sıkıştırmasını açmanız gerekir.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Azure için kendi Yatayya dayalı sanal makineli oluşturma

Alternatif olarak, kendi Yataylı kapsayıcı Linux görüntünüzü oluşturmayı seçebilirsiniz.

Linux tabanlı herhangi bir makinede, [Yataylı kapsayıcı Linux geliştirici SDK 'sı Kılavuzu](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)' nda açıklanan yönergeleri izleyin. `image_to_vm.sh`Betiği çalıştırırken `--format=azure` bir Azure sanal sabit diski oluşturmak için geçirdiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

. Vhd dosyasına sahip olduktan sonra, Azure 'da yeni sanal makineler oluşturmak için elde edilen dosyayı kullanabilirsiniz. Azure 'a ilk kez bir. vhd dosyası yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).
