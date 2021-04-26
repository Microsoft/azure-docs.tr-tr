---
ms.openlocfilehash: 0bfb23977f6553568da24df614621bdf1eb9d06d
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112782"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- İşletim sisteminiz için en son sürüm [.NET Core istemci kitaplığı](https://dotnet.microsoft.com/download/dotnet-core) .
- Etkin bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Önkoşul denetimi

- Bir Terminal veya komut penceresinde, `dotnet` .NET istemci kitaplığı 'nın yüklü olup olmadığını denetlemek için komutunu çalıştırın.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-c-application"></a>Yeni bir C# uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `PhoneNumbersQuickstart` . Bu komut, tek bir kaynak dosyası olan basit bir "Merhaba Dünya" C# projesi oluşturur: **program. cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin ve `dotnet build` uygulamanızı derlemek için komutunu kullanın.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>Paketi yükler

Hala uygulama dizininde, komutunu kullanarak .NET için Azure Iletişim PhoneNumbers istemci Kitaplığı ' nı yükleyebilirsiniz `dotnet add package` .

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.6
```

`using`Ad alanlarını dahil etmek Için **program. cs** ' nin üstüne bir yönerge ekleyin.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
```

`Main`İşlev imzasını zaman uyumsuz olacak şekilde güncelleştirin.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Telefon numarası istemcilerinin kimliği [Azure portal] [azure_portal] içindeki bir Azure Iletişim kaynaklarından alınan bağlantı dizesi kullanılarak doğrulanabilir.

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

Telefon numarası istemcileri, Azure Active Directory kimlik doğrulaması ile kimlik doğrulama seçeneğine de sahiptir. Bu seçenekle, `AZURE_CLIENT_SECRET` `AZURE_CLIENT_ID` ve `AZURE_TENANT_ID` ortam değişkenlerinin kimlik doğrulaması için ayarlanması gerekir.

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Telefon numaralarını yönetme

### <a name="search-for-available-phone-numbers"></a>Kullanılabilir telefon numaralarını ara

Telefon numaralarını satın almak için önce kullanılabilir telefon numaralarını aramanız gerekir. Telefon numaralarını aramak için, alan kodunu, atama türünü, [telefon numarası özelliklerini](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [telefon numarası türünü](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)ve miktarı belirtin. Ücretsiz telefon numarası türü için, alan kodunu sağlamanın isteğe bağlı olduğunu unutmayın.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Satın alma telefon numaraları

Telefon numarası arama sonucu bir `PhoneNumberSearchResult` . Bu, `SearchId` aramada sayıları almak için satın alma NUMARALARı API 'sine geçirilebilen bir içerir. Satın alma telefon numaraları API 'sinin çağrılması, Azure hesabınıza ücretlendirdiğine neden olur.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionResponseAsync();
```

### <a name="get-phone-numbers"></a>Telefon numaralarını al

Bir satın alma numarasından sonra, istemciden alabilirsiniz.
```csharp
var getPhoneNumberResponse = await client.GetPurchasedPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

Satın alınan tüm telefon numaralarını da alabilirsiniz.
``` csharp
var purchasedPhoneNumbers = client.GetPurchasedPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Telefon numarası yeteneklerini güncelleştirme

Satın alınan bir numarayla, yeteneklerini güncelleştirebilirsiniz.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Yayın telefon numarası

Satın alınan bir telefon numarasını serbest bırakabilirsiniz.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionResponseAsync();
````

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulamayı komut ile uygulama dizininizden çalıştırın `dotnet run` .

```console
dotnet run
```

## <a name="sample-code"></a>Örnek Kod

Örnek uygulamayı [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers) 'dan indirebilirsiniz
