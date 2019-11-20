---
title: SAS ile Azure CDN kullanma | Microsoft Docs
description: Azure CDN, özel depolama kapsayıcılarına sınırlı erişim vermek için paylaşılan erişim Imzası (SAS) kullanımını destekler.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.openlocfilehash: e7a170eaf74531cf4bd8c28aafaa5873f2459d0b
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982423"
---
# <a name="using-azure-cdn-with-sas"></a>SAS ile Azure CDN kullanma

İçeriği önbelleğe almak için kullanmak üzere Azure Content Delivery Network (CDN) için bir depolama hesabı ayarlarken, varsayılan olarak, depolama kapsayıcılarınızın URL 'Lerini bilen herkes karşıya yüklediğiniz dosyalara erişebilir. Depolama hesabınızdaki dosyaları korumak için, depolama kapsayıcılarınızın erişimini herkese özel olarak ayarlayabilirsiniz. Ancak bunu yaparsanız, dosyalarınıza hiç kimse erişemez. 

Özel depolama kapsayıcılarına sınırlı erişim vermek istiyorsanız, Azure depolama hesabınızın Paylaşılan Erişim İmzası (SAS) özelliğini kullanabilirsiniz. Bir SAS, hesap anahtarınızı açığa çıkarmadan Azure Depolama kaynaklarınıza kısıtlı erişim hakları veren bir URI'dir. Depolama hesabı anahtarınızla güvenmediğiniz, ancak belirli depolama hesabı kaynaklarına erişim vermek istediğiniz istemcilere bir SAS sağlayabilirsiniz. Bu istemcilere paylaşılan erişim imzası URI 'SI dağıtarak, belirli bir süre için kaynağa erişim izni vermiş olursunuz.
 
SAS ile başlangıç ve sona erme zamanları, izinler (okuma/yazma) ve IP aralıkları gibi bir Blobun farklı erişim parametrelerini tanımlayabilirsiniz. Bu makalede, Azure CDN ile birlikte SAS 'nin nasıl kullanılacağı açıklanır. SAS hakkında daha fazla bilgi için, bunu oluşturma ve parametre seçeneklerini kullanma, bkz. [paylaşılan erişim imzaları (SAS) kullanma](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Depolama SAS ile çalışmak için Azure CDN ayarlama
Azure CDN ile SAS kullanımı için aşağıdaki üç seçenek önerilir. Tüm seçenekler zaten bir çalışma SAS oluşturmuş olduğunu varsayar (bkz. Önkoşullar). 
 
