---
title: Azure Izleyici ile Surface Hub 'Larını izleme | Microsoft Docs
description: Surface Hub 'larınızın durumunu izlemek ve bunların nasıl kullanıldığını anlamak için Surface Hub çözümünü kullanın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2018
ms.openlocfilehash: 7ced5f678b9f8b2d4aa073a984276f41b8b7c4b9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900632"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Azure Izleyici ile Surface Hub 'larını izleyerek sistem durumlarını izleyin

![Surface Hub simgesi](./media/surface-hubs/surface-hub-symbol.png)

Bu makalede, Microsoft Surface Hub cihazlarını izlemek için Azure Izleyici 'de Surface Hub çözümünü nasıl kullanabileceğiniz açıklanır. Çözüm, Surface Hub 'larınızın durumunu izlemenize ve bunların nasıl kullanıldığını anlamanıza yardımcı olur.

Her Surface Hub Microsoft Monitoring Agent yüklüdür. Aracı aracılığıyla Surface Hub verileri Azure Izleyici 'deki bir Log Analytics çalışma alanına gönderebilirsiniz. Günlük dosyaları, Surface Hub 'larından okundum ve Azure Izleyici 'ye gönderilir. Azure Izleyici 'de Surface Hub panosunda, sunucular çevrimdışıyken, takvim eşitlenmiyor veya cihaz hesabı Skype Kurumsal oturum açamıyor gibi sorunlar görüntülenir. Panodaki verileri kullanarak, çalıştırmayan veya başka sorunlar yaşayan cihazları tanımlayabilir ve algılanan sorunlar için olası düzeltmeleri uygulayabilirsiniz.

## <a name="install-and-configure-the-solution"></a>Çözümü yükleyip yapılandırma
Çözümü yüklemek ve yapılandırmak için aşağıdaki bilgileri kullanın. Surface Hub 'larınızı Azure Izleyici 'de yönetmek için şunlar gerekir:

* İzlemek istediğiniz cihaz sayısını destekleyecek [Log Analytics abonelik](https://azure.microsoft.com/pricing/details/log-analytics/) düzeyi. Log Analytics fiyatlandırma, kaç cihazın kayıtlı olduğuna ve ne kadar veri işleme olduğuna bağlı olarak değişir. Surface Hub dağıtımı planlarken bunu dikkate almak isteyeceksiniz.

Sonra, var olan bir Log Analytics çalışma alanını ekleyecek veya yeni bir tane oluşturacaksınız. Her iki yöntemi kullanmaya yönelik ayrıntılı yönergeler [Azure portal Log Analytics çalışma alanı oluşturur](../learn/quick-create-workspace.md). Log Analytics çalışma alanı yapılandırıldıktan sonra, Surface Hub cihazlarınızı kaydetmek için iki yol vardır:

* Intune üzerinden otomatik olarak
* Surface Hub cihazınızdaki **Ayarlar** aracılığıyla el ile.

## <a name="set-up-monitoring"></a>İzlemeyi ayarlama
Azure Izleyici 'yi kullanarak Surface Hub sistem durumunu ve etkinliğini izleyebilirsiniz. Surface Hub, Intune kullanarak veya Surface Hub **ayarları** kullanarak yerel olarak kaydedebilirsiniz.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Surface Hub 'Larını Intune aracılığıyla Azure Izleyici 'ye bağlama
Surface Hub 'larınızı yönetecek Log Analytics çalışma alanı için çalışma alanı KIMLIĞI ve çalışma alanı anahtarına ihtiyacınız olacak. Azure portal çalışma alanı ayarlarından alabilirsiniz.

Intune, cihazlarınızdan bir veya daha fazlasına uygulanan Log Analytics çalışma alanı yapılandırma ayarlarını merkezi olarak yönetmenize olanak tanıyan bir Microsoft ürünüdür. Cihazlarınızı Intune ile yapılandırmak için aşağıdaki adımları izleyin:

1. Intune 'da oturum açın.
2. **Bağlı kaynaklara** > **Ayarlar** ' a gidin.
3. Surface Hub şablonunu temel alan bir ilke oluşturun veya düzenleyin.
4. İlkenin Azure Operasyonel Öngörüler bölümüne gidin ve ilkeye Log Analytics *çalışma alanı kimliği* ve *çalışma alanı anahtarını* ekleyin.
5. İlkeyi kaydedin.
6. İlkeyi uygun cihaz grubuyla ilişkilendirin.

   ![Intune ilkesi](./media/surface-hubs/intune.png)

Daha sonra Intune, Log Analytics ayarlarını hedef gruptaki cihazlarla eşitler ve bunları Log Analytics çalışma alanınıza kaydetmektir.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Ayarlar uygulamasını kullanarak Surface Hub 'Larını Azure Izleyici 'ye bağlama
Surface Hub 'larınızı yönetecek Log Analytics çalışma alanı için çalışma alanı KIMLIĞI ve çalışma alanı anahtarına ihtiyacınız olacak. Azure portal Log Analytics çalışma alanı ayarlarından alabilirsiniz.

Ortamınızı yönetmek için Intune kullanmıyorsanız, her bir Surface Hub **ayarları** aracılığıyla cihazları el ile kaydedebilirsiniz:

1. Surface Hub, **Ayarlar**' ı açın.
2. İstendiğinde Cihaz Yöneticisi kimlik bilgilerini girin.
3. **Bu cihaza**ve **izleme**' ye tıklayın **Log Analytics Ayarları Yapılandır**' a tıklayın.
4. **Izlemeyi etkinleştir**' i seçin.
5. Log Analytics Ayarları iletişim kutusunda, Log Analytics **çalışma alanı kimliğini** yazın ve **çalışma alanı anahtarını**yazın.  
   ![ayarları](./media/surface-hubs/settings.png)
6. Yapılandırmayı gerçekleştirmek için **Tamam** ' ı tıklatın.

Yapılandırmanın cihaza başarıyla uygulanıp uygulanmadığını belirten bir onay görüntülenir. Bu ise, aracının Azure Izleyici 'ye başarıyla bağlandığını belirten bir ileti görünür. Daha sonra cihaz, verileri görüntüleyebileceğiniz ve üzerinde davranabileceğiniz Azure Izleyici 'ye veri göndermeye başlar.

## <a name="monitor-surface-hubs"></a>Surface Hub 'Ları izleme
Azure Izleyici 'yi kullanarak Surface Hub 'larınızı izlemek, diğer kayıtlı cihazları izlemeye çok benzer.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Surface Hub kutucuğuna tıkladığınızda cihazınızın sistem durumu görüntülenir.

   ![Surface Hub panosu](./media/surface-hubs/surface-hub-dashboard.png)

Mevcut veya özel günlük aramalarına göre [Uyarılar](../platform/alerts-overview.md) oluşturabilirsiniz. Azure Izleyici, Surface Hub 'larınızdan toplanan verileri kullanarak, cihazlarınız için tanımladığınız koşullarda sorun ve uyarı araması yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* Ayrıntılı Surface Hub verilerini görüntülemek için [Azure izleyici 'de günlük sorgularını](../log-query/log-query-overview.md) kullanın.
* Surface Hub 'larınız ile ilgili sorunlar oluştuğunda size bildirimde bulunan [Uyarılar](../platform/alerts-overview.md) oluşturun.
