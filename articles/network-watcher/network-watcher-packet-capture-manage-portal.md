---
title: Paket yakalamalarını yönetme-Azure portal
titleSuffix: Azure Network Watcher
description: Azure portal kullanarak ağ Izleyicisi 'nin paket yakalama özelliğini yönetmeyi öğrenin.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d4112bd1d632ec21e2d8cb565027277eeb85452d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018268"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Portalı kullanarak paket yakalamalarını Azure ağ Izleyicisi ile yönetme

Ağ Izleyicisi paket yakalama, bir sanal makineden gelen ve giden trafiği izlemek için yakalama oturumları oluşturmanızı sağlar. Yalnızca istediğiniz trafiği yakalamanızı sağlamak için yakalama oturumu için filtreler sağlanır. Paket yakalama, ağ anormalilerin hem yeniden etkin hem de proaktif olarak tanımlanmasına yardımcı olur. Diğer kullanımlar arasında ağ istatistiklerini toplama, ağ erişimi ile ilgili bilgi alma, istemci-sunucu iletişiminde hata ayıklama ve çok daha fazlası yer alır. Paket yakalamalarını uzaktan tetikleyebilmek için, istenen bir sanal makinede bir paket yakalamayı çalıştırmanın yükünü kolaylaştırır ve bu da değerli süreyi kaydeder.

Bu makalede bir paket yakalamayı başlatmayı, durdurmayı, indirmeyi ve silmeyi öğreneceksiniz. 

## <a name="before-you-begin"></a>Başlamadan önce

Paket yakalama için aşağıdaki giden TCP bağlantısı gereklidir:
- 443 numaralı bağlantı noktası üzerinden seçilen depolama hesabına
- 80 numaralı bağlantı noktası üzerinden 169.254.169.254
- 8037 numaralı bağlantı noktası üzerinden 168.63.129.16

> [!NOTE]
> Yukarıdaki ikinci iki durumda bahsedilen bağlantı noktaları, ağ Izleyicisi uzantısını içeren tüm ağ Izleyicisi özellikleri genelinde ortaktır ve zaman zaman değişebilir.


Ağ güvenlik grubu, ağ arabirimi veya ağ arabiriminin içinde bulunduğu alt ağ ile ilişkiliyse, önceki bağlantı noktalarına izin veren kuralların mevcut olduğundan emin olun. Benzer şekilde, ağınıza Kullanıcı tanımlı trafik rotaları eklemek, yukarıda bahsedilen IP ve bağlantı noktalarıyla bağlantıyı engelleyebilir. Lütfen erişilebilir olduklarından emin olun. 

## <a name="start-a-packet-capture"></a>Paket yakalaması başlatma

