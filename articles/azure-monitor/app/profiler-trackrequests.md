---
title: İstekleri Azure Application Insights izlemek için kod yazma | Microsoft Docs
description: İsteklerinize yönelik profilleri alabilmeniz için Application Insights olan istekleri izlemek için kod yazın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3f449c98ed44f13fb6b3849ef2457cd8fbd916de
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900006"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Application Insights ile istekleri izlemek için kod yazma

Performans sayfasında uygulamanızın profillerini görüntülemek için, Azure Application Insights uygulamanızın isteklerini izlemesi gerekir. Application Insights, zaten işaretlenmiş çerçeveler üzerinde oluşturulmuş uygulamalar için istekleri otomatik olarak izleyebilir. İki örnek ASP.NET ve ASP.NET Core. 

Azure Cloud Services çalışan rolleri ve durum bilgisiz API 'Leri Service Fabric gibi diğer uygulamalar için, isteklerinizin başlayacağı ve sona erdirmek Application Insights bildirmek için kod yazmanız gerekir. Bu kodu yazdıktan sonra, Application Insights telemetri gönderilir. Telemetri ' i performans sayfasında görüntüleyebilirsiniz ve bu istekler için profiller toplanır. 

İstekleri el ile izlemek için aşağıdakileri yapın:

  1. Uygulama ömrünün başlarında, aşağıdaki kodu ekleyin:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Bu genel izleme anahtarı yapılandırması hakkında daha fazla bilgi için bkz. [Application Insights ile Service Fabric kullanma](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. İşaretlemek istediğiniz herhangi bir kod parçası için, aşağıdaki örnekte gösterildiği gibi, etrafında bir using ifadesini **kullanarak** bir `StartOperation<RequestTelemetry>` ekleyin:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Başka bir `StartOperation<RequestTelemetry>` kapsamı içinde `StartOperation<RequestTelemetry>` çağrılması desteklenmez. Bunun yerine iç içe kapsamdaki `StartOperation<DependencyTelemetry>` kullanabilirsiniz. Örnek:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
