---
title: Kaynak dosyaları oluşturma ve kullanma-Azure Batch | Microsoft Docs
description: Çeşitli giriş kaynaklarından Azure Batch kaynak dosyaları oluşturmayı öğrenin.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 9c55b22d1cb85fb645087cf48b54f9d5ac12d58f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322178"
---
# <a name="creating-and-using-resource-files"></a>Kaynak dosyalarını oluşturma ve kullanma

Azure Batch bir görev, genellikle işlemek için bir veri biçimi gerektirir. Kaynak dosyaları, bu verileri Batch sanal makinenize (VM) bir görev aracılığıyla sağlamak anlamına gelir. Tüm görev türleri kaynak dosyalarını destekler: görevler, başlangıç görevleri, iş hazırlama görevleri, iş sürümü görevleri vb. Bu makalede, kaynak dosyaları oluşturma ve bunları bir VM 'ye yerleştirme hakkında bazı yaygın yöntemler ele alınmaktadır.  

Kaynak dosyaları, Batch 'teki bir VM 'ye veri yerleştirmek için bir mekanizmadır, ancak veri türü ve kullanım sıklığı esnektir. Ancak bazı yaygın kullanım durumları vardır:

1. Başlangıç görevindeki kaynak dosyalarını kullanarak her VM 'de ortak dosyaları sağlama
1. Görevlere göre işlenecek giriş verilerini sağla

Ortak dosyalar, örneğin, görevlerinizin çalıştırdığı uygulamaları yüklemek için kullanılan bir başlangıç görevinde dosyalar olabilir. Giriş verileri ham görüntü veya video verisi veya toplu Işlem tarafından işlenecek herhangi bir bilgi olabilir.

## <a name="types-of-resource-files"></a>Kaynak dosya türleri

Kaynak dosyaları oluşturmak için kullanabileceğiniz birkaç farklı seçenek vardır. Kaynak dosyaları oluşturma işlemi, özgün verilerin depolandığı yere bağlı olarak değişir.

Kaynak dosyası oluşturma seçenekleri:

