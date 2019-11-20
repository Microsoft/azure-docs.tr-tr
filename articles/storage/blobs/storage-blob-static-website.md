---
title: Azure depolama 'da statik Web sitesi barındırma
description: Modern Web uygulamalarını barındırmak için uygun maliyetli ve ölçeklenebilir bir çözüm sunan Azure Storage statik Web sitesi barındırma.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 8de36ea9f7bb77443b22e038172ee69bb8435b29
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311227"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure depolama 'da statik Web sitesi barındırma

*$Web*adlı bir depolama kapsayıcısından doğrudan statik IÇERIK (HTML, CSS, JavaScript ve resim dosyaları) hizmeti sağlayabilirsiniz. İçeriğinizi Azure Storage 'da barındırmak, [Azure işlevleri](/azure/azure-functions/functions-overview) ve diğer hizmet olarak platform (PaaS) hizmetlerini içeren sunucusuz mimariler kullanmanıza olanak sağlar.

> [!NOTE]
> Siteniz sunucu tarafı koduna bağımlıysa bunun yerine [Azure App Service](/azure/app-service/overview) kullanın.

## <a name="setting-up-a-static-website"></a>Statik Web sitesi ayarlama

Statik Web sitesi barındırma, depolama hesabında etkinleştirmeniz gereken bir özelliktir.

Statik Web sitesi barındırmayı etkinleştirmek için, varsayılan dosyanızın adını seçin ve isteğe bağlı olarak özel bir 404 sayfasının yolunu belirtin. **$Web** adlı bir BLOB depolama kapsayıcısı hesapta zaten yoksa, sizin için bir tane oluşturulur. Sitenizin dosyalarını bu kapsayıcıya ekleyin.

