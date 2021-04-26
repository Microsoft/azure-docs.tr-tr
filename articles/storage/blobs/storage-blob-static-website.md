---
title: Azure Depolama'da statik web sitesi barındırma
description: Modern Web uygulamalarını barındırmak için uygun maliyetli ve ölçeklenebilir bir çözüm sunan Azure Storage statik Web sitesi barındırma.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: bbb996362df1f99a8702de310bc11f6828cc303a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008459"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Depolama'da statik web sitesi barındırma

*$Web* adlı bir depolama kapsayıcısından doğrudan statik IÇERIK (HTML, CSS, JavaScript ve resim dosyaları) hizmeti sağlayabilirsiniz. İçeriğinizi Azure Storage 'da barındırmak, [Azure işlevleri](../../azure-functions/functions-overview.md) ve diğer hizmet olarak platform (PaaS) hizmetlerini içeren sunucusuz mimariler kullanmanıza olanak sağlar. Azure Storage statik Web sitesi barındırma, bir Web sunucusunun içerik işlemesini gerektirmeyen durumlarda harika bir seçenektir.

[App Service statik Web Apps](https://azure.microsoft.com/services/app-service/static/) , Azure Storage statik Web sitesi barındırma için harika bir alternatiftir ve ayrıca bir Web sunucusunun içerik işlemesini gerektirmeyen durumlarda de uygundur. App Service statik Web Apps, GitHub kaynağından genel dağıtıma kadar tam olarak yönetilen bir sürekli tümleştirme ve sürekli teslim (CI/CD) iş akışı sağlar.

İçerik işlemek için bir Web sunucusuna ihtiyacınız varsa [Azure App Service](https://azure.microsoft.com/services/app-service/)kullanabilirsiniz.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up-a-static-website"></a>Statik Web sitesi ayarlama

Statik Web sitesi barındırma, depolama hesabında etkinleştirmeniz gereken bir özelliktir.

Statik Web sitesi barındırmayı etkinleştirmek için, varsayılan dosyanızın adını seçin ve isteğe bağlı olarak özel bir 404 sayfasının yolunu belirtin. **$Web** adlı bir BLOB depolama kapsayıcısı hesapta zaten yoksa, sizin için bir tane oluşturulur. Sitenizin dosyalarını bu kapsayıcıya ekleyin.

Adım adım yönergeler için bkz. [Azure depolama 'da statik bir Web sitesi barındırma](storage-blob-static-website-how-to.md).

![Azure depolama statik Web siteleri ölçümleri ölçümü](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$Web** kapsayıcısındaki dosyalar, anonim erişim istekleri aracılığıyla sunulan ve yalnızca okuma işlemleri aracılığıyla erişilebilen büyük/küçük harfe duyarlıdır.

## <a name="uploading-content"></a>İçerik karşıya yükleniyor

**$Web** kapsayıcısına içerik yüklemek için bu araçlardan herhangi birini kullanabilirsiniz:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell modülü](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code uzantısı](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>İçeriği görüntüleme

Kullanıcılar Web sitesinin genel URL 'sini kullanarak bir tarayıcıdan site içeriğini görüntüleyebilir. Azure portal, Azure CLı veya PowerShell kullanarak URL 'YI bulabilirsiniz. Bkz. [Web sitesi URL 'Sini bulma](storage-blob-static-website-how-to.md#portal-find-url).

Sunucu bir 404 hatası döndürürse ve Web sitesini etkinleştirdiğinizde bir hata belgesi belirtmediğinde, kullanıcıya varsayılan bir 404 sayfası döndürülür.

> [!NOTE]
> [Azure depolama Için çıkış noktaları arası kaynak paylaşımı (CORS) desteği](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) , statik Web sitesinde desteklenmez.

### <a name="regional-codes"></a>Bölgesel kodlar

Sitenizin URL 'SI bölgesel bir kod içerir. Örneğin, URL `https://contosoblobaccount.z22.web.core.windows.net/` bölgesel kod içerir `z22` .

Bu kodun URL 'de kalması gerekir, ancak yalnızca iç kullanım içindir ve bu kodu başka bir şekilde kullanmak zorunda kalmazsınız.

Statik Web sitesi barındırmayı etkinleştirdiğinizde belirttiğiniz dizin belgesi, kullanıcılar siteyi açtıklarında ve belirli bir dosya belirtmezseniz görüntülenir (örneğin: `https://contosoblobaccount.z22.web.core.windows.net` ).

### <a name="secondary-endpoints"></a>İkincil uç noktalar

[İkincil bir bölgede artıklık](../common/storage-redundancy.md#redundancy-in-a-secondary-region)ayarlarsanız, ikincil bir uç nokta kullanarak Web sitesi içeriğine de erişebilirsiniz. Veriler ikincil bölgelere zaman uyumsuz olarak çoğaltıldığından, ikincil uç noktada kullanılabilen dosyalar birincil uç noktada kullanılabilir dosyalarla her zaman eşitlenmemektedir.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Web kapsayıcısının genel erişim düzeyini ayarlamanın etkisi

**$Web** kapsayıcısının genel erişim düzeyini değiştirebilirsiniz, ancak bu dosyalar Anonim erişim istekleri aracılığıyla sunulduğundan birincil statik Web sitesi uç noktası üzerinde hiçbir etkisi yoktur. Bu, tüm dosyalara genel (salt okuma) erişimi anlamına gelir.

Aşağıdaki ekran görüntüsünde Azure portal ortak erişim düzeyi ayarı gösterilmektedir:

![Portalda genel erişim düzeyinin nasıl ayarlanacağını gösteren ekran görüntüsü](./media/anonymous-read-access-configure/configure-public-access-container.png)

Birincil statik Web sitesi uç noktası etkilenmediğinden, genel erişim düzeyinde yapılan bir değişiklik birincil blob hizmeti uç noktasını etkiler.

Örneğin, **$Web** kapsayıcısının genel erişim düzeyini ( **Anonim erişim olmadan)** **BLOB 'a (yalnızca blob 'lar için anonim okuma erişimi**) değiştirirseniz, birincil statik Web sitesi uç noktasına genel erişim düzeyi `https://contosoblobaccount.z22.web.core.windows.net/index.html` değişmez.

Bununla birlikte, birincil blob hizmeti uç noktasına genel erişim, `https://contosoblobaccount.blob.core.windows.net/$web/index.html` Private iken public olarak değişir. Artık kullanıcılar bu iki uç noktanın birini kullanarak bu dosyayı açabilir.

Bir depolama hesabında genel erişimin devre dışı bırakılması, bu depolama hesabında barındırılan statik Web sitelerini etkilemez. Daha fazla bilgi için bkz. [kapsayıcılar ve Bloblar için anonim genel okuma erişimini yapılandırma](anonymous-read-access-configure.md).

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Özel bir etki alanını statik bir Web sitesi URL 'siyle eşleme

Statik Web sitenizi özel bir etki alanı aracılığıyla kullanılabilir hale getirebilirsiniz.

Azure Storage tarafından yerel olarak desteklendiğinden, özel etki alanınız için HTTP erişimini etkinleştirmek daha kolay olur. HTTPS 'yi etkinleştirmek için, Azure Storage özel etki alanları ile HTTPS 'yi henüz yerel olarak desteklemediğinden Azure CDN kullanmanız gerekir. adım adım yönergeler için bkz. [özel bir etki alanını Azure Blob depolama uç noktasına eşleme](storage-custom-domain-name.md) .

Depolama hesabı HTTPS üzerinden [Güvenli aktarım gerektirecek](../common/storage-require-secure-transfer.md) şekilde yapılandırıldıysa, kullanıcıların HTTPS uç noktasını kullanması gerekir.

> [!TIP]
> Etki alanınızı Azure 'da barındırmayı düşünün. Daha fazla bilgi için bkz. [Azure DNS etki alanınızı barındırma](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>HTTP üstbilgileri ekleme

Üstbilgileri statik Web sitesi özelliğinin bir parçası olarak yapılandırmanın bir yolu yoktur. Ancak üst bilgileri eklemek ve başlık değerlerini eklemek (veya üzerine yazmak) için Azure CDN kullanabilirsiniz. [Azure CDN Için standart kurallar altyapısı başvurusuna](../../cdn/cdn-standard-rules-engine-reference.md)bakın.

Önbelleğe almayı denetlemek için üst bilgileri kullanmak istiyorsanız, bkz. önbelleğe alma [kurallarıyla denetim Azure CDN önbelleğe alma davranışı](../../cdn/cdn-caching-rules.md).

## <a name="multi-region-website-hosting"></a>Çok bölgeli Web sitesi barındırma

Bir Web sitesini birden çok coğrafi durumda barındırmak istiyorsanız, bölgesel önbelleğe alma için bir [Content Delivery Network](../../cdn/index.yml) kullanmanızı öneririz. Her bölgede farklı içerik sağlamak istiyorsanız [Azure ön kapısını](../../frontdoor/index.yml) kullanın. Ayrıca yük devretme özellikleri de sağlar. Özel bir etki alanı kullanmayı planlıyorsanız [Azure Traffic Manager](../../traffic-manager/index.yml) önerilmez. Azure depolama 'nın özel etki alanı adlarını doğrulama biçimi nedeniyle sorunlar oluşabilir.


## <a name="pricing"></a>Fiyatlandırma

Statik Web sitesi barındırmayı ücretsiz olarak etkinleştirebilirsiniz. Yalnızca sitenizin kullandığı BLOB depolama alanı ve operasyon maliyetlerine göre faturalandırılırsınız. Azure Blob depolama fiyatları hakkında daha fazla bilgi için [Azure Blob depolama fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/storage/blobs/)göz atın.

## <a name="metrics"></a>Ölçümler

Statik Web sitesi sayfalarında ölçümleri etkinleştirebilirsiniz. Ölçümleri etkinleştirdikten sonra, **$Web** kapsayıcısındaki dosyalardaki trafik istatistikleri ölçüm panosunda raporlanır.

Statik Web sitesi sayfalarınızda ölçümleri etkinleştirmek için bkz. [statik Web sitesi sayfalarında ölçümleri etkinleştirme](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure depolama 'da statik bir Web sitesi barındırma](storage-blob-static-website-how-to.md)
* [Özel bir etki alanını Azure Blob depolama uç noktasıyla eşleme](storage-custom-domain-name.md)
* [Azure İşlevleri](../../azure-functions/functions-overview.md)
* [Azure App Service](../../app-service/overview.md)
* [İlk sunucusuz Web uygulamanızı oluşturma](/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Öğretici: Azure DNS’te etki alanınızı barındırma](../../dns/dns-delegate-domain-azure-dns.md)