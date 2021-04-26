---
title: Yaygın Azure gizli bilgi işlem senaryoları ve kullanım örnekleri
description: Senaryolarınızın gizli bilgi işlem kullanımını nasıl kullanacağınızı anlayın.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 9/22/2020
ms.author: jencook
ms.openlocfilehash: 47938f3a44c3a47f8b444b59d7e2f0867a274f33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566626"
---
# <a name="common-scenarios-for-azure-confidential-computing"></a>Azure gizli bilgi işlem için genel senaryolar

Bu makalede, Azure gizli bilgi işlem için birkaç yaygın senaryoya genel bakış sunulmaktadır. Bu makaledeki öneriler, uygulamanızın gizli bilgi işlem hizmetleri ve çerçeveleri kullanarak geliştirilmesi sırasında başlangıç noktası olarak hizmet verir. 

Bu makaleyi okuduktan sonra aşağıdaki soruları cevaplayabilirsiniz:

- Azure gizli bilgi işlem için bazı senaryolar nelerdir?-
- Çok taraflı senaryolar için Azure gizli bilgi işlem kullanmanın avantajları nelerdir?
- Blok zinciri ağında Azure gizli bilgi işlem kullanmanın avantajları nelerdir?


## <a name="secure-multi-party-computation"></a>Güvenli çok taraflı hesaplama
Azure gizli bilgi işlem, giriş verilerini diğer taraflara açığa çıkarmadan birden çok kaynaktaki verileri işlemenize olanak sağlar. Bu tür bir güvenli hesaplama, çok çeşitli senaryolara izin vermez: para azaltma, sahtekarlık algılama ve sağlık verileri için güvenli analiz.

Birden çok kaynak, verilerini bir sanal makinedeki tek bir şifreye karşıya yükleyebilir. Bir taraf, şifreleme işleminin veri üzerinde hesaplama veya işleme işlemlerini yapmayı söyler. Hiçbir taraf (analizin yürütülmediği olmasa bile), gerçekten de kuşkuya yüklenmiş diğer tarafın verilerini görebilir. 

Güvenli çok taraflı bilgi işlem ortamında, şifrelenmiş veriler kuşatma ' a gider. şifreleme, bir anahtar kullanarak verilerin şifresini çözer, analiz gerçekleştirir, bir sonuç alır ve bir tarafın belirtilen anahtarla çözebilecekleri şifreli bir sonuç gönderir. 

