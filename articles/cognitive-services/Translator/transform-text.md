---
title: Metni Dönüştür-Translator Metin Çevirisi API'si
titleSuffix: Azure Cognitive Services
description: Translator Metin Çevirisi API'si kullanarak metni dönüştürün.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 533f09b09b7f5899833669b5f0ad4ffa5efa3a43
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594901"
---
# <a name="how-to-use-the-transformtext-method"></a>TransformText yöntemini kullanma

> [!NOTE]
> Bu yöntem kullanım dışıdır. Translator Metin Çevirisi API'si V 3.0 sürümünde kullanılamaz.

TransformText yöntemi, bir sosyal medya için bir metin normalleştirme işlevidir ve bu, girişin normalleştirilmiş bir biçimini döndürür. Yöntemi, makine çevirisi veya Kullanıcı tarafından oluşturulan içeriklerde genellikle bulunmayan Temizleme giriş metnini bekleyen diğer uygulamalar için bir ön işleme adımı olarak kullanılabilir. İşlev şu anda yalnızca Ingilizce girişle çalışır.

Yöntemi, HTTP üzerinden al kullanan bir yeniden hizmet hizmetidir. Basit XML ve JSON serileştirmesini destekler.

## <a name="parameters"></a>Parametreler

| Parametre | Açıklama |
|:---|:---|
| Yetkilendirme üst bilgisi | **Gerekli** Uygulamayı tanımlamak için kullanılan HTTP üstbilgisi. Anahtar kullan: "Yetkilendirme" ve değer: "Taşıyıcı" + "" + erişim belirteci. Ayrıntılar için buraya gidin.|
| dil | **Gerekli** Dil kodunu temsil eden bir dize. Bu parametre, dil **adı olarak yalnızca** İngilizce ile desteklenir.|
| category | **Isteğe bağlı** Çevirinin kategorisini veya etki alanını içeren bir dize. Bu parametre yalnızca **genel**varsayılan seçeneğini destekler.|
| tümce | **Gerekli** Düzeltmek istediğiniz bir cümle. |

## <a name="return-value"></a>Dönüş değeri

Dönüş değeri, dönüştürülmüş tümceyi sağlar.

> [!div class="tabbedCodeSnippets"]
> ```json
> GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); TransformTextResponse
> {
> int ec;            // A positive number representing an error condition
> string em;         // A descriptive error message
> string sentence;   // transformed text
> }
> ```

