---
title: Dijital TWINS modeli ayrıştırıcısı 'nı anlayın | Microsoft Docs
description: Bir geliştirici olarak, modelleri doğrulamak için DTDL ayrıştırıcısının nasıl kullanılacağını öğrenin.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d68abe8548dac3306228683e4b6ce8935a248ebc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92331796"
---
# <a name="understand-the-digital-twins-model-parser"></a>Dijital ikizler modeli ayrıştırıcısını anlama

Dijital TWINS tanım dili (DTDL) [Dtdl belirtiminde](https://github.com/Azure/opendigitaltwins-dtdl)açıklanmıştır. Kullanıcılar, birden çok dosyada tanımlanan bir modeli doğrulamak ve sorgulamak için _dijital TWINS modeli ayrıştırıcısı_ NuGet paketini kullanabilir.

## <a name="install-the-dtdl-model-parser"></a>DTDL model ayrıştırıcısı 'nı yükle

Ayrıştırıcının KIMLIĞI: [Microsoft. Azure. DigitalTwins. Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)ile NuGet.org ' de kullanılabilir. Ayrıştırıcıyı yüklemek için, Visual Studio veya CLI 'de olduğu gibi uyumlu herhangi bir NuGet paket yöneticisini kullanın `dotnet` .

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> Yazma sırasında, ayrıştırıcı sürümü ' dir `3.12.5` .

## <a name="use-the-parser-to-validate-a-model"></a>Bir modeli doğrulamak için ayrıştırıcısı kullanma

Bir model, JSON dosyalarında açıklanan bir veya daha fazla arabirimden oluşabilir. Ayrıştırıcıyı, belirli bir klasördeki tüm dosyaları yüklemek ve bu dosya arasındaki tüm başvuruları dahil tüm dosyaları bir bütün olarak doğrulamak üzere kullanmak için kullanabilirsiniz:

1. `IEnumerable<string>`Tüm model içeriklerinin listesini içeren bir oluştur:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. `ModelParser`Ve çağrısını oluşturun `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Doğrulama hatalarını kontrol edin. Ayrıştırıcı herhangi bir hata bulursa bir `ParsingException` hata listesi içeren bir oluşturur:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. ' Nı inceleyin `Model` . Doğrulama başarılı olursa modeli incelemek için model ayrıştırıcısı API 'sini kullanabilirsiniz. Aşağıdaki kod parçacığı, ayrıştırılabilen tüm modellerin nasıl yineleneceğini gösterir ve var olan özellikleri görüntüler:

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede gözden geçirilen model ayrıştırıcısı API 'SI, DTDL modellerine bağlı olan görevleri otomatik hale getirmek veya doğrulamak için birçok senaryo sağlar. Örneğin, modeldeki bilgilerden dinamik olarak bir kullanıcı arabirimi oluşturabilirsiniz.
