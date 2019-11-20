---
title: Azure API Management yedekleme ve geri yükleme kullanarak olağanüstü durum kurtarma uygulama | Microsoft Docs
description: Azure API Management 'da olağanüstü durum kurtarma işlemi gerçekleştirmek için yedekleme ve geri yüklemeyi nasıl kullanacağınızı öğrenin.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: 9c97723687484e8af82d63b6fb4999401a69fb2c
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958539"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Azure API Management hizmet yedekleme ve geri yükleme kullanarak olağanüstü durum kurtarma uygulama

Azure API Management aracılığıyla API 'lerinizi yayımlayarak ve yöneterek, el ile tasarlayarak, uygulamanız ve yönettiğinizde hata toleransı ve altyapı olanaklarından yararlanabilirsiniz. Azure platformu, olası hatalardan oluşan büyük bir kesri maliyetinin bir kesinde azaltır.

API Management hizmetinizi barındıran bölgeyi etkileyen kullanılabilirlik sorunlarından kurtarmak için, hizmetinize dilediğiniz zaman başka bir bölgede edilmeyen için hazır olun. Kurtarma zamanı amacınıza bağlı olarak, bir veya daha fazla bölgede bekleme hizmeti tutmak isteyebilirsiniz. Ayrıca, kurtarma noktası amacınıza göre yapılandırma ve içeriğini etkin hizmetle eşitlenmiş olarak tutmaya da deneyebilirsiniz. Hizmet yedekleme ve geri yükleme özellikleri, olağanüstü durum kurtarma stratejisi uygulamak için gereken yapı taşlarını sağlar.

Yedekleme ve geri yükleme işlemleri, işlemsel ortamlar arasında API Management hizmet yapılandırmasını çoğaltmak için de kullanılabilir (örneğin, geliştirme ve hazırlama). Kullanıcılar ve abonelikler gibi çalışma zamanı verilerinin de kopyalanıp kopyalanmayacağını unutmayın. Bu işlem her zaman istenmeyebilir.

Bu kılavuzda, yedekleme ve geri yükleme işlemlerinin nasıl otomatikleştirilmesi ve Azure Resource Manager göre yedekleme ve geri yükleme isteklerinin başarıyla doğrulanmasından nasıl emin olunması gösterilmektedir.

> [!IMPORTANT]
> Geri yükleme işlemi, hedef hizmetin özel konak adı yapılandırmasını değiştirmez. Hem etkin hem de bekleme Hizmetleri için aynı özel ana bilgisayar adı ve TLS sertifikası kullanmanız önerilir, böylece geri yükleme işlemi tamamlandıktan sonra trafik, basit bir DNS CNAME değişikliğine karşı bekleme örneğine yeniden yönlendirilebilir.
>
> Yedekleme işlemi, Azure portal analiz dikey penceresinde gösterilen raporlarda kullanılan önceden toplanmış günlük verilerini yakalamaz.

> [!WARNING]
> Her yedeklemenin süresi 30 gün sonra dolar. 30 günlük süre dolduktan sonra bir yedeklemeyi geri yüklemeye çalışırsanız, geri yükleme `Cannot restore: backup expired` iletisiyle başarısız olur.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Azure Resource Manager isteklerinin kimliğini doğrulama

> [!IMPORTANT]
> Yedekleme ve geri yükleme için REST API Azure Resource Manager kullanır ve API Management varlıklarınızı yönetmek için REST API 'lerden farklı bir kimlik doğrulama mekanizması vardır. Bu bölümdeki adımlarda Azure Resource Manager isteklerinin nasıl doğrulanabilmesi anlatılmaktadır. Daha fazla bilgi için bkz. [kimlik doğrulama Azure Resource Manager istekleri](/rest/api/index).

Azure Resource Manager kullanan kaynaklarda yaptığınız tüm görevlerin aşağıdaki adımları kullanarak Azure Active Directory kimlik doğrulamasından sahip olması gerekir:

-   Azure Active Directory kiracısına bir uygulama ekleyin.
-   Eklediğiniz uygulama için izinleri ayarlayın.
-   Azure Resource Manager istekler için kimlik doğrulama belirtecini alır.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory uygulaması oluşturma

