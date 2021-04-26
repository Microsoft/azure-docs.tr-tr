---
ms.openlocfilehash: 956c92c5c020f892b8148e9d43d403b1099fbdba
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113427"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Etkin LTS ve bakım LTS sürümleri (8.11.1 ve 10.14.1 önerilir).
- Etkin bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Önkoşul denetimi

- Node.js yüklendiğini denetlemek için bir Terminal veya komut penceresinde komutunu çalıştırın `node --version` .

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

İlk olarak, Terminal veya komut pencerenizi açın, uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

`npm init -y`Dosya **üzerinde** varsayılan ayarlarla birpackage.jsoluşturmak için öğesini çalıştırın.

```console
npm init -y
```

Yeni oluşturduğunuz dizinin kökünde **phone-numbers-quickstart.js** adlı bir dosya oluşturun. Aşağıdaki kod parçacığını buna ekleyin:

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>Paketi yükler

`npm install`JavaScript Için Azure Iletişim Hizmetleri telefon numaraları istemci kitaplığını yüklemek için komutunu kullanın.

```console
npm install @azure/communication-phone-numbers --save
```

`--save`Seçeneği, kitaplığı dosyadaki **package.js** bir bağımlılık olarak ekler.

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

**PhoneNumbersClient** istemci kitaplığından içeri aktarın ve bağlantı dizeniz ile örneğini oluşturun. Aşağıdaki kod, adlı bir ortam değişkeninden kaynak için bağlantı dizesini alır `COMMUNICATION_SERVICES_CONNECTION_STRING` . [Kaynağınızın bağlantı dizesini yönetme](../../create-communication-resource.md#store-your-connection-string)hakkında bilgi edinin.

Aşağıdaki kodu **phone-numbers-quickstart.js** üst kısmına ekleyin:

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Telefon numaralarını yönetme

### <a name="search-for-available-phone-numbers"></a>Kullanılabilir telefon numaralarını ara

Telefon numaralarını satın almak için önce kullanılabilir telefon numaralarını aramanız gerekir. Telefon numaralarını aramak için, alan kodunu, atama türünü, [telefon numarası özelliklerini](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [telefon numarası türünü](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)ve miktarı belirtin. Ücretsiz telefon numarası türü için, alan kodunu sağlamanın isteğe bağlı olduğunu unutmayın.

Aşağıdaki kod parçacığını `main` işlevinizin içine ekleyin:

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Satın alma telefon numarası

Telefon numarası arama sonucu bir `PhoneNumberSearchResult` . Bu, `searchId` aramada sayıları almak için satın alma NUMARALARı API 'sine geçirilebilen bir içerir. Satın alma telefon numaraları API 'sinin çağrılması, Azure hesabınıza ücretlendirdiğine neden olur.

Aşağıdaki kod parçacığını `main` işlevinizin içine ekleyin:

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Telefon numarası yeteneklerini güncelleştirme

Şimdi satın alınan bir telefon numarasıyla, yeteneklerini güncelleştirmek için aşağıdaki kodu ekleyin:

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Satın alınan telefon numaralarını al

Bir satın alma numarasından sonra, istemciden alabilirsiniz. `main`Yeni satın aldığınız telefon numarasını almak için işlevinizde aşağıdaki kodu ekleyin:

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

Satın alınan tüm telefon numaralarını da alabilirsiniz.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Yayın telefon numarası

Şimdi satın alınan telefon numarasını serbest bırakabilirsiniz. Aşağıdaki kod parçacığını işlevinizin altına ekleyin `main` :

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>Kodu çalıştırma

`node` **phone-numbers-quickstart.js** dosyasına eklediğiniz kodu çalıştırmak için komutunu kullanın.

```console
node phone-numbers-quickstart.js
```