---
title: Azure CDN dosyaları sıkıştırarak performansı geliştirme | Microsoft Docs
description: Dosya aktarım hızını geliştirmeyi ve Azure CDN dosyalarınızı sıkıştırarak sayfa yükleme performansını artırmayı öğrenin.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 11a2dbfc9c6da60e4dd96f65d2a20165a3663e8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601551"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Azure CDN’de dosyaları sıkıştırarak performansı geliştirme
Dosya sıkıştırma, dosya aktarım hızını artırmak için basit ve etkili bir yöntemdir ve bir dosyanın boyutunu sunucudan gönderilmeden önce azaltarak sayfa yükleme performansını artırır. Dosya sıkıştırma, bant genişliği maliyetlerini azaltabilir ve kullanıcılarınız için daha fazla yanıt veren bir deneyim sağlayabilir.

Dosya sıkıştırmasını etkinleştirmenin iki yolu vardır:

- Kaynak sunucunuzda sıkıştırmayı etkinleştirin. Bu durumda, Azure CDN sıkıştırılmış dosyalar üzerinde geçirilir ve bunları isteyen istemcilere gönderir.
- CDN POP sunucularında doğrudan sıkıştırmayı etkinleştirin (*anında sıkıştırma*). Bu durumda, CDN dosyaları sıkıştırır ve kaynak sunucu tarafından sıkıştırılmasa bile bunları son kullanıcılara sunar.

> [!IMPORTANT]
> Azure CDN yapılandırma değişikliklerinin ağ üzerinden yayılması biraz zaman alabilir: 
> - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
> - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
> - **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 10 dakika içinde tamamlanır. 
> 
> CDN uç noktanız için ilk kez sıkıştırma ayarlıyorsanız, sıkıştırma ayarlarının pop 'Lara yayıldığından emin olmak için sorun gidermeye başlamadan önce 1-2 saat beklemeyi göz önünde bulundurun.

