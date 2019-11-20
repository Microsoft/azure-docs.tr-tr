---
title: Azure CDN'de dosyaları sıkıştırarak performansı geliştirme | Microsoft Docs
description: Dosya aktarım hızını artırmak ve sayfa yükleme performansı, Azure CDN'de dosyaları sıkıştırarak artırmak öğrenin.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: magattus
ms.openlocfilehash: 9f185f58e1d33a3985777cb22bc7578f9f2c4541
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593794"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Azure CDN'de dosyaları sıkıştırarak performansı geliştirme
Dosya sıkıştırma dosya aktarım hızını artırmak ve sunucudan gönderilmeden önce bir dosyanın boyutunu azaltarak sayfa yükleme performansı artırmak için basit ve etkili bir yöntemdir. Dosya sıkıştırma, bant genişliği maliyetlerini azaltmak ve kullanıcılarınız için daha hızlı bir deneyim sağlayın.

Dosya sıkıştırma etkinleştirmek için iki yolu vardır:

- Kaynak sunucunuzda sıkıştırmayı etkinleştirin. Bu durumda, Azure CDN, sıkıştırılmış dosyalar geçirir ve bunları bunları isteyen istemcilere teslim eder.
- CDN POP sunucularında doğrudan sıkıştırmayı etkinleştir (*sıkıştırma hareket halindeyken*). Bu durumda, CDN dosyaları sıkıştırır ve bunlar kaynak sunucu tarafından sıkıştırılmamış bile bunları son kullanıcılara sunar.

> [!IMPORTANT]
> Azure CDN yapılandırma değişiklikleri ağ üzerinden yayılması biraz zaman alabilir: 
> - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
> - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
> - **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 10 dakika içinde tamamlanır. 
> 
> Yedekleme sıkıştırma CDN uç noktanız için ilk kez ayarlama, 1-2 saat sıkıştırma ayarları Pop'lere yayılmadan olmak için sorun giderme önce bekleyen göz önünde bulundurun.

