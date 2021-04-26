---
title: Güvenli Azure Service Fabric küme bağlantılarını yapılandırma
description: Azure Service Fabric kümesi tarafından desteklenen güvenli bağlantıları yapılandırmak için Visual Studio 'Yu nasıl kullanacağınızı öğrenin.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: d4d6b781d97d481793e69cf2ca97cca5b93ce432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008540"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Visual Studio 'dan Service Fabric kümesine güvenli bağlantıları yapılandırma
Visual Studio 'Yu kullanarak, erişim denetim ilkeleriyle yapılandırılmış bir Azure Service Fabric kümesine güvenli bir şekilde erişme hakkında bilgi edinin.

## <a name="cluster-connection-types"></a>Küme bağlantı türleri
Azure Service Fabric kümesi tarafından iki tür bağlantı desteklenir: **güvenli olmayan** bağlantılar ve **x509 sertifika tabanlı** güvenli bağlantılar. (Şirket içinde barındırılan Service Fabric kümeleri için **Windows** ve **DSTs** kimlik doğrulamaları da desteklenir.) Küme oluşturulurken küme bağlantı türünü yapılandırmanız gerekir. Oluşturulduktan sonra bağlantı türü değiştirilemez.

Visual Studio Service Fabric araçları, yayımlamak üzere bir kümeye bağlanmak için tüm kimlik doğrulama türlerini destekler. Güvenli bir Service Fabric kümesinin nasıl ayarlanacağı hakkında yönergeler için bkz. [Azure portal Service Fabric kümesi ayarlama](service-fabric-cluster-creation-via-portal.md) .

## <a name="configure-cluster-connections-in-publish-profiles"></a>Yayımlama profillerinde küme bağlantılarını yapılandırma
Visual Studio 'dan bir Service Fabric projesi yayımlarsanız, bir Azure Service Fabric kümesi seçmek için **Service Fabric uygulamayı Yayımla** iletişim kutusunu kullanın. **Bağlantı uç noktası**' nın altında, aboneliğiniz altında var olan bir kümeyi seçin.

![Service Fabric bağlantısını yapılandırmak için * * Publish Service Fabric Application * * iletişim kutusu kullanılır.][publishdialog]

**Service Fabric uygulaması Yayımla** iletişim kutusu, küme bağlantısını otomatik olarak doğrular. İstenirse, Azure hesabınızda oturum açın. Doğrulama başarılı olursa, sisteminizde kümeye güvenli bir şekilde bağlanmak için doğru sertifikalar yüklenmiş veya kümeniz güvenli olmayan bir durumda. Doğrulama hataları, ağ sorunlarından veya sisteminizin güvenli bir kümeye bağlanmak için doğru şekilde yapılandırılmış olmasına neden olabilir.

![* * Service Fabric uygulaması Yayımla * * iletişim kutusu, var olan, doğru şekilde yapılandırılmış bir Service Fabric kümesi bağlantısını doğrular.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Güvenli bir kümeye bağlanmak için
1. Hedef kümenin güvendiği istemci sertifikalarından birine erişebildiğinizden emin olun. Sertifika genellikle kişisel bilgi değişimi (. pfx) dosyası olarak paylaşılır. Bir istemciye erişim vermek üzere sunucunun nasıl yapılandırılacağı hakkında [Azure portal Service Fabric kümesi ayarlama](service-fabric-cluster-creation-via-portal.md) bölümüne bakın.
2. Güvenilen sertifikayı yükler. Bunu yapmak için. pfx dosyasına çift tıklayın veya sertifikaları içeri aktarmak için PowerShell betiği Import-PfxCertificate kullanın. Sertifikayı **CERT: \ Localmachine\'** a yükler. Sertifikayı içeri aktarırken tüm varsayılan ayarları kabul etmek tamam.
3. Projenin kısayol menüsündeki **Yayımla...** komutunu seçerek **Azure uygulaması Yayımla** iletişim kutusunu açın ve hedef kümeyi seçin. Araç otomatik olarak bağlantıyı çözümler ve güvenli bağlantı parametrelerini yayımlama profiline kaydeder.
4. İsteğe bağlı: yayımlama profilini, güvenli bir küme bağlantısı belirtmek üzere düzenleyebilirsiniz.
   
   Yayımlama profili XML dosyasını sertifika bilgilerini belirtecek şekilde el ile düzenlediğinizden, sertifika deposu adı, depo konumu ve sertifika parmak izini unutmayın. Sertifikanın mağaza adı ve depolama konumu için bu değerleri sağlamanız gerekir. Daha fazla bilgi için bkz. [nasıl yapılır: bir sertifikanın parmak Izini alma](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) .
   
   Service Fabric kümesine bağlanırken kullanılacak PowerShell parametrelerini belirtmek için *Clusterconnectionparameters* parametrelerini kullanabilirsiniz. Connect-ServiceFabricCluster cmdlet 'i tarafından kabul edilen geçerli parametreler şunlardır. Kullanılabilir parametrelerin listesi için bkz. [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) .
   
   Uzak bir kümeye yayımlıyorsanız, ilgili küme için uygun parametreleri belirtmeniz gerekir. Güvenli olmayan bir kümeye bağlanma örneği aşağıda verilmiştir:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   X509 sertifika tabanlı bir güvenli kümeye bağlanmak için bir örnek aşağıda verilmiştir:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Yükseltme parametreleri ve uygulama parametresi dosya konumu gibi diğer tüm gerekli ayarları düzenleyin ve ardından Visual Studio 'da **Service Fabric uygulaması Yayımla** iletişim kutusundan uygulamanızı yayımlayın.

## <a name="next-steps"></a>Sonraki adımlar
Service Fabric kümelerine erişme hakkında daha fazla bilgi için bkz. [Service Fabric Explorer kullanarak kümenizi görselleştirme](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
