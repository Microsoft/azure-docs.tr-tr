---
title: C# kullanarak Gen2 ortamından veri sorgulama-Azure Time Series Insights | Microsoft Docs
description: C# dilinde yazılmış bir uygulama kullanarak Azure Time Series Insights Gen2 ortamından verileri sorgulamayı öğrenin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: aecd18fd0d568904f9704b749525204ced05f3ef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463434"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>C Sharp kullanarak Azure Time Series Insights Gen2 ortamından veri sorgulama

Bu C# örneği, Azure Time Series Insights Gen2 ortamlarındaki [Gen2 veri erişimi API 'lerinden](/rest/api/time-series-insights/reference-data-access-overview) nasıl veri sorgulanacağını gösterir.

> [!TIP]
> Gen2 C# kod örneklerini şurada görüntüleyin: [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) .

## <a name="summary"></a>Özet

Aşağıdaki örnek kod aşağıdaki özellikleri göstermektedir:

* [Azure otomatik Rest](https://github.com/Azure/AutoRest)'ten SDK otomatik oluşturma desteği.
* [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)kullanarak Azure Active Directory aracılığıyla erişim belirteci alma.
* Bu alınan erişim belirtecini `Authorization` sonraki veri ERIŞIMI API isteklerinin üst bilgisinde geçirme.
* Örnek, HTTP isteklerinin aşağıdakilere nasıl yapıldığını gösteren bir konsol arabirimi sağlar:
  * [Gen2 ortamları API 'SI](/rest/api/time-series-insights/reference-environments-apis)
    * [Ortamların kullanılabilirlik API 'Sini al](/rest/api/time-series-insights/dataaccessgen2/query/getavailability) ve [olay şeması API 'si al](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [Gen2 sorgu API 'SI](/rest/api/time-series-insights/reference-query-apis)
    * [Olayları al API 'si](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), [seri API 'Si al](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)ve [toplama serisi API 'si al](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [Zaman serisi modeli API 'Leri](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [Hiyerarşilerin API](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) ve [hiyerarşileri al Batch API 'si](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [Türleri al API](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) ve [türler Batch API 'si](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [Örnekleri al API](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) ve [örnekleri Batch API 'si](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* Gelişmiş [arama](/rest/api/time-series-insights/reference-model-apis#search-features) ve [TSX](/rest/api/time-series-insights/reference-time-series-expression-syntax) özellikleri.

## <a name="prerequisites-and-setup"></a>Önkoşullar ve kurulum

Örnek kodu derleyip çalıştırmadan önce aşağıdaki adımları gerçekleştirin:

1. [Bir Gen2 Azure Time Series Insights ortamı sağlayın](./how-to-create-environment-using-portal.md) .
1. [Kimlik doğrulama ve yetkilendirme](time-series-insights-authentication-and-authorization.md)bölümünde açıklandığı gibi, Azure Active Directory için Azure Time Series Insights ortamınızı yapılandırın.
1. Azure Time Series Insights Gen2 istemci bağımlılıklarını oluşturmak için [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) , [README.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) içinde belirtilen şekilde çalıştırın.
1. Çözümü açın `TSIPreviewDataPlaneclient.sln` ve `DataPlaneClientSampleApp` Visual Studio 'da varsayılan proje olarak ayarlayın.
1. [Aşağıda](#project-dependencies) açıklanan adımları kullanarak gerekli proje bağımlılıklarını yükler ve örneği yürütülebilir bir `.exe` dosyaya derleyin.
1. `.exe`Dosyayı çift tıklayarak çalıştırın.

## <a name="project-dependencies"></a>Proje bağımlılıkları

Visual Studio 'nun en yeni sürümünü kullanmanız önerilir:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -sürüm 16.4.2 +

Örnek kodun, [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) dosyasında görüntülenebilen birkaç zorunlu bağımlılığı vardır.

**Build**  >  **Build Solution** seçeneğini seçerek paketleri Visual Studio 2019 ' de indirin.

Alternatif olarak, [NuGet 2.12 +](https://www.nuget.org/)kullanarak her bir paketi ekleyin. Örnek:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# örnek kodu

C# örnek koduna erişmek için lütfen [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) depoya bakın.

> [!NOTE]
>
> * Kod örneği, varsayılan ortam değişkenlerini değiştirmeksizin yürütülebilir.
> * Kod örneği bir .NET yürütülebilir konsol uygulamasına Derlenecek.

## <a name="next-steps"></a>Sonraki adımlar

* Sorgulama hakkında daha fazla bilgi edinmek için [sorgu API 'si başvurusunu](/rest/api/time-series-insights/reference-query-apis)okuyun.

* Azure Time Series Insights için [istemci SDK 'sını kullanarak bir JavaScript uygulamasının nasıl bağlanacağını](https://github.com/microsoft/tsiclient) okuyun.