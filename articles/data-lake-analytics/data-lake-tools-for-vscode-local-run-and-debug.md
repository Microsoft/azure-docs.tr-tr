---
title: U-SQL işlerinin hatalarını ayıklama-Visual Studio için Azure Data Lake Araçları kodu
description: U-SQL işlerini yerel olarak çalıştırmak ve hata ayıklamak için Visual Studio için Azure Data Lake Araçları kodu kullanmayı öğrenin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: e51b5640163546c673a1b0f61da47ccd992f27ad
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030043"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>U-SQL çalıştırma ve Visual Studio Code yerel olarak hata ayıklama
Bu makalede, erken kodlama aşamalarını hızlandırmak veya Visual Studio Code yerel olarak kodda hata ayıklamak için U-SQL işlerinin yerel bir geliştirme makinesinde nasıl çalıştırılacağı açıklanmaktadır. Visual Studio Code Azure Data Lake aracı hakkında yönergeler için bkz. [Visual Studio için Azure Data Lake araçları kodu kullanma](data-lake-analytics-data-lake-tools-for-vscode.md).

Yalnızca Visual Studio için Azure Data Lake Araçları Windows yüklemeleri U-SQL ' i yerel olarak çalıştırmaya yönelik eylemi destekler ve U-SQL ' i yerel olarak ayıklayın. MacOS ve Linux tabanlı işletim sistemlerine yönelik yüklemeler bu özelliği desteklemez.

## <a name="set-up-the-u-sql-local-run-environment"></a>U-SQL yerel çalıştırma ortamını ayarlama

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin ve ardından **adl: Yerel çalıştırma paketini indir** ' i girerek paketleri indirin.  

   ![ADL LocalRun bağımlılık paketlerini indirin](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. **Çıkış** bölmesinde gösterilen yoldan bağımlılık paketlerini bulun ve ardından buildtools ve Win10SDK 10240 ' yi yükleyip. Örnek bir yol aşağıda verilmiştir:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Bağımlılık paketlerini bulma](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2,1 **Buildtools**'u yüklemek Için LocalRunDependency klasöründeki visualcppbuildtools_full. exe ' ye tıklayın ve ardından sihirbaz yönergelerini izleyin.   

    ![BuildTools 'ı yükler](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2,2 **Win10SDK 10240**yüklemek Için, LocalRunDependency/win10sdk_ 10.0.10240 _2 klasöründeki sdksetup. exe ' ye tıklayın ve ardından sihirbaz yönergelerini izleyin.  

    ![Win10SDK 10240 'yi yükler](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Ortam değişkenini ayarlayın. **SCOPE_CPP_SDK** ortam değişkenini şu şekilde ayarlayın:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Yerel çalıştırma hizmetini başlatın ve U-SQL işini bir yerel hesaba gönder 
İlk kez Kullanıcı için, [U-SQL yerel çalıştırma ortamı ayarlanmamışsa](#set-up-the-u-sql-local-run-environment)yerel çalıştırma paketlerini Indirmek Için **adl: Yerel çalıştırma paketini** İndir ' i kullanın.

1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin ve ardından **adl: Yerel çalıştırma hizmetini Başlat**' ı girin.   
2. Microsoft yazılımı lisans koşulları 'nı ilk kez kabul etmek için **kabul et** ' i seçin. 

   ![Microsoft yazılımı lisans koşulları 'nı kabul edin](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Cmd konsolu açılır. İlk kullanıcılar için, **3**girmeniz ve ardından veri giriş ve çıkış için yerel klasör yolunu bulmanız gerekir. Yol ters eğik çizgi ile tanımlama başarısız olduysa, eğik çizgileri yeniden deneyin. Diğer seçenekler için varsayılan değerleri kullanabilirsiniz.

   ![Visual Studio Code yerel çalıştırma cmd için Data Lake araçları](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. CTRL + SHIFT + P ' yi seçerek komut paletini açın, **adl: gönder işini**girin ve ardından **Yerel** ' i seçerek işi yerel hesabınıza gönderebilirsiniz.

   ![Visual Studio Code Data Lake Araçlar yerel seçin](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. İşi gönderdikten sonra, gönderim ayrıntılarını görüntüleyebilirsiniz. Gönderim ayrıntılarını görüntülemek için **Çıkış** penceresinde **joburl** ' yi seçin. İş gönderme durumunu cmd konsolundan da görüntüleyebilirsiniz. Daha fazla iş ayrıntısı hakkında bilgi edinmek istiyorsanız cmd konsoluna **7** girin.

   Visual Studio Code yerel çalıştırma çıkışı için ![Data Lake araçları @ no__t-1 @ no__t-2Data Lake araçları Visual Studio Code yerel çalıştırma cmd durumu @ no__t-3 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>U-SQL işi için yerel bir hata ayıklama başlatma  
İlk kez Kullanıcı için:

1.  [U-SQL yerel çalıştırma ortamı ayarlanmamışsa](#set-up-the-u-sql-local-run-environment)yerel çalıştırma paketlerini Indirmek Için **adl: Yerel çalıştırma paketini indir** ' i kullanın.
2. Yüklü değilse, ileti kutusuna önerildiği gibi .NET Core SDK 2,0 ' i yükleme.
 
   @ no__t-1anımsatıcı DotNet @ no__t-2 değerini yüklüyor
3. Yüklü C# değilse ileti kutusunda önerilen Visual Studio Code için yükleme yapın. Devam etmek için **yüklensin** ' e tıklayın ve ardından vscode 'u yeniden başlatın.

    ![Yüklenecek anımsatıcıC#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Yerel hata ayıklamayı gerçekleştirmek için aşağıdaki adımları izleyin:
  
1. Komut paletini açmak için CTRL + SHIFT + P ' yi seçin ve ardından **adl: Yerel çalıştırma hizmetini Başlat**' ı girin. Cmd konsolu açılır. **Dataroot** 'ın ayarlandığından emin olun.
2. Arka plan C# kodunuzda bir kesme noktası ayarlayın.
3. Betik düzenleyicisine geri döndüğünüzde, sağ tıklayın ve **adl: yerel hata ayıkla**' yı seçin.
    
   ![Visual Studio Code yerel hata ayıklama sonucu için Data Lake araçları](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Sonraki adımlar
* [Visual Studio Code için Azure Data Lake Araçları’nı kullanma](data-lake-analytics-data-lake-tools-for-vscode.md)
* [VSCode 'da Azure Data Lake Analytics için Python, R ve CSharp ile U-SQL geliştirme](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [PowerShell kullanarak Data Lake Analytics kullanmaya başlama](data-lake-analytics-get-started-powershell.md)
* [Azure portal kullanarak Data Lake Analytics kullanmaya başlama](data-lake-analytics-get-started-portal.md)
* [Visual Studio için Data Lake araçları 'nı kullanarak U-SQL uygulamaları geliştirme](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics (U-SQL) kataloğunu kullanma](data-lake-analytics-use-u-sql-catalog.md)
