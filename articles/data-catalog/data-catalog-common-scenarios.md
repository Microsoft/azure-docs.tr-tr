---
title: Azure Veri Kataloğu genel senaryoları
description: Yüksek değerli veri kaynaklarını kaydetme ve bulma, self servis iş zekası sağlama ve veri kaynakları ve süreçleriyle ilgili mevcut bilgileri yakalama dahil olmak üzere Azure Veri Kataloğu için genel senaryolara genel bakış.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: aeae505b510f563a6640726c384ea358983eb24f
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736454"
---
# <a name="azure-data-catalog-common-scenarios"></a>Azure Veri Kataloğu genel senaryoları
Bu makalede, Azure Veri Kataloğu 'nun, kuruluşunuzun mevcut veri kaynaklarından daha fazla değer almasını sağlayan yaygın senaryolar sunulmaktadır.

## <a name="scenario-1-registration-of-central-data-sources"></a>Senaryo 1: Merkezi veri kaynaklarını kaydetme
Kuruluşların genellikle çok fazla yüksek değerli veri kaynağı vardır. Bu veri kaynakları, iş kolu, çevrimiçi işlem işleme (OLTP) sistemleri, veri ambarları ve iş zekası/analiz veritabanlarını içerir. Sistem sayısı ve bunlar arasındaki örtüşme, genellikle iş ihtiyaçları geliştikçe ve işletmenin kendisi gibi, birleşmeler ve alımlar aracılığıyla geliştikçe zaman içinde artar.

Kuruluş üyelerinin bu veri kaynaklarında verileri nerede bulabileceklerini bilmeleri zor olabilir. Aşağıdakiler gibi sorular çok yaygın olarak verilmiştir:

* Şirket içinde kullanılan üç HR sisteminin bu tür bir rapor oluşturmak için kullanmalıyım?
* Yalnızca sona ermiş mali yıl için sertifikalı satış numaralarını almak için nereye gitmem gerekir?
* Ne yapmam gerekir, ne yapmalıyım? veya veri ambarına erişim sağlamak için kullanmam gereken işlem nedir?
* Bu sayıların doğru olup olmadığını bilmiyorum. Bu panoyu ekibimde paylaşmadan önce bu verilerin nasıl kullanılması gerektiğini öğrenmek için kim olabilir?

Azure Veri Kataloğu, bu ve diğer sorulara yanıt verebilir. Kuruluşlar genelinde kullanılan Merkezi, yüksek değerli, BT tarafından yönetilen veri kaynakları genellikle kataloğun doldurulmasının mantıksal başlangıç noktasıdır. Herhangi bir Kullanıcı bir veri kaynağını kaydedebilse de, kataloğun en büyük Kullanıcı sayısı için değer sağlama olasılığı en yüksek olan veri kaynaklarıyla başlatılacak şekilde, sistemin benimseme ve kullanımına yardımcı olur. 

Azure Veri Kataloğu ile çalışmaya başladıysanız, birçok farklı veri tüketicisini tarafından kullanılan önemli veri kaynaklarını tanımlamak ve kaydetmek, başarılı bir şekilde ilk adımınıza sahip olabilir.

Bu senaryo Ayrıca, anlaşılması ve erişimi kolaylaştırmak için yüksek değerli veri kaynaklarına açıklama ekleme olanağı sunar. Bu çabanın bir en önemli yönü, kullanıcıların veri kaynağına erişim isteme hakkında bilgi içerelidir. Azure Veri Kataloğu ile veri kaynağı erişimini denetmaktan sorumlu kullanıcı veya takımın e-posta adresini, mevcut araçlara veya belgelere bağlantıları ya da erişim-istek işlemini açıklayan serbest metinleri sağlayabilirsiniz. Bu bilgiler, kayıtlı veri kaynaklarını keşfettiğiniz ancak veri kaynağı sahipleri tarafından tanımlanan ve denetlenen süreçler kullanılarak kolayca erişim istemek için verilere erişim izni olmayan üyelere yardımcı olur.

## <a name="scenario-2-self-service-business-intelligence"></a>Senaryo 2: Self servis iş zekası
Geleneksel kurumsal iş zekası çözümleri pek çok kuruluşun veri lanlarının önemli bir parçası olmaya devam etse de, iş için değişen destek, self servis BI 'ı daha fazla ve daha önemli hale getirmiştir. Self servis BI 'ı kullanarak bilgi çalışanları ve analistleri, merkezi bir BT ekibine bağlı olmadan veya BT ekibinin zamanlaması ve kullanılabilirliği tarafından sınırlandırılmadan kendi raporlarını, çalışma kitaplarını ve panoları oluşturabilir.

