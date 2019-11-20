---
title: 'Bir sanal makinenin D: sürücüsünü veri diski yapın '
description: 'D: sürücüsünü veri sürücüsü olarak kullanabilmeniz için bir Windows sanal makinesi için sürücü harflerinin nasıl değiştirileceğini açıklar.'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 491e7b7be084017cc370fe431c3175ac5b2673f9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033646"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>D: sürücüsünü Windows VM 'de veri sürücüsü olarak kullanma
Uygulamanızın verileri depolamak için D sürücüsünü kullanması gerekiyorsa, geçici disk için farklı bir sürücü harfi kullanmak üzere bu yönergeleri uygulayın. Saklamanız gereken verileri depolamak için hiçbir şekilde geçici diski kullanmayın.

Bir sanal makineyi yeniden boyutlandırabilir veya **durdurursanız (serbest bırakırsanız)** , bu, sanal makinenin yerleşimini yeni bir hiper yöneticiye tetikleyebilir. Planlı veya planlanmamış bir bakım olayı da bu yerleşimi tetikleyebilir. Bu senaryoda geçici disk, kullanılabilir ilk sürücü harfine yeniden atanır. Özellikle D: sürücüsünü gerektiren bir uygulamanız varsa, pagefile. sys dosyasını geçici olarak taşımak, yeni bir veri diski eklemek ve D harfine atamak için bu adımları uygulamanız gerekir ve ardından pagefile. sys dosyasını geçici sürücüye geri taşıyın. İşlem tamamlandıktan sonra, sanal makine farklı bir Hiper yöneticide taşınırsa Azure, D: ' yi geri almaz.

Azure 'un geçici diski nasıl kullandığı hakkında daha fazla bilgi için bkz [. Microsoft Azure sanal makineler geçici sürücüyü anlama](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Veri diski iliştirme
İlk olarak, veri diskini sanal makineye bağlamanız gerekir. Bunu Portal 'ı kullanarak yapmak için, bkz. [Azure Portal yönetilen veri diski iliştirme](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Pagefile. sys dosyasını C sürücüsüne geçici olarak taşıma
1. Sanal makineye bağlanın. 
2. **Başlat** menüsüne sağ tıklayıp **sistem**' i seçin.
3. Sol taraftaki menüde **Gelişmiş sistem ayarları**' nı seçin.
4. **Performans** bölümünde **Ayarlar**' ı seçin.
5. **Gelişmiş** sekmesini seçin.
6. **Sanal bellek** bölümünde **Değiştir**' i seçin.
7. **C** sürücüsünü seçin ve ardından **Sistem Yönetimli boyut** ' a ve **Ayarla**' ya tıklayın.
8. **D** sürücüsünü seçin ve ardından **disk belleği dosyası yok** ' a tıkladıktan sonra **Ayarla**' ya tıklayın.
9. Uygula ' ya tıklayın. Değişikliklerin etkili olması için bilgisayarın yeniden başlatılması gerektiğini belirten bir uyarı alacaksınız.
10. Sanal makineyi yeniden başlatın.

## <a name="change-the-drive-letters"></a>Sürücü harflerini değiştirme
1. VM yeniden başlatıldıktan sonra VM 'ye tekrar oturum açın.
2. **Başlat** menüsüne tıklayın ve **diskmgmt. msc** yazın ve ENTER tuşuna basın. Disk Yönetimi başlar.
3. **D**'ye, geçici depolama sürücüsüne sağ tıklayın ve **sürücü harfini ve yollarını Değiştir**' i seçin.
4. Sürücü harfi altında **T** gibi yeni bir sürücü seçin ve ardından **Tamam**' a tıklayın. 
5. Veri diskine sağ tıklayın ve **sürücü harfini ve yolları Değiştir**' i seçin.
6. Sürücü harfi altında sürücü **D** ' yi seçin ve ardından **Tamam**' a tıklayın. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Pagefile. sys dosyasını geçici depolama sürücüsüne geri taşıma
1. **Başlat** menüsüne sağ tıklayıp **sistem** ' i seçin
2. Sol taraftaki menüde **Gelişmiş sistem ayarları**' nı seçin.
3. **Performans** bölümünde **Ayarlar**' ı seçin.
4. **Gelişmiş** sekmesini seçin.
5. **Sanal bellek** bölümünde **Değiştir**' i seçin.
6. **C** OS sürücüsünü seçin ve **disk belleği dosyası yok** ' a tıkladıktan sonra **Ayarla**' ya tıklayın.
7. Geçici **depolama sürücüsünü seçin** ve ardından **Sistem Yönetimli boyut** ' a ve **Ayarla**' ya tıklayın.
8. **Uygula**'ya tıklayın. Değişikliklerin etkili olması için bilgisayarın yeniden başlatılması gerektiğini belirten bir uyarı alacaksınız.
9. Sanal makineyi yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar
* [Ek bir veri diski ekleyerek](attach-managed-disk-portal.md)sanal makineniz için kullanılabilir depolama alanını artırabilirsiniz.

