---
title: System Center Operations Manager ile tümleştirme VM'ler için Azure İzleyici | Microsoft Docs
description: VM'ler için Azure İzleyici Windows ve Linux sistemlerindeki uygulama bileşenlerini otomatik olarak bulur ve hizmetler arasındaki iletişimi eşler. Bu makalede, Operations Manager ' de otomatik olarak dağıtılmış uygulama diyagramları oluşturmak için harita özelliğinin kullanılması anlatılmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: 3523756e89506e90407090db105fdced5853d9d9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553966"
---
# <a name="system-center-operations-manager-integration-with-azure-monitor-for-vms-map-feature"></a>VM'ler için Azure İzleyici Map özelliği ile System Center Operations Manager tümleştirme

VM'ler için Azure İzleyici, Azure 'da veya ortamınızda çalışan Windows ve Linux sanal makinelerinde (VM 'Ler) bulunan uygulama bileşenlerini görüntüleyebilirsiniz. Eşleme özelliği ve System Center Operations Manager arasındaki bu tümleştirmeyle, VM'ler için Azure İzleyici içindeki dinamik bağımlılık haritalarını temel alan Operations Manager otomatik olarak dağıtılmış uygulama diyagramları oluşturabilirsiniz. 

>[!NOTE]
>Zaten Hizmet Eşlemesi dağıttıysanız, sanal makine sistem durumunu ve performansını izlemek için ek özellikler içeren haritalarınızı VM'ler için Azure İzleyici görüntüleyebilirsiniz. VM'ler için Azure İzleyici Map özelliği, tek başına Hizmet Eşlemesi çözümünün yerini alacak şekilde tasarlanmıştır. Daha fazla bilgi için bkz. [VM'ler için Azure izleyici genel bakış](vminsights-overview.md).

## <a name="prerequisites"></a>Önkoşullar

* System Center Operations Manager yönetim grubu (2012 R2 veya üzeri).
* VM'ler için Azure İzleyici desteklemek için yapılandırılmış bir Log Analytics çalışma alanı.
* Operations Manager tarafından izlenen bir veya daha fazla Windows ve Linux sanal makinesi veya fiziksel bilgisayar Log Analytics çalışma alanınıza veri gönderiyor. Bir Operations Manager yönetim grubuna raporlayan Linux sunucularının Azure Izleyici 'ye doğrudan bağlanacak şekilde yapılandırılması gerekir. Daha fazla bilgi için [Log Analytics aracısıyla günlük verilerini toplama](../platform/log-analytics-agent.md)bölümündeki genel bakışı gözden geçirin.
* Log Analytics çalışma alanıyla ilişkili Azure aboneliğine erişimi olan bir hizmet sorumlusu. Daha fazla bilgi için [hizmet sorumlusu oluşturma](#create-a-service-principal)bölümüne gidin.

## <a name="install-the-service-map-management-pack"></a>Hizmet Eşlemesi yönetim paketini yükler

Microsoft. SystemCenter. ServiceMap yönetim paketi paketini (Microsoft. SystemCenter. ServiceMap. mpb) içeri aktararak Operations Manager ile eşleme özelliği arasındaki tümleştirmeyi etkinleştirirsiniz. Yönetim Paketi grubunu [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=55763)' nden indirebilirsiniz. Paket aşağıdaki yönetim paketlerini içerir:

* Microsoft Hizmet Eşlemesi uygulama görünümleri
* Microsoft System Center Hizmet Eşlemesi Iç
* Microsoft System Center Hizmet Eşlemesi geçersiz kılmaları
* Microsoft System Center Hizmet Eşlemesi

## <a name="configure-integration"></a>Tümleştirmeyi yapılandırma

Hizmet Eşlemesi yönetim paketini yükledikten sonra, **hizmet eşlemesi**yeni bir düğüm, Operations Manager işletim konsolunuzun **Yönetim** bölmesinde **Operations Management Suite** altında görüntülenir.

>[!NOTE]
>[Operations Management Suite, Log Analytics dahil olan hizmetlerden oluşan bir koleksiyondur](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) . artık [Azure izleyici](../overview.md)'nin bir parçasıdır.

VM'ler için Azure İzleyici eşleme tümleştirmesini yapılandırmak için aşağıdakileri yapın:

1. Yapılandırma Sihirbazı 'nı açmak için **hizmet eşlemesi genel bakış** bölmesinde **çalışma alanı Ekle**' ye tıklayın.  

    ![Hizmet Eşlemesi Genel bakış bölmesi](media/service-map-scom/scom-configuration.png)

2. **Bağlantı yapılandırması** penceresinde kiracı adını veya kimliğini, uygulama kimliğini (Kullanıcı adı veya ClientID olarak da bilinir) ve hizmet sorumlusu parolasını girin ve ardından **İleri**' ye tıklayın. Daha fazla bilgi için hizmet sorumlusu oluşturma bölümüne gidin.

    ![Bağlantı yapılandırma penceresi](media/service-map-scom/scom-config-spn.png)

3. **Abonelik seçimi** penceresinde Azure aboneliği, Azure Kaynak grubu (Log Analytics çalışma alanını içeren) ve Log Analytics çalışma alanı ' nı seçin ve ardından **İleri**' ye tıklayın.

    ![Operations Manager yapılandırma çalışma alanı](media/service-map-scom/scom-config-workspace.png)

4. **Makine grubu seçim** penceresinde, Operations Manager eşitlemek Istediğiniz hizmet eşlemesi makine gruplarını seçersiniz. **Makine gruplarını Ekle/Kaldır**' a tıklayın, **kullanılabilir makine grupları**listesinden gruplar ' ı seçin ve **Ekle**' ye tıklayın.  Grupları seçmeyi tamamladığınızda, **Tamam** ' a tıklayarak işlemi sona erdirin.

    ![Operations Manager yapılandırma makinesi grupları](media/service-map-scom/scom-config-machine-groups.png)

5. **Sunucu seçimi** penceresinde, hizmet eşlemesi servers grubunu Operations Manager ile eşleme özelliği arasında eşitlemek istediğiniz sunucularla yapılandırırsınız. **Sunucu Ekle/Kaldır**' a tıklayın.

    Bir sunucu için dağıtılmış uygulama diyagramı oluşturmak amacıyla tümleştirme için sunucu şu şekilde olmalıdır:

   * Operations Manager tarafından izlenen
   * VM'ler için Azure İzleyici ile yapılandırılan Log Analytics çalışma alanına raporlamak için yapılandırılır
   * Hizmet Eşlemesi Servers grubunda listelenmiştir

     ![Operations Manager yapılandırma grubu](media/service-map-scom/scom-config-group.png)

6. İsteğe bağlı: Log Analytics iletişim kurmak için tüm yönetim sunucuları kaynak havuzunu seçin ve ardından **çalışma alanı Ekle**' ye tıklayın.

    ![Operations Manager yapılandırma kaynak havuzu](media/service-map-scom/scom-config-pool.png)

    Log Analytics çalışma alanını yapılandırmak ve kaydettirmek bir dakika sürebilir. Yapılandırıldıktan sonra, Operations Manager ilk eşleme eşitlemesini başlatır.

    ![Operations Manager yapılandırma kaynak havuzu](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>İzleme tümleştirmesi

Log Analytics çalışma alanı bağlandıktan sonra, Operations Manager Işletim konsolunun **izleme** bölmesinde yeni bir klasör hizmet eşlemesi görüntülenir.

![Operations Manager Izleme bölmesi](media/service-map-scom/scom-monitoring.png)

Hizmet Eşlemesi klasörü dört düğüme sahiptir:

* **Etkin uyarılar**: Operations Manager Ile Azure izleyici arasındaki iletişimle ilgili tüm etkin uyarıları listeler.  

  >[!NOTE]
  >Bu uyarılar Operations Manager ile eşitlenen Log Analytics uyarıları değil, Hizmet Eşlemesi yönetim paketinde tanımlanan iş akışlarına göre yönetim grubunda oluşturulur.

* **Sunucular**: VM'ler için Azure izleyici eşleme özelliğinden eşitlenecek şekilde yapılandırılan izlenen sunucuları listeler.

    ![Operations Manager Izleme sunucuları bölmesi](media/service-map-scom/scom-monitoring-servers.png)

* **Makine grubu bağımlılığı görünümleri**: harita özelliğinden eşitlenen tüm makine gruplarını listeler. Dağıtılmış uygulama diyagramını görüntülemek için herhangi bir gruba tıklayabilirsiniz.

    ![Operations Manager dağıtılmış uygulama diyagramı](media/service-map-scom/scom-group-dad.png)

* **Sunucu bağımlılığı görünümleri**: harita özelliğinden eşitlenen tüm sunucuları listeler. Dağıtılmış uygulama diyagramını görüntülemek için herhangi bir sunucu ' ya tıklayabilirsiniz.

    ![Operations Manager dağıtılmış uygulama diyagramı](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Çalışma alanını düzenleme veya silme

Yapılandırılan çalışma alanını **hizmet eşlemesi genel bakış** bölmesi (**Yönetim** bölmesi > **Operations Management Suite**  > **hizmet eşlemesi**) aracılığıyla düzenleyebilir veya silebilirsiniz.

>[!NOTE]
>Operations Management Suite, artık [Azure izleyici](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md)'nin bir parçası olan Log Analytics dahil olan [hizmetlerden oluşan bir koleksiyondur](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) .

Bu geçerli sürümde yalnızca bir Log Analytics çalışma alanı yapılandırabilirsiniz.

![Operations Manager düzenleme çalışma alanı bölmesi](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Kuralları ve geçersiz kılmaları yapılandırma

Bir kural, *Microsoft. SystemCenter. Servicemapımport. Rule*, düzenli aralıklarla VM'ler için Azure izleyici harita özelliğinden bilgi getirir. Eşitleme aralığını değiştirmek için kuralı geçersiz kılabilir ve değeri **ınterinterval**parametresi için değiştirebilirsiniz.

![Operations Manager Özellikler penceresi geçersiz kılınır](media/service-map-scom/scom-overrides.png)

* **Etkin**: otomatik güncelleştirmeleri etkinleştirin veya devre dışı bırakın.
* **Interınminutes**: güncelleştirmeler arasındaki süreyi belirtir. Varsayılan Aralık bir saattir. Haritaları daha sık eşitlemek istiyorsanız, değeri değiştirebilirsiniz.
* **TimeoutSeconds**: istek zaman aşımına uğramadan önceki sürenin uzunluğunu belirtir.
* **Timewindowminutes**: verileri sorgulamak için zaman penceresini belirtir. Varsayılan değer, izin verilen en fazla Aralık olan 60 dakikadır.

## <a name="known-issues-and-limitations"></a>Bilinen sorunlar ve sınırlamalar

Geçerli tasarımda aşağıdaki sorunlar ve sınırlamalar sunulmaktadır:

* Yalnızca tek bir Log Analytics çalışma alanına bağlanabilirsiniz.
* **Yazma** bölmesi aracılığıyla hizmet eşlemesi Servers grubuna el ile sunucu ekleyebilseniz de, bu sunucuların haritaları hemen eşitlenmez. Bir sonraki eşitleme çevrimi sırasında VM'ler için Azure İzleyici Map özelliğinden eşitlenir.
* Yönetim paketi tarafından oluşturulan dağıtılmış uygulama diyagramlarında herhangi bir değişiklik yaparsanız, bu değişikliklerin büyük olasılıkla VM'ler için Azure İzleyici bir sonraki eşitlemede üzerine yazılır.

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

Hizmet sorumlusu oluşturma hakkında resmi Azure belgeleri için bkz.:

* [PowerShell kullanarak hizmet sorumlusu oluşturma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLı kullanarak hizmet sorumlusu oluşturma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Azure portal kullanarak bir hizmet sorumlusu oluşturma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Geri Bildirim
VM'ler için Azure İzleyici Map özelliği veya bu belgeler ile tümleştirme hakkında bizimle ilgili geri bildiriminiz var mı? Özellik önerdiğiniz veya mevcut önerilere oy oluşturabileceğiniz [Kullanıcı ses](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)sayfamızı ziyaret edin.
