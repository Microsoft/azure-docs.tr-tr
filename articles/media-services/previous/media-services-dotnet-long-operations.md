---
title: Long-Running Işlemler yoklanıyor | Microsoft Docs
description: Azure Media Services, işlemleri başlatmak için Media Services istekleri gönderen API 'Ler sunar (örneğin, bir kanal oluşturma, başlatma, durdurma veya silme), bu işlemler uzun süredir çalışır. Bu konuda, uzun süreli işlemlerin nasıl yoklama yapılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7926f7aaa427d49d13cab5e13f5153bcd22e5898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013831"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Azure Media Services ile canlı akış sunma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Genel Bakış

Microsoft Azure Media Services, istekleri başlatma işlemlerine Media Services gönderen API 'Ler sunar (örneğin: Kanal oluşturma, başlatma, durdurma veya silme). Bu işlemler uzun süredir çalışıyor.

Media Services .NET SDK, isteği gönderen ve işlemin tamamlanmasını bekleyen API 'Ler sağlar (dahili olarak, API 'Ler bazı aralıklarda işlem ilerlemesini yoklamaktadır). Örneğin, kanalı çağırdığınızda. Start (), yöntem kanal başlatıldıktan sonra döndürülür. Zaman uyumsuz sürüm: await kanalını da kullanabilirsiniz. StartAsync () (görev tabanlı zaman uyumsuz model hakkında bilgi için bkz. [tap](./media-services-mes-schema.md)). İşlem isteği gönderen ve sonra işlem tamamlanana kadar durum yoklama yapan API 'Ler "yoklama yöntemleri" olarak adlandırılır. Bu Yöntemler (özellikle zaman uyumsuz sürüm) zengin istemci uygulamaları ve/veya durum bilgisi olmayan hizmetler için önerilir.

Uygulamanın uzun süre çalışan bir http isteğini bekleyemez ve işlem ilerlemesini el ile yoklamak isteyen senaryolar vardır. Tipik bir örnek, durum bilgisi olmayan bir Web hizmetiyle etkileşime geçen bir tarayıcı olabilir: tarayıcı bir kanal oluşturmak istediğinde, Web hizmeti uzun süre çalışan bir işlem başlatır ve işlem KIMLIĞINI tarayıcıya döndürür. Böylece tarayıcı, Web hizmetinden KIMLIĞE göre işlem durumunu almasını isteyebilir. Media Services .NET SDK, bu senaryo için yararlı olan API 'Ler sağlar. Bu API 'Ler "yoklama dışı Yöntemler" olarak adlandırılır.
"Yoklama dışı Yöntemler" Şu adlandırma düzenine sahiptir: gönderme *OperationName* işlemi (örneğin, SendCreateOperation). *OperationName* işlem yöntemlerini gönder **IOperation** nesnesini döndürür; döndürülen nesne, işlemi izlemek için kullanılabilecek bilgiler içerir. *OperationName* operationasync yöntemleri gönderme **görevi \<IOperation>**.

Şu anda aşağıdaki sınıflar yoklama dışı yöntemleri destekler:  **Channel**, **streamingendpoint** ve **Program**.

İşlem durumunu yoklamak için, **Operationbasecollection** sınıfında **GetOperation** yöntemini kullanın. İşlem durumunu denetlemek için aşağıdaki aralıkları kullanın: **Channel** ve **Streamingendpoint** işlemleri için 30 saniye kullanın; **Program** işlemleri için 10 saniye kullanın.

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

Geliştirme ortamınızı kurun ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun.

## <a name="example"></a>Örnek

Aşağıdaki örnek, **Channeloperations** adlı bir sınıfı tanımlar. Bu sınıf tanımı, Web hizmeti sınıf tanımınız için bir başlangıç noktası olabilir. Basitlik için aşağıdaki örneklerde yöntemlerin Async olmayan sürümleri kullanılır.

Örnek ayrıca istemcinin bu sınıfı nasıl kullandığını gösterir.

### <a name="channeloperations-class-definition"></a>ChannelOperations sınıf tanımı

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
{
    // Read values from the App.config file.
    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>İstemci kodu

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]