---
title: Paylaşılan erişim imzalarını kullanan iş akışı
titleSuffix: Microsoft Genomics
description: Bu makalede, depolama hesabı anahtarları yerine paylaşılan erişim imzaları (SAS) kullanılarak Microsoft Genomiks hizmetine bir iş akışı gönderme gösterilmektedir.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 03/02/2018
ms.openlocfilehash: 82f5e8b4a0c06517381857f0d914bcb65ba41d35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93394620"
---
# <a name="submit-a-workflow-to-microsoft-genomics-using-a-sas-instead-of-a-storage-account-key"></a>Depolama hesabı anahtarı yerine SAS kullanarak Microsoft Genomiks’e iş akışını gönderme 

Bu makalede, depolama hesabı anahtarları yerine [paylaşılan erişim imzaları (SAS)](../storage/common/storage-sas-overview.md) içeren bir config.txt dosyası kullanılarak Microsoft Genomiks hizmetine bir iş akışı gönderme gösterilmektedir. Config.txt dosyasında görünür depolama hesabı anahtarı olmasıyla ilgili güvenlik endişeleri varsa bu özellik yararlı olabilir. 

Bu makalede `msgen` istemcisini yükleyip çalıştırdığınız ve Azure Depolama’yı kullanma konusunda bilgi sahibi olduğunuz kabul edilmektedir. Belirtilen örnek verileri kullanarak bir iş akışını başarıyla gönderdiyseniz, bu makaleye devam etmeye hazırsınızdır. 

## <a name="what-is-a-sas"></a>SAS nedir?
[Paylaşılan erişim imzası (SAS)](../storage/common/storage-sas-overview.md), depolama hesabınızdaki kaynaklara temsilci erişimi sağlar. Bir SAS ile hesap anahtarlarınızı paylaşmadan depolama hesabınızdaki kaynaklara erişim verebilirsiniz. Bu, uygulamalarınızda paylaşılan erişim imzaları kullanmanın anahtar noktasıdır. SAS, hesap anahtarlarınızı tehlikeye atmadan depolama kaynaklarınızı paylaşmanın güvenli bir yoludur.

Microsoft Genomiks’e gönderilen SAS, yalnızca giriş ve çıkış dosyalarının depolandığı blob veya kapsayıcıya erişim veren bir [Hizmet SAS](/rest/api/storageservices/Constructing-a-Service-SAS) olmalıdır. 

Hizmet düzeyinde paylaşılan erişim imzası (SAS) belirtecinin URI’si, SAS’nin erişim vereceği kaynağın URI’sini ve ardından SAS belirtecini içerir. SAS belirteci, SAS kimlik doğrulaması için gereken tüm bilgileri içeren sorgu dizesidir ve kaynağı, erişim için kullanılabilen izinleri, imzanın geçerli olduğu süre aralığını, isteklerin kaynağı olarak desteklenen IP adresini veya adres aralığını, bir isteğin yapılabilmesi için desteklenen protokolü, istekle ilişkili isteğe bağlı bir erişim ilkesi tanımlayıcısını ve imzanın kendisini belirtir. 

## <a name="sas-needed-for-submitting-a-workflow-to-the-microsoft-genomics-service"></a>Bir iş akışını Microsoft Genomiks hizmetine göndermek için gereken SAS
Microsoft Genomiks hizmetine gönderilen her iş akışında, her bir giriş dosyası için bir ve çıkış kapsayıcısı için bir tane olmak üzere iki veya daha fazla SAS belirteci gereklidir.

Giriş dosyaları için SAS şu özelliklere sahip olmalıdır:
 - Kapsam (hesap, kapsayıcı, blob): blob
 - Süre sonu: şu andan itibaren 48 saat
 - İzinler: okuma

Çıkış kapsayıcısı için SAS şu özelliklere sahip olmalıdır:
 - Kapsam (hesap, kapsayıcı, blob): kapsayıcı
 - Süre sonu: şu andan itibaren 48 saat
 - İzinler: okuma, yazma, silme


## <a name="create-a-sas-for-the-input-files-and-the-output-container"></a>Giriş dosyaları ve çıkış kapsayıcısı için SAS oluşturma
Bir SAS belirteci, Azure Depolama Gezini kullanılarak veya program aracılığıyla oluşturulabilir.  Kod yazıyorsanız, SAS’yi kendiniz oluşturabilir veya tercih ettiğiniz dilde Azure Depolama SDK'sını kullanabilirsiniz.


