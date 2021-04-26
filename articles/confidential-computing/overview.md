---
title: Azure gizli bilgi Işlem genel bakış
description: Azure gizli (ACC) Bilgi Işlem 'ye Genel Bakış
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: c3ef6a764123f52583f081a3b152651b9bb8b8b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554199"
---
# <a name="confidential-computing-on-azure"></a>Azure 'da gizli bilgi işlem

Azure gizli bilgi işlem, bulutta işlendiği sırada hassas verilerinizi yalıtmanızı sağlar. Birçok sektör, aşağıdakileri yapmak için gizli bilgi işlem kullanarak verilerini korumak için gizli bilgi işlem kullanır:

- Finansal verileri güvenli hale getirme
- Hasta bilgilerini koruma
- Hassas bilgiler üzerinde makine öğrenimi süreçlerini Çalıştır
- Birden çok kaynaktan şifrelenmiş veri kümelerinde algoritmalar gerçekleştirme


## <a name="overview"></a>Genel Bakış
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Bulut verilerinizin güvenliğini güvenli hale getirmenin önemli olduğunu biliyoruz. Kaygılarınızı duyduk. Müşterilerimiz hassas iş yüklerini buluta taşırken yalnızca birkaç soru aşağıda verilmiştir: 

- Nasıl yaparım? Microsoft 'un şifrelenmeyen verilere erişemediğinden emin olun.
- Nasıl yaparım? şirketimin içindeki ayrıcalıklı yöneticilerden güvenlik tehditleri önlemektir mi?
- Üçüncü tarafların hassas müşteri verilerine erişmesini Engelleyebildiğim daha fazla yol var mı?

Microsoft Azure, saldırı yüzeyinizi daha güçlü veri koruma kazanmak için en aza indirmenize yardımcı olur Azure, istemci tarafı şifreleme ve sunucu tarafı şifreleme gibi modellerle [**bekleyen verileri**](../security/fundamentals/encryption-atrest.md) korumak için zaten birçok araç sunmaktadır. Ayrıca, Azure, [**iletim içindeki VERILERI**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) TLS ve https gibi güvenli protokoller aracılığıyla şifrelemek için mekanizmalar sunar. Bu sayfada veri şifrelemenin üçüncü bir tarafı tanıtılmıştır. **Kullanımdaki verilerin** şifrelenmesi.

## <a name="introduction-to-confidential-computing"></a>Gizli bilgi işlem konusuna giriş 

