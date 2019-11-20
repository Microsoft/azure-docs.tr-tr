---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ddeb46a2c7bc7f24f55c22f446926529cee7b598
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059850"
---
Etki alanı adınızın kayıtları dağıtıldıktan sonra, Azure App Service ' deki Web uygulamanıza erişmek için özel etki alanı adınızın kullanılabildiğini doğrulamak üzere tarayıcınızı kullanabilmeniz gerekir.

> [!NOTE]
> CNAME 'nizin DNS sistemine yayılması biraz zaman alabilir. CNAME 'in kullanılabildiğini doğrulamak için gibi <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> bir hizmet kullanabilirsiniz.
> 
> 

Web uygulamanızı zaten bir Traffic Manager uç noktası olarak eklemediyseniz, özel etki alanı adı Traffic Manager yönlendirmeleri için ad çözümlemesi çalışmadan önce bunu yapmanız gerekir. Traffic Manager daha sonra Web uygulamanıza yönlendirir. Web uygulamanızı Traffic Manager profilinize bir uç nokta olarak eklemek için [uç nokta ekleme veya silme](../articles/traffic-manager/traffic-manager-endpoints.md) bölümündeki bilgileri kullanın.

> [!NOTE]
> Bir uç nokta eklenirken Web uygulamanız listelenmiyorsa, onun **standart** App Service plan modu için yapılandırıldığını doğrulayın. Traffic Manager çalışmak için Web uygulamanız için **Standart** Mod kullanmanız gerekir.
> 
> 

1. Tarayıcınızda [Azure portalını](https://portal.azure.com)açın.
2. **Web Apps** sekmesinde Web uygulamanızın adına tıklayın, **Ayarlar**' ı seçin ve ardından **özel etki alanları** ' nı seçin.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. **Özel etki alanları** dikey penceresinde **ana bilgisayar adı Ekle**' ye tıklayın.
4. Bu Web uygulamasıyla ilişkilendirilecek özel etki alanı adını girmek için **hostname** metin kutularını kullanın.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Etki alanı adı yapılandırmasını kaydetmek için **Doğrula** ' ya tıklayın.
6. Azure **doğrulaması** ' na tıkladıktan sonra etki alanı doğrulama iş akışını devre dışı bırakır. Bu, etki alanı sahipliğinin yanı sıra ana bilgisayar kullanılabilirliği ve hatanın nasıl düzeltileceğiyle ilgili olarak, hata veya ayrıntılı bir hata olduğunu denetler.    
7. Doğrulama başarıyla tamamlandığında **ana bilgisayar adı Ekle** düğmesi etkin olur ve ana bilgisayar adı ata ' yı kullanabilirsiniz. Şimdi bir tarayıcıda özel etki alanı adına gidin. Şimdi uygulamanızın özel etki alanı adınızı kullanarak çalıştığını görmeniz gerekir. 
   
   Yapılandırma tamamlandıktan sonra, Web uygulamanızın **etki alanı adları** bölümünde özel etki alanı adı listelenir.

Bu noktada, Traffic Manager etki alanı adını tarayıcınızda girebilmeniz ve Web uygulamanıza başarıyla gidebilmesi gerektiğini görmeniz gerekir.

