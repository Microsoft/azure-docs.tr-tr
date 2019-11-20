---
title: Azure blok zinciri çalışma ekranı 'nda akıllı sözleşme tümleştirme desenleri
description: Azure blok zinciri çalışma ekranı önizlemesinde akıllı sözleşme tümleştirme düzenlerine genel bakış.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 7176cddae6affd19df4fe418afcf420f92d624ab
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579783"
---
# <a name="smart-contract-integration-patterns"></a>Akıllı anlaşma tümleştirme desenleri

Akıllı sözleşmeler genellikle dış sistemlerle ve cihazlarla tümleştirilmesi gereken iş akışını temsil eder.

Bu iş akışlarının gereksinimleri, bir dış sistem, hizmet veya cihazdan veri içeren dağıtılmış bir defter üzerinde işlem başlatma gereksinimi içerir. Ayrıca, dağıtılmış bir muhasebedeki akıllı sözleşmelerden kaynaklanan olaylara yanıt vermek için dış sistemlerin da olması gerekir.

REST API ve mesajlaşma tümleştirmesi, dış sistemlerden Azure blok zinciri çalışma ekranı uygulamasına dahil edilen akıllı sözleşmelere işlem gönderir. Ayrıca, bir uygulama içinde gerçekleşen değişikliklere göre dış sistemlere olay bildirimleri gönderir.

Veri tümleştirme senaryolarında, Azure blok zinciri çalışma ekranı blok zincirinden ve uygulamalar ve akıllı sözleşmeler hakkında meta verilerden işlem verilerinin bir birleşimini birleştirerek oluşan bir veritabanı görünümleri kümesi içerir.

Ayrıca, tedarik zinciri veya medya ile ilgili olanlar gibi bazı senaryolar da belgelerin tümleştirilmesini gerektirebilir. Azure blok zinciri çalışma ekranı belgeleri doğrudan işlemek için API çağrıları sağlamıyorsa, belgeler blok zinciri uygulamasına eklenebilir. Bu bölümde ayrıca bu desenler de bulunur.

Bu bölüm, uçtan uca çözümlerinde bu tür tümleştirmelerin her birini uygulamak için tanımlanan desenleri içerir.

## <a name="rest-api-based-integration"></a>REST API tabanlı tümleştirme

Azure blok zinciri çalışma ekranı tarafından oluşturulan Web uygulaması içindeki yetenekler REST API aracılığıyla sunulur. Yetenekler, Azure blok zinciri çalışma ekranı yükleme, uygulamaların yapılandırılması ve yönetimi, dağıtılmış bir genel muhasebeye işlem gönderme ve uygulama meta verilerinin ve muhasebe verilerinin sorgulanmasına dahildir.

REST API, öncelikle web, mobil ve bot uygulamaları gibi etkileşimli istemciler için kullanılır.

Bu bölüm, dağıtılmış bir genel muhasebe ve Azure blok zinciri çalışma ekranı *zinciri* SQL veritabanı 'ndan işlemler hakkındaki verileri sorgulayan REST API yönlerini odaklanan desenlere bakar.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Dış sistemden dağıtılmış bir genel muhasebeye işlem gönderme

Azure blok zinciri çalışma ekranı REST API dağıtılmış bir muhasebedeki işlemleri yürütmek için kimliği doğrulanmış istekleri gönderir.

![Dağıtılmış bir genel muhasebeye işlem gönderme](./media/integration-patterns/send-transactions-ledger.png)

İşlemleri yürütmek, daha önce gösterilen işlemi kullanarak oluşur, burada:

-   Dış uygulama, Azure blok zinciri çalışma ekranı dağıtımının bir parçası olarak sağlanan Azure Active Directory kimliğini doğrular.
-   Yetkili kullanıcılar, API 'ye isteklerle gönderilebilecek bir taşıyıcı belirteci alır.
-   Dış uygulamalar, REST API taşıyıcı belirtecini kullanarak çağrılar yapar.
-   REST API, isteği bir ileti olarak paketler ve Service Bus gönderir. Buradan alınır, imzalanır ve uygun dağıtılmış bir genel muhasebeye gönderilir.
-   REST API, Azure blok zinciri için SQL DB 'nin isteği kaydetmesine ve geçerli sağlama durumunu kurmaya yönelik bir istek yapar.
-   SQL DB, sağlama durumunu döndürür ve API çağrısı KIMLIĞI onu çağıran dış uygulamaya döndürür.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Blok zinciri çalışma ekranı meta verileri ve dağıtılmış genel muhasebe işlemleri sorgulanıyor

