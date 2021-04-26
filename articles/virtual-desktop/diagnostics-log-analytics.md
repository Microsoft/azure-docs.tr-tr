---
title: Windows sanal masaüstü tanılama günlüğü Analizi-Azure
description: Windows sanal masaüstü tanılama özelliği ile Log Analytics 'i kullanma.
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0775df0d5ca1061bff127ba5491040a90ddb8597
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447021"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Tanılama özelliği için Log Analytics kullanma

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)bakın.

Windows sanal masaüstü, diğer birçok Azure hizmeti gibi izleme ve uyarılar için [Azure izleyicisini](../azure-monitor/overview.md) kullanır. Bu sayede yöneticiler sorunları tek bir arabirimden takip edebilir. Hizmet hem Kullanıcı hem de yönetim eylemleri için etkinlik günlükleri oluşturur. Her etkinlik günlüğü aşağıdaki kategoriler altında yer alıyorsa:

- Yönetim etkinlikleri:
    - API veya PowerShell kullanarak Windows sanal masaüstü nesnelerini değiştirme girişimlerinin başarılı olup olmadığını izleyin. Örneğin, PowerShell kullanarak birisi bir konak havuzunu başarıyla oluşturabilir mi?
- Bakış
    - Kullanıcılar çalışma alanlarına başarıyla abone olabilir mi?
    - Kullanıcılar, uzak masaüstü istemcisinde yayınlanan tüm kaynakları görebilir mi?
- Bağlantılar:
    - Kullanıcılar hizmet üzerinde bağlantı başlatan ve bu hizmetleri tamamlayacak zaman.
- Ana bilgisayar kaydı:
    - Oturum Ana bilgisayarı bağlandıktan sonra hizmete başarıyla kaydedildi mi?
- Hatalar:
    - Kullanıcılar belirli etkinliklerle ilgili herhangi bir sorunla karşılaşıyor mu? Bu özellik, etkinlik verilerini takip eden, bilgiler etkinliklerle birleştirilebilecek şekilde izleyen bir tablo oluşturabilir.
- Noktası
    - Bir etkinliğin ömrü boyunca belirli adımlar. Örneğin, bir oturum sırasında, bir Kullanıcı belirli bir konağa yük dengelemesi yapılmış, sonra Kullanıcı bir bağlantı sırasında oturum açtı ve bu şekilde devam eder.

Tanılama rolü hizmeti Windows sanal masaüstü 'nün bir parçası olduğundan, Windows sanal masaüstüne ulaşmayacak bağlantılar tanılama sonuçlarında gösterilmez. Kullanıcı ağ bağlantısı sorunları yaşanıyorsa Windows sanal masaüstü bağlantısı sorunları oluşabilir.

Azure Izleyici, Windows sanal masaüstü verilerini analiz etmenizi ve sanal makine (VM) performans sayaçlarını aynı araç içinde incelemenizi sağlar. Bu makalede, Windows sanal masaüstü ortamınız için tanılamayı etkinleştirme hakkında daha fazla bilgi bulabilirsiniz.

>[!NOTE]
>Azure 'da VM 'lerinizi izlemeyi öğrenmek için bkz. Azure [sanal makinelerini Azure izleyici Ile izleme](../azure-monitor/vm/monitor-vm-azure.md). Ayrıca, oturum ana bilgisayarında kullanıcı deneyiminizi daha iyi anlamak için [performans sayacı eşiklerini gözden geçirdiğinizden](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) emin olun.

## <a name="before-you-get-started"></a>Başlamadan önce

Log Analytics kullanabilmeniz için önce bir çalışma alanı oluşturmanız gerekir. Bunu yapmak için, aşağıdaki iki makaleden birindeki yönergeleri izleyin:

- Azure portal kullanmayı tercih ediyorsanız, bkz. [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md).
- PowerShell 'i tercih ediyorsanız, bkz. [PowerShell ile Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/powershell-workspace-configuration.md).

