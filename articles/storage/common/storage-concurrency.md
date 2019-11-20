---
title: Microsoft Azure Depolama'da Eşzamanlılığı Yönetme
description: Blob, kuyruk, tablo ve dosya hizmetleri için eşzamanlılık yönetimi
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 427cc34cc5a2801a2da98259f932678cdcf71ef7
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67870822"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Microsoft Azure Depolama'da Eşzamanlılığı Yönetme
## <a name="overview"></a>Genel Bakış
Modern Internet tabanlı uygulamalar genellikle birden çok kullanıcının aynı anda verileri görüntülemesini ve güncelleştirmesini de ister. Bu, özellikle birden çok kullanıcının aynı verileri güncelleştirebileceği senaryolar için, uygulama geliştiricilerinin son kullanıcılara öngörülebilir bir deneyim sağlama konusunda dikkatli bir şekilde karar sağlamasını gerektirir. Geliştiricilerin genellikle göz önünde bulundurulması gereken üç ana veri eşzamanlılık stratejisi vardır:  

1. İyimser eşzamanlılık: güncelleştirme gerçekleştiren bir uygulama, güncelleştirme kapsamında, uygulamanın en son verileri okuduğundan bu yana verilerin değişip değişmediğini doğrular. Örneğin, bir wiki sayfasını görüntüleyen iki kullanıcı aynı sayfada bir güncelleştirme yapsa, wiki platformu ikinci güncelleştirmenin ilk güncelleştirmenin üzerine yazmadığından ve her iki kullanıcının da güncelleştirmesinin başarılı olup olmadığını anlamalarına emin olmalıdır. Bu strateji çoğu zaman Web uygulamalarında kullanılır.
2. Kötümser eşzamanlılık: bir güncelleştirme gerçekleştirmeye yönelik bir uygulama, bir nesne üzerinde bir kilit alır ve bu da kilit serbest bırakılana kadar diğer kullanıcıların verileri güncelleştirmesini önler. Örneğin, yalnızca ana öğenin güncelleştirmeleri gerçekleştirdiği bir ana/alt veri çoğaltma senaryosunda, ana öğe genellikle, başka kimsenin güncelleştirememesini sağlamak için veriler üzerinde uzun bir süre boyunca özel bir kilit tutar.
3. Son yazıcı WINS – tüm güncelleştirme işlemlerinin, uygulamanın verileri ilk okumasından bu yana verileri güncelleştirmiş olup olmadığını doğrulamadan devam etmesine izin veren bir yaklaşım. Bu strateji (veya bir biçimsel stratejinin olmaması) genellikle verilerin bölümlenmesi durumunda birden çok kullanıcının aynı verilere erişebileceği bir olasılık olmaması halinde kullanılır. Kısa süreli veri akışlarının işlendiği durumlar da yararlı olabilir.  

Bu makalede, Azure depolama platformunun bu eşzamanlılık stratejilerinin üçü için birinci sınıf desteği sunarak geliştirmeyi nasıl basitleştirdiği hakkında bir genel bakış sunulmaktadır.  

## <a name="azure-storage--simplifies-cloud-development"></a>Azure depolama – bulut geliştirmeyi basitleştirir
Azure Storage hizmeti, her üç stratejiyi de destekler, ancak bu, bir güçlü tutarlılık modelini çok büyük bir şekilde desteklemek üzere tasarlandığından, Depolama hizmeti bir veri ekleme veya güncelleştirme işlemini kaydeder bu verilere daha fazla erişim, en son güncelleştirmeyi görür. Son tutarlılık modeli kullanan depolama platformları, bir kullanıcı tarafından bir yazma işlemi gerçekleştirildiğinde ve güncelleştirilmiş veriler diğer kullanıcılar tarafından görülebileceği zaman arasında bir gecikme olur ve bu sayede tutarsızlıkların oluşmasını engellemek için istemci uygulamalarının geliştirilmesini karmaşıtük edebilir son kullanıcıları etkileme.  

Uygun bir eşzamanlılık stratejisi geliştiricilerin seçimine ek olarak, bir depolama platformunun değişiklikleri nasıl yalıtdığının farkında olması gerekir. işlemler arasında özellikle de aynı nesneye yapılan değişiklikler. Azure depolama hizmeti, okuma işlemlerinin tek bir bölüm içindeki yazma işlemleriyle aynı anda gerçekleşmesini sağlamak için anlık görüntü yalıtımını kullanır. Diğer yalıtım düzeylerinden farklı olarak, anlık görüntü yalıtımı tüm okumaların, güncelleştirmeler gerçekleşirken bile verilerin tutarlı bir anlık görüntüsünü (aslında bir güncelleştirme işlemi işlenirken, son kaydedilen değerleri döndürerek) görmesini güvence altına alır.  

