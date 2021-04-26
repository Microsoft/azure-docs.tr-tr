---
title: DTDL modellerini yazmak ve doğrulamak için bir araç kullanma | Microsoft Docs
description: Visual Studio Code veya Visual Studio 2019 için DTDL düzenleyicisini yükleyip IoT Tak ve Kullan modellerini yazmak için kullanın.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 62a89fbb654624e1b1f9242d21d3bbb188140157
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067495"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>DTDL yazma araçlarını yükle ve kullan

[Dijital TWINS tanım dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) modelleri JSON dosyalarıdır. Bu model dosyalarını yazmak ve doğrulamak için Visual Studio Code veya Visual Studio 2019 için bir uzantı kullanabilirsiniz.

## <a name="install-and-use-the-vs-code-extension"></a>VS Code uzantısını yükleyip kullanma

VS Code için DTDL uzantısı aşağıdaki DTDL yazma özelliklerini ekler:

- DTDL v2 sözdizimi doğrulaması.
- Dil söz dizimine yardımcı olmak için otomatik tamamlama dahil IntelliSense.
- Komut paletinden arabirim oluşturma özelliği.

DTDL uzantısını yüklemek için, [Visual Studio Code Için dtdl Düzenleyicisi](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)' ne gidin. Ayrıca, VS Code uzantılar görünümünde **Dtdl** araması yapabilirsiniz.

Uzantıyı yüklediğinizde, VS Code 'da DTDL model dosyalarını yazmanıza yardımcı olması için bunu kullanın:

- Uzantı, DTDL model dosyalarında sözdizimi doğrulaması sağlar ve aşağıdaki ekran görüntüsünde gösterildiği gibi hataları vurgular:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="VS Code 'de model doğrulaması":::

- DTDL modellerini düzenlediğinizde IntelliSense ve AutoComplete kullanın:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="VS Code 'de DTDL modelleri için IntelliSense kullanın":::

- Yeni bir DTDL arabirimi oluşturun. **Dtdl: Create Interface** komutu, yeni bir ARABIRIMLE bir JSON dosyası oluşturur. Arabirim örnek telemetri, özellik ve komut tanımları içerir.

## <a name="install-and-use-the-visual-studio-extension"></a>Visual Studio uzantısını yükleyip kullanma

Visual Studio 2019 için DTDL uzantısı aşağıdaki DTDL yazma özelliklerini ekler:

- DTDL v2 sözdizimi doğrulaması.
- Dil söz dizimine yardımcı olmak için otomatik tamamlama dahil IntelliSense.

DTDL uzantısını yüklemek için, [VS 2019 Için Dtdl dil desteği](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport)' ne gidin. Ayrıca, Visual Studio 'da **Uzantıları Yönet** bölümünde **dtdl** araması yapabilirsiniz.

Uzantıyı yüklediğinizde, Visual Studio 'da DTDL model dosyalarını yazmanıza yardımcı olması için kullanın:

- Uzantı, DTDL model dosyalarında sözdizimi doğrulaması sağlar ve aşağıdaki ekran görüntüsünde gösterildiği gibi hataları vurgular:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Visual Studio 'da model doğrulaması":::

- DTDL modellerini düzenlediğinizde IntelliSense ve AutoComplete kullanın:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Visual Studio 'da DTDL modelleri için IntelliSense kullanma":::

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır makalesinde, DTDL model dosyalarını yazmak ve doğrulamak için Visual Studio Code ve Visual Studio 2019 için DTDL uzantılarını nasıl kullanacağınızı öğrendiniz. Önerilen bir sonraki adım, [Azure IoT Explorer 'ı modelleriniz ve cihazlarınızla](./howto-use-iot-explorer.md)nasıl kullanacağınızı öğrenirsiniz.
