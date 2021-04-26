---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: eae828d03431dd339c5399d8db8c6e46141ab11b
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075363"
---
## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Visual Studio Code, Azure 'da yayımlamadan önce bu projeyi yerel geliştirme bilgisayarınızda çalıştırmanıza olanak sağlamak için [Azure Işlevleri temel araçları](../articles/azure-functions/functions-run-local.md) ile tümleşir.

1. İşlevinizi çağırmak için <kbd>F5</kbd> tuşuna basarak işlev uygulaması projesini başlatın. Temel Araçlar’daki çıktı, **Terminal** panelinde görüntülenir. Uygulamanız, **Terminal** panelinden başlar. HTTP ile tetiklenen işlevinizin URL uç noktasını yerel olarak çalışan bir şekilde görebilirsiniz.

    ![Yerel işlev VS Code çıkışı](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Windows üzerinde çalışırken sorun yaşıyorsanız, Visual Studio Code için varsayılan terminalin **WSL Bash** olarak ayarlandığından emin olun.

1. Temel araçlar çalışırken **Azure: Functions** alanına gidin. **İşlevler** altında **Yerel proje**  >  **işlevleri**' ni genişletin. İşlevi sağ tıklatın (Windows) veya <kbd>CTRL</kbd> + tıklatın (MacOS) `HttpExample` ve **Şimdi işlevi Çalıştır...** seçeneğini belirleyin.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="İşlevi şimdi Visual Studio Code Çalıştır":::
    
1. **İstek gövdesi gir** bölümünde istek iletisi gövdesi değerini görürsünüz `{ "name": "Azure" }` . Bu istek iletisini işlevinizde göndermek için ENTER tuşuna basın. 

   Bunun yerine, `http://localhost:7071/api/HttpExample` bir Web tarayıcısında adrese BIR http get isteği gönderirsiniz.

1. İşlev yerel olarak yürütüldüğünde ve bir yanıt döndürdüğünde Visual Studio Code bir bildirim tetiklenir. İşlev yürütme hakkında bilgi, **Terminal** panelinde gösterilir.

1. Temel araçları durdurmak ve hata ayıklayıcının bağlantısını kesmek için <kbd>CTRL + C</kbd> tuşlarına basın.
