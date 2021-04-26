---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: eeea7b1fed0c2a1f805e21b4dec9ec3cad7fc976
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996839"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Uygulamanızı Azure depolama 'ya erişecek şekilde yapılandırma
Depolama hizmetlerine erişmek için uygulamanızın kimliğini doğrulamanın iki yolu vardır:

* Paylaşılan anahtar: yalnızca test amaçlı paylaşılan anahtar kullan
* Paylaşılan erişim Imzası (SAS): üretim uygulamaları için SAS kullanın

### <a name="shared-key"></a>Paylaşılan Anahtar
Paylaşılan anahtar kimlik doğrulaması, uygulamanızın depolama hizmetlerine erişmek için hesap adınızı ve hesap anahtarınızı kullanacağı anlamına gelir. Bu kitaplığın nasıl kullanılacağını hızlı bir şekilde gösteren amaçlar için, bu Başlarken ile paylaşılan anahtar kimlik doğrulamasını kullanacağız.

> [!WARNING] 
> **Test amacıyla yalnızca paylaşılan anahtar kimlik doğrulamasını kullanın!** Hesap adınız ve ilişkili depolama hesabına tam okuma/yazma erişimi veren hesap anahtarınız, uygulamanızı indiren her kişiye dağıtılır. Bu, **güvenilir olmayan istemciler** tarafından anahtarınıza karşı uzlaşılmış olduğunuz için iyi bir uygulamadır.
> 
> 

Paylaşılan anahtar kimlik doğrulaması kullanılırken bir [bağlantı dizesi](../articles/storage/common/storage-configure-connection-string.md)oluşturacaksınız. Bağlantı dizesi şu şekilde oluşur:  

* **Defaultendpointsprotocol** -http veya https seçebilirsiniz. Ancak HTTPS kullanmak kesinlikle önerilir.
* **Hesap adı** -depolama hesabınızın adı
* **Hesap anahtarı** - [Azure portalında](https://portal.azure.com)depolama hesabınıza gidin ve bu bilgileri bulmak için **anahtarlar** simgesine tıklayın.
* Seçim **Endpointsuffix** -bu, Azure Çin veya Azure İdaresi gibi farklı uç nokta sonekleri olan bölgelerde depolama hizmetleri için kullanılır.

Paylaşılan anahtar kimlik doğrulaması kullanan bir bağlantı dizesi örneği aşağıda verilmiştir:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Paylaşılan Erişim İmzaları (SAS)
Bir mobil uygulama için, Azure depolama hizmetine karşı bir istemciden gelen istek kimlik doğrulaması için önerilen yöntem, paylaşılan erişim Imzasını (SAS) kullanmaktır. SAS, belirli bir süre için, belirtilen bir izin kümesiyle bir kaynağa istemci erişimi vermenizi sağlar.
Depolama hesabı sahibi olarak, mobil istemcilerinizin kullanması için bir SAS oluşturmanız gerekir. SAS oluşturmak için, büyük olasılıkla istemcilerinize dağıtılacak SAS üreten ayrı bir hizmet yazmak isteyeceksiniz. Test amacıyla, bir SAS oluşturmak için [Microsoft Azure Depolama Gezgini](https://storageexplorer.com) veya [Azure portalını](https://portal.azure.com) kullanabilirsiniz. SAS oluştururken, SAS 'nin geçerli olduğu zaman aralığını ve sa 'ların istemciye verdiği izinleri belirtebilirsiniz.

Aşağıdaki örnek, Microsoft Azure Depolama Gezgini bir SAS oluşturmak için nasıl kullanılacağını gösterir.

1. Henüz yapmadıysanız, [Microsoft Azure Depolama Gezgini yüklenemedi](https://storageexplorer.com)
2. Aboneliğinize bağlanın.
3. Depolama Hesabınıza tıklayın ve sol alt kısımdaki "eylemler" sekmesine tıklayın. SAS için "bağlantı dizesi" oluşturmak için "paylaşılan erişim Imzasını al" a tıklayın.
4. Depolama hesabının blob hizmeti için hizmette, kapsayıcıda ve nesne düzeyinde okuma ve yazma izinleri veren bir SAS bağlantı dizesi örneği aşağıda verilmiştir.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Bir SAS kullanırken görebileceğiniz gibi, uygulamanızda hesap anahtarınızı kullanıma sunmuyoruz. Paylaşılan erişim Imzalarını kullanıma alarak SAS kullanmaya yönelik SAS ve en iyi uygulamalar hakkında daha fazla bilgi edinebilirsiniz [: SAS modelini anlama](../articles/storage/common/storage-sas-overview.md).