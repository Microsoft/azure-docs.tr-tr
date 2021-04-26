---
title: 'Başvuru: bilinen sorunlar & sorun giderme'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Veri Bilimi Sanal Makinesi bilinen sorunların, geçici çözümlerin ve sorun gidermenin bir listesini alın
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: bcd75347f91109967ac48427ca0b8855c11b7d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101656865"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Veri Bilimi Sanal Makinesi bilinen sorunlar ve sorun giderme

Bu makale, Azure Veri Bilimi Sanal Makinesi kullanırken karşılaşabileceğiniz hataları veya hataları bulmanıza ve düzeltmenize yardımcı olur.

## <a name="python-package-installation-issues"></a>Python paketi yükleme sorunları

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Linux 'ta pımlar bağımlılıkları olan paketleri yükleme

`sudo pip install` `pip install` Paketler yüklenirken yerine kullanın.

## <a name="disk-encryption-issues"></a>Disk şifreleme sorunları

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM 'de disk şifrelemesi başarısız oluyor

Azure disk şifrelemesi (ADE) Şu anda Ubuntu DSVM üzerinde desteklenmiyor. Geçici bir çözüm olarak, [Azure yönetilen disklerinin sunucu tarafı şifrelemesini](../../virtual-machines/disk-encryption.md)yapılandırmayı göz önünde bulundurun.

## <a name="tool-appears-disabled"></a>Araç devre dışı görünüyor

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V, Windows DSVM 'de çalışmıyor

Başlangıçta Windows üzerinde çalışan Hyper-V beklenen davranıştır. Önyükleme performansı için bazı hizmetleri devre dışı bırakmış oldunuz. Hyper-V ' i etkinleştirmek için:

1. Windows DSVM 'niz üzerinde arama çubuğunu açın
1. "Hizmetler" yazın
1. Tüm Hyper-V hizmetlerini "El Ile" olarak ayarlayın
1. "Hyper-V sanal makine yönetimi" ni "otomatik" olarak ayarla

Son ekranınız şuna benzemelidir:

   ![Hyper-V'yi etkinleştirme](./media/workaround/hyperv-enable-dsvm.png)