---
title: Azure'da Service Fabric üzerinde Windows kapsayıcı uygulaması oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, Azure Service Fabric üzerinde ilk Windows kapsayıcı uygulamanızı oluşturursunuz.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 096a398b8fc4f7f42dcc42feb7fe00b182d7649b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68599379"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Hızlı Başlangıç: Windows kapsayıcılarını Service Fabric dağıtma

Azure Service Fabric; ölçeklenebilir ve güvenilir mikro hizmetleri ve kapsayıcıları dağıtmayı ve yönetmeyi sağlayan bir dağıtılmış sistemler platformudur.

Bir Service Fabric kümesindeki Windows kapsayıcısında mevcut olan bir uygulamayı çalıştırmak için uygulamanızda herhangi bir değişiklik yapılması gerekmez. Bu hızlı başlangıç, Service Fabric uygulamasında önceden oluşturulmuş bir Docker kapsayıcısı görüntüsünü dağıtmayı gösterir. İşiniz bittiğinde, çalışan bir Windows Server Core 2016 sunucusu ve IIS kapsayıcısına sahip olacaksınız. Bu hızlı başlangıçta bir Windows kapsayıcısının dağıtımı açıklanmaktadır. Bir Linux kapsayıcısı dağıtmak için [Bu hızlı](service-fabric-quickstart-containers-linux.md) başlangıcı okuyun.

![IIS varsayılan web sayfası][iis-default]

Bu hızlı başlangıçta şunları yapmayı öğrenirsiniz:

