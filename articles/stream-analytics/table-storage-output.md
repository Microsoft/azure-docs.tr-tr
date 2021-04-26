---
title: Azure Stream Analytics 'den tablo depolama çıkışı
description: Bu makalede, Azure Tablo Depolamayı Azure Stream Analytics çıktı olarak açıklanmaktadır.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 558ddf065d33a552034c5b129ea70bc144c494ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013898"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Azure Stream Analytics 'den tablo depolama çıkışı

[Azure Tablo depolama](../storage/common/storage-introduction.md) , yüksek oranda kullanılabilir ve yüksek düzeyde ölçeklenebilir depolama sunarak, bir uygulamanın kullanıcı talebini karşılayacak şekilde otomatik olarak ölçeklenebilmesini sağlar. Tablo depolama, Microsoft 'un NoSQL anahtar/öznitelik deposudur ve bu, şema üzerinde daha az kısıtlama sunan yapılandırılmış veriler için kullanabileceğiniz bir veritabanıdır. Azure Tablo depolaması, verileri Kalıcılık ve verimli bir şekilde depolamak için kullanılabilir.

Aşağıdaki tabloda, tablo çıktısı oluşturmaya yönelik özellik adları ve açıklamaları listelenmektedir.

| Özellik adı | Description |
| --- | --- |
| Çıktı diğer adı |Sorgu çıkışını bu tablo depolamasına yönlendirmek için sorgularda kullanılan kolay bir ad. |
| Depolama hesabı |Çıktlarınızı gönderdiğiniz depolama hesabının adı. |
| Depolama hesabı anahtarı |Depolama hesabıyla ilişkilendirilmiş erişim anahtarı. |
| Tablo adı |Tablonun adı. Tablo yoksa oluşturulur. |
| Bölüm anahtarı |Bölüm anahtarını içeren çıkış sütununun adı. Bölüm anahtarı, bir varlığın birincil anahtarının ilk kısmını oluşturan tablo içindeki bölüm için benzersiz bir tanımlayıcıdır. Boyutu 1 KB 'a kadar olabilecek bir dize değeridir. |
| Satır anahtarı |Satır anahtarını içeren çıkış sütununun adı. Satır anahtarı, bir bölüm içindeki bir varlık için benzersiz bir tanımlayıcıdır. Bir varlığın birincil anahtarının ikinci bölümünü oluşturur. Satır anahtarı, boyutu 1 KB 'a kadar olabilecek bir dize değeridir. |
| Toplu iş boyutu |Bir toplu işlem için kayıt sayısı. Varsayılan (100) çoğu iş için yeterlidir. Bu ayarı değiştirme hakkında daha fazla bilgi için bkz. [tablo toplu işlem belirtimi](/java/api/com.microsoft.azure.storage.table.tablebatchoperation) . |

## <a name="partitioning"></a>Bölümleme

Bölüm anahtarı herhangi bir çıkış sütunudur. Çıktı yazıcılarının sayısı, [tam paralelleştirilmiş sorgular](stream-analytics-scale-jobs.md)için giriş bölümlemesini izler.

## <a name="output-batch-size"></a>Çıkış toplu iş boyutu

En büyük ileti boyutu için bkz. [Azure depolama sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Varsayılan değer tek bir işlem başına 100 varlıkdır, ancak gerektiğinde daha küçük bir değere yapılandırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: Azure portalını kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-portal.md)
* [Hızlı başlangıç: Azure CLı kullanarak Azure Stream Analytics işi oluşturma](quick-create-azure-cli.md)
* [Hızlı başlangıç: ARM şablonu kullanarak Azure Stream Analytics işi oluşturma](quick-create-azure-resource-manager.md)
* [Hızlı başlangıç: Azure PowerShell kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-powershell.md)
* [Hızlı başlangıç: Visual Studio 'Yu kullanarak Azure Stream Analytics işi oluşturma](stream-analytics-quick-create-vs.md)
* [Hızlı başlangıç: Visual Studio Code Azure Stream Analytics iş oluşturma](quick-create-visual-studio-code.md)