## <a name="enabling-compression"></a>Sıkıştırmayı etkinleştirme
Standart ve premium CDN katmanları için sıkıştırma işlevsellik sağlasa da, kullanıcı arabirimi farklıdır. Standart ve premium CDN katmanları arasındaki farklar hakkında daha fazla bilgi için bkz. [Azure CDN'ye genel bakış](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standart CDN profili 
> [!NOTE]
> Bu bölümde uygulandığı **Azure CDN standart Microsoft gelen**, **verizon'dan Azure CDN standart**, ve **akamai'den Azure CDN standart** profilleri.
> 
> 

1. CDN profili sayfasından, yönetmek istediğiniz CDN uç noktası seçin.

    ![CDN profili uç noktaları](./media/cdn-file-compression/cdn-endpoints.png)

    CDN uç noktası sayfası açılır.
2. Seçin **sıkıştırma**.

    ![CDN sıkıştırma seçimi](./media/cdn-file-compression/cdn-compress-select-std.png)

    Sıkıştırma sayfası açılır.
3. Seçin **üzerinde** sıkıştırmasını etkinleştirmek için.

    ![CDN dosya sıkıştırma seçeneklerini](./media/cdn-file-compression/cdn-compress-standard.png)
4. Varsayılan MIME türleri kullanın veya ekleyerek veya kaldırarak MIME türleri listesini değiştirebilirsiniz.

   > [!TIP]
   > Mümkün olsa da, sıkıştırma için sıkıştırma biçimlerinin uygulamak için önerilmez. Örneğin, ZIP, MP3, MP4 veya JPG.
   > 

   > [!NOTE]
   > Varsayılan MIME türleri listesini değiştirme şu anda Azure CDN standart Microsoft gelen desteklenmez.
   > 

5. Değişikliklerinizi yaptıktan sonra seçin **Kaydet**.

### <a name="premium-cdn-profiles"></a>Premium CDN profilleri
> [!NOTE]
> Bu bölüm yalnızca geçerlidir **verizon'dan Azure CDN Premium** profilleri.
> 

1. CDN profili sayfasından seçin **Yönet**.

    ![CDN'yi yönetmek seçin](./media/cdn-file-compression/cdn-manage-btn.png)

    CDN yönetim portalına açılır.
2. Üzerine **HTTP büyük** sekmesine ve ardından üzerine **önbellek ayarları** açılır öğesi. Seçin **sıkıştırma**.

    ![CDN sıkıştırma seçimi](./media/cdn-file-compression/cdn-compress-select.png)

    Sıkıştırma seçeneklerini görüntülenir.

    ![CDN dosya sıkıştırma seçeneklerini](./media/cdn-file-compression/cdn-compress-files.png)
3. Seçerek sıkıştırmayı etkinleştir **sıkıştırmanın etkin**. Girin, istediğiniz bir virgülle ayrılmış bir liste (boşluksuz) sıkıştırmak için MIME türlerini **dosya türleri** kutusu.

   > [!TIP]
   > Mümkün olsa da, sıkıştırma için sıkıştırma biçimlerinin uygulamak için önerilmez. Örneğin, ZIP, MP3, MP4 veya JPG.
   > 

4. Değişikliklerinizi yaptıktan sonra seçin **güncelleştirme**.

## <a name="compression-rules"></a>Sıkıştırma kuralları

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN standart'ı Microsoft profilleri

İçin **Azure CDN standart Microsoft gelen** profilleri, yalnızca uygun dosyaları sıkıştırılır. Sıkıştırma için uygun olması için bir dosya olmalıdır:
- Yapılmış bir MIME türünde [sıkıştırma için yapılandırılmış](#enabling-compression).
- 1 KB'den büyük olmaması
- 8 MB'tan küçük olmalıdır

Bu profiller, aşağıdaki sıkıştırma kodlamaları destekler:
- gzip (GNU zip)
- brotli 

İsteği birden fazla sıkıştırma türünü destekliyorsa, brotli sıkıştırma önceliklidir.

Bir varlık için bir istek önbellek isabetsizliği gzip sıkıştırma ve istek sonuçları belirttiğinde, Azure CDN POP sunucusunda doğrudan varlığın gzip sıkıştırma gerçekleştirir. Daha sonra sıkıştırılmış dosyayı önbellekten sunulur.

### <a name="azure-cdn-from-verizon-profiles"></a>Profillerinden Verizon'dan Azure CDN

İçin **verizon'dan Azure CDN standart** ve **verizon'dan Azure CDN Premium** profilleri, yalnızca uygun dosyaları sıkıştırılır. Sıkıştırma için uygun olması için bir dosya olmalıdır:
- 128 bayttan büyük olmalıdır
- 3 MB'tan küçük olmalıdır

Bu profiller, aşağıdaki sıkıştırma kodlamaları destekler:
- gzip (GNU zip)
- SÖNDÜR
- bzip2
- brotli 

İsteği birden fazla sıkıştırma türünü destekliyorsa, bu sıkıştırma türleri brotli sıkıştırmasının önceliklidir.

Ne zaman bir varlık için bir istek brotli sıkıştırma belirtir (HTTP üst bilgisi `Accept-Encoding: br`) ve istek sonuçları önbellek isabetsizliği, Azure CDN POP sunucusunda doğrudan varlığın brotli sıkıştırma gerçekleştirir. Daha sonra sıkıştırılmış dosyayı önbellekten sunulur.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN standart Akamai profillerinden

İçin **akamai'den Azure CDN standart** profilleri, tüm dosyaları sıkıştırma için uygun. Ancak, bir dosya olan bir MIME türü olmalıdır [sıkıştırma için yapılandırılmış](#enabling-compression).

Bu profiller, gzip sıkıştırma yalnızca kodlamayı destekler. Bir profili bitiş noktasına gzip ile kodlanmış bir dosyayı istediğinde, istemci istek bağımsız olarak kaynaktan her zaman istenmektedir. 

## <a name="compression-behavior-tables"></a>Sıkıştırma davranışı tabloları
Aşağıdaki tablo, her senaryo için Azure CDN sıkıştırma davranışını açıklar:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Dosya sıkıştırma için geçersiz ya da sıkıştırma devre dışı
| İstemci tarafından istenen biçimi (aracılığıyla Accept-Encoding üstbilgisi) | Önbelleğe alınmış dosya biçimi | CDN yanıtı istemciye | Notları&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Sıkıştırılmış |Sıkıştırılmış |Sıkıştırılmış | |
| Sıkıştırılmış |Sıkıştırılmamış |Sıkıştırılmamış | |
| Sıkıştırılmış |Önbelleğe alınmış |Sıkıştırılmış veya sıkıştırılmamış |Kaynak yanıtı CDN bir sıkıştırma gerçekleştirmiş olup olmadığını belirler. |
| Sıkıştırılmamış |Sıkıştırılmış |Sıkıştırılmamış | |
| Sıkıştırılmamış |Sıkıştırılmamış |Sıkıştırılmamış | |
| Sıkıştırılmamış |Önbelleğe alınmış |Sıkıştırılmamış | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Dosya sıkıştırma için uygundur ve sıkıştırma etkin
| İstemci tarafından istenen biçimi (aracılığıyla Accept-Encoding üstbilgisi) | Önbelleğe alınmış dosya biçimi | CDN yanıtı istemciye | Notlar |
| --- | --- | --- | --- |
| Sıkıştırılmış |Sıkıştırılmış |Sıkıştırılmış |Desteklenen biçimler arasında CDN dönüştürür. |
| Sıkıştırılmış |Sıkıştırılmamış |Sıkıştırılmış |CDN bir sıkıştırma gerçekleştirir. |
| Sıkıştırılmış |Önbelleğe alınmış |Sıkıştırılmış |CDN kaynağını sıkıştırılmamış dosyasını döndürür bir sıkıştırma gerçekleştirir. <br/>**Verizon'dan Azure CDN** sıkıştırılmamış dosyayı ilk isteğe geçirir ve sonra sıkıştırır ve sonraki istekler için dosyayı önbelleğe alır. <br/>İle dosyaları `Cache-Control: no-cache` üstbilgi asla sıkıştırılmaz. |
| Sıkıştırılmamış |Sıkıştırılmış |Sıkıştırılmamış |CDN bir sıkıştırma gerçekleştirir. |
| Sıkıştırılmamış |Sıkıştırılmamış |Sıkıştırılmamış | |
| Sıkıştırılmamış |Önbelleğe alınmış |Sıkıştırılmamış | |

## <a name="media-services-cdn-compression"></a>CDN sıkıştırma medya Hizmetleri
Medya Hizmetleri CDN akış için etkinleştirilmiş uç noktaları için varsayılan olarak aşağıdaki MIME türleri için sıkıştırmanın etkin: 
- Uygulama/vnd.ms-sstr + xml şeklindedir 
- Uygulama/dash + xml şeklindedir
- Application/vnd.apple.mpegurl
- Uygulama/f4m + xml şeklindedir 

## <a name="see-also"></a>Ayrıca bkz.
* [CDN dosya sıkıştırma sorunlarını giderme](cdn-troubleshoot-compression.md)    

