---
title: Jenkins dağıtımlarını Azure VM aracılarıyla ölçeklendirme.
description: Jenkins Azure VM Agents eklentisinin yüklü olduğu Azure sanal makineleriyle Jenkins işlem hatlarınızın kapasitesini artırın.
keywords: jenkins, azure, devops, sanal makine, aracılar
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 2e811d628c017316a5bc50a8ddc22ee24d6f744e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158539"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Talebi karşılamak için Jenkins dağıtımlarınızı Azure VM aracılarıyla ölçeklendirin

Bu öğreticide Jenkins [Azure VM Agents eklentisini](https://plugins.jenkins.io/azure-vm-agents) kullanarak Azure'da çalışan Linux sanal makineleriyle isteğe bağlı kapasite ekleme adımları gösterilmektedir.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Azure VM Agents eklentisini yükleme
> * Eklentiyi yapılandırarak Azure aboneliğinizde gerekli kaynakları oluşturma
> * Her aracı için kullanılabilir işlem kaynaklarını ayarlama
> * Her aracıya yüklenen işletim sistemini ve araçları ayarlama
> * Yeni bir serbest tarzda Jenkins işi oluşturma
> * İşi bir Azure VM aracısında çalıştırma

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliği
* Bir Jenkins ana sunucusu. Yoksa [hızlı başlangıcı](install-jenkins-solution-template.md) görüntüleyerek Azure'da bir tane ayarlayabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Azure VM Aracıları eklentisini yükleme

> [!TIP]
> Jenkins'i Azure'a [çözüm şablonunu](install-jenkins-solution-template.md) kullanarak dağıttıysanız Azure VM Agents eklentisi zaten yüklüdür.

1. Jenkins panosundan **Manage Jenkins** (Jenkins’i Yönet) ve ardından **Manage Plugins** (Eklentileri Yönet) öğelerini seçin.
1. **Available** (Kullanılabilir) sekmesini seçtikten sonra **Azure VM Agents** (Azure VM Aracıları) araması yapın. Eklentinin yanındaki onay kutusunu seçtikten sonra panonun en altında bulunan **Install without restart** (Yeniden başlatmadan yükle) öğesini seçin.

## <a name="configure-the-azure-vm-agents-plugin"></a>Azure VM Agents eklentisini yapılandırma

1. Jenkins panosundan **Manage Jenkins** (Jenkins'i Yönet) ve ardından **Configure System** (Sistemi Yapılandır) öğelerini seçin.
1. Sayfanın en altına gidin ve **Cloud** (Bulut) bölümündeki **Add new cloud** (Yeni bulut ekle) açılan menüsünden **Microsoft Azure VM Agents** (Microsoft Azure VM Aracıları) öğesini seçin.
1. **Azure Credentials** (Azure Kimlik Bilgileri) bölümündeki **Add** (Ekle) açılan menüsünden var olan hizmet sorumlularından birini seçin. Liste boşsa aşağıdaki adımları izleyerek Azure hesabınız için [bir hizmet sorumlusu oluşturun](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) ve bunu Jenkins yapılandırmanıza ekleyin:   

    a. **Azure Credentials** (Azure Kimlik Bilgileri) bölümünden **Add** (Ekle) öğesini ve ardından **Jenkins**'i seçin.   
    b. **Add Credentials** (Kimlik Bilgilerini Ekle) iletişim kutusundaki **Kind** (Tür) açılan menüsünden **Microsoft Azure Service Principal** (Microsoft Azure Hizmet Sorumlusu) girişini seçin.   
    c. Azure CLI veya [Cloud Shell](/azure/cloud-shell/overview) ile bir Active Directory hizmet sorumlusu oluşturun.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Hizmet sorumlusunun kimlik bilgilerini **Add credentials** (Kimlik bilgilerini ekle) iletişim kutusuna girin. Azure abonelik kimliğinizi bilmiyorsanız CLI ile sorgulayabilirsiniz:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    Tamamlanmış hizmet sorumlusu `id`Abonelik Kimliği**için** alanını, `appId`İstemci Kimliği**için** değerini, `password`Gizli Anahtar**için** değerini, `tenant`Kiracı Kimliği**için de** değerini kullanmalıdır. **Add** (Ekle) öğesine tıklayarak hizmet sorumlusunu ekleyin ve eklentiyi yeni oluşturulan hizmet sorumlusunu kullanacak şekilde yapılandırın.

    ![Azure hizmet sorumlusunu yapılandırma](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

1. **Resource Group Name** (Kaynak Grubu Adı) bölümünde **Create new** (Yeni oluştur) değerini bırakın ve `myJenkinsAgentGroup` yazın.
1. **Verify configuration** (Yapılandırmayı doğrula) bağlantısını seçerek profil ayarlarını test etmek için Azure'a bağlanın.
1. Eklenti yapılandırmasını güncelleştirmek için **Apply** (Uygula) öğesini seçin.

## <a name="configure-agent-resources"></a>Aracı kaynaklarını yapılandırma

Bir Azure VM aracısı tanımlamak için kullanılacak şablonu yapılandırın. Bu şablon, yeni oluşturulan her aracıda bulunacak işlem kaynaklarını tanımlar.

1. **Add Azure Virtual Machine Template** (Azure Sanal Makine Şablonu Ekle) bölümündeki **Add** (Ekle) öğesini seçin.
1. `defaulttemplate`Name **(Ad) alanına** yazın.
1. `ubuntu`Label **(Etiket) alanına** yazın.
1. Birleşik giriş kutusundan istediğiniz [Azure region](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) (Azure bölgesi) seçimini yapın.
1. [Virtual Machine Size](/azure/virtual-machines/linux/sizes) (Sanal Makine Boyutu) bölümündeki **VM size** (VM boyutu) açılan menüsünden seçim yapın. Bu öğretici için genel amaçlı `Standard_DS1_v2` boyutu yeterli olacaktır.   
1. **Retention time** (Saklama süresi) değerini `60` olarak bırakın. Bu ayar, boştaki aracıları serbest bırakmadan önce Jenkins’in kaç dakika bekleyeceğini belirler. Boştaki aracıların otomatik olarak kaldırılmasını istemiyorsanız 0 değerini belirtin.

   ![Genel VM yapılandırması](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Aracı işletim sistemini ve araçları yapılandırma

Eklenti yapılandırma ayarlarının **Image Configuration** (Görüntü Yapılandırması) bölümünde **Ubuntu 16.04 LTS** öğesini seçin. **Install Git (Latest)** (Git'i Yükle (En Son)), **Install Maven (V3.5.0)** (Maven'ı Yükle) ve **Install Docker** (Docker'ı Yükle) onay kutularını işaretleyerek bu araçların yeni oluşturulan aracılara yüklenmesini sağlayın.

![VM işletim sistemini ve araçları yapılandırma](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

**Admin Credentials** (Yönetici Kimlik Bilgileri) bölümünde **Add** (Ekle) öğesini ve ardından **Jenkins**'i seçin. Aracılarda oturum açmak için kullanılacak bir kullanıcı adı ve parola girin ve bu bilgilerin Azure VM'leri üzerindeki yönetici hesaplarına yönelik [kullanıcı adı ve parola ilkesine](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) uygun olduğundan emin olun.

Yapılandırmayı doğrulamak için **Verify Template** (Şablonu Doğrula) öğesini seçin ve ardından **Save** (Kaydet) öğesini seçerek değişikliklerinizi kaydedip Jenkins panosuna dönün.

## <a name="create-a-job-in-jenkins"></a>Jenkins içinde iş oluşturma

1. Jenkins panosunda **Yeni Öğe**’ye tıklayın. 
1. Ad için `demoproject1` yazın, **Freestyle project** (Serbest tarzda proje) ve ardından **Tamam** öğesini seçin.
1. **General** (Genel) sekmesinde **Restrict where project can be run** (Projenin nerede çalıştırılabileceğini kısıtla) öğesini seçip `ubuntu`Label Expression **(Etiket İfadesi) alanına** yazın. Etiketin bir önceki adımda oluşturulan buluta yapılandırması tarafında sunulduğunu onaylayan bir ileti göreceksiniz. 
   ![İşi ayarlama](./media/jenkins-azure-vm-agents/job-config.png)
1. **Source Code Management** (Kaynak Kod Yönetimi) sekmesinde **Git**'i seçin ve **Repository URL** (Depo URL'si) alanına şu URL'yi yazın: `https://github.com/spring-projects/spring-petclinic.git`
1. **Build** (Derleme) sekmesinde **Add build step** (Derleme adımı ekle) ve ardından **Invoke top-level Maven targets** (Üst düzey Maven hedeflerini çağır) öğesini seçin. `package`Goals **(Hedefler) alanına** yazın.
1. İş tanımını kaydetmek için **Save** (Kaydet) öğesini seçin.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Yeni işi Azure VM aracısında derleme

1. Jenkins panosuna geri dönün.
1. Bir önceki adımda oluşturduğunuz işi seçin ve **Build now** (Şimdi derle) öğesine tıklayın. Yeni bir derleme kuyruğa alınır ancak Azure aboneliğinizde bir aracı VM oluşturulana kadar başlatılmaz.
1. Derleme tamamlandıktan sonra **Konsol çıktısı**’na gidin. Derlemenin Azure aracısı üzerinde uzaktan gerçekleştirildiğini görürsünüz.

![Konsol çıktısı](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins eklentisiyle ilgili sorunlarını giderme

Jenkins eklentileriyle ilgili hatalarla karşılaşırsanız [Jenkins JIRA](https://issues.jenkins-ci.org/) sayfasında söz konusu bileşenle ilgili sorun bildirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure App Service'e CI/CD](java-deploy-webapp-tutorial.md)