1. Tarayıcınızda [Azure Portal](https://portal.azure.com) gidin ve **tüm hizmetler**' i seçin ve ağ **İzleyicisi** ' ni **seçin.**
2. **Ağ tanılama araçları** altında **paket yakalama** ' yı seçin. Mevcut paket yakalamaları, durumlarına bakılmaksızın listelenir.
3. Paket yakalama oluşturmak için **Ekle** ' yi seçin. Aşağıdaki özellikler için değer seçebilirsiniz:
   - **Abonelik**: paket yakalamayı oluşturmak istediğiniz sanal makinenin içinde olduğu abonelik.
   - **Kaynak grubu**: sanal makinenin kaynak grubu.
   - **Hedef sanal makine**: paket yakalamayı oluşturmak istediğiniz sanal makine.
   - **Paket yakalama adı**: paket yakalama için bir ad.
   - **Depolama hesabı veya dosyası**: **depolama hesabı**, **Dosya** veya her ikisini birden seçin. **Dosya**' yı seçerseniz yakalama, sanal makine içindeki bir yola yazılır.
   - **Yerel dosya yolu**: sanal makinedeki paket yakalamanın kaydedileceği yerel yol (yalnızca *Dosya* seçildiğinde geçerlidir). Yol geçerli bir yol olmalıdır. Linux sanal makinesi kullanıyorsanız yolun */var/yakalamaları* ile başlaması gerekir.
   - **Depolama hesapları**: *depolama hesabı*' nı seçtiyseniz, var olan bir depolama hesabını seçin. Bu seçenek yalnızca **depolama**' yı seçtiyseniz kullanılabilir.
   
     > [!NOTE]
     > Premium Depolama hesapları Şu anda paket yakalamalarını depolamak için desteklenmiyor.

   - **Paket başına en fazla bayt**: yakalanan her paketten bayt sayısı. Boş bırakılırsa, tüm baytlar yakalanır.
   - **Oturum başına en fazla bayt**: yakalanan toplam bayt sayısıdır. Değere ulaşıldığında, paket yakalama durdu.
   - **Süre sınırı (saniye)**: paket yakalamanın durdurulmadan önce geçmesi gereken süre. Varsayılan değer 18.000 saniyedir.
   - Filtreleme (Isteğe bağlı). **+ Filtre Ekle** ' yi seçin
     - **Protokol**: paket yakalama için filtrelenecek protokol. Kullanılabilir değerler TCP, UDP ve any ' dir.
     - **Yerel IP adresi**: yerel IP adresinin bu değerle eşleştiği paketler için paket yakalamaya filtre uygular.
     - **Yerel bağlantı noktası**: yerel bağlantı noktasının bu değerle eşleştiği paketler için paket yakalamayı filtreler.
     - **Uzak IP adresi**: uzak IP adresinin bu değerle eşleştiği paketler için paket yakalamaya filtre uygular.
     - **Uzak bağlantı noktası**: uzak bağlantı noktasının bu değerle eşleştiği paketler için paket yakalamayı filtreler.
    
     > [!NOTE]
     > Bağlantı noktası ve IP adresi değerleri tek bir değer, değer aralığı veya bağlantı noktası için 80-1024 gibi bir Aralık olabilir. İhtiyaç duyduğunuz kadar çok filtre tanımlayabilirsiniz.

4. **Tamam**’ı seçin.

Paket yakalamada ayarlanan zaman sınırının süresi dolduktan sonra, paket yakalama durdurulur ve incelenebilir. Ayrıca, bir paket yakalama oturumunu el ile durdurabilirsiniz.

> [!NOTE]
> Portal otomatik olarak:
>  * Bölgenin zaten bir ağ izleyicisi yoksa, seçtiğiniz sanal makinenin bulunduğu bölge ile aynı bölgede bir ağ izleyicisi oluşturur.
>  * *AzureNetworkWatcherExtension* [Linux](../virtual-machines/extensions/network-watcher-linux.md) veya [Windows](../virtual-machines/extensions/network-watcher-windows.md) sanal makine uzantısını, zaten yüklenmemişse sanal makineye ekler.

## <a name="delete-a-packet-capture"></a>Paket yakalamayı silme

1. Paket yakalama görünümünde, paket yakalamanın sağ tarafındaki **...** seçeneğini belirleyin veya var olan bir paket yakalamaya sağ tıklayın ve **Sil**' i seçin.
2. Paket yakalamayı silmek istediğinizi onaylamanız istenir. **Evet**’i seçin.

> [!NOTE]
> Bir paket yakalamanın silinmesi, depolama hesabındaki veya sanal makinedeki yakalama dosyasını silmez.

## <a name="stop-a-packet-capture"></a>Paket yakalamayı durdur

Paket yakalama görünümünde, paket yakalamanın sağ tarafındaki **...** seçeneğini belirleyin veya var olan bir paket yakalamaya sağ tıklayın ve **Durdur**' u seçin.

## <a name="download-a-packet-capture"></a>Paket yakalama indirin

Paket yakalama oturumunuz tamamlandığında, yakalama dosyası BLOB depolama alanına veya sanal makinedeki yerel bir dosyaya yüklenir. Paket yakalamanın depolama konumu, paket yakalamanın oluşturulması sırasında tanımlanmıştır. Bir depolama hesabına kaydedilmiş yakalama dosyalarına erişmek için uygun bir araç, Microsoft Azure Depolama Gezgini [indirebilir](https://storageexplorer.com/).

Bir depolama hesabı belirtilmişse, paket yakalama dosyaları aşağıdaki konumdaki bir depolama hesabına kaydedilir:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Yakalamayı oluştururken **Dosya** ' yı seçtiyseniz, dosyayı sanal makinede yapılandırdığınız yoldan görüntüleyebilir veya indirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Paket yakalamalarını sanal makine uyarılarla otomatik hale getirmeyi öğrenmek için bkz. [paket yakalamayı tetikleyen uyarı oluşturma](network-watcher-alert-triggered-packet-capture.md).
- Bir sanal makinede belirli trafiğe izin verilip verilmediğini belirlemek için, bkz. [sanal makine ağ trafiği filtresini tanılama sorunu](diagnose-vm-network-traffic-filtering-problem.md).