Azure blok zinciri çalışma ekranı REST API, dağıtılmış bir muhasebedeki akıllı sözleşme yürütme ile ilgili ayrıntıları sorgulamak için kimliği doğrulanmış istekleri gönderir.

![Meta veriler sorgulanıyor](./media/integration-patterns/querying-metadata.png)

Sorgulama, daha önce gösterilen işlemi kullanarak gerçekleşir, burada:

1. Dış uygulama, Azure blok zinciri çalışma ekranı dağıtımının bir parçası olarak sağlanan Azure Active Directory kimliğini doğrular.
2. Yetkili kullanıcılar, API 'ye isteklerle gönderilebilecek bir taşıyıcı belirteci alır.
3. Dış uygulamalar, REST API taşıyıcı belirtecini kullanarak çağrılar yapar.
4. REST API SQL DB 'den istek için verileri sorgular ve istemciye döndürür.

## <a name="messaging-integration"></a>Mesajlaşma tümleştirmesi

Mesajlaşma tümleştirmesi, etkileşimli bir oturum açma mümkün veya istenmediğinde sistemlerle, hizmetlerle ve cihazlarla etkileşimi kolaylaştırır. Mesajlaşma tümleştirmesi iki tür iletiye odaklanır: işlem yapan iletiler dağıtılmış bir muhasebede yürütülür ve işlemler gerçekleştiği sırada bu muhasebe tarafından kullanıma sunulan olaylardır.

Mesajlaşma tümleştirmesi, Kullanıcı oluşturma, sözleşme oluşturma ve sözleşmelerin üzerinde işlem yürütme ile ilgili işlemlerin yürütülmesine ve izlenmesine odaklanır ve öncelikle *gözetimsiz* arka uç sistemleri tarafından kullanılır.

Bu bölüm, dağıtılmış bir defter ve temel alınan dağıtılmış defter tarafından kullanıma sunulan olay iletilerini temsil eden desenlere işlem gönderen ileti tabanlı API 'nin yönlerine odaklanan desenlere bakar.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Bir akıllı sözleşmeden bir olay tüketicisine tek yönlü olay teslimi 

Bu senaryoda, bir akıllı sözleşmede, örneğin bir durum değişikliği veya belirli bir işlem türünün yürütülmesi gibi bir olay oluşur. Bu olay, bir Event Grid aşağı akış tüketicilerine ve bu tüketicilerle ilgili işlemleri gerçekleştirebilir.

Bu senaryoya örnek olarak, bir işlem gerçekleştiğinde bir tüketici uyarı verebilir ve bilgileri bir SQL VERITABANıNA veya Common Data Service kaydetme gibi işlemler gerçekleştirebilir. Bu senaryo, SQL DB 'nin *kapalı zincirini* doldurmak için aşağıdaki şekilde kullanılan modeldir.

Başka bir deyişle, bir anlaşma bir *Outofuyum*içine geçtiğinde, bir akıllı anlaşma belirli bir duruma geçiş yaptığında olur. Bu durum değişikliği gerçekleştiğinde, bir uyarının bir yöneticinin cep telefonuna gönderilmesini tetikleyemeyebilir.

![Tek yönlü olay teslimi](./media/integration-patterns/one-way-event-delivery.png)

Bu senaryo, daha önce gösterilen işlemi kullanarak oluşur; burada:

-   Akıllı sözleşme yeni bir duruma geçiş yapar ve bir olayı genel muhasebeye gönderir.
-   Genel muhasebe, olayı alır ve Azure blok zinciri çalışma ekranına gönderir.
-   Azure blok zinciri çalışma ekranı, genel muhasebedeki olaylara abone olur ve olayı alır.
-   Azure blok zinciri çalışma ekranı, etkinliği Event Grid abonelere yayımlar.
-   Dış sistemler Event Grid abone olunmuş, iletiyi tüketir ve uygun eylemleri gerçekleştirebilir.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Bir dış sistemden bir akıllı sözleşmeye yönelik bir iletinin tek yönlü olay teslimi

