---
title: Windows sanal masaüstü Izleyiciyi Izlemeyi kullan-Azure
description: Windows sanal masaüstü için Azure Izleyicisi 'ni kullanma.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7da35f77dd232e5f523e2bdc3f125c68015ab871
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448194"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Dağıtımınızı izlemek için Windows sanal masaüstü için Azure Izleyicisini kullanın

Windows sanal masaüstü için Azure Izleyici, BT uzmanlarının Windows sanal masaüstü ortamlarını anlamasına yardımcı olan Azure Izleyici çalışma kitaplarında oluşturulmuş bir panodur. Bu konu, Windows sanal masaüstü ortamlarınızı izlemek üzere Windows sanal masaüstü için Azure Izleyici 'yi ayarlama konusunda size kılavuzluk eder.

## <a name="requirements"></a>Gereksinimler

Windows sanal masaüstü için Azure Izleyici 'yi kullanmaya başlamadan önce, aşağıdaki şeyleri ayarlamanız gerekir:

- İzlemeniz gereken tüm Windows sanal masaüstü ortamları, Azure Resource Manager ile uyumlu olan en son Windows sanal masaüstü sürümünü temel almalıdır.
- En az bir yapılandırılmış Log Analytics çalışma alanı. Performans sayaçlarının ve olaylarının yalnızca Windows sanal masaüstü dağıtımınızdaki oturum konaklarından toplanmasını sağlamak için, Windows sanal masaüstü oturum ana bilgisayarları için tasarlanmış bir Log Analytics çalışma alanı kullanın.
- Log Analytics çalışma alanınızdaki şu şeyler için veri toplamayı etkinleştirin:
    - Windows sanal masaüstü ortamınızdan tanılama
    - Windows sanal masaüstü oturumu konaklarınızdaki önerilen performans sayaçları
    - Windows sanal masaüstü oturumu konaklarınızdaki önerilen Windows olay günlükleri

 Bu makalede açıklanan veri kurulum işlemi, Windows sanal masaüstünü izlemeniz gereken tek bir işlemdir. Maliyetleri kaydetmek için Log Analytics çalışma alanınıza veri gönderen diğer tüm öğeleri devre dışı bırakabilirsiniz.

Ortamınız için Windows sanal masaüstü için Azure Izleyicisini izleyen herkesin, aşağıdaki okuma erişim izinlerine de ihtiyacı vardır:

- Windows sanal masaüstü kaynaklarınızı tutan Azure aboneliklerine yönelik okuma erişimi
- Windows sanal masaüstü oturumu konaklarınızı tutan aboneliğin kaynak gruplarına okuma erişimi
- Log Analytics çalışma alanına veya çalışma alanlarına okuma erişimi

>[!NOTE]
> Okuma erişimi yalnızca yöneticilerin verileri görüntülemesini sağlar. Windows sanal masaüstü portalındaki kaynakları yönetmek için farklı izinlere sahip olmaları gerekir.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Windows sanal masaüstü için Azure Izleyicisini açın

Windows sanal masaüstü için Azure Izleyici 'yi aşağıdaki yöntemlerden biriyle açabilirsiniz:

