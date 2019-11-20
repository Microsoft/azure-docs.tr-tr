---
title: Visual Studio (WebJob projeleri) kullanarak BLOB depolama ile çalışmaya başlama
description: Visual Studio bağlı hizmetleri kullanarak bir Azure depolama 'ya bağlandıktan sonra bir WebJob projesinde blob depolamayı kullanmaya başlama.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 90aa824b7df575eb2783ece5bd88322f0b55f0a2
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299968"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Azure Blob depolama ve Visual Studio bağlı hizmetleri (WebJob projeleri) ile çalışmaya başlama
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Genel Bakış
Bu makalede, C# bir Azure blobu oluşturulduğunda veya güncelleştirilirken bir işlemin nasıl tetikleneceğini gösteren kod örnekleri sağlanmaktadır. Kod örnekleri, [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) sürüm 1. x ' i kullanır. Visual Studio **bağlı hizmetler Ekle** iletişim kutusunu kullanarak bir Web işi projesine bir depolama hesabı eklediğinizde, uygun Azure depolama NuGet paketi yüklüyse, projeye uygun .NET başvuruları eklenir ve bu için bağlantı dizeleri depolama hesabı App. config dosyasında güncelleştirilir.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Blob oluşturulduğunda veya güncelleştirilirken bir işlevi tetikleme
Bu bölümde **Blobtrigger** özniteliğinin nasıl kullanılacağı gösterilmektedir.

 **Note:** WebJobs SDK, yeni veya değiştirilmiş blob 'ları izlemek için günlük dosyalarını tarar. Bu işlem, doğal olarak yavaştır; bir işlev, blob oluşturulduktan sonra birkaç dakika veya daha uzun bir süre boyunca tetiklenmeyebilir.  Uygulamanızın blob 'ları hemen işlemesi gerekiyorsa, blobu oluştururken bir kuyruk iletisi oluşturmak ve blobu işleyen işlevde **Blobtrigger** özniteliği yerine **Queuetrigger** özniteliğini kullanmanız önerilir .

### <a name="single-placeholder-for-blob-name-with-extension"></a>Uzantıya sahip blob adı için tek yer tutucu
Aşağıdaki kod örneği, *giriş* kapsayıcısında görünen metin bloblarını *Çıkış* kapsayıcısına kopyalar:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Öznitelik Oluşturucusu, kapsayıcı adı ve BLOB adı için bir yer tutucu belirten bir String parametresi alır. Bu örnekte, *giriş* kapsayıcısında *Blob1. txt* adlı bir blob oluşturulduysa, işlev *Çıkış* kapsayıcısında *Blob1. txt* adlı bir blob oluşturur.

Aşağıdaki kod örneğinde gösterildiği gibi BLOB adı yer tutucusu ile bir ad stili belirtebilirsiniz:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Bu kod yalnızca adları "özgün-" ile başlayan Blobları kopyalar. Örneğin, *giriş* kapsayıcısında *Original-Blob1. txt* dosyası *Çıkış* kapsayıcısında *Copy-Blob1. txt* dosyasına kopyalanır.

Adında küme ayraçları olan blob adları için bir ad stili belirtmeniz gerekiyorsa, küme ayraçları iki katına koyun. Örneğin, *görüntüler* kapsayıcısında aşağıdaki gibi adlara sahip Blobları bulmak istiyorsanız:

        {20140101}-soundfile.mp3

Bunu, örüntüiniz için kullanın:

        images/{{20140101}}-{name}

Örnekte, *ad* yer tutucu değeri *soundfile. mp3*olur.

### <a name="separate-blob-name-and-extension-placeholders"></a>Blob adı ve uzantı yer tutucuları ayır
Aşağıdaki kod örneği, *giriş* kapsayıcısında görüntülenen blob 'ları *Çıkış* kapsayıcısına kopyalayan dosya uzantısını değiştirir. Kod, *giriş* Blobun uzantısını günlüğe kaydeder ve *Çıkış* blobunun uzantısını *. txt*olarak ayarlar.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Bloblara bağlayabileceğiniz türler
**Blobtrigger** özniteliğini aşağıdaki türlerde kullanabilirsiniz:

* **dizisinde**
* **Değerine**
* **Ka**
* **Ihoparlör blobu**
* **CloudBlockBlob**
* **CloudPageBlob**
* [Ihoparlör Blobstreambınder](#getting-serialized-blob-content-by-using-icloudblobstreambinder) tarafından seri durumdan çıkarılan diğer türler

Doğrudan Azure Storage hesabıyla çalışmak istiyorsanız, yöntem imzasına bir **Cloudstorageaccount** parametresi de ekleyebilirsiniz.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Dizeye bağlayarak metin blobu içeriği alma
Metin Blobları bekleniyorsa **Blobtrigger** bir **dize** parametresine uygulanabilir. Aşağıdaki kod örneği, bir metin blobunu **LogMessage**adlı bir **dize** parametresine bağlar. İşlevi, Web Işleri SDK panosuna Blobun içeriğini yazmak için bu parametreyi kullanır.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Ihoparlör Blobstreambınder kullanarak serileştirilmiş blob içeriği alma
Aşağıdaki kod örneği, **Blobtrigger** özniteliğinin bir blobu **Web görüntüsü** türüne bağlamasını etkinleştirmek Için **ITIL blobstreambinder** uygulayan bir sınıf kullanır.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

**Webımage** bağlama kodu, **ICODE Blobstreambinder**öğesinden türetilen bir **webımageciltçi** sınıfında sağlanır.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Zarar bloblarını işleme
Bir **Blobtrigger** işlevi başarısız olduğunda, hata geçici bir hatadan KAYNAKLANıRSA, SDK bunu yeniden çağırır. Hatanın nedeni blob 'un içeriği olursa, işlev blobu her işlemeye çalıştığında başarısız olur. Varsayılan olarak SDK, belirli bir blob için 5 kata kadar bir işlevi çağırır. Beşinci deneme başarısız olursa, SDK *WebJobs-blobtrigger-zeadlı*bir kuyruğa ileti ekler.

En fazla yeniden deneme sayısı yapılandırılabilir. Aynı **Maxdequeuecount** ayarı, zarar blobu işleme ve zarar sırası ileti işleme için kullanılır.

Zarar Blobları için kuyruk iletisi aşağıdaki özellikleri içeren bir JSON nesnesidir:

* FunctionID ( *{WebJob Name}* biçiminde). Lerdir. *{Function Name}* , örneğin: WebJob1. Functions. copyblob)
* BlobType ("BlockBlob" veya "PageBlob")
* ContainerName
* BlobName
* ETag (bir blob sürüm tanımlayıcısı, örneğin: "0x8D1DC6E70A277EF")

