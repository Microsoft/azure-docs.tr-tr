---
title: İşleme yeteneklerini kullanma
description: Azure Batch işleme özelliklerini kullanma. Doğrudan veya bir istemci uygulama eklentisinden çağrılan Batch Explorer uygulamasını kullanmayı deneyin.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2020
ms.topic: how-to
ms.openlocfilehash: dc3d2cc53b478b1ec955d8f4b3717b0407772849
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496635"
---
# <a name="using-azure-batch-rendering"></a>Azure Batch işleme kullanma

> [!IMPORTANT]
> İşlenen VM görüntüleri ve kullanım için ödeme lisansı [kullanım dışı bırakılmıştır ve 29 şubat 2024 tarihinde kullanımdan](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/)kaldırılacaktır. Toplu Işi işlemek için kullanmak üzere [Özel BIR VM görüntüsü ve standart uygulama lisanslama kullanılmalıdır.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

Azure Batch işleme kullanmanın birkaç yolu vardır:

* API'ler:
  * Batch API 'Lerinden herhangi birini kullanarak kod yazın.  Geliştiriciler Azure Batch yeteneklerini bulut ya da şirket içinde bulunan mevcut uygulamalarına veya iş akışına tümleştirebilir.
* Komut satırı araçları:
  * [Azure komut satırı](/cli/azure/) veya [PowerShell](/powershell/azure/) , toplu kullanım betiği için kullanılabilir.
  * Özellikle, [Batch CLI şablonu desteği](./batch-cli-templates.md) havuzları oluşturmayı ve işleri göndermeyi çok daha kolay hale getirir.
* Batch Explorer kullanıcı arabirimi:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) , Batch hesaplarının yönetilmesini ve izlenmesini sağlayan platformlar arası bir istemci aracıdır.
  * İşleme uygulamalarının her biri için, kolayca havuzlar oluşturmak ve işleri göndermek üzere kullanılabilecek bir dizi havuz ve iş şablonu sağlanır.  Şablonlar kümesi, şablon dosyaları GitHub 'dan erişildiği şekilde uygulama kullanıcı arabiriminde listelenir.
  * Özel şablonlar sıfırdan yazılabilir veya GitHub 'dan sağlanan şablonlar kopyalanabilir ve değiştirilebilir.
* İstemci uygulaması eklentileri:
  * Toplu işlemenin doğrudan istemci tasarımı ve modelleme uygulamaları içinden kullanılmasına izin veren eklentiler vardır.  Eklentiler genellikle geçerli 3B modeliyle ilgili bağlamsal bilgilerle Batch Explorer uygulamayı çağırır ve varlıkları yönetmeye yardımcı olacak özellikler içerir.

Geliştirici olmayan ve Azure uzmanlarından sorumlu olmayan son kullanıcılar için Azure Batch işleme ve en basit yolu yapmanın en iyi yolu, doğrudan veya bir istemci uygulaması eklentisiyle çağrılan Batch Explorer uygulamasını kullanmaktır.

## <a name="using-batch-explorer"></a>Batch Explorer kullanma

İşleme gerçekleştirmek için Batch Explorer kullanmaya yönelik adım adım bir öğretici için, bkz. [blender öğreticisi](./tutorial-rendering-batchexplorer-blender.md).

### <a name="download-and-install"></a>İndir ve yükle

Batch Explorer [Indirmeleri](https://azure.github.io/BatchExplorer/) Windows, OSX ve Linux 'ta kullanılabilir.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Havuzlar oluşturmak ve işleri çalıştırmak için şablonları kullanma

Kapsamlı bir şablon kümesi, havuzları, işleri ve görevleri doğrudan Batch ile oluşturmak için gereken tüm özellikleri belirtmek zorunda kalmadan havuzlar oluşturmayı ve çeşitli işleme uygulamaları için iş göndermeyi kolaylaştıran Batch Explorer ile kullanılabilir.  Batch Explorer bulunan şablonlar [bir GitHub deposunda](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)depolanır ve görünür.

![Batch Explorer Galerisi](./media/batch-rendering-using/batch-explorer-gallery.png)

Şablonlar, Market işleme VM görüntülerinde bulunan tüm uygulamalar için karşılamak tarafından sağlanır.  Her uygulama için, CPU ve GPU havuzları, Windows ve Linux havuzları için havuz şablonları dahil olmak üzere birden çok şablon mevcuttur; iş şablonları tam çerçeve veya döşeli blender işleme ve V-Ray dağıtılmış işleme içerir. Sağlanan şablonlar kümesi, havuz otomatik ölçeklendirme gibi diğer toplu Iş özellikleri için zaman içinde genişletilir.

Özel şablonların, sıfırdan veya sağlanan şablonları değiştirerek üretilmesi de mümkündür. Özel şablonlar, Batch Explorer ' Galeri ' bölümündeki ' Yerel Şablonlar ' öğesi seçilerek kullanılabilir.

### <a name="file-system-and-data-movement"></a>Dosya sistemi ve veri taşıma

Batch Explorer 'daki ' Data ' bölümü, dosyaların yerel bir dosya sistemi ve Azure depolama hesapları arasında kopyalanmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Toplu işleme örnekleri için iki öğreticiyi deneyin:

* [Azure CLı kullanarak işleme](./tutorial-rendering-cli.md)
* [Batch Explorer'ı kullanarak işleme](./tutorial-rendering-batchexplorer-blender.md)
