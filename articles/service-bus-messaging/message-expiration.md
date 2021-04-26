---
title: Azure Service Bus-ileti süre sonu
description: Bu makalede Azure Service Bus iletilerinin yaşam süresi ve yaşam süresi hakkında bilgi verilmektedir. Bu süre dolduktan sonra ileti artık teslim alınmaz.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 5d60d84bdc0d437d97c369296a414d55beda4167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952329"
---
# <a name="message-expiration-time-to-live"></a>İleti süre sonu (Yaşam Süresi)
Bir iletideki yük veya bir iletinin alıcıyı ileten bir komut ya da sorgulama, neredeyse her zaman bir uygulama düzeyi sona erme tarihi biçimine tabidir. Bu süre sonunda, içerik artık teslim edilmemiş veya istenen işlem artık yürütülmemiş.

Kuyrukların ve konuların genellikle uygulamaların veya uygulama bölümlerinin kısmi çalıştırmaları bağlamında kullanıldığı geliştirme ve test ortamları için, bir sonraki test çalışmasının temizlemeye başlayabilmesi için, yabanlanılan test iletilerinin otomatik olarak atık toplanmasını de tercih edilir.

Her bir ileti için süre sonu, göreli bir süre belirten **yaşam süresi** sistem özelliği ayarlanarak denetlenebilir. İleti varlığa sıralandığında süre sonu mutlak bir anlık olur. Bu tarihte, Expires- **UTC** özelliği, **sıraya alınan saat-UTC**  +  **yaşam süresi** değerini alır. Aracılı bir iletideki yaşam süresi (TTL) ayarı, etkin bir istemci olmadığında zorunlu değildir.

UTC 'den **süresi dolan** anlık iletiler, geri alma için uygun hale gelir. Süre sonu, şu anda teslim için kilitli iletileri etkilemez. Bu iletiler normal olarak yine de işlenir. Kilidin süresi dolarsa veya ileti durdurulmadığında, süre sonu hemen yürürlüğe girer.

İleti kilit altındayken uygulama, süresi sona ermemiş bir iletinin elinde olabilir. Uygulamanın işleme devam edip etmeyeceğini veya iletiyi bırakmaya izin verip uygulamamayı tercih edin.

Milisaniye veya saniye düzeninde son derece düşük TTL, alıcı uygulamaları alınmadan önce iletilerin süre sonu almasına neden olabilir. Uygulamanız için en yüksek TTL 'yi göz önünde bulundurun.

## <a name="entity-level-expiration"></a>Varlık düzeyi süre sonu
Bir sıraya veya konuya gönderilen tüm iletiler, varlık düzeyinde ayarlanan varsayılan bir süre sonuna tabidir. Ayrıca, oluşturma sırasında portalda ayarlanabilir ve daha sonra ayarlanabilir. Varsayılan süre sonu, varlığa gönderilen sürenin açık olarak ayarlandığı, varlığa gönderilen tüm iletiler için kullanılır. Varsayılan süre sonu Ayrıca yaşam süresi değeri için tavan olarak çalışır. Varsayılan değerden daha uzun bir yaşam süresi dolduktan sonra, varsayılan değerin kuyruğa alınmadan önce varsayılan ileti yaşam süresi değerine sessizce ayarlanır.

> [!NOTE]
> Aracılı bir ileti için varsayılan yaşam süresi değeri, aksi belirtilmedikçe, birlikte bulunan 64 bitlik bir tamsayı için olası en büyük değerdir.
>
> İleti oluşturma varlıkları (kuyruklar ve konular) için, varsayılan sona erme saati, Service Bus standart ve Premium katmanlar için birlikte bulunan 64 bitlik bir tamsayı için olası en büyük değerdir. **Temel** katman için varsayılan (Ayrıca maksimum süre) geçerlilik süresi **14 gündür**.

Süre [dolmayan](service-bus-dead-letter-queues.md)iletiler, isteğe bağlı olarak bir atılacak ileti kuyruğuna taşınabilir. Bu ayarı programlı bir şekilde veya Azure portal kullanarak yapılandırabilirsiniz. Seçenek devre dışı bırakılırsa, zaman aşımına uğradı iletileri bırakılır. Teslim edilemeyen ileti kuyruğuna taşınan süre sonu iletileri, aracının Kullanıcı Özellikleri bölümünde yer aldığı [atılacak harf nedeni](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) özelliği hesaplanarak, diğer kullanılmayan iletilerden ayırt edilebilir. 

