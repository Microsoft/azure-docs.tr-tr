---
title: .NET uygulamaları için Azure Application Insights Snapshot Debugger yükseltmesi | Microsoft Docs
description: Snapshot Debugger Azure Uygulama Hizmetleri veya NuGet paketleri aracılığıyla en son sürüme yükseltme
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 51642dde3de16f2bed3ca247e573237effb30917
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73935978"
---
# <a name="upgrading-the-snapshot-debugger"></a>Snapshot Debugger yükseltiliyor

Verileriniz için mümkün olan en iyi güvenliği sağlamak üzere Microsoft, belirlenen saldırganlar tarafından açık olarak gösterilen TLS 1,0 ve TLS 1,1 ' den uzaklaşmakta. Site uzantısının daha eski bir sürümünü kullanıyorsanız, çalışmaya devam etmek için yükseltmenin yapılması gerekir. Bu belgede, anlık görüntü hata ayıklayıcıyı en son sürüme yükseltmek için gereken adımlar özetlenmektedir. Snapshot Debugger bir site uzantısı kullanarak veya uygulamanıza eklenmiş bir SDK/NuGet kullandıysanız, bu iki birincil yükseltme yolu vardır. Her iki yükseltme yolu da aşağıda ele alınmıştır. 

## <a name="upgrading-the-site-extension"></a>Site uzantısını yükseltme

> [!IMPORTANT]
> Application Insights eski sürümleri _Azure App Service için Application Insights uzantısı_adlı bir özel site uzantısı kullandı. Geçerli Application Insights deneyimi, uygulama ayarları önceden yüklenmiş bir site uzantısını açık olacak şekilde ayarlanarak etkinleştirilir.
> Sitenizin çalışmayı durdurmasına neden olabilecek çakışmaların önüne geçmek için, önce özel site uzantısının silinmesi önemlidir. Aşağıdaki 4. adıma bakın.

Site uzantısını kullanarak anlık görüntü hata ayıklayıcısını etkinleştirdiyseniz, aşağıdaki yordamı kullanarak yükseltebilirsiniz:

1. Azure Portal’da oturum açın.
2. Application Insights ve anlık görüntü hata ayıklayıcısı etkin olan kaynağınız için gidin. Örneğin, bir Web uygulaması için App Service kaynağına gidin:

   ![DiagService01 adlı tek bir App Service kaynağının ekran görüntüsü](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Kaynak ile gezindikten sonra, uzantılar dikey penceresine tıklayın ve eklenecek uzantılar listesini bekleyin:

   ![Azure App Service yüklü Application Insights uzantısını gösteren App Service uzantılarının ekran görüntüsü](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. _Azure App Service için Application Insights uzantısının_ herhangi bir sürümü yüklüyse, seçin ve Sil ' e tıklayın. Uzantıyı silmek için **Evet** ' i onaylayın ve sonraki adıma geçmeden önce silme işleminin tamamlanmasını bekleyin.

   ![Sil düğmesi vurgulanmış şekilde Azure App Service Application Insights uzantısını gösteren App Service uzantılarının ekran görüntüsü](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Kaynağınızın genel bakış dikey penceresine gidip Application Insights ' ye tıklayın:

   ![Üç düğme ekran görüntüsü. Application Insights adlı Merkez düğmesi seçili](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Bu App Service için Application Insights dikey pencere ilk kez görüntüleniyorsa Application Insights açmanız istenir. **Application Insights aç '** ı seçin.
 
   ![Aç Application Insights düğmesine vurgulanmış Application Insights dikey penceresindeki ilk seferlik deneyimin ekran görüntüsü](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Geçerli Application Insights ayarları görüntülenir. Ayarlarınızı değiştirme fırsatı almak istemediğiniz sürece, bunları olduğu gibi bırakabilirsiniz. Dikey pencerenin alt kısmındaki **Uygula** düğmesi varsayılan olarak etkin değildir ve düğmeyi etkinleştirmek için ayarlardan birini ayarlamanız gerekir. Gerçek ayarları değiştirmeniz gerekmez, bunun yerine ayarı değiştirebilir ve sonra hemen değiştirebilirsiniz. Profil Oluşturucu ayarını ve ardından **Uygula**' yı seçmeniz önerilir.

   ![Application Insights App Service yapılandırma sayfasının kırmızı renkle vurgulanmış şekilde ekran görüntüsü](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. **Uygula**' ya tıkladığınızda, değişiklikleri onaylamanız istenir.

    > [!NOTE]
    > Site, yükseltme işleminin bir parçası olarak yeniden başlatılacak.

   ![App Service izleme isteminin ekran görüntüsü. Metin kutusu şu iletiyi görüntüler: "Şimdi uygulama ayarlarınıza değişiklikler uygulayacağız ve Application Insights kaynağınızı Web uygulamasına bağlamak için araçlarımızı yükleyeceğiz. Bu, siteyi yeniden başlatacak. Devam etmek istiyor musunuz? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Değişiklikleri uygulamak için **Evet** ' i tıklatın ve işlemin tamamlanmasını bekleyin.

Site artık yükseltildi ve kullanıma hazır.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>SDK/NuGet kullanarak Snapshot Debugger yükseltme

Uygulama, 1.3.1 sürümünün `Microsoft.ApplicationInsights.SnapshotCollector` bir sürümünü kullanıyorsa, çalışmaya devam etmek için [daha yeni bir sürüme](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) yükseltilmesi gerekir.
