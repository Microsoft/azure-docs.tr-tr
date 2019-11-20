---
title: Azure Media Services akış uç noktasına genel bakış | Microsoft Docs
description: Bu konu, Azure Media Services akış uç noktalarına genel bir bakış sunar.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ac9c9a73e52c678c8a6d9b1e1779d9ec75cab2c8
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "69016443"
---
# <a name="streaming-endpoints-overview"></a>Akış uç noktalarına genel bakış  

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services (AMS) ' de bir **akış uç noktası** , içeriği doğrudan istemci oynatıcı uygulamasına veya daha fazla dağıtım için bir Content DELIVERY Network (CDN) teslim edebilen bir akış hizmetini temsil eder. Media Services Ayrıca sorunsuz Azure CDN tümleştirme sağlar. Bir StreamingEndpoint hizmetinden giden akış canlı bir akış, isteğe bağlı bir video veya Media Services hesabınızda varlığınızın aşamalı olarak indirilmesi olabilir. Her Azure Media Services hesabı varsayılan bir StreamingEndpoint içerir. Hesap altında ek StreamingEndpoints oluşturulabilir. İki StreamingEndpoints, 1,0 ve 2,0 sürümü vardır. 10 Ocak 2017 tarihinden itibaren, yeni oluşturulan AMS hesapları sürüm 2,0 **varsayılan** streamingendpoint ' i içerecektir. Bu hesaba eklediğiniz ek akış uç noktaları da sürüm 2,0 olacaktır. Bu değişiklik mevcut hesapları etkilemez; Mevcut StreamingEndpoints sürümü 1,0 olur ve sürüm 2,0 ' ye yükseltilebilir. Bu değişiklik ile davranış, faturalandırma ve özellik değişiklikleri olacaktır (daha fazla bilgi için aşağıda belgelenen **akış türleri ve sürümleri** bölümüne bakın).