## <a name="managing-concurrency-in-blob-storage"></a>Blob depolamada eşzamanlılık yönetimi
Blob hizmetindeki bloblara ve kapsayıcılara erişimi yönetmek için iyimser veya Kötümser eşzamanlılık modellerini kullanmayı tercih edebilirsiniz. Açık olarak bir strateji belirtmezseniz, WINS varsayılan olarak yazar.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Bloblar ve kapsayıcılar için iyimser eşzamanlılık
Depolama hizmeti, depolanan her nesneye bir tanımlayıcı atar. Bu tanımlayıcı, bir nesne üzerinde her güncelleştirme işlemi gerçekleştirildiğinde güncellenir. Tanımlayıcı, HTTP protokolünde tanımlanmış ETag (varlık etiketi) üst bilgisini kullanan HTTP GET yanıtının bir parçası olarak istemciye döndürülür. Bu tür bir nesne üzerinde bir güncelleştirme gerçekleştiren bir Kullanıcı, bir güncelleştirmenin yalnızca belirli bir koşul karşılanırsa meydana geldiğinden emin olmak için bir koşullu üstbilgiyle birlikte özgün ETag içinde gönderebilir. Bu durumda koşul, depolama hizmeti t gerektiren bir "IF-Match" üst bilgisi olur. o güncelleştirme isteğinde belirtilen ETag değerinin, depolama hizmetinde depolandıkları ile aynı olduğundan emin olun.  

Bu işlemin ana hattı aşağıdaki gibidir:  

1. Depolama hizmetinden bir blob alma, yanıt, depolama hizmetindeki nesnenin geçerli sürümünü tanımlayan bir HTTP ETag üst bilgisi değeri içerir.
2. Blobu güncelleştirdiğinizde, hizmete göndereceğiniz isteğin **IF-Match** koşullu üst bilgisinde adım 1 ' de aldığınız ETag değerini ekleyin.
3. Hizmet, istekteki ETag değerini Blobun geçerli ETag değeriyle karşılaştırır.
4. Blob 'un geçerli ETag değeri istekteki **IF-Match** koşullu üstbilgisindeki ETag 'ten farklı bir sürümse, hizmet istemciye 412 hatası döndürür. Bu, istemcinin aldığı bu yana başka bir işlemin blobu güncelleştirdiğini istemciye bildirir.
5. Blob 'un geçerli ETag değeri, istekteki **IF-Match** koşullu üstbilgisindeki ETag ile aynı sürümse, hizmet istenen işlemi gerçekleştirir ve yeni bir sürüm oluşturduğunu göstermek için Blobun geçerli ETag değerini güncelleştirir.  

Aşağıdaki C# kod parçacığı (Istemci depolama kitaplığı 4.2.0 kullanarak), daha önce alınan veya bir Blobun özelliklerinden erişilen ETag değerine bağlı olarak **IF-Match erişimkoşulunun** nasıl oluşturulacağı hakkında basit bir örnek gösterir. takılmamış. Ardından, blobu güncelleştirdiğinde **accesscondition** nesnesini kullanır: **accesscondition** nesnesi isteğe **IF-Match** üst bilgisini ekler. Başka bir işlem blobu güncelleştirdiyse, blob hizmeti bir HTTP 412 (Önkoşul başarısız) durum iletisi döndürür. Tam örneği buradan indirebilirsiniz: [Azure Storage kullanarak eşzamanlılık yönetimi](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Depolama hizmeti Ayrıca, **If-Modified-Since**, **If-Modified-Since** ve **If-None-Match** gibi ek koşullu üstbilgiler için de destek içerir. Daha fazla bilgi için bkz. MSDN 'de [BLOB hizmeti işlemleri Için koşullu üstbilgileri belirtme](https://msdn.microsoft.com/library/azure/dd179371.aspx) .  

Aşağıdaki tablo, istekte **IF-Match** gibi koşullu üstbilgileri kabul eden ve yanıtta bir ETag değeri döndüren kapsayıcı işlemlerini özetler.  

