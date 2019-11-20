---
title: Azure Analysis Services için şirket içi veri ağ geçidini yükler | Microsoft Docs
description: Bir Azure Analysis Services sunucusundan şirket içi veri kaynaklarına bağlanmak için şirket içi veri ağ geçidini yüklemeyi ve yapılandırmayı öğrenin.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 37658ea6b94d6369d282c49f79947a1f183b61c7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572847"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Şirket içi veri ağ geçidini yükleme ve yapılandırma

Aynı bölgedeki bir veya daha fazla Azure Analysis Services sunucusu şirket içi veri kaynaklarına bağlandığında şirket içi veri ağ geçidi gerekir.  Yüklediğiniz ağ geçidi, Azure Analysis Services için yüklenirken Power BI, Power Apps ve Logic Apps gibi diğer hizmetler tarafından kullanılan ile aynı olsa da, gerçekleştirmeniz gereken bazı ek adımlar vardır. Bu Install article **Azure Analysis Services**özeldir.

Ağ geçidi ve Azure Analysis Services tarafından nasıl kullanıldığı hakkında daha fazla bilgi edinmek için bkz. [Şirket içi veri kaynaklarına bağlanma](analysis-services-gateway.md).

## <a name="prerequisites"></a>Ön koşullar

**Minimum gereksinimler:**

* .NET 4,5 çerçevesi
* Windows 7/Windows Server 2008 R2 64-bit sürümü (veya üzeri)

**Önerilen**

* 8 çekirdekli CPU
* 8 GB bellek
* Windows 2012 R2 64-bit sürümü (veya üzeri)

**Önemli noktalar:**

* Kurulum sırasında, ağ geçidinizi Azure 'a kaydederken aboneliğinizin varsayılan bölgesi seçilidir. Farklı bir bölge seçebilirsiniz. Birden fazla bölgede sunucularınız varsa, her bölge için bir ağ geçidi kurmanız gerekir. 
* Ağ Geçidi, bir etki alanı denetleyicisine yüklenemez.
* Tek bir bilgisayara yalnızca bir ağ geçidi yüklenebilir.
* Ağ geçidini üzerinde kalan ve uyku moduna geçmediğinden bir bilgisayara yükler.
* Ağ geçidini, ağınıza kablosuz olarak bağlı bir bilgisayara yüklemeyin. Performans düşebilir.
* Ağ geçidini yüklerken bilgisayarınızda oturum açtığınız kullanıcı hesabının hizmet olarak oturum açma ayrıcalıklarına sahip olması gerekir. Yüklemesi tamamlandığında, şirket içi veri ağ geçidi hizmeti bir hizmet olarak oturum açmak için NT Servıce\pbıegwservice hesabını kullanır. Kurulum tamamlandıktan sonra kurulum sırasında veya hizmetlerde farklı bir hesap belirtilebilir. Grup ilkesi ayarlarının, yüklerken ve seçtiğiniz hizmet hesabının hizmet olarak oturum açma ayrıcalıklarına sahip olduğundan emin olun.
* Azure 'da, ağ geçidini kaydetmekte olduğunuz abonelikle aynı [kiracı](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) IÇIN Azure AD 'de bir hesapla oturum açın. Bir ağ geçidini yüklerken ve kaydederken Azure B2B (konuk) hesapları desteklenmez.
* Veri kaynakları bir Azure sanal ağı (VNet) üzerinde ise, [Alwaysusegateway](analysis-services-vnet-gateway.md) sunucusu özelliğini yapılandırmanız gerekir.
* Burada açıklanan (Unified) ağ geçidi, Azure Almanya bölgelerinde desteklenmez. Bunun yerine, portaldaki sunucunuzun **hızlı başlangıç** yüklü **Azure Analysis Services için adanmış şirket içi ağ geçidini**kullanın. 


