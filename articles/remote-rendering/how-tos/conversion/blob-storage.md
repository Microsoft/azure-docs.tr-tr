---
title: Model dönüştürmesi için Azure Blob depolamayı kullanma
description: Model dönüştürmesi için blob depolamayı ayarlamaya ve kullanmaya yönelik yaygın adımları açıklar.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 629b48a378dd029d9952270e3c71762a5bb7aa82
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306730"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Model dönüştürmesi için Azure Blob depolamayı kullanma

[Model dönüştürme](model-conversion.md) hizmeti, giriş verilerini almak ve çıkış verilerini depolamak Için Azure Blob depolama alanına erişim gerektirir. Bu makalede en sık kullanılan adımların nasıl yapılacağı açıklanır.

## <a name="prepare-azure-storage-accounts"></a>Azure depolama hesaplarını hazırlama

- Depolama hesabı oluşturma (StorageV2)
- Depolama hesabında bir giriş blobu kapsayıcısı oluşturun (örneğin, "arrinput" adlı)
- Depolama hesabında bir çıkış blobu kapsayıcısı oluşturun (örneğin, "arroutput" adlı)

> [!TIP]
> Depolama hesabınızı ayarlama hakkında adım adım yönergeler için [hızlı başlangıç: bir modeli işleme Için dönüştürme](../../quickstarts/convert-model.md) bölümüne bakın

Depolama hesabı ve BLOB kapsayıcıları oluşturma, aşağıdaki araçlardan biriyle yapılabilir:

- [Azure portalı](https://portal.azure.com)
- [az komut satırı](/cli/azure/install-azure-cli)
- [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)
- SDK 'lar (C#, Python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Azure uzaktan Işleme 'nin depolama hesabınıza erişebildiğinden emin olun

Azure uzaktan saklama 'nın, depolama hesabınızdan model verilerini alması ve verileri buna geri yazması gerekir.

Aşağıdaki iki yolla depolama hesabınıza Azure uzaktan Işleme erişimi verebilirsiniz:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Azure depolama hesabınızı Azure uzaktan Işleme hesabınızla bağlama

[Hesap oluşturma](../create-an-account.md#link-storage-accounts) bölümünde verilen adımları izleyin.

### <a name="retrieve-sas-for-the-storage-containers"></a>Depolama kapsayıcıları için SAS alma

Depolanan erişim imzaları (SAS), giriş için okuma erişimi sağlamak ve çıkış için yazma erişimi sağlamak üzere kullanılır. Her model dönüştürülürken yeni URI 'Ler oluşturmanızı öneririz. URI 'Lerin süresi bir süre sonra sona erdiğinden, daha uzun bir süre boyunca kalıcı hale getirerek uygulamanızı beklenmedik şekilde bozabilir.

SAS hakkındaki ayrıntılar [SAS belgelerinde](../../../storage/common/storage-sas-overview.md)bulunabilir.

Bir SAS URI 'SI aşağıdakilerden biri kullanılarak oluşturulabilir:

- az PowerShell modülü
  - [örnek PowerShell betiklerine](../../samples/powershell-example-scripts.md) bakın
- [az komut satırı](/cli/azure/install-azure-cli)
- [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)
  - "paylaşılan erişim Imzasını al" kapsayıcısına sağ tıklama (okuma, giriş kapsayıcısı için erişimi listeleme, çıkış kapsayıcısı için yazma erişimi)
- SDK 'lar (C#, Python...)

Varlık dönüştürmesinde paylaşılan erişim Imzalarının kullanılmasına bir örnek, [PowerShell örnek betiklerinin](../../samples/powershell-example-scripts.md#script-conversionps1)Conversion.ps1 gösterilmektedir.

## <a name="upload-an-input-model"></a>Giriş modelini karşıya yükleme

Bir modeli dönüştürmeye başlamak için, aşağıdaki seçeneklerden birini kullanarak karşıya yüklemeniz gerekir:

- [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) -Azure Blob Storage 'da dosyaları karşıya yüklemek/indirmek/yönetmek için uygun bir kullanıcı arabirimi
- [Azure komut satırı](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Azure PowerShell modülü](/powershell/azure/install-az-ps)
  - [örnek PowerShell betiklerine](../../samples/powershell-example-scripts.md) bakın
- [Depolama SDK 'sını kullanma (Python, C#...)](../../../storage/index.yml)
- [Azure depolama REST API 'Lerini kullanma](/rest/api/storageservices/blob-service-rest-api)

Dönüştürme için verileri karşıya yükleme hakkında bir örnek için [PowerShell örnek betiklerinin](../../samples/powershell-example-scripts.md#script-conversionps1)Conversion.ps1 bakın.

> [!Note]
> Bir giriş modelini karşıya yüklerken, hizmette [Windows yol uzunluğu sınırı](https://docs.mxicrosoft.com/windows/win32/fileio/maximum-file-path-limitation) sorunlarından kaçınmak için uzun dosya adlarından ve/veya klasör yapılarından kaçınmak için dikkatli olmanız gerekmez. 

## <a name="get-a-sas-uri-for-the-converted-model"></a>Dönüştürülen model için SAS URI 'SI al

Bu adım, [depolama kapsayıcıları IÇIN SAS almaya](#retrieve-sas-for-the-storage-containers)benzer. Ancak, bu süre, çıkış kapsayıcısına yazılmış olan model dosyası için bir SAS URI 'SI almanız gerekir.

Örneğin, [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)aracılığıyla SAS URI 'sini almak için model dosyasına sağ tıklayın ve "paylaşılan erişim imzasını al" seçeneğini belirleyin.

Depolama hesabınızı Azure uzaktan Işleme hesabınıza eklemediğiniz takdirde yük modelleriyle paylaşılan erişim Imzası (SAS) gereklidir. [Hesap oluşturma](../create-an-account.md#link-storage-accounts)bölümünde hesabınızı nasıl bağlayabileceğinizi öğrenebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Model dönüştürmeyi yapılandırma](configure-model-conversion.md)
- [Model dönüştürme REST API](conversion-rest-api.md)