## <a name="enabling-compression"></a>Sıkıştırmayı etkinleştirme
Standart ve Premium CDN katmanları aynı sıkıştırma işlevselliğine sahiptir, ancak kullanıcı arabirimi farklıdır. Standart ve Premium CDN katmanları arasındaki farklar hakkında daha fazla bilgi için bkz. [Azure CDN genel bakış](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standart CDN profilleri 
> [!NOTE]
> Bu bölüm, **Microsoft 'tan Azure CDN Standart**, **Verizon 'ten standart Azure CDN** ve **Akamai profillerden Azure CDN Standart** için geçerlidir.
> 
> 

1. CDN profili sayfasında, yönetmek istediğiniz CDN uç noktasını seçin.

    ![CDN profili uç noktaları](./media/cdn-file-compression/cdn-endpoints.png)

    CDN uç noktası sayfası açılır.
2. **Sıkıştırma**' yı seçin.

    ![Ekran görüntüsünde, Portal menüsünden sıkıştırmayla seçili bir uç nokta gösterilir.](./media/cdn-file-compression/cdn-compress-select-std.png)

    Sıkıştırma sayfası açılır.
3. Sıkıştırmayı açmak için **Açık '** ı seçin.

    ![Ekran görüntüsü sıkıştırmayı açmayı gösterir.](./media/cdn-file-compression/cdn-compress-standard.png)
4. Varsayılan MIME türlerini kullanın veya MIME türlerini ekleyerek veya kaldırarak listeyi değiştirin.

   > [!TIP]
   > Mümkün olsa da, sıkıştırılan biçimlere sıkıştırma uygulanması önerilmez. Örneğin, ZIP, MP3, MP4 veya JPG.
   > 

5. Değişikliklerinizi yaptıktan sonra **Kaydet**' i seçin.

### <a name="premium-cdn-profiles"></a>Premium CDN profilleri
> [!NOTE]
> Bu bölüm yalnızca **Verizon profillerden Azure CDN Premium** için geçerlidir.
> 

1. CDN profili sayfasında **Yönet**' i seçin.

    ![CDN yönetme seçimi](./media/cdn-file-compression/cdn-manage-btn.png)

    CDN yönetim portalı açılır.
2. **Http büyük** sekmesinin üzerine gelin ve ardından **önbellek ayarları** açılır seçeneğinin üzerine gelin. **Sıkıştırma**' yı seçin.

    ![CDN sıkıştırma seçimi](./media/cdn-file-compression/cdn-compress-select.png)

    Sıkıştırma seçenekleri görüntülenir.

    ![CDN dosya sıkıştırma seçenekleri](./media/cdn-file-compression/cdn-compress-files.png)
3. **Sıkıştırma etkin**' i seçerek sıkıştırmayı etkinleştirin. **Dosya türleri** kutusuna virgülle ayrılmış bir liste (boşluk yok) olarak SıKıŞTıRMAK istediğiniz MIME türlerini girin.

   > [!TIP]
   > Mümkün olsa da, sıkıştırılan biçimlere sıkıştırma uygulanması önerilmez. Örneğin, ZIP, MP3, MP4 veya JPG.
   > 

4. Değişikliklerinizi yaptıktan sonra **Güncelleştir**' i seçin.

## <a name="compression-rules"></a>Sıkıştırma kuralları

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Microsoft profillerden Azure CDN Standart

**Microsoft profillerinin Azure CDN Standart** için, yalnızca uygun dosyalar sıkıştırılır. Sıkıştırmaya uygun olması için bir dosya şunları içermelidir:
- [Sıkıştırma için yapılandırılmış](#enabling-compression)bir MIME türü olmalıdır.
- 1 KB 'den büyük olmalıdır
- 8 MB 'tan küçük olmalıdır

Bu profiller aşağıdaki sıkıştırma kodlamalarını destekler:
- gzip (GNU zip)
- brotli 

İstek birden fazla sıkıştırma türünü destekliyorsa, brotli sıkıştırması önceliklidir.

Bir varlık isteği gzip sıkıştırmasını belirttiğinde ve istek bir önbellek isabetsizliği ile sonuçlanırsa, Azure CDN doğrudan POP sunucusunda varlığın gzip sıkıştırmasını gerçekleştirir. Daha sonra, sıkıştırılan dosya önbellekten sunulur.

Kaynak, CDN POP 'a sıkıştırılmış veri göndermek için öbekli aktarım kodlaması (CTE) kullanıyorsa, 8MB 'den büyük yanıt boyutları desteklenmez. 

### <a name="azure-cdn-from-verizon-profiles"></a>Verizon profillerden Azure CDN

**Verizon 'den Azure CDN Standart** ve **Verizon profillerden Azure CDN Premium** için, yalnızca uygun dosyalar sıkıştırılır. Sıkıştırmaya uygun olması için bir dosya şunları içermelidir:
- 128 bayttan büyük olmalıdır
- 3 MB 'tan küçük olmalıdır

Bu profiller aşağıdaki sıkıştırma kodlamalarını destekler:
- gzip (GNU zip)
- Söndür
- bzip2
- brotli 

İstek birden fazla sıkıştırma türünü destekliyorsa, bu sıkıştırma türleri brotli sıkıştırmadan önceliklidir.

Bir varlık için bir istek brotli Compression (HTTP üstbilgisi `Accept-Encoding: br` ) belirttiğinde ve istek bir önbellek isabetsizliği ile sonuçlanırsa, Azure CDN varlığın doğrudan pop sunucusunda brotli sıkıştırmasını gerçekleştirir. Daha sonra, sıkıştırılan dosya önbellekten sunulur.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Akamai profillerden Azure CDN Standart

**Akamai profillerden Azure CDN Standart** için tüm dosyalar sıkıştırmaya uygundur. Ancak, bir dosyanın [sıkıştırma için yapılandırılmış](#enabling-compression)bir MIME türünde olması gerekir.

Bu profiller yalnızca gzip sıkıştırma kodlamasını destekler. Bir profil uç noktası gzip kodlu bir dosya istediğinde, istemci isteğinden bağımsız olarak her zaman kaynaktan istenir. 

## <a name="compression-behavior-tables"></a>Sıkıştırma davranışı tabloları
Aşağıdaki tablolar her senaryo için Azure CDN sıkıştırma davranışını anlatmaktadır:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Sıkıştırma devre dışı bırakıldı veya dosya sıkıştırma için uygun değil
| İstemci tarafından istenen biçim (Accept-Encoding üst bilgi aracılığıyla) | Önbelleğe alınmış dosya biçimi | İstemciye yönelik CDN yanıtı | &nbsp; &nbsp; &nbsp; &nbsp; Notlar &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Sıkıştırılmış |Sıkıştırılmış |Sıkıştırılmış | |
| Sıkıştırılmış |Sıkıştırılmamış |Sıkıştırılmamış | |
| Sıkıştırılmış |Önbelleğe alınmamış |Sıkıştırılmış veya sıkıştırılmamış |Kaynak yanıtı, CDN 'nin bir sıkıştırma yapıp gerçekleştirmeyeceğini belirler. |
| Sıkıştırılmamış |Sıkıştırılmış |Sıkıştırılmamış | |
| Sıkıştırılmamış |Sıkıştırılmamış |Sıkıştırılmamış | |
| Sıkıştırılmamış |Önbelleğe alınmamış |Sıkıştırılmamış | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Sıkıştırma etkin ve dosya sıkıştırma için uygun
| İstemci tarafından istenen biçim (Accept-Encoding üst bilgi aracılığıyla) | Önbelleğe alınmış dosya biçimi | İstemciye CDN yanıtı | Notlar |
| --- | --- | --- | --- |
| Sıkıştırılmış |Sıkıştırılmış |Sıkıştırılmış |Desteklenen biçimler arasında CDN dönüştürme kodları. <br/>**Microsoft 'tan Azure CDN** , biçimler arasında dönüştürme ve bunun yerine verileri kaynaktan getirir, biçim için ayrı ayrı sıkıştırır ve önbelleğe alır. |
| Sıkıştırılmış |Sıkıştırılmamış |Sıkıştırılmış |CDN bir sıkıştırma gerçekleştirir. |
| Sıkıştırılmış |Önbelleğe alınmamış |Sıkıştırılmış |Kaynak sıkıştırılmamış bir dosya döndürürse CDN bir sıkıştırma gerçekleştirir. <br/>**Verizon öğesinden Azure CDN** , ilk istekteki sıkıştırılmamış dosyayı geçirir ve ardından sonraki istekler için dosyayı sıkıştırır ve önbelleğe alır. <br/>`Cache-Control: no-cache`Üst bilgisine sahip dosyalar hiçbir şekilde sıkıştırılmaz. |
| Sıkıştırılmamış |Sıkıştırılmış |Sıkıştırılmamış |CDN bir açma işlemi gerçekleştirir. <br/>**Microsoft Azure CDN** , sıkıştırmayı desteklemez ve bunun yerine sıkıştırılmamış istemciler için kaynak ve önbelleklerden veri getirir. |
| Sıkıştırılmamış |Sıkıştırılmamış |Sıkıştırılmamış | |
| Sıkıştırılmamış |Önbelleğe alınmamış |Sıkıştırılmamış | |

## <a name="media-services-cdn-compression"></a>Media Services CDN sıkıştırması
Media Services CDN akışı için etkin olan uç noktalar için, aşağıdaki MIME türleri için sıkıştırma varsayılan olarak etkindir: 
- application/vnd. MS-SSTR + XML 
- Uygulama/tire + XML
- Uygulama/vnd.apple.mpegURL 'si
- Application/f4m + XML 

## <a name="see-also"></a>Ayrıca bkz.
* [CDN dosya sıkıştırma sorunlarını giderme](cdn-troubleshoot-compression.md)    