### <a name="set-up-create-a-sas-using-azure-storage-explorer"></a>Ayarlayın: Azure Depolama Gezgini'ni kullanarak SAS oluşturma

[Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/), Azure Depolama'da depoladığınız kaynakları yönetmeye yönelik bir araçtır.  Azure Depolama Gezgini’ni kullanma hakkında daha fazla bilgiyi [burada](../vs-azure-tools-storage-manage-with-storage-explorer.md) bulabilirsiniz.

Giriş dosyaları için SAS kapsamı belirli giriş dosyası (blob) olarak belirlenmelidir. Bir SAS belirteci oluşturmak için [bu yönergeleri](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) izleyin. SAS oluşturduktan sonra, ekranda sorgu dizesini içeren tam URL'nin yanı sıra sorgu dizesinin kendisi sağlanır ve bu değerler kopyalanabilir.

 ![Genomiksas Depolama Gezgini](./media/quickstart-input-sas/genomics-sas-storageexplorer.png "Genomiksas Depolama Gezgini")


### <a name="set-up-create-a-sas-programmatically"></a>Kurulumu: program aracılığıyla SAS oluşturma

Azure Depolama SDK'sı kullanarak bir SAS oluşturmak için [.NET](../storage/common/storage-sas-overview.md), [Python](../storage/blobs/storage-quickstart-blobs-python.md) ve [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) dahil birkaç dilde mevcut olan belgelere bakın. 

SDK olmadan bir SAS oluşturmak için SAS sorgu dizesi, SAS kimlik doğrulaması yapmak için gereken tüm bilgiler dahil olmak üzere doğrudan oluşturulabilir. Bu [yönergeler](/rest/api/storageservices/constructing-a-service-sas) SAS sorgu dizesinin bileşenleri ve nasıl oluşturulacağına ilişkin ayrıntıları verir. Bu [yönergelerde](/rest/api/storageservices/service-sas-examples) açıklandığı gibi, blob/kapsayıcı kimlik doğrulama bilgileri ile bir HMAC oluşturularak gerekli SAS imzası oluşturulur.


## <a name="add-the-sas-to-the-configtxt-file"></a>SAS’yi config.txt dosyasına ekleme
Bir SAS sorgu dizesini kullanarak Microsoft Genomiks hizmeti aracılığıyla iş akışı çalıştırmak için, config.txt dosyasını düzenleyerek config.txt dosyasındaki anahtarları kaldırın. Ardından, SAS sorgu dizesini (`?` ile başlar) resimde gösterildiği gibi çıkış kapsayıcısı adına ekleyin. 

![Genomiksas yapılandırması](./media/quickstart-input-sas/genomics-sas-config.png "Genomiksas yapılandırması")

Aşağıdaki komutla iş akışınızı göndermek için Microsoft Genomiks Python istemcisini kullanın ve giriş blob adlarının her birine karşılık gelen SAS sorgu dizesini ekleyin:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read file, SAS query string appended] -b2 [name of your second paired end read file, SAS query string appended]
```

### <a name="if-adding-the-input-file-names-to-the-configtxt-file"></a>config.txt dosyasına giriş dosya adlarını ekliyorsanız
Alternatif olarak, resimde gösterildiği gibi SAS sorgu belirteçleri eklenerek, eşlenmiş uç okuma dosyalarının adları config.txt dosyasına doğrudan eklenebilir:

![Genomiksas yapılandırması blobadları](./media/quickstart-input-sas/genomics-sas-config-blobnames.png "Genomiksas yapılandırması blobadları")

Bu örnekte, `-b1` ve `-b2` komutlarını çıkarıp aşağıdaki komutu kullanarak Microsoft Genomiks Python istemcisiyle iş akışınızı gönderin:

```python
msgen submit -f [full path to your config file] 
```

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede bir iş akışını `msgen` Python istemcisi aracılığıyla Microsoft Genomiks hizmetine göndermek için hesap anahtarları yerine SAS belirteçleri kullandınız. İş akışının gönderilmesi ve Microsoft Genomiks hizmetiyle kullanabileceğiniz diğer komutlar hakkında daha fazla bilgi için bkz. [SSS](frequently-asked-questions-genomics.md).