---
title: Yerel hata ayıklama Event Grid Azure Işlevleri
description: Event Grid bir olay tarafından tetiklenen Azure işlevlerini yerel olarak hata ayıklamanın nasıl yapılacağını öğrenin
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure işlevleri, işlevler, sunucusuz mimari
ms.service: azure-functions
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: e28abbe8d44094d8599545479f4611a84e9d9bd5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085683"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure Işlevi Event Grid yerel hata ayıklamayı Tetikle

Bu makalede, bir depolama hesabı tarafından oluşturulan Azure Event Grid olayını işleyen yerel bir işlevde hata ayıklama işlemi gösterilmektedir. 

## <a name="prerequisites"></a>Önkoşullar

- Mevcut bir işlev uygulaması oluşturma veya kullanma
- Var olan bir depolama hesabı oluşturun veya kullanın
- Azure 'un yerel işlevinizi aramasını sağlamak için [ngrok](https://ngrok.com/) indirin

## <a name="create-a-new-function"></a>Yeni işlev oluştur

İşlev uygulamanızı Visual Studio 'da açın ve Çözüm Gezgini proje adına sağ tıklayın ve **> yeni Azure Işlevi Ekle**' ye tıklayın.

*Yeni Azure işlevi* penceresinde, **Event Grid tetikleyicisi** ' ni seçin ve **Tamam**' ı tıklatın.

![Yeni işlev oluşturma](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

İşlev oluşturulduktan sonra, kod dosyasını açın ve dosyanın en üstünde açıklamalı URL 'YI kopyalayın. Event Grid tetikleyicisi yapılandırılırken bu konum kullanılır.

![Konumu Kopyala](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Sonra, ile `log.LogInformation`başlayan satırda bir kesme noktası ayarlayın.

![Kesme noktası ayarla](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Sonra, bir hata ayıklama oturumu başlatmak için **F5 tuşuna basın** .

## <a name="allow-azure-to-call-your-local-function"></a>Azure 'un yerel işlevinizi aramasını sağlar

Makinenizde hata ayıklamakta olan bir işleve bölmek için, Azure 'un buluttan yerel işleviniz ile iletişim kurması için bir yol etkinleştirmeniz gerekir.

[Ngrok](https://ngrok.com/) yardımcı programı, Azure 'un makinenizde çalışan işlevi çağırması için bir yol sağlar. Aşağıdaki komutu kullanarak *ngrok* öğesini başlatın:

```bash
ngrok http -host-header=localhost 7071
```
Yardımcı program ayarlandığı için komut penceresi aşağıdaki ekran görüntüsüne benzer görünmelidir:

![Ngrok 'yi Başlat](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

*Ngrok* çalıştırıldığında oluşturulan **https** URL 'sini kopyalayın. Bu değer, Event Grid olay uç noktası yapılandırılırken kullanılır.

## <a name="add-a-storage-event"></a>Depolama olayı ekleme

Azure portal açın ve bir depolama hesabına gidin ve **Olaylar** seçeneğine tıklayın.

![Depolama hesabı ekleme olayı](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

*Olaylar* penceresinde **olay aboneliği** düğmesine tıklayın. *Çift abonelik* penceresinde, *uç nokta türü* açılan listesine tıklayın ve **Web kancası**' yi seçin.

![Abonelik türünü seçin](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Uç nokta türü yapılandırıldıktan sonra uç nokta değerini yapılandırmak için **uç nokta seç** ' e tıklayın.

![Uç nokta türünü seçin](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

*Abone uç noktası* değeri üç farklı değerden oluşur. Ön ek, *ngrok*tarafından oluşturulan https URL 'sidir. URL 'nin geri kalanı işlev kodu dosyasında bulunan URL 'den gelir ve işlev adı sona eklenir. İşlev kodu dosyasındaki URL 'den başlayarak, *ngrok* URL 'sinin yerini `http://localhost:7071` alır ve işlev adı değiştirilir `{functionname}`.

Aşağıdaki ekran görüntüsünde, son URL 'nin nasıl aranacağı gösterilmektedir:

![Uç nokta seçimi](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Uygun değeri girdikten sonra **Seçimi Onayla**' ya tıklayın.

> [!IMPORTANT]
> *Ngrok*'yi her BAŞLATTıĞıNıZDA, https URL 'si yeniden oluşturulur ve değer değişir. Bu nedenle, *ngrok*aracılığıyla Işlevinizi Azure 'a her kullanıma sunışınızda yeni bir olay aboneliği oluşturmanız gerekir.

## <a name="upload-a-file"></a>Karşıya dosya yükle

Şimdi, yerel işlevinizin işlemesi için bir Event Grid olayı tetiklemek üzere depolama hesabınıza bir dosya yükleyebilirsiniz. 

[Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) açın ve depolama hesabınıza bağlanın. 

- **BLOB kapsayıcılarını** Genişlet 
- Sağ tıklayıp **BLOB kapsayıcısı oluştur**' u seçin.
- Kapsayıcı **testini** adlandırın
- *Test* kapsayıcısını seçin
- **Karşıya yükle** düğmesine tıklayın
- **Dosyaları karşıya yükle** 'ye tıklayın
- Bir dosya seçin ve BLOB kapsayıcısına yükleyin

## <a name="debug-the-function"></a>İşlevde hata ayıkla

Event Grid, depolama kapsayıcısına yeni bir dosya yükledikten sonra, yerel işlevinizde kesme noktası isabet edilir.

![Ngrok 'yi Başlat](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynakları temizlemek için Depolama hesabınızdaki **Test** kapsayıcısını silin.

## <a name="next-steps"></a>Sonraki adımlar

- [Karşıya yüklenen görüntüleri yeniden boyutlandırmayı Event Grid kullanarak otomatikleştirme](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Işlevleri için Event Grid tetikleyicisi](./functions-bindings-event-grid.md)
