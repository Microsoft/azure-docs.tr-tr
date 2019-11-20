---
title: Azure portal ile bölge Windows VM oluşturma
description: Azure portal ile bir kullanılabilirlik alanında Windows VM oluşturma
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: c6f843693ccfa16f9b31027ba370242e0462b138
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033886"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Azure portal ile bir kullanılabilirlik alanında Windows sanal makinesi oluşturma

Bu makalede, Azure kullanılabilirlik bölgesinde bir sanal makine oluşturmak için Azure portal kullanma adımları sağlanır. [Kullanılabilirlik alanı](../../availability-zones/az-overview.md), bir Azure bölgesinde fiziksel olarak ayrılmış bir alandır. Uygulamalarınızı beklenmeyen hatalardan veya tüm veri merkezinin kaybedilmesinden korumak için kullanılabilirlik alanlarından yararlanın.

Kullanılabilirlik alanı kullanmak için, [desteklenen bir Azure bölgesinde](../../availability-zones/az-overview.md#services-support-by-region) sanal makinenizi oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma 

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesine tıklayın.

2. **İşlem**'i seçin ve sonra da **Windows Server 2016 Datacenter**'ı seçin. 

3. Sanal makine bilgilerini girin. Burada girilen kullanıcı adı ve parola, sanal makinede oturum açarken kullanılır. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](faq.md#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır. Kullanılabilirlik alanlarını destekleyen Doğu ABD 2 gibi bir konum seçin. İşlem tamamlandığında **Tamam**’a tıklayın.

    ![Portal dikey penceresinde VM’niz ile ilgili temel bilgileri girin](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. VM için bir boyut seçin. Önerilen bir boyut seçin veya özelliklere göre filtre uygulayın. Boyut, kullanmak istediğiniz bölgede kullanılabilir olduğunu onaylayın.

    ![VM boyutu seçin](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. **Ayarlar** > **yüksek kullanılabilirlik**altında, **kullanılabilirlik bölgesi** açılan listesinden numaralandırılmış bölgelerden birini seçin, kalan varsayılan ayarları tutun ve **Tamam**' a tıklayın.

    ![Bir kullanılabilirlik bölgesi seçin](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Özet sayfasında, sanal makine dağıtımını başlatmak için **Oluştur** ' a tıklayın.

7. VM, Azure portalı panosuna sabitlenir. Dağıtım tamamlandıktan sonra VM özeti otomatik olarak açılır.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Yönetilen disk ve IP adresi için bölgeyi Onayla

VM bir kullanılabilirlik alanında dağıtıldığında, sanal makine için yönetilen bir disk aynı Kullanılabilirlik bölgesinde oluşturulur. Varsayılan olarak, bu bölgede genel bir IP adresi de oluşturulur.

Portalda bu kaynakların bölge ayarlarını doğrulayabilirsiniz.  

1. **Kaynak grupları** ' nı ve ardından VM için kaynak grubunun adını ( *myresourcegroup*gibi) tıklatın.

2. Disk kaynağının adına tıklayın. **Genel bakış** sayfası, kaynağın konumu ve kullanılabilirlik bölgesi hakkındaki ayrıntıları içerir.

    ![Yönetilen disk için kullanılabilirlik bölgesi](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Genel IP adresi kaynağının adına tıklayın. **Genel bakış** sayfası, kaynağın konumu ve kullanılabilirlik bölgesi hakkındaki ayrıntıları içerir.

    ![IP adresi için kullanılabilirlik alanı](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kullanılabilirlik alanında nasıl sanal makine oluşturulacağını öğrendiniz. Azure VM 'Leri için [kullanılabilirlik](availability.md) hakkında daha fazla bilgi edinin.
