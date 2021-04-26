---
title: Azure Veri Paylaşımı nedir?
description: Azure veri paylaşımı 'nı kullanarak birden çok müşteriye ve iş ortağına verileri kolayca paylaşma hakkında bilgi edinin.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: overview
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: 3a7c73e4a5ba00155ab905f28edbcb0eb42c0539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724872"
---
# <a name="what-is-azure-data-share"></a>Azure Veri Paylaşımı nedir?

Bugünün dünyasında, veriler birçok kuruluşun müşterileri ve iş ortaklarıyla tek ve güvenli bir şekilde paylaşması gereken önemli bir stratejik varlık olarak görüntülenir. Bu şekilde, müşterilerin bu şekilde, FTP, e-posta, API 'Ler ve birkaç kez ad vermek üzere çok çeşitli yolları vardır. Kuruluşlar, verilerini paylaştıkları kişileri kolayca kaybedebilir. Verileri FTP aracılığıyla veya kendi API altyapısını kullanarak paylaşmak, sağlama ve yönetme konusunda genellikle pahalıdır. Büyük ölçekte bu paylaşım yöntemlerini kullanmayla ilişkili yönetim ek yükü vardır. 

Birçok kuruluşun paylaşıldıkları veriler için sorumlu olması gerekir. Birçok kuruluş, sorumluluğuna ek olarak, tüm veri paylaımlarını basit bir şekilde denetleyebilmek, yönetebilmek ve izlemek istiyor. Bugünün dünyasında, verilerin üstel bir hızda büyümeye devam etmesi beklendiğinde, kuruluşların büyük verileri paylaşmanın basit bir yolu olması gerekir. Müşteriler zamanında Öngörüler elde edebilmeleri için en güncel verileri talep ettebilirler.

Azure veri paylaşma, kuruluşların birden çok müşteriyle ve iş ortaklarıyla basit ve güvenli bir şekilde veri paylaşmasını sağlar. Yalnızca birkaç tıklamayla yeni bir veri paylaşımı hesabı sağlayabilir, veri kümesi ekleyebilir, müşterilerinizi ve iş ortaklarınızı veri paylaşımınıza davet edebilirsiniz. Veri sağlayıcılar, paylaştıkları verilerin kontrolünü her zaman elde tutar. Azure Veri Paylaşımı, hangi verilerin, ne zaman ve kim tarafından paylaşıldığını yönetmeyi ve izlemeyi kolaylaştırır. 

Veri sağlayıcısı, veri paylaşımıyla ilgili kullanım koşulları belirtilerek verilerinin nasıl ele alındığını kontrol ediyor olabilir. Veri tüketicisinin verileri alabilmek için önce bu koşulları kabul etmesi gerekir. Veri sağlayıcıları, veri tüketicilerinin güncelleştirmeleri ne sıklıkta alacağını belirtebilir. Veri sağlayıcıları istedikleri zaman yeni güncelleştirmelere erişimi iptal edebilir. 

Azure veri paylaşımında, üçüncü tarafların verileri zenginleştirerek analiz ve AI senaryolarına kadar zengin bir şekilde birleştirerek Öngörüler geliştirmeye yardımcı olur. Azure veri paylaşım kullanarak paylaşılan verileri hazırlamak, işlemek ve analiz etmek için Azure Analytics araçlarının gücünden kolayca yararlanabilirsiniz. 

Veri sağlayıcısı ve veri tüketicisi, verileri paylaşmak ve almak için bir Azure aboneliğine sahip olmalıdır. Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="scenarios-for-azure-data-share"></a>Azure veri paylaşımıyla ilgili senaryolar

Azure veri paylaşma çeşitli farklı sektörlerde kullanılabilir. Örneğin, bir perakende satış verilerinin son noktalarını tedarikçilerle paylaşmak isteyebilir. Bir satıcı, Azure veri paylaşımının kullanıldığı tüm tedarikçilerle ilgili satış verileri noktası içeren bir veri paylaşımının yanı sıra bir saatlik ya da günlük olarak satış paylaşabilir. 

Azure veri paylaşma, belirli bir sektör için veri marketi oluşturmak için de kullanılabilir. Örneğin, üçüncü taraflarla popülasyon büyümesi hakkında anonim verileri düzenli olarak paylaşan bir devlet veya araştırma kurumu. 

Azure veri paylaşımında başka bir kullanım örneği de veri Konsorsiyumu oluşturur. Örneğin, bir dizi farklı araştırma kurumları, verileri tek bir güvenilen gövdele paylaşabilir. Veriler, Azure Analytics araçları kullanılarak çözümlenir, toplanır veya işlenir ve ardından ilgilenen taraflarla paylaşılır. 

## <a name="how-it-works"></a>Nasıl çalışır?

Azure veri paylaşımı Şu anda anlık görüntü tabanlı paylaşım ve yerinde paylaşım sunmaktadır. 

Anlık görüntü tabanlı paylaşımda, veriler veri sağlayıcının Azure aboneliğinden ve veri tüketicinin Azure aboneliğindeki bir yerden taşınıyor. Veri sağlayıcısı olarak, bir veri paylaşma sağlar ve alıcıları veri paylaşımında davet edersiniz. Veri tüketicileri, e-posta aracılığıyla veri paylaşımınıza bir davet alır. Bir veri tüketicisi daveti kabul ettiğinde, bunlarla paylaşılan verilerin tam anlık görüntüsünü tetikleyebilirler. Bu veriler veri tüketicileri depolama hesabına alınır. Veri tüketicileri, her zaman verilerin en son sürümüne sahip olmaları için kendileriyle paylaşılan veriler için düzenli, artımlı güncelleştirmeler alabilir. 