## <a name="download"></a>İndirme

 [Ağ geçidini indirin](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Yükleyeceğiniz

1. Kurulumu çalıştırın.

2. Şirket **içi veri ağ geçidini**seçin.

   ![Şunu seçin:](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Bir konum seçin, koşulları kabul edin ve ardından **yükler**' i tıklatın.

   ![Konum ve lisans koşulları 'nı yükler](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Azure'da oturum açın. Hesabın kiracınızın Azure Active Directory olması gerekir. Bu hesap, Ağ Geçidi Yöneticisi için kullanılır. Ağ geçidini yüklerken ve kaydederken Azure B2B (konuk) hesapları desteklenmez.

   ![Azure'da oturum açma](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Bir etki alanı hesabıyla oturum açarsanız, Azure AD 'de Kurumsal hesabınıza eşlenir. Kuruluş hesabınız ağ geçidi Yöneticisi olarak kullanılır.

## <a name="register"></a>Kaydolunamadı

Azure 'da bir ağ geçidi kaynağı oluşturmak için, ağ geçidi bulut hizmeti ile yüklediğiniz yerel örneği kaydetmeniz gerekir. 

1.  **Bu bilgisayara yeni bir ağ geçidi Kaydet '** i seçin.

    ![Kaydolma](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Ağ geçidiniz için bir ad ve kurtarma anahtarı yazın. Varsayılan olarak ağ geçidi, aboneliğinizin varsayılan bölgesini kullanır. Farklı bir bölge seçmeniz gerekiyorsa, **bölgeyi değiştir**' i seçin.

    > [!IMPORTANT]
    > Kurtarma Anahtarınızı güvenli bir yere kaydedin. Bir ağ geçidini almak, geçirmek veya geri yüklemek için kurtarma anahtarı gereklidir. 

   ![Kaydolma](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Azure ağ geçidi kaynağı oluşturma

Ağ geçidinizin yüklenip kaydolduktan sonra Azure aboneliğinizde bir ağ geçidi kaynağı oluşturmanız gerekir. Ağ geçidini kaydederken kullandığınız hesapla Azure 'da oturum açın.

1. Azure portal, **kaynak oluştur ' a**tıklayın, sonra şirket **içi veri ağ geçidi**için arama yapın ve ardından **Oluştur**' a tıklayın.

   ![Ağ Geçidi kaynağı oluşturma](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. **Bağlantı ağ geçidi oluştur**' da şu ayarları girin:

   * **Ad**: ağ geçidi kaynağınız için bir ad girin. 

   * **Abonelik**: ağ geçidi kaynağınız Ile ilişkilendirilecek Azure aboneliğini seçin. 
   
     Varsayılan abonelik, oturum açmak için kullandığınız Azure hesabını temel alır.

   * **Kaynak grubu**: Kaynak grubu oluşturun veya mevcut bir kaynak grubunu seçin.

   * **Konum**: ağ geçidinizi kaydettiğiniz bölgeyi seçin.

   * **Yükleme adı**: ağ geçidi yüklemeniz zaten seçili değilse, bilgisayarınızda yüklü olan ve kayıtlı olan ağ geçidini seçin. 

     İşiniz bittiğinde **Oluştur**' a tıklayın.

## <a name="connect-servers"></a>Sunucuları ağ geçidi kaynağına bağlama

1. Azure Analysis Services sunucunuza genel bakış ' da, Şirket **Içi veri ağ geçidi**' ne tıklayın.

   ![Sunucuyu ağ geçidine bağlama](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. **Bağlanmak için bir şirket Içi veri ağ geçidi seçin**' de, ağ geçidi kaynağınızı seçin ve ardından **Seçili ağ geçidini bağla**' ya tıklayın.

   ![Sunucuyu ağ geçidi kaynağına bağlama](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Ağ geçidiniz listede görünmezse, sunucunuz büyük olasılıkla ağ geçidini kaydederken belirttiğiniz bölgeyle aynı bölgede yer almıyor olabilir.

    Sunucunuz ve ağ geçidi kaynağınız arasındaki bağlantı başarılı olursa, durum **bağlı**görünür.


    ![Sunucuyu ağ geçidi kaynağına bağlama başarılı](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

İşte bu kadar. Bağlantı noktalarını açmanız veya sorun giderme yapmanız gerekiyorsa, [Şirket içi veri ağ geçidini](analysis-services-gateway.md)kullanıma aldığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

* [Analysis Services’ı yönetme](analysis-services-manage.md)   
* [Azure Analysis Services veri al](analysis-services-connect.md)   
* [Azure Sanal Ağı üzerindeki veri kaynakları için ağ geçidi kullanma](analysis-services-vnet-gateway.md)
