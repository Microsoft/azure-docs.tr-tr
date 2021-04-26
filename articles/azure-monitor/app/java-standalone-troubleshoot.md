---
title: Java için Azure Izleyici Application Insights sorunlarını giderme
description: Azure Izleyici için Java aracısında sorun gidermeyi öğrenin Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9bcd0ead2516b040a5a5aee4a7fae042a5f678a2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449996"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Sorun giderme kılavuzu: Java için Azure Izleyici Application Insights

Bu makalede, Application Insights için Java aracısını kullanarak bir Java uygulamasını düzenleme sırasında karşılaşabileceğiniz yaygın sorunlardan bazılarını ele aldık. Ayrıca, bu sorunları çözmek için gereken adımları ele alınmaktadır. Application Insights, Azure Izleyici Platform hizmeti 'nin bir özelliğidir.

## <a name="check-the-self-diagnostic-log-file"></a>Kendi kendine tanılama günlük dosyasını denetleme

Varsayılan olarak, Application Insights için Java 3,0 Aracısı, `applicationinsights.log` dosyayı tutan aynı dizinde adlı bir günlük dosyası üretir `applicationinsights-agent-3.0.3.jar` .

Bu günlük dosyası, karşılaşmanız gerekebilecek herhangi bir sorunun ipuçlarını denetlemek için ilk yerdir.

## <a name="jvm-fails-to-start"></a>JVM başlatılamıyor

JVM "ZIP dosyası açma veya JAR bildirimi eksik" hatası ile başlayamazsa, dosya aktarımı sırasında bozulmuş olabileceğinden, aracı jar dosyasını yeniden indirmeyi deneyin.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Java 2. x SDK Application Insights yükseltme

