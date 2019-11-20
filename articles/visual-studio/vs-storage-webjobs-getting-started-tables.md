---
title: Visual Studio kullanarak Azure depolama ile çalışmaya başlama (WebJob projeleri)
description: Visual Studio bağlı hizmetler 'i kullanarak bir depolama hesabına bağlandıktan sonra Visual Studio 'da Azure Web Işleri projesinde Azure Tablo Depolamayı kullanmaya başlama
services: storage
author: ghogen
manager: jillfra
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 042f2659d3691e8c51e092bf69473187b8615ee6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299960"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Azure Storage 'ı kullanmaya başlama (Azure WebJob projeleri)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Genel Bakış
Bu makalede, C# Azure Tablo depolama hizmeti Ile Azure WEBJOBS SDK sürüm 1. x ' in nasıl kullanılacağını gösteren kod örnekleri sağlanmaktadır. Kod örnekleri, [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) sürüm 1. x ' i kullanır.

Azure Tablo depolama hizmeti, büyük miktarlarda yapılandırılmış verileri depolamanıza olanak sağlar. Hizmet, Azure bulutu içinden ve dışından kimliği doğrulanmış çağrıları kabul eden bir NoSQL veri deposu olur. Azure tabloları, yapılandırılmış ve ilişkisel olmayan verilerin depolanması için idealdir.  Daha fazla bilgi için bkz. [.NET kullanarak Azure Tablo Depolamayı kullanmaya başlama](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) .

Bazı kod parçacıklarında, tetikleyici özniteliklerinden biri kullanılarak değil, el ile çağrılan işlevlerde kullanılan **tablo** özniteliği gösterilmektedir.

## <a name="how-to-add-entities-to-a-table"></a>Tabloya varlıklar ekleme
Bir tabloya varlık eklemek için, **ICollector @ no__t-2T >** veya **ıasynccollector @ No__t-4T >** parametresiyle **tablo** özniteliğini kullanın; burada **t** , eklemek istediğiniz varlıkların şemasını belirtir. Öznitelik Oluşturucusu, tablonun adını belirten bir String parametresi alır.

Aşağıdaki kod örneği, *giriş adlı bir*tabloya **kişi** varlıkları ekler.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

Genellikle **ICollector** ile kullandığınız tür **tableentity** öğesinden türetilir veya **ıtableentity**' ı uygular, ancak bunu yapmak zorunda değildir. Aşağıdaki **kişi** sınıflarından biri **, önceki giriş** yönteminde gösterilen kodla çalışır.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Doğrudan Azure Storage API 'SI ile çalışmak istiyorsanız Yöntem imzasına bir **Cloudstorageaccount** parametresi ekleyebilirsiniz.

## <a name="real-time-monitoring"></a>Gerçek zamanlı izleme
Veri giriş işlevleri genellikle büyük hacimlerdeki verileri işletiğinden, Web Işleri SDK panosu gerçek zamanlı izleme verileri sağlar. **Çağırma günlüğü** bölümü, işlevin hala çalışır durumda olup olmadığını söyler.

![Giriş işlevi çalışıyor](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

**Çağırma ayrıntıları** sayfası, çalışırken işlevin ilerlemesini (yazılan varlıkların sayısı) bildirir ve bu işlemi iptal etmek için size bir fırsat sağlar.

![Giriş işlevi çalışıyor](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

İşlev tamamlandığında, **çağırma ayrıntıları** sayfası yazılan satır sayısını bildirir.

![Giriş işlevi tamamlandı](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Tablodaki birden çok varlığı okuma
Bir tabloyu okumak için **tablo** özniteliğini bir **IQueryable @ No__t-2T >** parametresi ile birlikte kullanın. tür **t** , **tableentity** 'dan türetilir veya **ıtableentity**uygular.

Aşağıdaki kod **örneği, giriş tablosundan tüm** satırları okur ve günlüğe kaydeder:

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>Bir tablodan tek bir varlığı okuma
Tek bir tablo varlığına bağlamak istediğinizde, Bölüm anahtarını ve satır anahtarını belirtmenizi sağlayan iki ek parametre içeren bir **tablo** özniteliği Oluşturucusu vardır.

Aşağıdaki kod örneği, bir kuyruk iletisinde alınan bölüm anahtarını ve satır anahtarı değerlerini temel alan bir **kişi** varlığının tablo satırını okur:  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


Bu örnekteki **kişi** sınıfının **ıtableentity**uygulaması gerekmez.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>.NET Storage API 'sini doğrudan bir tabloyla çalışmak üzere kullanma
Tablo ile çalışma konusunda daha fazla esneklik için bir **cloudtable** nesnesi ile **Table** özniteliğini de kullanabilirsiniz.

Aşağıdaki kod *örneği, giriş tablosuna tek* bir varlık eklemek Için bir **cloudtable** nesnesi kullanır.

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

**Cloudtable** nesnesinin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [.NET kullanarak Azure Tablo Depolamayı kullanmaya başlama](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Kuyruklar nasıl yapılır makalesi kapsamındaki ilgili konular
Bir kuyruk iletisi tarafından tetiklenen tablo işlemenin nasıl işleneceği veya tablo işlemeye özgü olmayan WebJobs SDK senaryolarında, bkz. [Azure kuyruk depolama ve Visual Studio bağlı hizmetleri (WebJob projeleri) ile çalışmaya](../storage/vs-storage-webjobs-getting-started-queues.md)başlama.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Azure tablolarıyla çalışmaya yönelik yaygın senaryoları nasıl işleyebileceğini gösteren kod örnekleri verilmiştir. Azure WebJobs ve WebJobs SDK 'sını kullanma hakkında daha fazla bilgi için bkz. [Azure WebJobs belge kaynakları](https://go.microsoft.com/fwlink/?linkid=390226).

