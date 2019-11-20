---
title: Azure Blob depolama olaylarına yeniden davranıma | Microsoft Docs
description: Blob depolama olaylarına abone olmak için Azure Event Grid’i kullanın.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: b813ef89bb1a55f769d0ea2391855ba5d671c140
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648789"
---
# <a name="reacting-to-blob-storage-events"></a>BLOB depolama olaylarına yeniden davranıyor

Azure depolama olayları, uygulamaların, modern sunucusuz mimariler kullanılarak Blobların oluşturulması ve silinmesi gibi olaylara tepki vermesini sağlar. Bu, karmaşık kod veya pahalı ve verimsiz yoklama Hizmetleri gereksinimini ortadan kaldırmaz.

Bunun yerine, olaylar Azure Işlevleri, Azure Logic Apps, hatta kendi özel http dinleyiciniz gibi abonelere [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) gönderilir ve yalnızca kullandığınız kadar ödersiniz.

BLOB depolama olayları, zengin yeniden deneme ilkeleri ve atılacak mektup teslimi aracılığıyla uygulamalarınıza güvenilir teslim hizmetleri sağlayan Event Grid hizmetine güvenilir bir şekilde gönderilir.

Ortak BLOB depolama olay senaryolarında görüntü veya video işleme, arama dizini oluşturma veya herhangi bir dosya odaklı iş akışı bulunur. Zaman uyumsuz dosya yüklemeleri, olaylara yönelik harika bir uyum. Değişiklikler seyrek olduğunda, ancak senaryonuz anında yanıt vermeyi gerektirdiğinde, olay tabanlı mimari özellikle etkili olabilir.

Bunu şimdi denemek istiyorsanız, bu hızlı başlangıç makalelerinden herhangi birine bakın:

