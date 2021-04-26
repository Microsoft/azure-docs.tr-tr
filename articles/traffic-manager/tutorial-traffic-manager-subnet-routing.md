---
title: Öğretici-Azure Traffic Manager alt ağ trafiği yönlendirmeyi yapılandırma
description: Bu öğreticide, trafiği Kullanıcı alt ağlarından belirli uç noktalara yönlendirmek üzere Traffic Manager nasıl yapılandırılacağı açıklanmaktadır.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 9b916f9942b0459b41d98b952fad072ae48318b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505447"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Öğretici: Traffic Manager kullanarak Kullanıcı alt ağına dayalı belirli uç noktalara trafik yönlendirin

Bu makalede alt ağ trafiği yönlendirme yöntemini yapılandırma adımları anlatılmaktadır. **Alt ağ** trafiği-yönlendirme yöntemi, bir IP adresi aralığını belirli uç noktalara eşlemenizi sağlar. Traffic Manager tarafından bir istek alındığında, isteğin kaynak IP 'sini inceler ve onunla ilişkili uç noktayı döndürür.

Bu öğreticide, Kullanıcı sorgusunun IP adresine bağlı olarak, alt ağ yönlendirmeyi kullanarak trafik bir iç Web sitesine veya bir üretim Web sitesine yönlendirilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * IIS üzerinde basit bir web sitesi çalıştıran iki VM oluşturma
> * Traffic Manager'ı uygulamalı olarak görmek için iki test amaçlı VM oluşturma
> * IIS çalıştıran VM'lerin DNS adını yapılandırma
> * Trafiği kullanıcının alt ağına göre yönlendirmek için bir Traffic Manager profili oluşturma
> * Traffic Manager profiline VM uç noktaları ekleme
> * Traffic Manager'ın nasıl çalıştığını görün

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide Traffic Manager görmek için, aşağıdaki işlemleri dağıtmanız gerekir:

- farklı Azure bölgelerinde çalışan iki basit web sitesi: **Doğu ABD** (iç web sitesi olarak görev yapar) ve **Batı Avrupa** (üretim web sitesi olarak görev yapar).
- Traffic Manager'ı test etmek için iki test amaçlı VM: bir VM **Doğu ABD** bölgesinde, ikinci VM ise **Batı Avrupa** bölgesinde olmalıdır.

Test amaçlı VM'ler Traffic Manager'ın kullanıcı sorgusunun geldiği alt ağa göre kullanıcı trafiğini iç web sitesine veya üretim web sitesine nasıl yönlendirdiğini göstermek için kullanılır.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

### <a name="create-websites"></a>Web sitelerini oluşturma

Bu bölümde Traffic Manager profili için iki farklı Azure bölgesinde iki hizmet uç noktası sunan iki web sitesi örneği oluşturacaksınız. İki web sitesi oluşturmak için aşağıdaki adımları izleyin:

1. Biri **Doğu ABD** diğeri **Batı Avrupa** bölgesinde olmak üzere basit bir web sitesi çalıştıran iki VM oluşturun.
2. İki VM'de de IIS sunucusu yükleyin ve varsayılan web sitesi sayfasını web sitesini ziyaret eden kullanıcıların VM adını göreceği şekilde güncelleştirin.

#### <a name="create-vms-for-running-websites"></a>Web sitelerini çalıştırmak için VM oluşturma

Bu bölümde, **Doğu ABD** ve Azure bölgelerine **Batı Avrupa** Iki VM *myIISVMEastUS* ve *myIISVMWestEurope* oluşturulur.

1. Azure Portal sol üst köşesinde, **kaynak oluştur**  >  **işlem**  >  **Windows Server 2019 Datacenter**' u seçin.
2. **Sanal makine oluştur**' da, **temel bilgiler** sekmesinde aşağıdaki değerleri yazın veya seçin:

   - **Abonelik**  >  **Kaynak grubu**: **Yeni oluştur** ' u seçin ve **myResourceGroupTM1** yazın.
   - **Örnek ayrıntıları**  >  **Sanal makine adı**: *myIISVMEastUS* yazın.
   - **Örnek ayrıntıları**  >  **Bölge**: **Doğu ABD** seçin.
   - **Yönetici hesabı**  >  **Kullanıcı** adı: tercih etmek için bir Kullanıcı adı girin.
   - **Yönetici hesabı**  >  **Parola**: seçtiğiniz parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.
   - **Gelen bağlantı noktası kuralları**  >  **Ortak gelen bağlantı noktaları**: **Seçili bağlantı noktalarına izin ver** öğesini seçin.
   - **Gelen bağlantı noktası kuralları**  >  **Gelen bağlantı noktalarını seçin**: aşağı açılan kutuda **RDP** ve **http** ' yi seçin.

