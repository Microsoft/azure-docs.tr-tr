---
title: 'Hızlı başlangıç: uygulamalar için bir profil oluşturma-Azure portal-Azure Traffic Manager'
description: Bu hızlı başlangıç makalesinde yüksek oranda kullanılabilir web uygulaması oluşturmak için bir Traffic Manager profili oluşturma adımlarını anlatılmaktadır.
services: traffic-manager
author: asudbring
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: allensu
ms.openlocfilehash: b6a015c7462f082d5114a51d83cfacb0c550b026
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032707"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak Traffic Manager profili oluşturma

Bu hızlı başlangıçta, Web uygulamanız için yüksek kullanılabilirlik sunan bir Traffic Manager profilinin nasıl oluşturulacağı açıklanmaktadır.

Bu hızlı başlangıçta, bir Web uygulamasının iki örneği hakkında bilgi edineceksiniz. Bunların her biri farklı bir Azure bölgesinde çalışmaktadır. [Uç nokta önceliğine](traffic-manager-routing-methods.md#priority)göre bir Traffic Manager profili oluşturacaksınız. Profil, Kullanıcı trafiğini Web uygulamasını çalıştıran birincil siteye yönlendirir. Traffic Manager Web uygulamasını sürekli izler. Birincil site kullanılamıyorsa, yedekleme sitesine otomatik yük devretme sağlar.

Azure aboneliğiniz yoksa şimdi [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıçta iki farklı Azure bölgesinde (*Doğu ABD* ve *Batı Avrupa*) dağıtılan bir Web uygulamasının iki örneğine ihtiyacınız olacaktır. Her biri, Traffic Manager için birincil ve yük devretme uç noktaları olarak görev yapar.

1. Ekranın sol üst kısmında **web** > **Web uygulaması** > **kaynak oluştur** ' u seçin.

1. **Web uygulaması oluştur**' da, **temel bilgiler** sekmesinde aşağıdaki değerleri yazın veya seçin:

   - **Abonelik** > **kaynak grubu**: **Yeni oluştur** ' u seçin ve **myResourceGroupTM1**yazın.
   - **Örnek ayrıntıları** > **adı**: tür *myWebAppEastUS*.
   - **Örnek ayrıntıları** **Yayımla** > : **kodu**seçin.
   - **Örnek ayrıntıları** > **çalışma zamanı yığını**: **ASP.net v 4.7** seçin
   - **Örnek ayrıntıları** > **Işletim sistemi**: **Windows**' u seçin.
   - **Örnek ayrıntıları** > **bölgesi**: **Doğu ABD**seçin.
   - **App Service plan** > **Windows planı (Doğu ABD)** : **Yeni oluştur** ' u seçin ve ardından **myAppServicePlanEastUS** yazın.
   - **App Service Plan** > **SKU ve boyut**: **Standart S1**' i seçin.
   
3. **İzleme** sekmesini seçin veya **İleri: izleme**' yi seçin.  **İzleme**altında, **Application Insights** > **Application Insights** **Hayır**olarak ayarlayın.

4. **Gözden geçir ve oluştur** seçeneğini belirleyin

5. Ayarları gözden geçirin ve ardından **Oluştur**' a tıklayın.  Web uygulaması başarılı bir şekilde dağıttığında, varsayılan bir Web sitesi oluşturur.

6. *MyWebAppWestEurope*adlı Ikinci bir Web uygulaması oluşturmak için adımları Izleyin. **kaynak grubu** adı *myResourceGroupTM2*, bir **bölge** *Batı Avrupa*, **myAppServicePlanWestEurope** **App Service planı** adı ve diğer tüm ayarlar *myWebAppEastUS*ile aynı.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

Uç nokta önceliğine göre Kullanıcı trafiğini yönlendiren bir Traffic Manager profili oluşturun.

1. Ekranın sol üst kısmında, **kaynak oluştur** > **ağ** > **Traffic Manager profil**' i seçin.
2. **Oluşturma Traffic Manager profilinde**, bu ayarları girin veya seçin:

    | Ayar | Değer |
    | --------| ----- |
    | Ad | Traffic Manager profiliniz için benzersiz bir ad girin.|
    | Yönlendirme yöntemi | **Öncelik**' i seçin.|
    | Abonelik | Traffic Manager profilinin uygulanmasını istediğiniz aboneliği seçin. |
    | Kaynak grubu | *MyResourceGroupTM1*öğesini seçin.|
    | Konum |Bu ayar, kaynak grubunun konumunu ifade eder. Genel olarak dağıtılacak Traffic Manager profili üzerinde hiçbir etkisi yoktur.|

3. **Oluştur**'u seçin.

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager uç noktalarını ekleme

Tüm kullanıcı trafiğini yönlendirmek için birincil uç nokta olarak *Doğu ABD* bölgesindeki web sitesini ekleyin. *Batı Avrupa* Web sitesini yük devretme uç noktası olarak ekleyin. Birincil uç nokta kullanılamadığında, trafik otomatik olarak yük devretme uç noktasına yönlendirir.

1. Portalın arama çubuğunda, önceki bölümde oluşturduğunuz Traffic Manager profili adını girin.
2. Arama sonuçlarından profili seçin.
3. **Traffic Manager profili**' nde, **Ayarlar** bölümünde **uç noktalar**' ı seçin ve ardından **Ekle**' yi seçin.
4. Bu ayarları girin veya seçin:

    | Ayar | Değer |
    | ------- | ------|
    | Tür | **Azure uç noktası**' nı seçin. |
    | Ad | *Myprimaryendpoint*girin. |
    | Hedef kaynak türü | **App Service**seçin. |
    | Hedef kaynak |  > **bir App Service** seçin **Doğu ABD**seçin. |
    | Öncelik | **1**'i seçin. Tüm trafik sağlıklı olduğunda bu uç noktaya gider. |

    ![Traffic Manager profilinize bir uç nokta eklediğiniz ekran görüntüsü.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. **Tamam**’ı seçin.
6. İkinci Azure bölgenize yönelik bir yük devretme uç noktası oluşturmak için bu ayarlarla 3 ve 4 numaralı adımları yineleyin:

    | Ayar | Değer |
    | ------- | ------|
    | Tür | **Azure uç noktası**' nı seçin. |
    | Ad | *Myfailoverendpoint*girin. |
    | Hedef kaynak türü | **App Service**seçin. |
    | Hedef kaynak |  > **bir App Service** seçin **Batı Avrupa**seçin. |
    | Öncelik | **2**öğesini seçin. Birincil uç nokta sağlıksız ise tüm trafik bu yük devretme uç noktasına gider. |

7. **Tamam**’ı seçin.

İki uç nokta eklemeyi tamamladığınızda, bunlar **Traffic Manager profilinde**görüntülenirler. İzleme durumlarının şimdi **çevrimiçi** olduğuna dikkat edin.

## <a name="test-traffic-manager-profile"></a>Traffic Manager profilini test etme

Bu bölümde, Traffic Manager profilinizin etki alanı adını kontrol edeceksiniz. Ayrıca birincil uç noktayı kullanılamaz olarak yapılandıracaksınız. Son olarak, Web uygulamasının hala kullanılabilir olduğunu görmeniz gerekir. Bunun nedeni, trafiği yük devretme uç noktasına gönderiyor Traffic Manager.

### <a name="check-the-dns-name"></a>DNS adını denetleyin

1. Portalın arama çubuğunda önceki bölümde oluşturduğunuz **Traffic Manager profili** adını arayın.
2. Traffic Manager profilini seçin. **Genel bakış** görüntülenir.
3. **Traffic Manager profili** penceresinde yeni oluşturduğunuz Traffic Manager profilinin DNS adı görüntülenir.
  
   ![Traffic Manager DNS adınızın konumunun ekran görüntüsü](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager'ın nasıl çalıştığını görün

1. Web tarayıcısında, Web uygulamanızın varsayılan Web sitesini görüntülemek için Traffic Manager profilinizin DNS adını girin.

    > [!NOTE]
    > Bu hızlı başlangıç senaryosunda, tüm istekler birincil uç noktaya yönlendirir. **Öncelik 1**olarak ayarlanır.

    ![Traffic Manager profilinin kullanılabilirliğini onaylamak için Web sayfasının ekran görüntüsü](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. Traffic Manager yük devretmeyi görüntülemek için birincil sitenizi devre dışı bırakın:
    1. Traffic Manager profili sayfasında, **genel bakış** bölümünden **Myprimaryendpoint**' i seçin.
    2. *Myprimaryendpoint*Içinde, **devre dışı** > **Kaydet**' i seçin.
    3. **Myprimaryendpoint**öğesini kapatın. Durumun şimdi **devre dışı bırakıldığını** unutmayın.
3. Web sitesini yeni bir Web tarayıcısı oturumunda görüntülemek için, önceki adımda Traffic Manager profilinizin DNS adını kopyalayın.
4. Web uygulamasının hala kullanılabilir olduğunu doğrulayın.

Birincil uç nokta kullanılamıyor, bu nedenle yük devretme uç noktasına yönlendirilmiştiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

İşiniz bittiğinde, kaynak gruplarını, Web uygulamalarını ve tüm ilgili kaynakları silin. Bunu yapmak için, panonuzdaki her bir öğeyi seçin ve her sayfanın üst kısmında **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Traffic Manager profili oluşturdunuz. Yüksek kullanılabilirliğe sahip Web uygulamaları için Kullanıcı trafiğini yönlendirme olanağı sağlar. Yönlendirme trafiği hakkında daha fazla bilgi edinmek için Traffic Manager öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Traffic Manager öğreticileri](tutorial-traffic-manager-improve-website-response.md)
