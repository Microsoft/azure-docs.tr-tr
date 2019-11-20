---
title: Azure Blob 'Ları, Azure dosyalarını veya Azure disklerini ne zaman kullanacağınızı belirleme
description: Hangi teknolojiyi kullanacağınıza karar vermenize yardımcı olması için Azure 'da verileri depolamanın ve erişmenin farklı yolları hakkında bilgi edinin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671040"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Azure Blob 'Ları, Azure dosyalarını veya Azure disklerini ne zaman kullanacağınızı belirleme

Microsoft Azure buluttaki verilerinizi depolamak ve bunlara erişmek için Azure depolama 'da çeşitli özellikler sağlar. Bu makalede Azure dosyaları, Bloblar ve diskler yer almaktadır ve bu özellikler arasında seçim yapmanıza yardımcı olmak üzere tasarlanmıştır.

## <a name="scenarios"></a>Senaryolar

Aşağıdaki tabloda dosyalar, Bloblar ve diskler karşılaştırılmaktadır ve her biri için uygun olan örnek senaryolar gösterilmiştir.

| Özellik | Açıklama | Kullanılması gereken durumlar |
|--------------|-------------|-------------|
| **Azure dosyaları** | Bir SMB arabirimi, istemci kitaplıkları ve her yerden depolanan dosyaların erişimine izin veren bir [rest arabirimi](/rest/api/storageservices/file-service-rest-api) sağlar. | Azure 'da çalışan ve diğer uygulamalar arasında veri paylaşmak için zaten yerel dosya sistemi API 'Lerini kullanan bir uygulamayı buluta "kaldırmak ve kaydırmak" istiyorsunuz.<br/><br/>Birçok sanal makineden erişilmesi gereken geliştirme ve hata ayıklama araçlarını depolamak istiyorsunuz. |
| **Azure Blob 'Ları** | Yapılandırılmamış veri depolanmasına ve Blok Bloblarında çok büyük ölçekte erişilmesine izin veren istemci kitaplıkları ve [rest arabirimi](/rest/api/storageservices/blob-service-rest-api) sağlar.<br/><br/>Ayrıca kurumsal büyük veri analizi çözümleri için [Azure Data Lake Storage 2.](../blobs/data-lake-storage-introduction.md) destekler. | Uygulamanızın akış ve rastgele erişim senaryolarını desteklemesini istiyorsunuz.<br/><br/>Uygulama verilerine dilediğiniz yerden erişebilmek istiyorsunuz.<br/><br/>Azure 'da bir kurumsal veri Gölü oluşturmak ve büyük veri analizi gerçekleştirmek istiyorsunuz. |
| **Azure diskleri** | , Verilerin kalıcı olarak depolanmasına ve bağlı bir sanal sabit diskten erişilmesine izin veren istemci kitaplıkları ve [rest arabirimi](/rest/api/compute/manageddisks/disks/disks-rest-api) sağlar. | Kalıcı disklere veri okumak ve yazmak için yerel dosya sistemi API 'Leri kullanan uygulamaları kaldırmak ve kaydırmak istiyorsunuz.<br/><br/>Diskin eklendiği sanal makinenin dışından erişilmesi gerekmeyen verileri depolamak istiyorsunuz. |


## <a name="next-steps"></a>Sonraki adımlar

Verilerinizin nasıl depolandığı ve erişildiği hakkında kararlar verirken, dahil edilen maliyetleri de göz önünde bulundurmanız gerekir. Daha fazla bilgi için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).
  
Bazı SMB özellikleri bulut için geçerli değildir. Daha fazla bilgi için bkz. [Azure dosya hizmeti tarafından desteklenmeyen özellikler](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Azure Blob 'Ları hakkında daha fazla bilgi için bkz. [Azure Blob depolama nedir?](../blobs/storage-blobs-overview.md).

Disk Depolama hakkında daha fazla bilgi için bkz. [yönetilen disklere giriş](../../virtual-machines/windows/managed-disks-overview.md).

Azure dosyaları hakkında daha fazla bilgi için, [Azure dosyaları dağıtımını planlama](../files/storage-files-planning.md)başlıklı makalemize bakın.