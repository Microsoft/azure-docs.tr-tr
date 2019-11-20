---
title: Java ve IntelliJ ile bir Azure işlevi oluşturma | Microsoft Docs
description: Java ve IntelliJ ile Azure 'da basit bir HTTP ile tetiklenen, sunucusuz bir uygulama oluşturmayı ve yayımlamayı öğrenin.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure işlevleri, işlevler, olay işleme, işlem, sunucusuz mimari, Java
ms.service: azure-functions
ms.topic: conceptual
ms.devlang: java
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 5aa0763206289305e98960b6e03ad903eb4547ae
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70744028"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Java ve IntelliJ ile ilk Azure işlevinizi oluşturma

Bu makalede şu gösterilir:
- IntelliJ ıDEA ve Apache Maven ile [sunucusuz](https://azure.microsoft.com/overview/serverless-computing/) bir işlev projesi oluşturma
- Kendi bilgisayarınızdaki tümleşik geliştirme ortamında (IDE) işlevi test etme ve hata ayıklama adımları
- İşlev projesini Azure Işlevlerine dağıtmaya yönelik yönergeler

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

Java ve IntelliJ ile bir işlev geliştirmek için aşağıdaki yazılımı yüklemelisiniz:

- [Java geliştirici seti](https://www.azul.com/downloads/zulu/) (JDK), sürüm 8
- [Apache Maven](https://maven.apache.org), sürüm 3,0 veya üzeri
- [IntelliJ fikir](https://www.jetbrains.com/idea/download), Maven ile topluluk veya Ultimate sürümleri
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Bu makaledeki adımları tamamlayabilmeniz için JAVA_HOME ortam değişkeni JDK 'nin Install konumuna ayarlanmalıdır.

 [Azure Functions Core Tools, sürüm 2 ' yi](functions-run-local.md#v2)yüklemenizi öneririz. Azure Işlevleri yazmak, çalıştırmak ve hata ayıklamak için yerel bir geliştirme ortamı sağlar.

## <a name="create-a-functions-project"></a>Işlevler projesi oluşturma

1. IntelliJ fıkır bölümünde **Yeni proje oluştur**' u seçin.  
1. **Yeni proje** penceresinde sol bölmeden **Maven** ' ı seçin.
1. Arşiv **'Ten oluştur** onay kutusunu seçin ve ardından [Azure-Functions-arşiv ETYPE](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)Için, arşiv **ETYPE Ekle** ' yi seçin.
1. Arşiv **Ekle** penceresinde, alanları aşağıdaki gibi doldurun:
    - _GroupID_: com. Microsoft. Azure
    - _ArtifactId_: Azure-Functions-arşiv Etype
    - _Sürüm_: [Merkezi depodan](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
     en![son sürüm 1,22 ' u kullanarak, IntelliJ ıdeda bir Maven projesi oluşturun.](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. **Tamam**' ı ve ardından **İleri**' yi seçin.
1. Geçerli proje için ayrıntılarınızı girin ve **son**' u seçin.

Maven, proje dosyalarını _ArtifactId_ değeriyle aynı ada sahip yeni bir klasörde oluşturur. Projenin oluşturulan kodu, tetikleme HTTP isteğinin gövdesini yansıtan basit bir [http ile tetiklenen](/azure/azure-functions/functions-bindings-http-webhook) bir işlevdir.

## <a name="run-functions-locally-in-the-ide"></a>İşlevleri IDE 'de yerel olarak çalıştırma

> [!NOTE]
> İşlevleri yerel olarak çalıştırmak ve hatalarını ayıklamak için [Azure Functions Core Tools, sürüm 2 ' yi](functions-run-local.md#v2)yüklediğinizden emin olun.

1. Değişiklikleri el ile içeri aktarın veya [otomatik içeri aktarmayı](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)etkinleştirin.
1. **Maven projeleri** araç çubuğunu açın.
1. **Yaşam döngüsü**' ni genişletin ve ardından **paketi**açın. Çözüm yeni oluşturulan bir hedef dizinde oluşturulup paketlenmiştir.
1. **Eklentiler** > **Azure-Functions** ' i genişletin ve Azure işlevleri 'ni açın. Azure işlevleri yerel çalışma zamanına başlamak için **çalıştırın** .  
  ![Azure Işlevleri için Maven araç çubuğu](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. İşlevinizi test etmeyi bitirdiğinizde Çalıştır iletişim kutusunu kapatın. Tek seferde yalnızca bir işlev Konağı etkin ve yerel olarak çalıştırılabilir.

## <a name="debug-the-function-in-intellij"></a>IntelliJ 'de işlevde hata ayıkla

1. İşlev konağını hata ayıklama modunda başlatmak için, işlevinizi çalıştırdığınızda bağımsız değişken olarak ' **denabledebug** ' ekleyin. [Maven hedeflerde](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) yapılandırmayı değiştirebilir veya bir Terminal penceresinde aşağıdaki komutu çalıştırabilirsiniz:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Bu komut, işlev ana bilgisayarının 5005 adresinden bir hata ayıklama bağlantı noktası açmasına neden olur.

1. **Çalıştır** menüsünde, **konfigürasyonları Düzenle**' yi seçin.
1. **Uzaktan**eklemek için **(+)** seçeneğini belirleyin.
1. _Ad_ ve _Ayarlar_ alanlarını doldurun ve ardından yapılandırmayı kaydetmek için **Tamam** ' ı seçin.
1. Kurulumdan sonra, hata ayıklamayı başlatmak için **< uzak yapılandırma adı > hata ayıkla** ' yı seçin veya klavyenizde SHIFT + F9 tuşlarına basın.

   ![IntelliJ 'de hata ayıklama işlevleri](media/functions-create-first-java-intellij/debug-configuration-intellij.png)

1. İşiniz bittiğinde, hata ayıklayıcıyı ve çalışan işlemi durdurun. Tek seferde yalnızca bir işlev Konağı etkin ve yerel olarak çalıştırılabilir.

## <a name="deploy-the-function-to-azure"></a>İşlevi Azure’a dağıtma

1. İşlevinizi Azure 'a dağıtabilmeniz için önce [Azure CLI 'yi kullanarak oturum](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)açmalısınız.

   ``` azurecli
   az login
   ```

1. `azure-functions:deploy` Maven hedefini kullanarak kodunuzu yeni bir işleve dağıtın. Ayrıca Maven projeleri penceresinde **Azure-Functions: dağıt** seçeneğini de belirleyebilirsiniz.

   ```
   mvn azure-functions:deploy
   ```

1. İşlev başarıyla dağıtıldıktan sonra, Azure CLı çıktısında işlevinizin URL 'sini bulun.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Sonraki adımlar

- Java işlevleri geliştirme hakkında daha fazla bilgi edinmek için [Java İşlevleri geliştirici kılavuzunu](functions-reference-java.md) gözden geçirin.
- `azure-functions:add` Maven hedefini kullanarak projenize farklı tetikleyicilerle ek işlevler ekleyin.
