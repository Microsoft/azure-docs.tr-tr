---
title: 'Öğretici: Depolama bloblarına HTTPS üzerinden Azure CDN özel etki alanı kullanarak erişme'
description: Özel BLOB depolama uç noktanız için Azure CDN özel etki alanı ekleme ve bu etki alanında HTTPS 'yi etkinleştirme hakkında bilgi edinin.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 296a51edf024551a03330f4843690e97c6abd47d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564892"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Öğretici: Depolama bloblarına HTTPS üzerinden Azure CDN özel etki alanı kullanarak erişme

Azure depolama hesabınızı Azure Content Delivery Network (CDN) ile tümleştirdikten sonra, özel bir etki alanı ekleyebilir ve özel blob depolama uç noktanız için bu etki alanında HTTPS’yi etkinleştirebilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticideki adımları tamamlamadan önce, ilk olarak Azure depolama hesabınızı Azure CDN ile tümleştirmeniz gerekir. Daha fazla bilgi için bkz. [Hızlı başlangıç: Azure CDN ile bir Azure depolama hesabını tümleştirme](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Özel etki alanı ekleme
Profilinizde bir CDN uç noktası oluşturduğunuzda, varsayılan olarak CDN içeriği sunmak için URL’nize azureedge.net adresinin alt etki alanı olan uç nokta adı eklenir. Bir CDN uç noktası ile özel etki alanını ilişkilendirme seçeneğiniz de vardır. Bu seçeneği kullanarak URL’nizde bir uç nokta adı yerine özel etki alanı ile içerik sunabilirsiniz. Uç noktanıza özel bir etki alanı eklemek için, şu öğreticideki yönergeleri izleyin: [Azure CDN uç noktanıza özel etki alanı ekleme](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>HTTPS’yi yapılandırma
Özel etki alanınızda HTTPS protokolünü kullanarak, verilerinizin TLS/SSL şifrelemesi aracılığıyla güvenli bir şekilde teslim edilmesini sağlarsınız. Web tarayıcınız HTTPS üzerinden bir web sitesine bağlanırken, web sitesinin güvenlik sertifikasını doğrular ve bu sertifikanın yasal bir sertifika yetkilisi tarafından verildiğini doğrular. Özel etki alanınızda HTTPS yapılandırmak için, şu öğreticideki yönergeleri izleyin: [Azure CDN özel etki alanında HTTPS yapılandırma](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Paylaşılan Erişim İmzaları
Blob depolama uç noktanız anonim okuma erişimini engellemek için yapılandırılmışsa, özel etki alanınıza yaptığınız her istekte bir [Paylaşılan Erişim İmzası (SAS)](cdn-sas-storage-support.md) belirteci sağlamanız gerekir. Varsayılan olarak, blob depolama uç noktaları anonim okuma erişimini engeller. SAS hakkında daha fazla bilgi için bkz. [Kapsayıcılara ve bloblara anonim okuma erişimini yönetme](../storage/blobs/anonymous-read-access-configure.md).

Azure CDN SAS belirtecine eklenen kısıtlamaları yok sayar. Örneğin, tüm SAS belirteçlerinin bir sona erme zamanı vardır, yani içerik CDN varlık noktası (POP) sunucularından temizlenene kadar içeriğe süresi dolmuş bir SAS ile hala erişilebilir. Önbellek yanıtı üst bilgisini ayarlayarak verilerin Azure CDN üzerinde ne kadar süreyle önbelleğe alınacağını denetleyebilirsiniz. Daha fazla bilgi için, bkz. [Azure CDN’de Azure Depolama bloblarının sona erme tarihini yönetme](cdn-manage-expiration-of-blob-content.md).

Aynı blob uç noktası için birden çok SAS URL’si oluşturursanız, sorgu dizesi önbelleğe almayı göz önünde bulundurun. Bunu yapmak her URL’nin benzersiz bir varlık olarak kabul edilmesini sağlar. Daha fazla bilgi için bkz. [Sorgu dizeleri içeren Azure CDN önbelleğe alma davranışını kontrol etme](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>HTTP’den -HTTPS’ye yeniden yönlendirme
[Standart kurallar altyapısı](cdn-standard-rules-engine.md) veya [Verizon Premium kuralları ALTYAPıSı](cdn-verizon-premium-rules-engine.md)ile bir URL YENIDEN yönlendirme KURALı oluşturarak HTTP trafiğini HTTPS 'ye yeniden yönlendirmeyi tercih edebilirsiniz. Standart kurallar altyapısı yalnızca Microsoft profillerinin Azure CDN için kullanılabilir, ancak Verizon Premium kuralları altyapısı yalnızca Verizon profillerdeki Azure CDN Premium ile kullanılabilir.

![Microsoft yeniden yönlendirme kuralı](./media/cdn-storage-custom-domain-https/cdn-standard-redirect-rule.png)

Yukarıdaki kuralda, ana bilgisayar adı, yol, sorgu dizesi ve parçadan ayrıldığınızda gelen değerler yeniden yönlendirmede kullanılır. 

![Verizon yeniden yönlendirme kuralı](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

Yukarıdaki kuralda, CDN uç *nokta-adı* , CDN uç noktanız için yapılandırdığınız ada başvurur ve bu, açılan listeden seçim yapabilirsiniz. *origin-path* değeri statik içeriğinizin bulunduğu kaynak depolama hesabı içindeki yola başvurur. Tüm statik içeriği tek bir kapsayıcıda barındırıyorsanız, *origin-path* değerini bu kapsayıcının adıyla değiştirin.

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama
Azure CDN üzerinden bloblara eriştiğinizde, POP sunucuları ve kaynak (Blob depolama) arasında trafik için [Blob depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/) ve POP sunucularından erişilen veriler için [Azure CDN fiyatlandırması](https://azure.microsoft.com/pricing/details/cdn/) üzerinden ücret ödersiniz.

Örneğin, ABD’de Azure CDN kullanılarak erişilen bir depolama hesabınız varsa ve Avrupa’da bulunan biri Azure CDN üzerinden bu depolama hesabına erişmeye çalışırsa, Azure CDN önce bu blob için Avrupa’ya en yakın POP sunucusunu denetler. Bulunursa, Azure CDN blobun bu kopyasına erişir ve Azure CDN üzerinden erişildiği için CDN fiyatlandırmasını kullanır. Bulunamazsa, Azure CDN blobu POP sunucusuna kopyalar (bu işlem Blob depolama fiyatlandırmasında belirtilen çıkış ve işlem ücretlerine tabidir) ve ardından POP sunucusu üzerinde dosyaya erişir (bu işlem Azure CDN faturalandırmasıyla sonuçlanır).

## <a name="next-steps"></a>Sonraki adımlar
[Öğretici: Azure CDN önbelleğe alma kurallarını ayarlama](cdn-caching-rules-tutorial.md)