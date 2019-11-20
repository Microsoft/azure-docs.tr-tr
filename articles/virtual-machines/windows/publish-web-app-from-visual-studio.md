---
title: Visual Studio 'dan bir Azure VM 'de Web uygulaması yayımlama
description: Visual Studio 'dan bir Azure sanal makinesine ASP.NET Web uygulaması yayımlama
services: virtual-machines-windows
author: ghogen
manager: jillfra
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 6e5db9b4e46019aa386057d51d956ff11d90f498
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970874"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Visual Studio 'dan bir Azure VM 'de ASP.NET Web uygulaması yayımlama

Bu belgede, Visual Studio 2019 ' de **Microsoft Azure sanal makineler** yayımlama özelliğini kullanarak bir Azure sanal MAKINESINE (VM) bir ASP.NET Web uygulamasının nasıl yayımlanacağı açıklanmaktadır.  

## <a name="prerequisites"></a>Önkoşullar
Bir ASP.NET projesini bir Azure sanal makinesine yayımlamak için Visual Studio 'yu kullanabilmek üzere VM 'nin doğru şekilde ayarlanması gerekir.

- Makinenin bir ASP.NET Web uygulaması çalıştıracak şekilde yapılandırılması ve WebDeploy 'nin yüklü olması gerekir.

- VM 'nin yapılandırılmış bir DNS adı olmalıdır. Daha fazla bilgi için bkz. [WINDOWS VM için Azure Portal tam etki alanı adı oluşturma](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Visual Studio 'Yu kullanarak ASP.NET Web uygulamanızı Azure VM 'de yayımlayın
Aşağıdaki bölümde, mevcut bir ASP.NET Web uygulamasının bir Azure sanal makinesine nasıl yayımlanacağı açıklanmaktadır.

1. Web uygulaması çözümünüzü Visual Studio 2019 ' de açın.
2. Çözüm Gezgini projeye sağ tıklayın ve Yayımla ' yı seçin **...**
3. **Microsoft Azure sanal makineler**bulana kadar yayınlama seçeneklerinde gezinmek için sayfanın sağ tarafındaki oku kullanın.  

   ![Yayımla sayfası-sağ ok]

4. **Microsoft Azure sanal makineler** simgesini seçin ve **Yayımla**' yı seçin.

   ![Yayımlama sayfası-Microsoft Azure sanal makine simgesi]

5. Uygun hesabı seçin (sanal makinenize bağlı Azure aboneliği ile).  
   - Visual Studio 'da oturum açtıysanız, hesap listesi tüm kimliği doğrulanmış hesaplarınızla doldurulur.  
   - Oturum açmadıysanız veya ihtiyacınız olan Hesap listelenmiyorsa, "Hesap Ekle..." öğesini seçin. ve oturum açmak için istemleri takip edin.  
   ![Azure hesap Seçicisi]  

6. Mevcut sanal makineler listesinden uygun VM 'yi seçin.

   > [!Note]
   > Bu listenin doldurulması biraz zaman alabilir.

   ![Azure VM Seçicisi]

7. Yayımlamaya başlamak için Tamam ' ı tıklatın.

8. Kimlik bilgileri istendiğinde, yayımlama haklarıyla yapılandırılan hedef VM 'de bir kullanıcı hesabının kullanıcı adını ve parolasını sağlayın. Bu kimlik bilgileri genellikle VM oluşturulurken kullanılan yönetici Kullanıcı adı ve parolasıdır.  

   ![WebDeploy oturumu açma]

9. Güvenlik sertifikasını kabul edin.

   ![Sertifika hatası]

10. Yayımla işleminin ilerleme durumunu denetlemek için çıkış penceresini izleyin.

    ![Çıkış Penceresi]

11. Yayımlama başarılı olursa, yeni yayınlanan sitenin URL 'sini açmak için bir tarayıcı başlatılır.

**Başarılı!**

Artık Web uygulamanızı bir Azure sanal makinesine başarıyla yayımladınız.

## <a name="publish-page-options"></a>Yayımlama sayfası seçenekleri

Yayımlama Sihirbazı 'nı tamamladıktan sonra Yayımla sayfası, yeni yayımlama profili seçili olan belge ile birlikte açılır.

### <a name="re-publish"></a>Yeniden Yayımla

Web uygulamanızda güncelleştirmeleri yayımlamak için Yayımla sayfasındaki **Yayımla** düğmesini seçin.  
- İstenirse, Kullanıcı adı ve parola girin.  
- Yayımlama hemen başlar.

![Yayımlama sayfası-Yayımla düğmesi]

### <a name="modify-publish-profile-settings"></a>Yayımlama profili ayarlarını değiştir

Yayımlama profili ayarlarını görüntülemek ve değiştirmek için **ayarlar...** ' ı seçin.  

![Yayımlama sayfası-Ayarlar düğmesi]

Ayarlarınız şuna benzer görünmelidir:  

![Yayımlama ayarları-bağlantı sayfası]

#### <a name="save-user-name-and-password"></a>Kullanıcı adını ve Parolayı Kaydet
- Her yayımlamanızda kimlik doğrulama bilgilerini sağlamaktan kaçının. Bunu yapmak için, **Kullanıcı adı** ve **parola** alanlarını doldurun ve **Parolayı kaydet** kutusunu seçin.
- Doğru bilgileri girdiğinizi onaylamak için **bağlantıyı doğrula** düğmesini kullanın.

#### <a name="deploy-to-clean-web-server"></a>Web sunucusuna dağıtma

- Web sunucusunun her karşıya yüklemeden sonra Web uygulamasının temiz bir kopyasına sahip olduğundan ve önceki bir dağıtımdan başka hiçbir dosya ayrıldıktan emin olmak istiyorsanız, **Ayarlar** sekmesindeki **Hedefteki ek dosyaları Kaldır** onay kutusunu işaretleyebilirsiniz.

- Uyarı: Bu ayarla yayımlama, Web sunucusunda bulunan tüm dosyaları siler (Wwwroot dizini). Bu seçenekle yayımlamadan önce makinenin durumunu öğrendiğinizden emin olun. 

![Yayımlama ayarları-Ayarlar sayfası]

## <a name="next-steps"></a>Sonraki adımlar

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Azure VM 'ye otomatik dağıtım için CI/CD ayarlama

Azure Pipelines ile sürekli teslim işlem hattı ayarlamak için bkz. [Windows sanal makinesine dağıtma](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Yayımla sayfası-sağ ok]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Yayımlama sayfası-Microsoft Azure sanal makine simgesi]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure hesap Seçicisi]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM Seçicisi]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy oturumu açma]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Sertifika hatası]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Çıkış Penceresi]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Yayımlama sayfası-Yayımla düğmesi]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Yayımlama sayfası-Ayarlar düğmesi]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Yayımlama ayarları-bağlantı sayfası]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Yayımlama ayarları-Ayarlar sayfası]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
