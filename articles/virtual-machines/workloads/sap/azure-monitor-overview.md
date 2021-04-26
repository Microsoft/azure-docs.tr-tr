---
title: SAP çözümlerine genel bakış ve mimari için Azure Izleyici | Microsoft Docs
description: Bu makale, SAP Çözümleri için Azure izleyici hakkında sık sorulan soruların yanıtlarını sağlar
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 45085c910974402a968075a66087a04fb30e8bd9
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576212"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>SAP Çözümleri için Azure izleyici (Önizleme)

## <a name="overview"></a>Genel Bakış

SAP Çözümleri için Azure Izleyici, Azure 'da SAP 'lerini çalıştıran, müşteriler için Azure Native bir izleme ürünüdür. Ürün [Azure sanal makinelerinde hem SAP](./hana-get-started.md) hem de [Azure büyük örneklerde SAP](./hana-overview-architecture.md)ile birlikte kullanılabilir.
SAP Çözümleri için Azure Izleyici ile müşteriler, Azure altyapısından ve veritabanlarından tek bir merkezi konumda telemetri verileri toplayabilir ve daha hızlı sorun giderme için telemetri verilerini görsel olarak ilişkilendirebilir.

SAP Çözümleri için Azure Izleyici, Azure Marketi aracılığıyla sunulur. Basit, sezgisel bir kurulum deneyimi sağlar ve yalnızca birkaç tıklamayla yararlanarak SAP için Azure Izleyici çözümleri için kaynak ( **SAP Monitor kaynağı** olarak bilinir) dağıtımı gerçekleştirir.

Müşteriler, bu bileşene karşılık gelen **sağlayıcıyı** ekleyerek Azure sanal makineleri, yüksek kullanılabilirlik kümesi, SAP HANA VERITABANı, SAP NetWeaver vb. gibi SAP yatay 'ın farklı bileşenlerini izleyebilir.

Desteklenen altyapı:

- Azure Sanal Makinesi
- Azure büyük örnek

Desteklenen veritabanları:
- SAP HANA Veritabanı
- Microsoft SQL Server

SAP Çözümleri için Azure Izleyici, daha fazla izleme özelliği sağlamak üzere Log Analytics ve [çalışma kitapları](../../../azure-monitor/visualize/workbooks-overview.md) gibi mevcut [Azure izleyici](../../../azure-monitor/overview.md) yeteneklerinin gücünü kullanır. Müşteriler, SAP Çözümleri için Azure Izleyici tarafından sunulan varsayılan çalışma kitaplarını düzenleyerek özel bir [görselleştirme](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) oluşturabilir, Azure Log Analytics çalışma alanı kullanarak [özel sorgular](../../../azure-monitor/logs/log-analytics-tutorial.md) yazabilir ve [özel uyarılar](../../../azure-monitor/alerts/tutorial-response.md) oluşturabilir, [Esnek saklama süresinden](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) yararlanır ve izleme verilerini bilet oluşturma sistemiyle birbirine bağlayabilirler.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>SAP Çözümleri için Azure Izleyici hangi verileri toplar?

SAP Çözümleri için Azure Izleyici 'de veri toplama, müşteriler tarafından yapılandırılan sağlayıcılara bağlıdır. Genel Önizleme sırasında aşağıdaki veriler toplanmaktadır.

