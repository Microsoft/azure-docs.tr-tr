---
title: Azure depolama ölçümleri geçişi | Microsoft Docs
description: Eski ölçümleri Azure Izleyici tarafından yönetilen yeni ölçümlere geçirmeyi öğrenin.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855333"
---
# <a name="azure-storage-metrics-migration"></a>Azure depolama ölçümleri geçişi

Azure Storage, izleme deneyimini Azure 'da kaldırma stratejisi ile hizalı olarak ölçümleri Azure Izleyici platformu ile tümleştirir. Gelecekte, eski ölçümlerin hizmeti Azure ilkesi temel alınarak erken bir bildirimle sona acaktır. Eski depolama ölçümlerini kullanıyorsanız, ölçüm bilgilerinizi korumak için hizmet bitiş tarihinden önce geçiş yapmanız gerekir.

Bu makalede, eski ölçülerden yeni ölçümlere nasıl geçiş yapılacağı gösterilmektedir.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Azure depolama tarafından yönetilen eski ölçümleri anlama

Azure depolama, eski ölçüm değerlerini toplar ve bunları aynı depolama hesabındaki $Metric tablolarında toplar ve depolar. Bir izleme grafiği ayarlamak için Azure portal kullanabilirsiniz. Şemayı temel alan $Metric tablolarından verileri okumak için Azure Storage SDK 'larını de kullanabilirsiniz. Daha fazla bilgi için bkz. [depolama Analizi](./storage-analytics.md).

Eski ölçümler yalnızca Azure Blob depolama alanı üzerinde kapasite ölçümleri sağlar. Eski ölçümler, BLOB depolama, tablo depolama, Azure dosyaları ve kuyruk depolama üzerinde işlem ölçümleri sağlar.

Eski ölçümler düz bir şemada tasarlanmıştır. Ölçüyü tetikleyerek trafik desenleri yoksa tasarım sıfır ölçüm değeriyle sonuçlanır. Örneğin, canlı trafiğinden bir depolama hesabına herhangi bir sunucu zaman aşımı hatası almadığınızda bile, **Servertimeouterror** değeri, $Metric tablolarında 0 olarak ayarlanır.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Azure Izleyici tarafından yönetilen yeni ölçümleri anlama

Yeni depolama ölçümleri için Azure depolama, ölçüm verilerini Azure Izleyici arka ucuna yayar. Azure Izleyici, portaldan veriler ve veri alımı dahil olmak üzere Birleşik bir izleme deneyimi sağlar. Daha fazla ayrıntı için bu [makaleye](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)başvurabilirsiniz.

Yeni ölçümler; blob, tablo, dosya, kuyruk ve Premium depolamada kapasite ölçümleri ve işlem ölçümleri sağlar.