| Çalışma | Kapsayıcı ETag değerini döndürür | Koşullu üstbilgileri kabul eder |
|:--- |:--- |:--- |
| Kapsayıcı oluşturma |Evet |Hayır |
| Kapsayıcı özelliklerini al |Evet |Hayır |
| Kapsayıcı meta verilerini al |Evet |Hayır |
| Kapsayıcı meta verilerini ayarla |Evet |Evet |
| Kapsayıcı ACL 'sini al |Evet |Hayır |
| Kapsayıcı ACL 'sini ayarla |Evet |Evet (*) |
| Kapsayıcıyı Sil |Hayır |Evet |
| Kira kapsayıcısı |Evet |Evet |
| Blobları Listele |Hayır |Hayır |

(*) SetContainerACL tarafından tanımlanan izinler önbelleğe alınır ve bu izinlere yönelik güncelleştirmeler, zaman içindeki güncelleştirmelerin tutarlı olması garanti edilmediği için 30 saniye sürer.  

Aşağıdaki tablo, istekte **IF-Match** gibi koşullu üstbilgileri kabul eden ve yanıtta bir ETag değeri döndüren blob işlemlerini özetler.

| Çalışma | ETag değerini döndürür | Koşullu üstbilgileri kabul eder |
|:--- |:--- |:--- |
| Blobu koy |Evet |Evet |
| Blob al |Evet |Evet |
| Blob özelliklerini al |Evet |Evet |
| Blob özelliklerini ayarla |Evet |Evet |
| Blob meta verilerini al |Evet |Evet |
| Blob meta verilerini ayarla |Evet |Evet |
| Kira Blobu (*) |Evet |Evet |
| Anlık görüntü blobu |Evet |Evet |
| Kopya blob'u |Evet |Evet (kaynak ve hedef blobu için) |
| Kopyalama blobu durdur |Hayır |Hayır |
| BLOB silme |Hayır |Evet |
| Yerleştirme bloğu |Hayır |Hayır |
| Öbek listesini yerleştirme |Evet |Evet |
| Engelleme listesini al |Evet |Hayır |
| Yerleştirme sayfası |Evet |Evet |
| Sayfa aralıklarını al |Evet |Evet |

(*) Kira blobu blob üzerinde ETag öğesini değiştirmez.  