|Bu aracı kullanmak istiyorsanız:    |Şu makaleye bakın: |
|--|-|
|Azure portal    |[Hızlı Başlangıç: Blob Depolama olaylarını Azure portal web uç noktasına yönlendirme](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Hızlı Başlangıç: PowerShell ile Depolama olaylarını Web uç noktasına yönlendirme](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Hızlı Başlangıç: Azure CLı ile Depolama olaylarını Web uç noktasına yönlendirme](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Hesabınızda hiyerarşik bir ad alanı varsa, bu öğretici bir Event Grid aboneliği, bir Azure Işlevi ve Azure Databricks bir [işi](https://docs.azuredatabricks.net/user-guide/jobs.html) nasıl bağlayacaksınız: [Öğreticide Databricks Delta tablosunu](data-lake-storage-events.md)güncelleştirmek için Azure Data Lake Storage 2. olaylarını kullanın.

## <a name="the-event-model"></a>Olay modeli

Event Grid, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../../event-grid/concepts.md#event-subscriptions) kullanır. Bu görüntüde olay yayımcıları, olay abonelikleri ve olay işleyicileri arasındaki ilişki gösterilmektedir.

![Event Grid modeli](./media/storage-blob-event-overview/event-grid-functional-model.png)

İlk olarak, bir uç noktayı olaya abone olun. Ardından, bir olay tetiklendiğinde Event Grid hizmeti bu olayla ilgili verileri uç noktaya gönderir.

Görüntülemek için [BLOB depolama olayları şeması](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) makalesine bakın:

> [!div class="checklist"]
> * BLOB depolama olaylarının ve her olayın nasıl tetiklendiğine ilişkin kapsamlı bir liste.
> * Event Grid bu olayların her biri için gönderebilecek verilere bir örnek.
> * Verilerde görüntülenen her anahtar değer çiftinin amacı.

## <a name="filtering-events"></a>Olayları filtreleme

Blob olay abonelikleri, olay türüne ve oluşturulan veya Silinen nesnenin kapsayıcı adına ve BLOB adına göre filtrelenebilir.  Filtre, olay aboneliklerinin [oluşturulması](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) sırasında veya [daha sonraki bir zamanda](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)olay aboneliklerine uygulanabilir. "İle başlar" ve "ile biter" ile birlikte Event Grid iş filtreleri, eşleşen bir konuya sahip olayların aboneye teslim edilmesini sağlar.

Filtrelerin nasıl uygulanacağı hakkında daha fazla bilgi edinmek için bkz. [Event Grid olayları filtreleme](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Blob Storage olaylarının konusu şu biçimi kullanır:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Bir depolama hesabının tüm olaylarını eşleştirmek için, konu filtrelerini boş bırakabilirsiniz.

Bir öneki paylaşan bir kapsayıcı kümesi içinde oluşturulan Blobların olaylarını eşleştirmek için şöyle bir `subjectBeginsWith` filtre kullanın:

```
/blobServices/default/containers/containerprefix
```

Belirli bir kapsayıcıda oluşturulan Blobların olaylarını eşleştirmek için şöyle bir `subjectBeginsWith` filtre kullanın:

```
/blobServices/default/containers/containername/
```

Blob adı ön ekini paylaşan belirli bir kapsayıcıda oluşturulan Blobların olaylarını eşleştirmek için şöyle bir `subjectBeginsWith` filtre kullanın:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Blob sonekini paylaşan belirli bir kapsayıcıda oluşturulan Blobların olaylarını eşleştirmek için ". log" `subjectEndsWith` veya ". jpg" gibi bir filtre kullanın. Daha fazla bilgi için bkz. [Event Grid kavramları](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Olayları tüketen uygulamalar

Blob Depolama olaylarını işleyen uygulamalar birkaç önerilen uygulamayı izlemelidir:
> [!div class="checklist"]
> * Birden çok abonelik olayları aynı olay işleyicisine yönlendirmek üzere yapılandırılabildiğiniz için, olayların belirli bir kaynaktan olduğunu varsaymamak, ancak beklediğiniz depolama hesabından geldiğinden emin olmak için iletinin konusunu denetlemek önemlidir.
> * Benzer şekilde, eventType için hazırlanmakta olan bir olay olduğunu ve aldığınız tüm olayların istediğiniz tür olacağını kabul edin.
> * İletiler sıraya alınır ve bir gecikmeden sonra, nesneler hakkındaki bilgilerinizin güncel olup olmadığını anlamak için ETag alanlarını kullanın.  Ayrıca, belirli bir nesne üzerindeki olayların sırasını anlamak için sıralayıcı alanlarını kullanın.
> * Blob üzerinde ne tür işlemlere izin verileceğini ve Blobun erişmek için hangi istemci kitaplığı türlerini kullanacağınızı anlamak için blobType alanını kullanın. Geçerli değerler ya `PageBlob`da `BlockBlob` . 
> * Blob 'a erişmek için `CloudBlockBlob` ve `CloudAppendBlob` oluşturucularla birlikte URL alanını kullanın.
> * Anladığınızı alanları yoksayın. Bu uygulama, gelecekte eklenebilecek yeni özelliklere dayanıklı tutmaya yardımcı olur.
> * **Microsoft. Storage. blobcreated** olayının yalnızca bir blok `CopyBlob`blobu tamamen yürütüldüğü zaman tetiklendiğinden emin olmak istiyorsanız,, `PutBlockList` veya `FlushWithClose` REST API çağrılarının olayını `PutBlob`filtreleyin. Bu API çağrıları, **Microsoft. Storage. BlobCreated** olayını yalnızca veriler bir blok blobuna tam olarak kaydedildikten sonra tetikler. Filtre oluşturmayı öğrenmek için bkz. [Event Grid olayları filtreleme](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Sonraki adımlar

Event Grid hakkında daha fazla bilgi edinin ve BLOB depolama olaylarına bir deneme verin:

- [Event Grid Hakkında](../../event-grid/overview.md)
- [Blob Depolama olaylarını özel bir Web uç noktasına yönlendirme](storage-blob-event-quickstart.md)
