---
title: Apache Cassandra ve Azure Cosmos DB Cassandra API için Azure yönetilen örneği arasındaki farklılıklar
description: Apache Cassandra için Azure yönetilen örneği ve Azure Cosmos DB Cassandra API arasındaki farklar hakkında bilgi edinin. Ayrıca, bu hizmetlerin her birinin avantajlarını ve ne zaman seçim yapılacağını öğrenirsiniz.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101749017"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Apache Cassandra için Azure yönetilen örneği (Önizleme) ve Azure Cosmos DB Cassandra API arasındaki farklar 

Bu makalede, Apache Cassandra için Azure yönetilen örneği ve Azure Cosmos DB Cassandra API arasındaki farkları öğreneceksiniz. Bu makalede, iki hizmet arasında seçim yapma veya kendi Apache Cassandra ortamınızı barındırma hakkında öneriler sunulmaktadır.

> [!IMPORTANT]
> Apache Cassandra için Azure yönetilen örneği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-differences"></a>Temel farklılıklar

Apache Cassandra için Azure yönetilen örneği, Azure 'daki açık kaynaklı Apache Cassandra örnekleri için düğüm durumunu korumak üzere otomatik dağıtım, ölçeklendirme ve işlemler sağlar. Ayrıca, mevcut şirket içi veya bulut şirket içinde barındırılan Apache Cassandra kümelerinin kapasitesini ölçeklendirmeye olanak tanır. Mevcut küme halkasını yönetilen Cassandra veri merkezleri ekleyerek ölçeği ölçeklendirir.

Azure Cosmos DB [Cassandra API](../cosmos-db/cassandra-introduction.md) , Microsoft 'un genel olarak dağıtılmış bulut Yerel veritabanı hizmeti [Azure Cosmos DB](../cosmos-db/index.yml)üzerinde bir uyumluluk katmanıdır. Azure 'da bu hizmetlerin birleşimi, karmaşık karma bulut ortamlarında Apache Cassandra kullanıcıları için bir karmaşıklık sağlar.

## <a name="how-to-choose"></a>Nasıl seçirsiniz?

Aşağıdaki tabloda, bu dağıtımın her birinin yaklaşıtığı yaygın senaryolar, iş yükü gereksinimleri ve ASP 'ler gösterilmektedir:

| |Şirket içinde veya Azure 'da şirket içinde barındırılan Apache Cassandra | Apache Cassandra için Azure yönetilen örneği | Azure Cosmos DB Cassandra API'si |
|---------|---------|---------|---------|
|**Dağıtım türü**| Özel düzeltme ekleri veya hatalarla birlikte özelleştirilmiş bir Apache Cassandra dağıtımına sahipsiniz. | Herhangi bir özel kod olmadan standart bir açık kaynaklı Apache Cassandra dağıtımına sahipsiniz. | Aşağıda Apache Cassandra olmayan, ancak bir [kablo protokol](../cosmos-db/cassandra-support.md) düzeyindeki tüm açık kaynaklı istemci sürücüleriyle uyumlu olan bir platforma sahip içeriktir. |
| **İşletimsel ek yük**| Kümelerinizi dağıtabilecek, yapılandırabilen ve koruyabilen mevcut Cassandra uzmanlarınız var.  | Apache Cassandra düğümünüz için işletimsel ek yükünü düşürmek istiyorsanız, ancak çoğaltma ve tutarlılık gibi platform düzeyi yapılandırmalarda denetimi devam ettirmek istiyorsunuz. | Bulutta tam olarak yönetilen bir hizmet olarak platform veritabanı kullanarak işlem yükünü ortadan kaldırmak istiyorsunuz. |
| **İşletim sistemi gereksinimleri**| Özel veya altın sanal makine işletim sistemi görüntülerini sürdürme gereksinimleriniz vardır. | Vanilla görüntülerini kullanabilir, ancak SKU, bellek, disk ve ıOPS üzerinde denetime sahip olmak isteyebilirsiniz. | Kapasite sağlamanın basitleşmesini ve tek bir normalleştirilmiş ölçüm olarak ifade edilmesi, örneğin Azure Cosmos DB [İstek birimleri](../cosmos-db/request-units.md) gibi, aktarım hızı ile bire bir ilişki sağlamak istersiniz. |
| **Fiyatlandırma modeli**| DataStax araçları gibi yönetim yazılımlarını kullanmak ve lisanslama maliyetleriyle memnun olmak istiyorsunuz. | Saf açık kaynaklı lisanslama ve VM örneği tabanlı fiyatlandırmayı tercih edersiniz. | [Otomatik ölçeklendirme](../cosmos-db/manage-scale-cassandra.md#use-autoscale) ve [sunucusuz](../cosmos-db/serverless.md) teklifler içeren Cloud-Native fiyatlandırma kullanmak istiyorsunuz. |
| **Analiz**| Oluşturma ve bakımını yapma yükünden bağımsız olarak analitik işlem hatları sağlanması üzerinde tam denetim istiyorsunuz. | Azure Databricks gibi bulut tabanlı analitik hizmetleri kullanmak istiyorsunuz. | [Cosmos DB Için Azure SYNAPSE bağlantısı](../cosmos-db/synapse-link.md)ile platformda yerleşik olarak neredeyse gerçek zamanlı karma işlem analizlerinin bulunmasını istiyorsunuz. |
| **İş yükü kalıbı**| İş yükünüz oldukça kararlı bir durumdur ve kümede sıklıkla ölçekleme düğümlerine gerek kalmaz. | İş yükünüz geçici ve bir veri merkezinde düğümleri ölçeklendirmeniz veya ölçeklendirmeniz ya da veri merkezlerini kolayca ekleyebilmeniz ya da kaldırabilmeniz gerekir. | İş yükünüz genellikle geçici olur ve önemli bir birimde ölçeği hızla ve ölçeklendirebilmeniz gerekir. |
| **SLA’lar**| SLA 'Lar tutarlılık, aktarım hızı, kullanılabilirlik ve olağanüstü durum kurtarma işlemlerinde korunabilirliklerinizden memnun olursunuz. | SLA 'Ları tutarlılık, verimlilik ve kullanılabilirliğine karşı korumaya yönelik işlemleriniz, ancak yedeklemelerle ilgili yardıma ihtiyacınız vardır. | Tutarlılık, verimlilik, kullanılabilirlik ve olağanüstü durum kurtarma konularında tam kapsamlı SLA 'Lar istiyorsunuz. |

## <a name="next-steps"></a>Sonraki adımlar

Hızlı başlangıçlardan birini kullanmaya başlayın:

* [Azure portal yönetilen örnek kümesi oluşturma](create-cluster-portal.md)