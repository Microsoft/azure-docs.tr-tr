---
title: Data Factory kullanım örneği-ürün önerileri
description: Diğer hizmetlerle birlikte Azure Data Factory kullanarak uygulanan kullanım durumu hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 49ad9be7c70602132436b14234f01a4086d8e1fe
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139155"
---
# <a name="use-case---product-recommendations"></a>Kullanım Örneği - Ürün Önerileri
Azure Data Factory, çözüm Hızlandırıcıların Cortana Intelligence Suite uygulamak için kullanılan birçok hizmetlerden biridir.  Bu paket hakkındaki ayrıntılar için bkz. [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) sayfası. Bu belgede, Azure kullanıcılarının Azure Data Factory ve diğer Cortana Intelligence bileşen hizmetlerini kullanarak zaten çözmesinin ve uyguladığı yaygın bir kullanım durumu açıklanır.

## <a name="scenario"></a>Senaryo
Çevrimiçi perakendeciler genellikle müşterileri ürün satın almaya ikna etmek istiyor ve bu nedenle en büyük olasılıkla satın alması gereken ürünler. Bu işlemi gerçekleştirmek için çevrimiçi perakendeciler, bu belirli kullanıcı için kişiselleştirilmiş ürün önerilerini kullanarak kullanıcının çevrimiçi deneyimlerini özelleştirmeniz gerekir. Bu kişiselleştirilmiş öneriler, geçerli ve geçmiş alışveriş davranışı verilerine, ürün bilgilerine, Yeni tanıtılan markalara ve ürün ve müşteri kesimlemeye göre yapılır.  Bunlara ek olarak, kullanıcıların tüm kullanıcıları tarafından kullanılan genel kullanım davranışının analizine dayalı olarak Kullanıcı ürün önerilerini sağlayabilirler.

Bu perakendecinin amacı, Kullanıcı tıklama-satış dönüştürmelerini iyileştirmek ve daha yüksek satış gelirine sahip olmaktır.  Müşteri ilgi alanları ve eylemlerine bağlı olarak bağlamsal, davranış tabanlı ürün önerileri sunarak bu dönüştürmeye ulaşırlar. Bu kullanım örneğinde, müşterileri için optimize etmek isteyen işletmelerin bir örneği olarak çevrimiçi perakendecileri kullanırız. Ancak, bu ilkeler, müşterilerine kendi mal ve Hizmetleri etrafında etkileşim kurmak ve müşterilerin satın alma deneyimini kişiselleştirilmiş ürün önerileri ile geliştirmek isteyen tüm işletmeler için geçerlidir.

## <a name="challenges"></a>Zorluklar
Bu tür kullanım durumunu uygulamaya çalışırken çevrimiçi perakendecilerden oluşan birçok zorluk vardır. 

İlk olarak, farklı boyutlardaki ve şekillerin verileri, hem şirket içinde hem de bulutta birden çok veri kaynağından alınmalıdır. Bu veriler, Kullanıcı çevrimiçi perakende sitesine göz attığında ürün verilerini, geçmiş müşteri davranışı verilerini ve Kullanıcı verilerini içerir. 

İkinci olarak, kişiselleştirilmiş ürün önerileri makul ve doğru hesaplanmalı ve tahmin edilebilir olmalıdır. Çevrimiçi perakendecilere, ürün, marka ve müşteri davranış verilerinin yanı sıra, Kullanıcı için en iyi ürün önerilerini belirleme konusunda, son satın alımlara müşteri geri bildirimi de dahil etmek gerekir. 

Üçüncü olarak, sorunsuz bir göz atma ve satın alma deneyimi sağlamak ve en son ve ilgili önerileri sağlamak için önerilerin kullanıcıya hemen teslim edilebilir olması gerekir. 

Son olarak, perakendecinin, genel ürün satışı ve çapraz satış tıklama satışları başarılarını izleyerek ve gelecekteki önerilerine göre yaptığı yaklaşımların verimliliğini ölçmesi gerekir.

