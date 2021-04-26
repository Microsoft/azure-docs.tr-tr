---
title: Azure Media Services’a genel bakış | Microsoft Docs
description: Microsoft Azure Media Services, geliştiricilerin ölçeklenebilir medya yönetimi ve teslimi uygulamaları oluşturmalarına olanak tanıyan genişletilebilir bir bulut tabanlı platformdur. Bu makale Azure Media Services bir genel bakış sunar.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6e68e53387aa50b99bab8ed4cdba7f1e97fc48c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008250"
---
# <a name="azure-media-services-overview"></a>Azure Media Services’a genel bakış

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Media Services sürümünü seçin:"]
> * [Sürüm 3](../latest/media-services-overview.md)
> * [Sürüm 2](media-services-overview.md)

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS), geliştiricilerin ölçeklenebilir medya yönetimi ve teslimi uygulamaları oluşturmalarına olanak tanıyan genişletilebilir bir bulut tabanlı platformdur. Media Services, çeşitli istemcilere (TV, PC ve mobil cihazlar gibi) isteğe bağlı olarak veya canlı akış halinde teslim amacıyla video ve ses içeriklerini güvenli bir şekilde yüklemenizi, depolamanızı, kodlamanızı ve paketlemenizi sağlayan REST API'lerini temel alır.

Yalnızca Media Services’i kullanarak uçtan uca iş akışları oluşturabilirsiniz. Ayrıca, iş akışınızın bazı bölümleri için üçüncü taraf bileşenleri kullanmayı da tercih edebilirsiniz. Örneğin, bir üçüncü taraf kodlayıcısı kullanarak kodlayın. Daha sonra Media Services’i kullanarak yükleyin, koruyun, paketleyin ve teslim edin. İçeriğinizi canlı akışla aktarmayı veya isteğe bağlı içerik teslimini tercih edebilirsiniz. 


## <a name="compliance-privacy-and-security"></a>Uyumluluk, Gizlilik ve Güvenlik

Önemli bir anımsatıcı olarak, Azure Media Services kullanmanız için geçerli olan yasaların tümüne uymanız gerekir ve Media Services veya herhangi bir Azure hizmetini diğerlerinin haklarını ihlal eden veya başkalarına zararlı olabilecek bir biçimde kullanamazsınız.

Media Services herhangi bir videoyu/görüntüyü karşıya yüklemeden önce, video/görüntüdeki tüm gerekli haklara (varsa), verilerin Media Services ve Azure 'da kullanım, işleme ve depolama için gerekli olduğu durumlar dahil olmak üzere videoyu/görüntüsünü kullanmak için uygun haklara sahip olmanız gerekir. Bazı daireler, koleksiyon için özel yasal gereksinimler (örneğin, biyometrik veriler gibi belirli veri kategorilerinin çevrimiçi olarak işlenmesi ve depolanması) uygulayabilir. Media Services ve Azure 'u özel yasal gereksinimlere yönelik herhangi bir veri konusunun işleme ve depolama alanı için kullanmadan önce, sizin için geçerli olabilecek her türlü meşru gereksinimlerle uyumluluğa sahip olduğunuzdan emin olmanız gerekir.

Media Services uyumluluk, gizlilik ve güvenlik hakkında bilgi edinmek için lütfen Microsoft [Güven Merkezi](https://www.microsoft.com/trust-center/?rtc=1)' ni ziyaret edin. Microsoft 'un gizlilik yükümlülüklerinde, verilerinizi silme dahil olmak üzere veri işleme ve bekletme uygulamaları için lütfen Microsoft 'un [Gizlilik bildirimi](https://privacy.microsoft.com/PrivacyStatement), [çevrimiçi hizmet koşulları](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") ve [veri işleme eki](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA") gözden geçirin. Media Services kullanarak, OST, DPA ve gizlilik bildirimiyle bağlanmayı kabul etmiş olursunuz.
 
## <a name="prerequisites"></a>Önkoşullar

Azure Media Services’i kullanmaya başlamak için aşağıdakilerin bulunması gerekir:

* Azure hesabı. Bir hesabınız yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com).
* Bir Azure Media Services hesabı. Daha fazla bilgi için bkz. [Hesap Oluşturma](media-services-portal-create-account.md).
* (İsteğe bağlı) Geliştirme ortamı ayarlayın. Geliştirme ortamınız için .NET veya REST API’yi seçin. Daha fazla bilgi için bkz. [Ortam Ayarlama](media-services-dotnet-how-to-use.md).

    Ayrıca, [AMS API’ye programlama aracılığıyla nasıl bağlanılacağını öğrenin](media-services-use-aad-auth-to-access-ams-api.md).
* Standart veya premium akış uç noktası başlatılmış durumda.  Daha fazla bilgi için bkz. [akış uç noktalarını yönetme](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDK’lar ve araçlar

Media Services çözümleri oluşturmak için şunları kullanabilirsiniz:

* [Media Services REST API'si](/rest/api/media/operations/azure-media-services-rest-api-reference)
* Kullanılabilir istemci SDK'larından biri:
    * .NET için Azure Media Services SDK
    
        * [NuGet paketi](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub Kaynak kodu](https://github.com/Azure/azure-sdk-for-media-services)
    * [Java Için Azure SDK](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Node.js için Azure Media Services](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Bu, Microsoft dışı bir Node.js SDK sürümüdür. Bir topluluğun gözetimi altındadır ve şu anda AMS API'lerinin %100’ünü kapsamamaktadır).
* Mevcut araçlar:
    * [Azure portalı](https://portal.azure.com/)
    * [Azure-Media-Services-Gezgini](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Gezgini (AMSE), Windows için bir Winforms/C# uygulamasıdır)

> [!NOTE]
> Java SDK'ın en son sürümünü almak ve Java ile geliştirmeye başlamak için bkz. [Media Services için Java istemcisi SDK’sı ile çalışmaya başlama](./media-services-java-how-to-use.md). <br/>
> Media Services için en yeni PHP SDK'sını indirmek üzere, [Packagist deposunda](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) Microsoft/WindowAzure paketinin 0.5.7 sürümünü arayın.  

## <a name="code-samples"></a>Kod örnekleri

Birden fazla kod örneği için **Azure Kod Örnekleri** galerisine bakın: [Azure Media Services kod örnekleri](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Kavramlar

Azure Media Services kavramları hakkında bilgi edinmek için bkz. [Kavramlar](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Desteklenen senaryolar ve veri merkezleri arasında Media Services kullanılabilirliği

Azure ortak senaryolar hakkında daha fazla bilgi için bkz. [AMS senaryoları](scenarios-and-availability.md).
Bölgesel kullanılabilirlik hakkında daha fazla bilgi için bkz. [Media Service kullanılabilirliği](availability-regions-v-2.md).

## <a name="service-level-agreement-sla"></a>Hizmet Düzeyi Sözleşmesi (SLA)

Daha fazla bilgi için bkz. [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

## <a name="support"></a>Destek

[Azure Desteği](https://azure.microsoft.com/support/options/), Media Services de dahil olmak üzere Azure için destek seçenekleri sağlar.

## <a name="provide-feedback"></a>Geribildirim gönderme

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
