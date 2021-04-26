---
title: Azure Depolama’da Tanılama Verilerini Depolama ve Görüntüleme
description: Azure tanılama verilerini bir Azure depolama hesabında nasıl toplayacağınızı öğrenmek için, mevcut araçlardan birini kullanarak görüntüleyebilirsiniz.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: application-insights
ms.openlocfilehash: b58249dac5b98bb86c35fae9ac574b6100fa9fd5
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383108"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Azure Depolama’daki tanılama verilerini depolama ve görüntüleme

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Tanılama verileri, Microsoft Azure Depolama Öykünücüsü veya Azure depolama alanına aktarmadığınız takdirde kalıcı olarak depolanmaz. Depolama alanındaki bir kez, kullanılabilir araçlardan biri ile görüntülenebilir.

## <a name="specify-a-storage-account"></a>Bir depolama hesabı belirtin
ServiceConfiguration. cscfg dosyasında kullanmak istediğiniz depolama hesabını belirtirsiniz. Hesap bilgileri bir yapılandırma ayarında bağlantı dizesi olarak tanımlanır. Aşağıdaki örnekte, Visual Studio 'da yeni bir bulut hizmeti projesi için oluşturulan varsayılan bağlantı dizesi gösterilmektedir:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Bu bağlantı dizesini, bir Azure depolama hesabı için hesap bilgilerini sağlamak üzere değiştirebilirsiniz.

Toplanmakta olan tanılama verilerinin türüne bağlı olarak, Azure Tanılama blob hizmetini veya tablo hizmetini kullanır. Aşağıdaki tabloda kalıcı olan veri kaynakları ve bunların biçimi gösterilmektedir.

| Veri kaynağı | Depolama biçimi |
| --- | --- |
| Azure günlükleri |Tablo |
| IIS 7,0 günlükleri |Blob |
| Azure Tanılama altyapısı günlükleri |Tablo |
| Başarısız Istek Izleme günlükleri |Blob |
| Windows olay günlükleri |Tablo |
| Performans sayaçları |Tablo |
| Kilitlenme bilgi dökümleri |Blob |
| Özel hata günlükleri |Blob |

## <a name="transfer-diagnostic-data"></a>Tanılama verilerini aktar
SDK 2,5 ve üzeri için, tanılama verilerini aktarma isteği yapılandırma dosyası aracılığıyla gerçekleşebilir. Tanılama verilerini, yapılandırmada belirtilen şekilde, zamanlanan aralıklarda aktarabilirsiniz.

SDK 2,4 ve önceki bir için, tanılama verilerini yapılandırma dosyası ve programlama yoluyla aktarmaya de isteyebilirsiniz. Programlı yaklaşım da isteğe bağlı aktarımları yapmanıza olanak sağlar.

> [!IMPORTANT]
> Tanılama verilerini bir Azure depolama hesabına aktardığınızda, tanılama verilerinizin kullandığı depolama kaynakları için ücretlendirilirsiniz.
> 
> 

## <a name="store-diagnostic-data"></a>Tanılama verilerini depola
Günlük verileri, aşağıdaki adlara sahip blob veya tablo depolama alanında depolanır:

**Tablolar**

* **Wadlogstable** -izleme dinleyicisi kullanılarak kodda yazılan günlükler.
* **WADDiagnosticInfrastructureLogsTable** -tanılama izleyici ve yapılandırma değişiklikleri.
* **Waddirectoriestable** : Tanılama izleyicisinin izlediği dizinler.  Buna IIS günlükleri, IIS başarısız istek günlükleri ve özel dizinler dahildir.  Blob günlük dosyasının konumu kapsayıcı alanında belirtilir ve BLOB adı RelativePath alanında bulunur.  AbsolutePath alanı, Azure sanal makinesinde var olan dosyanın konumunu ve adını gösterir.
* **WADPerformanceCountersTable** – performans sayaçları.
* **Wadwindowseventlogstable** – Windows olay günlükleri.

**Bloblar**

* **wad-Control-Container** – (yalnızca SDK 2,4 ve önceki için), Azure TANıLAMAYı denetleyen XML yapılandırma dosyalarını içerir.
* **wad-IIS-failedreqlogfiles** – IIS başarısız istek günlüklerinden bilgiler içerir.
* **wad-IIS-LogFiles** : IIS günlükleri hakkında bilgi içerir.
* **"Custom"** – tanılama İzleyicisi tarafından izlenen dizinleri yapılandırmaya dayalı özel bir kapsayıcı.  Bu blob kapsayıcısının adı WADDirectoriesTable içinde belirtilecektir.

## <a name="tools-to-view-diagnostic-data"></a>Tanılama verilerini görüntülemek için Araçlar
Verileri depolama alanına aktarıldıktan sonra görüntülemek için kullanabileceğiniz çeşitli araçlar vardır. Örnek:

* Visual Studio 'da Sunucu Gezgini-Azure araçlarını Microsoft Visual Studio yüklediyseniz, Azure depolama hesaplarınızdan salt okunurdur blob ve tablo verilerini görüntülemek için Sunucu Gezgini Azure Storage düğümünü kullanabilirsiniz. Yerel depolama öykünücü hesabınızdan ve ayrıca Azure için oluşturduğunuz depolama hesaplarından verileri görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [Sunucu Gezgini Ile depolama kaynaklarına göz atma ve yönetme](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md) , Windows, OSX ve Linux 'Ta Azure Depolama verileriyle kolayca çalışabilmenizi sağlayan tek başına bir uygulamadır.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) , Azure üzerinde çalışan uygulamalar tarafından toplanan tanılama verilerini görüntülemenize, yüklemenize ve yönetmenize olanak tanıyan Azure tanılama yöneticisini içerir.

## <a name="next-steps"></a>Sonraki Adımlar
[Azure Tanılama ile Cloud Services uygulamasındaki akışı izleme](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


