---
title: Kiracı modeli oluşturma (Önizleme)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Hem güvenli hem de uyumlu olan kuruluşa özgü terimler için en iyi konuşma tanıma sunmak üzere Office 365 verilerinizi kullanan bir kiracı modelini (Office 365 verileriyle Özel Konuşma Tanıma) otomatik olarak oluşturun.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: c8a2855ce9cd320be3aea8b3b4a05f3b3eb39976
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578230"
---
# <a name="create-a-tenant-model-preview"></a>Kiracı modeli oluşturma (Önizleme)

Kiracı modeli (Office 365 verileriyle Özel Konuşma Tanıma), kuruluşunuzun Office365 verilerinden otomatik olarak özel bir konuşma tanıma modeli üreten Office 365 kurumsal müşterilerine yönelik bir katılım hizmetidir. Oluşturulan model teknik şartlar, öğretmek ve insanların adları için en iyi duruma getirilmiştir, güvenli ve uyumlu bir şekilde yapılır.

> [!IMPORTANT]
> Kuruluşunuzun kiracı modeliyle ilgili olması halinde konuşma hizmeti, kuruluşunuzun Office 365 Genel Grup e-postalarından ve kuruluşunuzdaki herkes tarafından görülemeyen belgelerden oluşturulan dil modeline erişebilir. Kuruluşunuzun Office 365 Yöneticisi, Office 365 Yönetici portalını kullanarak kuruluş genelindeki dil modelinin kullanımını kapatabilir/kapatabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Microsoft 365 Yönetim merkezinde kiracı modeli kullanmak için kaydolma
> * Konuşma aboneliği anahtarı al
> * Kiracı modeli oluşturma
> * Kiracı modeli dağıtma
> * Konuşma SDK 'Sı ile kiracı modeli kullanma

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Microsoft 365 Yönetim merkezini kullanarak kaydetme

Kiracı modelinizi dağıtabilmeniz için önce Microsoft 365 Yönetim merkezini kullanarak kaydetmeniz gerekir. Bu görev yalnızca Microsoft 365 yöneticiniz tarafından tamamlanabilir.