### <a name="pessimistic-concurrency-for-blobs"></a>Blob 'lar için Kötümser eşzamanlılık
Bir blobu özel kullanım için kilitlemek üzere, üzerinde bir [kira](https://msdn.microsoft.com/library/azure/ee691972.aspx) elde edebilirsiniz. Kira aldığınızda, kira ne kadar süreyle ihtiyacınız olduğunu belirtirsiniz: Bu, bir özel kilit için, 15 ila 60 saniye veya sonsuz arasında olabilir. Sınırlı bir kirayı genişletmek için yenileyebilirsiniz ve bununla işiniz bittiğinde herhangi bir kiralamayı serbest bırakabilirsiniz. Blob hizmeti, süresi dolduğunda sınırlı kiraları otomatik olarak serbest bırakır.  

Kiralamalar, özel yazma/paylaşılan okuma, dışlamalı yazma/özel okuma ve paylaşılan yazma/özel okuma gibi farklı eşitleme stratejilerinin desteklenme imkanı sağlar. Kira mevcut olduğunda, depolama hizmeti özel yazma işlemleri uygular (put, set ve DELETE Operations) ancak denetim for Read işlemleri için, geliştiricinin tüm istemci uygulamalarının bir kira KIMLIĞI kullandığından ve aynı anda yalnızca bir istemcinin bir hizmet aldığından emin olmasını sağlar. Geçerli kira KIMLIĞI. Paylaşılan okumaların kira KIMLIĞI sonucunu içermeyen okuma işlemleri.  

Aşağıdaki C# kod parçacığında bir blob üzerinde 30 saniye için özel bir kira alma, Blobun içeriğini güncelleştirme ve ardından kirayı serbest bırakma örneği gösterilmektedir. Yeni bir kira almaya çalıştığınızda blob üzerinde zaten geçerli bir kira varsa, blob hizmeti "HTTP (409) çakışması" durum sonucunu döndürür. Aşağıdaki kod parçacığı, depolama hizmetindeki blobu güncellemek için bir istek yaptığında kira bilgilerini kapsüllemek için bir **Accesscondition** nesnesi kullanır.  Tam örneği buradan indirebilirsiniz: [Azure Storage kullanarak eşzamanlılık yönetimi](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Kira KIMLIĞINI geçirmeden kiralanan bir blob üzerinde yazma işlemine çalışırsanız, istek 412 hatasıyla başarısız olur. Kiranın, **Uploadtext** yöntemini çağırmadan önce süresinin dolacağını ancak kira kimliğini hala iletseniz, isteğin de **412** hatasıyla başarısız olduğunu unutmayın. Kira süre sonu zamanlarını ve kira kimliklerini yönetme hakkında daha fazla bilgi için bkz. [kira blobu](https://msdn.microsoft.com/library/azure/ee691972.aspx) Rest belgeleri.  

Aşağıdaki blob işlemleri, Kötümser eşzamanlılık 'yi yönetmek için kiraları kullanabilir:  

* Blobu koy
* Blob al
* Blob özelliklerini al
* Blob özelliklerini ayarla
* Blob meta verilerini al
* Blob meta verilerini ayarla
* BLOB silme
* Yerleştirme bloğu
* Öbek listesini yerleştirme
* Engelleme listesini al
* Yerleştirme sayfası
* Sayfa aralıklarını al
* Anlık görüntü blobu-Kiralama KIMLIĞI bir kira varsa isteğe bağlıdır
* Kopyalama blobu-hedef blobu üzerinde kira varsa, kira KIMLIĞI gereklidir
* Kopyalama blobu durdur-hedef blobu üzerinde sonsuz bir kira varsa, kira KIMLIĞI gereklidir
* Kira blobu  

### <a name="pessimistic-concurrency-for-containers"></a>Kapsayıcılar için Kötümser eşzamanlılık
Kapsayıcılardaki kiralamalar, bloblarda (özel yazma/paylaşılan okuma, dışlamalı yazma/özel okuma ve paylaşılan yazma/özel okuma) olarak aynı eşitleme stratejilerini destekler, ancak Blobların aksine depolama hizmeti yalnızca denetim üzerinde zorlanır silme işlemleri. Etkin bir kiralamaya sahip bir kapsayıcıyı silmek için, istemci, silme isteğiyle birlikte etkin Kiralama KIMLIĞINI içermelidir. Diğer tüm kapsayıcı işlemleri, kira KIMLIĞINI dahil etmeden bir kiralanan kapsayıcıda başarılı olur ve bu durumda paylaşılan işlemler olur. Denetim of Update (put veya set) veya Read işlemleri gerekliyse, geliştiriciler tüm istemcilerin bir kira KIMLIĞI kullandığından ve aynı anda yalnızca bir istemcinin geçerli bir kira KIMLIĞI olduğundan emin olmalıdır.  

Aşağıdaki kapsayıcı işlemleri, Kötümser eşzamanlılık 'yi yönetmek için kiraları kullanabilir:  

* Kapsayıcıyı Sil
* Kapsayıcı özelliklerini al
* Kapsayıcı meta verilerini al
* Kapsayıcı meta verilerini ayarla
* Kapsayıcı ACL 'sini al
* Kapsayıcı ACL 'sini ayarla
* Kira kapsayıcısı  

Daha fazla bilgi için bkz.  

* [Blob hizmeti Işlemleri için koşullu üstbilgileri belirtme](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Kira kapsayıcısı](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Kira blobu](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Tablo hizmetinde eşzamanlılık yönetimi
Tablo hizmeti, doğrudan iyimser eşzamanlılık denetimleri gerçekleştirmeyi tercih etmeniz gereken blob hizmetinden farklı olarak, varlıklarla çalışırken varsayılan davranış olarak iyimser eşzamanlılık denetimleri kullanır. Tablo ve BLOB Hizmetleri arasındaki diğer fark, blob hizmeti ile yalnızca varlıkların eşzamanlılık davranışını yönetebilmeniz, her iki kapsayıcının ve Blobların Eşzamanlılığını yönetebilirsiniz.  

İyimser eşzamanlılığı kullanmak ve tablo depolama hizmetinden aldıktan sonra başka bir işlemin bir varlığı değiştirse denetlemek için, tablo hizmeti bir varlık döndürdüğünde aldığınız ETag değerini kullanabilirsiniz. Bu işlemin ana hattı aşağıdaki gibidir:  

1. Tablo depolama hizmetinden bir varlık alma, yanıt, depolama hizmetindeki bu varlıkla ilişkili geçerli tanımlayıcıyı tanımlayan bir ETag değeri içerir.
2. Varlığı güncelleştirdiğinizde, hizmete göndereceğiniz isteğin zorunlu **IF-Match** üst bilgisinde 1. adım ' da aldığınız ETag değerini ekleyin.
3. Hizmet, istekteki ETag değerini varlığın geçerli ETag değeri ile karşılaştırır.
4. Varlığın geçerli ETag değeri, istekteki zorunlu **IF-Match** üst bilgisinde ETag değerinden farklıysa, hizmet istemciye 412 hatası döndürür. Bu, istemcinin aldığı bu yana başka bir işlemin varlığı güncelleştirdiğini gösterir.
5. Varlığın geçerli ETag değeri, istekteki zorunlu **IF-Match** üst bilgisindeki ETag ile aynı veya **IF-Match** üst bilgisi joker karakteri (*) içeriyorsa, hizmet istenen Işlemi gerçekleştirir ve geçerli ETag 'i güncelleştirir güncelleştirilmiş olduğunu göstermek için varlığın değeri.  

Blob hizmeti 'nin aksine, tablo hizmeti istemcinin güncelleştirme isteklerinde bir **IF-Match** üst bilgisi içermesini gerektirdiğini unutmayın. Ancak, istemci istekte **IF-Match** üst bilgisini (*) joker karakteri (*) olarak ayarladıysanız, koşulsuz bir güncelleştirme (son yazıcı WINS stratejisi) zorlamak ve eşzamanlılık denetimlerini atlamak mümkündür.  

Aşağıdaki C# kod parçacığında, daha önce oluşturulmuş veya e-posta adresi güncelleştirildikten sonra alınan bir müşteri varlığı gösterilmektedir. İlk ekleme veya alma işlemi, ETag değerini Customer nesnesinde depolar ve örnek değiştirme işlemini yürütürken aynı nesne örneğini kullandığından, otomatik olarak, ETag değerini tablo hizmetine geri gönderir ve hizmetin Eşzamanlılık ihlallerini denetleyin. Başka bir işlem, varlığı tablo depolamadaki güncelleştirdiyseniz, hizmet bir HTTP 412 (Önkoşul başarısız) durum iletisi döndürür.  Tam örneği buradan indirebilirsiniz: [Azure Storage kullanarak eşzamanlılık yönetimi](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Eşzamanlılık denetimini açıkça devre dışı bırakmak için, Replace işlemini yürütmeden önce **Employee** nesnesinin **ETag** özelliğini "*" olarak ayarlamanız gerekir.  

```csharp
customer.ETag = "*";  
```

Aşağıdaki tablo, tablo varlığı işlemlerinin ETag değerlerini nasıl kullandığını özetler:

| Çalışma | ETag değerini döndürür | IF-Match istek üst bilgisi gerektirir |
|:--- |:--- |:--- |
| Sorgu varlıkları |Evet |Hayır |
| Varlık Ekle |Evet |Hayır |
| Varlığı Güncelleştir |Evet |Evet |
| Birleştirme varlığı |Evet |Evet |
| Varlığı Sil |Hayır |Evet |
| Varlık Ekle veya Değiştir |Evet |Hayır |
| Varlık ekleme veya birleştirme |Evet |Hayır |

**Varlık Ekle veya Değiştir** ve **Ekle ya da Birleştir** Işlemlerinin tablo hizmetine bir ETag değeri göndermediğinden hiçbir eşzamanlılık *denetimi gerçekleştirmediğini* unutmayın.  

Tabloları kullanan genel geliştiriciler, ölçeklenebilir uygulamalar geliştirirken iyimser eşzamanlılık kullanır. Kötümser kilitleme gerekiyorsa, tablolara erişirken bir yaklaşım, her tablo için belirlenmiş bir blob atamak ve tablo üzerinde çalışmadan önce blob üzerinde kira almaya çalışır. Bu yaklaşım, tüm veri erişim yollarının tablo üzerinde çalıştırılmadan önce kirayı almasını sağlamak için uygulamanın kullanılmasını gerektirir. Ayrıca minimum kira süresinin 15 saniye olduğunu ve bu da ölçeklenebilirlik için dikkatli bir dikkat gerektirdiğini unutmayın.  

Daha fazla bilgi için bkz.  

* [Varlıklar üzerinde işlemler](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Kuyruk hizmetinde eşzamanlılık yönetimi
Sıraya alma hizmetinde eşzamanlılık olan bir senaryo, birden çok istemcinin bir kuyruktan ileti aldığı yerdir. Kuyruktan bir ileti alındığında, yanıt iletiyi silmek için gereken iletiyi ve bir pop alındı değerini içerir. İleti kuyruktan otomatik olarak silinmez, ancak alındıktan sonra, diğer istemcilere visibilitytimeout parametresi tarafından belirtilen zaman aralığı için görünür değildir. İletiyi alan istemcinin, işleme alındıktan sonra iletiyi silmesi beklenir ve bu süre önce, bu, visibilitytimeout parametresinin değeri temel alınarak hesaplanan yanıtın TimeNextVisible öğesi tarafından belirtilen süreden önce. Visibilitytimeout değeri, görünen TimeNextVisible değerini belirlemekte iletinin alındığı zamana eklenir.  

Kuyruk hizmeti iyimser veya Kötümser eşzamanlılık desteğine sahip değil ve bu nedenle, bir kuyruktan alınan iletilerin işlenmesi, iletilerin bir ıdempotent şekilde işlenmesini sağlamalıdır. Son yazıcı WINS stratejisi, SetQueueServiceProperties, SetQueueMetaData, SetQueueACL ve UpdateMessage gibi güncelleştirme işlemleri için kullanılır.  

Daha fazla bilgi için bkz.  

* [Kuyruk hizmeti REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Iletileri al](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Dosya hizmetinde eşzamanlılık yönetimi
Dosya hizmetine iki farklı protokol uç noktası, SMB ve REST kullanılarak erişilebilir. REST hizmeti, iyimser kilitleme veya Kötümser kilitleme desteğine sahip değildir ve tüm güncelleştirmeler son yazıcı WINS stratejisini takip eder. Dosya paylaşımlarını bağlayan SMB istemcileri, Kötümser kilitleme gerçekleştirme özelliği de dahil olmak üzere paylaşılan dosyalara erişimi yönetmek için dosya sistemi kilitleme mekanizmalarından yararlanabilir. Bir SMB istemcisi bir dosyayı açtığında, hem dosya erişimi hem de paylaşma modunu belirtir. "Write" veya "Read/Write" adlı bir dosya erişimi seçeneğinin ve "none" dosya paylaşma moduyla birlikte ayarlanması, dosya kapatılıncaya kadar dosyanın bir SMB istemcisi tarafından kilitlenme oluşmasına neden olur. Bir SMB istemcisinin dosya kilitli olduğu bir dosya üzerinde REST işlemi deneniyorsa REST hizmeti, 409 (çakışma) durum kodunu bir hata kodu Sharingihlayle döndürür.  

Bir SMB istemcisi silme için bir dosyayı açtığında, bu dosyadaki diğer tüm SMB istemci açık tanıtıcıları kapanana kadar dosyayı bekleyen silme olarak işaretler. Bir dosya bekleyen silme olarak işaretlendiğinden, bu dosyadaki tüm REST işlemleri SMBDeletePending hata kodu ile 409 (çakışma) durum kodunu döndürür. SMB istemcisinin dosyayı kapatmadan önce bekleyen silme bayrağını kaldırması mümkün olduğundan, durum kodu 404 (bulunamadı) döndürülmedi. Diğer bir deyişle, 404 (bulunamadı) durum kodu yalnızca dosya kaldırıldığında beklenir. Bir dosya SMB bekleyen silme durumundaysa, liste dosyaları sonuçlarına dahil edilmez. Ayrıca, REST silme dosyası ve REST Delete dizin işlemlerinin, kararlılık ve bekleyen silme durumuyla sonuçlanmadığını unutmayın.  

Daha fazla bilgi için bkz.  

* [Dosya kilitlerini yönetme](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Özet ve sonraki adımlar
Microsoft Azure Depolama Hizmeti, geliştiricilerin eşzamanlılık ve veri tutarlılığı gibi önemli tasarım varsayımlarını tehlikeye atabilmeleri gerekmeden en karmaşık çevrimiçi uygulamaların ihtiyaçlarını karşılayacak şekilde tasarlandı iznine.  

Bu blogda başvurulan tüm örnek uygulamalar için:  

* [Azure depolama kullanarak eşzamanlılık yönetme-örnek uygulama](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Azure depolama hakkında daha fazla bilgi için bkz.  

* [Microsoft Azure Depolama giriş sayfası](https://azure.microsoft.com/services/storage/)
* [Azure Depolama’ya giriş](storage-introduction.md)
* [BLOB](../blobs/storage-dotnet-how-to-use-blobs.md), [tablo](../../cosmos-db/table-storage-how-to-use-dotnet.md), [kuyruk](../storage-dotnet-how-to-use-queues.md)ve [dosyalar](../storage-dotnet-how-to-use-files.md) için depolama kullanmaya başlama
* Depolama mimarisi – [Azure depolama: Güçlü tutarlılığı olan yüksek oranda kullanılabilir bir bulut depolama hizmeti](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

