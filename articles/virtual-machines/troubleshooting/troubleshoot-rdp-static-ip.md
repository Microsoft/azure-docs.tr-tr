---
title: Uzak Masaüstü Azure sanal makineler için statik IP nedeniyle olamaz | Microsoft Docs
description: Microsoft azure'da statik IP kaynaklanan RDP sorun gidermeyi öğrenin. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: d1cb34f1666db0e213a41fa8d2e684ba148b7e61
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088454"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Uzak Masaüstü Azure sanal makineler için statik IP nedeniyle olamaz

Bu makalede bir sorun sanal Makineye statik IP yapılandırdıktan sonra Uzak Masaüstü Azure Windows sanal makinelerin (VM'ler) olamaz.

> [!NOTE]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../azure-resource-manager/resource-manager-deployment-model.md). Bu makale, Klasik dağıtım modeli yerine yeni dağıtımlar için kullanmanızı öneririz Resource Manager dağıtım modelini kullanarak kapsar.

## <a name="symptoms"></a>Belirtiler

Azure'da VM ile RDP bağlantısı değişiklik yaptığınızda, aşağıdaki hata iletisini alıyorsunuz:

**Uzak Masaüstü aşağıdaki nedenlerden biri için uzak bilgisayara bağlanamıyor:**

1. **Uzaktan erişim sunucusu için etkin değil**

2. **Uzak bilgisayar kapalı**

3. **Uzak bilgisayarın ağda kullanılamaz**

**Uzak bilgisayarın açık ve ağa bağlı ve Uzaktan erişim etkinleştirildiğinden emin olun.**

Ne zaman iade ekran [önyükleme tanılaması](../troubleshooting/boot-diagnostics.md) Azure Portal'da, VM normal önyüklenir ve kimlik bilgileri oturum açma ekranında bekleyeceği görürsünüz.

## <a name="cause"></a>Nedeni

Sanal Makinenin ağ arabiriminde Windows içinde tanımlanmış statik bir IP adresi vardır. Bu IP adresi Azure Portalı'nda tanımlanan adresi farklıdır.

## <a name="solution"></a>Çözüm

Bu adımları gerçekleştirmeden önce etkilenen makinenin işletim sistemi diskinin anlık yedekleyin. Daha fazla bilgi için [bir diskin anlık görüntüsünü alma](../windows/snapshot-copy-managed-disk.md).

Bu sorunu çözmek için DHCP etkinleştirmek için seri denetimi kullanın veya [sıfırlama ağ arabirimi](reset-network-interface.md) VM için.

### <a name="use-serial-control"></a>Seri denetimini kullanma

1. Bağlanma [seri konsol ve örnek CMD Aç](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Seri konsol sanal makinenizde etkinleştirilmemiş olmadığını [sıfırlama ağ arabirimi](reset-network-interface.md).
2. DHCP ağ arabiriminde devre dışı bırakılıp bırakılmadığını kontrol edin:

        netsh interface ip show config
3. DHCP devre dışıysa, DHCP kullanacak şekilde ağ arabiriminin yapılandırmasını geri döndür:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Örneğin, "Ethernet 2" birlikte işlemek arabirimi adları ise aşağıdaki komutu çalıştırın:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. IP yapılandırması yeniden ağ arabirimi artık doğru şekilde ayarlandığından emin olmak için sorgu. Yeni IP adresini, Azure tarafından sağlanan hesapla eşleşmelidir.

        netsh interface ip show config

    Bu noktada VM yeniden başlatma gerekmez. VM'yi geri erişilebilir olacaktır.

Bundan sonra sanal makine için statik IP yapılandırmak istiyorsanız, bkz: [bir VM için statik IP adreslerini yapılandırın](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).