* Docker görüntü kapsayıcısını paketleme
* İletişimi yapılandırma
* Service Fabric uygulamasını oluşturma ve paketleme
* Kapsayıcı uygulamasını Azure’a dağıtma


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliği ([ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturabilirsiniz).
* Şunları çalıştıran bir geliştirme bilgisayarı:
  * Visual Studio 2019 veya Windows 2019.
  * [Service Fabric SDK’sı ve araçları](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Visual Studio ile Docker görüntü kapsayıcısını paketleme

Service Fabric SDK’sı ve araçları, bir kapsayıcıyı Service Fabric kümesine dağıtmanıza yardımcı olan bir hizmet şablonu sağlar.

Visual Studio'yu “Yönetici” olarak başlatın.  **Dosya** > **Yeni** > **Proje**’yi seçin.

**Service Fabric uygulama**seçin, "MyFirstContainer" olarak adlandırın ve **Oluştur**' a tıklayın.

**Barındırılan Kapsayıcılar ve Uygulamalar** şablonlarından **Kapsayıcı**’yı seçin.

**Görüntü adı**alanına "MCR.Microsoft.com/Windows/ServerCore/iis:windowsservercore-ltsc2016", [Windows Server Core Server ve IIS temel görüntüsünü](https://hub.docker.com/_/microsoft-windows-servercore-iis)girin.

80 numaralı bağlantı noktasında hizmete gelen isteklerin, kapsayıcı üzerindeki 80 numaralı bağlantı noktasıyla eşlenmesi için kapsayıcının bağlantı noktasından konağa bağlantı noktası eşlenmesini yapılandırın.  **Kapsayıcı Bağlantı Noktası**’nı "80" olarak ve **Ana Bilgisayar Bağlantı Noktası**’nı "80" olarak ayarlayın.  

Hizmeti "MyContainerService" olarak adlandırın ve **Tamam**’a tıklayın.

![Yeni hizmet iletişim kutusu][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Kapsayıcı görüntünüz için OS derlemesini belirtme

Windows Server'ın belirli bir sürümüyle derlenen kapsayıcılar, Windows Server'ın farklı sürümünü çalıştıran bir konakta çalışmayabilir. Örneğin, Windows Server sürüm 1709 kullanılarak oluşturulan kapsayıcılar Windows Server 2016 çalıştıran konaklarda çalıştırılmayın. Daha fazla bilgi için bkz. [Windows Server kapsayıcı işletim sistemi ve ana bilgisayar işletim sistemi uyumluluğu](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

Service Fabric çalışma zamanının sürüm 6.1 veya daha yeni bir sürümüyle, kapsayıcı başına birden çok işletim sistemi görüntüsü belirtebilir ve her birini dağıtılacağı işletim sisteminin derleme sürümüyle etiketleyebilirsiniz. Bu, uygulamanızın Windows işletim sisteminin farklı sürümlerini çalıştıran konaklar arasında çalıştırılabilmesine yardımcı olur. Daha fazla bilgi edinmek için bkz. [İşletim sistemi derlemesine özgü kapsayıcı görüntüleri belirtme](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

Microsoft, Windows Server'ın farklı sürümleri üzerinde oluşturulmuş IIS sürümleri için farklı görüntüler yayımlar. Service Fabric'in, uygulamanızın dağıtıldığı küme düğümlerinde çalıştırılan Windows Server sürümüyle uyumlu bir kapsayıcı dağıttığından emin olmak için, *ApplicationManifest.xml* dosyasına aşağıdaki satırları ekleyin. WIndows Server 2016 için derleme sürümü 14393, Windows Server 1709 sürümü için derleme sürümü 16299’dur.

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

Hizmet bildirimi, `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016` nano sunucusu için tek bir görüntü belirtmeye devam eder.

*ApplicationManifest. xml* dosyasında Ayrıca, passwor' i **yanlış**olarak değiştirin. Bu hesap ve parola, Docker Hub 'ında bulunan ortak kapsayıcı görüntüsü için boştur, bu nedenle boş bir parolanın şifrelenmesi bir yapı hatası üretecek için şifrelemeyi kapattık.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>Küme oluşturma

Aşağıdaki örnek betik, bir X. 509.440 sertifikasıyla korunan beş düğümlü bir Service Fabric kümesi oluşturur. Bu komut otomatik olarak imzalanan bir sertifika oluşturur ve bunu yeni bir anahtar kasasına yükler. Sertifika aynı zamanda bir yerel dizine de kopyalanır. Bu betiği kullanarak bir küme oluşturma hakkında daha fazla bilgi için [Service Fabric kümesi oluşturun](scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/overview)bulunan yönergeleri kullanarak Azure PowerShell ' yi yüklemelisiniz.

Aşağıdaki betiği çalıştırmadan önce, PowerShell çalıştırmasında `Connect-AzAccount` Azure ile bir bağlantı oluşturun.

Aşağıdaki betiği Pano 'ya kopyalayın ve **Windows PowerShell ISE**açın.  İçeriği boş Untitled1. ps1 penceresine yapıştırın. Sonra betikteki Değişkenler `subscriptionId`için değerler sağlayın:, `certpwd`, `certfolder` `adminuser`,, `adminpwd`, vb.  İçin `certfolder` belirttiğiniz dizin, betiği çalıştırmadan önce mevcut olmalıdır.

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

Değişkenleri için değerlerinizi girdikten sonra, komut dosyasını çalıştırmak için **F5** tuşuna basın.

Betiği çalıştıktan ve küme oluşturulduktan sonra çıktıda bulun `ClusterEndpoint` . Örneğin:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>Küme için sertifikayı yükler

Şimdi de PFX 'yi *Currentuser\certificate* deposuna yükleyeceğiz. PFX dosyası, yukarıdaki PowerShell betiğindeki `certfolder` ortam değişkenini kullanarak belirttiğiniz dizinde olacaktır.

Bu dizine geçin ve ardından `certfolder` dizininizde bulunan PFX dosyasının adını ve `certpwd` değişkende belirttiğiniz parolayı değiştirerek aşağıdaki PowerShell komutunu çalıştırın. Bu örnekte, geçerli dizin, PowerShell betiğinin `certfolder` değişkeni tarafından belirtilen dizine ayarlanır. `Import-PfxCertificate` Komutun çalıştırıldığı komut:

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

Komut parmak Izini döndürür:

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

Aşağıdaki adım için parmak izi değerini hatırlayın.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Visual Studio kullanarak uygulamayı Azure’a dağıtma

Uygulama hazır olduğuna göre, doğrudan Visual Studio'dan bir kümeye dağıtabilirsiniz.

Çözüm Gezgini'nde **MyFirstContainer**’a sağ tıklayın ve **Yayımla**’yı seçin. Yayımla iletişim kutusu görüntülenir.

Yukarıdaki `Import-PfxCertificate` komutu çalıştırdığınız zaman PowerShell penceresinde **CN =** içeriğini kopyalayın ve buna bağlantı noktası `19000` ekleyin. Örneğin: `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. **Bağlantıyı bağlantı uç noktası** alanına kopyalayın. Gelecekteki bir adımda ihtiyaç duyacağınız için bu değeri unutmayın.

**Gelişmiş Bağlantı Parametrelerine** tıklayıp bağlantı parametresi bilgilerini doğrulayın.  *FindValue* ve *servercertparmak izi* değerleri, önceki adımda çalıştırdığınızda `Import-PfxCertificate` yüklenen sertifikanın parmak izine uymalıdır.

![Yayımla İletişim Kutusu](./media/service-fabric-quickstart-containers/publish-app.png)

Tıklayın **yayımlama**.

Kümedeki her uygulamanın benzersiz bir adı olmalıdır. Ad çakışması varsa, Visual Studio projesini yeniden adlandırın ve yeniden dağıtın.

Bir tarayıcı açın ve önceki adımda **bağlantı uç noktası** alanına yerleştirdiğiniz adrese gidin. İsteğe bağlı olarak, URL’nin başına düzen tanımlayıcısını (`http://`) ve sonuna bağlantı noktasını (`:80`) ekleyebilirsiniz. Örneğin, http:\//mysfcluster.SouthCentralUS.cloudapp.Azure.com:80.

 IIS varsayılan Web sayfasını görmeniz gerekir: ![IIS varsayılan Web sayfası][iis-default]

## <a name="clean-up"></a>Temizleme

Küme çalışırken ücretlendirmeye devam edersiniz. [Kümenizi silmeyi](service-fabric-cluster-delete.md)göz önünde bulundurun.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

* Docker görüntü kapsayıcısını paketleme
* İletişimi yapılandırma
* Service Fabric uygulamasını oluşturma ve paketleme
* Kapsayıcı uygulamasını Azure’a dağıtma

Service Fabric’te Windows kapsayıcılarıyla çalışma hakkında daha fazla bilgi için Windows kapsayıcı uygulamaları öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Windows kapsayıcı uygulaması oluşturma](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