Çalışma alanınızı oluşturduktan sonra, aşağıdaki bilgileri almak için [Windows bilgisayarları Azure 'A bağlama](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) bölümündeki yönergeleri izleyin:

- Çalışma alanı KIMLIĞI
- Çalışma alanınızın birincil anahtarı

Bu bilgilere kurulum sürecinde daha sonra ihtiyacınız olacak.

Windows sanal masaüstü ortamınızı izleyen ve koruanlar için veri erişimini etkinleştirmek üzere Azure Izleyici için izin yönetimini gözden geçirdiğinizden emin olun. Daha fazla bilgi için bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](../azure-monitor/roles-permissions-security.md).

## <a name="push-diagnostics-data-to-your-workspace"></a>Tanılama verilerini çalışma alanınıza gönderin

Tanılama verilerini Windows sanal masaüstü nesnelerinden çalışma alanınız için Log Analytics gönderebilirsiniz. Bu özelliği, nesnelerinizi ilk oluşturduğunuzda hemen ayarlayabilirsiniz.

Yeni bir nesne için Log Analytics ayarlamak için:

1. Azure portal oturum açın ve **Windows sanal masaüstü**' ne gidin.

2. Günlüklerini ve olaylarını yakalamak istediğiniz nesneye (bir konak havuzu, uygulama grubu veya çalışma alanı gibi) gidin.

3. Ekranın sol tarafındaki menüden **Tanılama ayarları** ' nı seçin.

