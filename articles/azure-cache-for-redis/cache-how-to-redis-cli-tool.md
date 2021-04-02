---
title: Redis için Azure Cache ile Redis-CLI kullanma
description: İstemci olarak Redis için Azure önbelleğiyle etkileşim kurmak üzere bir komut satırı aracı olarak *redis-cli.exe* kullanmayı öğrenin
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: e4f5fc7290b45f65067f6711f70476e13a010223
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183395"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Redis için Azure Cache ile Redis komut satırı aracını kullanın

*redis-cli.exe* , Red, istemci olarak bir Azure önbelleğiyle etkileşim kurmaya yönelik popüler bir komut satırı aracıdır. Bu araç redde için Azure Cache ile birlikte kullanılabilir.

Araç, [Windows Için redis komut satırı araçları](https://github.com/MSOpenTech/redis/releases/)'Nı indirerek Windows platformları için kullanılabilir. 

Komut satırı aracını başka bir platformda çalıştırmak istiyorsanız, Redis için Azure önbelleği ' ni indirin [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Önbellek erişim bilgilerini topla

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Önbelleğe erişmek için gereken bilgileri üç yöntem kullanarak toplayabilirsiniz:

1. [Az redis List-Keys](/cli/azure/redis#az-redis-list-keys) kullanarak Azure CLI
2. [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey) kullanarak Azure PowerShell
3. Azure portalı kullanarak.

Bu bölümde, Azure portal anahtarları alırsınız.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>redis-cli.exe için erişimi etkinleştir

Redis için Azure Cache ile yalnızca TLS bağlantı noktası (6380) varsayılan olarak etkindir. `redis-cli.exe`Komut satırı aracı TLS 'yi desteklemez. Bunu kullanmak için iki yapılandırma seçeneğiniz vardır:

1. [TLS olmayan bağlantı noktasını etkinleştirme (6379)](cache-configure.md#access-ports)  -  Bu yapılandırmada bu **yapılandırma önerilmez** çünkü erişim tuşları, şifresiz metın olarak TCP aracılığıyla gönderilir. Bu değişiklik önbelleğiniz için erişimi tehlikeye atabilir. Yalnızca bir test önbelleğine erişirken bu yapılandırmayı düşünebileceğiniz tek senaryo.

2. [Stunnel](https://www.stunnel.org/downloads.html)indirin ve yükleyin.

    Sunucuyu başlatmak için **Stunnel GUI başlangıcını** çalıştırın.

    Stunnel sunucusu için görev çubuğu simgesine sağ tıklayın ve **günlük penceresini göster**' e tıklayın.

    Stunnel günlük penceresi menüsünde **yapılandırma**  >  **düzenleme yapılandırması** ' na tıklayarak geçerli yapılandırma dosyasını açın.

    **Hizmet tanımları** bölümünün altındaki *redis-cli.exe* için aşağıdaki girişi ekleyin. Yerine gerçek önbelleğiniz adını ekleyin `yourcachename` . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Yapılandırma dosyasını kaydedin ve kapatın. 
  
    Stunnel günlüğü penceresi menüsünde, **yapılandırma**  >  **yeniden yükleme yapılandırması**' na tıklayın.


## <a name="connect-using-the-redis-command-line-tool"></a>Redis komut satırı aracını kullanarak bağlanın.

Stunnel kullanırken *redis-cli.exe* çalıştırın ve yalnızca *bağlantı noktasını* geçirin ve önbelleğe bağlanmak için *erişim anahtarı* (birincil veya ikincil) kullanın.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Önbellek bağlantınızın başarılı olduğunu gösteren ekran görüntüsü.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

**Güvenli** olmayan TLS bağlantı noktası ile bir sınama önbelleği kullanıyorsanız, `redis-cli.exe` Test önbelleğine bağlanmak için *ana bilgisayar adınızı*, *bağlantı noktasını* ve *erişim anahtarınızı* (birincil veya ikincil) çalıştırın ve geçirin.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![redin-CLI ile Stunnel](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Sonraki adımlar

Komutları vermek için [Redis konsolunu](cache-configure.md#redis-console) kullanma hakkında daha fazla bilgi edinin.
