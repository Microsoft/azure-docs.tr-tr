---
title: Azure Cosmos DB ile yerleşik analiz için kullanım örnekleri.
description: Farklı kullanım durumlarında Azure Cosmos DB ile yerleşik analiz kullanmayı öğrenin.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757069"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Azure Cosmos DB ile yerleşik analiz için kullanım örnekleri

Aşağıdaki kullanım örnekleri, Azure Cosmos DB Apache Spark yerleşik analizler kullanılarak elde edilebilir:

## <a name="htap-scenarios"></a>HTAP senaryoları

Azure Cosmos DB içindeki yerleşik analizler şu şekilde kullanılabilir:

* İşlem işleme sırasında sahtekarlık algıla.
* Bir ECommerce Mağazası 'na göz atarken alışverişçilere öneriler sağlayın.
* Uyarı işleçlerini, önemli bir üretim donanımı parçasının olası hatasına karşı uyarır.
* Gerçek zamanlı analizleri doğrudan işletimsel verilere gömerek hızlı, eyleme dönüştürülebilir Öngörüler oluşturun.

Azure Cosmos DB, yerel olarak yerleşik Apache Spark kullanarak karma Işlem/analitik Işleme (HTAP) senaryolarını destekler. Azure Cosmos DB geleneksel sistemlerle birlikte gelen işletimsel ve analitik ayrımı ortadan kaldırır.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Herhangi bir ETL gerekmeden Global olarak dağıtılmış veri Gölü

Yerel olarak yerleşik Apache Spark Azure Cosmos DB, tek bir sistem görüntüsü sağlayan küresel olarak dağıtılan bir veri Gölü oluşturmak için hızlı, basit ve ölçeklenebilir bir yol sağlar. Küresel olarak dağıtılan çok modelli veriler, pahalı ETL işlem hattına yatırım yapmak ve isteğe bağlı olarak ölçeklendirmenin yanı sıra veri ekibinin veri kümelerini çözümleme şeklini ortadan kaldırır.

## <a name="time-series-analytics-over-historic-data"></a>Geçmiş veriler üzerinde zaman serisi analizi

Bazı durumlarda, geçmişte gerçekleşen olaylar üzerinde belirli bir zamanda, verileri temel alan soruları cevaplamak gerekebilir. Örneğin, belirli bir tarihteki CRM etkinlik durumlarının sayısını almak için. Raporu bir hafta önce çalıştırdıysanız, durum sayısı o zaman içindeki her bir etkinliğin durumlarına göre olur. Aynı raporu bugün çalıştırmak, durumları bugün olduğu gibi, son haftadan bu yana değişmiş olabilecek etkinliklerin sayısını, açık olan yaşam döngülerine giderek bu tarihten itibaren değiştirmiş olacak şekilde verir. Bu nedenle, büyük/küçük dönemin yaşam döngüsünün her aşamasında anlık görüntüye rapor etmeniz gerekir.

Geleneksel veri ambarı senaryolarında, veri ambarları kendisini içerecek şekilde tasarlanmadığından ve veriler yalnızca geçerli şeyler için geçerli bir görünüm sağladığından anlık görüntü kavramı mümkün değildir. Azure Cosmos DB, kullanıcılar zaman gezimi kavramını uygulama olasılığına sahiptir, verileri geriye dönük olarak sorgulayabilir ve çalıştırabilir ve verilerin geçmişte belirli bir zaman noktasında nasıl aranmasını isteyebilir. Bu, kullanıcıların hem geçerli hem geçmiş görünümlerini kolayca görüntüleyebileceği ve üzerinde analiz çalıştırabileceği anlamına gelir.

## <a name="globally-distributed-machine-learning-and-ai"></a>Küresel olarak dağıtılmış makine öğrenimi ve AI

İşletmeler hızlı bir şekilde büyüyen veri hacimlerini ve çeşitli veri türleri ve biçimlerini genişleterek, daha derin ve daha doğru Öngörüler elde etme olanağı dünya genelinde petaborlar üzerinde imkansız hale gelebilir. Yerel olarak yerleşik Apache Spark, Azure Cosmos DB, makine öğrenimi algoritmalarının kapsamlı kitaplığını sunan küresel bir şekilde dağıtılmış analiz platformu sağlar. Etkileşimli Jupyter not defterlerini, modeller oluşturup eğitme ve küme yönetimi özellikleri oluşturmak ve eğitebilmeniz için kullanabilirsiniz. Bu yetenekler, isteğe bağlı olarak yüksek düzeyde ayarlanmış ve otomatik elastik Spark kümeleri sağlamanıza olanak tanır.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Çok modelli Global olarak dağıtılmış veriler hakkında derin öğrenme

