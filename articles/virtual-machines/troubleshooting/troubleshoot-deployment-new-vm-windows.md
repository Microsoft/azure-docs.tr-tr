---
title: Azure 'da Windows VM dağıtımı sorunlarını giderme | Microsoft Docs
description: Azure 'da yeni bir Windows sanal makinesi oluştururken Kaynak Yöneticisi dağıtım sorunlarını giderme
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6dbe4c1533aecfab4a62ce3ad90b694c0c00f4b6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103481"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Azure 'da yeni bir Windows VM oluştururken dağıtım sorunlarını giderme
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>En önemli sorunlar
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Diğer VM dağıtım sorunları ve soruları için bkz. [Azure 'Da Windows sanal makine sorunlarını giderme](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Etkinlik günlüklerini topla
Sorun gidermeye başlamak için, sorunla ilişkili hatayı belirlemek üzere etkinlik günlüklerini toplayın. Aşağıdaki bağlantılar, izlenecek işlemle ilgili ayrıntılı bilgiler içerir.

[Dağıtım işlemlerini görüntüleme](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Azure kaynaklarını yönetmek için etkinlik günlüklerini görüntüleme](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**IZ** İşletim sistemi Windows genelleştirilemez ve bu, yüklenip/veya Genelleştirilmiş ayarla yakalandıysa, herhangi bir hata olmayacaktır. Benzer şekilde, işletim sistemi Windows özelleştirilmiştir ve bu, yüklenip/veya özel ayar ile yakalandıysa, herhangi bir hata olmayacaktır.

**Karşıya yükleme hataları:**

**N<sup>1</sup>:** İşletim sistemi Windows genelleştirilir ve özelleştirilmiş olarak karşıya yüklenirse, VM ile OOBE ekranında takılı bir sağlama zaman aşımı hatası alırsınız.

**N<sup>2</sup>:** İşletim sistemi Windows özel ise ve genelleştirilemez olarak yüklenirse, yeni VM özgün bilgisayar adı, Kullanıcı adı ve parolasıyla çalıştığı için, VM 'nin OOBE ekranına takılmış bir sağlama hatası hatası alırsınız.

**Çözümleme**

Bu hataları çözmek için, şirket içinde kullanılabilir olan özgün VHD 'yi, işletim sistemi (Genelleştirilmiş/özelleştirilmiş) ile aynı ayarla birlikte [karşıya yüklemek Için Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd)kullanın. Genelleştirilmiş olarak karşıya yüklemek için önce Sysprep 'i çalıştırmayı unutmayın.

**Yakalama hataları:**

**N<sup>3</sup>:** İşletim sistemi Windows genelleştirilir ve özel olarak yakalandıysa, orijinal VM Genelleştirilmiş olarak işaretlendiğinden kullanılabilir olmadığı için bir sağlama zaman aşımı hatası alırsınız.

**N<sup>4</sup>:** İşletim sistemi Windows özel ise ve genelleştirilmiş olarak yakalandıysa, yeni VM özgün bilgisayar adı, Kullanıcı adı ve parolasıyla çalıştığı için bir sağlama hatası hatası alırsınız. Ayrıca, özgün VM özelleştirilmiş olarak işaretlendiğinden kullanılabilir değildir.

**Çözümleme**

Bu hataları gidermek için, portaldan geçerli görüntüyü silin ve [geçerli VHD 'lerden](../windows/create-vm-specialized.md) , işletim sistemi (Genelleştirilmiş/özelleştirilmiş) ile aynı ayarı kullanarak yeniden yakalayın.

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Sorun: Özel/Galeri/Market görüntüsü; ayırma hatası
Bu hata, yeni VM isteği istenen VM boyutunu destekleyebilen bir kümeye sabitlendiğinde veya isteğe uygun boş alana sahip olmadığında ortaya çıkar.

**Neden 1:** Küme, istenen VM boyutunu desteklemez.

**Çözüm 1:**

* Daha küçük bir VM boyutu kullanarak isteği yeniden deneyin.
* İstenen VM 'nin boyutu değiştirilenemez:
  * Kullanılabilirlik kümesindeki tüm VM 'Leri durdurun.
    Kaynak **grupları kaynak grupları** >  > 'na > tıklayın kullanılabilirlik kümesisanal > **makineleriniz sanal makineniz** > *Durdur.*  > 
  * Tüm VM 'Ler durduktan sonra, istenen boyutta yeni VM 'yi oluşturun.
  * Önce yeni VM 'yi başlatın ve sonra durdurulan sanal makinelerin her birini seçip **Başlat**' a tıklayın.

**Neden 2:** Kümede boş kaynak yok.

**Çözüm 2:**

* İsteği daha sonra yeniden deneyin.
* Yeni VM farklı bir kullanılabilirlik kümesinin parçası olabilir
  * Farklı bir kullanılabilirlik kümesinde (aynı bölgede) yeni bir VM oluşturun.
  * Yeni VM 'yi aynı sanal ağa ekleyin.

## <a name="next-steps"></a>Sonraki adımlar
Durdurulmuş bir Windows sanal makinesini başlattığınızda veya Azure 'da var olan bir Windows VM 'yi yeniden boyutlandırdığınızda sorunlarla karşılaşırsanız bkz. [Azure 'da var olan bir Windows sanal makinesini yeniden başlatma veya yeniden boyutlandırma ile ilgili dağıtım sorunlarını giderme Kaynak Yöneticisi](restart-resize-error-troubleshooting.md).