4. Ekranın sağ tarafında görünen menüde **Tanılama ayarı Ekle** ' yi seçin.

    Tanılama Ayarları sayfasında gösterilen seçenekler, düzenlemekte olduğunuz nesne türüne göre değişir.

    Örneğin, bir uygulama grubu için tanılamayı etkinleştirirken denetim noktalarını, hataları ve yönetimi yapılandırma seçeneklerini görürsünüz. Çalışma alanları için, bu kategoriler kullanıcıların uygulama listesine ne zaman abone olduğunu izlemek üzere bir akış yapılandırır. Tanılama ayarları hakkında daha fazla bilgi edinmek için bkz. [Azure 'da kaynak günlüklerini ve ölçümleri toplamak için tanılama ayarı oluşturma](../azure-monitor/essentials/diagnostic-settings.md).

     >[!IMPORTANT]
     >İzlemek istediğiniz her bir Azure Resource Manager nesne için tanılamayı etkinleştirmeyi unutmayın. Tanılama etkinleştirildikten sonra veriler etkinlikler için kullanılabilir olacak. Bu işlem, ilk kez kurulduktan sonra birkaç saat sürebilir.

5. Ayarlar yapılandırmanız için bir ad girin ve **Log Analytics gönder**' i seçin. Kullandığınız ad boşluk içermemelidir ve [Azure adlandırma kurallarına](../azure-resource-manager/management/resource-name-rules.md)uymalıdır. Günlüklerin bir parçası olarak, Log Analytics eklenmesini istediğiniz tüm seçenekleri (denetim noktası, hata, yönetim vb.) seçebilirsiniz.

6. **Kaydet**’i seçin.

>[!NOTE]
>Log Analytics, verileri [Event Hubs](../event-hubs/event-hubs-about.md) veya depolama hesabında arşivlemek için veri akışı seçeneği sunar. Bu özellik hakkında daha fazla bilgi edinmek için bkz. [Azure izleme verilerini bir olay hub 'ına akış](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md) ve [Azure Kaynak günlüklerini depolama hesabına arşivleme](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

## <a name="how-to-access-log-analytics"></a>Log Analytics erişme

Azure portal veya Azure Izleyici üzerinde Log Analytics çalışma alanına erişebilirsiniz.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics çalışma alanındaki Log Analytics erişim

1. Azure portalında oturum açın.

2. **Log Analytics çalışma alanı** araması yapın.

3. Hizmetler ' in altında **Log Analytics çalışma alanları**' nı seçin.

4. Listeden, Windows sanal masaüstü nesneniz için yapılandırdığınız çalışma alanını seçin.

5. Çalışma alanınızda bir kez, **Günlükler**' i seçin. **Arama** işleviyle menü listenizi filtreleyebilirsiniz.

### <a name="access-log-analytics-on-azure-monitor"></a>Azure Izleyici 'de Log Analytics erişim

1. Azure portalda oturum açma

2. **İzleme** araması yapın ve seçin.

3. **Günlükleri** seçin.

4. Sorgunuzun kapsamını ayarlamak için günlük sayfasındaki yönergeleri izleyin.

5. Tanılamayı sorgulamaya hazırlanın. Tüm tanılama tablolarının "WVD" öneki vardır.

>[!NOTE]
>Azure Izleyici günlüklerinde depolanan tablolar hakkında daha ayrıntılı bilgi için bkz. [Azure izleyici veri refti](/azure/azure-monitor/reference/). Windows sanal masaüstü ile ilgili tüm tablolar "WVD" olarak etiketlenir.

## <a name="cadence-for-sending-diagnostic-events"></a>Tanılama olaylarını göndermek için temposunda

Tanılama olayları tamamlandığında Log Analytics gönderilir.

Bağlantı etkinlikleri için yalnızca bu ara durumdaki raporları Log Analytics:

- Başlatıldı: bir Kullanıcı, uzak masaüstü istemcisinde bir uygulama veya masaüstü seçer ve bu sunucuya bağlanır.
- Bağlandı: Kullanıcı uygulamanın veya masaüstünün barındırıldığı VM 'ye başarıyla bağlandığında.
- Tamamlandı: Kullanıcı veya sunucu, etkinliğin gerçekleştiği oturumun bağlantısını keser.

## <a name="example-queries"></a>Örnek sorgular

Azure Izleyici Log Analytics Kullanıcı arabirimi aracılığıyla örnek sorgulara erişin:
1. Log Analytics çalışma alanınıza gidin ve ardından **Günlükler**' i seçin. Örnek sorgu Kullanıcı arabirimi otomatik olarak gösterilir.
1. Filtreyi **Kategori** olarak değiştirin.
1. Kullanılabilir sorguları gözden geçirmek için **Windows sanal masaüstü 'nü** seçin.
1. Seçili sorguyu çalıştırmak için **Çalıştır** ' ı seçin.

[Azure izleyici Log Analytics kayıtlı sorgularda](../azure-monitor/logs/example-queries.md)örnek sorgu arabirimi hakkında daha fazla bilgi edinin.

Aşağıdaki sorgu listesi, tek bir kullanıcı için bağlantı bilgilerini veya sorunları incelemenizi sağlar. Bu sorguları [Log Analytics sorgu düzenleyicisinde](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)çalıştırabilirsiniz. Her sorgu için, `userupn` aramak istediğiniz kullanıcının UPN 'si ile değiştirin.


Tek bir kullanıcıya ait tüm bağlantıları bulmak için:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


Bir kullanıcının gün başına kaç kez bağlı olduğunu bulmak için:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

Oturum süresini kullanıcıya göre bulmak için:

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

Belirli bir kullanıcıya ilişkin hataları bulmak için:

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

Diğer kullanıcılar için belirli bir hatanın oluşup oluşmadığını öğrenmek için:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- Kullanıcı tam masaüstü açtığında, oturumdaki uygulama kullanımı WVDCheckpoints tablosunda denetim noktaları olarak izlenmez.
>- WVDConnections tablosundaki ResourcesAlias sütunu, bir kullanıcının tam bir masaüstüne veya yayımlanmış bir uygulamaya bağlanıp bağlanmadığını gösterir. Sütun yalnızca bağlantı sırasında açıldıkları ilk uygulamayı gösterir. Kullanıcının açtığı tüm yayımlanmış uygulamalar WVDCheckpoints içinde izlenir.
>- WVDErrors tablosu, yönetim hatalarını, ana bilgisayar kayıt sorunlarını ve Kullanıcı bir uygulama veya masaüstü bilgisayar listesine abone olduğunda gerçekleşen diğer sorunları gösterir.
>- WVDErrors, yönetici görevleri tarafından çözümlenebilen sorunları belirlemenize yardımcı olur. ServiceError üzerindeki değer her zaman bu tür sorunlar için "false" diyor. ServiceError = "true" ise, sorunu Microsoft 'a ilerletebilirsiniz. Yönettiğiniz hatalar için CorrelationId sağladığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Tanılama özelliğinin sizin için tanımlayabilen yaygın hata senaryolarını gözden geçirmek için bkz. [sorunları tanımlama ve tanılama](diagnostics-role-service.md#common-error-scenarios).