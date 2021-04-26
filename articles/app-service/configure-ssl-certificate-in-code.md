---
title: Kodda bir TLS/SSL sertifikası kullan
description: Kodunuzda istemci sertifikalarının nasıl kullanılacağını öğrenin. İstemci sertifikası ile uzak kaynaklarla kimlik doğrulaması yapın veya bunlarla şifreleme görevlerini çalıştırın.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 294587fde846a3774f7d74f64029e0bca00e9c08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829413"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Azure App Service'te kodunuzda TLS/SSL sertifikası kullanma

Uygulama kodunuzda, [App Service için eklediğiniz ortak veya özel sertifikalara](configure-ssl-certificate.md)erişebilirsiniz. Uygulama kodunuz istemci olarak davranabilir ve sertifika kimlik doğrulaması gerektiren bir dış hizmete erişebilir ya da şifreleme görevlerini gerçekleştirmek zorunda kalabilir. Bu nasıl yapılır kılavuzunda, uygulama kodunuzda ortak veya özel sertifikaların nasıl kullanılacağı gösterilmektedir.

Kodunuzda sertifika kullanmaya yönelik bu yaklaşım, uygulamanızın **temel** katmanda veya üzerinde olmasını GEREKTIREN App Service TLS işlevselliğini kullanır. Uygulamanız **ücretsiz** veya **paylaşılan** katmanındaysa, [sertifika dosyasını uygulama deponuza dahil](#load-certificate-from-file)edebilirsiniz.

TLS/SSL sertifikalarınızı App Service yönettiğinizde, sertifikaları ve uygulama kodunuzu ayrı olarak koruyabilir ve hassas verilerinizi koruyabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu izlemek için:

- [App Service uygulaması oluşturma](./index.yml)
- [Uygulamanıza bir sertifika ekleyin](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Parmak izini bulma

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>, sol menüden **uygulama hizmetleri**' ni seçin  >  **\<app-name>** .

Uygulamanızın sol gezinti bölmesinde, **TLS/SSL ayarları**' nı seçin ve ardından **özel anahtar sertifikaları (. pfx** ) veya **ortak anahtar sertifikaları (. cer)** seçeneğini belirleyin.

Kullanmak istediğiniz sertifikayı bulun ve parmak izini kopyalayın.

![Sertifika parmak izini kopyalama](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Sertifikayı erişilebilir yapma

Uygulama kodunuzda bir sertifikaya erişmek için, `WEBSITE_LOAD_CERTIFICATES` <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>aşağıdaki komutu çalıştırarak parmak izini uygulama ayarına ekleyin:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Tüm sertifikalarınızı erişilebilir hale getirmek için değerini olarak ayarlayın `*` .

## <a name="load-certificate-in-windows-apps"></a>Windows uygulamalarında sertifika yükleme

`WEBSITE_LOAD_CERTIFICATES`Uygulama ayarı, belirtilen sertifikaları Windows sertifika deposundaki Windows barındırılan uygulamanız tarafından erişilebilir hale getirir. [](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)

C# kodunda sertifika parmak izine göre sertifikaya erişirsiniz. Aşağıdaki kod parmak izine sahip bir sertifika yükler `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

Java kodunda, "Windows-MY" mağazasındaki sertifikaya, konu ortak adı alanını kullanarak erişirsiniz (bkz. [ortak anahtar sertifikası](https://en.wikipedia.org/wiki/Public_key_certificate)). Aşağıdaki kod, bir özel anahtar sertifikasının nasıl yükleneceğini göstermektedir:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Windows sertifika deposu için desteklemeyen veya desteklemeyen diller için bkz. [dosyadan sertifika yükleme](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Sertifikayı dosyadan yükle

El ile karşıya yüklediğiniz bir sertifika dosyası yüklemeniz gerekiyorsa, örneğin [Git](deploy-local-git.md)yerine [FTPS](deploy-ftp.md) 'yi kullanarak sertifikayı karşıya yüklemek daha iyidir. Gizli verileri, kaynak denetiminden özel bir sertifika gibi tutmanız gerekir.

> [!NOTE]
> Windows üzerinde ASP.NET ve ASP.NET Core, bir dosyadan sertifika yükleseniz bile sertifika deposuna erişmelidir. Bir Windows .NET uygulamasına bir sertifika dosyası yüklemek için, geçerli kullanıcı profilini <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>aşağıdaki komutla yükleyin:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Kodunuzda sertifika kullanmaya yönelik bu yaklaşım, uygulamanızın **temel** katmanda veya üzerinde olmasını GEREKTIREN App Service TLS işlevselliğini kullanır.

Aşağıdaki C# örneği, uygulamanızda göreli bir yoldan ortak bir sertifika yükler:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Node.js, PHP, Python, Java veya Ruby içindeki bir dosyadan bir TLS/SSL sertifikası yüklemeyi öğrenmek için ilgili dile veya Web platformuna yönelik belgelere bakın.

## <a name="load-certificate-in-linuxwindows-containers"></a>Linux/Windows kapsayıcılarındaki sertifikayı yükle

`WEBSITE_LOAD_CERTIFICATES`Uygulama ayarları, belirtilen sertifikaları Windows veya Linux kapsayıcı uygulamalarınız (yerleşik Linux kapsayıcıları dahil) dosya olarak erişilebilir hale getirir. Dosyalar aşağıdaki dizinler altında bulunur:

| Kapsayıcı platformu | Ortak sertifikalar | Özel sertifikalar |
| - | - | - |
| Windows kapsayıcısı | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Linux kapsayıcısı | `/var/ssl/certs` | `/var/ssl/private` |

Sertifika dosyası adları, sertifika parmak izlerdir. 

> [!NOTE]
> App Service, sertifika yollarını aşağıdaki ortam değişkenleri,, ve gibi Windows kapsayıcılarına ekleyebilir `WEBSITE_PRIVATE_CERTS_PATH` `WEBSITE_INTERMEDIATE_CERTS_PATH` `WEBSITE_PUBLIC_CERTS_PATH` `WEBSITE_ROOT_CERTS_PATH` . Sertifika yollarının ileride değişiklik yaptığı durumlarda sertifika yolunu sabit kodlamak yerine ortam değişkenlerine başvurmak daha iyidir.
>

Ayrıca, [Windows Server Core kapsayıcıları](configure-custom-container.md#supported-parent-images) sertifikaları otomatik olarak sertifika deposuna yükler, **localmachine\.** Sertifikaları yüklemek için [Windows uygulamalarında yükleme sertifikası](#load-certificate-in-windows-apps)ile aynı modele uyun. Windows nano tabanlı kapsayıcılar için, [sertifikayı doğrudan dosyadan yüklemek](#load-certificate-from-file)üzere yukarıda belirtilen dosya yollarını kullanın.

Aşağıdaki C# kodu, bir Linux uygulamasına ortak bir sertifikanın nasıl yükleneceğini göstermektedir.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Aşağıdaki C# kodu bir Linux uygulamasında özel bir sertifikanın nasıl yükleneceğini gösterir.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;
...
var bytes = File.ReadAllBytes("/var/ssl/private/<thumbprint>.p12");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Node.js, PHP, Python, Java veya Ruby içindeki bir dosyadan bir TLS/SSL sertifikası yüklemeyi öğrenmek için ilgili dile veya Web platformuna yönelik belgelere bakın.

## <a name="more-resources"></a>Diğer kaynaklar

* [Azure App Service 'de TLS/SSL bağlaması ile özel bir DNS adının güvenliğini sağlama](configure-ssl-bindings.md)
* [HTTPS'yi zorunlu tutma](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 zorlama](configure-ssl-bindings.md#enforce-tls-versions)
* [SSS: sertifikalar App Service](./faq-configuration-and-management.md)
