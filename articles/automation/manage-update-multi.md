---
title: Birden fazla Azure sanal makinesi için güncelleştirmeleri yönetme
description: Bu makalede, Azure sanal makineleri için güncelleştirmelerin nasıl yönetileceği açıklanır.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 367a4409c004c98cc4b5ec844aab5b05ec74abcb
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374505"
---
# <a name="manage-updates-for-multiple-machines"></a>Birden çok makine için güncelleştirmeleri yönetme

Windows ve Linux sanal makineleriniz için güncelleştirmeleri ve yamaları yönetmek üzere Güncelleştirme Yönetimi çözümünü kullanabilirsiniz. [Azure Otomasyonu](automation-offering-get-started.md) hesabınızdan şunları yapabilirsiniz:

- Yerleşik sanal makineler
- Kullanılabilir güncelleştirmelerin durumunu değerlendirin
- Gerekli güncelleştirmelerin yüklemesini zamanla
- Güncelleştirmelerin Güncelleştirme Yönetimi etkinleştirildiği tüm sanal makinelere başarıyla uygulandığını doğrulamak için dağıtım sonuçlarını gözden geçirin

## <a name="prerequisites"></a>Önkoşullar

Güncelleştirme Yönetimi kullanmak için şunlar gerekir:

- Desteklenen işletim sistemlerinden birinin yüklü olduğu bir sanal makine veya bilgisayar.

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Güncelleştirme Yönetimi aşağıdaki işletim sistemlerinde desteklenir:

