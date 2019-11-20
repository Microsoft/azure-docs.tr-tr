---
title: Web hizmeti parametreleri-Azure Machine Learning Studio (klasik) | Microsoft Docs
description: Web hizmetine erişildiğinde modelinizin davranışını değiştirmek için Azure Machine Learning Web hizmeti parametrelerini kullanma.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: 62c6488cfcb30c969c388343c766c482cff7e03b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466996"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Azure Machine Learning Studio (klasik) Web hizmeti parametrelerini kullanma
Yapılandırılabilir parametrelere sahip modüller içeren bir deneme yayımlayarak bir Azure Machine Learning Web hizmeti oluşturulur. Bazı durumlarda, Web hizmeti çalışırken modül davranışını değiştirmek isteyebilirsiniz. *Web hizmeti parametreleri* bu görevi yapmanıza izin verir. 

Ortak bir örnek, Web hizmetine erişildiğinde yayınlanan Web hizmeti kullanıcısının farklı bir veri kaynağı belirleyebilmesi için [verileri Içeri aktarma][reader] modülünü ayarlamadır. Ya da [dışarı aktarma veri][writer] modülünü farklı bir hedefin belirtime için yapılandırma. Diğer bazı örnekler, [özellik karma][feature-hashing] modülünün bit sayısını veya [filtre tabanlı özellik seçimi][filter-based-feature-selection] modülünün istenen özelliklerinin sayısını değiştirmeyi içerir. 

Web hizmeti parametreleri ayarlayabilir ve denemenizin bir veya daha fazla modül parametresiyle ilişkilendirip gerekli veya isteğe bağlı olup olmadığını belirtebilirsiniz. Web hizmeti 'nin kullanıcısı, Web hizmetini çağırdığınızda bu parametrelerin değerlerini sağlayabilir. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Web hizmeti parametreleri ayarlama ve kullanma
Bir modülün parametresinin yanındaki simgeye tıklayıp "Web hizmeti parametresi olarak ayarla" yı seçerek bir Web hizmeti parametresi tanımlarsınız. Bu, yeni bir Web hizmeti parametresi oluşturur ve onu bu modül parametresine bağlar. Daha sonra, Web hizmetine erişildiğinde, Kullanıcı Web hizmeti parametresi için bir değer belirtebilir ve modül parametresine uygulanır.

Bir Web hizmeti parametresini tanımladıktan sonra, bu, denemenin içindeki başka bir modül parametresi için kullanılabilir. Bir modül için parametresiyle ilişkili bir Web hizmeti parametresi tanımlarsanız, parametre aynı değer türünü beklediği sürece aynı Web hizmeti parametresini başka bir modül için kullanabilirsiniz. Örneğin, Web hizmeti parametresi sayısal bir değer ise, yalnızca sayısal bir değer bekleyen modül parametreleri için kullanılabilir. Kullanıcı Web hizmeti parametresi için bir değer ayarladığında, ilişkili tüm modül parametrelerine uygulanır.

Web hizmeti parametresi için varsayılan değer sağlayıp sağlamamaya karar verebilirsiniz. Bunu yaparsanız, parametresi Web hizmeti kullanıcısı için isteğe bağlıdır. Varsayılan bir değer sağlamazsanız, Web hizmetine erişildiğinde kullanıcının bir değer girmesi gerekir.

Web hizmetine yönelik API belgeleri Web hizmetine erişirken Web hizmeti parametresinin programlı bir şekilde nasıl belirtilmesinin yapılacağı Web hizmeti kullanıcısına ilişkin bilgiler içerir.

> [!NOTE]
> Klasik bir Web hizmeti için API belgeleri, Machine Learning Studio (klasik) içindeki Web hizmeti **panosundaki** **API Yardım sayfası** bağlantısı aracılığıyla sağlanır. Yeni bir Web hizmetine yönelik API belgeleri, Web hizmetiniz için **kullanım ve** **Swagger API** sayfalarında [Azure Machine Learning Web Hizmetleri](https://services.azureml.net/Quickstart) portalı aracılığıyla sağlanır.
> 
> 

## <a name="example"></a>Örnek
Örnek olarak, Azure Blob depolama alanına bilgi gönderen bir [dışarı aktarma veri][writer] modülünün denediğimiz bir deneyime sahip olduğunu varsayalım. Web hizmeti kullanıcısının, hizmete erişildiğinde blob depolaması yolunu değiştirmesine izin veren "blob yolu" adlı bir Web hizmeti parametresi tanımlayacağız.

1. Machine Learning Studio klasik sürümünde, [verileri dışarı aktarma][writer] modülüne tıklayarak seçin. Özellikleri, deneme tuvalinin sağ tarafındaki Özellikler bölmesinde gösterilir.
2. Depolama türünü belirtin:
   
   * **Lütfen veri hedefini belirtin**bölümünde "Azure Blob depolama" seçeneğini belirleyin.
   * **Lütfen kimlik doğrulama türünü belirtin**altında "hesap" seçeneğini belirleyin.
   * Azure Blob depolama alanı için hesap bilgilerini girin. 

3. **Kapsayıcı parametresi ile başlayan blob yolunun**sağındaki simgeye tıklayın. Şöyle görünür:
   
   ![Web hizmeti parametre simgesi](./media/web-service-parameters/icon.png)
   
   "Web hizmeti parametresi olarak ayarla" yı seçin.
   
   Özellikler bölmesinin alt kısmındaki **Web hizmeti parametreleri** altına, "kapsayıcı ile başlayan blob yolu" adıyla bir giriş eklenir. Bu, şu anda bu [veri verme][writer] modülü parametresiyle Ilişkili olan Web hizmeti parametresidir.
4. Web hizmeti parametresini yeniden adlandırmak için ada tıklayın, "blob yolu" yazın ve **ENTER** tuşuna basın. 
5. Web hizmeti parametresi için varsayılan bir değer sağlamak üzere, adın sağ tarafındaki simgeye tıklayın, "varsayılan değer sağla" seçeneğini belirleyin, bir değer girin (örneğin, "kapsayıcı1/output1. csv") ve **ENTER** tuşuna basın.
   
   ![Web hizmeti parametresi](./media/web-service-parameters/parameter.png)
6. **Çalıştır**’a tıklayın. 
7. Web **Hizmeti Dağıt ' a** tıklayın ve Web hizmetini dağıtmak için Web Hizmeti Dağıt **[klasik]** veya Web hizmeti dağıtma [ **Yeni]** seçeneğini belirleyin.

> [!NOTE] 
> Yeni bir Web hizmeti dağıtmak için, Web hizmetini dağıttığınız abonelikte yeterli izinlere sahip olmanız gerekir. Daha fazla bilgi için, [Azure Machine Learning Web Hizmetleri portalını kullanarak bir Web hizmetini yönetme](manage-new-webservice.md)makalesine bakın. 

Web hizmetinin kullanıcısı artık Web hizmetine erişirken [verileri dışarı aktarma][writer] modülü için yeni bir hedef belirtebilir.

## <a name="more-information"></a>Daha fazla bilgi
Daha ayrıntılı bir örnek için [Machine Learning blogdaki](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) [Web hizmeti parametreleri](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) girişine bakın.

Machine Learning Web hizmetine erişme hakkında daha fazla bilgi için bkz. [Azure Machine Learning Web hizmeti kullanma](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

