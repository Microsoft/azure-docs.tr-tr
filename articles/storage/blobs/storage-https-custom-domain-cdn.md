---
title: HTTPS üzerinden özel etki alanlarıyla bloblara erişmek için Azure CDN kullanma
description: HTTPS üzerinden özel etki alanlarıyla bloblara erişmek için blob depolamayla Azure CDN tümleştirme hakkında bilgi edinin
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: f3386d04cd3316b38a094524d0d5d4f3c5ab36bb
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986850"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>HTTPS üzerinden özel etki alanlarıyla bloblara erişmek için Azure CDN kullanma

Azure Content Delivery Network (Azure CDN) artık özel etki alanı adları için HTTPS 'yi destekliyor. Azure CDN ile, HTTPS üzerinden özel etki alanı adınızı kullanarak bloblara erişebilirsiniz. Bunu yapmak için, blob veya Web uç noktanıza Azure CDN etkinleştirin ve Azure CDN özel bir etki alanı adına eşleyin. İşiniz bittiğinde, Azure tek tıklamayla erişim ve tam sertifika yönetimi aracılığıyla özel etki alanınız için HTTPS 'yi etkinleştirmeyi basitleştirir. Normal Azure CDN fiyatlandırmasında artış yoktur.

Azure CDN, aktarım sırasında Web uygulaması verilerinizin gizliliğini ve veri bütünlüğünü korumanıza yardımcı olur. HTTPS üzerinden trafiğe hizmeti sağlamak için SSL protokolünü kullanarak Azure CDN, verilerin internet üzerinden gönderildiğinde şifrelenmesini sağlar. Azure CDN ile HTTPS kullanmak, Web uygulamalarınızı saldırılara karşı korumanıza yardımcı olur.

> [!NOTE]  
> Azure CDN, özel etki alanı adları için SSL desteği sağlamaya ek olarak, dünyanın dört bir yanındaki yüksek bant genişliğine sahip içerik sunmak için uygulamanızı ölçeklendirmenize yardımcı olabilir. Daha fazla bilgi edinmek için bkz. [Azure CDN genel bakış](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Hızlı Başlangıç

Özel BLOB depolama uç noktanız için HTTPS 'yi etkinleştirmek üzere şunları yapın:

1.  [Bir Azure Depolama hesabını Azure CDN Ile tümleştirin](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Bu makalede, daha önce yapmadıysanız Azure portal bir depolama hesabı oluşturma işlemi adım adım açıklanmaktadır.

    > [!NOTE]  
    > Azure depolama 'daki statik Web siteleri desteğinin önizlemesi sırasında depolama Web uç noktanızı eklemek için, **kaynak türü** aşağı açılan listesinden **özel kaynak** ' ı seçin. Azure portal, bunu doğrudan depolama hesabınızda değil Azure CDN profilinizde yapmanız gerekir.

2.  [Azure CDN içeriğini özel bir etki alanıyla eşleyin](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Azure CDN özel bir etki alanında https 'Yi etkinleştirin](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Paylaşılan erişim imzaları

Varsayılan olarak, BLOB depolama uç noktaları anonim okuma erişimine izin vermez. BLOB depolama uç noktanız anonim okuma erişimine izin vermeyecek şekilde yapılandırıldıysa, özel etki alanınız için her istekte bir [paylaşılan erişim imza](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) belirteci sağlayın. Daha fazla bilgi için bkz. [Kapsayıcılara ve bloblara anonim okuma erişimini yönetme](storage-manage-access-to-resources.md).

Azure CDN, paylaşılan erişim imzası belirtecine eklenen kısıtlamalara uymaz. Örneğin, tüm paylaşılan erişim imzası belirteçleri sona erer. İçerik Azure CDN Edge düğümlerinden temizlenene kadar, zaman aşımına uğradı bir paylaşılan erişim imzasıyla içeriğe erişmeye devam edebilirsiniz. Önbellek yanıtı üst bilgisini ayarlayarak verilerin Azure CDN üzerinde ne kadar süreyle önbelleğe alınacağını denetleyebilirsiniz. Hakkında bilgi edinmek için bkz. [Azure CDN Azure Storage bloblarının kullanım süresini yönetme](../../cdn/cdn-manage-expiration-of-blob-content.md).

Aynı blob uç noktası için iki veya daha fazla paylaşılan erişim imzası URL 'Si oluşturursanız, Azure CDN için sorgu dizesi önbelleğini açmayı öneririz. Bu eylem, Azure 'un her bir URL 'YI benzersiz bir varlık olarak ele almasını sağlar. Daha fazla bilgi için bkz. [Sorgu dizeleri içeren Azure CDN önbelleğe alma davranışını kontrol etme](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>HTTP 'den HTTPS 'ye yeniden yönlendirme

HTTP trafiğini HTTPS 'ye yeniden yönlendirebilirsiniz. Bunun yapılması, Verizon ' den Azure CDN Premium sunumunun kullanımını gerektirir. Aşağıdaki kuralı uygulayarak [Azure CDN Rules ALTYAPıSıYLA http davranışını geçersiz kılın](../../cdn/cdn-rules-engine.md) :

![HTTP 'den HTTPS 'ye yönlendirme kuralı](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

Aşağı açılan listede seçtiğiniz *CDN-Endpoint-Name*, Azure CDN uç noktanız için yapılandırdığınız ada başvurur. *Kaynak yolu* , statik içeriğinizin depolandığı kaynak Depolama hesabınızdaki yolu ifade eder. Tüm statik içerikleri tek bir kapsayıcıda barındırıyorsanız, *kaynak-yolunu* o kapsayıcının adıyla değiştirin.

Daha ayrıntılı kurallar için [Azure CDN kuralları altyapısı özelliklerine](../../cdn/cdn-rules-engine-reference-features.md)bakın.

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Blob 'ları Azure CDN aracılığıyla eriştiğinizde, uç düğümleri ve kaynak (BLOB depolama) arasındaki trafik için [BLOB depolama fiyatlarını](https://azure.microsoft.com/pricing/details/storage/blobs/) ödeyin. Kenar düğümlerinden erişilen veriler için [Azure CDN fiyatlarını](https://azure.microsoft.com/pricing/details/cdn/) ödeyin.

Örneğin, Azure CDN üzerinden eriştiğiniz Batı ABD bir depolama hesabınız olduğunu varsayalım. UK 'teki birisi, bu depolama hesabındaki bir bloba Azure CDN aracılığıyla erişmeyi denediğinde, Azure ilk olarak UK 'ye en yakın olan uç düğümde blob 'u kontrol eder. Azure blobu bulursa, bir kopyaya erişir ve Azure CDN kendisine eriştiği için Azure CDN fiyatlandırması kullanır. Azure blobu bulamazsa, blobu kenar düğümüne kopyalar. Bu eylem, Blob Depolama fiyatlandırması 'nda belirtilen şekilde çıkış ve işlem ücretleri ile sonuçlanır. Daha sonra Azure, Azure CDN faturalandırma ile sonuçlanan kenar düğümündeki dosyaya erişir.

[Azure CDN fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/cdn/), özel etki alanı adları için HTTPS desteği yalnızca Verizon standart ve Premium ürünlerden Azure CDN için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [BLOB depolama uç noktanız için özel bir etki alanı adı yapılandırma](storage-custom-domain-name.md)
* [Azure Depolama'da statik web sitesi barındırma](storage-blob-static-website.md)
