---
title: 'Öğretici: GitHub eylemleri için DevOps Starter kullanarak Node.js uygulamanızı Azure Web uygulamasına dağıtma'
description: DevOps Starter, Azure 'da çalışmaya başlamanızı ve Node.js uygulamanızı birkaç hızlı adımda Azure Web App 'e dağıtmayı kolaylaştırır.
ms.author: ninallam
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 08/25/2020
author: ninallam
ms.openlocfilehash: 4caac943df33177728997c74c9ec812105efd62c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588786"
---
# <a name="tutorial-deploy-nodejs-app-to-azure-web-app-using-devops-starter-for-github-actions"></a>Öğretici: GitHub eylemleri için DevOps Starter kullanarak Azure Web uygulamasına Node.js uygulaması dağıtma

GitHub eylemleri için DevOps Starter, Azure 'a dağıtmak üzere bir sürekli tümleştirme (CI) ve sürekli teslim (CD) iş akışı oluşturmak için örnek bir uygulama seçebileceğiniz basitleştirilmiş bir deneyim sunar. 

DevOps Starter ayrıca:
* , Yeni bir Azure Web uygulaması gibi otomatik olarak Azure kaynakları oluşturur.
* GitHub 'da CI için derleme işi içeren bir iş akışı oluşturur ve yapılandırır.
* İş akışı CD için bir dağıtım işi de içerir. 
* İzleme için bir Azure Application Insights kaynağı oluşturur.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Node.js uygulamasını dağıtmak için DevOps Starter 'ı kullanma
> * GitHub 'ı ve bir Azure aboneliğini yapılandırma 
> * GitHub iş akışını inceleyin
> * Değişiklikleri GitHub 'a işleyin ve Azure 'a otomatik olarak dağıtın
> * Application Insights izlemeyi yapılandırma
> * Kaynakları temizleme

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="use-devops-starter-to-deploy-a-nodejs-app"></a>Node.js uygulamasını dağıtmak için DevOps Starter 'ı kullanma

DevOps Starter, GitHub 'da bir iş akışı oluşturur. Mevcut bir GitHub organizasyonunu kullanabilirsiniz. DevOps Starter Ayrıca seçtiğiniz Azure aboneliğinde Web uygulaması gibi Azure kaynakları da oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png)

1. CI/CD sağlayıcısının **GitHub eylemleri** olarak seçildiğinden emin olun.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. **Node.js**' yi seçin ve ardından **İleri**' yi seçin.

1. **Uygulama çerçevesi seçin** altında **Express.js**' yi seçin ve ardından **İleri**' yi seçin. Önceki adımda seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler. 

1. **Windows Web uygulaması**' nı seçin ve ardından **İleri**' yi seçin.

## <a name="configure-github-and-an-azure-subscription"></a>GitHub 'ı ve bir Azure aboneliğini yapılandırma

1. **Yetkilendir** GitHub ve var olan bir GitHub organizasyonunu seçin. 

1. **GitHub deponuz** için bir ad girin. 

1. Azure abonelik hizmetlerinizi seçin. İsteğe bağlı olarak **Değiştir** ' i seçip Azure kaynaklarının konumu gibi daha fazla yapılandırma ayrıntıları girebilirsiniz.
 
1. Bir Web uygulaması adı girin ve **bitti**' yi seçin. Birkaç dakika sonra Azure Web uygulaması hazırlanacaktır. Örnek bir Node.js uygulaması GitHub kuruluşunuzda bir depoda ayarlanır, bir iş akışı tetiklenir ve uygulamanız yeni oluşturulan Azure Web uygulamasına dağıtılır.

   ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)    

   İşlem tamamlandıktan sonra DevOps başlangıç panosu Azure portal görüntülenir. Ayrıca, panoda doğrudan Azure portal **tüm kaynaklardan** da gidebilirsiniz. 

   Pano, GitHub kod deponuzu, CI/CD iş akışınızı ve çalışan uygulamanızı Azure 'da görünürlük sağlar.   

   ![Pano görünümü](_img/azure-devops-project-nodejs/full-dashboard.png)

DevOps Starter, kod değişikliklerini depoya dağıtan bir tetikleyiciyi otomatik olarak yapılandırır.
    
## <a name="examine-the-github-workflow"></a>GitHub iş akışını inceleyin

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

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

DevOps Starter, GitHub 'da bir depo oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdakileri yapın:

1. DevOps başlangıç panosunun sol tarafında, ana dalınızın bağlantısını seçin. Bu bağlantı, yeni oluşturulan GitHub deposunun bir görünümünü açar.

1. Depo kopyası URL 'sini görüntülemek için tarayıcının sağ üst köşesindeki **Kopyala** ' yı seçin. Git deponuzu en sevdiğiniz IDE’de kopyalayabilirsiniz. Sonraki birkaç adımda, kod değişikliklerini doğrudan ana dala getirmek ve yürütmek için Web tarayıcısını kullanabilirsiniz.

