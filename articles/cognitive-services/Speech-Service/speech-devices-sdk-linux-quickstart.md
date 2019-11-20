---
title: "Hızlı başlangıç: Linux konuşma hizmetinde konuşma cihazları SDK 'sını çalıştırma"
titleSuffix: Azure Cognitive Services
description: Bir Linux konuşma cihazları SDK 'Sı ile çalışmaya başlama önkoşulları ve yönergeleri.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: erhopf
ms.openlocfilehash: 18b96e9afbf2a83b917d6848b419fb76518035de
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090437"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-linux"></a>Hızlı başlangıç: Linux 'ta konuşma cihazları SDK örnek uygulamasını çalıştırma

Bu hızlı başlangıçta, konuşma özellikli bir ürün oluşturmak veya bir konuşma [dökümü](conversation-transcription-service.md) cihazı olarak kullanmak için Linux Için konuşma cihazları SDK 'sını nasıl kullanacağınızı öğreneceksiniz. Şu anda yalnızca [Azure Kinect dk](https://azure.microsoft.com/services/kinect-dk/) destekleniyor.

Uygulama, konuşma SDK paketiyle oluşturulmuştur ve 64 bitlik Linux (Ubuntu 16,04, Ubuntu 18,04, detem 9) üzerinde Java IDE tutulma (v4). 64 bit Java 8 çalışma zamanı ortamında (JRE) çalışır.

