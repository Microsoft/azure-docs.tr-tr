---
title: Geliştiriciler için API 'Ler ve araçlar
description: Azure Batch hizmeti ile çözüm geliştirmek için kullanılabilen API’ler ve araçlar hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: b7c68ab16834bbd746cf52708db0cdb3f31c8d4d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219935"
---
# <a name="overview-of-batch-apis-and-tools"></a>Batch API'lerine ve araçlarına genel bakış

Azure Batch ile paralel iş yükleri genellikle Batch API'lerinden biri kullanılarak programlı bir şekilde işlenir. İstemci uygulamanız veya hizmetiniz, Batch hizmetiyle iletişim kurmak için Batch API'lerini kullanabilir. Batch API'leriyle, işlem düğümü havuzları (sanal makineler veya bulut hizmetleri) oluşturabilir veya yönetebilirsiniz. Ardından bu düğümlerde çalıştırılacak işleri ve görevleri zamanlayabilirsiniz.

Kuruluşunuz için büyük ölçekli iş yüklerini verimli bir şekilde işleyebilir ya da müşterilerinizin tek, yüzlerce ve hatta binlerce düğümde istek üzerine veya planlı olarak işleri ve görevleri çalıştırabileceği şekilde onlara bir hizmet ön ucu sağlayabilirsiniz. [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json) gibi araçlarla yönetilen Azure Batch'i daha büyük bir iş akışının parçası olarak da kullanabilirsiniz.

> [!TIP]
> Azure Batch ' de kullanılan özellikler ve iş akışı hakkında daha fazla bilgi edinmek için bkz. [Batch hizmeti iş akışı ve kaynaklar](batch-service-workflow-features.md).

## <a name="azure-accounts-for-batch-development"></a>Batch geliştirme için Azure hesapları

Batch çözümleri geliştirdiğinizde, Azure aboneliğinizdeki aşağıdaki hesapları kullanacaksınız:

- **Batch hesabı** - Havuzlar, işlem düğümleri, işler ve görevler gibi bir Azure [Batch hesabıyla](accounts.md) ilişkilendirilmiş Azure Batch kaynakları. Uygulamanız, Batch hizmetinden bir istekte bulunduğunda istek Azure Batch hesabı adı, hesabın URL'si ve erişim anahtarı veya Azure Active Directory belirteci kullanılarak kimlik doğrulamasından geçirilir. Azure portal veya program aracılığıyla [bir Batch hesabı oluşturabilirsiniz](batch-account-create-portal.md) .
- **Depolama hesabı** - Batch’te [Azure Depolama](../storage/index.yml) dosyalarıyla çalışmak için yerleşik destek bulunur. Neredeyse tüm Batch senaryolarında, görevlerinizin çalıştırdığı programların ve işlediği verilerin hazırlanmasının yanı sıra oluşturduğu çıktı verilerinin depolanması için Azure Blob depolama kullanılır. Her Batch hesabı genellikle karşılık gelen bir depolama hesabıyla ilişkilendirilir.

## <a name="service-level-and-management-level-apis"></a>Hizmet düzeyi ve Yönetim düzeyi API 'Ler

Azure Batch, biri hizmet düzeyi ve diğeri Yönetim düzeyi için olmak üzere iki API kümesine sahiptir. Adlandırma genellikle benzerdir, ancak farklı sonuçlar döndürür.

Etkinlik günlüğünde yalnızca yönetim API 'Lerine ait eylemler izlenir. Hizmet düzeyi API 'Leri, Azure Kaynak Yönetimi katmanını (management.azure.com) atlar ve günlüğe kaydedilmez.

