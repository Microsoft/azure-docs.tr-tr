---
title: 'Azure VPN Gateway: P2S için & dışarı aktarma sertifikaları oluşturma: MakeCert'
description: Otomatik olarak imzalanan bir kök sertifika oluşturun, ortak anahtarı dışarı aktarın ve MakeCert kullanarak istemci sertifikaları oluşturun.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 55e22ebec5853d6b4f10b53be8e24f4dbebe4e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659786"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>MakeCert kullanarak noktadan siteye bağlantılar için sertifikaları oluşturma ve dışarı aktarma

Noktadan siteye bağlantılar kimlik doğrulaması için sertifikaları kullanır. Bu makalede, otomatik olarak imzalanan bir kök sertifika oluşturma ve MakeCert kullanarak istemci sertifikaları oluşturma işlemlerinin nasıl yapılacağı gösterilir. Farklı sertifika yönergeleri arıyorsanız bkz. [Sertifikalar-PowerShell](vpn-gateway-certificates-point-to-site.md) veya [Sertifikalar-Linux](vpn-gateway-certificates-point-to-site-linux.md).

Sertifikalarınızı oluşturmak için [Windows 10 PowerShell adımlarını](vpn-gateway-certificates-point-to-site.md) kullanmanız önerilir, bu MakeCert talimatlarını isteğe bağlı bir yöntem olarak sağlıyoruz. Her iki yöntemi kullanarak oluşturduğunuz Sertifikalar [desteklenen herhangi bir istemci işletim sistemine](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)yüklenebilir. Ancak, MakeCert aşağıdaki sınırlamalara sahiptir:

* MakeCert kullanım dışıdır. Bu, aracın herhangi bir noktada kaldırılabileceği anlamına gelir. MakeCert kullanarak oluşturmuş olduğunuz tüm sertifikalar, MakeCert artık kullanılabilir olmadığında etkilenmez. MakeCert yalnızca sertifikaları oluşturmak için kullanılır, doğrulama mekanizması olarak değil.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Otomatik olarak imzalanan kök sertifika oluşturma

Aşağıdaki adımlarda, MakeCert kullanarak otomatik olarak imzalanan bir sertifika oluşturma adımları gösterilmektedir. Bu adımlar dağıtım modeline özgü değildir. Bunlar hem Kaynak Yöneticisi hem de klasik için geçerlidir.

1. [MakeCert](/windows/win32/seccrypto/makecert)indirin ve yükleyin.
2. Yükleme sonrasında, genellikle makecert.exe yardımcı programını şu yolda bulabilirsiniz: ' C:\Program Files (x86) \Windows Kits\10\bin \<arch> '. Ancak, başka bir konuma yüklenmiş olabilir. Yönetici olarak bir komut istemi açın ve MakeCert yardımcı programının konumuna gidin. Doğru konum için ayarlama yaparak aşağıdaki örneği kullanabilirsiniz:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Bilgisayarınızdaki kişisel sertifika deposunda bir sertifika oluşturun ve bu sertifikayı oluşturun. Aşağıdaki örnek, P2S yapılandırılırken Azure 'a yüklediğiniz karşılık gelen bir *. cer* dosyası oluşturur. ' P2SRootCert ' ve ' P2SRootCert. cer ' değerini, sertifika için kullanmak istediğiniz adla değiştirin. Sertifika, ' sertifikalar-geçerli kullanıcı \ kişisel \ Sertifikalar ' yolunda bulunur.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Ortak anahtarı (. cer) dışa aktarma

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Aktarılmış. cer dosyası Azure 'a yüklenmelidir. Yönergeler için bkz. [Noktadan siteye bağlantı yapılandırma](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Ek bir güvenilen kök sertifika eklemek için makalenin [Bu bölümüne](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) bakın.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Otomatik olarak imzalanan sertifikayı ve özel anahtarı depolamak için dışarı aktarma (isteğe bağlı)

