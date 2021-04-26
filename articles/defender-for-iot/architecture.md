---
title: Aracısız çözüm mimarisi nedir?
description: IoT aracısız mimari ve bilgi akışı için Azure Defender hakkında bilgi edinin.
ms.topic: overview
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: bd639ca2656b901bd87fccf36fb4a5d90d3bf918
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383550"
---
# <a name="azure-defender-for-iot-architecture"></a>IoT mimarisi için Azure Defender

Bu makalede, IoT aracısız çözümü için Defender 'ın işlevsel sistem mimarisi açıklanmaktadır. IoT için Azure Defender, ortamınızın ihtiyaçlarını, kuruluşların aracısız çözümünü ve cihaz oluşturucuları için aracı tabanlı çözümü sunan iki özellik kümesi sunar.

## <a name="agentless-solution-for-organizations"></a>Kuruluşlar için aracısız çözüm
### <a name="defender-for-iot-components"></a>IoT bileşenleri için Defender

IoT için Defender hem Azure bulutuna hem de şirket içi bileşenlere bağlanır. Çözüm, birden fazla uzak konum içeren büyük ve coğrafi olarak dağıtılmış ortamlarda ölçeklenebilirlik için tasarlanmıştır. Bu çözüm, ülke, bölge, iş birimi veya bölgeye göre çok katmanlı bir dağıtılmış mimariye izin vermez. 

IoT için Azure Defender aşağıdaki bileşenleri içerir: 

**Buluta bağlı dağıtımlar**

- IoT algılayıcı VM veya gereç için Azure Defender
- Bulut yönetimi için Azure portal ve Azure Sentinel ile tümleştirme
- Yerel site yönetimi için şirket içi yönetim konsolu
- Katıştırılmış bir güvenlik Aracısı (isteğe bağlı)

**AIR-gapped (çevrimdışı) dağıtımlar**

- IoT algılayıcı VM veya gereç için Azure Defender
- Yerel site yönetimi için şirket içi yönetim konsolu

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="IoT için Defender mimarisi.":::

### <a name="azure-defender-for-iot-sensors"></a>IoT sensörleri için Azure Defender

IoT sensörleri için Defender, ağ cihazlarını bulur ve sürekli olarak izler. Algılayıcılar, IoT ve OT cihazlarındaki pasif (aracısız) izleme kullanan ICS ağ trafiğini toplar. 
 
Amaç-IoT ve OT ağları için tasarlanan aracısız teknoloji, IoT 'ye ayrıntılı görünürlük ve ağa bağlı dakikalar içinde risk riski sunuyor. Ağ ve ağ cihazlarında, ağ trafiği analizi (NTA) yaklaşımına bağlı olarak sıfır performans etkisi vardır. 
 
Patentli, IoT ve OT kullanan davranış analizi ve katman 7 derin paket Incelemesi (DPı) uygulanıyor, geleneksel imza tabanlı çözümlerin ötesinde, anormal veya yetkisiz etkinliklere dayalı olarak Gelişmiş IoT ve OT tehditleri (örneğin, çok sayıda kötü amaçlı yazılım) hemen tespit etmenizi sağlar. 
  
IoT sensörleri için Defender, bir SPAN bağlantı noktasına veya ağa DOKUNMASıNA bağlanır ve IoT ve OT ağ trafiği üzerinde hemen DPı gerçekleştirmeye başlar. 
 
Veri toplama, işleme, analiz ve uyarı doğrudan sensörde gerçekleşir. Bu işlem, düşük bant genişliğine sahip konumlar veya yüksek gecikmeli bağlantı olan konumlara ideal hale getirir çünkü yalnızca meta veriler yönetim konsoluna aktarılmaktadır.

Algılayıcı beş analiz algılama altyapısı içerir. Altyapılar, hem gerçek zamanlı hem de önceden kaydedilmiş trafiğin analizine dayalı olarak uyarıları tetikler. Aşağıdaki altyapılar mevcuttur: 

#### <a name="protocol-violation-detection-engine"></a>Protokol ihlali algılama altyapısı
Protokol ihlali algılama altyapısı, ICS protokol belirtimlerini ihlal eden paket yapılarının ve alan değerlerinin kullanımını tanımlar, örneğin: Modbus özel durumu ve kullanılmayan bir işlev kodu uyarılarını başlatma.

#### <a name="policy-violation-detection-engine"></a>İlke ihlali algılama altyapısı
Makine öğrenimini kullanarak, ilke ihlali algılama altyapısı, belirli işlev kodlarının yetkisiz kullanımı, belirli nesnelere erişim veya cihaz yapılandırmasındaki değişiklikler gibi taban çizgisi davranışından herhangi bir sapmanın Kullanıcı sayısını uyarır. Örneğin: DeltaV yazılım sürümü değişti ve yetkisiz PLC programlama uyarıları. Özellikle, ilke ihlali altyapısı, sektör sınırlı durum modelleme (IFSM) adlı bir patentli tekniği kullanarak, ICS ağlarını durum ve geçişlerin belirleyici dizileri olarak modeller. İlke ihlali algılama altyapısı, ICS ağlarının bir temelini oluşturur, böylece platform, ağın bir taban çizgisini oluşturmak için daha kısa bir öğrenme dönemi gerektirir. bu sayede, ağ için başlangıçta OT ağları yerine geliştirmiş olan genel matematik yaklaşımlarına veya analizinden daha kısa bir öğrenme süresi gerekir.

