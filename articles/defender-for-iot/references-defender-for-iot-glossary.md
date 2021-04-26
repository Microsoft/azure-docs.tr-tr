---
title: IoT için Defender sözlüğü
description: Bu sözlük, IoT Platformu hüküm ve kavramları için önemli bir Defender hakkında kısa bir açıklama sağlar.
ms.date: 12/09/2020
ms.topic: article
ms.openlocfilehash: 191b94ca37118ed6413149dd6047f94c5ee568cd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786830"
---
# <a name="defender-for-iot-glossary"></a>IoT için Defender sözlüğü

Bu sözlük, IoT Platformu için Azure Defender 'a yönelik önemli hüküm ve kavramların kısa bir açıklamasını sağlar. Sözlükteki ilgili koşullara gitmek için **daha fazla bilgi edinin** bağlantısını seçin. Bu, ürün araçlarını daha hızlı öğrenmenize ve kullanmanıza yardımcı olur.

> [!Note]
> Adında listelenen herhangi bir dönem `(DB)` , aracı tabanlı bir cihaz Oluşturucu terimidir. 

<a name="glossary-a"></a>

## <a name="a"></a>A

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Erişim grubu** | Erişim grubu kuralları oluşturarak büyük kuruluşlar için Kullanıcı erişim gereksinimlerini destekleme.<br /><br />Kurallar, ilgili iş birimleri, bölgeler, siteler ve bölgelerdeki belirli kullanıcı rolleri için bkz. IoT şirket içi yönetim konsolu için Defender 'a görüntüleme ve yapılandırma erişimini denetlemenize olanak tanır.<br /><br />Örneğin, bir Active Directory grubundan Güvenlik analistlerinin Batı Avrupa 'daki verilere erişmesine izin verin, ancak Afrika 'daki verilere erişimi engelleyin. | **[Şirket içi yönetim konsolu](#o)** <br /><br />**[İş birimi](#b)** |
| **Erişim belirteçleri** | IoT REST API için Defender 'a erişmek üzere erişim belirteçleri oluşturun. | **[API](#glossary-a)** |
| **Uyarı olayını Onayla** | Algılanan olay için Defender 'ı IoT 'ye, uyarıyı bir kez gizlemesini söyleyin. Olay tekrar algılanırsa uyarı tekrar tetiklenir. | **[](#glossary-a) <br /> Uyarı <br /> [Uyarı olayını öğrenin](#l) <br /> <br /> [Uyarı olayını sustur](#m)** |
| **Uyarı** | IoT altyapısının bir Defender 'ın yetkili ağ davranışı, ağ bozuklukları veya şüpheli ağ etkinliği ve trafiğinden sapmalarla ilgili olarak tetiklediği bir ileti. | **[İletme kuralı](#f) <br /> <br /> [Dışlama kuralı](#e) <br /> <br /> [Sistem bildirimleri](#s)** |
| **Uyarı açıklaması** | Güvenlik analistlerinin ve yöneticilerinin uyarı iletilerinde yapabelirten yorumlar. Örneğin, bir uyarı yorumu gerçekleştirilecek risk azaltma eylemleri veya olayla ilgili iletişim kurulacak kişilerin adları hakkında yönergeler verebilir.<br /><br />Uyarıları gözden geçiren kullanıcılar, olay durumunu en iyi şekilde yansıtan yorumu veya açıklamaları ya da uyarıyı araştırmak için yapılan adımları seçebilir. | **[Uyarı](#glossary-a)** |
| **Anomali altyapısı** | Olağandışı makineden makineye ('U M2M) iletişimi ve davranışını algılayan IoT altyapısı için bir Defender. Örneğin, altyapı aşırı sayıda SMB oturum açma denemesini algılayabilir. Anomali uyarıları, bu olaylar algılandığında tetiklenir. | **[IoT motorları için Defender](#d)** |
| **API** | Dış sistemlerin, IoT için bulunan ve SSL bağlantıları üzerinden dış REST API kullanarak eylemler gerçekleştirdiği verilere erişmesine izin verir. | **[Erişim belirteçleri](#glossary-a)** |
| **Saldırı vektörü raporu** | Açıktan yararlanma uç noktaları için güvenlik açığı zincirlerinin gerçek zamanlı grafik gösterimi.<br /><br />Raporlar, saldırı sırasındaki risk azaltma etkinliklerinin etkisini değerlendirmenize olanak tanır. Örneğin, bir sistem yükseltmesinin saldırı zincirini bozarak veya alternatif bir saldırı yolunun kalıp kalmadığını değerlendirebilmenizi sağlayabilirsiniz. Bu, düzeltme ve azaltma etkinliklerini önceliklendirir. | **[Risk değerlendirmesi raporu](#r)** |

## <a name="b"></a>B

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **İş birimi** | Belirli sektörlere göre işletmenizin mantıksal organizasyonu.<br /><br />Örneğin, cam fabrikaları ve Plastik fabrikaları içeren küresel bir şirket iki farklı iş birimi olarak yönetilebilir. Belirli iş birimlerine IoT kullanıcıları için Defender erişimini denetleyebilirsiniz. | **[Şirket içi yönetim konsolu](#o) <br /> <br /> [Erişim grubu](#glossary-a) <br /> <br /> [](#s) <br /> Site <br /> [Bölge](#z)** |
| **Taban çizgisi** | Onaylanan ağ trafiği, protokoller, komutlar ve cihazlar. IoT için Defender, ağ temeliyle sapmaları tanımlar. Veri araştırma raporları oluşturarak onaylanan temel trafiği görüntüleyin. | **[Veri araştırma](#d) <br /> <br /> [Öğrenme modu](#l)** |

## <a name="c"></a>C

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **CLı komutları** | IoT yönetici kullanıcıları için Defender için komut satırı arabirimi (CLı) seçenekleri. CLı komutları, IoT konsolları için Defender 'dan erişilemeyen özellikler için kullanılabilir. | - |


## <a name="d"></a>D

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Veri araştırma** | Ağ cihazlarınızla ilgili kapsamlı ve ayrıntılı raporlar oluşturun:<br /><br />- **SOC olay yanıtı**: anında olay yanıtıyla başa çıkmak için gerçek zamanlı raporlar. Örneğin, bir rapor düzeltme eki ihtiyacı olabilecek cihazları listeleyebilir.<br /><br />- **Forensics**: araştırma raporlarının geçmiş verilerini temel alan raporlar.<br /><br />- **BT ağ bütünlüğü**: genel ağ güvenliğinin artırılmasına yardımcı olan raporlar. Örneğin, bir rapor zayıf kimlik doğrulama kimlik bilgileri olan cihazları listeleyebilir.<br /><br />- **görünürlük**: ağınızın tüm temel parametrelerini görüntülemek için tüm sorgu öğelerini kapsayan raporlar.<br /><br />Salt okuma kullanıcılarına görüntülemek için veri araştırma raporlarını kaydedin. | **[Taban çizgisi](#b) <br /> <br /> [Raporlar](#r)** |
| **IoT motorları için Defender** | IoT için Defender 'daki kendi kendine öğrenme Analizi motorları, imzaları güncelleştirme veya kuralları tanımlama ihtiyacını ortadan kaldırır. Altyapılar, kötü amaçlı yazılım, işlemsel sorunlar, protokol ihlalleri ve temel ağ etkinliğinden sapmaları için OT ağ trafiğini sürekli olarak analiz etmek üzere ICS 'ye özgü davranış analizini ve veri bilimi kullanır.<br /><br />Bir altyapı bir sapma algıladığında, bir uyarı tetiklenir. Uyarılar, **Uyarılar** ekranından veya SIEM 'den görüntülenebilir ve yönetilebilir. | **[Uyarı](#glossary-a)** |
| **IoT Platformu için Defender** | IoT algılayıcı ve şirket içi yönetim konsolu için Defender 'da yüklü IoT çözümü için Defender. | **[](#s) <br /> Algılayıcı <br /> [Şirket içi yönetim konsolu](#o)** |
| **Cihaz Haritası** | IoT için Defender tarafından algılanan ağ cihazlarının grafik gösterimi. Her cihazla ilgili cihazlar ve bilgiler arasındaki bağlantıları gösterir. Haritayı kullanarak şunları yapın:<br /><br />-Kritik cihaz bilgilerini alın ve denetleyin.<br /><br />-Ağ dilimlerini analiz edin.<br /><br />-Cihaz ayrıntılarını ve özetlerini dışarı aktarın. | **[Takip eden katman grubu](#p)** |
| **Cihaz envanteri-algılayıcı** | Cihaz envanteri, bir IoT için Defender tarafından algılanan kapsamlı bir cihaz öznitelikleri yelpazesi görüntüler. Seçenekler kullanılabilir:<br /><br />-Filtre görüntülenmiş bilgiler.<br /><br />-Bu bilgileri bir CSV dosyasına dışarı aktarın.<br /><br />-Windows kayıt defteri ayrıntılarını içeri aktarın. | **[Grup](#g)** <br /><br />**[Cihaz envanteri-şirket içi yönetim konsolu](#d)** |
| **Cihaz envanteri-şirket içi yönetim konsolu** | Bağlı sensörlerden cihaz bilgileri cihaz envanterindeki şirket içi yönetim konsolundan görüntülenebilir. Bu, şirket içi yönetim konsolu kullanıcılarına tüm ağ bilgilerinin kapsamlı bir görünümünü sağlar. | **[Cihaz envanteri-algılayıcı](#d) <br /> <br /> [Cihaz envanteri-veri tümleştirici](#d)** |
| **Cihaz envanteri-veri tümleştirici** | Şirket içi yönetim konsolunun veri tümleştirme özellikleri, cihaz envanterindeki verileri diğer kurumsal kaynaklardaki bilgilerle geliştirmenize olanak sağlar. Örnek kaynaklar CMDBs, DNS, güvenlik duvarları ve Web API 'larıdır. | **[Cihaz envanteri-şirket içi yönetim konsolu](#d)** |
| **Cihaz** ikikesi `(DB)` | Cihaz TWINS, meta veriler, konfigürasyonlar ve koşullar dahil olmak üzere cihaz durum bilgilerini depolayan JSON belgelerdir. | [Modül Ikizi](#m) <br /> <br />[Defender-IoT-Micro-Agent ikizi](#s) |

## <a name="e"></a>E

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Kurumsal Görünüm** | IoT sensörlerine yönelik Savunların yüklendiği iş birimleri, siteler ve bölgeler sunan genel harita. Kötü amaçlı uyarıların, işlemsel uyarıların ve daha fazlalığının coğrafi konumlarını görüntüleyin. | **[İş birimi](#b) <br /> <br /> [](#s) <br /> Site <br /> [Bölge](#z)** |
| **Olay zaman çizelgesi** | Ağınızda algılanan ve aşağıdakiler dahil olmak üzere bir etkinlik zaman çizelgesi:<br /><br />-Uyarılar tetiklendi.<br /><br />-Ağ olayları (bilgilendirici).<br /><br />-Oturum açma, Kullanıcı silme ve Kullanıcı oluşturma gibi kullanıcı işlemleri ve sessiz, öğrenme ve bildirim gibi uyarı yönetimi işlemleri. Algılayıcı konsollarında kullanılabilir. | - |
| **Dışlama kuralı** | Defender 'ın IoT için, zaman dilimine, cihaz adresine ve uyarı adına veya belirli bir sensöre göre uyarı tetikleyicilerini yoksaymasını söyleyin.<br /><br />Örneğin, belirli bir algılayıcı tarafından izlenen tüm OT cihazlarının sabah 6:30 ve 10:15 arasında bir bakım prosedürü üzerinden gidebileceğini biliyorsanız, bu sensörin önceden tanımlanmış dönemde hiç uyarı gönderemeyeceğini belirten bir dışlama kuralı ayarlayabilirsiniz. | **[](#glossary-a) <br /> Uyarı <br /> [Uyarı olayını sustur](#m)** |

## <a name="f"></a>F

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **İletme kuralı** | İletme kuralları, IoT için Defender 'a, iş ortağı satıcılarına veya sistemlerine uyarı bilgileri göndermek için yönlendirir.<br /><br />Örneğin, bir splunk sunucusuna veya Syslog sunucusuna uyarı bilgileri gönderin. | **[Uyarı](#glossary-a)** |

## <a name="g"></a>G

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Grup** | Belirli bir alt ağda bulunan programlama etkinliklerini veya cihazlarını çalıştıran cihazlar gibi belirli öznitelikleri içeren önceden tanımlanmış veya özel cihaz grupları. Cihazları görüntülemenize ve IoT için Defender 'da cihazları çözümlemenize yardımcı olacak grupları kullanın.<br /><br />Gruplar, cihaz haritalarından ve cihaz envanterinden görüntülenebilir ve oluşturulabilir. | **[Cihaz Haritası](#d) <br /> <br /> [Cihaz envanteri](#d)** |

## <a name="h"></a>H

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Ufku açık geliştirme ortamı** | Herhangi bir standarda göre farklılık gösteren özel ve özel protokoller veya protokoller çalıştıran güvenli IoT ve ICS cihazları. Tanımlı protokollere göre ağ trafiğinin kodunu çözen dissektör eklentileri geliştirmek için ufuk açık geliştirme ortamı (ODE) SDK 'sını kullanın. IoT Hizmetleri için savunma, tüm izleme, uyarı ve raporlama sağlamak üzere trafiği analiz eder.<br /><br />Ufku kullanarak:<br /><br />- IoT platform sürümleri için Defender 'ı yükseltmeye gerek olmadan görünürlük ve denetim ' **i genişletin** .<br /><br />- Şirket içi bir eklenti olarak sitede geliştirme yaparak özel bilgileri **güvenli hale** getirin.<br /><br />- Uyarılar, olaylar ve protokol parametreleri için metni **yerelleştirin** .<br /><br />Ayrıntılar için Müşteri başarı temsilcinizle iletişime geçin. | **[Protokol desteği](#p) <br /> <br /> [Yerelleştirme](#l)** |
| **Ufuk özel uyarısı** | Herhangi bir protokol için özel uyarılar tetikleyerek (ufuk çerçevesi trafik kesilmeleri tabanlı), kuruluşunuzda uyarı yönetimini geliştirin.<br /><br />Bu uyarılar, bilgileri iletmek için kullanılabilir:<br /><br />-Özel bir ufuk eklentisindeki protokolleri ve temel alınan protokolleri temel alan trafik algılamaları hakkında.<br /><br />-Tüm protokol katmanlarından protokol alanları birleşimi hakkında. | **[Protokol desteği](#p)** |

## <a name="i"></a>I

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **IoT Hub’ı** `(DB)` | Bulutta barındırılan yönetilen hizmet, IoT uygulamanız ile yönettiği cihazlar arasında çift yönlü iletişim için merkezi bir ileti hub 'ı olarak görev yapar.  |   |
| **Tümleştirmeler** | Cihaz bilgilerini iş ortağı sistemleriyle paylaşarak IoT özellikleri için Defender 'ı genişletin. Kuruluşlar, sistem genelinde yanıtları hızlandırmak ve daha hızlı risk hafifletmek için daha önce yığılıyor güvenlik, NAC, olay yönetimi ve cihaz yönetim çözümlerini köprüleyebilirler. | **[İletme kuralı](#f)** |
| **İç alt ağ** | IoT için Defender tarafından tanımlanan alt ağ yapılandırması. Ortak aralıkları iç aralıklar olarak kullanan ortamlar gibi bazı durumlarda, Defender 'ın IoT için tüm alt ağları iç alt ağ olarak çözmelerini söyleyebilirsiniz. Alt ağlar haritada ve IoT raporlarında çeşitli Defender 'da görüntülenir. | **[Alt ağlar](#s)** |

## <a name="l"></a>L

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Uyarı olayını öğrenin** | Bir uyarı olayında algılanan trafiği yetkilendirmek için Defender for IoT 'e bildirin. | **[](#glossary-a) <br /> Uyarı <br /> [Uyarı olayını Onayla](#glossary-a) <br /> <br /> [Uyarı olayını sustur](#m)** |
| **Öğrenme modu** | IoT için Defender, ağ etkinliğinizi öğrendiğinde kullanılan mod. Bu etkinlik, ağ temelinizi olur. IoT için Defender, yüklemeden sonra önceden tanımlanmış bir süre için modda kalır. Bu dönemden sonra öğrenilmiş etkinlikten sapmadan oluşan etkinlik, Defender 'ı IoT uyarıları için tetikler. | **[Akıllı BT öğrenimi](#s) <br /> <br /> [Taban çizgisi](#b)** |
| **Yerelleştirme** | Ufuk tarafından geliştirilen dissektör eklentileri için uyarıları, olayları ve protokol parametrelerini yerelleştirin. | **[Ufku açık geliştirme ortamı](#h)** |

## <a name="m"></a>M


| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Mikro aracı**`(DB)` | IoT cihazları için güvenlik sonrası ve tehdit algılama dahil olmak üzere derinlemesine güvenlik özellikleri sağlar. | |
| **Modül ikizi**`(DB)` | Modül ikizleri; meta veriler, yapılandırmalar ve koşullar gibi modül durumu bilgilerini depolayan JSON belgelerdir. | [Cihaz çifti](#d) <br /> <br />[Defender-IoT-Micro-Agent ikizi](#s) |
| **Uyarı olayını sustur** | Aynı cihazlarla ve karşılaştırılabilir trafikle etkinlikleri sürekli olarak yoksayması için Defender 'a söyleyin. | **[](#glossary-a) <br /> Uyarı <br /> [Dışlama kuralı](#e) <br /> <br /> [Uyarı olayını Onayla](#glossary-a) <br /> <br /> [Uyarı olayını öğrenin](#l)** |

## <a name="n"></a>N

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Bildirimler** | Ağ değişiklikleri veya çözümlenmemiş cihaz özellikleri hakkında bilgi. Cihaz ve ağ bilgilerini, algılanan yeni verilerle güncelleştirmek için seçenekler kullanılabilir. Bildirimlere yanıt vermek cihaz envanterini, eşlemeyi ve çeşitli raporları zenginleştirir. Algılayıcı konsollarında kullanılabilir. | **[](#glossary-a) <br /> Uyarı <br /> [Sistem bildirimleri](#s)** |

## <a name="o"></a>O

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Şirket içi yönetim konsolu** | Şirket içi yönetim konsolu, kuruluşunuzda IoT algılayıcı dağıtımlarını algılayan cihazların ve tehditlerin merkezi bir görünümünü ve yönetimini sağlar. | **[IoT Platformu](#d) <br /> <br /> için Defender [Algılayıcı](#s)** |
| **İşlemsel uyarı** | Ağ bağlantısının kesilmesi şüpheli bir cihaz gibi işlemsel ağ sorunlarıyla ilgilenen uyarılar. | **[](#glossary-a) <br /> Uyarı <br /> [Güvenlik uyarısı](#s)** |

## <a name="p"></a>P

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Takip tarihi katmanı** | Haritadaki tipik bir ICS 'nin ana bileşenlerinin bağlantılarını ve bağımlılıklarını gösterir. |  |
| **Protokol desteği** | Yerleşik protokol desteğine ek olarak, özel ve özel protokoller çalıştıran IoT ve ICS cihazlarının yanı sıra, ufuk açık geliştirme ortamı SDK 'sını kullanarak herhangi bir standarda göre farklılık gösteren protokolleri güvenli hale getirebilirsiniz. | **[Ufku açık geliştirme ortamı](#h)** |

## <a name="r"></a>R

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Bölge** | Küresel bir kuruluşun mantıksal bölümü coğrafi bölgelere. Örnekler şunlardır Kuzey Amerika, Batı Avrupa ve Doğu Avrupa.<br /><br />Kuzey Amerika çeşitli iş birimlerinden fabrikaları olabilir. | **[Erişim grubu](#glossary-a) <br /> <br /> [İş birimi](#b) <br /> <br /> [Şirket içi yönetim konsolu](#o) <br /> <br /> [](#s) <br /> Site <br /> [Bölge](#z)** |
| **Raporlar** | Raporlar, veri madenciliği sorgu sonuçları tarafından oluşturulan bilgileri yansıtır. Bu, **raporlar** görünümünde kullanılabilen varsayılan veri araştırma sonuçlarını içerir. Yöneticiler ve Güvenlik analistleri Ayrıca özel veri araştırma sorguları oluşturabilir ve bunları rapor olarak kaydedebilir. Bu raporlar, salt okuma kullanıcıları için de kullanılabilir. | **[Veri araştırma](#d)** |
| **Risk değerlendirmesi raporu** | Risk değerlendirmesi raporlaması, her ağ aygıtı için bir güvenlik puanı oluşturmanıza olanak sağlar ve bu da genel bir ağ güvenlik puanına sahip olursunuz. Genel puan yüzde 100 güvenlik yüzdesini temsil eder. Rapor, geçerli güvenlik puanınızı iyileştirmenize yardımcı olacak risk azaltma önerileri sağlar. | - |

## <a name="s"></a>S

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Güvenlik Uyarısı** | Aşırı SMB oturum açma girişimleri veya kötü amaçlı yazılım algılamaları gibi güvenlik sorunlarıyla ilgilenen uyarılar. | **[](#glossary-a) <br /> Uyarı <br /> [İşlemsel uyarı](#o)** |
| **Defender-IoT-Micro-Agent ikizi**`(DB)` | Defender-IoT-Micro-Agent ikizi, çözümünüzdeki her bir cihaz için cihaz güvenliği ile ilgili tüm bilgileri tutar. | [Cihaz çifti](#d) <br /> <br />[Modül Ikizi](#m)  |
| **Seçmeli yoklama** | IoT için Defender, BT ve OT trafiğini büyük ölçüde inceler ve cihazlar, öznitelikleri, davranışları ve daha fazlası için ilgili bilgileri algılar. Bazı durumlarda, bazı bilgiler pasif ağ analizlerinde görünmeyebilir.<br /><br />Bu durumda, daha önce erişilemeyen cihazlarda önemli bilgileri keşfedebilmeniz için, IoT için Defender 'daki güvenli ve ayrıntılı araştırma araçlarını kullanabilirsiniz. | - |
| **Algılayıcısı** | IoT Platformu için Defender 'ın yüklü olduğu fiziksel veya sanal makine. | **[Şirket içi yönetim konsolu](#o)** |
| **Site** | Bir fabrika ya da başka bir varlığa ait bir konum. Site, bir algılayıcısı veya bir algılayıcı 'un yüklü olduğu birkaç bölgeyi içermelidir. | **[Bölge](#z)** |
| **Site yönetimi** | Kurumsal algılayıcıları yönetmenize olanak sağlayan şirket içi yönetim konsolu seçeneği. | - |
| **Akıllı BT öğrenimi** | Öğrenme süresi tamamlandıktan ve öğrenme modu devre dışı bırakıldıktan sonra IoT için Defender, DNS ve HTTP istekleri gibi normal BT etkinliğinin sonucu olan olağandışı yüksek düzeyde temel bir değişikliği algılayabilir. Bu trafik, gereksiz ilke ihlali uyarılarını ve sistem bildirimlerini tetikleyebilir. Bu uyarıları ve bildirimleri azaltmak için akıllı BT öğrenimini etkinleştirebilirsiniz. | **[Öğrenme modu](#l) <br /> <br /> [Taban çizgisi](#b)** |
| **Alt ağlar** | OT cihazlarda odaklanmayı etkinleştirmek için, BT cihazları otomatik olarak cihaz eşlemesindeki alt ağ tarafından toplanır. Her alt ağ, bir BT alt ağına ve geri odaklanmak için etkileşimli daraltma veya genişletme özelliği de dahil olmak üzere haritada tek bir varlık olarak sunulur. | **[Cihaz Haritası](#d)** |
| **Sistem bildirimleri** | Şirket içi yönetim konsolundan ilgili bildirimler:<br /><br />-Algılayıcı bağlantı durumu.<br /><br />-Uzak yedekleme sorunları. | **[](#n) <br /> Bildirimler <br /> [Uyarı](#glossary-a)** |

## <a name="z"></a>Z

| Süre | Açıklama | Daha fazla bilgi edinin |
|--|--|--|
| **Bölge** | Bir algılayıcı veya algılayıcıların yüklendiği bir site içindeki alan. | **[](#s) <br /> Site <br /> [İş birimi](#b) <br /> <br /> [Bölge](#r)** |