Adım adım yönergeler için bkz. [Azure depolama 'da statik bir Web sitesi barındırma](storage-blob-static-website-how-to.md).

![Azure depolama statik Web siteleri ölçümleri ölçümü](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$Web** kapsayıcısındaki dosyalar, anonim erişim istekleri aracılığıyla sunulan ve yalnızca okuma işlemleri aracılığıyla erişilebilen büyük/küçük harfe duyarlıdır.

## <a name="uploading-content"></a>İçerik karşıya yükleniyor

**$Web** kapsayıcısına içerik yüklemek için bu araçlardan herhangi birini kullanabilirsiniz:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell modülü](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code uzantısı](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>İçeriği görüntüleme

Kullanıcılar Web sitesinin genel URL 'sini kullanarak bir tarayıcıdan site içeriğini görüntüleyebilir. Azure portal, Azure CLı veya PowerShell kullanarak URL 'YI bulabilirsiniz. Bu tabloyu kılavuz olarak kullanın.

|Araç| Kılavuz |
|----|----|
|**Azure portalda** | [Azure portal kullanarak Web sitesi URL 'sini bulma](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Azure CLı kullanarak Web sitesi URL 'sini bulma](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell modülü** | [PowerShell kullanarak Web sitesi URL 'sini bulma](storage-blob-static-website-how-to.md#powershell-find-url) |

Sitenizin URL 'SI bölgesel bir kod içerir. Örneğin `https://contosoblobaccount.z22.web.core.windows.net/` URL 'SI, `z22` bölgesel kodunu içerir.

Bu kodun URL 'de kalması gerekir, ancak yalnızca iç kullanım içindir ve bu kodu başka bir şekilde kullanmak zorunda kalmazsınız.

Statik Web sitesi barındırmayı etkinleştirdiğinizde belirttiğiniz dizin belgesi, kullanıcılar siteyi açtıklarında ve belirli bir dosya belirtmezseniz görüntülenir (örneğin: `https://contosoblobaccount.z22.web.core.windows.net`).  

Sunucu bir 404 hatası döndürürse ve Web sitesini etkinleştirdiğinizde bir hata belgesi belirtmediğinde, kullanıcıya varsayılan bir 404 sayfası döndürülür.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Web kapsayıcısının genel erişim düzeyini ayarlamanın etkisi

**$Web** kapsayıcısının genel erişim düzeyini değiştirebilirsiniz, ancak bu dosyalar Anonim erişim istekleri aracılığıyla sunulduğundan birincil statik Web sitesi uç noktası üzerinde hiçbir etkisi yoktur. Bu, tüm dosyalara genel (salt okuma) erişimi anlamına gelir.

Aşağıdaki ekran görüntüsünde Azure portal ortak erişim düzeyi ayarı gösterilmektedir:

![Portalda genel erişim düzeyinin nasıl ayarlanacağını gösteren ekran görüntüsü](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Birincil statik Web sitesi uç noktası etkilenmediğinden, genel erişim düzeyinde yapılan bir değişiklik birincil blob hizmeti uç noktasını etkiler.

Örneğin, **$Web** kapsayıcısının genel erişim düzeyini ( **Anonim erişim olmadan)** **BLOB 'a (yalnızca blob 'lar için anonim okuma erişimi**) değiştirirseniz, birincil statik Web sitesi uç noktasına genel erişim düzeyi `https://contosoblobaccount.z22.web.core.windows.net/index.html` değişmez.

Ancak, birincil blob hizmeti uç noktasına `https://contosoblobaccount.blob.core.windows.net/$web/index.html` ' a Genel erişim Private ' dan Public ' e değişir. Artık kullanıcılar bu iki uç noktanın birini kullanarak bu dosyayı açabilir.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Content Delivery Network (CDN) ve Güvenli Yuva Katmanı (SSL) desteği

Statik Web sitesi dosyalarınızı özel etki alanınız ve HTTPS üzerinden kullanılabilir hale getirmek için, [https üzerinden özel etki alanlarıyla bloblara erişmek üzere Azure CDN kullanma](storage-https-custom-domain-cdn.md)konusuna bakın. Bu işlemin bir parçası olarak, birincil *BLOB hizmeti* uç noktası yerine CDN 'nizi birincil *statik Web sitesi* uç noktasına getirmeniz gerekir. CDN yapılandırması hemen yürütülmediğinden, içeriğiniz görünür olması için birkaç dakika beklemeniz gerekebilir.

Statik Web sitenizi güncelleştirdiğinizde, CDN uç noktası ' nı temizleyerek CDN uç sunucularında önbelleğe alınmış içeriği temizlemeyi unutmayın. Daha fazla bilgi için bkz. [Azure CDN uç noktasını temizleme](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> HTTPS, hesap Web uç noktası aracılığıyla yerel olarak desteklenir, bu nedenle web uç noktasına hem HTTP hem de HTTPS aracılığıyla erişilebilir. Ancak, depolama hesabı HTTPS üzerinden güvenli aktarım gerektirecek şekilde yapılandırıldıysa, kullanıcıların HTTPS uç noktasını kullanması gerekir. Daha fazla bilgi için bkz. [Azure Storage 'da güvenli aktarım gerektir](../common/storage-require-secure-transfer.md).
>
> HTTPS üzerinden özel etki alanlarının kullanılması için şu anda Azure CDN kullanılması gerekir.

## <a name="custom-domain-names"></a>Özel etki alanı adları

Statik Web sitenizi özel bir etki alanı aracılığıyla kullanılabilir hale getirebilirsiniz. Daha fazla bilgi için bkz. [Azure depolama hesabınız için özel etki alanı adı yapılandırma](storage-custom-domain-name.md).

Etki alanınızı Azure 'da barındırmada derinlemesine bir bakış için bkz. [Azure DNS etki alanınızı](../../dns/dns-delegate-domain-azure-dns.md)barındırma.

## <a name="pricing"></a>Fiyatlandırma

Statik Web sitesi barındırmayı ücretsiz olarak etkinleştirebilirsiniz. Yalnızca sitenizin kullandığı BLOB depolama alanı ve operasyon maliyetlerine göre faturalandırılırsınız. Azure Blob depolama fiyatları hakkında daha fazla bilgi için [Azure Blob depolama fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/storage/blobs/)göz atın.

## <a name="metrics"></a>Ölçümler

Statik Web sitesi sayfalarında ölçümleri etkinleştirebilirsiniz. Ölçümleri etkinleştirdikten sonra, **$Web** kapsayıcısındaki dosyalardaki trafik istatistikleri ölçüm panosunda raporlanır.

Statik Web sitesi sayfalarınızda ölçümleri etkinleştirmek için bkz. [statik Web sitesi sayfalarında ölçümleri etkinleştirme](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure depolama 'da statik bir Web sitesi barındırma](storage-blob-static-website-how-to.md)
* [HTTPS üzerinden özel etki alanlarıyla bloblara erişmek için Azure CDN kullanma](storage-https-custom-domain-cdn.md)
* [Blob veya Web uç noktanız için özel bir etki alanı adı yapılandırma](storage-custom-domain-name.md)
* [Azure İşlevleri](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [İlk sunucusuz Web uygulamanızı oluşturma](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Öğretici: etki alanınızı Azure DNS barındırın](../../dns/dns-delegate-domain-azure-dns.md)
