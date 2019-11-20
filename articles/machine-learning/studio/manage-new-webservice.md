---
title: Web hizmetlerini yönetme
titleSuffix: ML Studio (classic) - Azure
description: Machine Learning yeni ve klasik Web hizmetlerinizi Microsoft Azure Machine Learning Web Hizmetleri portalını kullanarak yönetin. Klasik Web Hizmetleri ve yeni Web Hizmetleri, farklı temel teknolojileri temel alarak, bunların her biri için biraz farklı yönetim yeteneğiniz vardır.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 2e72c8a363ee7f58630b1c0d8d0f00434c2e2b7e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838784"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Azure Machine Learning Studio (klasik) Web Hizmetleri portalını kullanarak bir Web hizmetini yönetme
Machine Learning yeni ve klasik Web hizmetlerinizi Microsoft Azure Machine Learning Web Hizmetleri portalını kullanarak yönetebilirsiniz. Klasik Web Hizmetleri ve yeni Web Hizmetleri, farklı temel teknolojileri temel alarak, bunların her biri için biraz farklı yönetim yeteneğiniz vardır.

Machine Learning Web Hizmetleri portalında şunları yapabilirsiniz:

* Web hizmetinin nasıl kullanıldığını izleyin.
* Açıklamayı yapılandırın, Web hizmeti anahtarlarını güncelleştirin (yalnızca yeni), depolama hesabı anahtarınızı güncelleştirin (yalnızca yeni), günlüğü etkinleştirin ve örnek verileri etkinleştirin veya devre dışı bırakın.
* Web hizmetini silin.
* Faturalandırma planları oluşturma, silme veya güncelleştirme (yalnızca yeni).
* Uç nokta ekleme ve silme (yalnızca klasik)

>[!NOTE]
>Klasik Web hizmetlerini **Web Hizmetleri** sekmesinde [Machine Learning Studio (klasik)](https://studio.azureml.net) içinde de yönetebilirsiniz.

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Yeni Kaynak Yöneticisi tabanlı Web hizmetlerini yönetme izinleri

Yeni Web Hizmetleri Azure kaynakları olarak dağıtılır. Bu nedenle, yeni Web hizmetlerini dağıtmak ve yönetmek için doğru izinlere sahip olmanız gerekir.  Yeni Web hizmetlerini dağıtmak veya yönetmek için, Web hizmetinin dağıtıldığı abonelikte bir katkıda bulunan veya yönetici rolü atanması gerekir. Bir Machine Learning çalışma alanına başka bir kullanıcı davet ederseniz, Web hizmetlerini dağıtabilmeleri veya yönetebilmeniz için bunları abonelikte bir katkıda bulunan veya yönetici rolüne atamanız gerekir. 

Kullanıcı, Azure Machine Learning Web Hizmetleri portalındaki kaynaklara erişmek için doğru izinlere sahip değilse, bir Web hizmetini dağıtmaya çalışırken şu hatayı alırlar:

*Web hizmeti dağıtımı başarısız oldu. Bu hesabın, çalışma alanını içeren Azure aboneliğine yeterli erişimi yok. Bir Web hizmetini Azure 'a dağıtmak için, aynı hesabın çalışma alanına davet edilmesi ve çalışma alanını içeren Azure aboneliğine erişim verilmesi gerekir.*

Çalışma alanı oluşturma hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio (klasik) çalışma alanı oluşturma ve paylaşma](create-workspace.md).