Örneğin, [bir havuzu silmek Için Batch hizmeti API 'si](/rest/api/batchservice/pool/delete) doğrudan Batch hesabına yöneliktir: `DELETE {batchUrl}/pools/{poolId}`

[Bir havuzu silmeye yönelik Batch yönetim API 'si](/rest/api/batchmanagement/pool/delete) Management.Azure.com katmanına hedeflenmiş olur:`DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>Batch hizmeti API’leri

Uygulamalarınız ve hizmetleriniz doğrudan REST API çağrıları kullanabilir veya Azure Batch iş yüklerinizi çalıştırmak ya da yönetmek için aşağıdaki istemci kitaplıklardan birini veya daha fazlasını kullanabilir.

| API | API başvurusu | İndir | Öğretici | Kod örnekleri | Daha fazla bilgi |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[Azure REST API-docs](/rest/api/batchservice/) |Yok |- |- | [Desteklenen sürümler](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[.NET için Azure SDK-docs](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[Öğretici](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [Sürüm notları](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[Python için Azure SDK-docs](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[Öğretici](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [Benioku](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch JavaScript** |[JavaScript için Azure SDK-docs](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[Öğretici](batch-js-get-started.md) |- | [Benioku](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Java için Azure SDK-docs](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [Benioku](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch Yönetimi API’leri

Batch için Azure Resource Manager API'leri, Batch hesaplarına programlı erişim sağlar. Bu API’leri kullanarak Batch hesaplarını, kotaları, uygulama paketlerini ve diğer kaynakları Microsoft.Batch sağlayıcısı üzerinden programlı bir şekilde yönetebilirsiniz.  

| API | API başvurusu | İndir | Öğretici | Kod örnekleri |
| --- | --- | --- | --- | --- |
| **Batch Yönetimi REST** |[Azure REST API-docs](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Yönetimi .NET** |[.NET için Azure SDK-docs](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [Öğretici](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Batch Yönetimi Python** |[Python için Azure SDK-docs](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Batch yönetimi JavaScript 'ı** |[JavaScript için Azure SDK-docs](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Batch Yönetimi Java** |[Java için Azure SDK-docs](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Batch komut satırı araçları

Bu komut satırı araçları, Batch hizmeti ve Batch Yönetimi API'leri ile aynı işlevi sağlar: 

- [Batch PowerShell cmdlet'leri](/powershell/module/az.batch/): [Azure PowerShell](/powershell/azure/) modülündeki Azure Batch cmdlet'leri PowerShell ile Batch kaynaklarını yönetmenizi sağlar.
- [Azure CLI](/cli/azure): Azure CLI, Batch hizmeti ve Batch Yönetimi hizmeti de dahil olmak üzere çok sayıda Azure hizmetiyle etkileşim için kabuk komutları sağlayan, platformlar arası bir araç takımıdır. Azure CLI’yı Batch ile birlikte kullanma hakkında daha fazla bilgi için bkz. [Azure CLI ile Batch kaynaklarını yönetme](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Uygulama geliştirme için diğer araçlar

Bu ek araçlar Batch uygulamalarınızın ve hizmetlerinizin oluşturulması ve hata ayıklaması için yararlı olabilir.

- [Azure portalı](https://portal.azure.com/): Azure portalında Batch havuzlarını, işlerini ve görevlerini oluşturabilir, izleyebilir ve silebilirsiniz. İşlerinizi çalıştırırken bu ve diğer kaynakların durum bilgilerini görüntüleyebilir ve hatta havuzlarınızdaki işlem düğümlerinden dosya indirebilirsiniz. Örneğin, sorun giderme sırasında başarısız bir görevin `stderr.txt` öğesini indirebilirsiniz. İşlem düğümlerinde oturum açmak için kullanabileceğiniz Uzak Masaüstü (RDP) dosyalarını da indirebilirsiniz.
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): Batch Explorer (eski adı BatchLabs), Azure Batch uygulamalarıyla ilgili oluşturma, hata ayıklama ve izleme işlemlerini gerçekleştirmenize yardımcı olan ücretsiz, gelişmiş özelliklere sahip ve tek başına kullanılan bir istemci aracıdır. Mac, Linux veya Windows için [yükleme paketi](https://azure.github.io/BatchExplorer/) indirebilirsiniz.
- [Shipbahçe Azure Batch](https://github.com/Azure/batch-shipyard): Batch shipbahçe, Azure Batch üzerinde kapsayıcı tabanlı toplu Işleme ve HPC iş yüklerini sağlamaya, yürütmeye ve izlemeye yardımcı olan bir araçtır.
- [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/): kesinlikle Azure Batch bir araç olmadığı sürece Depolama Gezgini, Batch çözümlerinizi geliştirirken ve hata ayıklamanıza çalışırken sahip olacak başka bir değerli araçtır.

## <a name="additional-resources"></a>Ek kaynaklar

- Batch uygulamanızdan olayları günlüğe kaydetme hakkında bilgi edinmek için bkz. [Tanılama değerlendirmesi ve izleme Için Batch ölçümleri, uyarılar ve Günlükler](batch-diagnostics.md).
- Batch hizmeti tarafından oluşturulan olaylar hakkında başvuru bilgileri için bkz. [Batch Analytics](batch-analytics.md).
- İşlem düğümleri için ortam değişkenleri hakkında bilgi için bkz. [Azure Batch Runtime ortam değişkenleri](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- Genel bir Batch iş akışını kullanarak basit bir iş yükü yürütmek üzere C# ve Batch .NET kitaplığını kullanma hakkında bilgi için bkz. [.NET için Azure Batch kitaplığını kullanmaya başlama](tutorial-parallel-dotnet.md). Bir [Python sürümü](tutorial-parallel-python.md) ve bir [JavaScript öğreticisi](batch-js-get-started.md) de mevcuttur.
- Hem C# hem de Python'un örnek iş yüklerini zamanlamak ve işlemek üzere Batch ile arabirim oluşturmasını görmek için [GitHub'daki kod örneklerini](https://github.com/Azure-Samples/azure-batch-samples) indirin.