- [Depolama kapsayıcısı URL 'si](#storage-container-url): Azure 'daki herhangi bir depolama kapsayıcısından bir kaynak dosyası oluşturur
- [Depolama kapsayıcısı adı](#storage-container-name): Toplu Iş ile bağlantılı bir Azure depolama hesabındaki kapsayıcının adından bir kaynak dosyası oluşturur
- [Web uç noktası](#web-endpoint): Geçerli HTTP URL 'sinden bir kaynak dosyası oluşturur

### <a name="storage-container-url"></a>Depolama kapsayıcısı URL 'SI

Depolama kapsayıcısı URL 'SI kullanmak, Azure 'daki herhangi bir depolama kapsayıcısındaki dosyalara doğru izinlerle erişebileceğiniz anlamına gelir.

Bu C# örnekte, dosyalar zaten BLOB depolama alanı olarak bir Azure depolama kapsayıcısına yüklenmiş. Bir kaynak dosyası oluşturmak için gereken verilere erişmek için öncelikle depolama kapsayıcısına erişim edinmemiz gerekir.

Depolama kapsayıcısına erişmek için doğru izinlere sahip bir paylaşılan erişim imzası (SAS) URI 'SI oluşturun. SAS için sona erme saati ve izinlerini ayarlayın. Bu durumda başlangıç saati belirtilmez, bu nedenle SAS hemen geçerli hale gelir ve oluşturulduktan sonra iki saat sona erer.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Kapsayıcı erişimi için hem hem de `Read` `List` izinlerinizin olması gerekir, ancak bu, blob `Read` erişimi ile yalnızca izninizin olması gerekir.

İzinler yapılandırıldıktan sonra, SAS belirtecini oluşturun ve depolama kapsayıcısına erişim için SAS URL 'sini biçimlendirin. Depolama kapsayıcısı için biçimlendirilen SAS URL 'sini kullanarak ile [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)bir kaynak dosyası oluşturun.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Bir SAS URL 'SI oluşturmanın alternatifi, Azure Blob depolama alanındaki bir kapsayıcıya ve bloblarına anonim, genel okuma erişimi sağlamak için kullanılır. Bunu yaparak, hesap anahtarınızı paylaşmadan ve SAS gerekmeden bu kaynaklara salt okuma erişimi verebilirsiniz. Genel okuma erişimi, genellikle belirli Blobların anonim okuma erişimi için her zaman kullanılabilir olmasını istediğiniz senaryolar için kullanılır. Bu senaryo çözümünüze uygunsa, blob verilerinize erişimi yönetme hakkında daha fazla bilgi edinmek için [bloblara anonim erişim](../storage/blobs/storage-manage-access-to-resources.md) makalesine bakın.

### <a name="storage-container-name"></a>Depolama kapsayıcısı adı

Bir SAS URL 'SI yapılandırmak ve oluşturmak yerine, blob verilerinize erişmek için Azure depolama kapsayıcının adını kullanabilirsiniz. Kullanılan depolama kapsayıcısının, yığın hesabınızla bağlantılı olan Azure depolama hesabında olması gerekir, bu, oto depolama hesabı olarak bilinir. Bir oto depolama hesabının depolama kapsayıcısı adının kullanılması, bir depolama kapsayıcısına erişmek için bir SAS URL 'SI yapılandırmayı ve oluşturmayı atlamanızı sağlar.

Bu örnekte, kaynak dosyası oluşturma için kullanılacak verilerin, Batch hesabınıza bağlı bir Azure depolama hesabında zaten olduğunu varsaytık. Bir yeniden depolama hesabınız yoksa, bir hesap oluşturma ve bağlama hakkında ayrıntılı bilgi için [Batch hesabı oluşturma](batch-account-create-portal.md) ' daki adımlara bakın.

Bağlı bir depolama hesabı kullanarak bir depolama kapsayıcısına SAS URL 'SI oluşturmanız ve yapılandırmanız gerekmez. Bunun yerine, bağlantılı depolama hesabınızda depolama kapsayıcısının adını sağlayın.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web uç noktası

Azure depolama 'ya yüklenmemiş veriler, kaynak dosyaları oluşturmak için hala kullanılabilir. Giriş verilerinizi içeren herhangi bir geçerli HTTP URL 'sini belirtebilirsiniz. URL Batch API 'sine sağlanır ve ardından veri, kaynak dosyası oluşturmak için kullanılır.

Aşağıdaki C# örnekte, giriş verileri kurgusal GitHub uç noktasında barındırılır. API, geçerli Web uç noktasından dosyayı alır ve göreviniz tarafından tüketilen bir kaynak dosyası oluşturur. Bu senaryo için kimlik bilgisi gerekmez.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>İpuçları ve öneriler

Her bir Azure Batch görevi dosyaları farklı kullanır, bu nedenle Batch 'de dosyaları yönetmek için kullanılabilir seçenekler vardır. Aşağıdaki senaryolar kapsamlı bir şekilde değildir, bunun yerine birkaç yaygın durum ele alınmaktadır ve öneriler sağlar.

### <a name="many-resource-files"></a>Birçok kaynak dosyası

Batch işiniz, hepsi aynı ortak dosyaları kullanan birkaç görev içerebilir. Ortak görev dosyaları birçok görev arasında paylaşılmışsa, kaynak dosyalarını kullanmak yerine dosyaları içerecek bir uygulama paketi kullanmak daha iyi bir seçenek olabilir. Uygulama paketleri indirme hızı için iyileştirme sağlar. Ayrıca, uygulama paketlerdeki veriler görevler arasında önbelleğe alınır, bu nedenle görev dosyalarınız sık değişmemesi halinde uygulama paketleri çözümünüz için uygun olabilir. Uygulama paketleriyle, Azure depolama 'daki dosyalara erişmek için çeşitli kaynak dosyalarını el ile yönetmeniz veya SAS URL 'Leri oluşturmanız gerekmez. Batch, uygulama paketlerini depolamak ve işlem düğümlerine dağıtmak için Azure depolama ile arka planda çalışmaktadır.

Her görevin bu göreve özgü çok sayıda dosyası varsa, kaynak dosyaları çoğunlukla en iyi seçenektir. Benzersiz dosyalar kullanan görevlerin genellikle, uygulama paketleri içeriğiyle birlikte kullanılması kolay bir şekilde güncellenmesi veya değiştirilmeleri gerekir. Kaynak dosyaları, bireysel dosyaları güncelleştirme, ekleme veya düzenlemeyle ilgili ek esneklik sağlar.

### <a name="number-of-resource-files-per-task"></a>Görev başına kaynak dosyası sayısı

Bir görevde birden fazla yüz kaynak dosyası belirtilmişse toplu Işlem, görevi çok büyük hale karşı reddedebilir. Görevin kendisinde kaynak dosyalarının sayısını en aza indirerek görevlerinizi küçük tutmanız en iyisidir.

Görevin ihtiyaç duyacağı dosya sayısını en aza indirmenin bir yolu yoksa, kaynak dosyalarının depolama kapsayıcısına başvuran tek bir kaynak dosyası oluşturarak görevi iyileştirebilirsiniz. Bunu yapmak için, kaynak dosyalarınızı bir Azure depolama kapsayıcısına koyun ve kaynak dosyalarının farklı "kapsayıcı" modlarını kullanın. Görevleriniz için indirilecek dosya koleksiyonlarını belirtmek için blob öneki seçeneklerini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- Kaynak dosyalarına alternatif olarak [uygulama paketleri](batch-application-packages.md) hakkında bilgi edinin.
- Kaynak dosyaları için kapsayıcıları kullanma hakkında daha fazla bilgi için bkz. [kapsayıcı iş yükleri](batch-docker-container-workloads.md).
- Görevlerinizin çıkış verilerini nasıl toplayıp kaydedeceğiniz hakkında bilgi edinmek için bkz. [kalıcı iş ve görev çıktısı](batch-task-output.md).
- Batch çözümleri oluşturmak için kullanılabilen [Batch API’leri ve araçları](batch-apis-tools.md) hakkında bilgi alın.
