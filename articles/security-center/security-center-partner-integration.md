---
title: Azure Güvenlik Merkezi'ndeki tümleşik güvenlik çözümleri | Microsoft Docs
description: Azure Güvenlik Merkezi'nin, Azure kaynaklarınızın genel güvenliğini geliştirmek amacıyla iş ortaklarıyla nasıl tümleştirildiğini öğrenin.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 0a3bc6bcae2f06173cbc334ffe80e2dfa001e407
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309276"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Güvenlik Merkezi'ndeki tümleşik güvenlik çözümleri
Bu belge Azure Güvenlik Merkezi'ne bağlanmış olan güvenlik çözümlerini yönetmenize ve yenilerini eklemenize yardımcı olur.

> [!NOTE]
> Güvenlik çözümlerinin bir alt kümesi 31 Temmuz 2019 tarihinde kullanımdan kaldırılmıştır. Daha fazla bilgi ve diğer hizmetler için bkz. [Güvenlik Merkezi özelliklerini devre dışı bırakma (2019 Temmuz)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Tümleşik Azure güvenlik çözümleri
Güvenlik Merkezi, Azure'daki tümleşik güvenlik çözümlerini etkinleştirmeyi kolaylaştırır. Faydaları şunlardır:

- **Basitleştirilmiş dağıtım**: Güvenlik Merkezi, tümleşik iş ortağı çözümlerinin kolaylaştırılabilir sağlamasını sağlar. Güvenlik Merkezi, kötü amaçlı yazılımdan koruma ve güvenlik açığı değerlendirmesi gibi çözümler için sanal makinelerinizde aracıyı temin edebilir. Güvenlik Duvarı gereçlerinde, Güvenlik Merkezi için gereken ağ yapılandırmasının büyük bir bölümü olabilir.
- **Tümleşik algılamalar**: İş ortağı çözümlerinden gelen güvenlik olayları otomatik olarak toplanır, birleştirilir ve Güvenlik Merkezi uyarıları ve olaylarının bir parçası olarak görüntülenir. Gelişmiş tehdit algılama özellikleri sağlanması amacıyla, bu olaylar diğer kaynaklardan alınan algılamalarla da birleştirilir.
- **Birleşik sistem durumu izleme ve yönetimi**: Müşteriler, tümleşik sistem durumu olaylarını kullanarak bir bakışta tüm iş ortağı çözümlerini izleyebilir. İş ortağı çözümü kullanarak gelişmiş kuruluma kolayca erişim olanağıyla temel yapılandırma seçeneği sunulur.

Şu anda, tümleşik güvenlik çözümleri [Qualys](https://www.qualys.com/public-cloud/#azure) ve [Rapid7](https://www.rapid7.com/products/insightvm/) ve Microsoft Application Gateway Web uygulaması güvenlik duvarı tarafından güvenlik açığı değerlendirmesi içerir.

> [!NOTE]
> Güvenlik Merkezi, çoğu güvenlik satıcısı, gereçlerinde çalışan dış aracıları yasakladığından, iş ortağı sanal gereçlerine Microsoft Monitoring Agent yüklemez.
>
>

## <a name="how-security-solutions-are-integrated"></a>Güvenlik çözümlerinin tümleştirilme şekli
Güvenlik Merkezinden dağıtılan Azure güvenlik çözümleri otomatik olarak bağlanır. Ayrıca, şirket içinde veya diğer bulutlarda çalışan bilgisayarlar da dahil olmak üzere diğer güvenlik veri kaynaklarını da bağlayabilirsiniz.

![İş ortağı çözümlerini tümleştirme](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Tümleşik Azure güvenlik çözümlerini ve diğer veri kaynaklarını yönetme

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) oturum açın.

2. **Microsoft Azure** menüsünde **Güvenlik Merkezi**’ni seçin. **Güvenlik Merkezi - Genel Bakış** açılır.

3. Güvenlik Merkezi menüsünde **Güvenlik çözümleri**’ni seçin.

   ![Güvenlik Merkezine Genel Bakış](./media/security-center-partner-integration/overview.png)

**Güvenlik çözümlerinde**, tümleşik Azure güvenlik çözümlerinin durumunu görebilir ve temel yönetim görevlerini çalıştırabilirsiniz.

### <a name="connected-solutions"></a>Bağlantılı çözümler

**Bağlı çözümler** bölümü, güvenlik merkezi 'ne bağlı olan güvenlik çözümlerini içerir. Ayrıca, her çözümün sistem durumunu gösterir.  

![Bağlantılı çözümler](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

İş ortağı çözümünün durumu şu olabilir:

* Sağlıklı (yeşil)-sistem durumu sorunu yok.
* Sağlıksız (kırmızı)-hemen ilgilenilmesi gereken bir sistem durumu sorunu var.
* Sağlık sorunları (turuncu)-çözüm, sistem durumunu raporlamayı durdurdu.
* Bildirilmedi (gri)-çözüm henüz bir şey bildirmedi ve sistem durumu verisi yok. Bir çözümün durumu, yakın zamanda bağlandıysa ve hala dağıtılarak bildirilmeyebilir.

> [!NOTE]
> Sağlık durumu verileri yoksa, Güvenlik Merkezi, çözümün raporlama yapılıp yapılmayacağını göstermek için alınan son olayın tarih ve saatini gösterir. Bir sistem durumu verisi yoksa ve son 14 gün içinde hiçbir uyarı alınmıyorsa, Güvenlik Merkezi, çözümün sağlıksız olduğunu veya bildirilmediğini belirtir.
>
>

1. Ek bilgi ve seçenekler için **Görünüm** ' ü seçin:

   - **Çözüm konsolu**. Bu çözüm için yönetim deneyimini açar.
   - **VM 'Yi bağlayın**. Uygulamaları bağla sayfasını açar. Burada, kaynakları iş ortağı çözümüne bağlayabilirsiniz.
   - **Çözümü silin**.
   - Öğesini **yapılandırın**.

   ![İş ortağı çözümü ayrıntısı](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Keşfedilen çözümler

Güvenlik Merkezi, Azure 'da çalışan ancak güvenlik merkezi 'ne bağlı olmayan güvenlik çözümlerini otomatik olarak bulur ve **bulunan çözümler** bölümünde çözümleri görüntüler. Bu çözümler [Azure AD kimlik koruması](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)ve iş ortağı çözümleri gibi Azure çözümlerini içerir.

> [!NOTE]
> Güvenlik Merkezi’nin Standart katmanı, bulunan çözümler özelliği için abonelik düzeyinde gereklidir. Fiyatlandırma katmanları hakkında daha fazla bilgi için bkz. [fiyatlandırma](security-center-pricing.md) .
>
>

Güvenlik Merkezi ile tümleştirilecek ve güvenlik uyarıları hakkında bildirim almak için bir çözüm altında **Bağlan** ' ı seçin.

![Keşfedilen çözümler](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>Veri kaynakları ekleyin

**Veri kaynakları ekleyin** bölümünde bağlanabilecek diğer veri kaynakları bulunur. Bu kaynaklardan veri ekleme talimatları için **EKLE**'ye tıklayın.

![Veri kaynakları](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Verileri SıEM 'ye aktarma

Sıems veya diğer izleme araçlarınızı Azure Güvenlik Merkezi olaylarını alacak şekilde yapılandırabilirsiniz.

Azure Güvenlik Merkezi 'ndeki tüm olaylar Azure Izleyici 'nin Azure [etkinlik günlüğüne](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)yayımlanır. Azure Izleyici, verileri daha sonra izleme aracınızdan çektiği bir olay hub 'ına akışa almak için [birleştirilmiş bir işlem hattı](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) kullanır.

Sonraki bölümlerde, verileri bir olay hub 'ına akışa almak üzere nasıl yapılandırabileceğiniz açıklanır. Adımlarda Azure aboneliğinizde zaten Azure Güvenlik Merkezi 'nin yapılandırıldığı varsayılır.

### <a name="high-level-overview"></a>Yüksek düzey genel bakış

![Üst düzey genel bakış](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>SıEM 'ye sunulan Azure Güvenlik verileri nedir?

Bu sürümde, [güvenlik uyarılarını](../security-center/security-center-managing-and-responding-alerts.md) kullanıma sunduk. Yaklaşan sürümlerde, veri kümesini güvenlik önerileri ile Zenginleştireceğiz.

### <a name="how-to-set-up-the-pipeline"></a>İşlem hattını ayarlama

#### <a name="create-an-event-hub"></a>Event Hub'ı oluşturma

Başlamadan önce, tüm izleme verilerinizin hedefi [olan bir Event Hubs ad alanı oluşturun](../event-hubs/event-hubs-create.md) .

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Azure etkinlik günlüğünü Event Hubs akışa almak

Event Hubs için aşağıdaki makaleye [Akış etkinlik günlüğüne](../azure-monitor/platform/activity-logs-stream-event-hubs.md) bakın

#### <a name="install-a-partner-siem-connector"></a>İş ortağı SıEM bağlayıcısını yükler 

İzleme verilerinizi Azure Izleyici ile bir olay hub 'ına yönlendirme, iş ortağı SıEM ve izleme araçlarıyla kolayca tümleştirmenize olanak sağlar.

[Desteklenen Sıems](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub) listesi için aşağıdaki makaleye bakın

### <a name="example-for-querying-data"></a>Veri sorgulama örneği 

Uyarı verilerini çekmek için kullanabileceğiniz bazı splunk sorguları aşağıda verilmiştir:

| **Sorgunun açıklaması** | **Sorgu** |
|----|----|
| Tüm Uyarılar| index = ana Microsoft. güvenlik/konumlar/uyarılar|
| İşlem sayısını adlarıyla özetleme| Index = Main SourceType = "Amal: güvenlik" \| tablo OperationName \| istatistik sayısı/OperationName|
| Uyarı bilgilerini al: Saat, ad, durum, KIMLIK ve abonelik | index = ana Microsoft. Security/Locations/Alerts \| tablosu \_saati, Properties. eventName, eyalet, Properties. operationId, am_subscriptionId |


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Güvenlik Merkezi'nde iş ortağı çözümlerinin nasıl tümleştirileceğini öğrendiniz. Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Güvenlik Merkezi'nde güvenlik durumunu izleme](security-center-monitoring.md). Azure kaynaklarınızı durumunu izleme hakkında bilgi edinin.
* [Azure Güvenlik Merkezi SSS](security-center-faq.md). Güvenlik Merkezi kullanımı ile ilgili sık sorulan soruların yanıtlarını alın.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/). Azure güvenliği ve uyumluluğu ile ilgili blog yazılarını bulun.