|İşletim sistemi  |Notlar  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Yalnızca güncelleştirme değerlendirmelerini destekler.         |
|Windows Server 2008 R2 SP1 ve üzeri     |Windows PowerShell 4,0 veya üzeri gereklidir. ([WMF 4,0 indirin](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5,1, daha fazla güvenilirlik için önerilir. ([WMF 5,1 indirin](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) ve 7 (x64)      | Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.        |
|Red Hat Enterprise 6 (x86/x64) ve 7 (x64)     | Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.        |
|SUSE Linux Enterprise Server 11 (x86/x64) ve 12 (x64)     | Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.        |
|Ubuntu 14,04 LTS, 16,04 LTS ve 18,04 LTS (x86/x64)      |Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.         |

> [!NOTE]
> Güncelleştirmelerin Ubuntu'daki bakım penceresinin dışında uygulanmasının önüne geçmek için Katılımsız Yükseltme paketini otomatik güncelleştirmeler devre dışı bırakılacak şekilden yeniden yapılandırın. Daha fazla bilgi için bkz. [Ubuntu Server Kılavuzu'ndaki Otomatik Güncelleştirmeler konu başlığı](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.

Bu çözüm, birden çok Azure Log Analytics çalışma alanına raporlamak üzere yapılandırılmış Linux için Log Analytics aracısını desteklemez.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Azure sanal makineleri için Güncelleştirme Yönetimi etkinleştirme

Azure portal Otomasyon hesabınızı açın ve **güncelleştirme yönetimi**' ni seçin.

**Azure VM 'Leri Ekle**' yi seçin.

![Azure VM sekmesi Ekle](./media/manage-update-multi/update-onboard-vm.png)

Eklemek için bir sanal makine seçin.

**Güncelleştirme yönetimi etkinleştir**altında, sanal makineyi eklemek için **Etkinleştir** ' i seçin.

![Güncelleştirme Yönetimini Etkinleştir iletişim kutusu](./media/manage-update-multi/update-enable.png)

Ekleme tamamlandığında, sanal makineniz için Güncelleştirme Yönetimi etkinleştirilir.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Azure olmayan sanal makineler ve bilgisayarlar için Güncelleştirme Yönetimi etkinleştirme

Azure olmayan Windows sanal makineleri ve bilgisayarları için Güncelleştirme Yönetimi etkinleştirmeyi öğrenmek için bkz. [Azure 'Da Windows bilgisayarlarını Azure izleyici hizmetine bağlama](../log-analytics/log-analytics-windows-agent.md).

Azure olmayan Linux sanal makineleri ve bilgisayarları için Güncelleştirme Yönetimi etkinleştirmeyi öğrenmek için bkz. [Linux bilgisayarlarınızı Azure izleyici günlüklerine bağlama](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Otomasyon hesabınıza bağlı bilgisayarları görüntüleme

Makineleriniz için Güncelleştirme Yönetimi etkinleştirdikten sonra **bilgisayarlar**' ı seçerek makine bilgilerini görebilirsiniz. *Makinenizde makine adı*, *uyumluluk durumu*, *ortam*, *işletim sistemi türü*, *kritik güncelleştirmeler ve güvenlik güncelleştirmeleri*, *yüklü diğer güncelleştirmeler*ve *Güncelleştirme Aracısı hazırlığı* hakkında bilgi alabilirsiniz: bilgisayarlar.

  ![Bilgisayarları görüntüle sekmesi](./media/manage-update-multi/update-computers-tab.png)

Güncelleştirme Yönetimi için yakın zamanda etkinleştirilen bilgisayarlar henüz değerlendirilemeyebilir. Bu bilgisayarların uyumluluk durumu durumu **değerlendirilmez**. Uyumluluk durumu için olası değerlerin bir listesi aşağıda verilmiştir:

- **Uyumlu**: eksik kritik veya güvenlik güncelleştirmeleri olmayan bilgisayarlar.

- **Uyumlu değil**: en az bir kritik veya güvenlik güncelleştirmesi eksik olan bilgisayarlar.

- **Değerlendirilmedi**: güncelleştirme değerlendirmesi verileri bilgisayardan beklenen zaman çerçevesi içinde alınmadı. Linux bilgisayarları için beklenen zaman dilimi son saat içinde olur. Windows bilgisayarları için beklenen zaman dilimi son 12 saat içinde olur.

Aracının durumunu görüntülemek için, **GÜNCELLEŞTIRME ARACıSı hazırlığı** sütunundaki bağlantıyı seçin. Bu seçeneğin belirlenmesi **karma çalışanı** bölmesini açar ve karma çalışanın durumunu gösterir. Aşağıdaki görüntüde, uzun bir süre Güncelleştirme Yönetimi bağlı olmayan bir aracının örneği gösterilmektedir:

![Bilgisayarları görüntüle sekmesi](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Güncelleştirme değerlendirmesini görüntüleme

Güncelleştirme Yönetimi etkinleştirildikten sonra **Güncelleştirme yönetimi** bölmesi açılır. **Eksik güncelleştirmeler** sekmesinde eksik güncelleştirmelerin bir listesini görebilirsiniz.

## <a name="collect-data"></a>Veri toplama

Sanal makinelere ve bilgisayarlara yüklenen aracılar güncelleştirmeler hakkında veri toplar. Aracılar, verileri Azure Güncelleştirme Yönetimi 'a gönderir.

### <a name="supported-agents"></a>Desteklenen aracılar

Aşağıdaki tabloda bu çözüm tarafından desteklenen bağlı kaynaklar açıklanmaktadır:

| Bağlı kaynak | Desteklenen | Açıklama |
| --- | --- | --- |
| Windows aracıları |Yes |Güncelleştirme Yönetimi, Windows aracılarından sistem güncelleştirmeleri hakkında bilgi toplar ve gerekli güncelleştirmelerin yüklemesini başlatır. |
| Linux aracıları |Yes |Güncelleştirme Yönetimi, Linux aracılarından sistem güncelleştirmeleri hakkında bilgi toplar ve desteklenen dağıtımlarda gerekli güncelleştirmelerin yüklemesini başlatır. |
| Operations Manager yönetim grubu |Yes |Güncelleştirme Yönetimi bağlı bir yönetim grubundaki aracılardan sistem güncelleştirmeleri hakkında bilgi toplar. |
| Azure Storage hesabı |Hayır |Azure depolama, sistem güncelleştirmeleriyle ilgili bilgiler içermez. |

### <a name="collection-frequency"></a>Toplama sıklığı

Bir bilgisayar güncelleştirme uyumluluğu taramasını tamamladıktan sonra, aracı bilgileri toplu olarak Azure Izleyici günlüklerine iletir. Bir Windows bilgisayarda, uyumluluk taraması varsayılan olarak her 12 saatte bir çalıştırılır.

Tarama zamanlamasının yanı sıra, güncelleştirme uyumluluğuna yönelik tarama, güncelleştirme yüklemesinden önce ve güncelleştirme yüklemesinden sonra, 15 dakika içinde başlatılır.

Bir Linux bilgisayar için, uyumluluk taraması varsayılan olarak saatte bir gerçekleştirilir. MMA Aracısı yeniden başlatılırsa, 15 dakika içinde bir uyumluluk taraması başlatılır.

Panonun yönetilen bilgisayarlardan güncelleştirilmiş verileri görüntülemesi 30 dakika ile 6 saat arasında sürebilir.

## <a name="schedule-an-update-deployment"></a>Güncelleştirme dağıtımı zamanlama

Güncelleştirmeleri yüklemek için, sürüm zamanlamanız ve hizmet pencerenize göre hizalanan bir dağıtım zamanlayın. Dağıtıma hangi güncelleştirme türlerinin dahil edileceğini seçebilirsiniz. Örneğin, kritik güncelleştirmeleri veya güvenlik güncelleştirmelerini dahil edip güncelleştirme paketlerini dışlayabilirsiniz.

Bir veya daha fazla sanal makine için yeni bir güncelleştirme dağıtımı zamanlamak için, **güncelleştirme yönetimi**altında **güncelleştirme dağıtımı zamanla**' yı seçin.

**Yeni güncelleştirme dağıtımı** bölmesinde aşağıdaki bilgileri belirtin:

- **Ad**: güncelleştirme dağıtımını tanımlamak için benzersiz bir ad girin.
- **İşletim sistemi**: **Windows** veya **Linux**seçeneğini belirleyin.
- **Güncelleştirilecek gruplar (önizleme)** : Dağıtımınıza dahil edilecek Azure sanal makinelerinin dinamik grubunu derlemek için bir abonelik, kaynak grupları, konumlar ve etiketler birleşimine göre bir sorgu tanımlayın. Daha fazla bilgi edinmek için bkz. [Dinamik Gruplar](automation-update-management-groups.md)
- **Güncelleştirilecek makineler**: güncelleştirmek istediğiniz makineleri seçmek Için, kaydedilmiş bir arama, içeri aktarılan grup veya makine seçin ' i seçin. **Makineler**'i seçerseniz makinenin hazır olma durumu **GÜNCELLEŞTİRME ARACISI HAZIRLIĞI** sütununda gösterilir. Güncelleştirme dağıtımını zamanlamadan önce makinenin sistem durumunu görebilirsiniz. Azure Izleyici günlüklerinde bilgisayar grupları oluşturmaya yönelik farklı yöntemler hakkında bilgi edinmek için bkz. [Azure izleyici günlüklerinde bilgisayar grupları](../azure-monitor/platform/computer-groups.md)

  ![Yeni güncelleştirme dağıtım bölmesi](./media/manage-update-multi/update-select-computers.png)

- **Güncelleştirme sınıflandırması**: güncelleştirme dağıtımına dahil edilecek yazılım türlerini seçin. Sınıflandırma türlerinin açıklaması için bkz. [güncelleştirme sınıflandırmaları](automation-view-update-assessments.md#update-classifications). Sınıflandırma türleri şunlardır:
  - Kritik güncelleştirmeler
  - Güvenlik güncelleştirmeleri
  - Güncelleştirme paketleri
  - Özellik paketleri
  - Hizmet paketleri
  - Tanım güncelleştirmeleri
  - Araçlar
  - Güncellemeler

- **Dahil edilecek/dışlanacak güncelleştirmeler** - Böylece **Dahil Et / Dışla** sayfası açılır. Dahil edilecek veya dışlanacak güncelleştirmeler ayrı sekmelerdedir. Ekleme hakkında daha fazla bilgi için bkz. [güncelleştirme dağıtımı zamanlama](automation-tutorial-update-management.md#schedule-an-update-deployment).

- **Zamanlama ayarları** - Geçerli saatten 30 dakika sonrası olan varsayılan tarih ve saati kabul edebilirsiniz. Ayrıca, farklı bir saat belirtebilirsiniz.

   Ayrıca, dağıtımın bir kez veya yinelenen bir zamanlamaya göre gerçekleşeceğini belirtebilirsiniz. Yinelenen bir zamanlama ayarlamak için **yinelenme**altında **yineleme**' yi seçin.

   ![Zamanlama Ayarları iletişim kutusu](./media/manage-update-multi/update-set-schedule.png)

- **Ön betikler + Son betikler**: Dağıtımınızdan önce ve sonra çalıştırılacak betikleri seçin. Daha fazla bilgi için bkz. [Ön ve Son betikleri yönetme](pre-post-scripts.md).
- **Bakım penceresi (dakika)** : güncelleştirme dağıtımının gerçekleşmesini istediğiniz süreyi belirtin. Bu ayar, değişikliklerin sizin tanımladığınız hizmet pencereleri içinde gerçekleştirilmesini sağlar.

- **Yeniden başlatma denetimi** -Bu ayar, güncelleştirme dağıtımı için yeniden başlatmalar nasıl işlendiğini belirler.

   |Seçenek|Açıklama|
   |---|---|
   |Gerekirse yeniden Başlat| **(Varsayılan)** Gerekirse, bakım penceresi izin veriyorsa yeniden başlatma başlatılır.|
   |Her zaman yeniden başlat|Bir yeniden başlatma, gerekli olup olmadığına bakılmaksızın başlatılır. |
   |Hiçbir zaman yeniden başlatma|Yeniden başlatma gerekli olursa olsun, yeniden başlatmalar bastırılır.|
   |Yalnızca yeniden başlatma - güncelleştirmeleri yüklemez|Bu seçenek güncelleştirmeleri yüklemeyi yoksayar ve yalnızca bir yeniden başlatma işlemi başlatır.|

Zamanlamayı yapılandırmayı tamamladığınızda, durum panosuna dönmek için **Oluştur** düğmesini seçin. **Zamanlanan** tablo, oluşturduğunuz dağıtım zamanlamasını gösterir.

> [!NOTE]
> Güncelleştirme Yönetimi, birinci taraf güncelleştirmelerini ve indirme öncesi yamalarını dağıtmayı destekler. Bu, düzeltme eki eklenen sistemlerde değişiklik yapılmasını gerektirir, sistemlerinizde bu ayarların nasıl yapılandırılacağını öğrenmek için [ilk taraf ve indirme öncesi destek](automation-configure-windows-update.md#pre-download-updates) ' e bakın.

## <a name="view-results-of-an-update-deployment"></a>Güncelleştirme dağıtımının sonuçlarını görüntüleme

Zamanlanmış dağıtım başladıktan sonra, bu dağıtımın durumunu **Güncelleştirme yönetimi** bölümündeki **Güncelleştirme dağıtımları** sekmesinde görebilirsiniz.

Dağıtım o anda çalışıyorsa, **Sürüyor** durumundadır. Dağıtım başarıyla tamamlandıktan sonra durum **başarılı**olarak değişir.

Dağıtımdaki bir veya daha fazla güncelleştirme başarısız olursa durum **Kısmen başarısız** olur.

![Güncelleştirme dağıtımının durumu](./media/manage-update-multi/update-view-results.png)

Bir güncelleştirme dağıtımının panosunu görmek için tamamlanan dağıtımı seçin.

**Güncelleştirme sonuçları** bölmesi, sanal makine için toplam güncelleştirme sayısını ve dağıtım sonuçlarını gösterir. Sağdaki tabloda her güncelleştirmenin ayrıntılı bir dökümü ve yükleme sonuçları verilmiştir. Yükleme sonuçları aşağıdaki değerlerden biri olabilir:

- **Denenmedi**: tanımlanan bakım penceresine göre yetersiz süre kullanılamadığından güncelleştirme yüklenmedi.
- **Başarılı**: Güncelleştirme başarılı oldu.
- **Başarısız**: Güncelleştirme başarısız oldu.

Dağıtımın oluşturduğu tüm günlük girişlerini görmek için **Tüm günlükler**’i seçin.

Hedef sanal makinede güncelleştirme dağıtımını yöneten runbook 'un iş akışını görmek için çıktı kutucuğunu seçin.

Dağıtımla ilgili her türlü hata hakkında ayrıntılı bilgi için **Hatalar**’ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Günlükler, çıkış ve hatalar dahil Güncelleştirme Yönetimi hakkında daha fazla bilgi edinmek için bkz. [Azure 'da güncelleştirme yönetimi çözümü](../operations-management-suite/oms-solution-update-management.md).

