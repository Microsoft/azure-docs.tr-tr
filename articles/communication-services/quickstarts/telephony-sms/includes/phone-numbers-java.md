---
ms.openlocfilehash: 4891a602e9fb5ebbdaadc980c1960b95816c6653
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113143"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) sürüm 8 veya üzeri.
- [Apache Maven](https://maven.apache.org/download.cgi).-dağıtılan bir iletişim hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-java-application"></a>Yeni bir Java uygulaması oluşturma

Terminal veya komut pencerenizi açın. Java uygulamanızı oluşturmak istediğiniz dizine gidin. Maven-,-hızlı başlangıç şablonundan Java projesi oluşturmak için aşağıdaki komutu çalıştırın.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

' Oluştur ' görevinin ile aynı ada sahip bir dizin oluşturduğunu fark edeceksiniz `artifactId` . Bu dizin altında src/Main/Java dizini proje kaynak kodunu içerir, `src/test/java directory` Test kaynağını içerir ve `pom.xml` dosya projenin proje nesne modeli veya Pod olur.

### <a name="install-the-package"></a>Paketi yükler

**pom.xml** dosyasını metin düzenleyicinizde açın. Aşağıdaki bağımlılık öğelerini bağımlılıklar grubuna ekleyin.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-phonenumbers</artifactId>
    <version>1.0.0-beta.7</version>
</dependency>

<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Proje dizininden:

1. */Src/Main/Java/com/Communication/QuickStart* dizinine gidin
1. Düzenleyicinizde *app. Java* dosyasını açın
1. İfadesini Değiştir `System.out.println("Hello world!");`
1. `import`Yönergeler ekleme

Başlamak için aşağıdaki kodu kullanın:

```java
import com.azure.communication.phonenumbers.*;
import com.azure.communication.phonenumbers.models.*;
import java.io.*;
import com.azure.core.util.Context;
import com.azure.core.util.polling.PollResponse;
import com.azure.identity.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Phone Numbers Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-phone-numbers-client"></a>Telefon numaraları Istemcisinin kimliğini doğrulama

PhoneNumberClientBuilder Azure Active Directory kimlik doğrulaması kullanmak için etkinleştirildi
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L52-L62 -->
```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

Alternatif olarak, kimlik doğrulaması için iletişim kaynağından uç nokta ve erişim anahtarı kullanmak da mümkündür.
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L30-L41 -->
```java
// You can find your connection string from your resource in the Azure portal
String connectionString = "https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<ACCESS_KEY>";

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

## <a name="manage-phone-numbers"></a>Telefon numaralarını yönetme

### <a name="search-for-available-phone-numbers"></a>Kullanılabilir telefon numaralarını ara

Telefon numaralarını satın almak için önce kullanılabilir telefon numaralarını aramanız gerekir. Telefon numaralarını aramak için, alan kodunu, atama türünü, [telefon numarası özelliklerini](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [telefon numarası türünü](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)ve miktarı belirtin. Ücretsiz telefon numarası türü için, alan kodunu sağlamanın isteğe bağlı olduğunu unutmayın.

```java
 PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities()
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PhoneNumberSearchOptions searchOptions = new PhoneNumberSearchOptions().setAreaCode("833").setQuantity(1);

PhoneNumberSearchResult searchResult = phoneNumberClient
    .beginSearchAvailablePhoneNumbers("US", PhoneNumberType.TOLL_FREE, PhoneNumberAssignmentType.APPLICATION, capabilities,  searchOptions, Context.NONE)
    .getFinalResult();

System.out.println("Searched phone numbers: " + searchResult.getPhoneNumbers());
System.out.println("Search expires by: " + searchResult.getSearchExpiresBy());
System.out.println("Phone number costs:" + searchResult.getCost().getAmount());
```

### <a name="purchase-phone-numbers"></a>Satın alma telefon numaraları

Telefon numarası arama sonucu, PhoneNumberSearchResult ' dır. Bu, `searchId` aramada sayıları almak için satın alma NUMARALARı API 'sine geçirilebilen bir içerir. Satın alma telefon numaraları API 'sinin çağrılması, Azure hesabınıza ücretlendirdiğine neden olur.

```java
PollResponse<PhoneNumberOperation> purchaseResponse = phoneNumberClient.beginPurchasePhoneNumbers(searchResult.getSearchId(), Context.NONE).waitForCompletion();
System.out.println("Purchase phone numbers operation is: " + purchaseResponse.getStatus());
```

### <a name="get-phone-numbers"></a>Telefon numaralarını al

Bir satın alma numarasından sonra, istemciden alabilirsiniz.
```java
PurchasedPhoneNumber phoneNumber = phoneNumberClient.getPurchasedPhoneNumber("+14255550123");
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

Satın alınan tüm telefon numaralarını da alabilirsiniz.
``` java
PagedIterable<PurchasedPhoneNumber> phoneNumbers = phoneNumberClient.listPurchasedPhoneNumbers(Context.NONE);
PurchasedPhoneNumber phoneNumber = phoneNumbers.iterator().next();
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

### <a name="update-phone-number-capabilities"></a>Telefon numarası yeteneklerini güncelleştirme

Satın alınan bir numarayla, yeteneklerini güncelleştirebilirsiniz.
```java
PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities();
capabilities
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PurchasedPhoneNumber phoneNumber = phoneNumberClient.beginUpdatePhoneNumberCapabilities("+14255550123", capabilities, Context.NONE).getFinalResult();

System.out.println("Phone Number Calling capabilities: " + phoneNumber.getCapabilities().getCalling()); //Phone Number Calling capabilities: inbound
System.out.println("Phone Number SMS capabilities: " + phoneNumber.getCapabilities().getSms()); //Phone Number SMS capabilities: inbound+outbound
```

### <a name="release-phone-number"></a>Yayın telefon numarası

Satın alınan bir telefon numarasını serbest bırakabilirsiniz.
```java
PollResponse<PhoneNumberOperation> releaseResponse =
    phoneNumberClient.beginReleasePhoneNumber("+14255550123", Context.NONE).waitForCompletion();
System.out.println("Release phone number operation is: " + releaseResponse.getStatus());
```

## <a name="run-the-code"></a>Kodu çalıştırma

*pom.xml* dosyasını içeren dizine gidin ve aşağıdaki komutu kullanarak projeyi derleyin `mvn` .

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

Uygulamanın çıktısı tamamlanan her eylemi açıklar:
<!---cSpell:disable --->
```console
Azure Communication Services - Phone Numbers Quickstart

Searched phone numbers: [+18001234567]

Purchase phone numbers operation is: SUCCESSFULLY_COMPLETED

Phone Number Country Code: US

Phone Number Calling capabilities: inbound

Phone Number SMS capabilities: inbound

Release phone number operation is: SUCCESSFULLY_COMPLETED

```
<!---cSpell:enable --->