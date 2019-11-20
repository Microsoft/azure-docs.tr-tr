---
title: Yükleme Azure İşlevleri Çalışma Zamanı | Microsoft Docs
description: Azure İşlevleri Çalışma Zamanı Preview 2 ' ye nasıl yüklenir
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 0fbc76f3cb3fea84347ef55e40e1b2bed9b98152
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085506"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Azure İşlevleri Çalışma Zamanı Preview 2 ' i yükler

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Azure İşlevleri Çalışma Zamanı Preview 2 ' yi yüklemek istiyorsanız aşağıdaki adımları izleyin:

1. Makinenizin en düşük gereksinimleri geçirdiğinden emin olun.
1. [Azure işlevleri çalışma zamanı Preview yükleyicisini](https://aka.ms/azafrv2)indirin.
1. Azure İşlevleri Çalışma Zamanı Preview 1 ' i kaldırın.
1. Azure İşlevleri Çalışma Zamanı Preview 2 ' i yükler.
1. Azure İşlevleri Çalışma Zamanı Preview 2 yapılandırmasını doldurun.
1. Azure İşlevleri Çalışma Zamanı önizlemede ilk işlevinizi oluşturma

## <a name="prerequisites"></a>Önkoşullar

Azure İşlevleri Çalışma Zamanı Önizlemeyi yüklemeden önce aşağıdaki kaynaklara sahip olmanız gerekir:

1. Microsoft Windows Server 2016 veya Microsoft Windows 10 Creators Update (Professional veya Enterprise Edition) çalıştıran bir makine.
1. Ağınız içinde çalışan bir SQL Server örneği.  Gerekli en düşük sürüm SQL Server Express.

## <a name="uninstall-previous-version"></a>Önceki sürümü kaldır

Azure İşlevleri Çalışma Zamanı önizlemesini daha önce yüklediyseniz, en son sürümü yüklemeden önce ' yi kaldırmanız gerekir.  Windows 'daki Program Ekle/Kaldır bölümünde programı kaldırarak Azure İşlevleri Çalışma Zamanı önizlemeyi kaldırın.

## <a name="install-the-azure-functions-runtime-preview"></a>Azure İşlevleri Çalışma Zamanı önizlemeyi yükler

Azure İşlevleri Çalışma Zamanı Preview yükleyicisi, Azure İşlevleri Çalışma Zamanı önizleme yönetimi ve çalışan rollerinin yüklenmesinde size kılavuzluk eder.  Yönetim ve çalışan rolünü aynı makineye yüklemek mümkündür.  Ancak, daha fazla işlev uygulaması eklediğinizde, işlevlerinizi birden çok çalışana ölçeklendirebilmek için ek makinelere daha fazla çalışan rolü dağıtmanız gerekir.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Yönetim ve çalışan rolünü aynı makineye yükler

1. Azure İşlevleri Çalışma Zamanı Preview yükleyicisini çalıştırın.

    ![Azure İşlevleri Çalışma Zamanı Preview yükleyicisi][1]

1. **İleri**'ye tıklayın.
1. **EULA**'nın koşullarını okuduktan sonra, koşulları kabul etmek için **kutuyu işaretleyin** ve **İleri** ' ye tıklayın.
1. Bu makine **Işlevleri yönetim rolü** ve/veya **işlevleri çalışan rolü** üzerine yüklemek Istediğiniz rolleri seçin ve **İleri**' ye tıklayın.

    ![Azure İşlevleri Çalışma Zamanı Preview yükleyicisi-rol seçimi][3]

    > [!NOTE]
    > **Işlevlerin çalışan rolünü** diğer birçok makineye yükleyebilirsiniz. Bunu yapmak için, bu yönergeleri izleyin ve yalnızca yükleyicideki **Işlevler çalışan rolü** ' nü seçin.

1. **Azure işlevleri çalışma zamanı Kurulum sihirbazının** makinenizde yükleme işlemini başlatmak için **İleri** ' ye tıklayın.
1. Tamamlandıktan sonra, Kurulum Sihirbazı **Azure işlevleri çalışma zamanı** yapılandırma aracını başlatır.

    ![Azure İşlevleri Çalışma Zamanı Preview yükleyicisi Tamam][6]

    > [!NOTE]
    > **Windows 10** ' a yüklüyorsanız ve **kapsayıcı** özelliği daha önce etkinleştirilmemişse, **Azure işlevleri çalışma zamanı kurulum** , yüklemeyi tamamlaması için makinenizi yeniden başlatmanızı ister.

## <a name="configure-the-azure-functions-runtime"></a>Azure İşlevleri Çalışma Zamanı yapılandırma

Azure İşlevleri Çalışma Zamanı yüklemesini gerçekleştirmek için, yapılandırmayı tamamlamalısınız.

1. **Azure işlevleri çalışma zamanı** yapılandırma aracı, makinenizde hangi rollerin yüklü olduğunu gösterir.

    ![Azure İşlevleri Çalışma Zamanı Preview yapılandırma aracı][7]

1. **Veritabanı** sekmesine tıklayın, bir [veritabanı ana anahtarı](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)belirtme dahil SQL Server Örneğiniz için bağlantı ayrıntılarını girin ve **Uygula**' ya tıklayın.  Azure İşlevleri Çalışma Zamanı çalışma zamanını destekleyecek bir veritabanı oluşturmak için bir SQL Server örneğine bağlantı gerekir.

    ![Azure İşlevleri Çalışma Zamanı Preview veritabanı yapılandırması][8]

1. **Kimlik bilgileri** sekmesine tıklayın.  Burada, tüm işlev uygulamalarınızı barındırmak için bir dosya paylaşımıyla birlikte kullanmak üzere iki yeni kimlik bilgisi oluşturmalısınız.  **Dosya paylaşımının sahibi** ve **dosya paylaşma kullanıcısı**Için **Kullanıcı adı** ve **parola** birleşimlerini belirtip **Uygula**' ya tıklayın.

    ![Azure İşlevleri Çalışma Zamanı Preview kimlik bilgileri][9]

1. **Dosya paylaşma** sekmesine tıklayın.  Burada dosya paylaşma konumunun ayrıntılarını belirtmeniz gerekir.  Dosya paylaşma sizin için oluşturulabilir veya var olan bir dosya paylaşımından kullanılabilir ve **Uygula**' ya tıklayabilirsiniz.  Yeni bir dosya paylaşma konumu seçerseniz, Azure İşlevleri Çalışma Zamanı tarafından kullanılacak bir dizin belirtmeniz gerekir.

    ![Azure İşlevleri Çalışma Zamanı Preview dosya paylaşma][10]

1. **IIS** sekmesine tıklayın.  Bu sekmede, Azure İşlevleri Çalışma Zamanı yapılandırma aracının oluşturduğu IIS 'deki Web sitelerinin ayrıntıları gösterilmektedir.  Azure İşlevleri Çalışma Zamanı Preview Portal için burada özel bir DNS adı belirtebilirsiniz.  Tamam ' a tıklayarak Tamam 'a tıklayın.

    ![Azure İşlevleri Çalışma Zamanı Preview IIS][11]

1. **Hizmetler** sekmesine tıklayın.  Bu sekmede Azure İşlevleri Çalışma Zamanı yapılandırma aracdaki hizmetlerin durumu gösterilmektedir.  **Azure Işlevleri ana bilgisayar etkinleştirme hizmeti** ilk yapılandırmadan sonra çalışmıyorsa, **Hizmeti Başlat**' a tıklayın.

    ![Azure İşlevleri Çalışma Zamanı Preview Yapılandırması Tamam][12]

1. **Azure işlevleri çalışma zamanı portalına** `https://<machinename>.<domain>/`gidin.

    ![Azure İşlevleri Çalışma Zamanı Preview Portal][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Azure İşlevleri Çalışma Zamanı önizlemede ilk işlevinizi oluşturma

Azure İşlevleri Çalışma Zamanı önizlemede ilk işlevinizi oluşturmak için

1. Örneğin `https://<machinename>.<domain>` Azureişlevleriçalışmazamanıportalına`https://mycomputer.mydomain.com`gidin.

1. Bir etki alanında dağıtılmışsa etki alanı hesabınızın Kullanıcı adı ve parolasını kullanıyorsanız **oturum açmanız**istenir, aksi takdirde portalda oturum açmak için yerel hesap Kullanıcı adınızı ve parolanızı kullanın.

    ![Azure İşlevleri Çalışma Zamanı Preview Portal oturum açma][14]

1. İşlev uygulamaları oluşturmak için bir abonelik oluşturmanız gerekir.  Portalın sol üst köşesinde, abonelikler ' in yanındaki **+** seçeneğe tıklayın.

    ![Azure İşlevleri Çalışma Zamanı Preview Portal abonelikleri][15]

1. **Defaultplan**' ı seçin, aboneliğiniz için bir ad girin ve **Oluştur**' a tıklayın.

    ![Azure İşlevleri Çalışma Zamanı Preview Portal abonelik planı ve adı][16]

1. Tüm işlev uygulamalarınız portalın sol bölmesinde listelenir.  Yeni bir işlev uygulaması oluşturmak için, **uygulama başlık işlevini** seçin ve **+** seçeneğe tıklayın.

1. İşlev uygulamanız için bir ad girin, doğru aboneliği seçin, hangi Azure Işlevleri çalışma zamanının Programlaştırmak istediğinizi seçin ve **Oluştur** ' a tıklayın.

    ![Azure İşlevleri Çalışma Zamanı Preview Portal yeni işlev uygulaması][17]

1. Yeni işlev uygulamanız portalın sol bölmesinde listelenir.  Işlevler ' i seçin ve ardından portalda orta bölmenin en üstündeki **yeni işlev** ' ı tıklatın.

    ![Azure İşlevleri Çalışma Zamanı önizleme şablonları][18]

1. Zamanlayıcı tetikleyicisi işlevini seçin, sağ taraftaki açılır menüdeki işlevinizi adlandırın ve zamanlamayı olarak `*/5 * * * * *` değiştirin (bu cron ifadesi Zamanlayıcı işlevinizin beş saniyede bir yürütmesine neden olur) ve **Oluştur** ' a tıklayın.

    ![Yeni zamanlayıcı işlev yapılandırması Azure İşlevleri Çalışma Zamanı Önizleme][19]

1. İşleviniz şimdi oluşturuldu.  Portalın alt kısmındaki **günlük** bölmesini genişleterek işlev uygulamanızın yürütme günlüğünü görüntüleyebilirsiniz.

    ![Azure İşlevleri Çalışma Zamanı Preview işlevi yürütülüyor][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