Çoklu boyut, Azure Izleyicisinin sağladığı özelliklerden biridir. Azure depolama, yeni ölçüm şeması tanımlama bölümünde tasarımı benimsemektedir. Ölçümler üzerinde desteklenen boyutlar için Azure [izleyici 'de Azure depolama ölçümleri](./storage-metrics-in-azure-monitor.md)' nde ayrıntıları bulabilirsiniz. Çok boyutlu tasarım, ölçüm ve kapasite depolama alanından her iki bant genişliği için maliyet verimliliği sağlar. Sonuç olarak, trafiğiniz ilgili ölçümleri tetiklememiş ise ilgili ölçüm verileri oluşturulmaz. Örneğin, trafiğiniz sunucu zaman aşımı hatalarını tetiklerse, Azure Izleyici, boyut **responseType** değeri **Servertimeouterror**'e eşit olan ölçüm **işlemlerinin** değerini sorgulayıp hiçbir veri döndürmez.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Eski ölçümler ve yeni ölçümler arasında ölçüm eşlemesi

Ölçüm verilerini programlı olarak okuduğunuzda, programlarınızda yeni ölçüm şemasını benimsemeniz gerekir. Değişiklikleri daha iyi anlamak için aşağıdaki tabloda listelenen eşlemeye başvurabilirsiniz:

**Kapasite ölçümleri**

| Eski ölçüm | Yeni ölçüm |
| ------------------- | ----------------- |
| **Kapasite**            | **Blobtype** , **Blok Blobu** veya **Pageblob** değerine eşit olan **blobcapacity** |
| **ObjectCount**        | **Blobtype değeri** **Blockblob** veya **Pageblob** değerine eşit olan **blobcount** |
| **ContainerCount**      | **ContainerCount** |

Aşağıdaki ölçümler, eski ölçümlerin desteklemediği yeni tekliflerdir:
* **Tablokapasitesi**
* **Tablosayısı**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **Dosya kapasitesi**
* **FileCount**
* **Dosya ShareCount**
* **UsedCapacity**

**İşlem ölçümleri**

| Eski ölçüm | Yeni ölçüm |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Boyut **responseType** değerine eşit olan Işlemler, **Authorizationerror** ve boyut **kimlik doğrulaması** **anonim** değere eşit |
| **Anonymousclienentothererror** | Boyut **responseType** değerine eşittir **Clienentothererror** ve boyut **Authentication** , **anonim** olarak eşit |
| **AnonymousClientTimeoutError** | Boyut **responseType değeri** **Clienttimeouterror** değerine eşit ve boyut **kimlik doğrulaması** **anonim** olarak eşit olan işlemler |
| **AnonymousNetworkError** | Boyut **responseType** değerine eşit olan Işlemler, **Networkerror** ve boyut **kimlik doğrulaması** **anonim** olarak eşittir |
| **AnonymousServerOtherError** | Boyut **responseType** değerine eşit olan işlem sayısı, **Sunucudiğerhata** ve boyut **kimlik doğrulaması** **anonim** olarak eşittir |
| **AnonymousServerTimeoutError** | Boyut **responseType değeri** **Servertimeouterror** değerine eşit ve boyut **kimlik doğrulaması** **anonim** olarak eşit olan işlemler |
| **AnonymousSuccess** | Boyut **responseType** değerine eşit olan işlemler ve boyut **kimlik doğrulaması** **anonim** olarak eşit |
| **Anonymouskısıtlar Lingerror** | Boyut **responseType** değerine eşit olan Işlem, **Clientkısıtlar Lingerror** veya **Serverbusyerror** ve boyut **kimlik doğrulaması** **anonim** değere eşit |
| **AuthorizationError** | Boyut **responseType** Ile **authorizationerror** 'a eşit işlemler |
| **Kullanılabilirlik** | **Kullanılabilirlik** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **Averageserverlatency gösteriyor** | **Başarılı Sunucugecikmesi** |
| **Clienentothererror** | Boyut **responseType** 'ı **Clienentothererror** değerine eşit olan işlemler |
| **ClientTimeoutError** | Boyut **responseType değeri** **Clienttimeouterror** değerine eşit olan işlemler |
| **NetworkError** | Boyut **responseType** Ile **networkerror** 'a eşit işlemler |
| **Yüztauthorizationerror** | Boyut **responseType** Ile **authorizationerror** 'a eşit işlemler |
| **Percentclienentothererror** | Boyut **responseType** 'ı **Clienentothererror** değerine eşit olan işlemler |
| **PercentNetworkError** | Boyut **responseType** Ile **networkerror** 'a eşit işlemler |
| **PercentServerOtherError** | Boyut **responseType** değerine eşit olan Işlemler **serverothererror** 'a eşittir |
| **PercentSuccess** | Boyut **responseType** değerine eşit olan Işlemler **başarılı oldu** |
| **PercentThrottlingError** | Boyut **responseType değeri** **Clientıngerror** veya **serverbusyerror** değerine eşit olan işlemler |
| **PercentTimeoutError** | Boyut **responseType değeri** **servertimeouterror** veya **responseType** ile **clienttimeouterror** değerine eşit olan işlemler |
| **SASAuthorizationError** | Boyut **responseType** değerine eşit olan ve **SAS** 'ye eşit boyut **kimlik doğrulaması** olan işlemler |
| **SASClientOtherError** | Boyut **responseType** değerine eşit olan ve **SAS** 'e eşit boyut **kimlik doğrulaması** olan işlemler |
| **SASClientTimeoutError** | Boyut **responseType değeri** **Clienttimeouterror** ve boyut **kimlik doğrulaması** **SAS** ile eşit olan işlemler |
| **SASNetworkError** | Boyut **responseType** değerine eşit olan Işlemler, **Networkerror** ve boyut **kimlik doğrulaması** **SAS** değerine eşit |
| **SASServerOtherError** | Boyut **responseType** değerine eşit olan ve **SAS** 'e eşit boyut **kimlik doğrulaması** olan işlemler |
| **SASServerTimeoutError** | Boyut **responseType değeri** **Servertimeouterror** ve boyut **kimlik doğrulaması** **SAS** değerine eşit olan işlemler |
| **SASSuccess** | Boyut **responseType** değerine eşit ve boyut **kimlik doğrulaması** **SAS** değerine eşit olan işlemler |
| **Saskısıtlar Lingerror** | Boyut **responseType** değerine eşit olan Işlem, **Clientkısıtlar lingerror** veya **serverbusyerror** ve **SAS** ile aynı boyut **kimlik doğrulaması** |
| **ServerOtherError** | Boyut **responseType** değerine eşit olan Işlemler **serverothererror** 'a eşittir |
| **ServerTimeoutError** | Boyut **responseType değeri** **Servertimeouterror** değerine eşit olan işlemler |
| **Başarılı** | Boyut **responseType** değerine eşit olan Işlemler **başarılı oldu** |
| **Kısıtlar Lingerror** | Boyut **responseType değeri** **Clientıngerror** veya **serverbusyerror** değerine eşit olan **işlemler**|
| **TotalBillableRequests** | **İşlemler** |
| **TotalEgress** | **Çıkış** |
| **Totalıngress** | **Giriş** |
| **Toplam Istek sayısı** | **İşlemler** |

## <a name="faq"></a>SSS

### <a name="how-should-i-migrate-existing-alert-rules"></a>Mevcut uyarı kurallarını nasıl geçirmem gerekir?

Eski depolama ölçümlerini temel alan klasik uyarı kuralları oluşturduysanız yeni ölçüm şemasına göre yeni uyarı kuralları oluşturmanız gerekir.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Yeni ölçüm verileri varsayılan olarak aynı depolama hesabında depolanır mi?

Hayır. Ölçüm verilerini bir depolama hesabına arşivlemek için [Azure Izleyici tanılama AYARı API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)'sini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure İzleyici](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Azure Izleyici 'de depolama ölçümleri](./storage-metrics-in-azure-monitor.md)
