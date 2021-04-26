---
title: Azure yay bulutu için ölçümler
description: Azure Spring Cloud 'da ölçümleri incelemeyi öğrenin
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 5a8d7d5906a769b778eb8f0ab5abe396a5f2e104
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878805"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure yay bulutu için ölçümler

Azure Ölçüm Gezgini, grafiklerin çizdirme, eğilimleri görsel olarak ilişkilendirme ve ölçümler ve DIB 'leri araştırma olanağı sağlayan Microsoft Azure portal bir bileşenidir. Kaynaklarınızın sistem durumunu ve kullanımını araştırmak için ölçüm gezginini kullanın. 

Azure yay bulutu 'nda ölçümler için iki görünüm noktası vardır.
* Her uygulamaya Genel Bakış sayfasındaki grafikler
* Ortak ölçümler sayfası

 ![Ölçüm grafikleri](media/metrics/metrics-1.png)

Uygulamaya **genel bakış** bölümünde bulunan grafikler, her uygulama için hızlı durum denetimleri sağlar. Ortak **ölçümler** sayfası, başvuru için kullanılabilen tüm ölçümleri içerir. Ortak ölçümler sayfasında kendi grafiklerinizi oluşturabilir ve bunları panoda sabitleyebilirsiniz.

## <a name="application-overview-page"></a>Uygulamaya genel bakış sayfası
Genel Bakış sayfasında grafikleri bulmak için **uygulamalarda** bir uygulama seçin.  

 ![Uygulama ölçümleri yönetimi](media/metrics/metrics-2.png)

Her uygulamanın **genel bakış** sayfasında, uygulamanızda hızlı bir durum denetimi gerçekleştirmenize olanak tanıyan bir ölçüm grafiği sunulmaktadır.  

 ![Uygulama ölçümlerine genel bakış](media/metrics/metrics-3.png)

Azure Spring Cloud, her dakikada güncelleştirilmiş ölçümlerle bu beş grafik sağlar:

* **Http sunucusu hataları**: UYGULAMANıZA yönelik http istekleri için hata sayısı
* **Içindeki veriler**: uygulamanız tarafından alınan bayt
* **Giden veri**: uygulamanız tarafından gönderilen baytlar
* **İstekler**: uygulamanız tarafından alınan istekler
* **Ortalama yanıt süresi**: uygulamanızdan alınan ortalama yanıt süresi

Grafik için bir saatten ve yedi güne kadar bir zaman aralığı seçebilirsiniz.

## <a name="common-metrics-page"></a>Ortak ölçümler sayfası

Sol gezinti bölmesindeki **ölçümler** ortak ölçümler sayfasına bağlanır.

İlk olarak, görüntülenecek ölçümleri seçin:

![Ölçüm görünümü seçin](media/metrics/metrics-4.png)

