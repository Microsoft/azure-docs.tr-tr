---
title: Paket yönetimi
description: Azure SYNAPSE Analytics 'te Apache Spark tarafından kullanılan kitaplıkları ekleme ve yönetme hakkında bilgi edinin.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: e8ad6d072af6979eb8509068c1dcd239e7840950
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598023"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te Apache Spark için kitaplıkları yönetme
Kitaplıklar, programlarınıza veya projelerinize dahil etmek isteyebileceğiniz yeniden kullanılabilir kod sağlar. 

Çeşitli nedenlerle sunucusuz Apache Spark havuz ortamınızı güncelleştirmeniz gerekebilir. Örneğin, şunları görebilirsiniz:
- temel bağımlılıklarından biri yeni bir sürüm yayımlamıştır.
- Machine Learning modelinize eğitim veya verilerinizi hazırlama hakkında ek bir pakete ihtiyacınız vardır.
- daha iyi bir paket bulduk ve artık eski pakete ihtiyacınız yoktur.
- Takımınız, Apache Spark havuzunuzdaki kullanılabilir olması gereken özel bir paket oluşturdu.

Üçüncü tarafa veya yerel olarak oluşturulmuş bir kodu uygulamalarınız için kullanılabilir hale getirmek için, bir kitaplığı sunucusuz Apache Spark havuzlarınızdan veya Not defteri oturumlarından birine yükleyebilirsiniz.
  
## <a name="default-installation"></a>Varsayılan yükleme
Azure SYNAPSE Analytics 'teki Apache Spark, tam bir anacondas yüklemesi artı ek kitaplıklar içerir. Tam kitaplıklar listesi, [Apache Spark sürüm desteği](apache-spark-version-support.md)' nde bulunabilir. 

Bir Spark örneği başlatıldığında, bu kitaplıklar otomatik olarak dahil edilir. Ek paketler Spark havuz düzeyinde veya oturum düzeyinde eklenebilir.

## <a name="workspace-packages"></a>Çalışma alanı paketleri
Özel uygulamalar veya modeller geliştirirken ekibiniz, kodunuzu paketlemek için tekerlek veya JAR dosyaları gibi çeşitli kod yapıtları geliştirebilir. 

SYNAPSE içinde, çalışma alanı paketleri özel veya özel bir tekerlek veya JAR dosyaları olabilir. Bu paketleri çalışma alanınıza yükleyebilir ve daha sonra belirli bir Spark havuzuna atayabilirsiniz. Bu çalışma alanı paketleri atandıktan sonra, tüm Spark havuzu oturumlarına otomatik olarak yüklenir.

Çalışma alanı kitaplıklarını yönetme hakkında daha fazla bilgi edinmek için aşağıdaki nasıl yapılır kılavuzlarını ziyaret edin:

- [Python çalışma alanı paketleri (Önizleme): ](./apache-spark-manage-python-packages.md#install-wheel-files) Python tekerlek dosyalarını bir çalışma alanı paketi olarak karşıya yükleyin ve daha sonra bu paketleri belirli sunucusuz Apache Spark havuzlarına ekleyin.
- [Scala/Java çalışma alanı paketleri (Önizleme): ](./apache-spark-manage-scala-packages.md#workspace-packages) Scala ve Java jar dosyalarını bir çalışma alanı paketi olarak karşıya yükleyin ve daha sonra bu paketleri belirli sunucusuz Apache Spark havuzlarına ekleyin.

## <a name="pool-packages"></a>Havuz paketleri
Bazı durumlarda, belirli bir Apache Spark havuzunda kullanılan paket kümesini standartlaştırmak isteyebilirsiniz. Aynı paketler, takımınızda birden çok kişi tarafından yaygın olarak yüklenirse bu standartlaştırma yararlı olabilir. 

Azure SYNAPSE Analytics havuz yönetimi yeteneklerini kullanarak, belirli bir sunucusuz Apache Spark havuzunda yüklenmesini istediğiniz varsayılan kitaplık kümesini yapılandırabilirsiniz. Bu kitaplıklar [temel çalışma zamanının](./apache-spark-version-support.md)üzerine yüklenir. 

Şu anda havuz yönetimi yalnızca Python için desteklenir. Python için SYNAPSE Spark havuzları, Python paket bağımlılıklarını yüklemek ve yönetmek için Conda kullanır. Havuz düzeyi kitaplıklarınızı belirtirken artık bir requirements.txt veya Environment. yıml sağlayabilirsiniz. Bu ortam yapılandırma dosyası, bu Spark havuzundan her bir Spark örneği oluşturulduğunda kullanılır. 

Bu yetenekler hakkında daha fazla bilgi edinmek için [Python havuz yönetimi](./apache-spark-manage-python-packages.md#pool-libraries)ile ilgili belgeleri ziyaret edin.

> [!IMPORTANT]
> - Yüklemekte olduğunuz Paket büyükse veya yüklemesi uzun sürüyorsa, bu, Spark örneği başlangıç süresini etkiler.
> - PySpark, Python, Scala/Java, .NET veya Spark sürümünün değiştirilmesi desteklenmez.
> - Pypı 'den paket yükleme, DEP özellikli çalışma alanları içinde desteklenmez.

## <a name="session-scoped-packages"></a>Oturum kapsamlı paketler
Genellikle etkileşimli veri analizi veya makine öğrenimi yaparken, daha yeni paketleri denemek istediğinizi fark edebilir veya Apache Spark havuzunuzdaki henüz kullanılamayan paketlere ihtiyacınız olabilir. Kullanıcılar, havuz yapılandırmasını güncelleştirmek yerine, oturum bağımlılıklarını eklemek, yönetmek ve güncelleştirmek için artık oturum kapsamlı paketler kullanabilir.

Oturum kapsamlı paketler, kullanıcıların oturumunun başlangıcında paket bağımlılıklarını tanımlamasına olanak tanır. Oturum kapsamlı bir paket yüklediğinizde, yalnızca geçerli oturumun belirtilen paketlere erişimi vardır. Sonuç olarak, bu oturum kapsamlı paketler aynı Apache Spark havuzunu kullanan diğer oturumları veya işleri etkilemez. Ayrıca, bu kitaplıklar temel çalışma zamanı ve havuz düzeyi paketlerinin üzerine yüklenir. 

Oturum kapsamlı paketlerin nasıl yönetileceği hakkında daha fazla bilgi edinmek için aşağıdaki nasıl yapılır kılavuzlarını ziyaret edin:

- [Python oturum paketleri (Önizleme):](./apache-spark-manage-python-packages.md) Bir oturumun başlangıcında, popüler depolardan ek Python paketleri yüklemek için Conda *Environment. yıml* sağlayın. 
- [Scala/Java oturum paketleri: ](./apache-spark-manage-scala-packages.md) Oturumunuzun başlangıcında, kullanarak yüklenecek jar dosyalarının bir listesini sağlayın `%%configure` .

## <a name="next-steps"></a>Sonraki adımlar
- Varsayılan kitaplıkları görüntüleme: [Apache Spark sürüm desteği](apache-spark-version-support.md)