3. **Yönetim** sekmesini seçin veya **Sonraki: diskler**' i ve sonra ileri: **ağ**' ı ve ardından **İleri: yönetim**' i seçin. **İzleme** altında, **önyükleme tanılamayı** **kapalı** olarak ayarlayın.
4. **Gözden geçir ve oluştur**’u seçin.
5. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.  
6. **Kaynak grubu** adı *myResourceGroupTM2*, *Batı Avrupa* **konumu** ve diğer tüm ayarların *myIISVMEastUS* ile aynı olan *myIISVMWestEurope* adlı ikinci bir sanal makine oluşturmak için adımları izleyin.
7. Sanal makinelerin oluşturulması birkaç dakika sürebilir. Her iki VM de oluşturuluncaya kadar kalan adımlara devam etmeyin.

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS yükleme ve varsayılan web sayfasını özelleştirme

Bu bölümde, IIS sunucusunu iki VM 'ye yüklersiniz- *myIISVMEastUS*  &  *myIISVMWestEurope* ve ardından varsayılan Web sitesi sayfasını güncelleştireceksiniz. Özelleştirilmiş Web sitesi sayfası bir Web tarayıcısından Web sitesini ziyaret ettiğinizde bağlanmakta olduğunuz VM 'nin adını gösterir.

1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynaklar listesinden *myResourceGroupTM1* kaynak grubunda bulunan *myIISVMEastUS* ' yi seçin.
2. **Genel bakış** sayfasında **Bağlan**' ı seçin ve sonra **sanal makineye bağlan**' a tıklayın, **RDP dosyasını indir**' i seçin.
3. İndirilen rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir.
4. **Tamam**’ı seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarıyı alırsanız, bağlantıya devam etmek için **Evet** veya **devam et**' i seçin.
6. Sunucu masaüstünde **Windows Yönetim Araçları** > **Sunucu Yöneticisi**' na gidin.
7. VM *myIISVMEastUS*'de Windows PowerShell 'i başlatın ve IIS sunucusu yüklemek ve varsayılan htm dosyasını güncelleştirmek için aşağıdaki komutları kullanın.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. *MyIISVMEastUS* VM ile RDP bağlantısını kapatın.
9. IIS yüklemek ve varsayılan Web sayfasını özelleştirmek için *myResourceGroupTM2* kaynak grubu Içinde VM *MYIISVMWESTEUROPE* ile bir RDP bağlantısı oluşturarak 1-6 arasındaki adımları yineleyin.
10. *MyIISVMWestEurope* VM 'de Windows PowerShell 'i başlatın ve IIS sunucusu yüklemek ve varsayılan htm dosyasını güncelleştirmek için aşağıdaki komutları kullanın.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS çalıştıran VM'lerin DNS adlarını yapılandırma

Traffic Manager, kullanıcı trafiğini hizmet uç noktalarının DNS adına göre yönlendirir. Bu bölümde, IIS sunucuları- *myIISVMEastUS* ve *myIISVMWestEurope* için DNS adlarını yapılandırırsınız.

1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynaklar listesinden *myResourceGroupTM1* kaynak grubunda bulunan *myIISVMEastUS* ' yi seçin.
2. **Genel bakış** sayfasının **DNS adı** bölümünde **Yapılandır**'ı seçin.
3. **Yapılandır** sayfasının DNS adı bölümünde benzersiz bir ad ekleyip **Kaydet**'i seçin.
4. *MyResourceGroupTM2* kaynak grubunda bulunan *myIISVMWestEurope* adlı VM için 1-3 adımlarını yineleyin.

### <a name="create-test-vms"></a>Test amaçlı VM'leri oluşturma

Bu bölümde, her bir Azure bölgesinde (**Doğu ABD** ve **Batı Avrupa**) bir VM (*myVMEastUS* ve *myVMWestEurope*) oluşturursunuz. Bu VM 'Leri, kullanıcının sorgusunun alt ağına göre Kullanıcı trafiğini yönlendirme Traffic Manager test etmek için kullanacaksınız.

