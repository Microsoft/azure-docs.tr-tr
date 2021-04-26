---
title: Önemli avantajlar
description: IoT kavramları için temel Defender hakkında bilgi edinin.
ms.date: 12/13/2020
ms.topic: article
ms.openlocfilehash: ca1e5a4d8554b208f5275fd0e7519f2db3fafc08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784756"
---
# <a name="basic-concepts"></a>Temel kavramlar 

Bu makalede IoT için Azure Defender 'ın temel avantajları açıklanmaktadır.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Hızlı bir şekilde sahip olmayan dağıtım ve pasif izleme

IoT sensörleri için Defender, anahtar yayma (yansıtma) bağlantı noktalarına ve ağ dokunmasına bağlanır ve doğrudan pasif (aracısız) izleme aracılığıyla ICS ağ trafiğini toplamaya başlar. Derin paket incelemesi (DPı), hem seri hem de Ethernet denetim ağı cihazından gelen trafiği dissect için kullanılır. IoT için Defender, veri yoluna yerleştirilmediğinden ve cihaz etkin bir şekilde taramadığı için OT ağları üzerinde sıfır etkiye sahiptir. 

Ayrıntılı Windows cihaz bilgilerinin anlık anlık görüntülerini teslim etmek için, IoT algılayıcısı için Defender, isteğe bağlı bir etkin bileşenle pasif izlemeyi tamamlayacak şekilde yapılandırılabilir. Bu bileşen, Windows cihazlarını cihaz ayrıntılarına göre sorgulamak için güvenli, satıcının onayladığı komutları kullanır.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>ICS protokolleri, cihazları ve uygulamaları için gömülü bilgi

Tek başına DPı, protokol bozuklukilerini belirlemek ve cihazı ayrıntılı bir düzeyde tanımlamak için yeterli değildir. IoT algılayıcısı için Defender, en büyük ve en karmaşık ortamların bazılarını ele alınmaktadır. Tüm endüstriyel kesimlerde 1.300 ' den fazla ağ güncel olarak çözümlenmelidir.

## <a name="analytics-and-self-learning-engines"></a>Analiz ve kendi kendine öğrenme motorları

Altyapılar, imzaları güncelleştirmek veya kuralları tanımlamak için gereksinimi ortadan kaldırmak üzere kendi kendine öğrenimi birleştiren sürekli izleme ve beş analiz altyapısı aracılığıyla güvenlik sorunlarını belirler. Altyapılar, anomali için ağ trafiğini sürekli olarak analiz etmek üzere ICS 'ye özgü davranış analizlerini ve veri bilimi 'ni kullanır. Beş altyapı şunlardır:

- **Protokol ihlali algılama**: ICS protokol belirtimlerini ihlal eden paket yapılarının ve alan değerlerinin kullanımını tanımlar.

- **İlke ihlali algılama**: işlev kodlarının yetkisiz kullanımı, belirli nesnelere erişim veya cihaz yapılandırmasındaki değişiklikler gibi ilke ihlallerini tanımlar.

- **Endüstriyel kötü amaçlı yazılım algılama**: Conficker, Black Energy, Havex, WannaCry ve NotPetya gibi bilinen kötü amaçlı yazılımların varlığını belirten davranışları tanımlar.

