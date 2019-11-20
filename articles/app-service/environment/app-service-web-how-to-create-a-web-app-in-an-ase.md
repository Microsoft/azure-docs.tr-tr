---
title: App Service Ortamı v1 'de Web uygulaması oluşturma-Azure
description: App Service Ortamı v1 'de Web uygulamaları ve App Service planları oluşturmayı öğrenin
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cc40c2296e583ab93a7c34d709cfbf1334ae3926
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069842"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>App Service Ortamı v1 'de Web uygulaması oluşturma

> [!NOTE]
> Bu makale App Service Ortamı v1 ile ilgilidir.  Daha güçlü altyapıda daha kolay ve çalışır App Service Ortamı daha yeni bir sürümü vardır. Yeni sürüm hakkında daha fazla bilgi edinmek için [App Service ortamı giriş](intro.md)ile başlayın.
> 

## <a name="overview"></a>Genel Bakış
Bu öğreticide, bir [App Service ortamı v1](app-service-app-service-environment-intro.md) 'de (Ao) Web uygulamaları ve App Service planları oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir. 

> [!NOTE]
> Bir Web uygulamasının nasıl oluşturulduğunu, ancak bir App Service Ortamı yapmanız gerekmediğini öğrenmek istiyorsanız, bkz. [.NET Web uygulaması oluşturma](../app-service-web-get-started-dotnet.md) veya diğer diller ve çerçeveler için ilgili öğreticilerden biri.
> 
> 

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticide bir App Service Ortamı oluşturmuş olduğunuz varsayılmaktadır. Henüz yapmadıysanız, bkz. [App Service ortamı oluşturma](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Web uygulaması oluşturma
1. [Azure portalında](https://portal.azure.com/) **> Web uygulaması Web ve mobil > kaynak oluştur ' a**tıklayın. 
   
    ![][1]
2. Aboneliğinizi seçin.  
   
    Birden çok aboneliğiniz varsa App Service Ortamı bir uygulama oluşturmayı biliyorsanız, ortamı oluştururken kullandığınız aboneliğin aynısını kullanmanız gerekir. 
3. Kaynak grubunu seçin veya oluşturun.
   
    *Kaynak grupları* , ilgili Azure kaynaklarını birim olarak yönetmenizi sağlar ve uygulamalarınız için *rol tabanlı erişim denetimi* (RBAC) kuralları oluştururken faydalıdır. Daha fazla bilgi için bkz. [Azure Resource Manager’a genel bakış][ResourceGroups]. 
4. Bir App Service planı seçin ya da oluşturun.
   
    *App Service planlar* , Web Apps 'in yönetilen kümeleridir.  Normal olarak, fiyatlandırma ' yı seçtiğinizde, ücretlendirilen fiyat ayrı uygulamalar yerine App Service planına uygulanır. ASA 'da, ASP 'iyle listelendikleriniz yerine asa 'ya ayrılan işlem örnekleri için ödeme yaparsınız.  Bir Web uygulamasının örnek sayısını ölçeklendirmek için App Service planınızın örneklerini ölçeklendirir ve bu plandaki tüm Web uygulamalarını etkiler.  Site yuvaları veya Sanal Ağ Tümleştirmesi gibi bazı özellikler de plan dahilinde miktar kısıtlamalarına sahiptir.  Daha fazla bilgi için bkz. [Azure App Service planlarına genel bakış](../overview-hosting-plans.md)
   
    Plan adı altında belirtilen konuma bakarak AŞIRINIZDEKI App Service planlarını belirleyebilirsiniz.  
   
    ![][5]
   
    App Service Ortamı zaten var olan bir App Service planını kullanmak istiyorsanız, bu planı seçin. Yeni bir App Service planı oluşturmak istiyorsanız, Bu öğreticinin aşağıdaki bölümüne bakın, [bir App Service Ortamı App Service planı oluşturun](#createplan).
5. Web uygulamanız için bir ad girin ve ardından **Oluştur**' a tıklayın. 
   
    Ao 'niz bir dış VIP kullanıyorsa Ao 'da bir uygulamanın URL 'SI: [*SiteName*]. [*App Service ortamı*]. p.azurewebsites.net yerine [*sitename*]. azurewebsites.net
   
    Ao 'niz bir Iç VIP kullanıyorsa, söz konusu Ao 'nun içindeki bir uygulamanın URL 'SI: [*SiteName*]. [*as oluşturma sırasında belirtilen alt etki alanı*]   
    AIN oluşturma sırasında ASP 'nizi seçtikten sonra, alt etki alanı güncelleştirmesini aşağıda **adı** görürsünüz

## <a name="createplan"></a>App Service planı oluşturma
Bir App Service Ortamı App Service planı oluşturduğunuzda, bir AO 'da paylaşılan çalışan olmadığı için çalışan seçenekleriniz farklı olacaktır.  Kullanmanız gereken çalışanlar, yönetici tarafından ATıCı 'e ayrıldıklardır.  Bu, yeni bir plan oluşturmak için, Ao çalışan havuzunuza zaten o çalışan havuzundaki tüm planlarınızda bulunan toplam örnek sayısından daha fazla çalışan olması gerektiğini belirtir.  Açi çalışan havuzunuzdaki planınızı oluşturmak için yeterli çalışanınız yoksa, bunları eklemek için Ao yöneticinizle birlikte çalışmanız gerekir.

Bir App Service Ortamı tarafından barındırılan App Service planlarındaki başka bir farklılık, fiyatlandırma seçiminin olmamasıdır.  Bir App Service Ortamı sahip olduğunuzda, sistem tarafından kullanılan işlem kaynakları için ödeme yaparsınız ve bu ortamdaki planlar için ücret ekleme ücreti yoktur.  Genellikle App Service bir plan oluşturduğunuzda, faturalandırmanızı belirleyen bir fiyatlandırma planı seçersiniz.  App Service Ortamı, aslında içerik oluşturabileceğiniz özel bir konumdur.  Ortamınız için ücret ödeyin ve içeriğinizi barındırmak için ödeme yaparsınız.

Aşağıdaki yönergelerde, öğreticinin önceki bölümünde açıklandığı gibi bir Web uygulaması oluştururken bir App Service planının nasıl oluşturulacağı gösterilmektedir.

1. Plan seçimi Kullanıcı arabiriminde **Yeni oluştur** ' a tıklayın ve genellıkle bir AO 'un dışında olduğu gibi planınız için bir ad sağlayın.
2. Konum seçicinizden kullanmak istediğiniz Ao 'yu seçin.
   
    App Service Ortamı aslında özel bir dağıtım konumu olduğundan konum altında gösterilir. 
   
    ![][2]
   
    Konum seçicideki bir AO seçildikten sonra App Service planı oluşturma Kullanıcı Arabirimi güncelleştirmeleri.  Konum artık açı sisteminin adını ve bulunduğu bölgeyi gösterir ve fiyatlandırma planı seçicisinin bir çalışan havuzu seçiciyle değiştirilmiştir.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Çalışan havuzu seçme
Normalde Azure App Service ve App Service Ortamı dışında, özel bir fiyat planının seçiminde kullanılabilir 3 işlem boyutu vardır.  Benzer bir şekilde, bir ASE için 3 adede kadar çalışan havuzu tanımlayabilir ve bu çalışan havuzu için kullanılan işlem boyutunu belirtebilirsiniz.  Bu, Ao 'nın kiracılar için, App Service planınız için işlem boyutu olan bir fiyatlandırma planı seçmek yerine, *çalışan havuzu*adı ' nı seçersiniz.  

Çalışan havuzu seçimi Kullanıcı arabirimi, bu çalışan havuzu için kullanılan işlem boyutunu adın altında gösterir.  Kullanılabilir miktar, bu havuzda kullanılmak üzere kaç işlem örneğinin kullanılabilir olduğunu gösterir.  Toplam Havuz gerçekte bu sayıdan daha fazla örneğe sahip olabilir, ancak bu değer, ne kadar çok kullanımda olduğunu ifade eder.  Daha fazla işlem kaynağı eklemek için App Service Ortamı ayarlamanız gerekiyorsa bkz. [App Service ortamı yapılandırma](app-service-web-configure-an-app-service-environment.md).

![][4]

Bu örnekte, kullanılabilir yalnızca iki çalışan havuzu görürsünüz. Bunun nedeni, Ao yöneticisinin yalnızca bu iki çalışan havuzlarında Konakları ayırdığından oluşur.  Üçüncü olarak, kendisine ayrılan VM 'Ler olduğunda bu durum gösterilir.  

## <a name="after-web-app-creation"></a>Web uygulaması oluşturulduktan sonra
Web uygulamalarını çalıştırmak ve hesaba alınması gereken bir AO 'da App Service planlarını yönetmek için bazı önemli noktalar vardır.  

Daha önce belirtildiği gibi, Ace 'nin sahibi sistemin boyutundan sorumludur ve sonuç olarak, istenen App Service planlarını barındırmak için yeterli kapasite olmasını sağlamaktan de sorumludur. Kullanılabilir çalışan yoksa App Service planınızı oluşturabileceksiniz.  Bu, Web uygulamanızı ölçeklendirmek için de geçerlidir.  Daha fazla örneğe ihtiyacınız varsa, daha fazla çalışan eklemek için App Service Ortamı yöneticinize almanız gerekir.

Web uygulamanızı oluşturduktan sonra plan App Service, ölçeği ölçeklendirmek iyi bir fikirdir.  Bir ASE 'de, uygulamalarınız için hataya dayanıklılık sağlamak üzere App Service planınızın en az 2 örneğe sahip olmanız gerekir.  Ao 'da bir App Service planının ölçeklendirilmesi, App Service planı Kullanıcı arabirimindeki normaldir.  Ölçeklendirme hakkında daha fazla bilgi için [App Service ortamı bir Web uygulamasını ölçeklendirme](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/resource-group-overview.md
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
