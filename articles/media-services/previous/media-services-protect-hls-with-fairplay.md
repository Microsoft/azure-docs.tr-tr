---
title: Microsoft PlayReady veya Apple FairPlay-Azure ile HLS içeriğini koruma | Microsoft Docs
description: Bu konu, genel bir bakış sağlar ve Apple FairPlay ile HTTP Canlı Akışı (HLS) içeriğinizi dinamik olarak şifrelemek için Azure Media Services nasıl kullanacağınızı gösterir. Ayrıca, FairPlay lisanslarını istemcilere iletmek için Media Services lisans teslim hizmetini nasıl kullanacağınızı gösterir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 870b567d5917c405a3eef70a27b1a19c021d7a80
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055153"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Apple FairPlay veya Microsoft PlayReady ile HLS içeriğinizi koruma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/).   Media Services V2 'ye yeni özellik veya işlevsellik eklenmiyor >. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)
>

Azure Media Services, aşağıdaki biçimleri kullanarak HTTP Canlı Akışı (HLS) içeriğinizi dinamik olarak şifrelemenizi sağlar:  

* **AES-128 zarfı şifresiz anahtar**

    Tüm öbek, **AES-128 CBC** modu kullanılarak şifrelenir. Akışın şifresinin çözülmesi iOS ve OS X Player tarafından yerel olarak desteklenir. Daha fazla bilgi için bkz. [AES-128 dinamik şifrelemesini ve anahtar teslim hizmetini kullanma](media-services-playready-license-template-overview.md).
* **Apple FairPlay**

    Tek tek video ve ses örnekleri **AES-128 CBC** modu kullanılarak şifrelenir. **Fairplay streaming** (fps), IOS ve Apple TV 'de yerel destek ile cihaz işletim sistemleriyle tümleşiktir. OS X üzerinde Safari, şifreli Medya Uzantıları (EME) arabirimi desteğini kullanarak FPS 'yi sunar.
* **Microsoft PlayReady**

Aşağıdaki görüntüde, **HLS + Fairplay veya PlayReady dinamik şifreleme** iş akışı gösterilmektedir.