## <a name="solution-overview"></a>Çözüme Genel Bakış
Bu örnek kullanım örneği, [HDInsight](https://azure.microsoft.com/services/hdinsight/) ve [Power BI](https://powerbi.microsoft.com/)de dahil olmak üzere Azure Data Factory ve diğer Cortana Intelligence Bileşen Hizmetleri kullanılarak gerçek Azure kullanıcıları tarafından çözüldü ve uygulanmıştır.

Çevrimiçi satıcı bir Azure Blob Mağazası, şirket içi SQL Server, Azure SQL VERITABANı ve iş akışı boyunca veri depolama seçenekleri olarak ilişkisel bir veri reyonunu kullanır.  Blob deposu müşteri bilgilerini, müşteri davranış verilerini ve ürün bilgileri verilerini içerir. Ürün bilgileri verileri, ürün markası bilgilerini ve şirket içinde bir SQL veri ambarı 'nda depolanan bir ürün kataloğunu içerir. 

Tüm veriler, müşteri ilgi alanları ve eylemlerine bağlı olarak kişiselleştirilmiş öneriler sunmak üzere bir ürün öneri sistemine birleştirilir ve bu, Kullanıcı Web sitesinde katalogda bulunan ürünlere gözatacak. Müşteriler Ayrıca, herhangi bir kullanıcıyla ilgili olmayan genel Web sitesi kullanım desenlerine göre baktıkları ürünle ilgili ürünleri de görür.

![kullanım durumu diyagramı](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabaytlık ham Web günlük dosyası, çevrimiçi satıcıdan gelen Web sitesinden yarı yapılandırılmış dosyalar olarak günlük olarak oluşturulur. Ham Web günlüğü dosyaları ve müşteri ve ürün kataloğu bilgileri, bir hizmet olarak Data Factory genel olarak dağıtılan veri hareketi kullanılarak Azure Blob depolama alanına düzenli olarak alınır. Günün ham günlük dosyaları, uzun vadeli depolama için blob depolamada bölümlenmiştir (yıl ve aya göre).  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) , blob deposundaki ham günlük dosyalarını bölümlemek ve hem Hive hem de Pig betiklerini kullanarak alınan günlükleri ölçeğinde şekilde işlemek için kullanılır. Daha sonra bölümlenmiş Web günlüğü verileri, kişiselleştirilmiş ürün önerilerini oluşturmak için bir makine öğrenimi öneri sistemine yönelik gerekli girişleri ayıklamak üzere işlenir.

Bu örnekte makine öğrenimi için kullanılan öneri sistemi, [Apache Mahout](https://mahout.apache.org/)'un açık kaynak makine öğrenimi önerisi platformudur.  Herhangi bir [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) veya özel model senaryoya uygulanabilir.  Mahout modeli, genel kullanım desenlerine göre Web sitesindeki öğeler arasında benzerliği tahmin etmek ve bireysel kullanıcıya dayalı kişiselleştirilmiş öneriler oluşturmak için kullanılır.

Son olarak, kişiselleştirilmiş ürün önerileri sonuç kümesi, perakende Web sitesinin tüketimi için ilişkisel bir veri reyonuna taşınır.  Sonuç kümesine başka bir uygulama tarafından doğrudan blob depolamadan de erişilebilir veya diğer tüketiciler ve kullanım örnekleri için ek depolara taşınabilir.

## <a name="benefits"></a>Avantajlar
Ürün önerisi stratejisini en iyi duruma getirerek ve iş hedefleri ile hizalayarak çözüm, çevrimiçi satıcıdan Merchandising ve pazarlama hedeflerini karşıladı. Ayrıca, ürün önerisi iş akışını verimli, güvenilir ve uygun maliyetli bir şekilde kullanıma açabilir ve yönetebilir. Yaklaşım, kendi modellerini güncelleştirilmesini ve satış tıklamaları-dönüştürme başarılarına göre verimliliğine ince ayar yapılmasını kolaylaştırır. Azure Data Factory kullanarak, zaman alan ve pahalı, el ile bulut kaynak yönetimini iptal edebilir ve isteğe bağlı bulut kaynak yönetimine geçebilir. Bu nedenle, zaman ve para tasarrufu sağlayabiliyor ve çözüm dağıtımına zaman harcamaları mümkün. Data kökenini görünümleri ve işletimsel hizmet sistem durumu, Azure portal sunulan sezgisel Data Factory izleme ve yönetim kullanıcı arabirimi ile görselleştirme ve sorun giderme konusunda kolay hale geldi. Bunlar artık, tamamlanmış verilerin güvenilir bir şekilde üretilmesi ve kullanıcılara teslim edilmesi için zamanlanabilecek ve yönetilebilir ve veri ve işleme bağımlılıkları, insan müdahalesi olmadan otomatik olarak yönetilir.

Bu kişiselleştirilmiş alışveriş deneyimini sunarak, çevrimiçi satıcı daha rekabet eden, ilgi çekici bir müşteri deneyimi oluşturmuş ve bu nedenle satışları ve genel müşteri memnuniyetini artırmış.