## <a name="example"></a>Örnek

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
//Requires System.Runtime.Serialization assembly to be referenced
using System.Runtime.Serialization.Json;
using System.Runtime.Serialization;
//Requires System.Web assembly to be referenced
using System.Web;
using System.Media;
using System.Threading;
namespace MicrosoftTranslatorSdk.HttpSamples
{
    class Program
    {
        static void Main(string[] args)
        {
            AdmAccessToken admToken;
            string headerValue;
            //Get Client Id and Client Secret from https://datamarket.azure.com/developer/applications/
            //Refer obtaining AccessToken (https://msdn.microsoft.com/library/hh454950.aspx)
            AdmAuthentication admAuth = new AdmAuthentication("clientID", "client secret");

            try
            {
                admToken = admAuth.GetAccessToken();
                // Create a header with the access_token property of the returned token
                headerValue = "Bearer " + admToken.access_token;
                TransformTextMethod(headerValue);
                Console.WriteLine("Press any key to continue...");
                Console.ReadKey(true);
            }
            catch (WebException e)
            {
                ProcessWebException(e);
                Console.WriteLine("Press any key to continue...");
                Console.ReadKey(true);
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
                Console.WriteLine("Press any key to continue...");
                Console.ReadKey(true);
            }
        }
        private static void TransformTextMethod(string authToken)
        {
            Console.WriteLine("Enter Text to transform: (e.g Dis is 2 strange i juss wanna go home sooooooon)");
            string textToTransform = Console.ReadLine();
            string language = "en";
            string domain = "general";
            //Keep appId parameter blank as we are sending access token in authorization header.
            string url = string.Format("https://api.microsofttranslator.com/V3/json/TransformText?sentence={0}&category={1}&language={2}",textToTransform,domain,language); ;
            TransformTextResponse transformTextResponse= new TransformTextResponse();
            HttpWebRequest httpWebRequest = (HttpWebRequest)WebRequest.Create(url);
            httpWebRequest.Headers.Add("Authorization", authToken);
            using (WebResponse response = httpWebRequest.GetResponse())
            {
                using( StreamReader sr = new StreamReader(response.GetResponseStream()))
                {
                    using (MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(sr.ReadToEnd())))
                    {
                        DataContractJsonSerializer serializer = new DataContractJsonSerializer(typeof(TransformTextResponse));
                        //Get deserialized TransformTextResponse object from JSON stream
                        transformTextResponse = (TransformTextResponse)serializer.ReadObject(ms);
                        Console.WriteLine("Transformed sentence: {0}", transformTextResponse.sentence);
                    }
                }
            }
        }
        private static void ProcessWebException(WebException e)
        {
            Console.WriteLine("{0}", e.ToString());
            // Obtain detailed error information
            string strResponse = string.Empty;
            using (HttpWebResponse response = (HttpWebResponse)e.Response)
            {
                using (Stream responseStream = response.GetResponseStream())
                {
                    using (StreamReader sr = new StreamReader(responseStream, System.Text.Encoding.ASCII))
                    {
                        strResponse = sr.ReadToEnd();
                    }
                }
            }
            Console.WriteLine("Http status code={0}, error message={1}", e.Status, strResponse);
        }
    }
    [DataContract]
    public class TransformTextResponse
    {
        [DataMember]
        public int ec { get; set; }
        [DataMember]
        public string em { get; set; }
        [DataMember]
        public string sentence { get; set; }
    }
    [DataContract]
    public class AdmAccessToken
    {
        [DataMember]
        public string access_token { get; set; }
        [DataMember]
        public string token_type { get; set; }
        [DataMember]
        public string expires_in { get; set; }
        [DataMember]
        public string scope { get; set; }
    }
    public class AdmAuthentication
    {
        public static readonly string DatamarketAccessUri = "https://datamarket.accesscontrol.windows.net/v2/OAuth2-13";
        private string clientId;
        private string clientSecret;
        private string request;
        private AdmAccessToken token;
        private Timer accessTokenRenewer;
        //Access token expires every 10 minutes. Renew it every 9 minutes only.
        private const int RefreshTokenDuration = 9;
        public AdmAuthentication(string clientId, string clientSecret)
        {
            this.clientId = clientId;
            this.clientSecret = clientSecret;
            //If clientid or client secret has special characters, encode before sending request
            this.request = string.Format("grant_type=client_credentials&client_id={0}&client_secret={1}&scope=http://api.microsofttranslator.com", HttpUtility.UrlEncode(clientId), HttpUtility.UrlEncode(clientSecret));
            this.token = HttpPost(DatamarketAccessUri, this.request);
            //renew the token every specified minutes
            accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback), this, TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
        }
        public AdmAccessToken GetAccessToken()
        {
            return this.token;
        }
        private void RenewAccessToken()
        {
            AdmAccessToken newAccessToken = HttpPost(DatamarketAccessUri, this.request);
            this.token = newAccessToken;
            Console.WriteLine(string.Format("Renewed token for user: {0} is: {1}", this.clientId, this.token.access_token));
        }
        private void OnTokenExpiredCallback(object stateInfo)
        {
            try
            {
                RenewAccessToken();
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
            }
            finally
            {
                try
                {
                    accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
                }
                catch (Exception ex)
                {
                    Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
                }
            }
        }
        private AdmAccessToken HttpPost(string DatamarketAccessUri, string requestDetails)
        {
            //Prepare OAuth request
            WebRequest webRequest = WebRequest.Create(DatamarketAccessUri);
            webRequest.ContentType = "application/x-www-form-urlencoded";
            webRequest.Method = "POST";
            byte[] bytes = Encoding.ASCII.GetBytes(requestDetails);
            webRequest.ContentLength = bytes.Length;
            using (Stream outputStream = webRequest.GetRequestStream())
            {
                outputStream.Write(bytes, 0, bytes.Length);
            }
            using (WebResponse webResponse = webRequest.GetResponse())
            {
                DataContractJsonSerializer serializer = new DataContractJsonSerializer(typeof(AdmAccessToken));
                //Get deserialized object from JSON stream
                AdmAccessToken token = (AdmAccessToken)serializer.ReadObject(webResponse.GetResponseStream());
                return token;
            }
        }
    }
}

```