1. Azure Portal sol üst köşesinde, **kaynak oluştur**  >  **işlem**  >  **Windows Server 2019 Datacenter**' u seçin.
2. **Sanal makine oluştur**' da, **temel bilgiler** sekmesinde aşağıdaki değerleri yazın veya seçin:

   - **Abonelik**  >  **Kaynak grubu**: **myResourceGroupTM1** seçin.
   - **Örnek ayrıntıları**  >  **Sanal makine adı**: *myVMEastUS* yazın.
   - **Örnek ayrıntıları**  >  **Bölge**: **Doğu ABD** seçin.
   - **Yönetici hesabı**  >  **Kullanıcı** adı: tercih etmek için bir Kullanıcı adı girin.
   - **Yönetici hesabı**  >  **Parola**: seçtiğiniz parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.
   - **Gelen bağlantı noktası kuralları**  >  **Ortak gelen bağlantı noktaları**: **Seçili bağlantı noktalarına izin ver** öğesini seçin.
   - **Gelen bağlantı noktası kuralları**  >  **Gelen bağlantı noktalarını seçin**: aşağı açılan kutuda **RDP** ' yi seçin.

3. **Yönetim** sekmesini seçin veya **Sonraki: diskler**' i ve sonra ileri: **ağ**' ı ve ardından **İleri: yönetim**' i seçin. **İzleme** altında, **önyükleme tanılamayı** **kapalı** olarak ayarlayın.
4. **Gözden geçir ve oluştur**’u seçin.
5. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.  
6. **Kaynak grubu** adı *myResourceGroupTM2*, *Batı Avrupa* **konumu** ve diğer tüm ayarların *myVMEastUS* ile aynı olan *myVMWestEurope* adlı ikinci bir sanal makine oluşturmak için adımları izleyin.
7. Sanal makinelerin oluşturulması birkaç dakika sürebilir. Her iki sanal makine de oluşturulmadan kalan adımlara devam etmeyin.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

İsteğin kaynak IP adresine göre belirli uç noktalar döndürmenizi sağlayacak bir Traffic Manager profili oluşturun.

