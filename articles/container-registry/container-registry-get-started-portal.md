---
title: "Özel Docker kayıt defteri oluşturma - Azure portalı | Microsoft Docs"
description: "Azure portalı ile özel Docker kapsayıcısı kayıt defterleri oluşturmaya ve yönetmeye başlayın"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e74c5428f0e31d9d3cf06b85aa8cefde868e9d67
ms.lasthandoff: 03/27/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Azure portalı kullanarak özel bir Docker kapsayıcı kayıt defteri oluşturma
Kapsayıcı kayıt defteri oluşturmak ve kayıt defteri ayarlarını yönetmek için Azure portalını kullanın. Ayrıca, [Azure CLI 2.0 komutlarını](container-registry-get-started-azure-cli.md) kullanarak veya Kapsayıcı Kayıt Defteri [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) ile programlı olarak da kapsayıcı kayıt defterleri oluşturup yönetebilirsiniz.

Arka plan ve kavramlar için, bkz: [genel bakış](container-registry-intro.md).



## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma
1. [Azure portalında](https://portal.azure.com) **+Yeni**’ye tıklayın.
2. Markette **Azure kapsayıcı kayıt defteri** ifadesini aratın.
3. Yayımcısı **Microsoft** olan **Azure Kapsayıcı Kayıt Defteri** uygulamasını seçin.
    ![Azure Market’te Container Kayıt Defteri hizmeti](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. **Oluştur**’a tıklayın. **Azure Kapsayıcı Kayıt Defteri** dikey penceresi görünür.

    ![Container kayıt defteri ayarları](./media/container-registry-get-started-portal/container-registry-settings.png)
5. **Azure Kapsayıcı Kayıt Defteri** dikey penceresinde aşağıdaki bilgileri girin. İşiniz bittiğinde **Oluştur**’a tıklayın.

    a. **Kayıt defteri adı**: Oluşturduğunuz kayıt defterinize özel olan, genel olarak benzersiz bir üst düzey etki alanı adı. Bu örnekte kayıt defterinin adı *myRegistry01*, ancak bunu kendi bulduğunuz bir adla değiştirin. Ad yalnızca harf ve sayı içerebilir.

    b. **Kaynak grubu**: Mevcut bir [kaynak grubu](../azure-resource-manager/resource-group-overview.md#resource-groups) seçin ya da yeni bir grup için ad yazın.

    c. **Konum**: Hizmetin [kullanılabilir](https://azure.microsoft.com/regions/services/) olduğu bir Azure veri merkezi bölgesi seçin, örneğin **Orta Güney ABD**.

    d. **Yönetici kullanıcı**: İsterseniz bir yönetici kullanıcıya kayıt defterine erişim izni verin. Kayıt defterini oluşturduktan sonra bu ayarı değiştirebilirsiniz.

    > [!IMPORTANT]
    > Kapsayıcı kayıt defterleri, bir yönetici kullanıcı hesabı aracılığıyla erişim sağlamaya ek olarak Azure Active Directory hizmet sorumluları tarafından desteklenen kimlik doğrulamasını destekler. Daha fazla bilgi ve göz önünde bulundurulması gereken diğer noktalar için bkz. [Kapsayıcı kayıt defteri ile kimlik doğrulama](container-registry-authentication.md).


    e. **Depolama hesabı**: Varsayılan ayarı kullanarak bir [depolama hesabı](../storage/storage-introduction.md) oluşturun veya aynı konumdaki mevcut bir depolama hesabını seçin. Premium Depolama şu anda desteklenmemektedir.


## <a name="manage-registry-settings"></a>Kayıt defteri ayarlarını yönetme
Kayıt defterini oluşturduktan sonra portaldaki **Container Kayıt Defterleri** dikey penceresinden başlayarak kayıt defteri ayarlarını bulun. Örneğin, kayıt defterinizde oturum açmak için ayarlara ihtiyacınız olabilir ya da yönetici kullanıcıyı etkinleştirmek veya devre dışı bırakmak isteyebilirsiniz.

1. **Container Kayıt Defterleri** dikey penceresinde kayıt defterinizin adına tıklayın.

    ![Container kayıt defteri dikey penceresi](./media/container-registry-get-started-portal/container-registry-blade.png)
2. Erişim ayarlarını yönetmek için **Erişim tuşu**’na tıklayın.

    ![Container kayıt defteri erişimi](./media/container-registry-get-started-portal/container-registry-access.png)
3. Aşağıdaki ayarlara dikkat edin:

   * **Oturum açma sunucusu** - Kayıt defterinde oturum açmak için kullandığınız tam ad. Bu örnekte bu değer `myregistry01.azurecr.io`’dur.
   * **Yönetici kullanıcı** - Kayıt defterindeki yönetici kullanıcı hesabını etkinleştirmek veya devre dışı bırakmak için düğmeyi değiştirin.
   * **Kullanıcı adı** ve **Parola** - Kayıt defterinde oturum açmak için kullanabileceğiniz yönetici kullanıcı hesabının (etkinse) kimlik bilgileri. İsteğe bağlı olarak parolaları yeniden oluşturabilirsiniz. Bir parolayı yeniden oluştururken diğer parolayı kullanarak kayıt defteri bağlantılarını sürdürebilmeniz için iki parola oluşturulur. Bunun yerine bir hizmet sorumlusu ile kimlik doğrulamak için bkz. [Özel bir Docker kapsayıcı kayıt defteri ile kimlik doğrulama](container-registry-authentication.md).

## <a name="next-steps"></a>Sonraki adımlar
* [Docker CLI’yı kullanarak ilk görüntünüzü itme](container-registry-get-started-docker-cli.md)