### <a name="prerequisites"></a>Önkoşullar
Başlamak için bir depolama hesabı oluşturun ve ardından varlığınız için bir SAS oluşturun. İki tür saklı erişim imzası oluşturabilirsiniz: hizmet SAS veya hesap SAS. Daha fazla bilgi için bkz. [paylaşılan erişim Imzaları türleri](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Bir SAS belirteci oluşturduktan sonra, URL 'nize ekleyerek `?sv=<SAS token>` BLOB depolama dosyanıza erişebilirsiniz. Bu URL aşağıdaki biçimdedir: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Örneğin:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Parametreleri ayarlama hakkında daha fazla bilgi için bkz. [SAS parametresi konuları](#sas-parameter-considerations) ve [paylaşılan erişim imzası parametreleri](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).

![CDN SAS ayarları](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Seçenek 1: Azure CDN 'ten blob depolamaya geçiş ile SAS kullanma

Bu seçenek en basit seçenektir ve Azure CDN kaynak sunucuya geçirilen tek bir SAS belirtecini kullanır.
 
1. Bir uç nokta seçin, **önbelleğe alma kuralları**' nı seçin, sonra **sorgu dizesi önbellek** listesinden **her benzersiz URL 'yi** seçin.

    ![CDN önbelleğe alma kuralları](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Depolama hesabınızda SAS ayarladıktan sonra, dosyaya erişmek için CDN uç noktası ve kaynak sunucu URL 'Leriyle SAS belirtecini kullanmanız gerekir. 
   
   Elde edilen CDN uç noktası URL 'SI aşağıdaki biçime sahiptir:`https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Örneğin:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Önbellek süresini, önbelleğe alma kurallarını kullanarak veya kaynak sunucuya üstbilgiler ekleyerek `Cache-Control` hassas bir şekilde ayarlayın. Azure CDN, SAS belirtecini düz bir sorgu dizesi olarak değerlendirir, en iyi uygulama olarak SAS süre sonu süresinden veya daha önce süresi dolacak bir önbelleğe alma süresi ayarlamanız gerekir. Aksi takdirde, bir dosya SAS etkin olduğundan daha uzun bir süre önbelleğe alınmışsa, SAS süre sonu süresi dolduktan sonra dosyaya Azure CDN kaynak sunucusundan erişilebilir. Bu durum oluşursa ve önbelleğe alınmış dosyanızı erişilemez hale getirmek istiyorsanız, dosyanın önbellekte temizlemek için bir temizleme işlemi gerçekleştirmeniz gerekir. Azure CDN önbellek süresini ayarlama hakkında daha fazla bilgi için bkz. önbellek [kuralları Ile denetim Azure CDN önbelleğe alma davranışı](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Seçenek 2: Yeniden yazma kuralı kullanan gizli CDN SAS belirteci
 
Bu seçenek yalnızca **Verizon profillerden Azure CDN Premium** için kullanılabilir. Bu seçenekle, blob depolamayı kaynak sunucuda güvenli hale getirebilirsiniz. Dosya için özel erişim kısıtlamalarına gerek duymuyorsanız, ancak kullanıcıların depolama kaynağına doğrudan erişmesini engellemek istiyorsanız, bu seçeneği kullanmak isteyebilirsiniz, ancak Azure CDN yük boşaltma süreleriyle Kullanıcı için bilinmeyen SAS belirteci, kaynak sunucunun belirtilen kapsayıcısındaki dosyalara erişen herkes için gereklidir. Bununla birlikte, URL yeniden yazma kuralı nedeniyle, CDN uç noktasında SAS belirteci gerekli değildir.
 
1. Bir URL yeniden yazma kuralı oluşturmak için [Rules altyapısını](cdn-rules-engine.md) kullanın. Yeni kuralların yayılması 4 saate kadar sürer.

   ![CDN Yönet düğmesi](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN kuralları altyapısı düğmesi](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Aşağıdaki örnek URL yeniden yazma kuralı bir yakalama grubu ve *sasstoragedemo*adlı bir uç nokta içeren bir normal ifade modelini kullanır:
   
   Kaynak:   
   `(container1\/.*)`
   
   Hedef:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL yeniden yazma kuralı-](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   sol![CDN URL yeniden yazma kuralı-sağ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Yeni kural etkin olduktan sonra herkes, URL 'de bir SAS belirteci kullanıp kullanmadıklarından bağımsız olarak, CDN uç noktasındaki belirtilen kapsayıcıdaki dosyalara erişebilir. Biçim şöyledir:`https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Örneğin:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Önbellek süresini, önbelleğe alma kurallarını kullanarak veya kaynak sunucuya üstbilgiler ekleyerek `Cache-Control` hassas bir şekilde ayarlayın. Azure CDN, SAS belirtecini düz bir sorgu dizesi olarak değerlendirir, en iyi uygulama olarak SAS süre sonu süresinden veya daha önce süresi dolacak bir önbelleğe alma süresi ayarlamanız gerekir. Aksi takdirde, bir dosya SAS etkin olduğundan daha uzun bir süre önbelleğe alınmışsa, SAS süre sonu süresi dolduktan sonra dosyaya Azure CDN kaynak sunucusundan erişilebilir. Bu durum oluşursa ve önbelleğe alınmış dosyanızı erişilemez hale getirmek istiyorsanız, dosyanın önbellekte temizlemek için bir temizleme işlemi gerçekleştirmeniz gerekir. Azure CDN önbellek süresini ayarlama hakkında daha fazla bilgi için bkz. önbellek [kuralları Ile denetim Azure CDN önbelleğe alma davranışı](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Seçenek 3: Bir yeniden yazma kuralıyla CDN güvenlik belirteci kimlik doğrulamasını kullanma

Azure CDN güvenlik belirteci kimlik doğrulamasını kullanmak için Verizon profile öğesinden bir **Azure CDN Premium** sahip olmanız gerekir. Bu seçenek en güvenli ve özelleştirilebilir. İstemci erişimi, güvenlik belirtecinde ayarladığınız güvenlik parametrelerini temel alır. Güvenlik belirtecini oluşturup ayarladıktan sonra, tüm CDN uç noktası URL 'Lerinde gerekli olacaktır. Bununla birlikte, URL yeniden yazma kuralı nedeniyle, CDN uç noktasında SAS belirteci gerekli değildir. SAS belirteci daha sonra geçersiz hale gelirse Azure CDN artık içeriğin kaynak sunucudan yeniden doğrulanması mümkün olmayacaktır.

1. [Azure CDN bir güvenlik belirteci oluşturun](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) ve kullanıcılarınızın dosyaya erişebileceği CDN uç noktası ve yolu için Rules altyapısını kullanarak etkinleştirin.

   Bir güvenlik belirteci uç noktası URL 'SI şu biçimdedir:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Örneğin:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Bir güvenlik belirteci kimlik doğrulaması için parametre seçenekleri bir SAS belirtecinin parametre seçeneklerinden farklıdır. Bir güvenlik belirteci oluştururken bir süre sonu süresi kullanmayı seçerseniz, bunu SAS belirtecinin sona erme süresi ile aynı değere ayarlamanız gerekir. Bunun yapılması, sona erme süresinin tahmin edilebilir olmasını sağlar. 
 
2. Kapsayıcıdaki tüm bloblara SAS belirteci erişimini etkinleştirmek için bir URL yeniden yazma kuralı oluşturmak üzere [Rules altyapısını](cdn-rules-engine.md) kullanın. Yeni kuralların yayılması 4 saate kadar sürer.

   Aşağıdaki örnek URL yeniden yazma kuralı bir yakalama grubu ve *sasstoragedemo*adlı bir uç nokta içeren bir normal ifade modelini kullanır:
   
   Kaynak:   
   `(container1\/.*)`
   
   Hedef:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL yeniden yazma kuralı-](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   sol![CDN URL yeniden yazma kuralı-sağ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. SAS 'Yi yenilemezseniz, URL yeniden yazma kuralını yeni SAS belirteciyle güncelleştirdiğinizden emin olun. 

## <a name="sas-parameter-considerations"></a>SAS parametresi konuları

SAS parametreleri Azure CDN görünür olmadığından, Azure CDN kendisine göre teslim davranışını değiştiremez. Tanımlanan parametre kısıtlamaları, istemciden Azure CDN istekleri için değil, yalnızca kaynak sunucuya Azure CDN istekleri uygular. Bu ayrım, SAS parametrelerini ayarladığınızda göz önünde bulundurmanız önemlidir. Bu gelişmiş yetenekler gerekliyse ve [seçenek 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)' ü kullanıyorsanız, Azure CDN güvenlik belirtecinde uygun kısıtlamaları ayarlayın.

| SAS parametre adı | Açıklama |
| --- | --- |
| Start | Azure CDN blob dosyasına erişmeye başlayabileceğiniz zaman. Saat farkı (farklı bileşenler için saat sinyali farklı zamanlara ulaştığında) nedeniyle, varlığın hemen kullanılabilir olmasını istiyorsanız 15 dakika daha erken bir süre seçin. |
| End | Azure CDN blob dosyasına artık erişememesi gereken süre. Daha önce Azure CDN önbelleğe alınmış dosyalara hala erişilebilir. Dosya sona erme süresini denetlemek için Azure CDN güvenlik belirtecinde uygun süre sonu saatini ayarlayın veya varlığı temizleyin. |
| İzin verilen IP adresleri | İsteğe bağlı. **Verizon ' den Azure CDN**kullanıyorsanız, bu parametreyi [VERIZON Edge Server IP aralıklarından Azure CDN](/azure/cdn/cdn-pop-list-api)tanımlanan aralıklar olarak ayarlayabilirsiniz. **Akamai ' den Azure CDN**kullanıyorsanız IP adresleri STATIK olmadığından IP aralıkları parametresini ayarlayamazsınız.|
| İzin verilen protokoller | Hesap SAS ile yapılan bir istek için izin verilen protokol (ler). HTTPS ayarı önerilir.|

## <a name="next-steps"></a>Sonraki adımlar

SAS hakkında daha fazla bilgi için aşağıdaki makalelere bakın:
- [Paylaşılan erişim imzaları (SAS) kullanma](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Paylaşılan erişim Imzaları, Bölüm 2: BLOB depolama ile SAS oluşturma ve kullanma](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Belirteç kimlik doğrulamasını ayarlama hakkında daha fazla bilgi için bkz. [Azure Content Delivery Network varlıklarını belirteç kimlik doğrulamasıyla koruma](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
