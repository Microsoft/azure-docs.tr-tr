---
title: Azure depolama için bağlantı dizesi yapılandırma
description: Azure depolama hesabı için bağlantı dizesi yapılandırma. Bir bağlantı dizesi, paylaşılan anahtar yetkilendirmesi kullanarak çalışma zamanında uygulamanızdan bir depolama hesabına erişim yetkisi vermek için gereken bilgileri içerir.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d1106865b3a2ea3164090896c5b90ab08f996f3d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640502"
---
# <a name="configure-azure-storage-connection-strings"></a>Azure Storage bağlantı dizelerini yapılandırma

Bir bağlantı dizesi, uygulamanızın paylaşılan anahtar yetkilendirmesi kullanarak çalışma zamanında bir Azure depolama hesabındaki verilere erişmesi için gereken yetkilendirme bilgilerini içerir. Bağlantı dizelerini şu şekilde yapılandırabilirsiniz:

* Azure Storage öykünücüsüne bağlanın.
* Azure 'da bir depolama hesabına erişin.
* Azure 'daki belirtilen kaynaklara paylaşılan erişim imzası (SAS) aracılığıyla erişin.

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Bağlantı dizesini görüntüleme ve kopyalama

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Bir bağlantı dizesi depolayın

Azure depolama 'ya yapılan istekleri yetkilendirmek için uygulamanızın çalışma zamanında bağlantı dizesine erişmesi gerekir. Bağlantı dizenizi depolamak için çeşitli seçenekleriniz vardır:

* Bağlantı dizenizi bir ortam değişkeninde saklayabilirsiniz.
* Masaüstünde veya cihazda çalışan bir uygulama, bağlantı dizesini **app. config** veya **Web. config** dosyasında saklayabilir. Bağlantı dizesini bu dosyalardaki **appSettings** bölümüne ekleyin.
* Azure bulut hizmetinde çalışan bir uygulama, bağlantı dizesini [Azure hizmet yapılandırma şeması (. cscfg) dosyasında](https://msdn.microsoft.com/library/ee758710.aspx)saklayabilir. Bağlantı dizesini hizmet yapılandırma dosyasının **ConfigurationSettings** bölümüne ekleyin.

Bağlantı dizenizi bir yapılandırma dosyasında depolamak, depolama öykünücüsü ile buluttaki bir Azure depolama hesabı arasında geçiş yapmak için bağlantı dizesini güncelleştirmeyi kolaylaştırır. Yalnızca hedef ortamınıza işaret etmek için bağlantı dizesini düzenlemeniz gerekir.

Uygulamanızın çalıştığı yere bakılmaksızın, çalışma zamanında Bağlantı dizenizi erişmek için [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) kullanabilirsiniz.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Depolama öykünücüsü için bir bağlantı dizesi yapılandırma

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Depolama öykünücüsü hakkında daha fazla bilgi için bkz. [geliştirme ve test Için Azure depolama öykünücüsünü kullanma](storage-use-emulator.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Azure depolama hesabı için bağlantı dizesi yapılandırma

Azure depolama hesabınız için bir bağlantı dizesi oluşturmak için aşağıdaki biçimi kullanın. HTTPS (önerilen) veya http üzerinden depolama hesabına bağlanmak isteyip istemediğinizi belirtin, depolama hesabınızın adıyla değiştirin `myAccountName` ve öğesini hesap erişim anahtarınızla değiştirin: `myAccountKey`

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Örneğin, bağlantı dizeniz şuna benzer görünebilir:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Azure Storage bir bağlantı dizesinde hem HTTP hem de HTTPS destekler, ancak *https kesinlikle önerilir*.

> [!TIP]
> Depolama hesabınızın bağlantı dizelerini [Azure Portal](https://portal.azure.com)bulabilirsiniz. Birincil ve ikincil erişim anahtarlarına yönelik bağlantı dizelerini görmek için, depolama hesabınızın menü dikey penceresindeki **Ayarlar** > **erişim anahtarlarına** gidin.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Paylaşılan erişim imzasını kullanarak bağlantı dizesi oluşturma

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Açık depolama uç noktası için bağlantı dizesi oluşturma

Varsayılan uç noktaları kullanmak yerine, bağlantı dizeniz üzerinde açık hizmet uç noktaları belirtebilirsiniz. Açık bir uç nokta belirten bir bağlantı dizesi oluşturmak için, protokol belirtimi (HTTPS (önerilen) veya HTTP) dahil olmak üzere her bir hizmetin tüm hizmet uç noktasını aşağıdaki biçimde belirtin:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

BLOB depolama uç noktanızı [özel bir etki alanına](../blobs/storage-custom-domain-name.md)eşleştirdikten sonra açık bir uç nokta belirtmek isteyebileceğiniz bir senaryo. Bu durumda, bağlantı dizinizdeki BLOB depolama için özel uç noktanızı belirtebilirsiniz. İsteğe bağlı olarak, uygulamanız tarafından kullanılıyorsa diğer hizmetler için varsayılan uç noktaları belirtebilirsiniz.

Blob hizmeti için açık bir uç nokta belirten bağlantı dizesinin bir örneği aşağıda verilmiştir:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Bu örnek, blob hizmeti için özel bir etki alanı da dahil olmak üzere tüm hizmetler için açık uç noktaları belirtir:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Bir bağlantı dizesindeki uç nokta değerleri, depolama hizmetlerine istek URI 'Leri oluşturmak için kullanılır ve kodunuza döndürülen URI 'lerin formunu dikte ediyor.

Bir depolama uç noktasını özel bir etki alanına eşleştirdiyseniz ve bu uç noktayı bir bağlantı dizesinden atlarsanız, bu bağlantı dizesini kodınızdan bu hizmette bulunan verilere erişmek için kullanamazsınız.

> [!IMPORTANT]
> Bağlantı dizelerinizdeki hizmet uç noktası değerlerinin (önerilir) veya `https://` `http://`dahil doğru biçimlendirilmiş URI 'ler olması gerekir. Azure Storage özel etki alanları için https 'yi desteklemediği için, özel bir etki alanına `http://` işaret eden herhangi bir uç nokta URI 'si belirtmeniz gerekir.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Uç nokta sonekine sahip bir bağlantı dizesi oluşturma

Azure Çin 21Vianet veya Azure Kamu gibi farklı uç nokta son eklerine sahip bölgelerde veya örneklerde bulunan bir depolama hizmeti için bağlantı dizesi oluşturmak için aşağıdaki bağlantı dizesi biçimini kullanın. HTTPS (önerilen) veya http üzerinden depolama hesabına bağlanmak isteyip istemediğinizi belirtin, depolama hesabınızın adıyla değiştirin `myAccountName` , hesap erişim anahtarınızla değiştirin `myAccountKey` ve URI sonekiyle değiştirin `mySuffix` :

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Azure Çin 21Vianet içindeki depolama hizmetleri için örnek bir bağlantı dizesi aşağıda verilmiştir:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Bağlantı dizesini ayrıştırma

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Geliştirme ve test için Azure depolama öykünücüsünü kullanma](storage-use-emulator.md)
* [Azure depolama gezginleri](storage-explorers.md)
* [Paylaşılan erişim imzaları (SAS) kullanma](storage-sas-overview.md)
