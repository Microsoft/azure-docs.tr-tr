---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673557"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Azure depolamaya erişmek için uygulamanızı yapılandırma
Uygulamanızın depolama hizmetlerine erişmek için kimlik doğrulaması için iki yolu vardır:

* Paylaşılan anahtar: Paylaşılan anahtar yalnızca test amacıyla kullanın.
* Paylaşılan erişim imzası (SAS): Üretim uygulamaları için SAS kullanın

### <a name="shared-key"></a>Paylaşılan Anahtar
Paylaşılan anahtar kimlik doğrulaması, uygulamanızın hesap adınızı ve hesap anahtarını depolama hizmetlerine erişmek için kullanacağı anlamına gelir. Hızlı bir şekilde bu kitaplığının nasıl kullanılacağını gösteren amacıyla, biz de bu Başlarken paylaşılan anahtar kimlik doğrulaması kullanacaklardır.

> [!WARNING] 
> **Yalnızca test amacıyla paylaşılan anahtar kimlik doğrulamasını kullanın!** Hesap adınızı ve hesap anahtarını, ilişkili depolama hesabına tam okuma/yazma erişimi veren, uygulamanızı indirir herkesin dağıtılacaktır. Bu **değil** güvenilmeyen istemciler tarafından tehlikeye anahtarınızı sahip risk gibi iyi bir uygulama.
> 
> 

Paylaşılan anahtar kimlik doğrulamasını kullanırken, oluşturacağınız bir [bağlantı dizesi](../articles/storage/common/storage-configure-connection-string.md). Bağlantı dizesi oluşur:  

* **Defaultendpointsprotocol =** -HTTP veya HTTPS seçebilirsiniz. Ancak, HTTPS kullanarak önemle tavsiye edilir.
* **Hesap adı** -depolama hesabınızın adı
* **Hesap anahtarı** - [Azure portalı](https://portal.azure.com), depolama hesabınıza gidin ve tıklayın **anahtarları** bu bilgileri bulmak için simge.
* (İsteğe bağlı) **EndpointSuffix** -bu bölgelerde Azure Çin veya Azure idare gibi farklı sayıda uç nokta sonekleri depolama hizmetleri için kullanılır.

Paylaşılan anahtar kimlik doğrulamasını kullanarak bağlantı dizesinin bir örnek aşağıda verilmiştir:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Paylaşılan Erişim İmzaları (SAS)
Bir mobil uygulama için bir paylaşılan erişim imzası (SAS) kullanarak Azure Depolama hizmetinden bir istemci tarafından bir istek kimliğini doğrulamak için önerilen yöntem olduğu. SAS süre boyunca belirtilen bir izin kümesi ile belirli bir süre için bir istemci bir kaynağa erişim izni vermenizi sağlar.
Depolama hesabı sahibi olarak kullanmak, mobil istemciler için bir SAS oluşturmak gerekir. SAS oluşturmak için büyük olasılıkla istemcilerinize dağıtılması için bir SAS oluşturuyor ayrı bir hizmet yazmak isteyebilirsiniz. Test amacıyla kullanabilirsiniz [Microsoft Azure Depolama Gezgini](https://storageexplorer.com) veya [Azure portalı](https://portal.azure.com) bir SAS oluşturmak için. SAS'ı oluşturduğunuzda, SAS geçerli olacağı zaman aralığını ve istemciye SAS veren izinler belirtebilirsiniz.

Aşağıdaki örnek, Microsoft Azure Depolama Gezgini bir SAS oluşturmak için nasıl kullanılacağını gösterir.

1. Henüz kaydolmadıysanız [Microsoft Azure Depolama Gezgini'ni yükleme](https://storageexplorer.com)
2. Aboneliğinize bağlanın.
3. Depolama hesabınıza tıklayın ve sol altta "Eylemler" sekmesine tıklayın. "Paylaşılan erişim"bağlantı dizesi"için bir SAS oluşturmak için imzası Al"'a tıklayın.
4. Burada, okuma ve yazma hizmeti, kapsayıcı ve depolama hesabının blob hizmeti için nesne düzeyi izinleri olduğunu bir SAS bağlantı dizesi örneği verilmiştir.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Bir SAS kullanırken görebileceğiniz gibi hesap anahtarınız uygulamanızda gösterme değil. SAS ve göz atarak SAS kullanmak için en iyi uygulamalar hakkında daha fazla bilgi [paylaşılan erişim imzaları: SAS modelini anlama](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