Tüm ölçüm seçeneklerinin ayrıntıları aşağıdaki [bölümde](#user-metrics-options) bulunabilir.

Ardından, her ölçüm için toplama türünü seçin:

![Ölçüm toplama](media/metrics/metrics-5.png)

Toplama türü, grafikteki ölçüm noktalarının zamana göre nasıl toplanacağını gösterir. Her dakikada bir ham ölçüm noktası vardır ve bir dakika içinde ön toplama türü, ölçüm türü tarafından önceden tanımlanmıştır.
* Sum: tüm değerleri hedef çıktı olarak toplayın.
* Average: dönemdeki ortalama değeri hedef çıktı olarak kullanın.
* Max/Min: dönemde hedef çıktı olarak en fazla/en fazla değeri kullanın.

Zaman aralığı, son 30 dakika ile son 30 güne veya özel bir zaman aralığına göre de ayarlanabilir.

![Ölçüm değişikliği](media/metrics/metrics-6.png)

Varsayılan görünüm, bir Azure yay bulut hizmeti 'nin applicationın ölçümlerini birlikte içerir. Bir uygulama veya örneğin ölçümleri, ekranda filtrelenebilir.  **Filtre Ekle**' ye tıklayın, özelliği **uygulama** olarak ayarlayın ve **değerler** metin kutusunda izlemek istediğiniz hedef uygulamayı seçin. 

İki tür filtre (Özellikler) kullanabilirsiniz:
* Uygulama: uygulama adına göre filtrele
* Örnek: uygulama örneğine göre filtrele

![Ölçüm filtreleri](media/metrics/metrics-7.png)

Ayrıca, bir uygulama için birden çok satır çizecek **bölme Uygula** seçeneğini de kullanabilirsiniz:

![Ölçüm bölme](media/metrics/metrics-8.png)

>[!TIP]
> Ölçümler sayfasında kendi grafiklerinizi oluşturabilir ve bunları **panonuza** sabitleyebilirsiniz. Grafiğinizi adlandırarak başlayın.  Sonra **sağ üst köşedeki panoya sabitle**' yi seçin. Artık uygulamanızı Portal **panonuzda** denetleyebilirsiniz.

## <a name="user-metrics-options"></a>Kullanıcı ölçümleri seçenekleri

Aşağıdaki tablolarda kullanılabilir ölçümler ve Ayrıntılar gösterilmektedir.

### <a name="error"></a>Hata
>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring çalıştırıcı ölçüm adı | Birim | Ayrıntılar |
>|----|----|----|------------|
>| Tomcat. Global. Error | Tomcat. Global. Error | Count | İşlenen isteklerde oluşan hata sayısı |

### <a name="performance"></a>Performans
>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring çalıştırıcı ölçüm adı | Birim | Ayrıntılar |
>|----|----|----|------------|
>| System. CPU. Usage | System. CPU. Usage | Yüzde | Tüm sistem için en son CPU kullanımı (kullanılmıyor ve kullanmayı önermeyin). Bu değer [0.0, 1.0] aralığındaki bir Double değeridir. 0,0 değeri, izlenen son süre boyunca tüm CPU 'ların boşta kaldığı, 1,0 değeri, tüm CPU 'ların, en son dönemde geçen sürenin %100 ' i etkin bir şekilde çalıştırdığı anlamına gelir.|
>| Process. CPU. Usage | Uygulama CPU kullanım yüzdesi | Yüzde | Java Sanal Makinesi işlemi için son CPU kullanımı (kullanılmıyor ve kullanmayı önermeyin). Bu değer [0.0, 1.0] aralığındaki bir Double değeridir. 0,0 değeri, en son geçen süre boyunca JVM işlemindeki iş parçacıklarını hiçbir CPU 'nun çalıştırmadığı anlamına gelir, 1,0 değeri, tüm CPU 'ların, en son dönemde geçen sürenin% JVM 100% ' den etkin bir şekilde çalıştığı anlamına gelir. JVM 'deki iş parçacıkları, uygulama iş parçacıklarını ve JVM iç iş parçacıklarını içerir.|
>| AppCpuUsage | Uygulama CPU Kullanımı | Yüzde | Bu uygulamaya ayrılan CPU 'ya karşı JVM işleminin en son CPU kullanımı, [0.0, 1.0] arasında çift tür değeri. 0,0 değeri, en son geçen süre boyunca JVM işlemindeki iş parçacıklarını hiçbir CPU 'nun çalıştırmadığı anlamına gelir, 1,0 değeri, tüm CPU 'ların, en son dönemde geçen sürenin% JVM 100% ' den etkin bir şekilde çalıştığı anlamına gelir. JVM 'deki iş parçacıkları, uygulama iş parçacıklarını ve JVM iç iş parçacıklarını içerir.|
>| JVM. Memory. taahhüt | JVM. Memory. taahhüt | Bayt | JVM tarafından kullanılabilecek garanti edilen bellek miktarını temsil eder. JVM, belleği sisteme bırakabilir ve yürütme init 'ten az olabilir. taahhüt edilen her zaman en fazla veya daha büyük olur. |
>| JVM. Memory. kullanıldı | JVM. Memory. kullanıldı | Bayt | Bayt cinsinden şu anda kullanılan bellek miktarını temsil eder. |
>| JVM. Memory. Max | JVM. Memory. Max | Bayt | Bellek yönetimi için kullanılabilecek maksimum bellek miktarını temsil eder. En büyük değer tanımlanırsa, kullanılan ve kaydedilmiş bellek miktarı her zaman en fazla eşit veya daha düşük olacaktır. Kullanılan > kullanılan belleği artırmayı denerse bir bellek ayırma başarısız olabilir. <= Max kullanılması hala true (örneğin, sistem sanal belleği düşük olduğunda). |
>| JVM. GC. Max. Data. size | JVM. GC. Max. Data. size | Bayt | Java sanal makinesi başlatıldığından bu yana eski nesil bellek havuzunun en yoğun bellek kullanımı. |
>| JVM. GC. Live. Data. size | JVM. GC. Live. Data. size | Bayt | Tam GC sonrasında eski nesil bellek havuzunun boyutu. |
>| JVM. GC. Memory. yükseltilen | JVM. GC. Memory. yükseltilen | Bayt | GC 'den sonra GC öncesinde, eski nesil bellek havuzunun boyutundaki pozitif artış sayısı. |
>| JVM. GC. Memory. ayrılmış | JVM. GC. Memory. ayrılmış | Bayt | Bir GC 'nin bir sonraki öncesinde bir GC sonrasında küçük kuşak bellek havuzunun boyutunun artması için artırılır. |
>| JVM. GC. Pause. Total. Count | JVM. GC. Pause (Toplam-sayı) | Count | Bu JMV başlatıldıktan sonra, Başak ve eski GC dahil olmak üzere toplam GC sayısı. |
>| JVM. GC. Pause. Total. Time | JVM. GC. Pause (Toplam süre) | Mayacak | Bu JMV başlatıldıktan sonra, Başak ve eski GC dahil olmak üzere tüketilen toplam GC süresi. |

### <a name="performance-net"></a>Performans (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring çalıştırıcı ölçüm adı | Birim | Ayrıntılar |
>|------|-----------------------------|------|---------|
>| CPU kullanımı       | CPU kullanımı      | Yüzde      | İşlemin CPU kullanımının tüm sistem CPU kaynaklarına göre yüzdesi [0-100]. |
>| Çalışma kümesi     | çalışma kümesi    | TI    | İşlem tarafından kullanılan çalışma kümesi miktarı. |
>| GC yığın boyutu    | GC-yığın boyutu   | TI    | Çöp toplayıcı tarafından bildirilen toplam yığın boyutu. |
>| Gen 0 GC sayısı  | Gen-0-GC-Count | Count        | Saniye başına kuşak 0 çöp koleksiyonlarının sayısı. |
>| Gen 1 GC sayısı  | Gen-1-GC-sayısı | Count        | Saniye başına 1. nesil atık koleksiyonları sayısı. |
>| Gen 2 GC sayısı  | Gen-2-GC-sayım | Count        | Saniye başına 2. nesil atık toplama sayısı. |
>| GC zamanı      | timein-GC      | Yüzde      | Son çöp toplamadan bu yana atık toplamada geçen sürenin yüzdesi. |
>| Gen 0 yığın boyutu | Gen-0-boyut     | Bayt        | Nesil 0 yığın boyutu. |
>| Gen 1 yığın boyutu | Gen-1-boyut     | Bayt        | 1. nesil yığın boyutu. |
>| Gen 2 yığın boyutu | Gen-2 boyutu     | Bayt        | 2. nesil yığın boyutu. |
>| LOH yığın boyutu   | Loh-boyut       | Bayt        | Büyük nesne yığın yığın boyutu. |
>| Ayırma oranı | ayırma oranı     | Bayt        | Saniye başına ayrılan bayt sayısı. |
>| Derleme sayısı  | derleme sayısı | Count        | Yüklenen derleme sayısı. |
>| Özel durum sayısı | özel durum-sayı | Count       | Saniye başına özel durum sayısı. |
>| İş parçacığı havuzu iş parçacığı sayısı      | ThreadPool-thread-Count              | Count | İş parçacığı havuzu iş parçacıklarının sayısı. |
>| Kilit çakışması sayısını izle | Monitor-Lock-çekişme-Count        | Count | Bir izleyicinin kilidi alınmaya çalışılırken, saniye başına çekişme sayısı. |
>| İş parçacığı havuzu sıra uzunluğu      | ThreadPool-kuyruk uzunluğu              | Count | İş parçacığı havuzu iş öğeleri sıra uzunluğu. |
>| İş parçacığı havuzu tamamlanan öğe sayısı | ThreadPool-tamamlanan-öğe sayısı | Count | İş parçacığı havuzu tamamlanan iş öğesi sayısı. |
>| Etkin zamanlayıcılar sayısı               | etkin-zamanlayıcı-sayısı               | Count | Şu anda etkin olan zamanlayıcılar sayısı. Etkin bir zamanlayıcı, gelecekte bir noktada Tick 'e kayıtlı ve henüz iptal edilmemiş bir zamanlayıcıya sahiptir. |

Daha fazla bilgi için bkz. [DotNet sayaçları](/dotnet/core/diagnostics/dotnet-counters).

### <a name="request"></a>İstek
>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring çalıştırıcı ölçüm adı | Birim | Ayrıntılar |
>|----|----|----|------------|
>| Tomcat. Global. gönderildi | Tomcat. Global. gönderildi | Bayt | Gönderilen veri miktarı Tomcat Web sunucusu |
>| Tomcat. Global. alındı | Tomcat. Global. alındı | Bayt | Alınan veri miktarı Tomcat Web sunucusu |
>| Tomcat. Global. Request. Total. Count | Tomcat. Global. Request (Toplam sayı) | Count | Toplam Tomcat Web sunucusu işlenen istek sayısı |
>| Tomcat. Global. Request. Max | Tomcat. Global. Request. Max | Mayacak | Bir isteği işlemek için en fazla Tomcat Web sunucusu süresi |

### <a name="request-net"></a>İstek (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring çalıştırıcı ölçüm adı | Birim | Ayrıntılar |
>|------|-----------------------------|------|---------|
>| Saniyedeki istek | saniye başına istek sayısı | Count | İstek hızı. |
>| Toplam istek sayısı | toplam istek sayısı | Count | Toplam istek sayısı. |
>| Geçerli istekler | geçerli istekler | Count | Geçerli istek sayısı. |
>| Başarısız istekler | başarısız-istekler | Count | Başarısız istek sayısı. |

Daha fazla bilgi için bkz. [DotNet sayaçları](/dotnet/core/diagnostics/dotnet-counters).

### <a name="session"></a>Oturum
>[!div class="mx-tdCol2BreakAll"]
>| Name | Spring çalıştırıcı ölçüm adı | Birim | Ayrıntılar |
>|----|----|----|------------|
>| Tomcat. Sessions. Active. Max | Tomcat. Sessions. Active. Max | Count | Aynı anda etkin olan en fazla oturum sayısı |
>| Tomcat. Sessions. canlı. Max | Tomcat. Sessions. canlı. Max | Mayacak | Süresi biten bir oturumun etkin olduğu en uzun süre (saniye cinsinden) |
>| Tomcat. Sessions. oluşturuldu | Tomcat. Sessions. oluşturuldu | Count | Oluşturulan oturum sayısı |
>| Tomcat. Sessions. süre geçildi | Tomcat. Sessions. süre geçildi | Count | Zaman aşımına uğramış oturum sayısı |
>| Tomcat. Sessions. reddedildi | Tomcat. Sessions. reddedildi | Count | En fazla etkin oturum sayısına ulaşıldığından, oluşturulmayan oturum sayısı. |
>| Tomcat. Sessions. ACTIVE. Current | Tomcat. Sessions. ACTIVE. Current | Count | Tomcat oturumu etkin sayısı |

## <a name="see-also"></a>Ayrıca bkz.

* [Hızlı başlangıç: Günlükler, ölçümler ve izleme ile Azure yay bulut uygulamalarını Izleme](spring-cloud-quickstart-logs-metrics-tracing.md)

* [Azure Ölçüm Gezgini'ni kullanmaya başlama](../azure-monitor/essentials/metrics-getting-started.md)

* [Tanılama ayarlarıyla günlükleri ve ölçümleri çözümleme](./diagnostic-services.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: uyarıları ve eylem gruplarını kullanarak yay bulut kaynaklarını Izleme](./spring-cloud-tutorial-alerts-action-groups.md)

* [Azure yay bulutu için kotalar ve hizmet planları](./spring-cloud-quotas.md)