Derin öğrenme, veri hacmi ve karmaşıklık büyümeye devam ettiğinden büyük veri tahmine dayalı analiz çözümleri sağlamanın ideal yoludur. Derin öğrenme sayesinde işletmeler, yapılandırılmamış ve yarı yapılandırılmış verilerin gücünden yararlanarak AI, doğal dil işleme ve daha fazlası gibi tekniklerden yararlanan kullanım örnekleri sunar.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Raporlama (Power Apps, Power BI ile tümleştirme)

Power BI, self servis iş zekası özelliklerine sahip etkileşimli görselleştirmeler sunarak son kullanıcıların kendi kendilerine rapor ve Pano oluşturmalarına olanak sağlar. Yerleşik Spark bağlayıcısını kullanarak, Power BI Desktop Azure Cosmos DB Apache Spark kümelerine bağlayabilirsiniz. Bu bağlayıcı, Power BI yüklemek istemediğiniz veya neredeyse gerçek zamanlı analiz yapmak istediğiniz büyük miktarda veriniz olduğunda harika olan Azure Cosmos DB Apache Spark işleme yükünü devretmek için doğrudan sorgu kullanmanıza olanak sağlar.

## <a name="iot-analytics-at-global-scale"></a>Küresel ölçekte IoT Analizi

Artan ağ sensörlerinden oluşturulan veriler, daha önce opak sistemler ve işlemlere, eski bir görünürlük getirir. Bu anahtar, IoT cihazlarının dünya genelinde dağıtıldığı durumlar ne olursa olsun, bu bilgilerin bu şekilde bu şekilde, eyleme dönüştürülebilir Öngörüler bulmaktadır. Azure Cosmos DB, ıOT şirketlerinin yüksek hızlı algılayıcı ve zaman serisi verilerini dünyanın her yerinden gerçek zamanlı olarak analiz etmesine olanak tanır. Geliştirilmiş müşteri deneyimleri, operasyonel verimlilik ve yeni gelir fırsatları sunmak için, birbirine bağlı bir dünyanın gerçek değerini bir şekilde kullanmanıza olanak sağlar.

## <a name="stream-processing-and-event-analytics"></a>Akış işleme ve olay Analizi 

Günlük dosyalarından Sensör verilerine kadar, işletmeler "akışlar" veri ile iş ihtiyacını artmaktadır. Bu veriler, genellikle birden çok kaynaktan aynı anda düzenli bir akışa ulaşır. Bu veri akışlarını diskte depolamak ve bunları geriye dönük olarak çözümlemek uygun olsa da, bu durum bazen, ulaştığı sırada verileri işlemek ve üzerinde işlem yapmak için uygun veya önemli olabilir. Örneğin, finansal işlemlerle ilgili veri akışları gerçek zamanlı olarak işlenebilir ve potansiyel olarak sahte işlemleri tespit edebilir ve reddedebilir.

## <a name="interactive-analytics"></a>Etkileşimli analiz

Satış, üretkenlik veya stok fiyatları için statik panolar oluşturmak üzere önceden tanımlanmış sorguları çalıştırmanın yanı sıra, verileri etkileşimli olarak araştırmak isteyebilirsiniz. Etkileşimli analiz, soru sormanız, sonuçları görüntülemeniz, ilk soruyu yanıta göre değiştirmek veya sonuçlara daha ayrıntılı bir bakış sağlamak için izin verir. Azure Cosmos DB Apache Spark, kolayca yanıt vererek ve uyarlanarak etkileşimli sorguları destekler.

## <a name="data-exploration-using-jupyter-notebooks"></a>Jupyıter not defterlerini kullanarak veri araştırması

Yeni bir veri kümeniz olduğunda, çalışma modellerini ve Testleri çalıştırmadan önce verilerinizi incelemeniz gerekir. Diğer bir deyişle, araştırmacı veri analizi yapmanız gerekir. Veri araştırması çeşitli kararlara bilgi verebilir. Örneğin, verilerinizde kullanılmak üzere uygun olan yöntemler gibi ayrıntıları, verilerin temizlenmesi, yeniden yapılandırılması vb. gibi belirli modelleme varsayımlarını karşılayıp karşılamadığını fark etmeksizin bulabilirsiniz. Azure Cosmos DB yerel olarak yerleşik jupi not defterlerini ve Apache Spark kullanarak, işlem ve analitik veriler üzerinde hızlı ve etkili araştırmacı veri analizi yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu kullanım durumlarını kullanmaya başlamak için aşağıdaki makalelere gidin:

* [Azure Cosmos DB yerleşik jupi Not defterleri](cosmosdb-jupyter-notebooks.md)
* [Azure Cosmos hesapları için not defterlerini etkinleştirme](enable-notebooks.md)
* [Verileri analiz etmek ve görselleştirmek için bir not defteri oluşturun](create-notebook-visualize-data.md)