- [Aka.MS/azmonwvdi](https://aka.ms/azmonwvdi)adresine gidin.
- Azure portal **Windows sanal masaüstü** araması yapın ve seçin ve ardından **Öngörüler**' i seçin.
- Azure portal **Azure izleyici** araması yapın ve seçin. **Öngörüler altında** **Öngörüler hub** ' ı seçin ve ardından **Windows sanal masaüstü**' nü seçin.
Sayfayı açtıktan sonra, izlemek istediğiniz ortamın **aboneliğini**, **kaynak grubunu**, **ana bilgisayar havuzunu** ve **zaman aralığını** girin.

>[!NOTE]
>Windows sanal masaüstü Şu anda yalnızca bir abonelik, kaynak grubu ve konak havuzunun aynı anda izlenmesini destekliyor. İzlemek istediğiniz ortamı bulamıyorsanız, bilinen özellik istekleri ve sorunları için [sorun giderme belgelerimize](troubleshoot-azure-monitor.md) bakın.

## <a name="log-analytics-settings"></a>Log Analytics Ayarları

Windows sanal masaüstü için Azure Izleyici 'yi kullanmaya başlamak için en az bir Log Analytics çalışma alanına ihtiyacınız olacaktır. Performans sayaçlarının ve olaylarının yalnızca Windows sanal masaüstü dağıtımınızdaki form oturumu konaklarının toplandığından emin olmak için Windows sanal masaüstü oturum ana bilgisayarları için belirlenmiş bir Log Analytics çalışma alanı kullanın. Zaten ayarlanmış bir çalışma alanınız varsa, [yapılandırma çalışma kitabını kullanarak ayarlamaya](#set-up-using-the-configuration-workbook)devam edin. Bir tane ayarlamak için, bkz. [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Log Analytics için standart veri depolama ücretleri uygulanır. Başlamak için Kullandıkça Öde modelini seçmenizi ve dağıtımınızı ölçeklendirdiğiniz ve daha fazla veri alacak şekilde ayarlamanız önerilir. Daha fazla bilgi için bkz. [Azure izleyici fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Yapılandırma çalışma kitabını kullanarak ayarlama

Windows sanal masaüstü için Azure Izleyici 'yi ilk kez açtığınızda, Windows sanal masaüstü ortamınız için Azure Izleyiciyi ayarlamanız gerekir. Kaynaklarınızı yapılandırmak için:

1. [Aka.MS/azmonwvdi](https://aka.ms/azmonwvdi)adresindeki Windows sanal masaüstü Için Azure izleyici Azure Portal açın ve sonra **yapılandırma çalışma kitabı**' nı seçin.
2. **Abonelik**, **kaynak grubu** ve **konak havuzu** altında yapılandırılacak bir ortam seçin.

Yapılandırma çalışma kitabı, izleme ortamınızı ayarlar ve kurulum işlemini tamamladıktan sonra yapılandırmayı denetlemenizi sağlar. Panodaki öğeler doğru görüntülenmiyorsa veya ürün grubu yeni ayarlar gerektiren güncelleştirmeler yayımladığında, yapılandırmanızı denetlemeniz önemlidir.

### <a name="resource-diagnostic-settings"></a>Kaynak Tanılama ayarları

Windows sanal masaüstü altyapınızda bilgi toplamak için Windows sanal masaüstü konak Havuzlarınızda ve çalışma alanlarınızda birkaç tanılama ayarını etkinleştirmeniz gerekir (Bu, Log Analytics çalışma alanınızın değil, Windows sanal masaüstü çalışma alanım). Konak havuzları, çalışma alanları ve diğer Windows Sanal Masaüstü kaynak nesneleri hakkında daha fazla bilgi edinmek için [ortam kılavuzumuza](environment-setup.md)bakın.

Windows sanal masaüstü tanılama ve desteklenen tanılama tabloları hakkında daha fazla bilgi [Için Windows sanal masaüstü tanılama 'yı Log Analytics gönder](diagnostics-log-analytics.md)' i kullanabilirsiniz.

Yapılandırma çalışma kitabında kaynak tanılama ayarlarınızı yapmak için: 

1. Yapılandırma çalışma kitabındaki **kaynak Tanılama ayarları** sekmesini seçin. 
2. Windows sanal masaüstü tanılamayı göndermek için **Log Analytics çalışma alanı** seçin. 

#### <a name="host-pool-diagnostic-settings"></a>Konak havuzu tanılama ayarları

Yapılandırma çalışma kitabındaki kaynak Tanılama Ayarları bölümünü kullanarak konak havuzu tanılamayı ayarlamak için:

1. **Konak havuzu** altında Windows sanal masaüstü tanılamaları 'nın etkin olup olmadığını denetleyin. Bu değillerse, "seçili konak havuzu için var olan tanılama yapılandırması bulunamadı" ifadesini içeren bir hata iletisi görüntülenir. Aşağıdaki desteklenen tanılama tablolarını etkinleştirmeniz gerekir:

    - Checkpoint
    - Hata
    - Yönetim
    - Bağlantı
    - HostRegistration
    - Yazılım durumu
    
    >[!NOTE]
    > Hata iletisini görmüyorsanız 2 ile 4 arasındaki adımları uygulamanız gerekmez.

2. **Konak havuzunu Yapılandır**' ı seçin.
3. **Dağıt**'ı seçin.
4. Yapılandırma çalışma kitabını yenileyin.

#### <a name="workspace-diagnostic-settings"></a>Çalışma alanı Tanılama ayarları 

Yapılandırma çalışma kitabındaki kaynak Tanılama Ayarları bölümünü kullanarak çalışma alanı tanılamayı ayarlamak için:

 1. **Çalışma alanı** altında Windows sanal masaüstü tanılama 'nın Windows sanal masaüstü çalışma alanı için etkinleştirilip etkinleştirilmediğini denetleyin. Bu değillerse, "seçili çalışma alanı için mevcut tanılama yapılandırması bulunamadı" ifadesini içeren bir hata iletisi görüntülenir. Aşağıdaki desteklenen tanılama tablolarını etkinleştirmeniz gerekir:
 
    - Checkpoint
    - Hata
    - Yönetim
    - Akış
    
    >[!NOTE]
    > Hata iletisini görmüyorsanız, 2-4 adımlarını uygulamanız gerekmez.

2. **Çalışma alanını Yapılandır**' ı seçin.
3. **Dağıt**'ı seçin.
4. Yapılandırma çalışma kitabını yenileyin.

### <a name="session-host-data-settings"></a>Oturum Ana bilgisayar veri ayarları

Windows sanal masaüstü oturumu konaklarınız hakkında bilgi toplamak için konak havuzundaki tüm oturum konaklarına Log Analytics aracısını yüklemeniz, oturum ana bilgisayarlarının bir Log Analytics çalışma alanına gönderdiğinizden emin olmanız ve Log Analytics Aracısı ayarlarınızı performans verilerini ve Windows olay günlüklerini toplayacak şekilde yapılandırmanız gerekir.

Oturum Ana bilgisayar verilerinin gönderileceği Log Analytics çalışma alanının, tanılama verilerini göndereceğiniz bir aynı olması gerekmez. Windows sanal masaüstü ortamınızın dışında Azure oturum Konakları varsa, Windows sanal masaüstü oturumu konakları için belirlenmiş bir Log Analytics çalışma alanı olmasını öneririz. 

Oturum Ana bilgisayar verilerini toplamak istediğiniz Log Analytics çalışma alanını ayarlamak için: 

1. Yapılandırma çalışma kitabındaki **oturum konak verileri ayarları** sekmesini seçin. 
2. Oturum Ana bilgisayar verilerini göndermek istediğiniz **Log Analytics çalışma alanını** seçin. 

#### <a name="session-hosts"></a>Oturum Konakları

Konak havuzundaki tüm oturum konaklarına Log Analytics aracısını yüklemeniz ve bu konaklardan seçtiğiniz Log Analytics çalışma alanınıza veri göndermeniz gerekir. Konak havuzundaki tüm oturum konakları için Log Analytics yapılandırılmamışsa, **oturum ana bilgisayar veri ayarlarının** üst kısmında oturum **Konakları** bölümü görürsünüz. "konak havuzundaki bazı konaklar seçili Log Analytics çalışma alanına veri göndermiyor."

>[!NOTE]
> **Oturum ana bilgisayarları** bölümünü veya hata iletisini görmüyorsanız, tüm oturum Konakları doğru şekilde ayarlanır. [Çalışma alanı performans sayaçları](#workspace-performance-counters)için yönergeleri ayarlamayı ileri atlayın.

Kalan oturum konaklarınızı yapılandırma çalışma kitabını kullanarak ayarlamak için:

1. **Çalışma alanına ana bilgisayar Ekle**' yi seçin. 
2. Yapılandırma çalışma kitabını yenileyin.

>[!NOTE]
>Log Analytics uzantısını yüklemek için konak makinenin çalışıyor olması gerekir. Otomatik dağıtım işe yaramazsa, uzantıyı bir konağa el ile yükleyebilirsiniz. Uzantıyı el ile yüklemeyi öğrenmek için bkz. [Windows için sanal makine uzantısı Log Analytics](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Çalışma alanı performans sayaçları

Oturum konaklarınızdaki performans bilgilerini toplamak ve Log Analytics çalışma alanına göndermek için belirli performans sayaçlarını etkinleştirmeniz gerekir.

Performans sayaçlarınız zaten etkinse ve bunları kaldırmak istiyorsanız, [performans sayaçlarını yapılandırma](../azure-monitor/agents/data-sources-performance-counters.md)konusundaki yönergeleri izleyin. Aynı konumda performans sayaçlarını ekleyebilir ve kaldırabilirsiniz.

Yapılandırma çalışma kitabını kullanarak performans sayaçlarını ayarlamak için: 

1. Yapılandırma çalışma kitabındaki **çalışma alanı performans sayaçları** ' nın altında, Log Analytics çalışma alanına göndermek için etkinleştirilmiş olan sayaçları görmek üzere **yapılandırılan sayaçlar** ' ı işaretleyin. Gerekli tüm sayaçları etkinleştirdiğinizden emin olmak için **eksik sayaçları** denetleyin.
2. Eksik sayaçlarınız varsa, **performans sayaçlarını Yapılandır**' ı seçin.
3. **Yapılandırma Uygula**' yı seçin.
4. Yapılandırma çalışma kitabını yenileyin.
5. **Eksik sayaç** listesini denetleyerek tüm gerekli sayaçların etkinleştirildiğinden emin olun. 

#### <a name="configure-windows-event-logs"></a>Windows olay günlüklerini yapılandırma

Ayrıca, oturum konaklarından hata, uyarı ve bilgi toplamak ve bunları Log Analytics çalışma alanına göndermek için belirli Windows olay günlüklerini etkinleştirmeniz gerekir.

Windows olay günlüklerini zaten etkinleştirdiyseniz ve kaldırmak istiyorsanız, [Windows olay günlüklerini yapılandırma](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)bölümündeki yönergeleri izleyin.  Windows olay günlüklerini aynı konuma ekleyebilir ve kaldırabilirsiniz.

Yapılandırma çalışma kitabını kullanarak Windows olay günlüklerini ayarlamak için:

1. **Windows olay günlüğü yapılandırması** altında, Log Analytics çalışma alanına göndermek için etkinleştirilmiş olan olay günlüklerini görmek Için **yapılandırılan olay günlüklerini** denetleyin. Tüm Windows olay günlüklerini etkinleştirdiğinizden emin olmak için **eksik olay günlüklerini** denetleyin.
2. Windows Olay günlüklerinizi kaçırdıysanız **olayları Yapılandır**' ı seçin.
3. **Dağıt**'ı seçin.
4. Yapılandırma çalışma kitabını yenileyin.
5. **Eksik olay günlükleri** listesini denetleyerek tüm gerekli Windows olay günlüklerinin etkinleştirildiğinden emin olun. 

>[!NOTE]
>Otomatik olay dağıtımı başarısız olursa, eksik Windows olay günlüklerini el ile eklemek için yapılandırma çalışma kitabındaki **Aracı yapılandırmasını aç** ' ı seçin. 

## <a name="optional-configure-alerts"></a>İsteğe bağlı: uyarıları yapılandırma

Windows sanal masaüstü için Azure Izleyici, seçili aboneliğinizde yer alan Azure Izleyici uyarılarını Windows sanal masaüstü verileriniz bağlamında izlemenizi sağlar. Azure Izleyici uyarıları, Azure aboneliklerinizde isteğe bağlı bir özelliktir ve bunları Windows sanal masaüstü için Azure Izleyici 'den ayrı olarak ayarlamanız gerekir. Windows sanal masaüstü olayları, tanılama ve kaynakları üzerinde özel uyarılar ayarlamak için Azure Izleyici uyarıları çerçevesini kullanabilirsiniz. Azure Izleyici uyarıları hakkında daha fazla bilgi edinmek için bkz. [Azure Izleyici uyarıları ile olaylara yanıt verme](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Tanılama ve kullanım verileri

Microsoft, Azure Izleyici hizmetini kullanımınız aracılığıyla kullanım ve performans verilerini otomatik olarak toplar. Microsoft bu verileri hizmetin kalitesini, güvenliğini ve bütünlüğünü geliştirmek için kullanır.

Doğru ve verimli sorun giderme özellikleri sağlamak için, toplanan veriler Portal oturum KIMLIĞI, Azure Active Directory Kullanıcı KIMLIĞI ve olayın gerçekleştiği Portal sekmesinin adını içerir. Microsoft ad, adres veya diğer iletişim bilgileri toplamaz.

Veri toplama ve kullanım hakkında daha fazla bilgi için [Microsoft Online Services gizlilik bildirimi](https://privacy.microsoft.com/privacystatement)' ne bakın.

>[!NOTE]
>Hizmet tarafından toplanan kişisel verilerinizi görüntüleme veya silme hakkında bilgi edinmek için bkz. [GDPR Için Azure veri konu istekleri](/microsoft-365/compliance/gdpr-dsr-azure). GDPR hakkında daha fazla bilgi için, [hizmet güven PORTALıNıN GDPR bölümüne](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Artık Windows sanal masaüstü ortamınız için Azure Izleyicisini yapılandırdığınıza göre, ortamınızı izlemeye başlamanıza yardımcı olabilecek bazı kaynaklar aşağıda verilmiştir:

- Windows sanal masaüstü için Azure Izleyici ile ilgili hüküm ve kavramlar hakkında daha fazla bilgi edinmek için [sözlüğümüze](azure-monitor-glossary.md) göz atın.
- Veri depolama maliyetlerinizi tahmin etmek, ölçmek ve yönetmek için bkz. [Azure izleyici maliyetlerini tahmin](azure-monitor-costs.md)etme.
- Bir sorunla karşılaşırsanız, yardım ve bilinen sorunlar için [sorun giderme kılavuzumuzu](troubleshoot-azure-monitor.md) inceleyin.
