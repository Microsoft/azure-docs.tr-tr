---
title: Dataköpek kaynağını yönetme-Azure iş ortağı çözümleri
description: Bu makalede Azure portal bir Dataköpek kaynağının yönetimi açıklanmaktadır. Çoklu oturum açmayı ayarlama, bir veya daha fazla kuruluşu silme ve destek alma.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 04aef540bc134e5ec307be6a232ce47f0923e528
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046360"
---
# <a name="manage-the-datadog-resource"></a>Dataköpek kaynağını yönetme

Bu makalede, Azure tümleştirmeyle ilgili ayarların veri Köpede nasıl yönetileceği gösterilmektedir.

## <a name="resource-overview"></a>Kaynağa genel bakış

Dataköpek kaynağınızın ayrıntılarını görmek için sol bölmedeki **genel bakış** ' ı seçin.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Dataköpek kaynağına genel bakış" border="true" lightbox="media/manage/resource-overview.png":::

Ayrıntılar şunları içerir:

- Kaynak grubu adı
- Konum/bölge
- Abonelik
- Etiketler
- Dataköpek kuruluşuna çoklu oturum açma bağlantısı
- Veri köpek teklifi/planı
- Fatura dönemi

Ayrıca, Dataköpek panoları, günlükleri ve konak haritaları için bağlantılar sağlar.

Genel bakış ekranı, veri köklerini ve ölçümleri gönderen kaynakların özetini sağlar.

- Kaynak türü – Azure Kaynak türü.
- Toplam Kaynaklar: kaynak türü için tüm kaynakların sayısı.
- Kaynakları gönderen kaynaklar – tümleştirme aracılığıyla veri köpeye günlük gönderen kaynak sayısı.
- Ölçümleri gönderen kaynaklar – tümleştirme aracılığıyla veri kökleri için ölçüm gönderen kaynak sayısı.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Ölçümler ve Günlükler için kuralları yeniden yapılandırın

Ölçümler ve Günlükler için yapılandırma kurallarını değiştirmek üzere sol bölmedeki **ölçümler ve Günlükler** ' i seçin.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Dataköpek kaynağı için günlüklerin ve ölçümlerin yapılandırmasını değiştirin." border="true":::

