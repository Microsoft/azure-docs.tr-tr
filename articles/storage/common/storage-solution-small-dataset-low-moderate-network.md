---
title: Düşük-orta ağ bant genişliğine sahip küçük veri kümeleri için Azure veri aktarımı seçenekleri | Microsoft Docs
description: Ortamınızda orta düzeyde ağ bant genişliğiniz olduğunda ve küçük veri kümelerini aktarmayı planlıyorsanız, veri aktarımı için bir Azure çözümü seçme hakkında bilgi edinin.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023236"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Bant genişliği düşük veya orta olduğunda küçük veri kümeleri için veri aktarımı
 
Bu makalede, ortamınızda orta düzeyde ağ bant genişliğiniz olduğunda ve küçük veri kümelerini aktarmayı planladığınızda veri aktarımı çözümlerine genel bir bakış sağlanır. Makalede ayrıca önerilen veri aktarımı seçenekleri ve bu senaryonun ilgili anahtar özelliği matrisi de açıklanmaktadır.

Tüm kullanılabilir veri aktarımı seçeneklerine genel bir bakış için bkz. [Azure veri aktarımı çözümü seçme](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Senaryo açıklaması

Küçük veri kümeleri, boyutu GB ile ölçülen ve en fazla birkaç TB'a ulaşan verileri ifade eder. Düşük ila orta ağ bant genişliği, 45 Mb/sn (veri merkezindeki T3 bağlantısı) ile 1 Gb/sn arasındaki hızları ifade eder.

- Yalnızca birkaç dosyayı aktarıyorsanız ve veri aktarımını otomatikleştirmeniz gerekmiyorsa, araçları bir grafik arabirimiyle değerlendirin.
- Sistem yönetimi konusunda bilginiz varsa komut satırı veya program/betik aracılığıyla kullanılan araçları tercih edebilirsiniz.

## <a name="recommended-options"></a>Önerilen seçenekler

Bu senaryoda önerilen seçenekler şunlardır:

- Azure Depolama Gezgini ve Azure portal Azure depolama gibi **grafik arabirim araçları** . Bu, verilerinizi görüntülemenin ve birkaç dosyayı hızlı bir şekilde aktarmanın kolay bir yolunu sağlar.

    - **Azure Depolama Gezgini** -bu platformlar arası araç, Azure depolama hesaplarınızın içeriğini yönetmenizi sağlar. Blob, dosya, kuyruk, tablo ve Azure Cosmos DB varlığı gibi nesneleri karşıya yükleyebilir, indirebilir ve yönetebilirsiniz. Blob depolama alanıyla birlikte kullanarak blobları ve klasörleri yönetebilir, yerel dosya sisteminizle Blob depolama alanı veya farklı depolama hesapları arasında blobları karşıya yükleme ve indirme işlemlerini gerçekleştirebilirsiniz.
    - **Azure Portal** -Azure Portal Azure depolama, dosyaları keşfetmeye ve yeni dosyaları tek seferde karşıya yüklemeye yönelik Web tabanlı bir arabirim sağlar. Herhangi bir araç yüklemeden veya komut kullanmadan dosyalarınıza hızlıca göz atmak veya birkaç tane dosyayı karşıya yüklemek için iyi bir seçenektir.

- AzCopy/PowerShell/Azure CLı ve Azure depolama REST API 'Leri gibi **komut dosyası/programlama araçları** .

    - **AzCopy** -bu komut satırı aracını, En Iyi performansla Azure Blob 'Larına, dosyalarına ve tablo depolamasına verileri kolayca kopyalamak için kullanın. AzCopy eşzamanlılık ve paralellik desteğine ek olarak kesintiye uğrayan kopyalama işlemlerini sürdürme olanağı sunar.
    - **Azure PowerShell** -sistem yönetimi sayesinde kullanıcılara rahat bir şekilde veri aktarmak Için Azure PowerShell Azure Storage modülünü kullanın.
    - **Azure CLI** -bu platformlar arası aracı kullanarak Azure hizmetlerini yönetin ve verileri Azure depolama 'ya yükleyin.
    - **Azure depolama REST API 'leri/SDK 'lar** – bir uygulama oluştururken, uygulamayı Azure Storage REST API 'Leri/SDK 'larda geliştirebilir ve birden çok dilde sunulan Azure istemci kitaplıklarını kullanabilirsiniz.


## <a name="comparison-of-key-capabilities"></a>Anahtar özellikleri karşılaştırması

Aşağıdaki tabloda, temel özellikler arasındaki farklar özetlenmiştir.

| Özellik | Azure Depolama Gezgini | Azure portalı | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure depolama REST API 'Leri veya SDK 'Ları |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Kullanılabilirlik | İndirme ve yükleme <br>Tek başına aracı | Azure portal web tabanlı araştırma araçları | Komut satırı aracı |.NET, Java, Python, JavaScript, C++, Go, Ruby ve PHP 'de programlanabilir arabirimler |
| Grafik arabirim | Yes | Yes | Hayır | Hayır |
| Desteklenen platformlar | Windows, Mac, Linux | Web tabanlı |Windows, Mac, Linux |Tüm platformlar |
| İzin verilen BLOB depolama işlemleri<br>Bloblar ve klasörler için | Karşıya Yükleme<br>İndir<br>Yönetme | Karşıya Yükleme<br>İndir<br>Yönetme |Karşıya Yükleme<br>İndir<br>Yönetme | Evet, özelleştirilebilir |
| Data Lake Gen1 Storage izin verildi<br>dosyalar ve klasörler için işlemler | Karşıya Yükleme<br>İndir<br>Yönetme | No |Karşıya Yükleme<br>İndir<br>Yönetme                   | No |
| İzin verilen dosya depolama işlemleri<br>dosyalar ve dizinler için | Karşıya Yükleme<br>İndir<br>Yönetme | Karşıya Yükleme<br>İndir<br>Yönetme   |Karşıya Yükleme<br>İndir<br>Yönetme | Evet, özelleştirilebilir |
| İzin verilen tablo depolama işlemleri<br>tablolar için |Yönetme | No |AzCopy v7 'ta tablo desteği |Evet, özelleştirilebilir|
| İzin verilen kuyruk depolaması | Yönetme | Hayır  |Hayır | Evet, özelleştirilebilir|


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Depolama Gezgini ile veri aktarmayı](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md)öğrenin.
- [AzCopy ile verileri aktarma](./storage-use-azcopy-v10.md)