Otomatik olarak imzalanan kök sertifikayı dışarı aktarmak ve güvenli bir şekilde depolamak isteyebilirsiniz. Gerekirse, daha sonra başka bir bilgisayara yükleyebilir ve daha fazla istemci sertifikası oluşturabilir ya da başka bir. cer dosyasını dışarı aktarabilirsiniz. Otomatik olarak imzalanan kök sertifikayı. pfx olarak dışarı aktarmak için kök sertifikayı seçin ve [istemci sertifikasını dışarı aktarma](#clientexport)bölümünde açıklanan adımları kullanın.

## <a name="create-and-install-client-certificates"></a>İstemci sertifikaları oluşturma ve yüklemeyi

Otomatik olarak imzalanan sertifikayı doğrudan istemci bilgisayara yüklemeyin. Otomatik olarak imzalanan sertifikadan bir istemci sertifikası oluşturmanız gerekir. Daha sonra istemci sertifikasını dışarı aktarıp istemci bilgisayara yüklersiniz. Aşağıdaki adımlar dağıtım modeline özgü değildir. Bunlar hem Kaynak Yöneticisi hem de klasik için geçerlidir.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>İstemci sertifikası oluşturma

Noktadan Siteye bağlantı kullanarak bir sanal ağa bağlanan her istemci bilgisayarda bir istemci sertifikası yüklü olmalıdır. Otomatik olarak imzalanan kök sertifikadan bir istemci sertifikası oluşturur ve ardından istemci sertifikasını dışarı aktarıp yükleyebilirsiniz. İstemci sertifikası yüklü değilse, kimlik doğrulaması başarısız olur. 

Aşağıdaki adımlarda, otomatik olarak imzalanan kök sertifikadan bir istemci sertifikası oluşturma işleminde size yol gösterilmektedir. Aynı kök sertifikadan birden çok istemci sertifikası oluşturabilirsiniz. Aşağıdaki adımları kullanarak istemci sertifikaları oluşturduğunuzda, istemci sertifikası sertifikayı oluşturmak için kullandığınız bilgisayara otomatik olarak yüklenir. İstemci sertifikasını başka bir istemci bilgisayara yüklemek istiyorsanız sertifikayı dışarı aktarabilirsiniz.
 
1. Otomatik olarak imzalanan sertifikayı oluşturmak için kullandığınız bilgisayarda, yönetici olarak bir komut istemi açın.
2. Bir istemci sertifikası oluşturmak için örneği değiştirin ve çalıştırın.
   * *"P2SRootCert"* öğesini, istemci sertifikasını oluşturduğunuz otomatik olarak imzalanan kök adına değiştirin. Kök sertifikanın adını kullandığınızdan emin olun. Bu, otomatik olarak imzalanan kök oluşturduğunuzda ' CN = ' değerinin belirtdikiydi.
   * *P2SChildCert* öğesini, bir istemci sertifikası oluşturmak istediğiniz adla değiştirin.

   Aşağıdaki örneği değiştirmeden çalıştırırsanız, sonuç, P2SChildcert adlı ve kök sertifikasından oluşturulan kişisel sertifika deponuzda bulunan bir istemci sertifikasıdır.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>İstemci sertifikasını dışarı aktarma

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Dışarı aktarılan bir istemci sertifikasını yükleme

İstemci sertifikası yüklemek için bkz. [istemci sertifikası yüklemesi](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Sonraki adımlar

Noktadan siteye yapılandırma ile devam edin. 

* **Kaynak Yöneticisi** dağıtım modeli adımları için bkz. [Yerel Azure sertifikası KIMLIK doğrulaması kullanarak P2S yapılandırma](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* **Klasik** dağıtım modeli adımları için bkz. [VNET 'e Noktadan siteye VPN bağlantısı yapılandırma (klasik)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

P2S sorun giderme bilgileri için [Azure noktadan siteye bağlantıları sorununu giderme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).