Daha fazla bilgi için bkz. [ölçümleri ve günlükleri yapılandırma](create.md#configure-metrics-and-logs).

## <a name="view-monitored-resources"></a>İzlenen kaynakları görüntüle

Verileri Dataköpek 'e yayma kaynak listesini görmek için sol bölmedeki **Izlenen kaynaklar** ' ı seçin.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Veri köpek tarafından izlenen kaynakları görüntüle" border="true":::

Kaynak türüne, kaynak grubu adına, konuma ve kaynağın günlükleri ve ölçümleri gönderip göndermediğini göre kaynak listesini filtreleyebilirsiniz.

Sütun, **veri köpeye Günlükler** , kaynağın dataköpek 'a günlük gönderip göndermediğini belirtir. Kaynak, günlükleri göndermemişse, bu alan günlüklerin neden veri Köbir şekilde gönderilmediğini belirtir. Nedenler şunlar olabilir:

- Kaynak, günlüklerin gönderilmesini desteklemiyor. Yalnızca izleme günlüğü kategorileri olan kaynak türleri, günlükleri veri kökten gönderecek şekilde yapılandırılabilir.
- Beş tanılama ayarı sınırına ulaşıldı. Her Azure kaynağının en fazla beş tanılama ayarı olabilir. Daha fazla bilgi için bkz. [Tanılama ayarları](../../azure-monitor/essentials/diagnostic-settings.md).
- Hata. Kaynak, günlükleri veri köpeye gönderecek şekilde yapılandırılmış, ancak bir hata tarafından engelleniyor.
- Günlükler yapılandırılmadı. Yalnızca uygun kaynak etiketlere sahip Azure kaynakları veri Köline Günlükler gönderecek şekilde yapılandırılmıştır.
- Bölge desteklenmiyor. Azure kaynağı, şu anda veri Köbir şekilde günlük göndermeyi desteklemeyen bir bölgedir.
- Dataköpek Aracısı yapılandırılmadı. Dataköpek Aracısı yüklü olmayan sanal makineler, günlükleri veri köpeye yaymayın.

## <a name="api-keys"></a>API Anahtarları

Dataköpek kaynağınız için API anahtarlarının listesini görüntülemek için sol bölmedeki **tuşları** seçin. Anahtarlarla ilgili bilgileri görürsünüz.

:::image type="content" source="media/manage/keys.png" alt-text="Dataköpek organizasyonu için API anahtarları." border="true":::

Azure portal, API anahtarlarının salt okunurdur görünümünü sağlar. Anahtarları yönetmek için Dataköpek portalı bağlantısını seçin. Dataköpek portalında değişiklikler yaptıktan sonra Azure portal görünümünü yenileyin.

Azure Dataköpek tümleştirmesi, bir sanal makineye veya App Service 'e Dataköpek Aracısı 'nı yüklemenize olanak sağlar. Varsayılan anahtar seçilmezse, Dataköpek Aracısı yüklemesi başarısız olur.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Datadog aracısını kullanarak sanal makineleri izleme

Veri köpek aracılarını, sanal makinelere uzantı olarak yükleyebilirsiniz. Sol bölmedeki Dataköpek org yapılandırmalarının altında **sanal makine aracısına** gidin. Bu ekranda, abonelikteki tüm sanal makinelerin listesi gösterilir.

Her sanal makine için aşağıdaki veriler görüntülenir:

- Kaynak adı – sanal makine adı
- Kaynak durumu – sanal makinenin durdurulmuş veya çalışır durumda olup olmadığı. Dataköpek Aracısı yalnızca çalıştıran sanal makinelere yüklenebilir. Sanal makine durdurulmuşsa, Dataköpek aracısının yüklenmesi devre dışı bırakılır.
- Aracı sürümü – Dataköpek Aracısı sürüm numarası.
- Aracı durumu – sanal makinede Dataköpek aracısının çalışıp çalışmadığını belirtir.
- Tümleştirmeler etkinleştirildi: Dataköpek Aracısı tarafından toplanmakta olan anahtar ölçümleri.
- Install yöntemi: Dataköpek Aracısı 'nı yüklemek için kullanılan özel araç. Örneğin, Chef veya script.
- Günlükleri gönderme – Dataköpek aracısının verileri veri Köbir şekilde gönderip göndermediğini belirtir.

Dataköpek aracısının yükleneceği sanal makineyi seçin. **Aracıyı yüklemeyi** seçin.

Portal, aracıyı varsayılan anahtarla yüklemek istediğinizi onaylamanızı ister. Yüklemeyi başlatmak için **Tamam ' ı** seçin. Azure, aracı yüklenip sağlanana kadar durumu **yükleme** olarak gösterir.

Dataköpek aracısı yüklendikten sonra durum yüklendi olarak değişir.

Dataköpek aracısının yüklü olduğunu görmek için, sanal makineyi seçin ve uzantılar penceresine gidin.

Sanal **makine aracısına** giderek, bir sanal makinede dataköpek aracılarını kaldırabilirsiniz. Sanal makineyi seçin ve **aracıyı kaldırın**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Uzantı olarak Dataköpek Aracısı 'nı kullanarak uygulama hizmetlerini izleme

Dataköpek aracılarını, uygulama hizmetlerine uzantı olarak yükleyebilirsiniz. Sol bölmedeki **App Service uzantıya** gidin. Bu ekran, abonelikteki tüm uygulama hizmetlerinin listesini gösterir.

Her bir App Service için aşağıdaki veri öğeleri görüntülenir:

- Kaynak adı – sanal makine adı.
- Kaynak durumu – App Service 'in durdurulmuş veya çalışır durumda olup olmadığı. Dataköpek Aracısı yalnızca çalıştıran uygulama hizmetleri üzerine yüklenebilir. App Service durdurulmuşsa, Dataköpek aracısının yüklenmesi devre dışı bırakılır.
- App Service planı: App Service için yapılandırılmış özel plan.
- Aracı sürümü – Dataköpek Aracısı sürüm numarası.

Dataköpek Aracısı 'nı yüklemek için App Service 'i ve **uzantıyı yüklemeyi** seçin. En son Dataköpek Aracısı, App Service 'e uzantı olarak yüklenir.

Portal, Dataköpek Aracısı 'nı yüklemek istediğinizi onaylar. Ayrıca, belirli App Service için uygulama ayarları varsayılan anahtarla güncellenir. Dataköpek Aracısı yüklemesi tamamlandıktan sonra App Service yeniden başlatılır.

Veri köpek aracısının yükleme işlemini başlatmak için **Tamam ' ı** seçin. Portal, aracı yüklenene kadar durumu **yükleme** olarak gösterir. Dataköpek aracısı yüklendikten sonra durum yüklendi olarak değişir.

App Service 'te Dataköpek aracılarını kaldırmak için **App Service uzantısına** gidin. App Service 'i seçin ve **uzantıyı kaldırın**

## <a name="reconfigure-single-sign-on"></a>Çoklu oturum açmayı yeniden yapılandırma

Çoklu oturum açmayı yeniden yapılandırmak istiyorsanız sol bölmedeki **Çoklu oturum açma** seçeneğini belirleyin.

Azure Active Directory aracılığıyla çoklu oturum açma kurmak için **Azure Active Directory aracılığıyla çoklu oturum açmayı etkinleştir**' i seçin.

Portal, uygun veri köpek uygulamasını Azure Active Directory alır. Uygulama, tümleştirme ayarlanırken seçtiğiniz kurumsal uygulama adından gelir. Aşağıda gösterildiği gibi Dataköpek uygulaması adını seçin:
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Çoklu oturum açma uygulamasını yeniden yapılandırın." border="true":::
 
## <a name="disable-or-enable-integration"></a>Tümleştirmeyi devre dışı bırakma veya etkinleştirme

Azure 'dan Dataköpek 'a günlük ve ölçüm göndermeyi durdurabilirsiniz. İzleme ölçümleri ve günlükleriyle ilgili olmayan diğer veri köpek Hizmetleri için faturalandırılmaya devam edeceksiniz.

Azure tümleştirmesini Dataköpek ile devre dışı bırakmak için **Genel Bakış ' a** gidin. **Devre dışı bırak** ve **Tamam**' ı seçin.
 
:::image type="content" source="media/manage/disable.png" alt-text="Dataköpek kaynağını devre dışı bırakın." border="true":::

Azure tümleştirmesini Dataköpek ile etkinleştirmek için **Genel Bakış ' a** gidin. **Etkinleştir** ve **Tamam**' ı seçin. **Etkinleştir** ' i seçtiğinizde ölçümler ve Günlükler için önceki yapılandırma alınır. Yapılandırma, hangi Azure kaynaklarının ölçüm ve günlükleri veri köpeye yaydığını belirler. Adımı tamamladıktan sonra, ölçümler ve Günlükler veri köpeye gönderilir.
 
:::image type="content" source="media/manage/enable.png" alt-text="Dataköpek kaynağını etkinleştirin." border="true":::

## <a name="delete-datadog-resource"></a>Dataköpek kaynağını Sil

Sol bölmede **Genel Bakış ' a** gidin ve **Sil**' i seçin. Dataköpek kaynağını silmek istediğinizi onaylayın. **Sil**’i seçin.

:::image type="content" source="media/manage/delete.png" alt-text="Dataköpek kaynağını Sil" border="true":::

Bir veri köpek kuruluşunda yalnızca bir Dataköpek kaynağı eşleniyorsa, Günlükler ve ölçümler artık veri köbir şekilde gönderilmez. Azure Marketi aracılığıyla veri Köpede tüm faturalandırma durduruluyor.

Birden fazla Dataköpek kaynağı, Dataköpek kuruluşunda eşlenmişse, Dataköpek kaynağı silindiğinde yalnızca bu Dataköpek kaynağı için günlük ve ölçüm gönderilmesi durdurulmaktadır. Veri köpek organizasyonu diğer Azure kaynaklarıyla bağlantılı olduğundan faturalandırma, Azure Marketi üzerinden devam etmektedir.

## <a name="getting-support"></a>Destek alma

Azure Dataköpek tümleştirmesiyle ilgili desteğe başvurmak için sol bölmedeki **yeni destek isteği** ' yi seçin. Dataköpek portalının bağlantısını seçin.

:::image type="content" source="media/manage/support-request.png" alt-text="Yeni bir Dataköpek destek isteği oluşturun" border="true":::

## <a name="next-steps"></a>Sonraki adımlar

Sorun giderme konusunda yardım için bkz. [Dataköpek çözümlerinde sorun giderme](troubleshoot.md).