Azure Media Services akış uç noktası varlığına aşağıdaki özellikleri ekledi: **Cdnprovider**, **cdnprofıle**, **streamingendpointversion**. Bu özelliklere ayrıntılı genel bakış için, bkz [](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Bir Azure Media Services hesabı oluşturduğunuzda, **durdurulmuş** durumda sizin için varsayılan standart akış uç noktası oluşturulur. Varsayılan akış uç noktasını silemezsiniz. Hedeflenen bölgedeki Azure CDN kullanılabilirliğine bağlı olarak, varsayılan olarak yeni oluşturulan varsayılan akış uç noktası, "StandardVerizon" CDN sağlayıcısı tümleştirmesini da içerir. 
                
> [!NOTE]
> Azure CDN tümleştirme, akış uç noktası başlatılmadan önce devre dışı bırakılabilir. `hostname` Ve akış URL 'si, CDN 'yi etkinleştirip etkinleştirmeksizin aynı kalır.

Bu konu, akış uç noktaları tarafından sunulan ana işlevlere genel bir bakış sunar.

## <a name="naming-conventions"></a>Adlandırma kuralları

Varsayılan uç nokta için:`{AccountName}.streaming.mediaservices.windows.net`

Ek uç noktalar için:`{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Akış türleri ve sürümleri

### <a name="standardpremium-types-version-20"></a>Standart/Premium türleri (sürüm 2,0)

Media Services Ocak 2017 sürümünden başlayarak iki akış türü vardır: **Standart** (Önizleme) ve **Premium**. Bu türler, "2,0" akış uç noktası sürümünün bir parçasıdır.


|Type|Açıklama|
|--------|--------|  
|**Standart**|Varsayılan akış uç noktası **Standart** bir türdür, akış birimleri ayarlanarak Premium türüne değiştirilebilir.|
|**Premium** |Bu seçenek, daha yüksek ölçek veya denetim gerektiren profesyonel senaryolar için uygundur. Akış birimlerini ayarlayarak bir **Premium** türüne geçiş yapabilirsiniz.<br/>Ayrılmış akış uç noktaları yalıtılmış ortamda canlı ve kaynaklar için rekabet etmez.|

Büyük internet kitlelerine içerik teslim etmek isteyen müşteriler için, akış uç noktasında CDN 'yi etkinleştirmenizi öneririz.

Daha ayrıntılı bilgi için aşağıdaki [akış türlerini karşılaştırma](#comparing-streaming-types) bölümüne bakın.

### <a name="classic-type-version-10"></a>Klasik tür (sürüm 1,0)

Ocak 10 2017 sürümünden önce AMS hesapları oluşturan kullanıcılar için, **Klasik** bir akış uç noktası türüne sahip olursunuz. Bu tür, "1,0" akış uç noktası sürümünün bir parçasıdır.

**Sürümünüz "1,0"** akış uç noktası > = 1 Premium akış BIRIMI (su) içeriyorsa, Premium akış uç noktası olur ve ek yapılandırma adımları olmadan tüm AMS özelliklerini ( **Standart/Premium** türü gibi) sağlar.

>[!NOTE]
>**Klasik** akış uç noktaları (sürüm "1,0" ve 0 su), sınırlı özellikler sağlar ve bir SLA içermez. Daha iyi bir deneyim almak ve dinamik paketleme veya şifreleme gibi özellikleri ve **Standart** türle birlikte gelen diğer özellikleri kullanmak için **Standart** türe geçiş yapmanız önerilir. **Standart** türe geçiş yapmak için [Azure Portal](https://portal.azure.com/) gidin ve **Standart olarak kabul**et ' i seçin. Geçiş hakkında daha fazla bilgi için [geçiş](#migration-between-types) bölümüne bakın.
>
>Bu işlemin geri çekilemez ve fiyatlandırma etkisi olduğunu unutmayın.
>
 
## <a name="comparing-streaming-types"></a>Akış türlerini karşılaştırma

### <a name="versions"></a>Sürümler

|Type|Streammingendpointversion|Ölçek birimleri|CDN|Faturalandırma|
|--------------|----------|-----------------|-----------------|-----------------|
|Klasik|1.0|0|NA|Boş|
|Standart akış uç noktası (Önizleme)|2.0|0|Evet|Ücretli|
|Premium Akış Birimleri|1.0|>0|Evet|Ücretli|
|Premium Akış Birimleri|2.0|>0|Evet|Ücretli|

### <a name="features"></a>Özellikler

Özellik|Standart|Premium
---|---|---
Aktarım hızı |600 Mbps 'e kadar, bir CDN kullanıldığında çok daha yüksek bir verimlilik sağlar.|akış birimi başına 200 Mbps (SU). , Bir CDN kullanıldığında daha yüksek etkili bir verimlilik sağlayabilir.
CDN|Azure CDN, üçüncü taraf CDN veya CDN yok.|Azure CDN, üçüncü taraf CDN veya CDN yok.
Faturalandırma eşit olarak dağıtılır| Günlük|Günlük
Dinamik şifreleme|Evet|Evet
Dinamik paketleme|Evet|Evet
Ölçek|Hedeflenen işleme kadar otomatik olarak ölçeklendirin.|Ek akış birimleri.
IP filtreleme/G20/özel ana bilgisayar <sup>1</sup>|Evet|Evet
Aşamalı indirme|Evet|Evet
Önerilen kullanım |Akış senaryolarının çoğunluğu için önerilir.|Profesyonel kullanım. 

<sup>1</sup> yalnızca CDN bitiş noktasında etkin olmadığında doğrudan akış uç noktasında kullanılır.<br/>

SLA bilgileri için bkz. [fiyatlandırma ve SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Türler arasında geçiş

Başlangıç | Bitiş | Action
---|---|---
Klasik|Standart|Kabul etmeniz gerekiyor
Klasik|Premium| Ölçek (ek akış birimleri)
Standart/Premium|Klasik|Kullanılamıyor (akış uç noktası sürümü 1,0 ise). Ölçek birimleri "0" olarak ayarlanarak klasik olarak değiştirilmesine izin verilir.
Standart (CDN ile/olmadan)|Aynı yapılandırmalara sahip Premium|**Başlangıç** durumunda izin verilir. (Azure portal aracılığıyla)
Premium (CDN ile/olmadan)|Aynı yapılandırmalara sahip standart|**Başlama** durumunda izin verildi (Azure Portal aracılığıyla)
Standart (CDN ile/olmadan)|Farklı bir yapılandırmaya sahip Premium|**Durdurulmuş** durumda (Azure Portal aracılığıyla) izin verilir. Çalışma durumunda izin verilmiyor.
Premium (CDN ile/olmadan)|Farklı bir yapılandırmaya sahip standart|**Durdurulmuş** durumda (Azure Portal aracılığıyla) izin verilir. Çalışma durumunda izin verilmiyor.
CDN ile SU > = 1 ile sürüm 1,0|CDN olmadan standart/Premium|**Durdurulma** durumunda izin verildi. **Başlangıç** durumunda izin verilmiyor.
CDN ile SU > = 1 ile sürüm 1,0|CDN ile/ve olmadan standart|**Durdurulma** durumunda izin verildi. **Başlangıç** durumunda izin verilmiyor. Sürüm 1,0 CDN silinecek ve yeni bir oluşturulup başlatılacak.
CDN ile SU > = 1 ile sürüm 1,0|CDN ve CDN olmadan Premium|**Durdurulma** durumunda izin verildi. **Başlangıç** durumunda izin verilmiyor. Klasik CDN silinecek ve yeni bir oluşturulup başlatılacak.

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