Kilit sırasında iletinin süresinin dolma karşı korunduğu ve varlıkta ayarlanmış olması durumunda ileti, kilitlenme terk edildiği veya süresi dolduğunda ileti atılacak ileti kuyruğuna taşınır. Ancak, ileti başarıyla kapatılmışsa taşınmaz ve bu da uygulamanın, nominal süre sonu artma içinde başarıyla işlendiğini varsayar.

Süre sonu ile yaşam süresi ve otomatik (ve işlem) atılacak zaman aşımı birleşimi, son tarihe ulaşıldığında, bir işleyiciye veya bir son tarih altındaki bir işleyici grubuna verilen bir işin işlenmek üzere alınması durumunda güven oluşturmak için değerli bir araçtır.

Örneğin, işleri ölçeği kısıtlanmış bir arka uçta güvenilir bir şekilde yürütmek için gereken bir Web sitesini göz önünde bulundurun ve zaman zaman trafik artışlarını ve bu arka ucun kullanılabilirlik bölümlerini ayrı olarak ele almak istemektedir. Normal durumda, gönderilen Kullanıcı verilerine yönelik sunucu tarafı işleyicisi bilgileri bir kuyruğa gönderir ve ardından işlemin başarılı bir şekilde işlenmesini onaylayan bir yanıt alır. Bir trafik ani işlemcisi varsa ve arka uç işleyicisi biriktirme listesi öğelerini zamanında işleyemezse, süresi dolmayan işler atılacak ileti kuyruğunda döndürülür. Etkileşimli kullanıcıya, istenen işlemin normalden biraz daha uzun sürmesi ve isteğin nihai işleme sonucunun e-posta ile kullanıcıya gönderildiği bir işleme yolu için farklı bir sıraya konabileceğini görebilirsiniz. 


## <a name="temporary-entities"></a>Geçici varlıklar

Service Bus kuyrukları, konuları ve abonelikleri, belirli bir süre boyunca kullanılmadıklarında otomatik olarak kaldırılan geçici varlıklar olarak oluşturulabilir.
 
Otomatik Temizleme, varlıkların dinamik olarak oluşturulduğu ve bir veya hata ayıklamanın çalışmasının kesintiye uğraması nedeniyle, kullanım sonrasında temizlenmeyen geliştirme ve test senaryolarında yararlıdır. Ayrıca, bir uygulama bir yanıt kuyruğu gibi dinamik varlıklar oluştururken, bir Web sunucusu işlemine geri yanıt almak için veya nesne örneği kaybolduğunda bu varlıkları güvenilir bir şekilde temizlemek zor olan başka görece kısa süreli bir nesne için de yararlıdır.

Özelliği, ad alanındaki **Boştayken otomatik olarak sil** özelliği kullanılarak etkinleştirilir. Bu özellik otomatik olarak silinmeden önce bir varlığın boşta (kullanılmamış) olması gereken süreye ayarlanır. Bu özellik için en düşük değer 5 dakikadır.
 
## <a name="idleness"></a>Uyku modu

Varlıkların (kuyruklar, konular ve abonelikler) kullanım dışı olduğu kabul edilir:

- Kuyruklar
    - Gönderme yok  
    - Alma yok  
    - Kuyrukta güncelleştirme yok  
    - Zamanlanmış ileti yok  
    - Göz atma/göz atma yok 
- Konu başlıkları  
    - Gönderme yok  
    - Konuya güncelleştirme yok  
    - Zamanlanmış ileti yok 
- Abonelikler
    - Alma yok  
    - Abonelikte güncelleştirme yok  
    - Aboneliğe yeni kural eklenmedi  
    - Göz atma/göz atma yok  
 

## <a name="next-steps"></a>Sonraki adımlar

Service Bus mesajlaşma hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Service Bus kuyrukları, konu başlıkları ve abonelikleri](service-bus-queues-topics-subscriptions.md)
* [Service Bus kuyrukları ile çalışmaya başlama](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus konu başlıklarını ve aboneliklerini kullanma](service-bus-dotnet-how-to-use-topics-subscriptions.md)