Gizli bilgi işlem, gizli bilgi işlem [Consortium](https://confidentialcomputing.io/) (CCC) tarafından tanımlanan bir sektör terimidir. Bu, gizli bilgi işlem benimsemeyi tanımlamaya ve hızlandırmaya ayrılmış bir temel hizmettir. CCC, donanım tabanlı bir güvenilir yürütme ortamında (t) hesaplamalar gerçekleştirerek kullanılan verilerin korunması gibi gizli bilgi işlem işlemlerini tanımlar.

T, yalnızca yetkili kodun yürütülmesini zorlayan bir ortamdır. T 'deki herhangi bir veri, bu ortamın dışındaki herhangi bir kodla okunamaz veya üzerinde oynanamaz. 

### <a name="lessen-the-need-for-trust"></a>Güven gereksinimini azaltır
Bulutta çalışan iş yükleri için güven gerekir. Bu güveni, uygulamanızın farklı bileşenlerini etkinleştiren çeşitli sağlayıcılara verirsiniz.


**Uygulama yazılım satıcıları**: açık kaynak kullanarak veya şirket içi uygulama yazılımı oluşturarak şirket içinde dağıtımı yaparak yazılıma güvenin.

**Donanım satıcıları**: şirket içi donanım veya şirket içi donanım kullanarak donanıma güvenin. 

**Altyapı sağlayıcıları**: bulut sağlayıcılarına güvenin veya kendi şirket içi veri merkezlerinizi yönetin.


Azure gizli bilgi işlem, işlem bulutu altyapısının çeşitli yönleri genelinde güven gereksinimini azaltarak bulut sağlayıcısına güvenmeyi kolaylaştırır. Azure gizli bilgi işlem, konak işletim sistemi çekirdeği, hiper yönetici, VM Yöneticisi ve konak Yöneticisi için güveni en aza indirir.

### <a name="reducing-the-attack-surface"></a>Saldırı yüzeyini azaltma
Güvenilir bilgi işlem tabanı (TCB), sistemin güvenli bir ortam sağlayan tüm donanım, bellenim ve yazılım bileşenlerine başvurur. TCB 'nin içindeki bileşenler "kritik" olarak değerlendirilir. TCB 'nin içindeki bir bileşen tehlikeye girerse, tüm sistem güvenliği de tehlikeye girebilir. 

Daha düşük bir TCB daha yüksek güvenlik anlamına gelir. Çeşitli güvenlik açıklarına, kötü amaçlı yazılımlara, saldırılara ve kötü amaçlı insanlarla etkilenme riski daha düşüktür. TEEs sunarak bulut iş yükleriniz için TCB 'yi düşürmek üzere Azure gizli bilgi işlem verileri. TEEs, güvenilir çalışma zamanı ikili dosyalarına, koduna ve kitaplıklara TCB 'yi küçültün. Gizli bilgi işlem için Azure altyapı ve hizmetlerini kullandığınızda, tüm Microsoft 'u TCB 'nizden kaldırabilirsiniz.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Bulut tabanlı gizli bilgi işlem için Azure kullanma <a id="cc-on-azure"></a>

Azure gizli bilgi işlem, sanallaştırılmış bir ortamda gizli bilgi işlem özelliklerinden yararlanmanızı sağlar. Artık güvenli donanımın üzerine derlemek için araçlar, yazılımlar ve bulut altyapısını kullanabilirsiniz.  

**Yetkisiz erişimi engelleyin**: bulutta hassas verileri çalıştırın. Azure 'un şimdiye kadar en iyi veri korumasını sağladığı güvenle, bugüne kadar çok değişiklik yapılmamaktadır.

**Mevzuata uyumluluğu**: kişisel bilgileri korumak ve kurumsal IP güvenliğini sağlamak için kuruluşa geçiş yapın ve kamu düzenlemelerine uygun şekilde tam denetim sağlayın.

**Güvenli ve güvenilmeyen işbirliği**: büyük veri analizlerinin ve daha derin Öngörüler 'in kilidini açmak için kuruluşlar genelinde, hatta rakipler genelinde sektör genelinde iş ölçeğinde sorunları ortadan kaldırır.

**Yalıtılmış işleme**: özel veriler üzerinde, görme engelli bir yükümlülüğün sorumluluğun kaldırılması için yeni bir ürün dalgası sunun. Kullanıcı verileri, hizmet sağlayıcısı tarafından bile alınamaz. 

## <a name="get-started"></a>Başlarken
### <a name="azure-compute"></a>Azure İşlem
Azure 'da gizli bilgi işlem IaaS tekliflerinin üzerine uygulamalar oluşturun.
- Sanal makineler (VM): [DCsv2-Series](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS): [gizli kapsayıcıları](confidential-nodes-aks-overview.md) düzenleyin

### <a name="azure-security"></a>Azure Güvenlik 
Doğrulama yöntemleri ve donanımla sınırlı anahtar yönetimi sayesinde iş yüklerinizin güvende olduğundan emin olun. 
- Kanıtlama: [kanıtlama Microsoft Azure (Önizleme)](../attestation/overview.md)
- Anahtar yönetimi: Managed-HSM (Önizleme)

### <a name="develop"></a>Geliştirme
Şifreleme ile uyumlu uygulamalar geliştirmeyi ve gizli INVEN çatı çerçevesini kullanarak gizli algoritmalar dağıtmayı kullanmaya başlayın.
- DCsv2 VM 'lerde çalıştırılacak uygulamaları yazın: [Open-Enclave SDK](https://github.com/openenclave/openenclave)
- ONNX çalışma zamanında gizli ML modelleri: [gizli ınzonde (Beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Sonraki adımlar

DCsv2-Series bir sanal makine dağıtın ve bu makineye OE SDK 'Yı yüklersiniz.

> [!div class="nextstepaction"]
> [Azure Marketi 'nde gizli bilgi işlem VM 'si dağıtma](quick-create-marketplace.md)