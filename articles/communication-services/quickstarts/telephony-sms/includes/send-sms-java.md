---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: cdf1267d53abc2214521f584b6cfb4738b808204
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113271"
---
SMS mesajları göndermek için Iletişim Hizmetleri Java SMS SDK 'sını kullanarak Azure Iletişim Hizmetleri ile çalışmaya başlayın.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) sürüm 8 veya üzeri.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Etkin bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- SMS etkin telefon numarası. [Telefon numarası alın](../get-phone-number.md).

### <a name="prerequisite-check"></a>Önkoşul denetimi

- Bir Terminal veya komut penceresinde, `mvn -v` Maven 'nin yüklenip yüklenmediğini denetlemek için komutunu çalıştırın.
- Iletişim Hizmetleri kaynağınız ile ilişkili telefon numaralarını görüntülemek için [Azure Portal](https://portal.azure.com/)oturum açın, iletişim hizmetleri kaynağınızı bulun ve sol gezinti bölmesinden **telefon numaraları** sekmesini açın.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-java-application"></a>Yeni bir Java uygulaması oluşturma

Terminal veya komut pencerenizi açın ve Java uygulamanızı oluşturmak istediğiniz dizine gidin. Maven-,-hızlı başlangıç şablonundan Java projesi oluşturmak için aşağıdaki komutu çalıştırın.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

' Oluştur ' hedefi, ArtifactId ile aynı ada sahip bir dizin oluşturur. Bu dizin altında **src/Main/Java** dizini proje kaynak kodunu içerir, **src/test/Java dizini** test kaynağını içerir ve **pom.xml** dosyası projenin proje nesne modeli veya Pod olur.

### <a name="install-the-package"></a>Paketi yükler

**pom.xml** dosyasını metin düzenleyicinizde açın. Aşağıdaki bağımlılık öğesini bağımlılıklar grubuna ekleyin.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

**/Src/Main/Java/com/Communication/QuickStart/App.Java** 'i bir metin düzenleyicisinde açın, içeri aktarma yönergeleri ekleyin ve `System.out.println("Hello world!");` ifadesini kaldırın:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Java için Azure Communication Services SMS SDK 'sının bazı önemli özelliklerinden bazılarını işler.

| Ad                                                             | Açıklama                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Bu sınıf, SmsClient oluşturur. Bunu uç nokta, kimlik bilgileri ve bir http istemcisiyle sağlarsınız. |
| SmsClient                    | Bu sınıf tüm SMS işlevleri için gereklidir. SMS mesajları göndermek için bunu kullanırsınız.                |
| Smssendoseçenekleri               | Bu sınıf özel etiketler eklemek ve teslim raporlamayı yapılandırmak için seçenekler sağlar. DeliveryReportEnabled değeri true olarak ayarlanırsa, teslim başarılı olduğunda bir olay yayınlanır |        
| SmsSendResult                | Bu sınıf, SMS hizmetinden elde edilen sonucu içerir.                                          |

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

`SmsClient`Bağlantı dizeniz ile bir örneğini oluşturun. (Kimlik bilgileri `Key` Azure Portal. [Kaynağınızın bağlantı dizesini yönetme](../../create-communication-resource.md#store-your-connection-string)hakkında bilgi edinin. Ayrıca, istemcisini uygulayan özel HTTP istemcisiyle istemciyi başlatabilirsiniz `com.azure.core.http.HttpClient` .

`main` yöntemine aşağıdaki kodu ekleyin:

```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .buildClient();
```

Ayrıca, uç nokta ve erişim anahtarı sağlamak yerine connectionString () işlevini kullanarak tüm bağlantı dizesini de sağlayabilirsiniz.
```java
// You can find your connection string from your resource in the Azure portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>1:1 SMS iletisi gönder

Tek bir alıcıya SMS iletisi göndermek için, `send` SmsClient 'deki yöntemi tek bir alıcı telefon numarasıyla çağırın. Ayrıca, teslim raporunun etkinleştirilip etkinleştirilmeyeceğini ve özel Etiketler ayarlayamayacağını belirtmek için isteğe bağlı parametreleri de geçirebilirsiniz.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```

`<from-phone-number>`Iletişim Hizmetleri kaynağınız ile ILIŞKILI SMS etkin telefon numarasıyla ve `<to-phone-number>` ileti göndermek istediğiniz telefon numarasıyla değiştirmelisiniz.

> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Seçeneklerle 1: N SMS ileti gönderin
Bir alıcı listesine SMS iletisi göndermek için, bir `send` alıcı telefon numarası listesiyle yöntemi çağırın. Ayrıca, teslim raporunun etkinleştirilip etkinleştirilmeyeceğini ve özel Etiketler ayarlayamayacağını belirtmek için isteğe bağlı parametreleri de geçirebilirsiniz.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.sendWithResponse(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE).getValue();

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

`<from-phone-number>`Iletişim Hizmetleri kaynağınız ile ILIŞKILI SMS etkin telefon numarasıyla `<to-phone-number-1>` ve `<to-phone-number-2>` ileti göndermek istediğiniz telefon numarası ile değiştirmelisiniz.

> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör: + 14255550123).

`setDeliveryReportEnabled`Yöntemi, teslim raporlamayı yapılandırmak için kullanılır. Bu, SMS iletileri teslim edildiğinde olayları yayma isteyebileceğiniz senaryolar için yararlıdır. SMS iletilerinize yönelik teslim raporlamayı yapılandırmak için [SMS olayları](../handle-sms-events.md) Hızlı Başlangıç Kılavuzu ' na bakın.

`setTag`Yöntemi teslim raporuna bir etiket uygulamak için kullanılır.

## <a name="run-the-code"></a>Kodu çalıştırma

**pom.xml** dosyasını içeren dizine gidin ve komutunu kullanarak projeyi derleyin `mvn` .

```console

mvn compile

```

Ardından, paketini oluşturun.

```console

mvn package

```

`mvn`Uygulamayı yürütmek için aşağıdaki komutu çalıştırın.

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```