Yüksek kullanılabilirliğe sahip pacemaker kümesi telemetrisi:
- Düğüm, kaynak ve SBD cihaz durumu
- Pacemaker konum kısıtlamaları
- Çekirdek oyları ve halka durumu
- [Diğerleri](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetri:
- CPU, bellek, disk ve ağ kullanımı
- HANA sistem çoğaltması (HSR)
- HANA yedekleme
- HANA konak durumu
- Dizin sunucusu ve ad sunucusu rolleri

Microsoft SQL Server telemetri:
- CPU, bellek, disk kullanımı
- Ana bilgisayar adı, SQL örneği adı, SAP sistem KIMLIĞI
- Zaman içinde toplu Istekler, derlemeler ve sayfa ömrü erkeklerin
- Zaman içinde en çok pahalı 10 SQL deyimi
- SAP sisteminde ilk 12 en büyük tablo
- SQL Server hata günlüğüne kaydedilen sorunlar
- Zaman içinde işlem ve SQL bekleme Istatistiklerini engelleme

İşletim sistemi telemetrisi (Linux) 
- CPU kullanımı, çatal sayısı, çalışan ve Engellenen süreçler. 
- Bellek kullanımı ve kullanılan, önbelleğe alınmış, ara belleğe alınmış. 
- Kullanım, sayfalama ve takas hızını değiştirme. 
- Dosya sistemleri kullanımı, blok cihaz başına okunan ve yazılan bayt sayısı. 
- Blok cihaz başına okuma/yazma gecikme süresi. 
- Devam eden g/ç sayısı, kalıcı bellek okuma/yazma baytları. 
- Ağ paketleri/Out, ağ baytları/Out 

SAP NetWeaver telemetrisi:

- Dağıtıcı, ICM, ağ geçidi, Ileti sunucusu, sıraya alma sunucusu, ıGS Bekinin örnek işleme kullanılabilirliği dahil olmak üzere SAP sistem ve uygulama sunucusu kullanılabilirliği
- İş süreci kullanım istatistikleri ve eğilimleri
- Kilit istatistiklerini ve eğilimlerini sıraya alma
- Kuyruk kullanım istatistikleri ve eğilimleri

## <a name="data-sharing-with-microsoft"></a>Microsoft ile veri paylaşımı

SAP Çözümleri için Azure Izleyici, Azure müşterilerine yönelik SAP 'imiz için geliştirilmiş destek sağlamak üzere sistem meta verilerini toplar. PII/EUıı toplanmadı.
Müşteriler, açılan listeden *Paylaşım* ' i seçerek SAP Çözümleri Için Azure izleyici kaynağı oluşturma sırasında Microsoft ile veri paylaşımını etkinleştirebilir.
Müşterilerin, Microsoft destek ve mühendislik ekiplerine müşteri ortamı hakkında daha fazla bilgi verdiği ve Azure müşterilerine yönelik görev açısından kritik SAP 'imize yönelik gelişmiş destek sağladığından veri paylaşımını etkinleştirmeleri önemle önerilir.

## <a name="architecture-overview"></a>Mimariye genel bakış

Yüksek düzeyde, aşağıdaki diyagramda SAP çözümlerinin Azure Izleyicisinin SAP HANA veritabanından telemetri nasıl topladığı açıklanmaktadır. Mimari, SAP HANA Azure sanal makinelerinde veya Azure büyük örneklerinde dağıtılmış olup olmadığı konusunda belirsiz değildir.

![SAP Çözümleri için Azure Izleyici mimarisi](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

Mimarinin temel bileşenleri şunlardır:
- Azure portal: müşteriler için başlangıç noktası. Müşteriler, Azure portal dahilinde Market 'e gidebilir ve SAP Çözümleri için Azure Izleyicisini bulabilir
- SAP Çözümleri için Azure Izleyici kaynak: müşterilerin izleme telemetrisini görüntülemesi için bir giriş yeri
- Yönetilen kaynak grubu – SAP Çözümleri için Azure Izleyici Kaynak dağıtımının bir parçası olarak otomatik olarak dağıtılır. Yönetilen kaynak grubu içinde dağıtılan kaynaklar telemetri topluluğunda yardım eder. Dağıtılan anahtar kaynakları ve amaçları şunlardır:
   - Azure sanal makinesi: *TOPLAYıCı VM* olarak da bilinir. Bu bir Standard_B2ms VM 'dir. Bu sanal makinenin ana amacı, *Izleme yükünü* barındırmak için kullanılır. İzleme yükü, kaynak sistemlerden telemetri toplama ve toplanan verileri izleme çerçevesine aktarma mantığını ifade eder. Yukarıdaki diyagramda, izleme yükü SQL bağlantı noktası üzerinden SAP HANA veritabanına bağlanma mantığını içerir.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): Bu kaynak, SAP HANA veritabanı kimlik bilgilerini güvenli bir şekilde tutmak ve [sağlayıcılar](./azure-monitor-providers.md)hakkındaki bilgileri depolamak için dağıtılır.
   - Log Analytics çalışma alanı: Telemetri verilerinin bulunduğu hedef.
      - Görselleştirme, [Azure çalışma kitaplarını](../../../azure-monitor/visualize/workbooks-overview.md)kullanarak Log Analytics telemetri üzerine kurulmuştur. Müşteriler görselleştirmeyi özelleştirebilir. Müşteriler, çalışma kitaplarını veya çalışma kitapları içindeki belirli görselleştirmeleri, en düşük ayrıntı düzeyi olan 30 dakikalık bir şekilde Azure panosuna de sabitleyebilir.
      - Müşteriler, dağıtım sırasında bu seçeneği belirleyerek, mevcut çalışma alanını SAP Monitor kaynağı ile aynı abonelikte kullanabilir.
      - Müşteriler, Log Analytics çalışma alanındaki ham tablolara karşı [sorgu](../../../azure-monitor/logs/log-query-overview.md) çalıştırmak için kusto sorgu DILI (KQL) kullanabilir. *Özel günlüklere* bakın.

> [!Note]
> Müşteriler, yönetilen kaynak grubunda dağıtılan VM 'nin düzeltme eki uygulama ve bakım yapmaktan sorumludur.

> [!Tip]
> Müşteriler, SAP Çözümleri için Azure Izleyici için kaynak olarak aynı Azure aboneliğine dağıtılmışsa, telemetri toplama için mevcut bir Log Analytics çalışma alanını kullanmayı tercih edebilir.

### <a name="architecture-highlights"></a>Mimari vurgulamalar

Mimarinin başlıca vurgulamaları aşağıda verilmiştir:
 - **Çoklu örnek** -MÜŞTERILER, SAP Çözümleri için tek bir Azure izleyici kaynağı olan bir sanal ağ içindeki bırden fazla sap SID 'de, belirli bir bileşen türünün (ÖRNEĞIN, Hana DB, ha kümesi, Microsoft SQL Server, SAP NetWeaver) birden çok örneği için izleyici oluşturabilir.
 - **Multi-Provider** -yukarıdaki mimari diyagramda, örnek olarak SAP HANA sağlayıcısı gösterilmektedir. Benzer şekilde, müşteriler ilgili bileşenleri (örneğin, HANA DB, HA kümesi, Microsoft SQL Server, SAP NetWeaver), bu bileşenlerden veri toplamak için daha fazla sağlayıcı yapılandırabilir.
 - **Açık kaynak** -SAP Için Azure izleyici çözümlerinin kaynak kodu [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions)' da bulunabilir. Müşteriler sağlayıcı koduna başvurabilir ve ürün, katkıda bulunma veya paylaşma hakkında daha fazla bilgi edinebilirsiniz.
 - **Genişletilebilir sorgu çerçevesi** -telemetri verilerini toplamak için SQL sorguları [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json)dilinde yazılır. Daha fazla telemetri verisi toplamak için daha fazla SQL sorgusu kolayca eklenebilir. Müşteriler, bu belgenin sonundaki bağlantı aracılığıyla geri bildirim bırakarak veya hesap ekibine başvurarak, SAP Çözümleri için Azure Izleyici 'ye belirli telemetri verileri eklemek isteyebilir.

## <a name="pricing"></a>Fiyatlandırma
SAP Çözümleri için Azure Izleyici, ücretsiz bir üründür (lisans ücreti yoktur). Müşteriler, yönetilen kaynak grubundaki temel bileşenler için maliyeti ödemekten sorumludur.

## <a name="next-steps"></a>Sonraki adımlar

Sağlayıcılar hakkında bilgi edinin ve SAP Çözümleri için ilk Azure Izleyici kaynağını oluşturun.
 - [Sağlayıcılar](./azure-monitor-providers.md) hakkında daha fazla bilgi edinin
 - [Azure PowerShell ile SAP Çözümleri için Azure Izleyici dağıtma](azure-monitor-sap-quickstart-powershell.md)
 - SAP Çözümleri için Azure Izleyici hakkında sorularınız mı var? [SSS](./azure-monitor-faq.md) bölümüne bakın