Erişim izinlerini ayarlama hakkında daha fazla bilgi için bkz. [RBAC kullanarak erişimi yönetme ve Azure Portal](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Yeni Web hizmetlerini yönetme
Yeni Web hizmetlerinizi yönetmek için:

1. Microsoft Azure hesabınızı kullanarak [Microsoft Azure Machine Learning Web Hizmetleri](https://services.azureml.net/quickstart) portalında oturum açın-Azure aboneliğiyle ilişkili hesabı kullanın.
2. Menüsünde, **Web Hizmetleri**' ne tıklayın.

Bu, aboneliğiniz için dağıtılan Web hizmetlerinin bir listesini görüntüler. 

Bir Web hizmetini yönetmek için Web Hizmetleri ' ne tıklayın. Web Hizmetleri sayfasından şunları yapabilirsiniz:

* Yönetmek için Web hizmetine tıklayın.
* Web hizmeti 'nin güncelleştirilmesi için faturalandırma planına tıklayın.
* Bir Web hizmetini silin.
* Bir Web hizmetini kopyalayın ve başka bir bölgeye dağıtın.

Bir Web hizmetine tıkladığınızda, Web hizmeti hızlı başlangıç sayfası açılır. Web hizmeti hızlı başlangıç sayfasında, Web hizmetinizi yönetmenizi sağlayan iki menü seçeneği vardır:

* **Pano** -Web hizmeti kullanımını görüntülemenize izin verir.
* **Yapılandırma** -açıklayıcı metin eklemenize, Web hizmetiyle ilişkili depolama hesabının anahtarını güncelleştirmenize ve örnek verileri etkinleştirip devre dışı bırakmanıza olanak tanır.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Web hizmetinin nasıl kullanıldığını izleme
**Pano** sekmesine tıklayın.

Panodan, Web hizmetinizin genel kullanımını bir süre içinde görüntüleyebilirsiniz. Kullanım grafiklerinin sağ üst köşesindeki dönem açılan menüsünden görüntülenecek dönemi seçebilirsiniz. Panoda aşağıdaki bilgiler gösterilmektedir:

* **Zaman Içindeki istekler** , seçilen dönemdeki istek sayısının adım grafiğini görüntüler. Kullanımda ani artışlar yaşadığınızı belirlemenize yardımcı olabilir.
* **İstek-yanıt istekleri** , hizmetin seçilen süre boyunca aldığı ve kaç tane başarısız olduğu istek yanıtı çağrılarının toplam sayısını görüntüler.
* **Ortalama istek-yanıt Işlem süresi** , alınan isteklerin yürütülmesi için gereken sürenin ortalamasını görüntüler.
* **Batch istekleri** , hizmetin seçilen süre boyunca aldığı ve kaç tane başarısız olduğunu gösteren toplu işlem isteklerinin toplam sayısını görüntüler.
* **Ortalama Iş gecikmesi** , alınan isteklerin yürütülmesi için gereken sürenin ortalamasını görüntüler.
* **Hatalar** , Web hizmeti çağrılarında oluşan toplam hata sayısını görüntüler.
* Hizmet **maliyetleri** , hizmetle ilişkili faturalandırma planının ücretini görüntüler.

### <a name="configuring-the-web-service"></a>Web hizmetini yapılandırma
**Yapılandır** menü seçeneğine tıklayın.

Aşağıdaki özellikleri güncelleştirebilirsiniz:

* **Açıklama** , Web hizmeti için bir açıklama girmenize olanak sağlar.
* **Başlık** , Web hizmeti için bir başlık girmenizi sağlar
* **Anahtarlar** , birincil ve ikincil API anahtarlarınızı döndürmenize olanak tanır.
* **Depolama hesabı anahtarı** , Web hizmeti değişiklikleriyle ilişkili depolama hesabının anahtarını güncelleştirmenize olanak tanır. 
* **Örnek verileri etkinleştirme** , Istek-yanıt hizmetini test etmek için kullanabileceğiniz örnek veriler sağlamanıza olanak tanır. Web hizmetini Machine Learning Studio (klasik) ' de oluşturduysanız, örnek veriler modelinize eğitebilmeniz için kullandığınız verilerden alınır. Hizmeti programlı olarak oluşturduysanız, veriler JSON paketinin bir parçası olarak verdiğiniz örnek verilerden alınır.

### <a name="managing-billing-plans"></a>Faturalandırma planlarını yönetme
Web hizmetleri hızlı başlangıç sayfasından **planlar** menü seçeneğine tıklayın. Ayrıca, bu planı yönetmek için belirli bir Web hizmetiyle ilişkili plana tıklayabilirsiniz.

* **Yeni** bir plan oluşturmanıza olanak sağlar.
* **Plan örneği Ekle/Kaldır** , kapasite eklemek için var olan bir planı "ölçeklendirmenizi" sağlar.
* **Yükseltme/düşürme** , kapasite eklemek için var olan bir planı "ölçeklendirmenizi" sağlar.
* **Silme** , bir planı silmenizi sağlar.

Panosunu görüntülemek için bir plana tıklayın. Pano, seçilen bir süre boyunca anlık görüntü veya plan kullanımı sağlar. Görüntülenecek zaman aralığını seçmek için panonun sağ üst köşesindeki **nokta** açılan listesine tıklayın. 

Plan Panosu aşağıdaki bilgileri sağlar:

* **Plan açıklaması** , planla ilişkili maliyetler ve kapasite hakkındaki bilgileri görüntüler.
* **Plan kullanımı** , plana göre ücretlendirilen işlem ve işlem saatleri sayısını görüntüler.
* **Web Hizmetleri** , bu planı kullanan Web hizmetlerinin sayısını görüntüler.
* **Çağrılardan En Iyi Web hizmeti** , plana göre ücretlendirilen çağrı yapan en popüler dört Web hizmetini görüntüler.
* **Işlem saat başına En Iyi Web Hizmetleri** , plana göre ücretlendirilen işlem kaynaklarını kullanan en popüler dört Web hizmetini görüntüler.

## <a name="manage-classic-web-services"></a>Klasik Web hizmetlerini yönetme
> [!NOTE]
> Bu bölümdeki yordamlar, Azure Machine Learning Web Hizmetleri Portalı aracılığıyla klasik Web hizmetlerini yönetmeyle ilgilidir. Klasik Web hizmetlerini Machine Learning Studio (klasik) ve Azure portal aracılığıyla yönetme hakkında bilgi için bkz. [Azure Machine Learning Studio (klasik) çalışma alanını yönetme](manage-workspace.md).
> 
> 

Klasik Web hizmetlerinizi yönetmek için:

1. Microsoft Azure hesabınızı kullanarak [Microsoft Azure Machine Learning Web Hizmetleri](https://services.azureml.net/quickstart) portalında oturum açın-Azure aboneliğiyle ilişkili hesabı kullanın.
2. Menüde **Klasik Web Hizmetleri**' ne tıklayın.

Klasik bir Web hizmetini yönetmek için **Klasik Web Hizmetleri**' ne tıklayın. Klasik Web Hizmetleri sayfasından şunları yapabilirsiniz:

* İlişkili uç noktaları görüntülemek için Web hizmetine tıklayın.
* Bir Web hizmetini silin.

Klasik bir Web hizmetini yönetirken, uç noktaların her birini ayrı ayrı yönetirsiniz. Web Hizmetleri sayfasında bir Web hizmetine tıkladığınızda, hizmetle ilişkili uç noktaların listesi açılır. 

Klasik Web hizmeti uç noktası sayfasında, hizmette uç noktalar ekleyip silebilirsiniz. Uç nokta ekleme hakkında daha fazla bilgi için bkz. [uç nokta oluşturma](create-endpoint.md).

Web hizmeti hızlı başlangıç sayfasını açmak için uç noktalardan birine tıklayın. Hızlı Başlangıç sayfasında, Web hizmetinizi yönetmenizi sağlayan iki menü seçeneği vardır:

* **Pano** -Web hizmeti kullanımını görüntülemenize izin verir.
* **Yapılandırma** -açıklayıcı metin eklemenize, hata oturum açma ve kapatma yapmanıza, Web hizmetiyle ilişkili depolama hesabının anahtarını güncelleştirmenize ve örnek verileri etkinleştirip devre dışı bırakmanıza olanak tanır.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Web hizmetinin nasıl kullanıldığını izleme
**Pano** sekmesine tıklayın.

Panodan, Web hizmetinizin genel kullanımını bir süre içinde görüntüleyebilirsiniz. Kullanım grafiklerinin sağ üst köşesindeki dönem açılan menüsünden görüntülenecek dönemi seçebilirsiniz. Panoda aşağıdaki bilgiler gösterilmektedir:

* **Zaman Içindeki istekler** , seçilen dönemdeki istek sayısının adım grafiğini görüntüler. Kullanımda ani artışlar yaşadığınızı belirlemenize yardımcı olabilir.
* **İstek-yanıt istekleri** , hizmetin seçilen süre boyunca aldığı ve kaç tane başarısız olduğu istek yanıtı çağrılarının toplam sayısını görüntüler.
* **Ortalama istek-yanıt Işlem süresi** , alınan isteklerin yürütülmesi için gereken sürenin ortalamasını görüntüler.
* **Batch istekleri** , hizmetin seçilen süre boyunca aldığı ve kaç tane başarısız olduğunu gösteren toplu işlem isteklerinin toplam sayısını görüntüler.
* **Ortalama Iş gecikmesi** , alınan isteklerin yürütülmesi için gereken sürenin ortalamasını görüntüler.
* **Hatalar** , Web hizmeti çağrılarında oluşan toplam hata sayısını görüntüler.
* Hizmet **maliyetleri** , hizmetle ilişkili faturalandırma planının ücretini görüntüler.

### <a name="configuring-the-web-service"></a>Web hizmetini yapılandırma
**Yapılandır** menü seçeneğine tıklayın.

Aşağıdaki özellikleri güncelleştirebilirsiniz:

* **Açıklama** , Web hizmeti için bir açıklama girmenize olanak sağlar. Açıklama gerekli bir alandır.
* **Günlüğe kaydetme** , uç noktada hata günlüğü etkinleştirmenizi veya devre dışı bırakmanızı sağlar. Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [Machine Learning Web Hizmetleri için günlüğü](web-services-logging.md)etkinleştirme.
* **Örnek verileri etkinleştirme** , Istek-yanıt hizmetini test etmek için kullanabileceğiniz örnek veriler sağlamanıza olanak tanır. Web hizmetini Machine Learning Studio (klasik) ' de oluşturduysanız, örnek veriler modelinize eğitebilmeniz için kullandığınız verilerden alınır. Hizmeti programlı olarak oluşturduysanız, veriler JSON paketinin bir parçası olarak verdiğiniz örnek verilerden alınır.