![Dinamik şifreleme iş akışı diyagramı](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Bu makalede, HLS içeriğinizi Apple FairPlay ile dinamik olarak şifrelemek için Media Services nasıl kullanılacağı gösterilmektedir. Ayrıca, FairPlay lisanslarını istemcilere iletmek için Media Services lisans teslim hizmetini nasıl kullanacağınızı gösterir.

> [!NOTE]
> Ayrıca, HLS içeriğinizi PlayReady ile şifrelemek istiyorsanız ortak bir içerik anahtarı oluşturmanız ve bunu varlığınızla ilişkilendirmeniz gerekir. Ayrıca, içerik anahtarının yetkilendirme ilkesini [PlayReady dinamik ortak şifreleme kullanma](media-services-protect-with-playready-widevine.md)bölümünde açıklandığı gibi yapılandırmanız gerekir.
>
>

## <a name="requirements-and-considerations"></a>Gereksinimler ve önemli noktalar

FairPlay ile şifrelenen HLS 'leri iletmek ve FairPlay lisanslarını iletmek için Media Services kullanılırken aşağıdakiler gereklidir:

  * Azure hesabı. Ayrıntılar için bkz. [Azure Ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Bir Media Services hesabı. Bir tane oluşturmak için, bkz. [Azure Portal kullanarak Azure Media Services hesabı oluşturma](media-services-portal-create-account.md).
  * [Apple geliştirme programı](https://developer.apple.com/)ile kaydolun.
  * Apple, içerik sahibinin [dağıtım paketini](https://developer.apple.com/contact/fps/)almasını gerektirir. Anahtar güvenlik modülünü (KSM) Media Services ile zaten uygulamış ve son FPS paketini istediğini unutmayın. Son FPS paketinde sertifika oluşturmak ve uygulama gizli anahtarı (ASK) almak için yönergeler vardır. FairPlay yapılandırmak için sorma ' yı kullanın.
  * Azure Media Services .NET SDK sürümü **3.6.0** veya üzeri.

Media Services anahtar teslim tarafında aşağıdaki şeyler ayarlanmalıdır:

  * **Uygulama sertifikası (AC)**: Bu, özel anahtarı içeren bir. pfx dosyasıdır. Bu dosyayı oluşturun ve bir parolayla şifreleyin.

       Bir anahtar teslim ilkesi yapılandırdığınızda, bu parolayı ve. pfx dosyasını Base64 biçiminde belirtmeniz gerekir.

      Aşağıdaki adımlarda, FairPlay için bir. pfx sertifika dosyasının nasıl oluşturulacağı açıklanır:

    1. Kaynağından OpenSSL 'yi yükler https://slproweb.com/products/Win32OpenSSL.html .

        FairPlay sertifikasının ve Apple tarafından sunulan diğer dosyaların bulunduğu klasöre gidin.
    2. Komut satırından aşağıdaki komutu çalıştırın. Bu,. cer dosyasını. ped dosyasına dönüştürür.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509-ın FairPlay. cer-Out FairPlay-out. ped
    3. Komut satırından aşağıdaki komutu çalıştırın. Bu,. PEA dosyasını özel anahtarla bir. pfx dosyasına dönüştürür. . Pfx dosyasının parolasına daha sonra OpenSSL sorulur.

        "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12-Export-Out FairPlay-out. pfx-INKEY PrivateKey. ped-in FairPlay-out. pek-passin file:privatekey-pem-pass.txt
  * **Uygulama sertifikası parolası**:. pfx dosyası oluşturmak için parola.
  * **Uygulama sertifikası parola kimliği**: diğer Media Services anahtarlarını karşıya yükleme biçimine benzer şekilde parolayı karşıya yüklemeniz gerekir. Media Services KIMLIĞINI almak için **Contentkeytype. FairPlayPfxPassword** enum değerini kullanın. Bu, anahtar teslim ilkesi seçeneği içinde kullanılması gereken şeydir.
  * **IV**: Bu, 16 baytlık rastgele bir değerdir. Varlık teslim ilkesindeki IV ile aynı olmalıdır. IV 'yi oluşturur ve her iki yere de koyabilirsiniz: varlık teslim ilkesi ve anahtar teslim ilkesi seçeneği.
  * **Sorun**: Bu anahtar, Apple Developer Portal 'ı kullanarak sertifika oluşturduğunuzda alınır. Her geliştirme ekibi, benzersiz bir sorun alır. ASK bir kopyasını kaydedin ve güvenli bir yerde saklayın. Daha sonra Media Services için ASK FairPlayAsk olarak yapılandırmanız gerekir.
  * **Sorun kimliği**: bu KIMLIK, ask Media Services yüklediğinizde elde edilir. **Contentkeytype. FairPlayAsk** enum DEĞERINI kullanarak ask öğesini karşıya yüklemeniz gerekir. Sonuç olarak, Media Services KIMLIĞI döndürülür ve anahtar teslim ilkesi seçeneği ayarlanırken kullanılması gerekir.

Aşağıdaki noktalar, FPS istemci tarafında ayarlanmalıdır:

  * **Uygulama sertifikası (AC)**: Bu, işletim sisteminin bazı yükü şifrelemek için kullandığı ortak anahtarı içeren bir. cer/. der dosyasıdır. Oyuncu, oynatıcı için gerekli olduğu için Media Services hakkında bilgi sahibi olmalıdır. Anahtar teslim hizmeti, karşılık gelen özel anahtarı kullanarak onun şifresini çözer.

FairPlay şifreli bir akış geri oynatmak için önce gerçek bir sorun alın ve ardından gerçek bir sertifika oluşturun. Bu işlem üç bölümden oluşur:

  * . der dosyası
  * . pfx dosyası
  * . pfx için parola

Aşağıdaki istemciler, HLS 'yi **AES-128 CBC** şifrelemesi ile destekler: OS X, Apple TV, IOS üzerinde Safari.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>FairPlay dinamik şifrelemeyi ve lisans teslim hizmetlerini yapılandırma
Aşağıda, Media Services lisans teslim hizmetini ve ayrıca dinamik şifrelemeyi kullanarak FairPlay ile varlıklarınızı korumanın genel adımları verilmiştir.

1. Bir varlık oluşturun ve dosyaları varlığa yükleyin.
2. Dosyayı içeren varlığı, bit hızı uyarlamalı MP4 kümesine kodlayın.
3. Bir içerik anahtarı oluşturup kodlanmış varlıkla ilişkilendirin.  
4. İçerik anahtarının yetkilendirme ilkesini yapılandırın. Aşağıdakileri belirtin:

   * Teslim yöntemi (Bu durumda FairPlay).
   * FairPlay ilke seçenekleri yapılandırması. FairPlay 'in nasıl yapılandırılacağı hakkında ayrıntılı bilgi için aşağıdaki örnekteki **ConfigureFairPlayPolicyOptions ()** yöntemine bakın.

     > [!NOTE]
     > Genellikle yalnızca bir sertifika kümesine ve SORMANıZ gerektiğinden, FairPlay ilke seçeneklerini yalnızca bir kez yapılandırmak isteyeceksiniz.
     >
     >
   * Kısıtlamalar (açık veya belirteç).
   * Anahtarın istemciye nasıl teslim edildiğini tanımlayan anahtar teslim türüne özgü bilgiler.
5. Varlık teslim ilkesini yapılandırın. Teslim ilkesi yapılandırması şunları içerir:

   * Teslim Protokolü (HLS).
   * Dinamik şifreleme türü (ortak CBC şifrelemesi).
   * Lisans alma URL 'SI.

     > [!NOTE]
     > FairPlay ve başka bir dijital Rights Management (DRM) sistemi ile şifrelenen bir akış sunmak istiyorsanız, ayrı teslim ilkeleri yapılandırmanız gerekir:
     >
     > * HTTP (DASH) üzerinde Common Encryption (CENC) (PlayReady + Widevine) ile dinamik Uyarlamalı akışı yapılandırmak için bir ıassetdeliverypolicy ve PlayReady ile Düzgünleştir
     > * HLS için FairPlay yapılandırma için başka bir ıassetdeliverypolicy
     >
     >
6. Akış URL’si almak için bir OnDemand bulucu oluşturun.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>FairPlay anahtar teslimini oynatıcı uygulamaları ile kullanma
İOS SDK 'Yı kullanarak oynatıcı uygulamaları geliştirebilirsiniz. FairPlay içeriğini oynatmak için, lisans değişim protokolünü uygulamanız gerekir. Bu protokol Apple tarafından belirtilmedi. Her bir uygulamaya, anahtar teslim istekleri gönderme işlemi yapılır. Media Services FairPlay anahtar teslimi hizmeti, aşağıdaki biçimde SPC 'nin bir www-form-URL kodlamalı gönderi iletisi olarak gelmesini bekler:

`spc=<Base64 encoded SPC>`

> [!NOTE]
> Azure Media Player FairPlay oynatmayı destekler. Daha fazla bilgi için bkz. [Azure Media Player belgeleri](https://amp.azure.net/libs/amp/latest/docs/index.html) .
>
>

## <a name="streaming-urls"></a>Akış URL 'Leri
Varlığınız birden fazla DRM ile şifrelendiyse, akış URL 'sinde bir şifreleme etiketi kullanmanız gerekir: (biçim = 'm 3u8-AAPL ', şifreleme = ' xxx ').

Aşağıdaki noktalara dikkat edilmelidir:

* Yalnızca sıfır veya bir şifreleme türü belirtilebilir.
* Varlığa yalnızca bir şifreleme uygulanmışsa, şifreleme türünün URL 'de belirtilmesi gerekmez.
* Şifreleme türü, büyük/küçük harfe duyarlıdır.
* Aşağıdaki şifreleme türleri belirtilebilir:  
  * **CENC**: ortak şifreleme (PlayReady veya Widevine)
  * **CBCS-AAPL**: Fairplay
  * **CBC**: AES zarf şifrelemesi

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

1. Geliştirme ortamınızı kurun ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun. 
2. App.config dosyanızda tanımlanan **appSettings**’e aşağıdaki öğeleri ekleyin:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Örnek

Aşağıdaki örnek, içeriğinizi FairPlay ile şifreli olarak sunmak için Media Services kullanma özelliğini gösterir. Bu işlevsellik, .NET için Azure Media Services SDK sürümü 3.6.0 eklenmiştir. 

Bu bölümde gösterilen kodu Program.cs dosyanızdaki kodun üzerine yazın.

>[!NOTE]
>Farklı AMS ilkeleri için sınır 1.000.000 ilkedir (örneğin, Bulucu ilkesi veya ContentKeyAuthorizationPolicy için). Uzun süre boyunca kullanılmak için oluşturulan bulucu ilkeleri gibi aynı günleri / erişim izinlerini sürekli olarak kullanıyorsanız, aynı ilke kimliğini kullanmalısınız (karşıya yükleme olmayan ilkeler için). Daha fazla bilgi için [Bu](media-services-dotnet-manage-entities.md#limit-access-policies) makaleye bakın.

Değişkenleri, giriş dosyalarınızın bulunduğu klasörlere işaret edecek şekilde güncelleştirdiğinizden emin olun.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                    };

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }


        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="next-steps-media-services-learning-paths"></a>Sonraki adımlar: Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