1. [Microsoft 365 Yönetim merkezinde](https://admin.microsoft.com )oturum açın.
2. Sol bölmede **Ayarlar** ve **uygulamalar**' ı seçin.

   ![Kiracı modeli kaydı](media/tenant-language-model/tenant-language-model-enrollment.png)

3. **Azure konuşma Hizmetleri**' ni bulun ve seçin.

   ![Kiracı modeli kaydı 2](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Onay kutusuna tıklayın ve kaydedin.

Kiracı modelini kapatmanız gerekiyorsa, bu ekrana geri gidin, onay kutusunun işaretini kaldırın ve kaydedin.

## <a name="get-a-speech-subscription-key"></a>Konuşma aboneliği anahtarı al

Konuşma SDK 'Sı ile bir kiracı modeli kullanabilmek için, bir konuşma kaynağına ve onunla ilişkili abonelik anahtarına ihtiyacınız vardır.

1. [Azure portalında](https://aka.ms/azureportal) oturum açın.
2. **Kaynak oluştur**' u seçin.
3. Arama çubuğuna şunu yazın: **konuşma**.
4. **Konuşma**' yı seçin ve ardından **Oluştur**' a tıklayın.
5. Kaynağı oluşturmak için ekrandaki yönergeleri izleyin. Emin ol:
   * **Konum** **eastus** veya **westus**olarak ayarlanır.
   * **Fiyatlandırma katmanı** **S0**olarak ayarlanır.
6. **Oluştur**'a tıklayın.
7. Birkaç dakika sonra kaynağınız oluşturulur. Abonelik anahtarı, kaynağınızın **genel bakış** bölümünde bulunabilir.

## <a name="create-a-model"></a>Bir model oluşturma

Yöneticiniz kuruluşunuz için kiracı modelini etkinleştirdikten sonra, Office365 verilerinizi temel alan bir dil modeli oluşturabilirsiniz.

1. [Konuşma Studio](https://speech.microsoft.com/)'da oturum açın.
2. Sağ üst köşede dişli simgesini (Ayarlar) bulup tıklatın, ardından **kiracı modeli ayarları**' nı seçin.

   ![Ayarlar menüsü](media/tenant-language-model/tenant-language-settings.png)

3. Bu noktada, bir kiracı modeli oluşturmak için uygun olup olmadığını bilmenizi sağlayan bir ileti görürsünüz.
   > [!NOTE]
   > Kuzey Amerika 'de Office 365 Kurumsal müşterileri bir kiracı modeli (Ingilizce) oluşturmaya uygundur. Müşteri Kasası (CLB), müşteri anahtarı (CK) veya Office 365 Kamu müşterisiyseniz bu özellik kullanılamaz. Müşteri Kasası veya müşteri anahtarı müşterisi olup olmadığınızı öğrenmek için şu yönergeleri izleyin:
   > * [Müşteri Kasası](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Müşteri anahtarı](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Office 365 Kamu](https://www.microsoft.com/microsoft-365/government)

4. Ardından **kabul**et ' i seçin. Kiracı modeliniz hazırlanmaya yönelik yönergeler içeren bir e-posta alacaksınız.

## <a name="deploy-your-model"></a>Modelinizi dağıtın

Kiracı modeliniz hazırlandığınızda, modelinizi dağıtmak için aşağıdaki adımları izleyin:

1. Aldığınız onay e-postasında **modeli görüntüle** düğmesine tıklayın veya [konuşma Studio](https://speech.microsoft.com/)'da oturum açın.
2. Sağ üst köşede dişli simgesini (Ayarlar) bulup tıklatın, ardından **kiracı modeli ayarları**' nı seçin.

   ![Ayarlar menüsü](media/tenant-language-model/tenant-language-settings.png)

3. **Dağıt**' a tıklayın.
4. Modeliniz dağıtıldığında, durum **dağıtıldı**olarak değişir.

## <a name="use-your-model-with-the-speech-sdk"></a>Konuşma SDK 'Sı ile modelinizi kullanma

Modelinizi dağıttığınıza göre, artık bunu konuşma SDK 'Sı ile kullanabilirsiniz. Bu bölümde, Azure AD kimlik doğrulaması kullanarak konuşma hizmetini çağırmak için sunulan örnek kodu kullanacaksınız.

' De C#konuşma SDK 'sını çağırmak için kullanacağınız koda göz atalım. Bu örnekte, bir kiracı modeli kullanarak konuşma tanımayı gerçekleştirirsiniz. Bu kılavuz, platformunuzun zaten ayarlandığı varsayılır. Kurulumu için yardıma ihtiyacınız varsa, bkz. [hızlı başlangıç: konuşmayı tanıma, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Bu kodu projenize kopyalayın:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // Note: ServiceApplicationId is a fixed value.  No need to change.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Ardından, komut satırından projeyi yeniden oluşturmanız ve çalıştırmanız gerekir. Komutu çalıştırmadan önce güncelleştirmeniz gereken birkaç parametre vardır.

1. `<Username>` ve `<Password>` değerlerini geçerli bir kiracı kullanıcısının değerleriyle değiştirin.
2. `<Subscription-Key>`, konuşma kaynağınızın abonelik anahtarıyla değiştirin. Bu değer, [Azure Portal](https://aka.ms/azureportal)konuşma kaynağınızın **genel bakış** bölümünde bulunur.
3. `<Endpoint-Uri>` aşağıdaki uç noktayla değiştirin. `{your-region}`, konuşma kaynağınızın oluşturulduğu bölge ile değiştirdiğinizden emin olun. Bu bölgeler desteklenir: `westus`, `westus2`ve `eastus`. Bölge bilgileriniz, [Azure Portal](https://aka.ms/azureportal)konuşma kaynağınızın **genel bakış** bölümünde bulunabilir.
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Şu komutu çalıştırın:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma Studio](https://speech.microsoft.com/)
* [Konuşma SDK'sı](speech-sdk.md)
