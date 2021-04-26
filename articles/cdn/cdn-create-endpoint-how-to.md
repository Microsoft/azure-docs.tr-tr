---
title: Azure CDN uç noktası oluşturma | Microsoft Docs
description: Bu makalede gelişmiş ayarlar dahil olmak üzere yeni bir Azure Content Delivery Network (CDN) uç noktası oluşturma gösterilmektedir.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/12/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 7bbd54ed68863a4704319a25af96b6463ad2377b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539703"
---
# <a name="create-an-azure-cdn-endpoint"></a>Azure CDN uç noktası oluşturma
Bu makalede, mevcut bir CDN profilinde [Azure Content Delivery Network (CDN)](cdn-overview.md) uç noktası oluşturmaya yönelik tüm ayarlar açıklanmaktadır. Bir profil ve uç nokta oluşturduktan sonra müşterilerinize içerik sunmaya başlayabilirsiniz. Bir profil ve uç nokta oluşturmaya yönelik hızlı başlangıç için bkz. [hızlı başlangıç: Azure CDN profili ve uç noktası oluşturma](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Önkoşullar
Bir CDN uç noktası oluşturabilmeniz için önce bir veya daha fazla CDN uç noktası içerebilen en az bir CDN profili oluşturmuş olmanız gerekir. CDN uç noktalarınızı internet etki alanı, web uygulaması veya başka ölçütlere göre düzenlemek için birden çok profil kullanabilirsiniz. CDN fiyatlandırma, CDN profil düzeyinde uygulandığından, Azure CDN fiyatlandırma katmanlarının bir karışımını kullanmak istiyorsanız birden çok CDN profili oluşturmanız gerekir. CDN profili oluşturmak için bkz. [Yeni BIR CDN profili oluşturma](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Azure portalında oturum açma
Azure hesabınızla [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-new-cdn-endpoint"></a>Yeni bir CDN uç noktası oluşturma

1. [Azure portalında](https://portal.azure.com), CDN profilinize gidin. Önceki adımda bunu panoya sabitlemiş olabilirsiniz. Aksi takdirde, **Tüm hizmetler**’i, sonra da **CDN profilleri**’ni seçerek bulabilirsiniz. **CDN profilleri** bölmesinde, uç noktanızı eklemeyi planladığınız profili seçin. 
   
    CDN profili bölmesi görünür.

2. **Uç nokta** seçin.
   
    ![CDN Select Endpoint](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    **Uç nokta ekleyin** sayfası görüntülenir.
   
    ![Uç nokta sayfası ekle](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. **Ad** için, yeni CDN uç noktasına yönelik benzersiz bir ad girin. Bu ad, etki alanındaki önbelleğe alınmış kaynaklarınıza erişmek için kullanılır. _\<endpointname>_ azureedge.net.

4. **Kaynak türü** için aşağıdaki kaynak türlerinden birini seçin: 
   - Azure depolama için **depolama**
   - Azure Cloud Services için **bulut hizmeti**
   - Azure Web Apps **Web uygulaması**
   - Genel olarak erişilebilen diğer tüm kaynak Web sunucuları için **özel başlangıç** (Azure 'da veya başka bir yerde barındırılan)

5. **Kaynak ana bilgisayar adı** için, kaynak sunucu etki alanınızı seçin veya girin. Açılan listede, 4. adımda belirttiğiniz türdeki tüm kullanılabilir kaynak sunucuları listelenir. Kaynak türü olarak **özel kaynak** ' ı seçtiyseniz, özel kaynak sunucunuzun etki alanını girin.
    
6. **Kaynak yolu** için, önbelleğe almak istediğiniz kaynakların yolunu girin. 5. adımda belirttiğiniz etki alanındaki herhangi bir kaynağın önbelleğe alınmasına izin vermek için bu ayarı boş bırakın.
    
7. **Kaynak ana bilgisayar üst bilgisi** için, Azure CDN'nin her bir istekle göndermesini istediğiniz ana bilgisayar üst bilgisini girin veya varsayılan değeri bırakın.
   
   > [!NOTE]
   > Azure Storage ve Web Apps gibi bazı kaynak türleri, ana bilgisayar üst bilgisinin kaynağın etki alanı ile eşleşmesini gerektirir. Etki alanından farklı ana bilgisayar üst bilgisi gerektiren bir kaynağa sahip değilseniz varsayılan değeri bırakmanız gerekir.
   > 
    
8. **Protokol** ve **kaynak bağlantı noktası** için, kaynak sunucudaki kaynaklarınıza erişmek üzere kullanılacak protokolleri ve bağlantı noktalarını belirtin. En az bir protokol (HTTP veya HTTPS) seçilmelidir. HTTPS içeriğine erişmek için CDN tarafından belirtilen etki alanını ( _\<endpointname>_ . azureedge.net) kullanın. 
   
   > [!NOTE]
   > **Kaynak bağlantı noktası** değeri, yalnızca uç noktanın kaynak sunucudan bilgi almak için kullandığı bağlantı noktasını belirler. Uç noktanın kendisi, **Kaynak bağlantı noktası** değerinden bağımsız olarak, yalnızca varsayılan HTTP ve HTTPS bağlantı noktalarındaki (80 ve 443) uç istemciler tarafından kullanılabilir.  
   > 
   > **Akamai'den Azure CDN** profillerindeki uç noktalar, kaynak bağlantı noktaları için tam TCP bağlantı noktası aralığına izin vermez. İzin verilmeyen kaynak bağlantı noktalarının listesi için bkz. [Akamai'den Azure CDN İzin Verilen Kaynak Bağlantı Noktaları](/previous-versions/azure/mt757337(v=azure.100)).  
   > 
   > Azure CDN özel etki alanları için HTTPS desteği **Akamai ürünlerden Azure CDN** desteklenmez. Daha fazla bilgi için bkz. [Azure CDN özel etki alanı üzerinde HTTPS'yi yapılandırma](cdn-custom-ssl.md).
    
9. **Için iyileştirilmiş** için, uç noktanın teslim etmesini istediğiniz senaryoya ve içerik türüne en iyi eşleşen bir iyileştirme türü seçin. Daha fazla bilgi için bkz. [içerik teslimi türü için Azure CDN iyileştirme](cdn-optimization-overview.md).

    Aşağıdaki en iyi duruma getirme türü ayarları, profil türüne göre desteklenir:
    - **Microsoft profillerinden Azure CDN Standart** :
       - [**Genel web teslimi**](cdn-optimization-overview.md#general-web-delivery)

    - **Verizon profillerden Verizon ve Azure CDN Premium** **'dan standart Azure CDN** :
       - [**Genel web teslimi**](cdn-optimization-overview.md#general-web-delivery)
       - [**Dinamik site hızlandırma**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Akamai profillerden Azure CDN Standart** :
       - [**Genel web teslimi**](cdn-optimization-overview.md#general-web-delivery)
       - [**Genel medya akışı**](cdn-optimization-overview.md#general-media-streaming)
       - [**İsteğe bağlı medya akışı videosu**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Büyük dosya indirme**](cdn-optimization-overview.md#large-file-download)
       - [**Dinamik site hızlandırma**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Yeni uç nokta oluşturmak için **Ekle**’yi seçin.
   
    Uç nokta oluşturulduktan sonra, profile yönelik uç noktalar listesinde görünür.
    
    ![CDN uç noktası](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Kaydın yayılması zaman alacağından, uç nokta hemen kullanılabilir olmaz: 
    - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
    - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
    - **Verizon 'den Azure CDN Standart** ve **Verizon profillerinden Azure CDN Premium** için, yayma genellikle 30 dakika içinde tamamlanır. 
   
    Uç nokta yapılandırması, mevcut nokta (POP) sunucularına yayılmadan önce CDN etki alanı adını kullanmayı denerseniz, bir HTTP 404 yanıt durumu alabilirsiniz. Uç noktanızı oluşturduğumdan bu yana birkaç saat sonra hala 404 yanıt durumu alıyorsanız, bkz. [404 durum kodu döndüren uç noktalar Azure CDN sorun giderme](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık gerekli olmadığında bir uç noktayı silmek için, seçin ve **Sil**' i seçin. 

## <a name="next-steps"></a>Sonraki adımlar
Özel etki alanları hakkında bilgi edinmek için, CDN uç noktanıza özel etki alanı eklemeye yönelik öğreticiye geçin.

> [!div class="nextstepaction"]
> [Özel etki alanı ekleme](cdn-map-content-to-custom-domain.md)


