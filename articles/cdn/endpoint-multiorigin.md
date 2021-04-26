---
title: Azure CDN uç noktası çoklu kaynak (Önizleme)
description: Azure CDN uç noktası birden çok kaynağı kullanmaya başlayın.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: 6e433950c04c4494201b090063b17a10e54a4822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685780"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN uç noktası çoklu kaynak

Çoklu kaynak desteği kapalı kalma süresini ortadan kaldırır ve küresel artıklık oluşturur. 

Azure CDN uç noktası içinde birden çok kaynak seçerek, belirtilen yedeklilik, her bir kaynağın sistem durumunu yoklayana ve gerekirse yük devreterek riski yayar.

Bir veya daha fazla kaynak grubu kurun ve varsayılan bir kaynak grubu seçin. Her kaynak grubu, benzer iş yükleri alan bir veya daha fazla çıkış koleksiyonudur.

> [!NOTE]
> Şu anda bu özellik yalnızca Microsoft 'tan Azure CDN kullanılabilir. 

## <a name="create-the-origin-group"></a>Kaynak grubu oluşturma

1. [Azure portalda](https://portal.azure.com) oturum açma

2. Azure CDN profilinizi seçin ve ardından çoklu kaynak için yapılandırılacak uç noktayı seçin.

3. Uç nokta yapılandırmasındaki **Ayarlar** bölümünden **kaynak** ' ı seçin:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN uç noktası" border="true":::

4. Çoklu kaynak özelliğini etkinleştirmek için en az bir kaynak grubunuz olması gerekir. **Kaynak grubu oluştur**' u seçin:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Kaynak ayarları" border="true":::

5. **Kaynak grubu yapılandırması Ekle** ' de aşağıdaki bilgileri girin veya seçin:

   | Ayar           | Değer                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Kaynak grubu adı | Kaynak grubunuz için bir ad girin.                                   |
   | Araştırma durumu      | **Etkin**'i seçin. </br> Azure CDN, kaynak sistem durumunu belirlemede dünya genelindeki farklı noktalarda sistem durumu araştırmalarını çalıştıracak. Geçerli kaynak grubu, ek maliyet olmaması için etkin değilse etkinleştirmeyin.
   | Araştırma yolu        | Sistem durumunu belirlemede kullanılan kaynak yolu. |
   | Araştırma aralığı    | 1, 2 veya 4 dakikalık bir yoklama aralığı seçin.                        |
   | Araştırma Protokolü    | **Http** veya **https** seçin.                                         |
   | Araştırma yöntemi      | **Baş** veya **Al** seçeneğini belirleyin.                                           |
   | Varsayılan kaynak grubu | Varsayılan kaynak grubu olarak ayarlanacak kutuyu seçin.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Kaynak Grubu Ekle" border="true":::

6. **Add (Ekle)** seçeneğini belirleyin.

## <a name="add-multiple-origins"></a>Birden çok çıkış Ekle

1. Uç noktanız için kaynak ayarları ' nda **+ kaynak oluştur**' u seçin.

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Kaynak oluştur" border="true":::

2. **Kaynak Yapılandırması Ekle** ' de aşağıdaki bilgileri girin veya seçin:

   | Ayar           | Değer                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Ad        | Kaynak için bir ad girin.        |
   | Kaynak türü | **Depolama**, **bulut hizmeti**, **Web uygulaması** veya **özel başlangıç** seçin.                                   |
   | Çıkış noktası konağı        | Kaynak ana bilgisayar adını seçin veya girin.  Açılan listede, önceki ayarda belirttiğiniz türün tüm kullanılabilir kaynakları listelenir. Kaynak türü olarak **özel kaynak** ' ı seçtiyseniz, müşteri kaynak sunucunuzun etki alanını girin. |
   | Kaynak barındırma üst bilgisi    | Her istekle göndermek Azure CDN istediğiniz ana bilgisayar üst bilgisini girin veya varsayılan ' ı bırakın.                        |
   | HTTP bağlantı noktası   | HTTP bağlantı noktasını girin.                                         |
   | HTTPS bağlantı noktası     | HTTPS bağlantı noktasını girin.                                           |
   | Öncelik    | 1 ile 5 arasında bir sayı girin.       |
   | Ağırlık      | 1 ile 1000 arasında bir sayı girin.   |

    > [!NOTE]
    > Kaynak grubu içinde bir kaynak oluşturulduğunda, accorded bir öncelik ve ağırlık olmalıdır. Kaynak grubunun yalnızca bir kaynağı varsa, varsayılan öncelik ve ağırlık 1 olarak ayarlanır. Kaynak sağlıklı ise trafik en yüksek öncelik kaynaklarına yönlendirilir. Bir kaynağın sağlıksız olduğu belirlenirse, bağlantılar öncelik sırasına göre başka bir kaynağa atanır. İki kaynak aynı önceliğe sahip ise, trafik, kaynak için belirtilen ağırlığa göre dağıtılır 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Ek Kaynak Ekle" border="true":::

3. **Add (Ekle)** seçeneğini belirleyin.

4. Tüm kaynaklar için kaynak yolunu ayarlamak üzere **Kaynağı Yapılandır** ' ı seçin:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Kaynak yolunu Yapılandır" border="true":::

5. **Tamam**’ı seçin.

## <a name="configure-origins-and-origin-group-settings"></a>Kaynakları ve kaynak grubu ayarlarını yapılandırma

Birkaç çıkış ve bir kaynak grubu olduktan sonra, kaynakları farklı gruplara ekleyebilir veya kaldırabilirsiniz. Aynı grup içindeki kaynaklar benzer iş yükleri için kullanılmalıdır. Trafik, sağlıklı durum, öncelik ve ağırlık değerine bağlı olarak bu kaynaklardan dağıtılır. 

1. Azure CDN uç noktasının kaynak ayarlarında, yapılandırmak istediğiniz kaynak grubunun adını seçin:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Kaynakları ve kaynak grubu ayarlarını yapılandırma" border="true":::

2. **Güncelleştirme kaynak grubu**' nda **+ Başlangıç kaynağı**' nı seçin:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Kaynak grubunu Güncelleştir" border="true":::

4. Açılır kutuda gruba eklenecek kaynağı seçin ve **Tamam**' ı seçin.

5. Kaynağı gruba eklendiğini doğrulayıp **Kaydet**' i seçin:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Gruba eklenen ek kaynağı doğrula" border="true":::

## <a name="remove-origin-from-origin-group"></a>Kaynağı kaynak grubundan kaldır

1. Azure CDN uç noktasının kaynak ayarları ' nda, kaynak grubunun adını seçin:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Kaynağı gruptan Kaldır" border="true":::

2. Kaynak grubundan bir kaynağı kaldırmak için, kaynak yanındaki çöp kutusu simgesini seçin ve **Kaydet**' i seçin:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Kaynak grubu silme kaynağını güncelleştir" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>Kural altyapısı ile kaynak grubunu geçersiz kıl

Standart kurallar altyapısını kullanarak trafiğin farklı kaynak gruplarına nasıl dağıtıldığını özelleştirin.

Trafiği, istek URL 'sini temel alarak farklı bir gruba dağıtın.

1. CDN uç noktanıza **Ayarlar** altında **kurallar altyapısı** ' nı seçin:

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="Kural altyapısı" border="true":::

2. **+ Kural Ekle**' yi seçin.

3. **Adda** kural için bir ad girin.

4. **+ Koşul**' ı seçin ve **URL yolu**' nu seçin.

5. **Operatör** açılır penceresinde **içerir**' i seçin.

6. **Değer** alanına **/Images** yazın.

7. **+ Eylem Ekle**' yi seçin ve **kaynak grubu geçersiz kılma**' yı seçin.

8. **Kaynak grubu**' nda, açılan kutuda kaynak grubunu seçin.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="Kural altyapısı koşulları" border="true":::

URL yolu **/Images** içeriyorsa tüm gelen istekler için, istek eylem bölümündeki **(myorigingroup)** kaynak grubuna atanır. 

## <a name="next-steps"></a>Sonraki Adımlar
Bu makalede, uç nokta çoklu kaynak Azure CDN etkinleştirdiniz.

Azure CDN ve bu makalede bahsedilen diğer Azure hizmetleri hakkında daha fazla bilgi için bkz.:

* [Azure CDN](./cdn-overview.md)
* [Azure CDN ürün özelliğini karşılaştırın](./cdn-features.md)