- **Anomali algılama**: olağan dışı makineden MAKINEYE ('u M2M) iletişimleri ve davranışları algılar. ICS ağlarını, devlet ve geçişlerin belirleyici dizileri olarak modelleyerek, altyapı endüstriyel sınırlı durum modelleme (IFSM) adlı bir patentli teknik kullanır. Çözüm, genel matematik yaklaşımlardan veya analizinden daha kısa bir öğrenme dönemi gerektirir. Bu, başlangıçta bu değil BT için geliştirilmiştir. Ayrıca, en az yanlış pozitif sonuç vererek anomali değerleri de algılar.

- **İşletimsel olay algılama**: donanım hatasının erken işaretlerini gösterebilen aralıklı bağlantı gibi işlemsel sorunları tanımlar.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Risk ve güvenlik açığı değerlendirmesi için ağ trafiği analizi

Sektörde benzersiz olan, IoT için Defender, tüm ağ ve uç nokta güvenlik açıklarını büyük ölçüde tanımlamak için özel ağ trafiği analizi (NTA) algoritmaları kullanır, örneğin:

- Yetkisiz uzaktan erişim bağlantıları
- Standart olmayan veya belgelenmemiş cihazlar
- Zayıf kimlik doğrulama
- Savunmasız cihazlar (düzeltme eki yüklenmemiş)
- Alt ağlar arasında yetkisiz köprüler
- Zayıf güvenlik duvarı kuralları

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Araştırmalar için veri madenciliği, Forensics ve tehdit Arayıcı

Platform, tüm ilgili boyutlar genelinde geçmiş trafiğin ayrıntılı bir şekilde aranması için sezgisel bir veri araştırma arabirimi sağlar. Örnekler arasında zaman dilimi, IP adresi, MAC adresi ve bağlantı noktaları bulunur. Ayrıca, işlev kodlarına, protokol hizmetlerine ve modüllere göre protokole özgü sorgular da yapabilirsiniz. Daha ayrıntılı analiz için tam uygunlukta PCAPs mevcuttur.

## <a name="sensor-cloud-management-mode"></a>Algılayıcı bulutu yönetim modu

Algılayıcı bulutu yönetim modu, cihazın, uyarının ve sensörin algıladığı diğer bilgilerin nerede görüntülendiğini belirler.

**Buluta bağlı sensörler** için sensör algıladığı bilgiler algılayıcı konsolunda görüntülenir. Uyarı bilgileri bir IoT Hub 'ı aracılığıyla dağıtılır ve Azure Sentinel gibi diğer Azure hizmetleriyle paylaşılabilir.

**Yerel olarak bağlı sensörler** için sensör algıladığı bilgiler algılayıcı konsolunda görüntülenir. Algılayıcı kendisine bağlıysa, algılama bilgileri de şirket içi yönetim konsoluyla paylaşılır.

## <a name="air-gapped-networks"></a>AIR-gapped ağları

AIR-gapped ortamında çalışıyorsanız, IoT için Defender 'daki şirket içi yönetim konsolu, tüm tesislerde önemli IoT ve OT risk göstergelerinin ve uyarılarının gerçek zamanlı bir görünümünü sunar. SOC iş akışlarınızla ve Runbook 'larınızla sıkı bir şekilde tümleştirildiğinde, risk azaltma etkinliklerinin ve siteler arası tehditlerin kolay bir şekilde önceliklendirilmesini sunar.  

IoT için Defender, tüm cihazlarınızın birleştirilmiş bir görünümünü sağlar. Ayrıca, tür (PLC, RTU, DCS ve daha fazlası), üretici, model ve üretici yazılımı değişiklik düzeyi gibi cihazlarla ilgili kritik bilgileri ve uyarı bilgilerini de sağlar.  

IoT için Defender, birden çok dağıtımın etkili yönetimini ve ağın kapsamlı bir görünümünü sunar. IoT için Defender, işlemsel ağ güvenliğinin uyarı işleme ve denetimini iyileştirir.

Şirket içi yönetim konsolu, genel algılayıcı yüklemelerinin etkinliklerini izlemenize ve denetlemenize olanak tanıyan Web tabanlı bir yönetim platformudur. Dağıtılan sensörlerden alınan verileri yönetmeye ek olarak, şirket içi yönetim konsolu çeşitli iş kaynaklarından verileri sorunsuzca tümleştirir: CMDBs, DNS, güvenlik duvarları, Web API 'Leri ve daha fazlası.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Şirket içi yönetim konsolu görüntülenir.":::

Şirket içi yönetim konsoluyla çalışmadan önce sensörlerde kullanılabilen kavramları, özellikleri ve özellikleri öğrenmeniz önerilir.

## <a name="integrations"></a>Tümleştirmeler

İş ortağı sistemleriyle hem cihaz hem de uyarı bilgilerini paylaşarak IoT için Defender 'ın yeteneklerini genişletebilirsiniz. Tümleştirmeler, kuruluşların cihaz görünürlüğünü ve tehdit bilgilerini önemli ölçüde geliştirmek için daha önce yığılıyor güvenlik çözümlerini köprüleyin. Tümleştirmeler, kuruluşların sistem genelinde yanıtları hızlandırmasına yardımcı olur ve riskleri daha hızlı azaltır. 

Tümleştirmeler karmaşıklığı azaltır ve var olan SOC iş akışlarınızla ve güvenlik yığınınıza tümleştirerek bu ve OT ortadan kaldırır. Örnek:

- IBM QRadar, splunk, Arcgözetimi, LogRhythm ve RSA Nettanık gibi Sıems

- ServiceNow ve IBM dayanıklı gibi güvenlik düzenlemesi ve bilet oluşturma sistemleri

- Siark ayrıcalıklı oturum Yöneticisi (PSD) ve BeyondTrust gibi güvenli uzaktan erişim çözümleri

- Aruba ClearPass ve Forescout Counteryasası gibi güvenli ağ erişim denetimi (NAC) sistemleri

- Fortinet ve Check Point gibi güvenlik duvarları

## <a name="complete-protocol-support"></a>Protokol desteğini doldurun

Gömülü protokol desteğine ek olarak, özel ve özel protokoller çalıştıran IoT ve ICS cihazlarının yanı sıra herhangi bir standarda göre farklılık gösteren protokolleri güvenli hale getirebilirsiniz. Geliştiriciler, ufuk açık geliştirme ortamı (ODE) SDK 'sını kullanarak, tanımlı protokollere göre ağ trafiğinin kodunu çözen dissektör eklentileri oluşturabilir. Hizmetler, tüm izleme, uyarı ve raporlama sağlamak için trafiği analiz eder. Ufku kullanarak:

- Yeni sürümlere yükseltmeye gerek olmadan görünürlük ve denetim ' i genişletin.

- Şirket içi bir eklenti olarak sitede geliştirme yaparak özel bilgileri güvenli hale getirin.

- Uyarılar, olaylar ve protokol parametreleri için metni yerelleştirin.

Ayrıca, bilgileri iletmek için özel protokol uyarılarını kullanabilirsiniz:

- Özel bir ufuk eklentisindeki protokolleri ve temel alınan protokolleri temel alan trafik algılamaları hakkında.

- Tüm protokol katmanlarından protokol alanlarının bir birleşimi hakkında. Örneğin, MODBUS çalıştıran bir ortamda, algılayıcı belirli bir IP adresi ve Ethernet hedefindeki bir bellek kaydına yazma komutu algıladığında bir uyarı oluşturmak isteyebilirsiniz. Ya da belirli bir IP adresine herhangi bir erişim gerçekleştirildiğinde uyarı oluşturmak isteyebilirsiniz.

Ufku uyarı kuralı Koşulları karşılandığında uyarılar tetiklenir.

Ayrıca, ufuk özel uyarılarla çalışma, kendi uyarı başlıklarınızı ve iletilerinizi yazmanızı sağlar. Çözümlenen protokol alanları ve değerleri, uyarı iletisi metnine gömülebilir.

Özel, koşul tabanlı uyarı tetikleme ve mesajlaşma, belirli ağ etkinliklerini sabitleme ve güvenlik, BT ve operasyonel ekiplerinizi etkin bir şekilde güncelleştirme konusunda yardımcı olur.


## <a name="high-availability"></a>Yüksek kullanılabilirlik

Şirket içi yönetim konsoluna yüksek kullanılabilirliğe sahip bir gereç yükleyerek, IoT için Defender 'ın esnekliği 'ın dağıtımını artırın. Yüksek kullanılabilirliğe sahip dağıtımlar, yönetilen sensörlerinizin etkin bir şirket içi yönetim konsoluna sürekli olarak rapor aldığından emin olmanızı sağlar.

Bu dağıtım, birincil ve ikincil gereç içeren bir şirket içi yönetim konsolu çifti ile uygulanır.

## <a name="localization"></a>Yerelleştirme

Birçok konsol özelliği, çeşitli dilleri destekler.

## <a name="next-step"></a>Sonraki adım

[IoT için Defender 'ı kullanmaya başlama](getting-started.md)