1. [Azure Portal](https://portal.azure.com)oturum açın.
2. API Management hizmeti örneğinizi içeren aboneliği kullanarak **Azure Active Directory** (Azure Active Directory > yönet/Uygulama kayıtları) **uygulama kayıtları** sekmesine gidin.

    > [!NOTE]
    > Azure Active Directory varsayılan dizin hesabınıza görünmüyorsa, hesabınıza gerekli izinleri vermek için Azure aboneliğinin yöneticisine başvurun.

3. **Yeni uygulama kaydı**' na tıklayın.

    Sağ tarafta **Oluştur** penceresi görünür. Bu, AAD uygulamasının ilgili bilgilerini girdiğiniz yerdir.

4. Uygulama için bir ad girin.
5. Uygulama türü için **Yerel**' i seçin.
6. **Yeniden YÖNLENDIRME URI**'si için `http://resources` gibi bir yer tutucu URL 'si girin, çünkü bu gerekli bir alandır, ancak değer daha sonra kullanılmaz. Uygulamayı kaydetmek için onay kutusuna tıklayın.
7. **Oluştur**'u tıklatın.

### <a name="add-an-application"></a>Uygulama ekleme

1. Uygulama oluşturulduktan sonra **Ayarlar**' a tıklayın.
2. **Gerekli izinler**' e tıklayın.
3. **+ Ekle**' ye tıklayın.
4. **BIR API seçin**' e basın.
5. **Windows** **Azure hizmet yönetim API'si**seçin.
6. **Seç**' e basın.

    ![İzin Ekle](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Yeni eklenen uygulamanın yanındaki **temsilci izinleri** ' ne tıklayın, **Azure hizmet yönetimi 'ne (Önizleme) erişim**kutusunu işaretleyin.
8. **Seç**' e basın.
9. **Izin ver**' e tıklayın.

### <a name="configuring-your-app"></a>Uygulamanızı yapılandırma

Yedeklemeyi oluşturan ve geri yükleyen API 'Leri çağırmadan önce bir belirteç almanız gerekir. Aşağıdaki örnek, belirteci almak için [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet paketini kullanır.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Aşağıdaki yönergeleri kullanarak `{tenant id}`, `{application id}` ve `{redirect uri}` ' yi değiştirin:

1. @No__t-0 ' yı oluşturduğunuz Azure Active Directory uygulamasının kiracı KIMLIĞIYLE değiştirin. **Uygulama kayıtları** -> **uç noktalarına**tıklayarak kimliğe erişebilirsiniz.

    ![Uç Noktalar][api-management-endpoint]

2. @No__t-0 ' yı, **Ayarlar** sayfasına giderek aldığınız değerle değiştirin.
3. @No__t-0 ' ı Azure Active Directory uygulamanızın **yeniden yönlendirme URI** 'si sekmesinden değeri ile değiştirin.

    Değerler belirtildiğinde, kod örneği aşağıdaki örneğe benzer bir belirteç döndürmelidir:

    ![Simgesinde][api-management-arm-token]

    > [!NOTE]
    > Belirtecin belirli bir süre sonunda süresi dolacak. Yeni bir belirteç oluşturmak için kod örneğini yeniden yürütün.

## <a name="calling-the-backup-and-restore-operations"></a>Yedekleme ve geri yükleme işlemlerini çağırma

REST API 'Leri, [API Management hizmeti-yedekleme](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) ve [API Management hizmeti-geri yükleme](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)' dir.

Aşağıdaki bölümlerde açıklanan "yedekleme ve geri yükleme" işlemlerini çağırmadan önce REST çağrınızın yetkilendirme isteği üst bilgisini ayarlayın.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>API Management hizmeti yedekleme

Bir API Management hizmetini yedeklemek için aşağıdaki HTTP isteğini sorun:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

burada:

-   `subscriptionId`-yedeklemeye çalıştığınız API Management hizmeti tutan aboneliğin KIMLIĞI
-   `resourceGroupName`-Azure API Management hizmetinizin kaynak grubunun adı
-   `serviceName`-oluşturma sırasında belirtilen bir yedeği yaptığınız API Management hizmetin adı
-   `api-version`-`2018-06-01-preview` ile Değiştir

İsteğin gövdesinde, hedef Azure depolama hesabı adı, erişim anahtarı, blob kapsayıcısı adı ve yedekleme adı ' nı belirtin:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

@No__t-0 istek üst bilgisinin değerini `application/json` olarak ayarlayın.

Yedekleme, tamamlanması bir dakikadan uzun süreolabilecek uzun süredir çalışan bir işlemdir. İstek başarılı olursa ve yedekleme işlemi başladıysa, `Location` üst bilgisine sahip `202 Accepted` yanıt durum kodu alırsınız. İşlemin durumunu öğrenmek için `Location` üstbilgisindeki URL 'ye ' GET ' istekleri yapın. Yedekleme devam ederken, bir ' 202 kabul edildi ' durum kodu almaya devam edersiniz. @No__t-0 yanıt kodu, yedekleme işleminin başarıyla tamamlandığını gösterir.

Yedekleme isteği yaparken aşağıdaki kısıtlamalara göz önünde edin:

-   İstek gövdesinde belirtilen **kapsayıcı** **mevcut olmalıdır**.
-   Yedekleme devam ederken, hizmet yönetiminde, SKU yükseltmesi veya düşürme, etki alanı adında değişiklik ve daha fazlası gibi **değişikliklerden kaçının** .
-   Bir yedeklemenin geri yüklenmesi yalnızca, oluşturulduktan sonra **30 gün boyunca garanti edilir** .
-   Analiz raporları oluşturmak için kullanılan **kullanım verileri** yedeklemeye **dahil değildir** . [Azure API Management REST API][azure api management rest api] kullanarak safekeeping için analiz raporlarını düzenli aralıklarla alın.
-   Ayrıca, aşağıdaki öğeler yedekleme verilerinin bir parçası değildir: özel etki alanı SSL sertifikaları ve müşteri, geliştirici portalı içeriği ve sanal ağ tümleştirme ayarları tarafından karşıya yüklenen tüm ara veya kök sertifikalar.
-   Hizmet yedeklemeleri gerçekleştirdiğiniz sıklık, kurtarma noktası hedefini etkiler. Bunu en aza indirmek için, API Management hizmetinize değişiklikler yaptıktan sonra düzenli yedeklemeler uygulamanızı ve isteğe bağlı yedeklemeler gerçekleştirmenizi öneririz.
-   Yedekleme işlemi sırasında hizmet yapılandırmasında yapılan **değişiklikler** (örneğin, API 'ler, ilkeler ve geliştirici portalı görünümü), **yedeklemeden dışlanmayabilir ve kaybolacaktır**.
-   Denetim düzlemine Azure depolama hesabına erişim **Izni verin** . Müşteri, yedekleme için depolama hesabında aşağıdaki gelen IP kümesini açmalı. 
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32
### <a name="step2"> </a>API Management hizmeti geri yükleme

Daha önce oluşturulmuş bir yedekten bir API Management hizmetini geri yüklemek için aşağıdaki HTTP isteğini yapın:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

burada:

-   `subscriptionId`-bir yedeği geri yüklemekte olduğunuz API Management hizmetini tutan aboneliğin KIMLIĞI
-   `resourceGroupName`-bir yedeği geri yüklemekte olduğunuz Azure API Management hizmetini tutan kaynak grubunun adı
-   `serviceName`-oluşturma sırasında belirtilen API Management hizmetinin adı.
-   `api-version`-`2018-06-01-preview` ile Değiştir

İsteğin gövdesinde, yedekleme dosyası konumunu belirtin. Diğer bir deyişle, Azure depolama hesabı adı, erişim anahtarı, blob kapsayıcısı adı ve yedekleme adını ekleyin:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

@No__t-0 istek üst bilgisinin değerini `application/json` olarak ayarlayın.

Restore, tamamlanması 30 veya daha fazla dakika süren uzun süredir çalışan bir işlemdir. İstek başarılı olursa ve geri yükleme işlemi başladıysa, `Location` üst bilgisine sahip `202 Accepted` yanıt durum kodu alırsınız. İşlemin durumunu öğrenmek için `Location` üstbilgisindeki URL 'ye ' GET ' istekleri yapın. Geri yükleme devam ederken, ' 202 kabul edildi ' durum kodunu almaya devam edersiniz. @No__t-0 yanıt kodu, geri yükleme işleminin başarıyla tamamlandığını gösterir.

> [!IMPORTANT]
> Geri yüklenen hizmetin **SKU 'su** , geri yüklenmekte olan yedeklenen hizmetin SKU 'su **ile aynı olmalıdır** .
>
> Geri yükleme işlemi devam ederken hizmet yapılandırmasında yapılan **değişiklikler** (örneğin, API 'ler, ilkeler, geliştirici portalı görünümü) **üzerine yazılabilir**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Yedekleme ve geri yükleme işlemleri, sırasıyla PowerShell [_yedekleme-azapimanave_](/powershell/module/az.apimanagement/backup-azapimanagement) [_geri yükleme-azapimanayönetimi_](/powershell/module/az.apimanagement/restore-azapimanagement) komutları ile de gerçekleştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

Yedekleme/geri yükleme işleminin farklı talimatları için aşağıdaki kaynaklara göz atın.

-   [Azure API Management hesaplarını çoğaltma](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Logic Apps ile yedekleme ve geri yükleme API Management otomatikleştirme](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: yapılandırma @no__t yedekleme ve geri yükleme](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)-1_Stuart tarafından ayrıntılı yaklaşım resmi kılavuzumüne uymuyor, ancak ilginç._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