#### <a name="industrial-malware-detection-engine"></a>Endüstriyel kötü amaçlı yazılım algılama altyapısı
Endüstriyel kötü amaçlı yazılım algılama altyapısı, Conficker, Black Energy, Havex, WannaCry, NotPetya ve Triton gibi bilinen kötü amaçlı yazılımların varlığını belirten davranışları belirler. 

#### <a name="anomaly-detection-engine"></a>Anomali algılama altyapısı
Anomali algılama altyapısı, olağandışı makineden makineye ('U M2M) iletişimleri ve davranışları algılar. Platformlar, ICS ağlarını durum ve geçişlerin belirleyici dizileri olarak modelleyerek, genel matematik yaklaşımlarından veya orijinal olarak bu şekilde geliştirilme yerine daha kısa bir öğrenme dönemi gerektirir. Ayrıca, en az yanlış pozitif sonuç vererek anomali değerleri de algılar. Anomali algılama altyapısı uyarıları aşırı sayıda SMB oturum açma girişiminde bulunur ve PLC taraması uyarı algıladı.

#### <a name="operational-incident-detection"></a>İşletimsel olay algılama
İşletimsel olay algılama, donanım hatasının erken işaretlerini gösterebilen aralıklı bağlantı gibi işlemsel sorunları algılar. Örneğin, cihazın bağlantısı kesilmiyor (yanıt vermiyor) ve Siemens S7 stop PLC komutu gönderilir.

### <a name="management-consoles"></a>Yönetim konsolları
Karma ortamlarda IoT için Azure Defender 'ın yönetilmesi iki yönetim portalı aracılığıyla gerçekleştirilir: 
- Algılayıcı konsolu
- Şirket içi yönetim konsolu
- Azure portal

### <a name="sensor-console"></a>Algılayıcı konsolu
Algılayıcı algılamaları, bir ağ eşlemesinde, cihaz envanterinde görüntülenebilecek, araştırılabilecek ve çözümlenebilecekleri, risk değerlendirmesi raporları, veri madenciliği sorguları ve saldırı vektörleri gibi çok sayıda raporda görüntülenmek, araştırılması ve çözümlenmesi gereken algılayıcı konsolunda görüntülenir. Ayrıca, algılayıcı motorları tarafından algılanan tehditleri görüntüleyip işlemek, bilgileri iş ortağı sistemlerine iletmek, kullanıcıları yönetmek ve daha fazlasını yapmak için konsolunu da kullanabilirsiniz.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="IoT algılayıcı konsolu için Defender":::

### <a name="on-premises-management-console"></a>Şirket içi yönetim konsolu
Şirket içi yönetim konsolu, güvenlik işlemleri Merkezi (SOC) işleçlerinin birden çok sensörden tek bir panoda toplanan uyarıları yönetmesine ve analiz etmesine olanak sağlar ve bu da OT ağlarının sistem durumunun genel bir görünümünü sağlar.

Bu mimari, bir SOC düzeyinde ağın kapsamlı bir görünümünü, iyileştirilmiş uyarı işlemeyi ve işlemsel ağ güvenliğinin denetimini sunarak karar verme ve risk yönetiminin flavasız kalmasını sağlar.

Yönetim Konsolu, çok kiracılı, izleme, veri analizi ve merkezi algılayıcı uzaktan kumandasına ek olarak, uzak gereçlerden her biri için ek sistem bakım araçları (uyarı dışlama gibi) ve tamamen özelleştirilmiş raporlama özellikleri sağlar. Bu mimari, bir site düzeyinde, bölge düzeyinde yerel yönetimi ve SOC içinde genel yönetimi destekler.

Yönetim Konsolu, kurtarma için gereken tüm yapılandırma dosyalarının yedeklemelerini düzenli aralıklarla alan bir yedekleme konsolu sağlayan yüksek kullanılabilirliğe sahip yapılandırma için dağıtılabilir. Birincil konsol başarısız olursa, yerel site yönetimi gereçleri, kesintiye uğramadan kullanılabilirliği sürdürmek için yedekleme konsolu ile eşitlenmek üzere otomatik olarak yük devreder.

SOC iş akışlarınızla sıkı bir şekilde tümleştirilmiş ve Kitaplar çalıştırdığından, risk azaltma etkinliklerinin ve siteler arası tehditlerin kolay bir şekilde önceliklendirilmesi sağlanır.

- Bütünsel-cihaz yönetimi, risk ve güvenlik açığı yönetimi ve olay yanıtıyla tehdit izleme için tek bir birleştirilmiş platformun karmaşıklığını azaltın.

- Toplama ve bağıntı: tüm sitelerden toplanan verileri ve Uyarıları görüntüleyin, toplayın ve çözümleyin.

- Tüm algılayıcıları denetle – tüm algılayıcıları tek bir konumdan yapılandırın ve izleyin.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Tüm uyarılarınızı ve bilgilerinizi yönetin.":::

### <a name="azure-portal"></a>Azure portal

Azure 'daki IoT portalı için Defender, size yardımcı olmak için kullanılır:

- Çözüm gereçlerini satın alma

- Yazılım yükleyip güncelleştirme

- Azure 'a sensörlerden katılın

- Tehdit zekası paketlerini güncelleştirme

## <a name="next-steps"></a>Sonraki adımlar

[IoT için Defender SSS](resources-frequently-asked-questions.md)

[Sistem önkoşulları](quickstart-system-prerequisites.md)
