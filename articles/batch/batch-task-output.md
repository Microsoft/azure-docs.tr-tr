---
title: Tamamlanan işlerden ve görevlerden sonuçları veya günlükleri bir veri deposuna kalıcı yap-Azure Batch | Microsoft Docs
description: Batch görevleri ve işlerinin kalıcı çıktı verilerine yönelik farklı seçenekler hakkında bilgi edinin. Verileri Azure Storage 'a veya başka bir veri deposuna kalıcı hale getirebilirsiniz.
services: batch
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d03fd754e5a8e2872063b8a10bd1293b94d8f3b6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094422"
---
# <a name="persist-job-and-task-output"></a>İş ve görev çıktılarını kalıcı hale getirme

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Görev çıkışının bazı yaygın örnekleri şunlardır:

- Görev giriş verilerini işlerken oluşturulan dosyalar.
- Görev yürütme ile ilişkili günlük dosyaları.

Bu makalede, kalıcı görev çıktısına yönelik çeşitli seçenekler açıklanmaktadır.

## <a name="options-for-persisting-output"></a>Kalıcı çıkış seçenekleri

Senaryonuza bağlı olarak, görev çıkışını kalıcı hale getirmek için uygulayabileceğiniz birkaç farklı yaklaşım vardır:

- [Batch HIZMETI API 'Sini kullanın](batch-task-output-files.md).  
- [.Net Için toplu Işlem dosya kuralları kitaplığını kullanın](batch-task-output-file-conventions.md).  
- Uygulamanızda toplu Işlem dosya kuralları standardını uygulayın.
- Özel bir dosya taşıma çözümü uygulayın.

Aşağıdaki bölümlerde her yaklaşımın yanı sıra kalıcı çıktıya yönelik genel tasarım konuları kısaca açıklanmıştır.

### <a name="use-the-batch-service-api"></a>Batch hizmeti API 'sini kullanma

Batch hizmeti, [bir işe görev eklediğinizde](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) veya bir [işe görevler koleksiyonu eklediğinizde](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job)görev verileri için Azure Storage 'da çıkış dosyaları belirtmeyi destekler.

Batch hizmeti API 'SI ile kalıcı görev çıktısı hakkında daha fazla bilgi için bkz. [Batch hizmeti API 'si Ile Azure depolama 'ya görev verilerini kalıcı hale](batch-task-output-files.md)getirme.

### <a name="use-the-batch-file-conventions-library-for-net"></a>.NET için toplu Işlem dosya kuralları kitaplığını kullanın

Batch, Azure Storage 'da görev çıktısı dosyalarını adlandırma için isteğe bağlı bir kural kümesi tanımlar. [Toplu Iş dosyası kuralları standardı](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) bu kuralları açıklar. Dosya kuralları standardı, hedef kapsayıcının adlarını ve belirli bir çıkış dosyası için Azure depolama 'daki blob yolunu iş ve görev adlarına göre belirler.

Çıktı veri dosyalarınızı adlandırmak için dosya kuralları standardını kullanmaya karar verip vermeyeceğinizi siz yapın. Hedef kapsayıcıyı ve blobu, ancak istediğiniz şekilde da adlandırın. Çıktı dosyalarını adlandırmak için dosya kuralları standardını kullanırsanız, çıkış dosyalarınız [Azure Portal][portal]görüntülenmek üzere kullanılabilir.

Ve .NET ile C# Batch çözümleri geliştiren geliştiriciler, [toplu iş dosya kuralları standardına](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)göre bir Azure depolama hesabına görev verilerini kalıcı hale getirmek Için [.NET için dosya kuralları kitaplığını][nuget_package] kullanabilir. Dosya kuralları kitaplığı, çıkış dosyalarını Azure depolama 'ya taşımayı ve hedef kapsayıcıları ve Blobları iyi bilinen bir şekilde adlandırmayı işler.

.NET için dosya kuralları kitaplığı ile kalıcı görev çıktısı hakkında daha fazla bilgi için bkz. [.net Için toplu Işlem dosya kuralları kitaplığı ile iş ve görev verilerini Azure depolama 'Ya kalıcı hale](batch-task-output-file-conventions.md)getirme.

### <a name="implement-the-batch-file-conventions-standard"></a>Toplu Işlem dosyası kuralları standardını uygulayın

.NET dışında bir dil kullanıyorsanız, [toplu Iş dosya kuralları standardını](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) kendi uygulamanızda uygulayabilirsiniz.

Kanıtlanmış bir adlandırma düzeni istediğinizde veya Azure portal görev çıktısını görüntülemek istediğinizde, dosya kuralları adlandırma standardını kendiniz uygulamak isteyebilirsiniz.

### <a name="implement-a-custom-file-movement-solution"></a>Özel bir dosya taşıma çözümü uygulama

Ayrıca, kendi dosya taşıma çözümünüzü de uygulayabilirsiniz. Şu durumlarda bu yaklaşımı kullanın:

- Görev verilerini Azure depolama dışında bir veri deposuna kalıcı hale getirmek istiyorsunuz. Dosyaları Azure SQL veya Azure Data Lake gibi bir veri deposuna yüklemek için, bu konuma yüklemek üzere özel bir betik veya yürütülebilir dosya oluşturabilirsiniz. Sonra, birincil yürütülebilir dosyanızı çalıştırdıktan sonra komut satırında bunu çağırabilirsiniz. Örneğin, bir Windows düğümünde şu iki komutu çağırabilirsiniz:`doMyWork.exe && uploadMyFilesToSql.exe`
- İlk sonuçların iade etme veya erken yükleme işlemini gerçekleştirmek istiyorsunuz.
- Hata işleme üzerinde ayrıntılı denetim sağlamak istiyorsunuz. Örneğin, belirli bir görev çıkış koduna göre belirli karşıya yükleme eylemlerini almak için görev bağımlılığı eylemlerini kullanmak istiyorsanız kendi çözümünüzü uygulamak isteyebilirsiniz. Görev bağımlılığı eylemleri hakkında daha fazla bilgi için bkz. [diğer görevlere bağımlı görevleri çalıştırmak için görev bağımlılıkları oluşturma](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Kalıcı çıkış için tasarım konuları

Batch çözümünüzü tasarlarken iş ve görev çıkışları ile ilgili aşağıdaki faktörleri göz önünde bulundurun.

- **İşlem düğümü ömrü**: İşlem düğümleri genellikle otomatik ölçeklendirme etkinleştirilmiş havuzlarda geçicidir. Düğüm üzerinde çalışan bir görevden alınan çıkış yalnızca düğüm varsa ve yalnızca görev için ayarladığınız dosya saklama süresi içinde kullanılabilir. Bir görev, görev tamamlandıktan sonra gerekebilecek bir çıktı üretirse, görevin çıkış dosyalarını Azure depolama gibi dayanıklı bir depoya yüklemesi gerekir.

- **Çıktı depolama alanı**: Azure Storage, görev çıktısı için bir veri deposu olarak önerilir, ancak herhangi bir dayanıklı depolama alanını kullanabilirsiniz. Görev çıktısını Azure depolama 'ya yazmak Batch hizmeti API 'siyle tümleşiktir. Daha dayanıklı bir depolama biçimi kullanıyorsanız, görev çıktısını kendiniz kalıcı hale getirmek için uygulama mantığını yazmanız gerekir.

- **Çıkış alımı**: Görev çıktısını doğrudan havuzunuzdaki işlem düğümlerinden veya kalıcı görev çıktılarınız varsa Azure depolama alanından veya başka bir veri deposundan alabilirsiniz. Bir görevin çıkışını doğrudan bir işlem düğümünden almak için, düğüm üzerinde dosya adı ve çıkış konumuna ihtiyacınız vardır. Görev çıktısını Azure depolama 'ya kalıcı hale getirmeniz durumunda çıkış dosyalarını Azure Storage SDK ile indirmek için Azure Storage 'daki dosyanın tam yolu gereklidir.

- **Çıktıyı görüntüleme**: Azure portal bir Batch görevine gittiğinizde ve **düğümdeki dosyaları**seçtiğinizde, yalnızca ilgilendiğiniz çıkış dosyalarını değil görevle ilişkili tüm dosyalar sunulur. Yine, işlem düğümlerinde bulunan dosyalar yalnızca düğüm mevcut olduğunda ve yalnızca görev için ayarladığınız dosya saklama süresi içinde kullanılabilir. Azure depolama 'da kalıcı olan görev çıktısını görüntülemek için, Azure portal veya [Azure Depolama Gezgini][storage_explorer]gibi bir Azure Storage istemci uygulaması kullanabilirsiniz. Azure depolama 'daki çıkış verilerini portal veya başka bir araçla görüntülemek için dosyanın konumunu bilmeniz ve doğrudan buna gitmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Batch hizmeti API 'si [Ile devam eden görev verilerini Azure depolama 'ya](batch-task-output-files.md)kalıcı olarak kalıcı hale getirmek için Batch hizmeti API 'sindeki yeni özellikleri kullanarak araştırma yapın.
- .NET için toplu [Işlem dosya kuralları kitaplığı ile, kalıcı iş ve görev verilerinde](batch-task-output-file-conventions.md).net Için toplu Işlem dosya kuralları kitaplığını kullanma hakkında bilgi edinin.
- GitHub 'daki [Persistoutkoyar][github_persistoutputs] örnek projesine bakın. Bu, hem .NET için Batch istemci kitaplığı 'nı hem de .net Için dosya kuralları kitaplığını kullanarak, sürekli depolamaya yönelik görev çıktısını kalıcı hale getirme işlemini gösterir.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
