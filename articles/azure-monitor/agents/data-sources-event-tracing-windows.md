---
title: Analiz Azure Izleyici günlükleri için Windows için olay Izleme (ETW) olayları toplanıyor
description: Azure Izleyici günlüklerinde analize yönelik olay Izlemeyi (ETW) nasıl toplayacağınızı öğrenin.
services: azure-monitor
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 73135d95a56dc03790b3b7368a276ebfc99275fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025076"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Analiz Azure Izleyici günlükleri için Windows için olay Izleme (ETW) olayları toplanıyor

*Windows Için olay izleme (ETW)* , Kullanıcı modu uygulamalarının ve çekirdek modu sürücülerin izlenmesi için bir mekanizma sağlar. Log Analytics Aracısı, yönetim ve operasyonel [ETW kanallarına](/windows/win32/wes/eventmanifestschema-channeltype-complextype)yazılan [Windows olaylarını toplamak](./data-sources-windows-events.md) için kullanılır. Ancak, analitik kanala yazılan gibi diğer olayları yakalamak ve analiz etmek de gerekebilir.  

## <a name="event-flow"></a>Olay akışı

Azure Izleyici günlüklerinde analizler için [bildirim tabanlı ETW olaylarını](/windows/win32/etw/about-event-tracing#types-of-providers) başarıyla toplamak üzere Windows için [Azure tanılama uzantısı](./diagnostics-extension-overview.md) 'nı (wad) kullanmanız gerekir. Bu senaryoda, tanılama uzantısı ETW tüketicisi görevi görür ve olayları bir ara mağaza olarak Azure depolama 'ya (tablolar) yazar. Burada, **Wadevillutablo** adlı bir tabloda depolanır. Log Analytics daha sonra Azure depolama alanından tablo verilerini toplayıp **Etwevent** adlı bir tablo olarak sunarak.

![Olay akışı](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>ETW günlük toplamasını yapılandırma

### <a name="step-1-locate-the-correct-etw-provider"></a>1. Adım: doğru ETW sağlayıcısını bulma

Kaynak bir Windows sisteminde ETW sağlayıcılarını numaralandırmak için aşağıdaki komutlardan birini kullanın.

Komut satırı:

```
logman query providers
```

PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
İsteğe bağlı olarak, gezinmeye yardımcı olması için bu PowerShell çıkışını Out-Gridview için yöneltmeye seçim yapabilirsiniz.

Olay Görüntüleyicisi sunulan analitik veya hata ayıklama günlüğüne veya olay verilerini toplamak istediğiniz modüle göre gösterilen ETW sağlayıcı adını ve GUID 'yi kaydedin.

### <a name="step-2-diagnostics-extension"></a>2. Adım: Tanılama uzantısı

Tüm kaynak sistemlerde *Windows Tanılama uzantısının* [yüklü](./diagnostics-extension-windows-install.md#install-with-azure-portal) olduğundan emin olun.

### <a name="step-3-configure-etw-log-collection"></a>3. Adım: ETW günlük toplamayı yapılandırma

1. Sanal makinenin **Tanılama ayarları** dikey penceresine gidin

2. **Günlükler** sekmesini seçin

3. Aşağı kaydırın ve **Windows Için olay izleme (ETW) olayları** seçeneğini etkinleştirin ![ Tanılama ayarlarının ekran görüntüsü](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Koleksiyonu yapılandırdığınız sağlayıcıyı temel alan sağlayıcı GUID 'sini veya sağlayıcı sınıfını ayarlayın

5. [**Günlük düzeyini**](/windows/win32/etw/configuring-and-starting-an-event-tracing-session) uygun şekilde ayarlayın

6. Sağlanan sağlayıcının yanındaki üç noktaya tıklayın ve **Yapılandır** ' a tıklayın.

7. **Varsayılan hedef tablonun** **etweventtable** olarak ayarlandığından emin olun

8. Gerekirse [**anahtar sözcük filtresi**](/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) ayarla

9. Sağlayıcıyı ve günlük ayarlarını kaydetme

Eşleşen olaylar oluşturulduktan sonra, Azure depolama 'daki **Wadevillutablo** tablosunda görünen ETW olaylarını görmeniz gerekir. Bunu doğrulamak için Azure Depolama Gezgini kullanabilirsiniz.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>4. Adım: Log Analytics depolama hesabı koleksiyonunu yapılandırma

Azure depolama 'dan günlükleri toplamak için [Bu yönergeleri](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) izleyin. Yapılandırıldıktan sonra, ETW olay verileri, **Etwevent** tablosu altında Log Analytics görünmelidir.

## <a name="next-steps"></a>Sonraki adımlar
- [Özel alanları](../logs/custom-fields.md) kullanarak ETW olaylarınızın yapısını oluşturun
- Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../logs/log-query-overview.md) hakkında bilgi edinin.