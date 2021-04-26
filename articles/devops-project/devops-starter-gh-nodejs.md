---
title: "Hızlı başlangıç: Azure 'a dağıtmak üzere GitHub için Node.js-DevOps Starter için bir CI/CD iş akışı oluşturma"
description: DevOps Starter, GitHub eylemlerini kullanarak Azure 'da çalışmaya başlamanızı kolaylaştırır.
author: ninallam
editor: ninallam
ms.assetid: ''
ms.devlang: na
ms.service: az-devops-project
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 08/24/2020
ms.author: ninallam
ms.openlocfilehash: e3c72ce6a15f90f0dbe08bbff10db0ca5f6b5c8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588820"
---
# <a name="set-up-cicd-for-a-nodejs-app-with-devops-starter-using-github-actions"></a>GitHub eylemlerini kullanarak DevOps Starter ile Node.js bir uygulama için CI/CD ayarlama

Bu hızlı başlangıçta, GitHub eylemlerini kullanarak Node.js uygulamanıza yönelik bir sürekli tümleştirme (CI) ve sürekli teslim (CD) iş akışı ayarlamak için Basitleştirilmiş DevOps Starter deneyimini kullanırsınız. Uygulamanızı geliştirmek, dağıtmak ve izlemek için ihtiyacınız olan her şeyi ayarlamak için DevOps Starter 'ı kullanabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Bir [GitHub](https://github.com/) hesabı.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

DevOps Starter, GitHub eylemlerini kullanarak bir CI/CD iş akışı oluşturur. DevOps Starter Ayrıca seçtiğiniz Azure aboneliğindeki Azure kaynaklarını da oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçme

1. Sağ taraftaki başlıkta **GitHub Ile DevOps Starter 'ı ayarlama** ' ya tıklayın.

    ![landing_page](_img/azure-devops-project-nodejs/landing-page.png)

1. CI/CD sağlayıcısının **GitHub eylemleri** olarak seçildiğinden emin olun.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. **Node.js** örnek uygulamasını seçin. Node.js örnekleri birkaç uygulama çerçevesi seçeneği içerir.

1. Varsayılan örnek çerçeve **Express.js**’dir. Varsayılan ayarı bırakın ve ardından **İleri**' yi seçin.   

2. Windows Web App varsayılan dağıtım hedefidir. Daha önce seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler. Varsayılan hizmeti bırakın ve ardından **İleri**' yi seçin.
 
## <a name="configure-github-account-and-an-azure-subscription"></a>GitHub hesabı ve bir Azure aboneliği yapılandırma 

1. GitHub ile kimlik doğrulaması yapın.

   1. **Yetkilendir** düğmesine tıklayın. 
   
   1. GitHub 'da oturum açın. GitHub hesabınız yoksa, burada da kaydolabilirsiniz.

2. Mevcut bir **GitHub organizasyonunu** seçin. 
   
   1. GitHub deponuz için bir ad seçin. 
   
   1. Azure aboneliğinizi ve konumunuzu seçin, uygulamanız için bir ad seçin ve **bitti**' yi seçin.
    
       ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)


    Birkaç dakika sonra DevOps başlangıç panosu Azure portal görüntülenir. Örnek uygulama, Azure DevOps kuruluşunuzda bir depoda ayarlanır, bir GitHub iş akışı tetiklenir ve uygulamanız Azure 'a dağıtılır. Bu Pano, kod deponuza, GitHub iş akışınıza ve Azure 'daki uygulamanıza ilişkin görünürlük sağlar.
   
3. Çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.
    
    Pano, GitHub iş akışı ve Azure kaynakları ayrıntılarını içerir. En son çalıştırma, işlemeler ve iş durumu gibi GitHub iş akışının ayrıntılarını görüntülemek için **GitHub 'A yetki** vermeniz gerekir.
   
   ![Authorize_dashboard](_img/azure-devops-project-nodejs/authenticate-dashboard.png)