Self servis BI senaryolarında kullanıcılar genellikle birden çok kaynaktan veri birleştirir, çoğu daha önceden bı ve analiz için kullanılmamış olabilir. Bu veri kaynaklarından bazıları zaten biliniyor olsa da, belirli bir görev için olası veri kaynaklarını bulmak ve değerlendirmek için ne yapılacağını saptamak zor olabilir.

Geleneksel olarak, bu bulma işlemi el ile bir işlemdir: analistleri, aranan verilerle çalışan diğerlerini belirlemek için eşler arası ağ bağlantılarını kullanır. Bir veri kaynağı bulduktan ve kullanıldıktan sonra, işlem, birden fazla kullanıcı el ile bulma işlemi gerçekleştirmeyle birlikte her iki Self-Service bı çabasında yeniden yinelenir.

Azure Veri Kataloğu ile kuruluşunuz bu çaba döngüsünü bozabilir. Bir veri kaynağını geleneksel yollarla bulduktan sonra analist, daha sonra diğer kullanıcılar tarafından daha kolay bulunabilir olması için bu kaydı kaydedebilir. Analist, kayıtlı veri varlıklarına açıklama ekleyerek daha fazla değer ekleyebilse de, bu ek açıklamanın kayıt ile aynı anda gerçekleşmesi gerekmez. Kullanıcılar zaman içinde katkıda bulunabilir, bu da zamanlamalarda bulunan veri kaynaklarına giderek kademeli olarak değer ekler.

Katalog içeriğinin bu organik büyümesi, merkezi veri kaynaklarının ön ön kaydına yönelik doğal bir tamamdır. Katalogdan çok sayıda kullanıcının ihtiyacı olan verilerle önceden doldurulmaları, ilk kullanım ve bulma işlemi için bir motive olabilir. Kullanıcıların ek kaynaklara kaydolmasını ve ek olarak Not eklemesini sağlamak, bunları ve diğer kuruluş üyelerini bağlı tutmanın bir yoludur.

Bu senaryo özellikle self servis BI 'a odaklansa da büyük ölçekli kurumsal bı projelerine yönelik aynı desenler ve güçlükler için de geçerlidir. Kuruluşunuz, veri kataloğu 'Nu kullanarak el ile gerçekleştirilen bir veri kaynağı bulma işlemini içeren herhangi bir çabayı geliştirebilirler.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Senaryo 3: Üç aylık bilgi yakalama
İşinizi ne yapmak için ihtiyacınız olan verileri ve verileri nerede bulabileceğinizi nasıl anlarsınız?

İşte bir süredir işiniz yaşıyorsanız, büyük olasılıkla çok fazla bilgi alabilirsiniz. Aşamalı bir öğrenme işleminden geçtiniz ve zaman içinde günlük çalışmalarınız için önemli olan veri kaynakları hakkında bilgi edindiniz.

Yeni bir çalışan ekibinize katıldığında, bu kişi iş için hangi verilerin gerekli olduğunu ve nerede bulunacağını nasıl öğrenebilirim?

ODDS, yeni kişi size bu soruları girer.

Bu sürekli bilgi aktarımı, büyük ve küçük kuruluşlarda veri kaynağı bulma sürecinin bir parçasıdır. Daha fazla kıdemli ve deneyimli ekip üyeleri, yıl boyunca bilgi edinmiş ve daha yeni takım üyeleri, soruları olduğunda bu kişilere sormasını öğrenmiş. En önemli bilgiler genellikle yalnızca birkaç önemli kişinin kafalarında bulunur ve bu kişilerin tatile açık olması veya kuruluştan ayrılmasından kaynaklanır.

Veri uzmanları genellikle kendi bilgilerini belgeleyip bir ekip SharePoint sitesindeki Word belgelerinden veya e-posta yoluyla paylaşarak bir çaba harcamasını sağlar. Bu yaklaşım değerli olsa da, yeni bir keşif sorunu ortaya çıkarır: kişiler hangi belgelerin olduğunu ve nerede bulabileceğinizi nasıl bilir?

Azure Veri Kataloğu sayesinde, kuruluşunuzun bu üç katlı bilgileri depolamak ve paylaşmak ve kolayca bulunabilir hale getirmek için tek bir merkezi konuma sahip olması gerekir. Veri kataloğunda veri uzmanlarınız doğrudan veri varlıklarına ek açıklama ekleyebilir ve mevcut belgelerin bağlantılarını sağlayabilir. Kuruluş üyeleri bir veri kaynağını bulmak için kataloğunu kullandıklarında yalnızca kaynağın kendisini değil, daha önce yalnızca kuruluşunuzun uzmanlarının fikirlerini de var olan bilgileri bulur.
