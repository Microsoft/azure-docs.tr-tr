---
title: Azure Cloud Shell penceresini kullanma | Microsoft Docs
description: Azure Cloud Shell penceresinin kullanımına genel bakış.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860311"
---
# <a name="using-the-azure-cloud-shell-window"></a>Azure Cloud Shell penceresini kullanma

Bu belgede Cloud Shell penceresinin nasıl kullanılacağı açıklanmaktadır.

## <a name="swap-between-bash-and-powershell-environments"></a>Bash ve PowerShell ortamları arasında takas et

Bash ve PowerShell ortamları arasında geçiş yapmak için Cloud Shell araç çubuğundaki ortam seçiciyi kullanın.  
![Ortam seçin](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Cloud Shell'i yeniden başlat
Makine durumunu sıfırlamak için Cloud Shell araç çubuğundaki yeniden Başlat simgesine tıklayın.  
![Cloud Shell'i yeniden başlat](media/using-the-shell-window/restart.png)
> [!WARNING]
> Cloud Shell yeniden başlatma, makine durumunu sıfırlar ve Azure dosya paylaşımınızda kalıcı olmayan tüm dosyalar kaybedilir.

## <a name="change-the-text-size"></a>Metin boyutunu değiştirme
Pencerenin sol üst kısmındaki Ayarlar simgesine tıklayın, ardından "metin boyutu" seçeneğinin üzerine gelin ve istediğiniz metin boyutunuzu seçin. Seçiminiz, oturumlarda kalıcı hale getirilir.
![Metin boyutu](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Yazı tipini değiştirme
Pencerenin sol üst kısmındaki Ayarlar simgesine tıklayın, ardından "yazı tipi" seçeneğinin üzerine gelin ve istediğiniz yazı tipini seçin.  Seçiminiz, oturumlarda kalıcı hale getirilir.
![Yazı tipi](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Dosyaları yükleme ve indirme
Pencerenin sol üst kısmındaki dosyaları karşıya yükle/indir simgesine tıklayın ve ardından karşıya yükle veya indir ' i seçin.  
![Dosyaları karşıya yükle/indir](media/using-the-shell-window/uploaddownload.png)
* Dosyaları karşıya yüklemek için, yerel bilgisayarınızdaki dosyaya gitmek üzere açılan pencereyi kullanın, istediğiniz dosyayı seçin ve "Aç" düğmesine tıklayın.  Dosya `/home/user` dizine yüklenir.
* Dosya indirmek için, tam dosya yolunu açılır pencereye (yani, varsayılan olarak gösterilen `/home/user` dizin altındaki bir yol) girin ve "İndir" düğmesini seçin.  
> [!NOTE] 
> Dosyalar ve dosya yolları Cloud Shell için büyük/küçük harfe duyarlıdır. Dosya yolunuzda büyük küçük harfe göz atın.

## <a name="open-another-cloud-shell-window"></a>Başka bir Cloud Shell penceresi aç
Cloud Shell, her bir oturumun ayrı bir işlem olarak mevcut olmasına izin vererek tarayıcı sekmelerinde birden çok eş zamanlı oturumu sağlar.
Bir oturumdan çıkılırken, her bir işlem aynı makinede çalıştırılsa da her bir işlem bağımsız olarak çalıştığı için her bir oturum penceresinde çıkmanız emin olun.  
Pencerenin sol üst kısmındaki Yeni oturum aç simgesine tıklayın. Mevcut kapsayıcıya bağlı başka bir oturum ile yeni bir sekme açılır.
![Yeni oturumu aç](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell Düzenleyicisi
* [Azure Cloud Shell düzenleyicisini kullanma](using-cloud-shell-editor.md) sayfasına bakın.

## <a name="web-preview"></a>Web önizlemesi
Pencerenin sol üst kısmındaki Web önizlemesi simgesine tıklayın, "Yapılandır" ı seçin, açmak istediğiniz bağlantı noktasını belirtin.  Yalnızca bağlantı noktasını açmak için "açık bağlantı noktası" ya da "aç ve araştır" ı seçerek bağlantı noktasını açın ve bağlantı noktasını yeni bir sekmede önizleyin.  
![Web önizlemesi](media/using-the-shell-window/preview.png)  
<br>
![Bağlantı noktasını yapılandır](media/using-the-shell-window/preview-configure.png)  
Pencerenin sol üst kısmındaki Web önizlemesi simgesine tıklayın, "Önizleme bağlantı noktası..." seçeneğini belirleyin. Yeni bir sekmede açık bir bağlantı noktasını önizlemek için. Pencerenin sol üst kısmındaki Web önizlemesi simgesine tıklayın, "bağlantı noktasını kapat..." seçeneğini belirleyin. açık bağlantı noktasını kapatmak için.  
![Önizleme/kapatma bağlantı noktası](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Cloud Shell pencerenin ekranı kaplamasını & Küçült
Pencerenin sağ üst köşesindeki Simge durumuna küçült simgesine tıklayarak gizleyin. Göstermek için Cloud Shell simgesine tekrar tıklayın.
Pencereyi maksimum yüksekliğe ayarlamak için Ekranı Kapla simgesine tıklayın. Pencereyi önceki boyutuna döndürmek için geri yükle ' ye tıklayın.  
![Pencereyi simge durumuna küçült veya Ekranı Kapla](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopyala ve yapıştır
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Cloud Shell pencereyi yeniden boyutlandır
Cloud Shell penceresini yeniden boyutlandırmak için araç çubuğunun üst kenarına tıklayın ve sürükleyin.

## <a name="scrolling-text-display"></a>Kayan metin görüntüsü
Terminal metnini taşımak için farenizle veya dokunmatik yüzeyde kaydırma yapın.

## <a name="exit-command"></a>Exit komutu
Çalıştırma `exit` etkin oturumu sonlandırır. Bu davranış, varsayılan olarak, etkileşim olmadan 20 dakika sonra gerçekleşir.

## <a name="next-steps"></a>Sonraki adımlar

[Bash Cloud Shell hızlı başlangıç](quickstart.md) <br>
[PowerShell Cloud Shell hızlı başlangıç](quickstart-powershell.md)
