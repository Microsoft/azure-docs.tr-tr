---
title: 'Hızlı başlangıç: Azure portal Python uygulaması oluşturma'
description: Azure portal kullanarak ilk Python uygulamanızı App Service bir Linux kapsayıcısına dağıtarak Azure App Service kullanmaya başlayın.
ms.topic: quickstart
ms.date: 04/01/2021
ms.custom: devx-track-python
robots: noindex
ms.openlocfilehash: 42f29152521da7bf90a550248e8429e51b728b24
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012365"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux-azure-portal"></a>Hızlı başlangıç: Linux üzerinde Azure App Service kullanarak Python uygulaması oluşturma (Azure portal)

Bu hızlı başlangıçta, Azure 'un yüksek düzeyde ölçeklenebilir ve kendini yayama Web barındırma hizmeti [olan Linux üzerinde App Service](overview.md#app-service-on-linux)Için bir Python web uygulaması dağıtırsınız. Flask veya Docgo çerçeveleri ile bir örnek dağıtmak için Azure portal kullanırsınız. Yapılandırdığınız Web uygulaması, Azure aboneliğinizde küçük bir ücret sunan temel bir App Service katmanını kullanır.

## <a name="configure-accounts"></a>Hesapları yapılandırma

- Etkin aboneliği olan bir Azure hesabınız yoksa, [ücretsiz olarak bir hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- GitHub hesabınız yoksa, oluşturmak için [GitHub.com](https://github.com) adresini ziyaret edin. 

## <a name="fork-the-sample-github-repository"></a>Örnek GitHub deposunu çatal

1. [GitHub.com](https://github.com) açın ve oturum açın.

1. Aşağıdaki örnek depolardan birine gidin:
    - [Flask Merhaba Dünya](https://github.com/Azure-Samples/python-docs-hello-world)
    - [Docgo Merhaba Dünya](https://github.com/Azure-Samples/python-docs-hello-django)

1. GitHub hesabınızda deponun bir kopyasını oluşturmak için GitHub sayfasının sağ üst köşesinde **çatalu** seçin:

    ![GitHub çatallama komutu](media/quickstart-python-portal/github-fork-command.png)

    Azure, depoyu içeren GitHub kuruluşuna erişiminizin olmasını gerektirir. Örneği kendi GitHub hesabınıza göre düzenleyerek, gerekli erişime otomatik olarak sahip olursunuz ve kodda de değişiklik yapabilirsiniz.

## <a name="provision-the-app-service-web-app"></a>App Service Web uygulaması sağlama

App Service Web uygulaması, kodunuzu dağıttığınız Web sunucusudur.

1. Azure portal açın [https://portal.azure.com](https://portal.azure.com) ve gerekirse oturum açın.

1. Azure portal en üstündeki arama çubuğunda "App Service" yazın ve ardından **uygulama hizmetleri**' ni seçin.

    ![Portal arama çubuğu ve App Service seçme](media/quickstart-python-portal/portal-search-bar.png)

1. **Uygulama hizmetleri** sayfasında "**+ Ekle**: ' yi seçin.

    ![App Service komutu Ekle](media/quickstart-python-portal/add-app-service.png)

1. **Web uygulaması oluştur** sayfasında, aşağıdaki işlemleri yapın:
    
    | Alan | Eylem |
    | --- | --- |
    | Abonelik | Kullanmak istediğiniz Azure aboneliğini seçin. |
    | Kaynak Grubu | Açılan listeden **Yeni oluştur** ' u seçin. Açılan pencerede "AppService-PythonQuickstart" yazın ve "**Tamam**" ı seçin. |
    | Name | Genellikle, *contoso-TestApp-123* gibi kişisel veya şirket adlarınızın bir birleşimini kullanarak tüm Azure genelinde benzersiz bir ad girin. |
    | Yayımlama | **Kod**’u seçin. |
    | Çalışma zamanı yığını | **Python 3,8**' i seçin. |
    | Operating System | **Linux** seçin (Python yalnızca Linux üzerinde desteklenir). |
    | Bölge | Size yakın olan bir bölgeyi seçin. |
    | Linux Planı | Bir çıkış App Service planı seçin veya yenisini oluşturmak için **Yeni oluştur** ' u kullanın. **Temel B1** planını kullanmanızı öneririz. |

    ![Azure portal web uygulaması formu oluşturma](media/quickstart-python-portal/create-web-app.png)

1. Sayfanın alt kısmındaki **gözden geçir + oluştur**' u seçin, ayrıntıları gözden geçirin ve ardından **Oluştur**' u seçin.

1. Sağlama tamamlandığında, yeni App Service sayfasına gitmek için **Kaynağa Git** ' i seçin. Bu noktadaki Web uygulamanız yalnızca bir varsayılan sayfa içerir, bu nedenle sonraki adımda örnek kod dağıtılır.

Sorun mu yaşıyorsunuz? [Bize bilgi verin](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="deploy-the-sample-code"></a>Örnek kodu dağıtma

1. Azure portal web uygulaması sayfasında, **Dağıtım Merkezi**' ni seçin:
    
    ![App Service menüsünde dağıtım merkezi komutu](media/quickstart-python-portal/deployment-center-command.png)


1. **Dağıtım Merkezi** sayfasında, zaten açık değilse **Ayarlar** sekmesini seçin:

    ![Dağıtım Merkezi ayarları sekmesi](media/quickstart-python-portal/deployment-center-settings-tab.png)

1. **Kaynak** altında **GitHub**' ı seçin, ardından görüntülenen **GitHub** formunda aşağıdaki eylemleri yapın:

    | Alan | Eylem |
    | --- | --- |
    | Oturum açılan hesap | Zaten GitHub 'da oturum açmadıysanız, şimdi oturum açın veya gerekirse *hesabı Değiştir* ' i seçin. |
    | Kuruluş | Gerekirse GitHub kuruluşunuzu seçin. |
    | Depo | Daha önce herhangi bir **Python-docs-Hello-World** (Flask) veya **Python-docs-Hello-docgo** (docgo) adlı örnek deponun adını seçin. |
    | Dal | **Ana** öğesini seçin. |

    ![Dağıtım Merkezi GitHub Kaynak Yapılandırması](media/quickstart-python-portal/deployment-center-configure-github-source.png)

1. Ayarları uygulamak için sayfanın en üstünde **Kaydet** ' i seçin.:

    ![GitHub Kaynak yapılandırmasını dağıtım merkezi 'ne kaydetme](media/quickstart-python-portal/deployment-center-configure-save.png)

1. Dağıtımın durumunu görüntülemek için **Günlükler** sekmesini seçin. Örnek oluşturulması ve dağıtılması birkaç dakika sürer ve işlem sırasında ek Günlükler görüntülenir. Tamamlandıktan sonra Günlükler **başarı durumunu (etkin)** yansıtmalıdır:

    ![Dağıtım Merkezi günlükleri sekmesi](media/quickstart-python-portal/deployment-center-logs.png)

Sorun mu yaşıyorsunuz? [Bize bilgi verin](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="browse-to-the-app"></a>Uygulamaya göz atma

1. Dağıtım tamamlandıktan sonra, Web uygulamasının ana sayfasına dönmek için sol taraftaki menüdeki **genel bakış** ' ı seçin.

1. Web uygulamasının adresini içeren **URL 'yi** seçin:

    ![Genel Bakış sayfasındaki Web uygulaması URL 'SI](media/quickstart-python-portal/web-app-url.png)

1. Uygulamanın çıktısının "Hello, World!" olduğunu doğrulayın:

    ![İlk dağıtımdan sonra çalışan uygulama](media/quickstart-python-portal/web-app-first-deploy-results.png)

Sorun mu yaşıyorsunuz? İlk olarak [sorun giderme kılavuzuna](configure-language-python.md#troubleshooting)başvurun, aksi takdirde [bize bilgi verin](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="make-a-change-and-redeploy"></a>Değişiklik yapma ve yeniden dağıtma

App Service deponuza bağlandığınızdan, kaynak deponuza yaptığınız değişiklikler otomatik olarak Web uygulamasına dağıtılır.

1. GitHub 'da doğrudan dallanmış deponuz üzerinde değişiklik yapabilir veya depoyu yerel olarak kopyalayabilir, değişiklikleri yapıp kaydedebilir ve sonra bu değişiklikleri GitHub 'a gönderebilirsiniz. Her iki yöntem de App Service bağlanan depoda değişikliğe neden olur.

1. XML **deponuzda**, uygulamanın Iletisini "Hello, World!" olarak değiştirin "Merhaba, Azure!" şöyle:
    - Flask (Python-docs-Merhaba-Dünya örneği): *Application.py* dosyasının 6. satırında metin dizesini değiştirin.
    - Docgo (Python-docs-Hello-docgo örneği): *Hello* klasörü içindeki *views.py* dosyasının 5. satırında bulunan metin dizesini değiştirin.

1. Değişikliği depoya kaydedin.

    Yerel bir kopya kullanıyorsanız, bu değişiklikleri GitHub 'a de gönderin.

1. Web uygulaması Azure portal, **Dağıtım Merkezi**' ne dönün, **Günlükler** sekmesini seçin ve devam etmesi gereken yeni dağıtım etkinliğini unutmayın.

1. Dağıtım tamamlandığında, Web uygulamasının **genel bakış** sayfasına dönün, Web uygulamasının URL 'sini yeniden açın ve uygulamadaki değişiklikleri gözlemleyin:

    ![Yeniden dağıtım sonrasında çalışan uygulama](media/quickstart-python-portal/web-app-second-deploy-results.png)

Sorun mu yaşıyorsunuz? İlk olarak [sorun giderme kılavuzuna](configure-language-python.md#troubleshooting)başvurun, aksi takdirde [bize bilgi verin](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıdaki adımlarda, Web uygulamasının *genel bakış** sayfasında gösterilen "appservice-PythonQuickstart" adlı bir kaynak grubunda Azure kaynakları oluşturdunuz. Web uygulamasını çalışır durumda tutarsanız, bazı devam eden maliyetlere (bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/linux/)) tabi olursunuz.

Gelecekte bu kaynaklara ihtiyaç duymazsanız, kaynak gruplarına genel bakış ' a gitmek için Web uygulamasına **genel bakış** sayfasında kaynak grubunun adını seçin. Bu seçim **kaynak grubunu sil** ' i seçin ve istemleri izleyin.

![Kaynak grubunu silme](media/quickstart-python-portal/delete-resource-group.png)


Sorun mu yaşıyorsunuz? [Bize bilgi verin](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: PostgreSQL ile Python (Docgo) Web uygulaması](/azure/developer/python/tutorial-python-postgresql-app-portal)

> [!div class="nextstepaction"]
> [Python uygulamasını yapılandırma](configure-language-python.md)

> [!div class="nextstepaction"]
> [Python web uygulamasına kullanıcı oturumu ekleme](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Öğretici: özel kapsayıcıda Python uygulaması çalıştırma](tutorial-custom-container.md)
