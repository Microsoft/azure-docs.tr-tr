---
title: Microsoft Azure Cloud Services SSS için yapılandırma ve yönetim sorunları | Microsoft Docs
description: Bu makalede Microsoft Azure Cloud Services yapılandırma ve yönetimi hakkında sık sorulan sorular listelenmektedir.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 704d6d4a12550507a8e38be4777e5abc7b57fe74
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161756"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services için yapılandırma ve yönetim sorunları: sık sorulan sorular (SSS)

Bu makalede [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)yapılandırma ve yönetim sorunları hakkında sık sorulan sorular yer almaktadır. Boyut bilgileri için [Cloud SERVICES VM boyutu sayfasına](cloud-services-sizes-specs.md) de başvurabilirsiniz.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Sertifikalar**

- [Bulut hizmeti SSL sertifikamın sertifika zinciri neden eksik?](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- ["Uzantılar için Windows Azure Araçları şifreleme sertifikası" amacı nedir?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Örnek içinde "RDP-ing" olmadan bir sertifika Imzalama Isteği (CSR) nasıl oluşturabilirim?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Bulut hizmeti yönetim sertifikamın süresi doluyor. Nasıl yenilenebilirim?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Ana SSL sertifikası (. pfx) ve ara sertifika (. p7b) yüklemesini otomatikleştirme](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- ["MachineKey için Microsoft Azure Service Management" sertifikası amacı nedir?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**İzleme ve günlüğe kaydetme**

- [Uygulamaları yönetmeye ve izlemeye yardımcı olabilecek Azure portal yakında sunulan bulut hizmeti özellikleri nelerdir?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [IIS neden günlük dizinine yazmayı durdurur?](#why-does-iis-stop-writing-to-the-log-directory)
- [Nasıl yaparım? Cloud Services için WAD günlüğü etkinleştirilsin mi?](#how-do-i-enable-wad-logging-for-cloud-services)

**Ağ yapılandırması**

- [Azure Yük Dengeleyici için boşta kalma zaman aşımını Nasıl yaparım? mı?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Nasıl yaparım? statik bir IP adresini bulut hizmetim ile ilişkilendirsin mi?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Azure temel IP/KIMLIKLERININ ve DDOS 'nin sağladığı özellikler ve yetenekler nelerdir?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Cloud Services VM 'de HTTP/2 nasıl etkinleştirilir?](#how-to-enable-http2-on-cloud-services-vm)

**İzinler**

- [Microsoft tarafından bulut hizmeti örneklerine izin verilmeden Microsoft iç mühendisleri uzak masaüstü olabilir mi?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [RDP dosyasını kullanarak bulut hizmeti sanal makinesine uzak masaüstü oluşturamıyorum. Şu hatayı alıyorum: bir kimlik doğrulama hatası oluştu (kod: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Ölçeklendirme**

- [X örneklerinin ötesine ölçeklendiremiyorum](#i-cannot-scale-beyond-x-instances)
- [Bellek ölçümlerine göre otomatik ölçeklendirmeyi nasıl yapılandırabilirim?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Yorlar**

- ["Noalgılamasına f" i web siteme Nasıl yaparım? eklensin mi?](#how-do-i-add-nosniff-to-my-website)
- [Web rolü için IIS 'yi özelleştirmek Nasıl yaparım??](#how-do-i-customize-iis-for-a-web-role)
- [Bulut hizmetimin kota sınırı nedir?](#what-is-the-quota-limit-for-my-cloud-service)
- [Bulut hizmeti sanal makinemdeki sürücü neden çok az boş disk alanı gösteriyor?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Cloud Services bir kötü amaçlı yazılımdan koruma uzantısını otomatik bir şekilde nasıl ekleyebilirim?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Cloud Services için Sunucu Adı Belirtme (SNı) nasıl etkinleştirilir?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Azure bulut hizmetinize nasıl etiket ekleyebilirim?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Azure portal bulut hizmetmin SDK sürümünü görüntülemez. Bunu nasıl alabilirim?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Bulut hizmetini birkaç ay boyunca kapatmak istiyorum. Bulut hizmetinin fatura maliyeti, IP adresini kaybetmeksizin nasıl azaltılıyor?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Sertifikalar

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Bulut hizmeti SSL sertifikamın sertifika zinciri neden eksik?
    
Müşterilerin yalnızca yaprak sertifika yerine tam sertifika zincirini (yaprak sertifikası, ara sertifikalar ve kök sertifika) yüklemesini öneririz. Yalnızca yaprak sertifikayı yüklediğinizde, CTL 'yi yürüyerek sertifika zincirini oluşturmak için Windows 'u temel alırsınız. Azure 'da aralıklı ağ veya DNS sorunları oluşursa veya Windows sertifikayı doğrulamaya çalışırken Windows Update, sertifika geçersiz kabul edilebilir. Tam sertifika zincirini yükleyerek bu sorunun kaçınılması gerekir. [ZINCIRLEME SSL sertifikasının nasıl yükleneceğini](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) gösteren blog bunun nasıl yapılacağını gösterir.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>"Uzantılar için Windows Azure Araçları şifreleme sertifikası" amacı nedir?

Bu sertifikalar, bulut hizmetine her uzantı eklendiğinde otomatik olarak oluşturulur. En yaygın olarak, bu WAD uzantısıdır veya RDP uzantısıdır, ancak kötü amaçlı yazılımdan koruma veya günlük Toplayıcı uzantısı gibi diğerleri olabilir. Bu sertifikalar yalnızca uzantının özel yapılandırmasını şifrelemek ve şifresini çözmek için kullanılır. Sona erme tarihi hiçbir zaman denetlenmez, bu nedenle sertifikanın süresinin dolmasının önemi yoktur. 

Bu sertifikaları yoksayabilirsiniz. Sertifikaları temizlemek istiyorsanız tümünü silmeyi deneyebilirsiniz. Kullanımda olan bir sertifikayı silmeye çalışırsanız Azure, bir hata oluşturur.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Örnek içinde "RDP-ing" olmadan bir sertifika Imzalama Isteği (CSR) nasıl oluşturabilirim?

Aşağıdaki kılavuz belgesine bakın:

[Windows Azure Web siteleri (WAWS) ile kullanmak için sertifika alma](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR yalnızca bir metin dosyasıdır. Sertifikanın son olarak kullanılacağı makineden oluşturulması gerekmez. Bu belge bir App Service için yazılsa da, CSR oluşturma geneldir ve Cloud Services için de geçerlidir.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Bulut hizmeti yönetim sertifikamın süresi doluyor. Nasıl yenilenebilirim?

Yönetim sertifikalarınızı yenilemek için aşağıdaki PowerShell komutlarını kullanabilirsiniz:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Get-Azuikinci dosya SettingsFile** , **Azure Portal > ** **Yönetim sertifikalarında** yeni bir yönetim sertifikası oluşturacaktır. Yeni sertifikanın adı "YourSubscriptionNam]-[CurrentDate]-Credentials" şeklinde görünür.

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Ana SSL sertifikası (. pfx) ve ara sertifika (. p7b) yüklemesini otomatikleştirme

Bu görevi, bir başlangıç betiği (Batch/cmd/PowerShell) kullanarak otomatikleştirebilir ve bu başlangıç betiğini hizmet tanımı dosyasına kaydedebilirsiniz. Başlangıç betiğinin aynı dizinindeki proje klasörüne hem başlangıç betiği hem de sertifika (. p7b dosyası) ekleyin.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>"MachineKey için Microsoft Azure Service Management" sertifikası amacı nedir?

Bu sertifika, Azure Web rolleri üzerinde makine anahtarlarını şifrelemek için kullanılır. Daha fazla bilgi edinmek için [Bu danışma belgesine](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731)göz atın.

Daha fazla bilgi için aşağıdaki makalelere bakın:
- [Bulut hizmeti için başlangıç görevlerini yapılandırma ve çalıştırma](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Ortak bulut hizmeti başlangıç görevleri](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>İzleme ve günlüğe kaydetme

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Uygulamaları yönetmeye ve izlemeye yardımcı olabilecek Azure portal yakında sunulan bulut hizmeti özellikleri nelerdir?

Uzak Masaüstü Protokolü (RDP) için yeni bir sertifika oluşturma özelliği yakında kullanıma sunulacak. Alternatif olarak, bu betiği çalıştırabilirsiniz:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Yalnızca bir blob veya yerel olarak, csdef ve cscfg karşıya yükleme konumunuz için yerel ' i seçebilme. [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0)kullanarak her bir konum değerini belirleyebilirsiniz.

Örnek düzeyinde ölçümleri izleme özelliği. Ek izleme özellikleri [Cloud Services izleme](cloud-services-how-to-monitor.md)bölümünde bulunur.

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>IIS neden günlük dizinine yazmayı durdurur?
Günlük dizinine yazmak için yerel depolama kotasını tüketmiş olursunuz. Bunu düzeltmek için üç işlemlerden birini yapabilirsiniz:
* IIS için tanılamayı etkinleştirin ve tanılamayı düzenli aralıklarla blob depolamaya taşındığını sağlayın.
* Günlük dosyalarını günlük dizininden el ile kaldırın.
* Yerel kaynaklar için kota sınırını artırın.

Daha fazla bilgi için aşağıdaki belgelere bakın:
* [Azure Depolama’daki tanılama verilerini depolama ve görüntüleme](cloud-services-dotnet-diagnostics-storage.md)
* [IIS günlükleri, bulut hizmeti 'nde yazmayı durdurur](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Nasıl yaparım? Cloud Services için WAD günlüğü etkinleştirilsin mi?
Aşağıdaki seçenekler aracılığıyla Windows Azure Tanılama (WAD) günlük kaydını etkinleştirebilirsiniz:
1. [Visual Studio 'dan etkinleştir](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [.NET kodu üzerinden etkinleştir](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [PowerShell aracılığıyla etkinleştir](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Bulut hizmetinizin geçerli WAD ayarlarını almak için [Get-Azurezervicediagnokısextensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) PS cmd komutunu kullanabilir veya "Cloud Services--> Extensions" dikey penceresinden Portal üzerinden görüntüleyebilirsiniz.


## <a name="network-configuration"></a>Ağ yapılandırması

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Azure Yük Dengeleyici için boşta kalma zaman aşımını Nasıl yaparım? mı?
Hizmet tanımınızda (csdef) zaman aşımını şu şekilde belirtebilirsiniz:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Daha fazla bilgi için bkz. [Yeni: yapılandırılabilir boşta zaman aşımı Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) .

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Nasıl yaparım? statik bir IP adresini bulut hizmetim ile ilişkilendirsin mi?
Statik bir IP adresi ayarlamak için ayrılmış bir IP oluşturmanız gerekir. Bu ayrılmış IP, yeni bir bulut hizmeti veya var olan bir dağıtım ile ilişkilendirilebilir. Ayrıntılar için aşağıdaki belgelere bakın:
* [Ayrılmış IP adresi oluşturma](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Mevcut bir bulut hizmetinin IP adresini ayır](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Ayrılmış IP 'yi yeni bir bulut hizmeti ile ilişkilendir](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Ayrılmış bir IP 'yi çalışan bir dağıtım ile ilişkilendir](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Hizmet yapılandırma dosyası kullanarak ayrılmış IP 'yi bulut hizmetiyle ilişkilendirme](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Azure temel IP/KIMLIKLERININ ve DDOS 'nin sağladığı özellikler ve yetenekler nelerdir?
Azure, tehditlere karşı savunmak için veri merkezi fiziksel sunucularındaki IP 'leri/KIMLIKLERI vardır. Ayrıca, müşteriler web uygulaması güvenlik duvarları, ağ güvenlik duvarları, kötü amaçlı yazılım, yetkisiz giriş algılama, önleme sistemleri (KIMLIKLER/IP 'ler) ve daha fazlası gibi üçüncü taraf güvenlik çözümlerini dağıtabilir. Daha fazla bilgi için bkz. [verilerinizi ve varlıklarınızı koruma ve genel güvenlik standartları ile uyumlu](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft, tehditleri algılamak için sunucuları, ağları ve uygulamaları sürekli olarak izler. Azure 'un multipronged tehdit yönetimi yaklaşımı, yetkisiz kullanım algılama, dağıtılmış hizmet reddi (DDoS) saldırısı önleme, sızma testi, davranış analizi, anomali algılama ve makine öğrenimini kullanarak savunmayı sürekli güçlendirin ve riskleri azaltır. Azure için Microsoft kötü amaçlı yazılımdan koruma, Azure Cloud Services ve sanal makinelerini korur. Web uygulaması ateş duvarları, ağ güvenlik duvarları, kötü amaçlı yazılım, yetkisiz giriş algılama ve önleme sistemleri (KIMLIKLER/IP 'ler) ve daha fazlası gibi üçüncü taraf güvenlik çözümlerini de dağıtma seçeneğiniz vardır.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Cloud Services VM 'de HTTP/2 nasıl etkinleştirilir?

Windows 10 ve Windows Server 2016 hem istemci hem de sunucu tarafında HTTP/2 desteğiyle birlikte gelir. İstemciniz (tarayıcı), TLS uzantıları aracılığıyla HTTP/2 ' yi görüşür ve TLS üzerinden IIS sunucusuna bağlanıyorsa, sunucu tarafında herhangi bir değişiklik yapmanız gerekmez. Bunun nedeni, TLS üzerinden HTTP/2 kullanımını belirten H2-14 üst bilgisi varsayılan olarak gönderilir. Diğer taraftan, istemciniz HTTP/2 ' ye yükseltmek için bir yükseltme üst bilgisi gönderiyorsa, yükseltmenin çalıştığından ve bir HTTP/2 bağlantısı ile sonlandırtığınızdan emin olmak için sunucu tarafında aşağıdaki değişikliği yapmanız gerekir. 

1. Regedit. exe ' yi çalıştırın.
2. Kayıt defteri anahtarına gidin: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. **Duoenabled**adlı yenı bir DWORD değeri oluşturun.
4. Değerini 1 olarak ayarlayın.
5. Sunucunuzu yeniden başlatın.
6. **Varsayılan Web sitenize** gidin ve **bağlamalar**' ın altında, yeni oluşturulan otomatik olarak Imzalanan sertifikayla yeni bir TLS bağlaması oluşturun. 

Daha fazla bilgi için bkz.

- [IIS üzerinde HTTP/2](https://blogs.iis.net/davidso/http2)
- [Video: Windows 10 ' da HTTP/2: tarayıcı, uygulamalar ve Web sunucusu](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Bu adımlar bir başlangıç göreviyle otomatikleştirilebilir, böylece her yeni PaaS örneği oluşturulduğunda bu değişiklikler sistem kayıt defterinde yukarıdaki değişiklikleri gerçekleştirebilir. Daha fazla bilgi için bkz. [bulut hizmeti için başlangıç görevlerini yapılandırma ve çalıştırma](cloud-services-startup-tasks.md).

 
Bu işlem yapıldıktan sonra, aşağıdaki yöntemlerden birini kullanarak HTTP/2 ' nin etkinleştirilip etkinleştirilmeyeceğini doğrulayabilirsiniz:

- IIS günlüklerinde protokol sürümünü etkinleştirin ve IIS günlüklerine bakın. Günlüklerde HTTP/2 gösterilecektir. 
- Internet Explorer veya Microsoft Edge 'de F12 geliştirici aracını etkinleştirin ve Protokolü doğrulamak için Ağ sekmesine geçin. 

Daha fazla bilgi için bkz. [IIS 'de http/2](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>İzinler

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Cloud Services için rol tabanlı erişimi nasıl uygulayabilirim?
Cloud Services, Azure Resource Manager tabanlı bir hizmet olmadığı için rol tabanlı erişim denetimi (RBAC) modelini desteklemez.

Bkz. [Azure 'daki farklı rolleri anlayın](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Uzak Masaüstü

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Microsoft tarafından bulut hizmeti örneklerine izin verilmeden Microsoft iç mühendisleri uzak masaüstü olabilir mi?
Microsoft, sahip veya kendi designee 'den, yazılı izin (e-posta veya başka yazılı iletişim) olmadan bulut hizmetinize iç mühendislere izin vermeyecek katı bir işlem izler.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>RDP dosyasını kullanarak bulut hizmeti sanal makinesine uzak masaüstü oluşturamıyorum. Şu hatayı alıyorum: bir kimlik doğrulama hatası oluştu (kod: 0x80004005)

Bu hata, Azure Active Directory katılmış bir makineden RDP dosyasını kullanıyorsanız ortaya çıkabilir. Bu sorunu çözmek için şu adımları izleyin:

1. İndirdiğiniz RDP dosyasına sağ tıklayın ve ardından **Düzenle**' yi seçin.
2. Kullanıcı adından&#92;önce önek olarak "" ekleyin. Örneğin, **Kullanıcı adı**yerine **.\username** kullanın.

## <a name="scaling"></a>Ölçeklendirme

### <a name="i-cannot-scale-beyond-x-instances"></a>X örneklerinin ötesine ölçeklendiremiyorum
Azure aboneliğinizin kullanabileceğiniz çekirdek sayısı sınırlıdır. Kullanılabilir tüm çekirdekleri kullandıysanız ölçekleme çalışmayacaktır. Örneğin, 100 çekirdek sınırına sahipseniz, bu, bulut hizmetiniz için 100 a1 boyutlu sanal makine örneklerine veya 50 a2 boyutlu sanal makine örneklerine sahip olabileceği anlamına gelir.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Bellek ölçümlerine göre otomatik ölçeklendirmeyi nasıl yapılandırabilirim?

Cloud Services için bellek ölçümlerine göre otomatik ölçeklendirme Şu anda desteklenmiyor. 

Bu sorunu geçici olarak çözmek için Application Insights kullanabilirsiniz. Otomatik ölçeklendirme, ölçüm kaynağı olarak Application Insights destekler ve "bellek" gibi Konuk ölçümüne göre rol örneği sayısını ölçeklendirebilir.  Bulut hizmeti proje paketi dosyanızdaki (*. cspkg) Application Insights yapılandırıp bu Fede uygulamak için hizmette Azure Tanılama uzantısını etkinleştirmeniz gerekir.

Cloud Services otomatik ölçeklendirmeyi yapılandırmak için Application Insights aracılığıyla özel bir ölçümü kullanma hakkında daha fazla bilgi için bkz. [Azure 'da özel ölçüm ile otomatik ölçeklendirmeyi kullanmaya başlama](../azure-monitor/platform/autoscale-custom-metric.md)

Cloud Services için Application Insights Azure Tanılama tümleştirme hakkında daha fazla bilgi için bkz. [bulut hizmeti, sanal makine veya Service Fabric Tanılama verileri gönderme Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Cloud Services için Application Insights etkinleştirme hakkında daha fazla bilgi için bkz. [Azure için Application Insights Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Cloud Services için Azure Tanılama günlüğe kaydetmenin nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Azure Cloud Services ve sanal makineler için tanılamayı ayarlama](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Yorlar

### <a name="how-do-i-add-nosniff-to-my-website"></a>"Noalgılamasına f" i web siteme Nasıl yaparım? eklensin mi?
İstemcilerin MIME türlerini algılaması için, *Web. config* dosyanıza bir ayar ekleyin.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Bunu IIS 'de bir ayar olarak da ekleyebilirsiniz. [Ortak Başlangıç görevleri](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) makalesinde aşağıdaki komutu kullanın.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Web rolü için IIS 'yi özelleştirmek Nasıl yaparım??
[Genel başlangıç görevleri](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) makalesindeki IIS başlangıç betiğini kullanın.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Bulut hizmetimin kota sınırı nedir?
Bkz. [hizmete özgü sınırlar](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Bulut hizmeti sanal makinemdeki sürücü neden çok az boş disk alanı gösteriyor?
Bu beklenen davranıştır ve uygulamanızda herhangi bir soruna neden olmaz. Azure PaaS VM 'lerinde% AppRoot% sürücüsü için günlük kaydı etkinleştirilir ve bu, temelde dosyaların normalde kapladığı alan miktarını iki katına tüketir. Bununla birlikte, dikkat etmeniz gereken birkaç nokta vardır.

% AppRoot% sürücü boyutu, \<boyutu. cspkg + Max günlük boyutu + boş alan > veya 1,5 GB, hangisi daha büyük bir kenar boşluğu olarak hesaplanır. SANAL makinenizin boyutunun bu hesaplama için bir pul yok. (VM boyutu yalnızca geçici C: sürücüsünün boyutunu etkiler.) 

% AppRoot% sürücüsüne yazmak desteklenmez. Azure VM 'ye yazıyorsanız, bunu geçici bir LocalStorage kaynağında (veya blob depolama, Azure dosyaları vb. gibi diğer bir seçeneğe) yapmanız gerekir. Bu nedenle% AppRoot% klasöründeki boş alan miktarı anlamlı değildir. Uygulamanızın% AppRoot% sürücüsüne yazıyor olduğundan emin değilseniz, hizmetinizin birkaç gün boyunca her zaman çalışmasına izin verebilir ve "önce" ve "sonra" boyutlarını karşılaştırabilirsiniz. 

Azure,% AppRoot% sürücüsüne hiçbir şey yazmayacak. VHD,. cspkg 'nizden oluşturulduktan ve Azure VM 'ye takıldıktan sonra bu sürücüye yazgerekebilecek tek şey uygulamanız olur. 

Günlük ayarları yapılandırılamaz, bu nedenle devre dışı bırakabilirsiniz.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Cloud Services bir kötü amaçlı yazılımdan koruma uzantısını otomatik bir şekilde nasıl ekleyebilirim?

Başlatma görevinde PowerShell betiği kullanarak kötü amaçlı yazılımdan koruma uzantısını etkinleştirebilirsiniz. Uygulamak için bu makalelerdeki adımları izleyin: 
 
- [PowerShell başlangıç görevi oluşturma](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Kötü amaçlı yazılımdan koruma senaryoları ve portaldan nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [kötü amaçlı yazılımdan koruma senaryoları](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Cloud Services için Sunucu Adı Belirtme (SNı) nasıl etkinleştirilir?

Aşağıdaki yöntemlerden birini kullanarak Cloud Services SNı 'yi etkinleştirebilirsiniz:

**Yöntem 1: PowerShell kullanma**

SNı bağlama, aşağıdaki gibi bir bulut hizmeti rolü örneği için başlangıç görevinde **New-Webbinding** PowerShell cmdlet 'i kullanılarak yapılandırılabilir:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
[Burada](https://technet.microsoft.com/library/ee790567.aspx)açıklandığı gibi, $sslFlags aşağıdaki gibi değerlerden biri olabilir:

|Değer|Anlamı|
------|------
|0|SNı yok|
|1|SNı etkin|
|2|Merkezi sertifika deposu kullanan SNı bağlama|
|3|Merkezi sertifika deposu kullanan SNı bağlaması|
 
**Yöntem 2: kodu kullanma**

SNı bağlama, bu [blog gönderisine](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/)göre rol başlangıcında kod aracılığıyla da yapılandırılabilir:

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                    binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Yukarıdaki yaklaşımlardan herhangi birini kullanarak, belirli ana bilgisayar adları için ilgili sertifikaların (*. pfx) ilk olarak bir başlangıç görevi kullanılarak veya SNı bağlamasının etkili olabilmesi için kod aracılığıyla rol örneklerine yüklenmesi gerekir.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Azure bulut hizmetinize nasıl etiket ekleyebilirim? 

Bulut hizmeti, klasik bir kaynaktır. Yalnızca Azure Resource Manager tarafından oluşturulan kaynaklar etiketleri destekler. Bulut hizmeti gibi klasik kaynaklara Etiketler uygulayamazsınız. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Azure portal bulut hizmetmin SDK sürümünü görüntülemez. Bunu nasıl alabilirim?

Bu özelliği Azure portal duruma getirmek için çalışıyoruz. Bu arada, SDK sürümünü almak için aşağıdaki PowerShell komutlarını kullanabilirsiniz:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Bulut hizmetini birkaç ay boyunca kapatmak istiyorum. Bulut hizmetinin fatura maliyeti, IP adresini kaybetmeksizin nasıl azaltılıyor?

Zaten dağıtılmış bir bulut hizmeti, kullandığı Işlem ve depolama için faturalandırılır. Bu nedenle, Azure VM 'yi kapatsanız bile depolama alanı için faturalandırılırsınız. 

Hizmetinizin IP adresini kaybetmeden faturanızı azaltmak için şunları yapabilirsiniz:

1. Dağıtımları silmeden önce [IP adresini ayırın](../virtual-network/virtual-networks-reserved-public-ip.md) .  Yalnızca bu IP adresi için faturalandırılırsınız. IP adresi faturalaması hakkında daha fazla bilgi için bkz. [IP adresleri fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Dağıtımları silin. Daha sonra kullanabilmeniz için xxx.cloudapp.net silmeyin.
3. Aboneliğinizde ayrılan ayrılmış IP 'yi kullanarak bulut hizmetini yeniden dağıtmak istiyorsanız, [Cloud Services ve sanal makineler için ayrılmış IP adresleri](https://azure.microsoft.com/blog/reserved-ip-addresses/)bölümüne bakın.

