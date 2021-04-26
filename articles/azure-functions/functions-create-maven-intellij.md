---
title: IntelliJ kullanarak Azure Işlevleri 'nde Java işlevi oluşturma
description: IntelliJ kullanarak, daha sonra Azure 'da sunucusuz bir ortamda çalıştırmak üzere yayımladığınız basit bir HTTP ile tetiklenen Java işlevi oluşturma hakkında bilgi edinin.
author: yucwan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: yucwan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 45fb62b446e6b589dc0cb9287a8aebe7f4e699b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543677"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>IntelliJ kullanarak Azure 'da ilk Java işlevinizi oluşturma

Bu makale:
- IntelliJ fıkır projesinde HTTP ile tetiklenen Java işlevi oluşturma.
- Kendi bilgisayarınızdaki tümleşik geliştirme ortamında (IDE) projeyi test etme ve hata ayıklama adımları.
- İşlev projesini Azure Işlevlerine dağıtmaya yönelik yönergeler

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

IntelliJ kullanarak Azure 'da Java işlevleri oluşturup yayımlamak için aşağıdaki yazılımı yüklemelisiniz:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Java 8 için [desteklenen bir Azure Java Geliştirme Seti (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support)
+ Yüklü [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition veya Community Edition
+ [Maven 3.5.0 +](https://maven.apache.org/download.cgi)
+ En son [Işlev çekirdek araçları](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Yükleme ve oturum açma

1. IntelliJ IDEA'nın Ayarlar/Tercihler iletişim kutusunda (Ctrl+Alt+S) **Eklentiler**’i seçin. Sonra **Marketplace**’de **Azure Toolkit for IntelliJ**’yi bulun ve **Yükle**’ye tıklayın. Yüklendikten sonra **Yeniden Başlat**’a tıklayarak eklentiyi etkinleştirin. 

    ![Marketplace’de Azure Toolkit for IntelliJ eklentisi][marketplace]

2. Azure hesabınızda oturum açmak için kenar çubuğunda **Azure Gezgini**’ni açın ve sonra üstteki çubukta **Azure Oturum Açma** simgesine (veya IDEA menüsünden **Araçlar/Azure/Azure Oturum Açma**) tıklayın.
    ![IntelliJ Azure Oturum Açma komutu][intellij-azure-login]

3. **Azure Oturum Açma** penceresinde **Cihaz Oturumu**’nu seçin ve ardından **Oturum aç**’a tıklayın ([diğer oturum açma seçenekleri](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Cihaz oturumu’nun seçili olduğu Azure Oturum Açma penceresi][intellij-azure-popup]

4. **Azure Cihaz Oturum Açma** iletişim kutusunda **Kopyala ve Aç**’a tıklayın.

   ![Azure Oturum Açma İletişim Kutusu penceresi][intellij-azure-copycode]

5. Tarayıcıda cihaz kodunuzu yapıştırın (son adımda **Kopyala ve Aç**’a tıkladığınızda kopyalanmış olan kod) ve ardından **İleri**’ye tıklayın.

   ![Cihaz oturumu tarayıcısı][intellij-azure-link-ms-account]

6. **Abonelik Seç** iletişim kutusunda, kullanmak istediğiniz abonelikleri seçin ve ardından **Seç**' e tıklayın.

   ![Abonelik Seç iletişim kutusu][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Yerel projenizi oluşturma

Bu bölümde, yerel bir Azure Işlevleri projesi oluşturmak için Azure Toolkit for IntelliJ kullanırsınız. Bu makalenin ilerleyen bölümlerinde işlev kodunu Azure'da yayımlayacaksınız. 

1. IntelliJ karşılama iletişim kutusunu açın, yeni *proje oluştur* ' u seçerek yeni bir proje Sihirbazı açın, *Azure işlevleri*' ni seçin.

    ![İşlev projesi oluştur](media/functions-create-first-java-intellij/create-functions-project.png)

1. *Http tetikleyicisi*' ni seçin ve ardından *İleri* ' ye tıklayın ve aşağıdaki sayfalardaki tüm yapılandırmalara gitmek için Sihirbazı izleyin; projenizin konumunu doğrulayın ve *son*' a tıklayın. Intellj fıkrı daha sonra yeni projenizi açacak.

    ![İşlev projesi oluşturma bitiş](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>Projeyi yerel olarak çalıştırma

1. `src/main/java/org/example/functions/HttpTriggerFunction.java`Oluşturulan kodu görmek için bölümüne gidin. *17*. satırın yanında, bir yeşil *çalışma* düğmesi olduğunu fark edeceksiniz ve *' Azure-Function-sınavu Çalıştır... '* seçeneğini belirlediğinizde, işlev uygulamanızın bir kaç günlükle yerel olarak çalıştığını görürsünüz.

    ![Yerel çalıştırma projesi](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Yerel proje çıkışını Çalıştır](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Yazdırılmış uç noktaya, gibi tarayıcıdan erişerek işlevi deneyebilirsiniz `http://localhost:7071/api/HttpTrigger-Java?name=Azure` .

    ![Yerel çalıştırma işlevi test sonucu](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Günlük Ayrıca fıkır olarak da yazdırılır, şimdi *Durdur* düğmesine tıklayarak işlev uygulamasını durdurun.

    ![Yerel çalıştırma işlevi test günlüğü](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>Projede yerel olarak hata ayıkla

1. Projenizdeki işlev kodunda yerel olarak hata ayıklamak için araç çubuğunda *Hata Ayıkla* düğmesini seçin. Araç çubuğunu görmüyorsanız, **görünümü görünüm**  >    >  **araç çubuğunu** seçerek etkinleştirin.

    ![Yerel hata ayıklama işlevi uygulama düğmesi](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Bir kesme noktası eklemek için dosyanın *20* . satırına tıklayın, `src/main/java/org/example/functions/HttpTriggerFunction.java` uç noktaya yeniden erişin, `http://localhost:7071/api/HttpTrigger-Java?name=Azure` kesme noktasının isabet olduğunu fark edersiniz. *adım*, *izleme*, *değerlendirme* gibi daha fazla hata ayıklama özelliğini deneyebilirsiniz. Durdur düğmesine tıklayarak hata ayıklama oturumunu durdurun.

    ![Yerel hata ayıklama işlevi uygulama kesmesi](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>Projenizi Azure 'a dağıtın

1. IntelliJ Proje Gezgini ' nde projenize sağ tıklayın, Azure *-> dağıtım işlevleri* ' ni seçin.

    ![Projeyi Azure 'a dağıtma](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Henüz İşlev Uygulaması yoksa, *+* *işlev* satırına tıklayın. İşlev uygulaması adını yazın ve uygun platformu seçin, burada yalnızca varsayılanı kabul edebiliyoruz. *Tamam* ' a tıklayın ve az önce oluşturduğunuz yeni işlev uygulaması otomatik olarak seçilir. İşlevlerinizi dağıtmak için *Çalıştır* ' a tıklayın.

    ![Azure 'da işlev uygulaması oluşturma](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

    ![İşlev uygulamasını Azure günlüğüne dağıtma](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>İşlev uygulamalarının FIKRINI yönetme

1. İşlevinizde *Azure Explorer* ile işlev uygulamalarınızı yönetebilirsiniz. *işlev uygulaması*' ye tıkladığınızda, tüm işlev uygulamalarınızı buradan görürsünüz.

    ![Gezgin 'de işlev uygulamalarını görüntüleme](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. İşlev uygulamalarınızdan birini seçin ve sağ tıklayın, *özellikleri göster* ' i seçerek ayrıntı sayfasını açın. 

    ![İşlev uygulaması özelliklerini göster](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. *Httptrigger-Java* işlev uygulamanıza sağ tıklayın ve *tetikleyici işlevi*' ni SEÇIN, tarayıcının tetikleyici URL 'si ile açıldığını görürsünüz.

    ![Ekran görüntüsü U R L ile bir tarayıcı gösterir.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Projeye daha fazla işlev ekleyin

1. *Org. example. Functions* paketine sağ tıklayın ve *New-> Azure Function Class*' ı seçin. 

    ![Proje girişine işlevler ekleyin](media/functions-create-first-java-intellij/add-functions-entry.png)

1. *Httptest* sınıf adı ' nı doldurup *httptrigger* ' ı seçin işlev sınıfı oluşturma sihirbazında, oluşturmak için *Tamam* ' a tıklayın, bu şekilde, istediğiniz gibi yeni işlevler oluşturabilirsiniz.

    ![Ekran görüntüsü Işlev sınıfı oluştur iletişim kutusunu gösterir.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Proje çıkışına işlevler ekleyin](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>İşlevleri Temizleme

1. Azure Explorer 'da işlevleri silme
      
      ![Ekran görüntüsü bağlam menüsünden Seçileni Sil ' i gösterir.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen bir işlevle bir Java projesi oluşturdunuz, yerel makinenizde çalıştırın ve Azure 'a dağıttınız. Şimdi, işlevinizi genişletin...

> [!div class="nextstepaction"]
> [Azure depolama kuyruğu çıkış bağlaması ekleme](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