1. Ekranın sol üst kısmında **kaynak oluştur**' u seçin. *Traffic Manager profili* araması yapın ve **Oluştur**' u seçin.
2. **Oluşturma Traffic Manager profilinde** aşağıdaki bilgileri girin veya seçin. Kalan ayarlar için varsayılan değerleri kabul edin ve **Oluştur**' u seçin.

    ![Traffic Manager profili oluşturma](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Ad                   | Bu adın trafficmanager.net bölgesinde benzersiz olması ve Traffic Manager profilinize erişmek için kullanılan trafficmanager.net DNS adı ile sonuçlanması gerekir.                                   |
    | Yönlendirme yöntemi          | **Alt ağ** yönlendirme yöntemini seçin.                                       |
    | Abonelik            | Aboneliğinizi seçin.                          |
    | Kaynak grubu          | **Var olan**’ı seçin ve sonra *myResourceGroupTM1* yazın. |

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager uç noktalarını ekleme

Kullanıcı   &  trafiğinin Kullanıcı sorgusunun alt ağına göre yönlendirilmesini sağlamak için IIS sunucularını çalıştıran iki VM 'yi ekleyin-myIISVMEastUS *myIISVMWestEurope* .

1. Portalın arama çubuğunda önceki bölümde oluşturduğunuz Traffic Manager profili adını arayın ve görüntülenen sonuçların arasından bu profili seçin.
2. **Traffic Manager profili**' nde, **Ayarlar** bölümünde **uç noktalar**' ı seçin ve ardından **Ekle**' yi seçin.
3. Aşağıdaki bilgileri girin veya seçin. Geri kalan ayarlar için varsayılan değerleri kabul edin ve ardından **Tamam**' ı seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Tür                    | Azure uç noktası                                   |
    | Name           | myInternalWebSiteEndpoint                                        |
    | Hedef kaynak türü           | Genel IP Adresi                          |
    | Hedef kaynak          | Aynı abonelikte genel IP adreslerine sahip kaynakların listesini göstermek için **Genel BIR IP adresi seçin** . **Kaynak** bölümünde *myIISVMEastUS-ip* adlı genel IP adresini seçin. Bu, Doğu ABD bölgesindeki IIS sunucusu VM'sinin IP adresidir.|
    |  Alt ağ yönlendirme ayarları    |   *MyVMEastUS* test VM 'sinin IP adresini ekleyin. Bu VM 'den kaynaklanan herhangi bir Kullanıcı sorgusu *Myınternalwebsiteendpoint*'e yönlendirilir.    |

4. *MyIISVMWestEurope* adlı IIS sunucusu VM ile ILIŞKILI genel IP adresi *myIISVMWestEurope-IP* Için *myprodwebsiteendpoint* adlı başka bir uç nokta eklemek üzere 2 ve 3. adımları yineleyin. **Alt ağ yönlendirme ayarları** için, test VM- *myVMWestEurope* IP adresini ekleyin. Bu test amaçlı VM'den gelen kullanıcı sorguları *myProdWebsiteEndpoint* adlı uç noktaya yönlendirilir.
5. Her iki uç noktanın eklenmesi tamamlandığında, bunlar **Traffic Manager profilinde** görüntülenir ve bunların Izleme durumu **çevrimiçi** olarak gösterilir.

## <a name="test-traffic-manager-profile"></a>Traffic Manager profilini test etme

Bu bölümde Traffic Manager'ın belirli bir alt ağdan gelen trafiği belirli bir uç noktaya nasıl yönlendirdiğini test edeceksiniz. Traffic Manager'ı uygulamalı olarak görmek için şu adımları gerçekleştirin:

1. Traffic Manager profilinizin DNS adını belirleyin.
2. Traffic Manager'ın nasıl çalıştığını görmek için şu adımları izleyin:
    - **Doğu ABD** bölgesinde bulunan test VM 'Sinden (*myVMEastUS*), bir Web TARAYıCıSıNDA, Traffic Manager profilinizin DNS adına gidin.
    - **Batı Avrupa** bölgesinde bulunan test VM 'Sinden (*myVMWestEurope*), bir Web TARAYıCıSıNDA, Traffic Manager profilinizin DNS adına gidin.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager profilinin DNS adını belirleme

Bu öğreticide kolaylık olması açısından web sitelerini ziyaret etmek için Traffic Manager profilinin DNS adı kullanılmaktadır.

Traffic Manager profilinizin DNS adını belirlemek için şu adımları izleyin:

1. Portalın arama çubuğunda, önceki bölümde oluşturduğunuz **Traffic Manager profili** adını arayın. Görüntülenen sonuçlarda Traffic Manager profilini seçin.
2. **Genel bakış**'ı seçin.
3. **Traffic Manager profili** penceresinde yeni oluşturduğunuz Traffic Manager profilinin DNS adı görüntülenir. Üretim dağıtımlarında bir gösterim etki alanı adını DNS CNAME kaydı kullanarak Traffic Manager etki alanı adına yönlendirirsiniz.

### <a name="view-traffic-manager-in-action"></a>Traffic Manager'ın nasıl çalıştığını görün

Bu bölümde Traffic Manager'ın nasıl çalıştığını görebilirsiniz.

1. Sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından kaynaklar listesinden *myResourceGroupTM1* kaynak grubunda bulunan *myVMEastUS* ' yi seçin.
2. **Genel bakış** sayfasında **Bağlan**' ı seçin ve sonra **sanal makineye bağlan**' a tıklayın, **RDP dosyasını indir**' i seçin.
3. İndirilen rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir.
4. **Tamam**’ı seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarıyı alırsanız, bağlantıya devam etmek için **Evet** veya **devam et**' i seçin.
6. Web sitesini görüntülemek için *myVMEastUS* adlı VM'de bir web tarayıcısında Traffic Manager profilinizin DNS adını yazın. VM *myVMEastUS* IP adresi *myınternalwebsiteendpoint* uç noktasıyla ilişkili olduğundan, Web tarayıcısı test Web sitesi sunucusunu ( *myIISVMEastUS*) başlatır.

7. Ardından, 1-5 adımlarını kullanarak **Batı Avrupa** bulunan VM *myVMWestEurope* 'e bağlanın ve bu VM 'nin Traffic Manager profili etki alanı adına gidin. VM *myVMWestEurope* IP adresi *myteswebsiteendpoint* uç noktasıyla ilişkili olduğundan, Web tarayıcısı test Web sitesi sunucusunu ( *myIISVMWestEurope*) başlatır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

İhtiyacınız kalmadığında kaynak gruplarını (**ResourceGroupTM1** ve **ResourceGroupTM2**) silebilirsiniz. Bunun için kaynak grubunu (**ResourceGroupTM1** veya **ResourceGroupTM2**) ve ardından **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Alt ağ yönlendirme yöntemi hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Alt ağ trafiği yönlendirme yöntemi](traffic-manager-routing-methods.md#subnet)
