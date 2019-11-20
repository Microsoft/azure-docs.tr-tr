---
title: Azure Güvenlik Merkezi 'nde VM 'Ler ve sunucular için tehdit algılama | Microsoft Docs
description: Bu konuda, Azure Güvenlik Merkezi 'nde bulunan VM ve sunucu uyarıları sunulmaktadır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 50d4023b09eb14fcfafe752ca60b8e888acc1fef
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273878"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde VM 'Ler ve sunucular için tehdit algılama

Bu konu, aşağıdaki işletim sistemlerine sahip VM 'Ler ve sunucular için kullanılabilen farklı algılama yöntemi türlerini ve uyarılarını gösterir. Desteklenen sürümlerin listesi için bkz. [Azure Güvenlik Merkezi tarafından desteklenen platformlar ve Özellikler](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Pencerelerin<a name="windows-machines"></a>

Azure Güvenlik Merkezi, Windows tabanlı makinelerinizi izlemek ve korumak için Azure hizmetleri ile tümleşir. Güvenlik Merkezi, tüm bu hizmetlerden gelen uyarıları ve düzeltme önerilerini kullanımı kolay bir biçimde sunar.

### Microsoft Defender ATP<a nanme="windows-atp"></a>

Güvenlik Merkezi, Microsoft Defender Gelişmiş tehdit koruması (ATP) ile tümleştirerek bulut iş yükü koruma platformlarını genişletir. Bu, kapsamlı uç nokta algılama ve yanıt (EDR) özellikleri sağlar.

> [!NOTE]
> Microsoft Defender ATP algılayıcısı, güvenlik merkezi kullanan Windows sunucularında otomatik olarak etkinleştirilir.

Microsoft Defender ATP bir tehdit algıladığında bir uyarı tetikler. Uyarı, güvenlik merkezi panosunda gösterilir. Panodan, Microsoft Defender ATP konsoluna Pivot verebilir ve saldırının kapsamını açığa çıkarmak için ayrıntılı bir araştırma gerçekleştirebilirsiniz. Microsoft Defender ATP hakkında daha fazla bilgi için bkz. [Microsoft Defender ATP hizmetine sunucu](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)ekleme.

### Kilitlenme bilgi döküm Analizi<a nanme="windows-dump"></a>

Yazılım kilitlendiğinde bir kilitlenme dökümü kilitlenme sırasında belleğin bir kısmını yakalar.

Kötü amaçlı yazılımdan koruma veya kötü amaçlı yazılım içeren bir kilitlenme oluşmuş olabilir. Güvenlik ürünleri tarafından algılanmayı önlemek için çeşitli kötü amaçlı yazılım biçimleri, diske yazmayı veya diske yazılmış yazılım bileşenlerini şifrelemeyi önleyen bir dosya daha az saldırı kullanır. Bu tür bir saldırı geleneksel disk tabanlı yaklaşımlar kullanılarak algılanarak tespit edilir.

Ancak, bellek analizini kullanarak bu tür bir saldırıyı tespit edebilirsiniz. Güvenlik Merkezi, kilitlenme dökümündeki belleği çözümleyerek saldırının kullandığı teknikleri algılayabilir. Örneğin, saldırı yazılımda güvenlik açıklarından yararlanmaya, gizli verilere erişebilmeye ve güvenliği aşılmış bir makinede sürekli olarak devam ediyor olabilir. Güvenlik Merkezi, ana bilgisayarlara en az performans etkisi ile çalışır.

> [!div class="mx-tableFixed"]