1. Tarayıcının sol tarafında **/Application/views/Index.Pug** dosyasına gidin.

1. **Düzenle**' yi seçin ve sonra bazı metinden değişiklik yapın.
    Örneğin, bazı metinleri etiketlerden biri için değiştirin.

1. **Yürüt**' ü seçin ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps başlangıç panosuna gidin.   
Şimdi sürmekte olan bir GitHub iş akışı derleme işini görmeniz gerekir. Yeni yaptığınız değişiklikler otomatik olarak bir GitHub iş akışı aracılığıyla oluşturulup dağıtılır.

1. Dağıtım tamamlandıktan sonra, değişikliklerinizi doğrulamak için uygulamanızı yenileyin.

## <a name="configure-azure-application-insights-monitoring"></a>Application Insights izlemeyi yapılandırma

Azure Application Insights ile, uygulamanızın performansını ve kullanımını kolayca izleyebilirsiniz. DevOps Starter, uygulamanız için bir Application Insights kaynağını otomatik olarak yapılandırır. Gerekirse başka uyarılar ve izleme özellikleri de yapılandırabilirsiniz.

1. Azure portal DevOps başlangıç panosuna gidin. 

1. Sağ alt köşedeki uygulamanızın **Application Insights** bağlantısını seçin. **Application Insights** bölmesi açılır. Bu görünüm uygulamanızın kullanım, performans ve kullanılabilirlik izleme bilgilerini içerir.

   ![Application Insights bölmesi](_img/azure-devops-project-github/appinsights.png) 

1. **Zaman aralığı**' nı seçin ve ardından **son saat**' i seçin. Sonuçları filtrelemek için **Güncelleştir**' i seçin. Artık son 60 dakikadan tüm etkinlikleri görüntüleyebilirsiniz. 
    
1. Zaman aralığından çıkmak için **x**' i seçin.

1. **Uyarılar**' ı seçin ve ardından **ölçüm uyarısı Ekle**' yi seçin. 

1. Uyarı için bir ad girin.

1. **Ölçüm** açılan listesinde çeşitli uyarı ölçümlerini inceleyin. Varsayılan uyarı, **1 saniyeden uzun sunucu yanıt süresi** içindir. Çeşitli uyarıları kolayca yapılandırıp uygulamanızın izleme özelliklerini geliştirebilirsiniz.

1. **E-posta sahipleri, katkıda bulunanlar ve okuyucular aracılığıyla bildir** onay kutusunu seçin. İsteğe bağlı olarak, bir Azure mantıksal uygulaması yürüterek bir uyarı görüntülendiğinde ek eylemler gerçekleştirebilirsiniz.

1. Uyarıyı oluşturmak için **Tamam ' ı** seçin. Birkaç dakika sonra, uyarı panoda etkin olarak görünür. 

1. **Uyarılar** alanından çıkıp **Application Insights** bölmesine geri dönün.

1. **Kullanılabilirlik**' i seçin ve ardından **Test Ekle**' yi seçin. 

1. Bir test adı girin ve ardından **Oluştur**' u seçin. Uygulamanızın kullanılabilirliğini doğrulamak için basit bir ping testi oluşturulur. Birkaç dakika sonra, test sonuçları sağlanır ve Application Insights panosu bir kullanılabilirlik durumu görüntüler.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Test ediyorsanız, kaynaklarınızı temizleyerek faturalandırma ücretlerinden kaçınabilirsiniz. Artık gerekli olmadığında, bu öğreticide oluşturduğunuz Azure sanal makinesini ve ilgili kaynakları silebilirsiniz. Bunu yapmak için DevOps başlangıç panosundaki **silme** işlevini kullanın. 

> [!IMPORTANT]
> Aşağıdaki yordam kaynakları kalıcı olarak siler. *Silme* işlevselliği, her iki Azure 'Da da DevOps Starter 'daki proje tarafından oluşturulan verileri yok eder ve bu işlemi geri alamazsınız. Bu yordamı yalnızca istemleri dikkatle okuduktan sonra kullanın.

1. Azure portal DevOps başlangıç panosuna gidin.
1. Sağ üst köşedeki **Sil**' i seçin. 
1. İstem sırasında, kaynakları *kalıcı olarak silmek* için **Evet** ' i seçin.

İsteğe bağlı olarak, ekibinizin ihtiyaçlarını karşılayacak şekilde iş akışını değiştirebilirsiniz. Bu CI/CD modelini diğer havuzlarınız için şablon olarak da kullanabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Node.js uygulamasını dağıtmak için DevOps Starter 'ı kullanma
> * GitHub 'ı ve bir Azure aboneliğini yapılandırma 
> * GitHub iş akışını inceleyin
> * Değişiklikleri GitHub 'a işleyin ve Azure 'a otomatik olarak dağıtın
> * Application Insights izlemeyi yapılandırma
> * Kaynakları temizleme

GitHub eylemleri ve iş akışları hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [GitHub iş akışını özelleştirme](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