Veri sağlayıcıları, veri tüketicilerini bir anlık görüntü zamanlaması aracılığıyla bunlarla paylaşılan verilere yönelik Artımlı güncelleştirmeleri sunabilir. Anlık görüntü zamanlamaları saatlik veya günlük olarak sunulur. Bir veri tüketicisi veri payını kabul ettiğinde ve yapılandırdığında, bir anlık görüntü çizelgesine abone olabilirler. Bu, paylaşılan verilerin düzenli aralıklarla güncelleştirildiği senaryolarda faydalıdır ve veri tüketicisi en güncel verilere ihtiyaç duyuyor. 

![veri paylaşma akışı](media/data-share-flow.png)

Bir veri tüketicisi bir veri paylaşımının kabul ettiğinde, verileri seçtikleri bir veri deposunda alabilecektir. Örneğin, veri sağlayıcısı verileri Azure Blob depolama kullanarak paylaşıyorsa veri tüketicisi bu verileri Azure Data Lake Store alabilir. Benzer şekilde, veri sağlayıcısı bir Azure SYNAPSE analizinden veri paylaşıyorsa veri tüketicisi, verileri bir Azure Data Lake Store, Azure SQL veritabanı veya Azure SYNAPSE analiziyle almak isteyip istemediğinizi seçebilirler. SQL tabanlı kaynaklardan paylaşım durumunda veri tüketicisi Ayrıca, Parquet veya CSV 'de veri alıp almamasını da seçebilir. 

Yerinde paylaşım sayesinde veri sağlayıcıları verileri kopyalamadan verileri paylaşabilir. İlişki paylaşımı, davet akışı aracılığıyla kurulduktan sonra, veri sağlayıcının kaynak veri deposu ve veri tüketicisinin hedef veri deposu arasında bir sembolik bağlantı oluşturulur. Veri tüketicisi, kendi veri deposunu kullanarak verileri gerçek zamanlı olarak okuyup sorgulayabilir. Kaynak veri deposunda yapılan değişiklikler veri TÜKETİCİSİNDE hemen kullanılabilir. Yerinde paylaşım Şu anda Azure Veri Gezgini için kullanılabilir.

## <a name="key-capabilities"></a>Temel işlevler

Azure veri paylaşma, veri sağlayıcılarının şunları yapmasını sağlar:

* [Desteklenen veri depoları](supported-data-stores.md) listesinden kuruluşunuzun dışındaki müşterilerle ve iş ortaklarıyla veri paylaşma

* Verilerinizi paylaştığınız kişileri takip edin

* Anlık görüntü veya yerinde paylaşım seçimi

* Veri Tüketicileriniz verilerinize güncelleştirme alma sıklığı

* Müşterilerinizin verilerinizin en son sürümünü gerektiği gibi çekmesini veya sizin tarafınızdan tanımlanan bir aralıkta verilerinize otomatik olarak artımlı değişiklikler almasına izin vermeyi sağlar

Azure veri paylaşma, veri tüketicilerinin şunları yapmasını sağlar: 

* Paylaşılmakta olan veri türünün açıklamasını görüntüle

* Verilerin kullanım koşullarını görüntüleme

* Azure Veri Paylaşma davetini kabul etme veya reddetme

* Sizinle paylaşılan verileri [desteklenen bir veri deposuna](supported-data-stores.md)kabul edin.

* Verilere yerinde erişin veya paylaşılan verilerin tam veya artımlı anlık görüntüsünü tetikleyin

Yukarıda listelenen tüm temel yetenekler Azure portal veya REST API 'Leri aracılığıyla desteklenir. REST API 'Leri aracılığıyla Azure veri paylaşımının kullanımı hakkında daha fazla bilgi için başvuru belgelerimize göz atın. 

## <a name="supported-regions"></a>Desteklenen bölgeler

Azure veri paylaşımının kullanılabilmesini sağlayan Azure bölgelerinin bir listesi için lütfen [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) sayfasına bakın ve Azure veri paylaşımında arama yapın. 

Azure veri paylaşımında depolanan meta veriler için Güneydoğu Asya 'da (Singapur), bölge içinde depolanır ve desteklenen diğer tüm bölgeler için coğrafi bölgede depolanır. Azure veri paylaşım, paylaşılan verilerin bir kopyasını depolamaz. Veriler paylaşılmakta olan temel alınan veri deposunda depolanır. Örneğin, bir veri sağlayıcısı verilerini Batı ABD bulunan Azure Data Lake Storage bir hesapta (verilerin depolandığı yer) depolar. Anlık görüntü aracılığıyla Batı Avrupa ' de bulunan bir Azure depolama hesabıyla veri paylaştıklarında, genellikle veriler doğrudan Batı Avrupa konumundaki Azure depolama hesabına aktarılır. 

Azure veri paylaşımının hizmeti 'nin hizmetten yararlanmak için bölgenizde kullanılabilir olması gerekmez. Örneğin, Azure veri paylaşımının henüz kullanılamadığı bir bölgede bulunan bir Azure depolama hesabında depolanan verileriniz varsa, verilerinizi paylaşmak için hizmetten yararlanmaya devam edebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Verileri paylaşmaya nasıl başlayacağınızı öğrenmek için [verilerinizi paylaşma](share-your-data.md) öğreticiye geçin.
