---
title: BLOB depolama için istekte bir şifreleme anahtarı sağlayın
titleSuffix: Azure Storage
description: Azure Blob depolamada istek yapan istemciler, istek temelinde bir şifreleme anahtarı sağlama seçeneğine sahiptir. İstek üzerine şifreleme anahtarı dahil olmak üzere, BLOB depolama işlemleri için şifreleme ayarları üzerinde ayrıntılı denetim sağlar.
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcc5c02c4a37e205622470260d3c620ad76d07d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97694708"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>BLOB depolama için istekte bir şifreleme anahtarı sağlayın

Azure Blob depolama 'ya karşı istek yapan istemciler, istek başına temelinde bir AES-256 şifreleme anahtarı sağlama seçeneğine sahiptir. İstek üzerine şifreleme anahtarı dahil olmak üzere, BLOB depolama işlemleri için şifreleme ayarları üzerinde ayrıntılı denetim sağlar. Müşteri tarafından sunulan anahtarlar, Azure Key Vault veya başka bir anahtar deposunda depolanabilir.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>Okuma ve yazma işlemlerini şifreleme

İstemci uygulaması istekte bir şifreleme anahtarı sağlıyorsa, blob verilerini okurken ve yazarken Azure Storage şifreleme ve şifre çözme işlemlerini saydam bir şekilde gerçekleştirir. Azure depolama, blob içeriklerinin yanı sıra şifreleme anahtarının SHA-256 karmasını yazar. Karma, blob 'a yönelik tüm sonraki işlemlerin aynı şifreleme anahtarını kullanmasını doğrulamak için kullanılır.

Azure depolama, istemcinin istekle birlikte gönderdiği şifreleme anahtarını depolamaz veya yönetemez. Şifreleme veya şifre çözme işlemi tamamlandıktan sonra anahtar güvenli bir şekilde atılır.

Bir istemci istekte müşteri tarafından sağlanmış bir anahtar kullanarak bir blob oluşturduğunda veya güncelleştirayarlandığında, bu Blobun sonraki okuma ve yazma istekleri de anahtarı sağlamalıdır. Anahtar, zaten müşteri tarafından sağlanmış bir anahtarla şifrelenen bir blob isteği üzerinde sağlanmazsa, istek 409 (çakışma) hata koduyla başarısız olur.

İstemci uygulaması istekte bir şifreleme anahtarı gönderirse ve depolama hesabı Microsoft tarafından yönetilen bir anahtar veya müşteri tarafından yönetilen anahtar kullanılarak da şifrelenirse Azure Storage, şifreleme ve şifre çözme isteğinde belirtilen anahtarı kullanır.

Şifreleme anahtarını isteğin bir parçası olarak göndermek için, bir istemcinin HTTPS kullanarak Azure depolama ile güvenli bir bağlantı kurması gerekir.

Her blob anlık görüntüsünün kendi şifreleme anahtarı olabilir.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Müşteri tarafından sunulan anahtarları belirtmek için istek üstbilgileri

İstemciler, REST çağrıları için aşağıdaki üst bilgileri kullanarak blob depolamaya yönelik bir istek üzerindeki şifreleme anahtarı bilgilerini güvenli bir şekilde geçirebilir:

|İstek Başlığı | Description |
|---------------|-------------|
|`x-ms-encryption-key` |Hem yazma hem de okuma istekleri için gereklidir. Base64 kodlamalı AES-256 şifreleme anahtarı değeri. |
|`x-ms-encryption-key-sha256`| Hem yazma hem de okuma istekleri için gereklidir. Şifreleme anahtarının Base64 ile kodlanmış SHA256. |
|`x-ms-encryption-algorithm` | Yazma istekleri için gereklidir, okuma istekleri için isteğe bağlıdır. Verilen anahtar kullanılarak verileri şifrelerken kullanılacak algoritmayı belirtir.  Bu üstbilginin değeri olmalıdır `AES256` . |

İstekte şifreleme anahtarlarının belirtilmesi isteğe bağlıdır. Ancak, bir yazma işlemi için yukarıda listelenen üst bilgilerden birini belirtirseniz, bunların tümünü belirtmeniz gerekir.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Müşteri tarafından sunulan anahtarları destekleyen BLOB depolama işlemleri

Aşağıdaki BLOB depolama işlemleri, bir istekte müşterinin sunduğu şifreleme anahtarlarını göndermeyi destekler:

- [İkili Büyük Nesne Koyma](/rest/api/storageservices/put-blob)
- [Öbek listesini yerleştirme](/rest/api/storageservices/put-block-list)
- [Yerleştirme bloğu](/rest/api/storageservices/put-block)
- [Bloğu URL 'den koy](/rest/api/storageservices/put-block-from-url)
- [Yerleştirme sayfası](/rest/api/storageservices/put-page)
- [URL 'den sayfa koy](/rest/api/storageservices/put-page-from-url)
- [Ekleme bloğu](/rest/api/storageservices/append-block)
- [Blob özelliklerini ayarla](/rest/api/storageservices/set-blob-properties)
- [Blob meta verilerini ayarla](/rest/api/storageservices/set-blob-metadata)
- [Blob al](/rest/api/storageservices/get-blob)
- [BLOB özelliklerini Al](/rest/api/storageservices/get-blob-properties)
- [Blob meta verilerini al](/rest/api/storageservices/get-blob-metadata)
- [İkili Büyük Nesne Anlık Görüntüsünü Alma](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Müşteri tarafından sunulan anahtarları döndür

Bir blobu şifrelemek için kullanılan bir şifreleme anahtarını döndürmek için, blobu indirin ve ardından yeni şifreleme anahtarıyla yeniden yükleyin.

> [!IMPORTANT]
> Azure portal, istekte sağlanmış bir anahtarla şifrelenen bir kapsayıcı veya Blobun okumak veya buradan yazmak için kullanılamaz.
>
> Azure Key Vault gibi güvenli bir anahtar deposunda blob depolamaya bir istekte sağladığınız şifreleme anahtarını koruduğunuzdan emin olun. Şifreleme anahtarı olmadan bir kapsayıcıda veya Blobun üzerinde yazma işlemi denerseniz, işlem başarısız olur ve nesneye erişiminizi kaybedersiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [.NET ile BLOB depolama için istekte müşteri tarafından sağlanmış bir anahtar belirtin](storage-blob-customer-provided-key.md)
- [Bekleyen veri için Azure Depolama şifrelemesi](../common/storage-service-encryption.md)