Aşağıdaki kod örneğinde, **Copyblob** işlevinin her çağrılışında başarısız olmasına neden olan kodu vardır. SDK, en fazla yeniden deneme sayısı için onu çağırdığında, zarar veren blob kuyruğunda bir ileti oluşturulur ve bu ileti **Logkirblob** işlevi tarafından işlenir.

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

SDK, JSON iletisini otomatik olarak serileştirir. Bu, **Kirblobmessage** sınıfı şudur:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Blob yoklama algoritması
Web Işleri SDK 'Sı, **Blobtrigger** öznitelikleriyle belirtilen tüm kapsayıcıları uygulama başlangıcında tarar. Büyük bir depolama hesabında, bu tarama biraz zaman alabilir, bu nedenle yeni blob 'ların bulunamaması ve **Blobtrigger** işlevleri yürütülmeden önce bir işlem olabilir.

Uygulama başladıktan sonra yeni veya değiştirilmiş Blobları algılamak için, SDK düzenli aralıklarla BLOB depolama günlüklerinden okur. Blob günlükleri arabelleğe alınır ve her 10 dakikada bir fiziksel olarak yazılır, bu nedenle karşılık gelen **Blobtrigger** işlevi yürütülmeden önce bir blob oluşturulduktan veya güncelleştirildikten sonra önemli bir gecikme olabilir.

**BLOB** özniteliğini kullanarak oluşturduğunuz Bloblar için bir özel durum vardır. WebJobs SDK 'Sı yeni bir blob oluşturduğunda, yeni blobu tüm eşleşen **Blobtetikleyici** işlevlerine anında geçirir. Bu nedenle, bir blob giriş ve çıkış zincirinize sahipseniz, SDK bunları verimli bir şekilde işleyebilir. Ancak, blob işleme işlevlerinizi başka yollarla oluşturulan veya güncellenen blob 'lar için düşük gecikme süresi istiyorsanız, **Blobtrigger**yerine **Queuetrigger** kullanılması önerilir.

### <a name="blob-receipts"></a>Blob alındıları
WebJobs SDK 'Sı aynı yeni veya güncelleştirilmiş blob için hiçbir **Blobtrigger** işlevinin birden çok kez çağrılmasına neden olur. Bu, belirli bir blob sürümünün işlenip işlenmeyeceğini anlamak için *BLOB alındıları* tutarak bunu yapar.

Blob alındıları, AzureWebJobsStorage bağlantı dizesi tarafından belirtilen Azure depolama hesabındaki *Azure-WebJobs-Konakları* adlı bir kapsayıcıda depolanır. Blob alındı bilgisi aşağıdaki bilgilere sahiptir:

* Blob (" *{WebJob Name}* ) için çağrılan işlev. Lerdir. *{Function Name}* ", örneğin:" WebJob1. Functions. copyblob ")
* Kapsayıcı adı
* Blob türü ("BlockBlob" veya "PageBlob")
* Blob adı
* ETag (bir blob sürüm tanımlayıcısı, örneğin: "0x8D1DC6E70A277EF")

Bir Blobun yeniden işlenmesine zorlamak isterseniz, *Azure-WebJobs-hosts* kapsayıcısından söz konusu Blobun blob alındığını el ile silebilirsiniz.

## <a name="related-topics-covered-by-the-queues-article"></a>Kuyruklar makalesinin kapsadığı ilgili konular
Bir kuyruk iletisi tarafından tetiklenen blob işlemeyi işleme veya blob işlemeye özgü olmayan WebJobs SDK senaryoları için bkz. [WEBJOBS SDK Ile Azure kuyruk depolamayı kullanma](https://github.com/Azure/azure-webjobs-sdk/wiki).

Bu makalede ele alınan ilgili konular şunları içerir:

* Zaman uyumsuz işlevler
* Birden çok örnek
* Düzgün kapanma
* Bir işlevin gövdesinde WebJobs SDK özniteliklerini kullanma
* Koddaki SDK bağlantı dizelerini ayarlayın.
* Koddaki WebJobs SDK Oluşturucu parametreleri için değerleri ayarla
* Zarar blobu işleme için **Maxdequeuecount** ayarını yapılandırın.
* Bir işlevi el ile tetikleme
* Yazma günlükleri

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Azure bloblarıyla çalışmaya yönelik yaygın senaryoları nasıl işleyebileceğini gösteren kod örnekleri verilmiştir. Azure WebJobs ve WebJobs SDK 'sını kullanma hakkında daha fazla bilgi için bkz. [Azure WebJobs belge kaynakları](https://go.microsoft.com/fwlink/?linkid=390226).