Ayrıca, karşıt yönden akan bir senaryo da vardır. Bu durumda, bir olay bir algılayıcı veya bir dış sistem tarafından oluşturulur ve bu olaydan gelen verilerin bir akıllı sözleşmeye gönderilmesi gerekir.

Yaygın olarak kullanılan örnek, finans pazarlarından veri tesliminin (örneğin, Commodities, hisse senedi veya Bonun fiyatları) akıllı bir sözleşmeye gönderilir.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Beklenen biçimde bir Azure blok zinciri çalışma ekranının doğrudan teslimi

Bazı uygulamalar, Azure blok zinciri çalışma ekranı ile tümleştirilecek şekilde oluşturulmuştur ve beklenen biçimlerde iletileri doğrudan oluşturur ve gönderir.

![Doğrudan teslim](./media/integration-patterns/direct-delivery.png)

Bu teslim, daha önce gösterilen işlem kullanılarak oluşur; burada:

-   Bir dış sistemde, Azure blok zinciri çalışma ekranı için ileti oluşturmayı tetikleyen bir olay oluşur.
-   Dış sistemin, bu iletiyi bilinen bir biçimde oluşturmak ve doğrudan Service Bus göndermesi için yazıldığı kodu vardır.
-   Azure blok zinciri çalışma ekranı, Service Bus olaylara abone olur ve iletiyi alır.
-   Azure blok zinciri çalışma ekranı, dış sistemden belirli bir sözleşmeye veri gönderen bir genel muhasebeye çağrı başlatır.
-   İleti alındıktan sonra, sözleşme yeni bir duruma geçer.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Azure blok zinciri için bilinmeyen bir biçimde ileti teslimi çalışma ekranı

Bazı sistemler, Azure blok zinciri çalışma ekranı tarafından kullanılan standart biçimlerdeki iletileri teslim etmek üzere değiştirilemez. Bu durumlarda, bu sistemlerdeki mevcut mekanizmalar ve ileti biçimleri genellikle kullanılabilir. Özellikle, bu sistemlerin yerel ileti türleri, beklenen standart mesajlaşma biçimlerinden biriyle eşlenecek Logic Apps, Azure Işlevleri veya diğer özel kodlar kullanılarak dönüştürülebilir.

![Bilinmeyen ileti biçimi](./media/integration-patterns/unknown-message-format.png)

Bu, daha önce gösterilen işlemi kullanarak oluşur; burada:

-   Bir dış sistemde bir ileti oluşturulmasını tetikleyen bir olay oluşur.
-   Bir mantıksal uygulama veya özel kod, bu iletiyi almak ve standart bir Azure blok zinciri çalışma ekranı biçimli iletiye dönüştürmek için kullanılır.
-   Mantıksal uygulama, dönüştürülmüş iletiyi doğrudan Service Bus gönderir.
-   Azure blok zinciri çalışma ekranı, Service Bus olaylara abone olur ve iletiyi alır.
-   Azure blok zinciri çalışma ekranı, verileri dış sistemden sözleşmede belirli bir işleve gönderen bir genel muhasebeye çağrı başlatır.
-   İşlev yürütülür ve genellikle durumu değiştirir. Durum değişikliği, akıllı sözleşmede yansıtıldığı iş iş akışını ileri doğru şekilde yürütülene kadar, diğer işlevlerin de uygun şekilde yürütülmesini sağlar.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Denetimi bir dış işleme geçme ve tamamlanmayı bekleme

Bir akıllı sözleşmenin iç yürütmeyi durdurması ve harici bir işleme kapalı olması gereken senaryolar vardır. Daha sonra bu dış işlem tamamlanır, akıllı sözleşmeye bir ileti gönderir ve ardından yürütme akıllı sözleşme içinde devam edecektir.

#### <a name="transition-to-the-external-process"></a>Dış işleme geçiş

Bu model genellikle aşağıdaki yaklaşım kullanılarak uygulanır:

