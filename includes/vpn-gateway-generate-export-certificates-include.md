---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/10/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 89fa06dda418f328b3bc07aada49aa347e35220a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182298"
---
## <a name="rootcert"></a>Otomatik olarak imzalanan kök sertifika oluşturma

Otomatik olarak imzalanan bir kök sertifika oluşturmak için New-SelfSignedCertificate cmdlet 'ini kullanın. Ek parametre bilgileri için, bkz. [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Windows 10 veya Windows Server 2016 çalıştıran bir bilgisayardan yükseltilmiş ayrıcalıklarla bir Windows PowerShell konsolu açın. Bu örnekler, "TRY It" Azure Cloud Shell çalışmaz. Bu örnekleri yerel olarak çalıştırmanız gerekir.
2. Otomatik olarak imzalanan kök sertifika oluşturmak için aşağıdaki örneği kullanın. Aşağıdaki örnek, ' sertifikalar-geçerli kullanıcı \ kişisel \ Sertifikalar ' ' a otomatik olarak yüklenen ' P2SRootCert ' adlı otomatik olarak imzalanan bir kök sertifika oluşturur. Sertifikayı *certmgr. msc*' yi açarak veya *Kullanıcı sertifikalarını yönettiğinizde*görüntüleyebilirsiniz.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>İstemci sertifikası oluştur

Noktadan Siteye bağlantı kullanarak bir sanal ağa bağlanan her istemci bilgisayarda bir istemci sertifikası yüklü olmalıdır. Otomatik olarak imzalanan kök sertifikadan bir istemci sertifikası oluşturur ve ardından istemci sertifikasını dışarı aktarıp yükleyebilirsiniz. İstemci sertifikası yüklü değilse, kimlik doğrulaması başarısız olur. 

Aşağıdaki adımlarda, otomatik olarak imzalanan kök sertifikadan bir istemci sertifikası oluşturma işleminde size yol gösterilmektedir. Aynı kök sertifikadan birden çok istemci sertifikası oluşturabilirsiniz. Aşağıdaki adımları kullanarak istemci sertifikaları oluşturduğunuzda, istemci sertifikası sertifikayı oluşturmak için kullandığınız bilgisayara otomatik olarak yüklenir. İstemci sertifikasını başka bir istemci bilgisayara yüklemek istiyorsanız sertifikayı dışarı aktarabilirsiniz.

Örnekler, bir yılda süresi dolan bir istemci sertifikası oluşturmak için New-SelfSignedCertificate cmdlet 'ini kullanır. İstemci sertifikası için farklı bir süre sonu değeri ayarlama gibi ek parametre bilgileri için, bkz. [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Örnek 1

Otomatik olarak imzalanan kök sertifikayı oluşturduktan sonra PowerShell konsolunuzu kapatmamışsa bu örneği kullanın. Bu örnek önceki bölümden devam eder ve belirtilen ' $cert ' değişkenini kullanır. Otomatik olarak imzalanan kök sertifikayı oluşturduktan sonra PowerShell konsolunu kapattıysanız veya yeni bir PowerShell konsolu oturumunda ek istemci sertifikaları oluşturuyorsanız, [örnek 2](#ex2)' deki adımları kullanın.

Bir istemci sertifikası oluşturmak için örneği değiştirin ve çalıştırın. Aşağıdaki örneği değiştirmeden çalıştırırsanız, sonuç ' P2SChildCert ' adlı bir istemci sertifikasıdır.  Alt sertifikayı başka bir şekilde adlandırmak istiyorsanız, CN değerini değiştirin. Bu örneği çalıştırırken TextExtension 'ı değiştirmeyin. Oluşturduğunuz istemci sertifikası, bilgisayarınızdaki ' sertifikalar-geçerli kullanıcı \ kişisel \ Sertifikalar ' ' a otomatik olarak yüklenir.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Örnek 2

Ek istemci sertifikaları oluşturuyorsanız veya otomatik olarak imzalanan kök sertifikanızı oluşturmak için kullandığınız PowerShell oturumunu kullanmıyorsanız, aşağıdaki adımları kullanın:

1. Bilgisayarda yüklü olan otomatik olarak imzalanan kök sertifikayı belirler. Bu cmdlet, bilgisayarınızda yüklü olan sertifikaların listesini döndürür.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. Döndürülen listeden konu adını bulun, sonra da yanındaki parmak izini bir metin dosyasına kopyalayın. Aşağıdaki örnekte, iki sertifika vardır. CN adı, alt sertifika oluşturmak istediğiniz otomatik olarak imzalanan kök sertifikanın adıdır. Bu durumda, ' P2SRootCert '.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Önceki adımdaki parmak izini kullanarak kök sertifika için bir değişken bildirin. PARMAK IZINI, alt sertifika oluşturmak istediğiniz kök sertifikanın parmak izine değiştirin.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Örneğin, önceki adımda P2SRootCert için parmak izini kullanarak değişken şöyle görünür:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Bir istemci sertifikası oluşturmak için örneği değiştirin ve çalıştırın. Aşağıdaki örneği değiştirmeden çalıştırırsanız, sonuç ' P2SChildCert ' adlı bir istemci sertifikasıdır. Alt sertifikayı başka bir şekilde adlandırmak istiyorsanız, CN değerini değiştirin. Bu örneği çalıştırırken TextExtension 'ı değiştirmeyin. Oluşturduğunuz istemci sertifikası, bilgisayarınızdaki ' sertifikalar-geçerli kullanıcı \ kişisel \ Sertifikalar ' ' a otomatik olarak yüklenir.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>Kök sertifika ortak anahtarını (. cer) dışa aktarma

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Otomatik olarak imzalanan kök sertifikayı ve özel anahtarı depolamak için dışarı aktarma (isteğe bağlı)

Otomatik olarak imzalanan kök sertifikayı dışarı aktarmak ve yedekleme olarak güvenli bir şekilde depolamak isteyebilirsiniz. Gerekirse, daha sonra başka bir bilgisayara yükleyebilir ve daha fazla istemci sertifikası oluşturabilirsiniz. Otomatik olarak imzalanan kök sertifikayı. pfx olarak dışarı aktarmak için kök sertifikayı seçin ve [istemci sertifikasını dışarı aktarma](#clientexport)bölümünde açıklanan adımları kullanın.

## <a name="clientexport"></a>İstemci sertifikasını dışarı aktarma

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