Uygulamanızda Java 2. x SDK Application Insights zaten kullanıyorsanız, bunu kullanmaya devam edebilirsiniz. Java 3,0 Aracısı tarafından algılanır. Daha fazla bilgi için bkz. [Java 2. x SDK 'Dan yükseltme](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Application Insights Java 3,0 Preview sürümünden yükseltme

Java 3,0 Önizleme aracılarından yükseltiyorsanız, tüm [yapılandırma seçeneklerini](./java-standalone-config.md) dikkatle gözden geçirin. JSON yapısı, 3,0 genel kullanılabilirlik (GA) sürümünde tamamen değiştirilmiştir.

Bu değişiklikler şunları içerir:

-  Yapılandırma dosyası adı, `ApplicationInsights.json` olarak değiştirildi `applicationinsights.json` .
-  `instrumentationSettings`Düğüm artık yok. İçindeki tüm içerik `instrumentationSettings` kök düzeye taşınır. 
-  ,, Ve gibi yapılandırma düğümleri `sampling` `jmxMetrics` `instrumentation` `heartbeat` `preview` kök düzeye taşınır.

## <a name="some-logging-is-not-auto-collected"></a>Bazı günlük kaydı otomatik olarak toplanmadı

Günlüğe kaydetme yalnızca, günlük çerçevesi için yapılandırılan düzeyi karşılıyorsa yakalanır ve ikincisi de Application Insights için yapılandırılan düzeyi karşılar.

Örneğin, günlük oluşturma çatısı `WARN` paketten (ve üzeri) günlüğe kaydedilecek şekilde yapılandırıldıysa `com.example` ve Application Insights `INFO` , (ve üzerine) yakalanacak şekilde yapılandırıldıysa, Application Insights yalnızca paketten (ve üzeri) yakalama yapılır `WARN` `com.example` .

Belirli bir günlüğe kaydetme bildiriminin günlüğe kaydetme çerçevelerinin yapılandırılmış eşiğini karşılayıp karşılamadığını öğrenmenin en iyi yolu, normal uygulama günlüğinizde (ör. dosya veya konsol) görüntülendiğini doğrulamadır.

Ayrıca, günlükçü için bir özel durum nesnesi geçirildiğinde, günlük iletisi (ve özel durum nesnesi ayrıntıları) tablo yerine tablonun altında Azure portal gösterir `exceptions` `traces` .

Daha fazla ayrıntı için [Otomatik toplanan günlük yapılandırmasına](./java-standalone-config.md#auto-collected-logging) bakın.

## <a name="import-ssl-certificates"></a>SSL sertifikalarını içeri aktarma

Bu bölüm, Java Aracısı kullanılırken SSL sertifikalarıyla ilgili özel durumları gidermenize ve muhtemelen düzeltmenize yardımcı olur.

Bu sorunu çözmek için aşağıdaki iki farklı yol vardır:
* Varsayılan bir Java anahtar deposu kullanılıyorsa
* Özel bir Java anahtar deposu kullanılıyorsa

Hangi yolun izleyeceğinizi bilmiyorsanız, bir JVM bağımsız değişkeni olup olmadığını kontrol edin `-Djavax.net.ssl.trustStore=...` .
Bu tür bir JVM bağımsız _değişkeni yoksa,_ muhtemelen varsayılan Java keystore ' u kullanıyorsunuz demektir.
Böyle bir JVM bağımsız _değişkeni varsa,_ büyük olasılıkla özel bir keystore kullanıyorsunuz ve JVM bağımsız değişkeni sizi özel keystore 'a işaret edecektir.

### <a name="if-using-the-default-java-keystore"></a>Varsayılan Java keystore kullanılıyorsa:

Genellikle varsayılan Java anahtar deposu, tüm CA kök sertifikalarına zaten sahip olur. Ancak, alma uç noktası sertifikası farklı bir kök sertifika tarafından imzalanabilir gibi bazı özel durumlar da olabilir. Bu nedenle, bu sorunu çözmek için aşağıdaki üç adımı öneririz:

1.  Application Insights uç noktasını imzalamak için kullanılan kök sertifikanın varsayılan keystore 'da zaten mevcut olup olmadığını denetleyin. Güvenilen CA sertifikaları, varsayılan olarak içinde depolanır `$JAVA_HOME/jre/lib/security/cacerts` . Bir Java anahtar deposu 'da sertifikaları listelemek için aşağıdaki komutu kullanın:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    Çıktıyı bunun gibi geçici bir dosyaya yeniden yönlendirebilirsiniz (daha sonra aramak daha kolay olacaktır)
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. Sertifika listesini aldıktan sonra, Application Insights uç noktasını imzalamak için kullanılan kök sertifikayı indirmek için aşağıdaki [adımları](#steps-to-download-ssl-certificate) izleyin.

    Sertifikayı indirdikten sonra, aşağıdaki komutu kullanarak sertifikada bir SHA-1 karması oluşturun:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    SHA-1 değerini kopyalayın ve daha önce kaydettiğiniz "temp.txt" dosyasında bu değerin mevcut olup olmadığını denetleyin.  Temp dosyasında SHA-1 değerini bulamıyorsanız, varsayılan Java keystore 'da indirilen kök sertifikanın eksik olduğunu gösterir.


3. Aşağıdaki komutu kullanarak kök sertifikayı varsayılan Java anahtar deposu 'a aktarın:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    Bu durumda,
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Özel bir Java keystore kullanılıyorsa:

Özel bir Java keystore kullanıyorsanız, Application Insights uç nokta (lar) ı kök SSL sertifikasını buna aktarmanız gerekebilir.
Bu sorunu çözmek için aşağıdaki iki adımı öneririz:
1. Application Insights uç noktasından kök sertifikayı indirmek için bu [adımları](#steps-to-download-ssl-certificate) izleyin.
2. Kök SSL sertifikasını özel Java keystore 'a aktarmak için aşağıdaki komutu kullanın:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>SSL sertifikası indirme adımları

1.  En sevdiğiniz tarayıcıyı açın ve `IngestionEndpoint` uygulamanızı işaretlemek için kullanılan bağlantı dizesinde bulunan URL 'ye gidin.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Bir Application Insights bağlantı dizesi gösteren ekran görüntüsü." lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Tarayıcıda **site bilgilerini görüntüle** (kilit) simgesini seçin ve ardından **sertifika** seçeneğini belirleyin.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Site bilgilerinde sertifika seçeneğinin ekran görüntüsü." lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  ' Yaprak ' sertifikasını indirmek yerine ' kök ' sertifikayı aşağıda gösterildiği gibi indirmeniz gerekir. Daha sonra, "sertifika yolu" na tıkla, > kök sertifikayı seçmeniz gerekir-> ' sertifikayı görüntüle ' seçeneğine tıklayın. Bu, yeni bir sertifika menüsü açılır ve yeni menüden sertifikayı indirebilirsiniz.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Kök sertifikayı seçme ekranının ekran görüntüsü." lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  **Ayrıntılar** sekmesine gidin ve **Dosyaya Kopyala**' yı seçin.
5.  **İleri** düğmesini seçin, **Base-64 Encoded X. 509.440 (. CER)** biçimini seçin ve ardından **İleri** ' yi seçin.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Sertifika Dışarı Aktarma Sihirbazı 'nın seçili bir biçim ile ekran görüntüsü." lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  SSL sertifikasını kaydetmek istediğiniz dosyayı belirtin. Ardından **İleri**  >  **son**' u seçin. "Dışarı aktarma başarılı oldu" iletisini görmeniz gerekir.

> [!WARNING]
> Geçerli sertifikanın süresi dolmadan önce yeni sertifikayı almak için bu adımları yinelemeniz gerekir. Süre sonu bilgilerini **sertifika** Iletişim kutusunun **Ayrıntılar** sekmesinde bulabilirsiniz.
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL sertifikası ayrıntılarını gösteren ekran görüntüsü." lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