-   Akıllı sözleşme belirli bir duruma geçiş yapar. Bu durumda, bir dış sistem istenen bir eylem yapana kadar hiçbir veya sınırlı sayıda işlev Yürütülebilirler.
-   Durum değişikliği, bir aşağı akış tüketiciye olay olarak ortaya çıkmış.
-   Aşağı akış tüketicisi olayı alır ve dış kod yürütmeyi tetikler.

![Dış işleme geçiş denetimi](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Akıllı sözleşmeden denetim döndürme

Dış sistemi özelleştirme yeteneğinin ne olduğuna bağlı olarak, Azure blok zinciri çalışma ekranının beklediği standart biçimlerden birinde iletileri teslim edebilir veya mümkün olmayabilir. Dış sistemleri temel alan bu iletilerden birini oluşturma özelliği, aşağıdaki iki dönüş yolundan hangisinin alındığını tespit edilir.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Beklenen biçimde bir Azure blok zinciri çalışma ekranının doğrudan teslimi

![](./media/integration-patterns/direct-delivery.png)

Bu modelde, sözleşmeye ve sonraki durum değişikliğine yönelik iletişim, önceki işlem sonrasında oluşur-

-   Dış kod yürütmesindeki tamamlamaya veya belirli bir kilometre taşına ulaştıktan sonra Azure blok zinciri çalışma ekranına bağlı Service Bus bir olay gönderilir.

-   API 'nin beklentilerine uyan bir ileti yazmak üzere doğrudan uyarlanabilen sistemler için, dönüştürülür.

-   İleti içeriği paketlenir ve akıllı sözleşmede belirli bir işleve gönderilir. Bu teslim, dış sistemle ilişkili kullanıcı adına yapılır.

-   İşlev yürütülür ve genellikle durumu değiştirir. Durum değişikliği, akıllı sözleşmede yansıtıldığı iş iş akışını ileri doğru şekilde yürütülene kadar, diğer işlevlerin de uygun şekilde yürütülmesini sağlar.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Azure blok zinciri için bilinmeyen bir biçimde ileti teslimi çalışma ekranı

![Bilinmeyen ileti biçimi](./media/integration-patterns/unknown-message-format.png)

Standart biçimdeki bir iletinin doğrudan gönderilemediği bu modelde, sözleşmeye ve sonraki durum değişikliğine yönelik iletişim, şu durumlarda bir önceki işlemden sonra oluşur:

1.  Dış kod yürütmesindeki tamamlamaya veya belirli bir kilometre taşına ulaştıktan sonra Azure blok zinciri çalışma ekranına bağlı Service Bus bir olay gönderilir.
2.  Bir mantıksal uygulama veya özel kod, bu iletiyi almak ve standart bir Azure blok zinciri çalışma ekranı biçimli iletiye dönüştürmek için kullanılır.
3.  Mantıksal uygulama, dönüştürülmüş iletiyi doğrudan Service Bus gönderir.
4.  Azure blok zinciri çalışma ekranı, Service Bus olaylara abone olur ve iletiyi alır.
5.  Azure blok zinciri çalışma ekranı, dış sistemden belirli bir sözleşmeye veri gönderen bir genel muhasebeye çağrı başlatır.
6. İleti içeriği paketlenir ve akıllı sözleşmede belirli bir işleve gönderilir. Bu teslim, dış sistemle ilişkili kullanıcı adına yapılır.
7.  İşlev yürütülür ve genellikle durumu değiştirir. Durum değişikliği, akıllı sözleşmede yansıtıldığı iş iş akışını ileri doğru şekilde yürütülene kadar, diğer işlevlerin de uygun şekilde yürütülmesini sağlar.

## <a name="iot-integration"></a>IoT tümleştirmesi

Ortak bir tümleştirme senaryosu, bir akıllı sözleşmede sensörlerden alınan Telemetri verilerinin içermesidir. Sensörler tarafından teslim edilen verilere bağlı olarak, akıllı sözleşmeler bilinçli eylemler alabilir ve sözleşmenin durumunu değiştirebilir.

Örneğin, bir kamyonun sıcaklığını 110 derecenin altına çıkarıyorsa, ilaç 'nın verimliliğini etkileyebilir ve tedarik zincirinde algılanmadığında ve kaldırılmadığında ortak bir güvenlik sorununa neden olabilir. Bir sürücü, arabasını saat başına 100 mil olarak hızlandırdıysanız, sonuçta elde edilen algılayıcı bilgileri sigorta sağlayıcısı tarafından sigorta iptali tetiklenebilir. Araba bir kiralama arabasında ise, GPS verileri, sürücünün kiralama anlaşmasıyla ele alınan bir Coğrafya dışında ne zaman başladığını belirtebilir ve ceza ücreti ödemenize neden olabilir.

Sınama, bu sensörlerden sabit bir şekilde veri teslim edilebilir ve bu verilerin tümünü akıllı bir sözleşmeye göndermek uygun değildir. Tipik bir yaklaşım, tüm iletileri ikincil depoya teslim ederken blok zincirine gönderilen ileti sayısını sınırlandırmalıdır. Örneğin, yalnızca sabit aralıklarla alınan iletileri, örneğin saatte bir kez ve kapsanan bir değer bir akıllı sözleşme için kabul edilen bir değerin dışında kaldığında teslim edin. Toleranslar dışında kalan değerler kontrol edilirken, sözleşmelerin iş mantığı ile ilgili verilerin alınıp yürütülmesi sağlanır. Aralıktaki değeri denetlemek, sensörin hala raporlama olduğunu onaylar. Tüm veriler, daha geniş raporlama, analiz ve makine öğrenimini sağlamak için ikincil bir raporlama deposuna gönderilir. Örneğin, GPS için sensörler için bir akıllı sözleşme için her dakika gerek duyulmayabilir, ancak raporlarda veya eşleme rotalarında kullanılacak ilginç veriler sağlayabilir.

Azure platformunda cihazlarla tümleştirme, genellikle IoT Hub ile yapılır. IoT Hub, iletilerin içeriğe göre yönlendirilmesini sağlar ve daha önce açıklanan işlevselliğin türünü sağlar.

![IoT iletileri](./media/integration-patterns/iot.png)

İşlem bir model gösterir:

-   Bir cihaz, IoT Hub için doğrudan veya bir alan ağ geçidi aracılığıyla iletişim kurar.
-   IoT Hub iletileri alır ve ileti içeriğini kontrol eden yollarla iletileri değerlendirir (örneğin,). *Algılayıcı 50 dereceden büyük bir sıcaklık rapor veriyor mu?*
-   IoT Hub, ölçütlere uyan iletileri yol için tanımlı bir Service Bus gönderir.
-   Bir mantıksal uygulama veya diğer kod, IoT Hub yol için kurduğu Service Bus dinler.
-   Mantıksal uygulama veya diğer kod, iletiyi alır ve bilinen bir biçime dönüştürür.
-   Dönüştürülmüş ileti, artık standart biçimde, Azure blok zinciri çalışma ekranı için Service Bus gönderilir.
-   Azure blok zinciri çalışma ekranı, Service Bus olaylara abone olur ve iletiyi alır.
-   Azure blok zinciri çalışma ekranı, dış sistemden belirli bir sözleşmeye veri gönderen bir genel muhasebeye çağrı başlatır.
-   İletinin alınması sırasında, sözleşme verileri değerlendirir ve bu değerlendirmenin sonucuna göre durumu değiştirebilir (örneğin, yüksek bir sıcaklık için durumu *uyumsuz*olarak değiştirin).

## <a name="data-integration"></a>Veri tümleştirmesi

REST ve ileti tabanlı API 'nin yanı sıra Azure blok zinciri çalışma ekranı Ayrıca, uygulama ve Sözleşme meta verilerinin yanı sıra dağıtılmış defterlerden işlem verileri ile doldurulmuş bir SQL DB 'ye erişim sağlar.

![Veri tümleştirmesi](./media/integration-patterns/data-integration.png)

Veri tümleştirmesi iyi bilinmektedir:

-   Azure blok zinciri, uygulamalar, iş akışları, sözleşmeler ve işlemler hakkındaki meta verileri normal çalışma davranışının bir parçası olarak depolar.
-   Dış sistemler veya Araçlar veritabanı sunucu adı, veritabanı adı, kimlik doğrulama türü, oturum açma kimlik bilgileri ve hangi veritabanı görünümlerinin kullanıldığı hakkında bilgi toplamayı kolaylaştırmak için bir veya daha fazla iletişim kutusu sağlar.
-   Dış sistemler, hizmetler, raporlama, geliştirici araçları ve kurumsal üretkenlik araçları tarafından aşağı akış tüketimini kolaylaştırmak için sorgular SQL veritabanı görünümlerine karşı yazılır.

## <a name="storage-integration"></a>Depolama tümleştirmesi

Birçok senaryo, attestable dosyalarını birleştirme gereksinimini gerektirebilir. Birden çok nedenden dolayı, dosyaları bir blok zincirine yerleştirmek uygun değildir. Bunun yerine, yaygın bir yaklaşım bir dosyaya karşı şifreleme karması (örneğin, SHA-256) gerçekleştirmek ve bu karmayı dağıtılmış bir defter üzerinde paylaşmalıdır. Daha sonra herhangi bir zamanda karmayı yeniden gerçekleştirmek aynı sonucu döndürmelidir. Dosya değiştirilirse, görüntüde yalnızca bir piksel değiştirilse bile, karma farklı bir değer döndürür.

![Depolama tümleştirmesi](./media/integration-patterns/storage-integration.png)

Bu model şu şekilde uygulanabilir:

-   Bir dış sistem, Azure depolama gibi bir depolama mekanizmasında bir dosyayı sürdürür.
-   Bir karma dosya veya dosya ile, sahip için bir tanımlayıcı, dosyanın bulunduğu URL, vs. gibi ilişkili meta veriler ile oluşturulur.
-   Karma ve tüm meta veriler akıllı bir sözleşmede bir işleve gönderilir; Örneğin, *dosya eklendi*
-   Gelecekte dosya ve meta veriler karma hale getirilir ve bu, genel muhasebede depolanan değerlerle karşılaştırılır.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>REST ve ileti API 'Lerini kullanarak tümleştirme desenleri uygulama önkoşulları

Bir dış sistemin veya cihazın, REST veya ileti API 'sini kullanarak akıllı sözleşmeyle etkileşime geçmesini kolaylaştırmak için aşağıdakiler gerçekleşmelidir-

1. Konsorsiyumun Azure Active Directory, dış sistemi veya cihazı temsil eden bir hesap oluşturulur.
2. Azure blok zinciri çalışma ekranı uygulamanız için bir veya daha fazla uygun akıllı sözleşme, dış sisteminizden veya cihazınızdan olayları kabul edecek şekilde tanımlı işlevlere sahip.
3. Akıllı sözleşmeniz için uygulama yapılandırma dosyası, sistemin veya cihazın atandığı rolü içerir.
4. Akıllı sözleşmeniz için uygulama yapılandırma dosyası, bu işlevin tanımlanan rol tarafından çağrıldığı durumları tanımlar.
5. Uygulama yapılandırma dosyası ve akıllı sözleşmeleri Azure blok zinciri çalışma ekranı 'na yüklendi.

Uygulama karşıya yüklendikten sonra, dış sistem için Azure Active Directory hesabı sözleşmeye ve ilişkili role atanır.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Tümleştirme kodu yazmadan önce dış sistem tümleştirme akışlarını test etme 

Dış sistemlerle tümleştirme pek çok senaryonun önemli bir gereksinimidir. Dış sistemlerle tümleştirme için kodun geliştirilmesine paralel olarak veya paralel olarak, akıllı sözleşme tasarımını doğrulayabilmek istenebilir.

Azure Active Directory (Azure AD) kullanımı, geliştirici üretkenliğini ve değere göre süreyi önemli ölçüde hızlandırmanızı sağlayabilir. Özellikle, bir dış sistemle kod tümleştirmesi, önemsiz olmayan bir süre alabilir. Azure AD 'yi ve UX 'i Azure blok zinciri çalışma ekranı tarafından otomatik olarak oluşturmayı kullanarak, geliştiricilerin dış sistem olarak blok zinciri çalışma ekranına oturum açmasını ve UX aracılığıyla dış sistemden değer doldurmasına izin verebilirsiniz. Tümleştirme kodu dış sistemler için yazılmadan önce kavram kanıtı ortamında hızlıca geliştirme ve doğrulama yapabilirsiniz.
