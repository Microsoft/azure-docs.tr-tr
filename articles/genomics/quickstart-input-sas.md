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
ms.openlocfilehash: d6228762b9a1299d8e9229f7a0f73dc7d0bca2b2
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248579"
---
# <a name="submit-a-workflow-to-microsoft-genomics-using-a-sas-instead-of-a-storage-account-key"></a>Depolama hesabı anahtarı yerine SAS kullanarak Microsoft Genomiks 'ye iş akışı gönderme 

Bu makalede, depolama hesabı anahtarları yerine [paylaşılan erişim imzaları (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) içeren bir config. txt dosyası kullanılarak Microsoft Genomiks hizmetine bir iş akışı gönderme gösterilmektedir. Bu özellik, depolama hesabı anahtarının Config. txt dosyasında görünür olmasının güvenlik sorunları varsa yararlı olabilir. 

Bu makalede, `msgen` istemcisini zaten yüklediğinizi ve çalıştırdığınız ve Azure depolama 'yı kullanma hakkında bilgi sahibi olduğunuz varsayılır. Belirtilen örnek verileri kullanarak bir iş akışını başarıyla gönderdiyseniz, bu makaleye devam etmeye hazırsınızdır. 

## <a name="what-is-a-sas"></a>SAS nedir?
[Paylaşılan erişim imzası (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) Depolama hesabınızdaki kaynaklara temsilci erişimi sağlar. SAS ile, hesap anahtarlarınızı paylaşmadan Depolama hesabınızdaki kaynaklara erişim izni verebilirsiniz. Bu, uygulamalarınızda paylaşılan erişim imzaları kullanmanın önemli noktasıdır. bir SAS, hesap anahtarlarınızla ödün vermeden depolama kaynaklarınızı paylaşmanın güvenli bir yoludur.

Microsoft Genomiks 'ye gönderilen SAS, yalnızca giriş ve çıkış dosyalarının depolandığı blob veya kapsayıcıya erişim veren bir [HIZMET SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS) olmalıdır. 

Hizmet düzeyi paylaşılan erişim imzası (SAS) belirtecinin URI 'si, sa 'nın erişim yetkisini alacak Kaynak URI 'sinden ve ardından SAS belirteci ile oluşur. SAS belirteci, SAS kimlik doğrulaması için gereken tüm bilgileri içeren sorgu dizesidir ve kaynak, erişim için kullanılabilir izinler, imzanın geçerli olduğu zaman aralığı, desteklenen IP adresi veya adres isteklerin kaynaklanlabileceği Aralık, bir isteğin üzerinde yapılabilecek desteklenen protokol, istekle ilişkili bir isteğe bağlı erişim ilkesi tanımlayıcısı ve imza kendisi. 

## <a name="sas-needed-for-submitting-a-workflow-to-the-microsoft-genomics-service"></a>Microsoft Genomiks hizmetine iş akışı göndermek için gereken SAS
Her bir giriş dosyası ve diğeri çıkış kapsayıcısı için bir tane olmak üzere Microsoft Genomiks hizmetine gönderilen her iş akışı için iki veya daha fazla SAS belirteci gereklidir.

Giriş dosyaları için SAS şu özelliklere sahip olmalıdır:
 - Kapsam (hesap, kapsayıcı, blob): blob
 - Süre sonu: 48 saat sonra
 - İzinler: okuma

Çıkış kapsayıcısı için SAS şu özelliklere sahip olmalıdır:
 - Kapsam (hesap, kapsayıcı, blob): kapsayıcı
 - Süre sonu: 48 saat sonra
 - İzinler: okuma, yazma, silme


## <a name="create-a-sas-for-the-input-files-and-the-output-container"></a>Giriş dosyaları ve çıkış kapsayıcısı için SAS oluşturma
Azure Depolama Gezgini veya program aracılığıyla bir SAS belirteci oluşturmanın iki yolu vardır.  Kod yazıyorsanız, SAS 'yi kendiniz oluşturabilir veya tercih ettiğiniz dilde Azure Storage SDK 'sını kullanabilirsiniz.


### <a name="set-up-create-a-sas-using-azure-storage-explorer"></a>Kurulum: Azure Depolama Gezgini kullanarak SAS oluşturma

[Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) , Azure depolama 'da depoladığınız kaynakları yönetmek için kullanılan bir araçtır.  [Azure Depolama Gezgini nasıl](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)kullanılacağı hakkında daha fazla bilgi edinebilirsiniz.

Giriş dosyaları için SAS, belirli giriş dosyası (blob) kapsamında olmalıdır. SAS belirteci oluşturmak için [Bu yönergeleri](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)izleyin. SAS oluşturduktan sonra sorgu dizesinin tam URL 'SI ve sorgu dizesinin kendisi sağlanır ve ekrandan kopyalanabilir.

 ![Genomiksas Depolama Gezgini](./media/quickstart-input-sas/genomics-sas-storageexplorer.png "genomiksas Depolama Gezgini")


### <a name="set-up-create-a-sas-programmatically"></a>Kurulumu: program aracılığıyla SAS oluşturma

Azure depolama SDK 'sını kullanarak bir SAS oluşturmak için, [.net](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-python-how-to-use-blob-storage)ve [Node. js](https://docs.microsoft.com/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage)dahil olmak üzere çeşitli dillerdeki mevcut belgelere başvurun. 

SDK olmadan bir SAS oluşturmak için SAS sorgu dizesi, SAS kimlik doğrulaması için gereken tüm bilgiler dahil olmak üzere doğrudan oluşturulabilir. Bu [yönergeler](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) SAS sorgu dizesinin bileşenlerini ve nasıl oluşturulacağını ayrıntılandırır. Gerekli SAS imzası, bu [yönergelerde](https://docs.microsoft.com/rest/api/storageservices/service-sas-examples)açıklandığı gibi BLOB/kapsayıcı kimlik doğrulama bilgileri KULLANıLARAK bir HMAC üretilerek oluşturulur.


## <a name="add-the-sas-to-the-configtxt-file"></a>SAS 'yi config. txt dosyasına ekleme
Bir SAS sorgu dizesi kullanarak Microsoft Genomiks hizmeti aracılığıyla iş akışı çalıştırmak için config. txt dosyasını düzenleyerek anahtarları config. txt dosyasından kaldırın. Ardından, gösterildiği gibi SAS sorgu dizesini (`?` ile başlayan) çıkış kapsayıcısı adına ekleyin. 

![GENOMIKSAS yapılandırması](./media/quickstart-input-sas/genomics-sas-config.png "genomiksas yapılandırması")

Aşağıdaki komutla iş akışınızı göndermek için Microsoft Genomiks Python istemcisini kullanın: her giriş blobu adına karşılık gelen SAS sorgu dizesini ekleyerek.

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read file, SAS query string appended] -b2 [name of your second paired end read file, SAS query string appended]
```

### <a name="if-adding-the-input-file-names-to-the-configtxt-file"></a>Giriş dosyası adlarını config. txt dosyasına ekleme
Alternatif olarak, eşleştirilmiş uç okuma dosyalarının adları,, SAS sorgu belirteçleri gösterildiği gibi eklenerek doğrudan config. txt dosyasına eklenebilir:

![Genomiksas yapılandırması blob adları](./media/quickstart-input-sas/genomics-sas-config-blobnames.png "genomiksas yapılandırması blob adları")

Bu durumda, `-b1` ve `-b2` komutlarını atlayarak iş akışınızı aşağıdaki komutla göndermek için Microsoft Genomiks Python istemcisini kullanın:

```python
msgen submit -f [full path to your config file] 
```

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, `msgen` Python istemcisi üzerinden Microsoft Genomiks hizmetine bir iş akışı göndermek için hesap anahtarları yerine SAS belirteçlerini kullandınız. İş akışı gönderme ve Microsoft Genomiks hizmetiyle kullanabileceğiniz diğer komutlar hakkında daha fazla bilgi için bkz. [SSS](frequently-asked-questions-genomics.md). 