Bu kılavuzda, bir konuşma Hizmetleri kaynağına sahip bir Azure bilişsel [Hizmetler](get-started.md) hesabı gerekir. Bir hesabınız yoksa, abonelik anahtarı almak için [ücretsiz deneme sürümünü](https://azure.microsoft.com/try/cognitive-services/) kullanabilirsiniz.

[Örnek uygulamanın](https://aka.ms/sdsdk-download-JRE) kaynak kodu, konuşma cihazları SDK 'sına dahildir. Ayrıca [github'da](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıç şunları gerektirir:

* İşletim sistemi: 64-bit Linux (Ubuntu 16,04, Ubuntu 18,04, detem 9)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Tutulma Java IDE](https://www.eclipse.org/downloads/)
* Yalnızca [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) veya [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* Konuşma hizmeti için bir Azure abonelik anahtarı. [Ücretsiz bir tane alın](get-started.md).
* Java için [konuşma cihazları SDK 'sının](https://aka.ms/sdsdk-download-JRE) en son sürümünü indirin ve. zip dosyasını çalışma dizininize ayıklayın.
   > [!NOTE]
   > JRE-Sample-Release. zip dosyası JRE örnek uygulamasını içerir ve bu hızlı başlangıçta uygulamanın/home/wcaltest/JRE-Sample-Release 'e ayıklanacağı varsayılmaktadır

Çakışan Küreler başlatılmadan önce bu bağımlılıkların yüklendiğinden emin olun.

* Ubuntu 'da:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* On yıl 9 ' da:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Konuşma dökümü şu anda yalnızca "en-US" ve "zh-CN" için kullanılabilir ve "merkezileştirme" ve "eastaya" bölgelerinde mevcuttur. Konuşma dökümünü kullanmak için bu bölgelerden birinde bir konuşma anahtarınız olmalıdır.

Hedefleri kullanmayı planlıyorsanız, bir [Language Understanding hizmeti (LUA)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) aboneliğine ihtiyacınız olacaktır. Lua ve amaç tanıma hakkında daha fazla bilgi edinmek için bkz. [lusıs C#Ile konuşma amaçlarını tanıma ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Bu uygulama için [Örnek BIR lusıs modeli](https://aka.ms/sdsdk-luis) vardır.

## <a name="create-and-configure-the-project"></a>Projeyi oluşturma ve yapılandırma

1. Eclipse’i başlatın.

1. **Çakışan Küreler IDE başlatıcısı**' nda, **çalışma alanı** alanına yeni bir çalışma alanı dizininin adını girin. Ardından **Başlat**’ı seçin.

   ![Eclipse Başlatıcısı ekran görüntüsü](media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Çok geçmeden Eclipse IDE ana penceresi görüntülenir. Varsa, Hoş Geldiniz ekranını kapatın.

1. Çakışan Küreler menü çubuğunda **dosya** > **Yeni** > **Java projesi**' ni seçerek yeni bir proje oluşturun. Kullanılabilir değilse **Proje** ' yi ve ardından **Java projesi**' ni seçin.

1. **Yeni Java proje** Sihirbazı başlatılır. Örnek projenin konumuna **gözatın** . **Son**’u seçin.

   ![Yeni Java Projesi sihirbazının ekran görüntüsü](media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. **Paket Gezgini**' nde projenize sağ tıklayın. Bağlam menüsünden **Yapılandır** > **Maven Projesine Dönüştür**’ü seçin. **Son**’u seçin.

   ![Paket gezgininin ekran görüntüsü](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Pod. xml dosyasını açın ve düzenleyin.

    Dosyanın sonunda, kapatma etiketiyle `</project>`önce, burada gösterildiği gibi `repositories` ve `dependencies` öğeleri oluşturun ve `version` geçerli sürümünüzle eşleştiğinden emin olun:
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.7.0</version>
        </dependency>
    </dependencies>
   ```

1. **Paket Gezgini**' nde projenize sağ tıklayın. **Özellikler**' i seçin, sonra yeni > **hata ayıklama ayarları** ' nı çalıştırın **...****Java uygulaması** > . 

1. **Yapılandırma düzenleme** penceresi görüntülenir. **Ad** alanına **Main**girin ve **com. Microsoft. biliveservices. Speech. Samples. functionslist**öğesini bulmak ve seçmek için **ana sınıf** için **aramayı** kullanın.

   ![Düzenleme başlatma yapılandırması ekran görüntüsü](media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. **Linux ARM** veya **Linux-x64**sürümünden hedef mimariniz için ses ikili dosyalarını, EG **/Home/wcaltest/JRE-Sample-Release** konumuna kopyalayın

1. Ayrıca **yapılandırma düzenleme** penceresinde ortam sayfasını ve **Yeni**' **yi** seçin. **Yeni ortam değişkeni** penceresi görüntülenir. **Ad** alanına **LD_LIBRARY_PATH** girin ve **değer** alanına *. so dosyalarını içeren klasörü girin, örneğin **/Home/wcaltest/JRE-Sample-Release**

1. `kws.table` ve `participants.properties` proje klasörü **hedefine/sınıflarına** Kopyala


## <a name="configure-the-sample-application"></a>Örnek uygulamayı yapılandırma

1. Konuşma abonelik anahtarınızı kaynak koda ekleyin. Amaç tanıma denemek istiyorsanız, ayrıca ekleyin, [Language Understanding hizmeti](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) abonelik anahtarı ve uygulama kimliği

   Konuşma ve LUSıS için, bilgileriniz `FunctionsList.java`'ye gider:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Konuşma dökümünü kullanıyorsanız, konuşma anahtarınız ve bölge bilgileriniz de `Cts.java`de gereklidir:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Default anahtar sözcüğü (anahtar sözcüğü) "bilgisayar" dır. "Makine" veya "yardımcı" gibi, belirtilen diğer anahtar sözcüklerden birini de deneyebilirsiniz. Bu alternatif anahtar sözcüklerin kaynak dosyaları, anahtar sözcük klasöründe konuşma cihazları SDK 'snda bulunur. Örneğin, `/home/wcaltest/JRE-Sample-Release/keyword/Computer` "bilgisayar" anahtar sözcüğü için kullanılan dosyaları içerir.

   > [!TIP]
   > Ayrıca, [özel bir anahtar sözcük oluşturabilirsiniz](speech-devices-sdk-create-kws.md).

    Yeni bir anahtar sözcük kullanmak için `FunctionsList.java`aşağıdaki satırı güncelleştirin ve anahtar sözcüğünü uygulamanıza kopyalayın. Örneğin, anahtar sözcük paketinden ' MACHINE ' anahtar sözcüğünü kullanmak için `machine.zip`:

   * `kws.table` dosyasını ZIP paketinden proje klasörü **hedefi/sınıfları**içine kopyalayın.

   * `FunctionsList.java` anahtar sözcük adıyla güncelleştirin:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Örnek uygulamayı tutulma 'dan çalıştırma

1. Çakışan Küreler menü çubuğundan > **Çalıştır** ' ı **çalıştırın** 

1. Konuşma cihaz SDK'sı örnek bir uygulama başlar ve şu seçeneklerini gösterir:

   ![Örnek konuşma cihaz SDK'sı örnek uygulama ve seçenekleri](media/speech-devices-sdk/java-sample-app-linux.png)

1. Yeni **konuşma dökümü** tanıtımı ' nı deneyin. **Oturum** > kullanmaya **başlayın**. Varsayılan olarak, herkes bir konudır. Ancak, katılımcının ses imzaları varsa proje klasörü **hedefi/sınıfları**içine `participants.properties` yerleştirebilirsiniz. Ses imzasını oluşturmak için, [konuşmalar (SDK)](how-to-use-conversation-transcription-service.md)konusuna bakın.

   ![Demo konuşma dökümü uygulaması](media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Uygulamayı tek başına oluşturma ve çalıştırma

1. **Paket Gezgini**' nde projenize sağ tıklayın. Seçin **dışarı**. 
1. **Dışarı aktarma** penceresi görüntülenir. **Java** ' yı genişletin ve **Runiçin jar dosyasını** seçin ve ardından **İleri**' yi seçin.

   ![Dışarı aktarma penceresinin ekran görüntüsü](media/speech-devices-sdk/eclipse-export-linux.png) 

1. **ÇALıŞTıRıLABILIR jar dosya dışarı aktarma** penceresi görüntülenir. Uygulama için bir **dışarı aktarma hedefi** seçin ve ardından **son**' u seçin.
 
   ![Runfor JAR dosya dışarı aktarma ekran görüntüsü](media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Lütfen uygulama tarafından bu dosyalar gerektiği için `kws.table` ve `participants.properties` yukarıda seçilen hedef klasöre koyun.

1. LD_LIBRARY_LIB *. so dosyalarını içeren klasöre ayarlayın

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Tek başına uygulamayı çalıştırmak için

     ```bash
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sürüm notlarını gözden geçirin](devices-sdk-release-notes.md)