DevOps Starter, GitHub eylemleri kullanılarak derleme ve dağıtma işleri ile otomatik olarak bir GitHub iş akışı yapılandırdı. Artık en son çalışmanızı otomatik olarak web sitenize dağıtan bir CI/CD işlemiyle Node.js uygulaması üzerinde bir ekiple birlikte çalışmaya hazırsınız.

   ![dashooard_view](_img/azure-devops-project-nodejs/full-dashboard.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

DevOps Starter, GitHub 'da bir depo oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdakileri yapın:

1. DevOps başlangıç panosunun sol tarafında, ana dalınızın bağlantısını seçin. Bu bağlantı, yeni oluşturulan GitHub deposunun bir görünümünü açar.

1. Depo kopyası URL 'sini görüntülemek için tarayıcının sağ üst köşesindeki **Kopyala** ' yı seçin. Git deponuzu en sevdiğiniz IDE’de kopyalayabilirsiniz. Sonraki birkaç adımda, kod değişikliklerini doğrudan ana dala getirmek ve yürütmek için Web tarayıcısını kullanabilirsiniz.

1. Tarayıcının sol tarafında **/Application/views/Index.Pug** dosyasına gidin.

1. **Düzenle**' yi seçin ve sonra bazı metinden değişiklik yapın.
    Örneğin, bazı metinleri etiketlerden biri için değiştirin.

1. **Yürüt**' ü seçin ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps başlangıç panosuna gidin.   
Şimdi sürmekte olan bir GitHub iş akışı derleme işini görmeniz gerekir. Yaptığınız değişiklikler otomatik olarak bir GitHub iş akışı aracılığıyla oluşturulup dağıtılır.

## <a name="view-the-github-workflow"></a>GitHub iş akışını görüntüleme

Önceki adımda, DevOps Starter tam bir GitHub iş akışını otomatik olarak yapılandırdı. İş akışını gereken şekilde araştırıp özelleştirin. İş akışını öğrenmek için aşağıdaki adımları uygulayın.

1. DevOps başlangıç panosunun sol tarafında **GitHub iş akışı**' nı seçin. Bu bağlantı, yeni projeniz için bir tarayıcı sekmesi ve GitHub iş akışı açar.
    > [!NOTE]
    > İş akışı dosyasını yeniden adlandırmayın. Panodaki değişiklikleri yansıtması için iş akışı dosyasının adı **DevOps-Starter-Workflow. yıml** olmalıdır

1. Workflow YAML dosyası, uygulamayı derlemek ve dağıtmak için gereken tüm GitHub eylemlerini içerir. İş akışı dosyanızı özelleştirmek için **Dosya Düzenle** seçeneğine tıklayın.

1. Deponun **kod** sekmesi altında **işlemeler**' e tıklayın. Bu görünüm, belirli bir dağıtımla ilişkili kod işlemelerini gösterir.

1. Deponun **Eylemler** sekmesinde, Deponuzdaki tüm iş akışı çalıştırmalarının geçmişini görüntüleyebilirsiniz.

1. İş akışında çalıştırılan tüm işleri görüntülemek için **en son çalıştırmayı** seçin.

1. İş akışı çalıştırmasının ayrıntılı günlüklerini görüntülemek için **işler** ' e tıklayın. Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunlar hem dağıtım sırasında hem de sonrasında görüntülenebilir.

1. Deponuzdaki tüm çekme isteklerini görüntülemek için **çekme isteği** sekmesine tıklayın

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda, Azure App Service ve diğer ilgili kaynakları silebilirsiniz. DevOps başlangıç panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

CI/CD işleminizi yapılandırdığınızda, GitHub iş akışı otomatik olarak oluşturulmuştur. Bu iş akışını, takımınızın ihtiyaçlarını karşılayacak şekilde değiştirebilirsiniz. GitHub eylemleri ve iş akışı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [GitHub iş akışını özelleştirme](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