**Kullanımdaki verileri koruma**: 
- Azure 'da bir DCsv2-Series sanal makinesini (VM), etkin Intel SGX desteğini kullanın. Bu VM 'Ler, uygulama verilerinizin ve kodunuzun bölümlerini güvenli hale getirmek ve yalıtmak için güvenilir yürütme ortamları (TEEs) ile etkinleştirilir.
- Sanal makine içinde bir şifreleme oluşturmak için bir kuşve duyarlı SDK kullanın. Kuşatma içinde, veri, VM sağlayıcısı bile herhangi bir şekilde gösterilmez. Kuşdaki veriler donanım desteğiyle şifrelenir.
    - Örneğin, sunucu tarafı işleme için [OE SDK 'yı](https://github.com/openenclave/openenclave) kullanabilirsiniz. 

**Aktarılan verileri koruma** 
- Yoldaki verilerin güvenliğini sağlamak için güvenli bir kanal olarak atsınanan TLS kullanın
- İstemci, verilerin yalnızca şifreleme ile korunan aynı sunucuya gönderilmesini sağlar. 

**Bekleyen verileri koruma**
- Rest sırasında verilerin güvenliğini sağlamak için korumalı ve güvenli veri depolarını kullanın 

### <a name="anti-money-laundering"></a>Para olumsuz korunma
Bu güvenli çok taraflı hesaplama örneğinde birden çok bankalar, müşterilerinin kişisel verilerini açığa çıkarmak zorunda kalmadan birbirleriyle verileri paylaşır. Bankalar, birleştirilmiş gizli veri kümesi üzerinde anlaşmaya varılmış olarak çalışır. Toplanan veri kümesindeki analizler, her bir kullanıcının verilerine erişen bankalar olmadan, birden çok bankalar arasında bir kullanıcı tarafından para hareketini algılayabilir.

Gizli bilgi işlem sayesinde, bu finans kurumları sahtekarlık algılama hızlarını artırabilir, parasal senaryolar ele alabilir, hatalı pozitif sonuçları azaltabilir ve daha büyük veri kümelerinden öğrenmeye devam edebilir. 

### <a name="drug-development-in-healthcare"></a>Sağlık Hizmetleri 'nde ilaç geliştirme
İş ortağı sistem durumu olanakları, ML modelini eğitmek için özel sistem durumu veri kümeleri katkıda bulunur. Her tesis yalnızca kendi veri kümesini görebilir. Başka bir tesis veya bulut sağlayıcısı bile veri veya eğitim modelini görebilir. 

![Hasta sistem durumu Analizi](./media/use-cases-scenarios/patient-data.png)

Tüm tesisler eğitilen modeli kullanmaktan faydalanır. Modeli daha fazla veri ile oluşturarak model daha doğru hale gelmiştir. Modeli eğitmek için katkıda bulunan her tesis bu hizmeti kullanabilir ve yararlı sonuçlar alabilir. 

## <a name="blockchain"></a>Blok Zinciri

Blok zinciri ağı, düğümler tarafından kullanılan bir ağ. Bu düğümler, bütünlük sağlamak ve ağ durumunda konsensus 'a ulaşmak isteyen operatörler veya doğrulayıcılar tarafından çalıştırılır ve sürdürülür. Düğümler, defterlerin Çoğaltmalarından ve blok zinciri işlemlerini izlemek için kullanılır. Her düğüm, dağıtılmış bir ağda bütünlük ve kullanılabilirlik sağlayan işlem geçmişinin tam bir kopyasına sahiptir.

Gizli bilgi işlem üzerinde oluşturulan blok zinciri teknolojileri, veri gizliliğini ve güvenli hesaplamaları etkinleştirmek için donanım tabanlı gizliliği kullanabilir. Bazı durumlarda, veri erişimini korumak için tüm genel muhasebe şifrelenir. Bazen işlemin kendisi, düğüm içindeki kuşın içindeki bir işlem modülü içinde meydana gelebilir.

### <a name="confidential-consortium-framework-ccf"></a>Gizli konsorsiyum çerçevesi (CCF)
[CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/) , Azure gizli bilgi işlem üzerinde oluşturulmuş bir dağıtılmış blok zinciri çerçevesinin bir örneğidir. Microsoft Research ile, bu çerçeve, kanıtlama için uzak şifreleme ağı oluşturmak için güvenilir yürütme ortamlarının (TEEs) gücünden yararlanır. Düğümler, Azure sanal makinelerinin ([DCsv2 serisi](confidential-computing-enclaves.md)) üzerinde çalışabilir ve şifreleme altyapısından faydalanabilirsiniz. Kanıtlama protokollerinden, blok zinciri kullanıcıları tek bir CCF düğümünün bütünlüğünü doğrulayabilirler ve tüm ağı etkili bir şekilde doğrular. 

![Düğüm ağı](./media/use-cases-scenarios/ccf.png)

CCF 'de, merkezi olmayan defter, tüm ağ düğümlerinde çoğaltılan bir Key-Value deposuna kaydedilen değişikliklerden oluşur. Bu düğümlerin her biri, TLS üzerinden blok zinciri kullanıcıları tarafından tetiklenebilecek bir işlem altyapısı çalıştırır. Bir uç nokta tetikleytiğinizin Key-Value depoyu zaman içinde oluşturursunuz. Şifrelenmiş değişiklik, merkezi olmayan bir muhasebeye kaydedilmeden önce, Consensus 'e ulaşmak için belirli sayıda düğüm tarafından kabul edilmelidir. 

## <a name="next-steps"></a>Sonraki Adımlar
DCsv2-Series bir sanal makine [dağıtın](quick-create-marketplace.md) .