|Uyarı|Açıklama|
|---|---|
|**Kod ekleme bulundu**|Kod ekleme, çalışmakta olan işlemlere veya iş parçacıklarına yürütülebilir modüllerin eklenmesidir. Bu teknik, kötü amaçlı yazılım tarafından, bulunan ve kaldırılan verileri başarıyla gizlerken, verileri erişmek için kullanılır. <br/>Bu uyarı, eklenen bir modülün kilitlenme bilgi dökümünde mevcut olduğunu belirtir. Güvenlik Merkezi, kötü amaçlı olan ve olmayan eklenen modülleri birbirinden ayırt etmek için eklenen modülün bir şüpheli davranış profiline uygun olup olmadığını denetler.|
|**Şüpheli kod segmenti bulundu**|Bir kod kesiminin, yansıtıcı ekleme ve işlem gizleme gibi standart olmayan yöntemler kullanılarak ayrıldığını gösterir. Uyarı, bildirilen kod segmentinin özellikleri ve davranışları için bağlam sağlamak üzere işlenmiş olan kod kesiminin ek özelliklerini sağlar.|
|**Bulunan shellcode**|Kabuk Kodu, kötü amaçlı yazılım bir yazılım güvenlik açığından yararlandıktan sonra çalıştırılan yüktür.<br/>Bu uyarı, kilitlenme dökümü analizinin kötü amaçlı yükler tarafından yaygın olarak gerçekleştirilen davranışı gösteren yürütülebilir kod algıladığını gösterir. Kötü amaçlı olmayan yazılımlar da bu davranışı gerçekleştirebilse de normal yazılım geliştirme uygulamalarından tipik değildir.|

### Dosya daha az saldırı algılama<a nanme="windows-fileless"></a>

Uç noktalarınızı hedefleyen dosya daha az saldırı yaygındır. Algılamayı önlemek için, dosya daha az saldırı kötü amaçlı yükleri belleğe ekler. Saldırgan yükleri, güvenliği aşılmış işlemlerin belleğinde kalır ve çok çeşitli kötü amaçlı etkinlikler gerçekleştirir.

Dosya daha az saldırı algılaması sayesinde, otomatik bellek oluşturma teknikleri, dosya daha az saldırı araç takımları, teknikler ve davranışları belirler. Bu çözüm, çalışma zamanında makinenizi düzenli olarak tarar ve öngörüleri doğrudan güvenlik açısından kritik işlemlerin belleğinden ayıklar.

Yararlanma, kod ekleme ve kötü amaçlı yükleri yürütme kanıtlarını bulur. Dosya daha az saldırı algılama, uyarı önceliklendirme, bağıntı ve aşağı akış yanıt süresini hızlandırmak için ayrıntılı güvenlik uyarıları üretir. Bu yaklaşım, daha fazla algılama kapsamı sağlayan olay tabanlı EDR çözümlerini tamamlar.

> [!NOTE]
> Azure Güvenlik Merkezi PlayBook 'u indirerek [Windows uyarılarının benzetimini yapabilirsiniz: Güvenlik uyarıları](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

> [!div class="mx-tableFixed"]

|Uyarı|Açıklama|
|---|---|
|**Dosya Less saldırı tekniği algılandı**|Belirtilen işlemin belleği bir dosya Less saldırı araç seti içeriyor: Meterpreter. Fileless saldırı araç takımları genellikle dosya sisteminde bir varlık değildir ve geleneksel virüsten koruma yazılımı tarafından algılamayı zorlaştırır.|

### <a name="further-reading"></a>Daha fazla bilgi

Örnekler ve güvenlik merkezi algılama hakkında daha fazla bilgi için bkz.:

* [Azure Güvenlik Merkezi, siber saldırısı algılamasını nasıl otomatikleştirir](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Güvenlik Merkezi 'nin yönetimsel araçları kullanarak güvenlik açıklarını nasıl algıladığı](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## 'Un<a name="linux-machines"></a>

Güvenlik Merkezi, en yaygın Linux denetim çerçevelerinden biri olan **auditd**'Yi kullanarak Linux makinelerden gelen denetim kayıtlarını toplar. auditd, ana hat çekirdekte yer yaşar. 

### Linux auditd uyarıları ve Microsoft Monitoring Agent (MMA) Tümleştirmesi<a name="linux-auditd"></a>

Auditd sistemi, sistem çağrılarını izlemekten sorumlu olan çekirdek düzeyindeki bir alt sistemden oluşur. Bu, belirli bir kural kümesi tarafından filtreleyerek filtre uygular ve iletileri bir yuvaya yazar. Güvenlik Merkezi, Microsoft Monitoring Agent (MMA) içindeki auditd paketinden işlevleri tümleştirir. Bu tümleştirme, tüm desteklenen Linux dağıtımlarına hiçbir önkoşul olmadan sestd olaylarının toplanmasını mümkün bir şekilde sunar.  

auditd kayıtları toplanır, zenginleştirir ve Linux MMA Aracısı kullanılarak olaylara toplanır. Güvenlik Merkezi, bulut ve şirket içi Linux makinelerinde kötü amaçlı davranışları algılamak için Linux sinyalleri kullanan yeni çözümlemeler ekler. Windows özelliklerine benzer şekilde, bu analizler şüpheli işlemlere, dubmerak eden oturum açma girişimlerini, çekirdek modülünü yüklemeyi ve diğer etkinlikleri kapsar. Bu etkinlikler, bir makinenin saldırıya açık ya da ihlal edilmiş olduğunu gösterebilir.  

Aşağıda, saldırı yaşam döngüsünün farklı aşamalarını kapsayan çeşitli analizler örnekleri verilmiştir.

> [!div class="mx-tableFixed"]

|Uyarı|Açıklama|
|---|---|
|**Beklenmedik bir şekilde SSH yetkili anahtarlar dosyasına erişim için işlem görüldü**|Bilinen kötü amaçlı yazılım kampanyalarına benzer bir yöntemde SSH yetkili anahtarları dosyasına erişildi. Bu erişim, bir saldırganın bir makineye kalıcı erişim kazanmaya çalıştığınızı gösterebilir.|
|**Algılanan Kalıcılık girişimi**|Konak veri analizi, tek kullanıcılı mod için bir başlangıç betiğinin yüklendiğini algıladı. <br/>Meşru bir işlemin bu modda çalıştırılması gerektiğinden nadir olduğundan, bu, bir saldırganın kalıcılığı garanti etmek için her çalışma düzeyine kötü amaçlı bir işlem eklediğine işaret ediyor olabilir.|
|**Zamanlanmış görevlerin işlenmesi algılandı**|Ana bilgisayar veri analizi, zamanlanmış görevler için olası bir düzenleme algıladı. Saldırganlar genellikle kalıcı görevleri riskli hale getikleri makinelere ekler.|
|**Şüpheli dosya zaman damgası değişikliği**|Ana bilgisayar veri analizi şüpheli bir zaman damgası değişikliği algıladı. Saldırganlar, bu yeni bırakılan dosyaların algılanmasını önlemek için genellikle mevcut, meşru dosyalardaki zaman damgalarını yeni araçlara kopyalar.|
|**Sudoers grubuna yeni bir kullanıcı eklendi**|Konak veri analizi, bir kullanıcının sudoers grubuna eklendiğini algıladı ve bu, üyelerinin yüksek ayrıcalıklarla komutları çalıştırmasına olanak sağlar.|
|**DHCP istemcisinde DynoRoot güvenlik açığından büyük olasılıkla faydalanma**|Ana bilgisayar veri analizi, bir alışılmadık komutun, dhclient betiğinin üst işlemle yürütüldüğünü algıladı.|
|**Şüpheli çekirdek modülü algılandı**|Ana bilgisayar veri analizi, bir çekirdek modülü olarak yüklenen paylaşılan bir nesne dosyası algıladı. Bu yasal bir etkinlik veya makinelerinizden birinin tehlikeye girdiği belirtiyi belirten bir göstergesi olabilir.|
|**Dijital para birimi madenciliği ile ilişkili işlem algılandı**|Ana bilgisayar veri analizi, normalde dijital para birimi madenciliği ile ilişkili bir işlemin yürütülmesini algıladı.|
|**Dış IP adresine yönelik olası bağlantı noktası iletme**|Ana bilgisayar veri analizi, bir dış IP adresine bağlantı noktası iletmeyi başlatma işlemini algıladı.|

> [!NOTE]
> Azure Güvenlik Merkezi PlayBook 'u indirerek [Windows uyarılarının benzetimini yapabilirsiniz: Linux algılamaları](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).


Daha fazla bilgi için bkz.  

* [Güvenliği tehlikeye giren Linux makinelerinin saldırı sırasında algılamak için Azure Güvenlik Merkezi 'Nden yararlanın](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Güvenlik Merkezi, Linux 'ta gelişen güvenlik açıklarını algılayabilir](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 