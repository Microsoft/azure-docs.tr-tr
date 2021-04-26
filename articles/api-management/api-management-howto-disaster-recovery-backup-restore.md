---
title: API Management yedekleme ve geri yükleme kullanarak olağanüstü durum kurtarma uygulama
titleSuffix: Azure API Management
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
ms.date: 12/05/2020
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7901dd4a238b27a31f95f1e22ddf9dc1ae5327a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813078"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Azure API Management'ta hizmet yedekleme ve geri yükleme işlevlerini kullanarak acil durumda kurtarma

Azure API Management aracılığıyla API 'lerinizi yayımlayarak ve yöneterek, el ile tasarlayarak, uygulamanız ve yönettiğinizde hata toleransı ve altyapı olanaklarından yararlanabilirsiniz. Azure platformu, olası hatalardan oluşan büyük bir kesri maliyetinin bir kesinde azaltır.

API Management hizmetinizi barındıran bölgeyi etkileyen kullanılabilirlik sorunlarından kurtarmak için, hizmetinize dilediğiniz zaman başka bir bölgede edilmeyen için hazır olun. Kurtarma zamanı amacınıza bağlı olarak, bir veya daha fazla bölgede bekleme hizmeti tutmak isteyebilirsiniz. Ayrıca, kurtarma noktası amacınıza göre yapılandırma ve içeriğini etkin hizmetle eşitlenmiş olarak tutmaya da deneyebilirsiniz. Hizmet yedekleme ve geri yükleme özellikleri, olağanüstü durum kurtarma stratejisi uygulamak için gereken yapı taşlarını sağlar.

Yedekleme ve geri yükleme işlemleri, işlemsel ortamlar arasında API Management hizmet yapılandırmasını çoğaltmak için de kullanılabilir (örneğin, geliştirme ve hazırlama). Kullanıcılar ve abonelikler gibi çalışma zamanı verilerinin de kopyalanıp kopyalanmayacağını unutmayın. Bu işlem her zaman istenmeyebilir.

Bu kılavuzda, yedekleme ve geri yükleme işlemlerinin nasıl otomatikleştirilmesi ve Azure Resource Manager göre yedekleme ve geri yükleme isteklerinin başarıyla doğrulanmasından nasıl emin olunması gösterilmektedir.

> [!IMPORTANT]
> Geri yükleme işlemi, hedef hizmetin özel konak adı yapılandırmasını değiştirmez. Hem etkin hem de bekleme Hizmetleri için aynı özel ana bilgisayar adı ve TLS sertifikası kullanmanız önerilir, böylece geri yükleme işlemi tamamlandıktan sonra trafik, basit bir DNS CNAME değişikliğine karşı bekleme örneğine yeniden yönlendirilebilir.
>
> Yedekleme işlemi, Azure portal analiz dikey penceresinde gösterilen raporlarda kullanılan önceden toplanmış günlük verilerini yakalamaz.

> [!WARNING]
> Her yedeklemenin süresi 30 gün sonra dolar. 30 günlük süre dolduktan sonra bir yedeklemeyi geri yüklemeye çalışırsanız, geri yükleme bir iletiyle başarısız olur `Cannot restore: backup expired` .

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

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. API Management hizmeti örneğinizi içeren aboneliği kullanarak **Azure Active Directory** (Azure Active Directory > yönet/Uygulama kayıtları) **uygulama kayıtları** sekmesine gidin.

    > [!NOTE]
    > Azure Active Directory varsayılan dizin hesabınıza görünmüyorsa, hesabınıza gerekli izinleri vermek için Azure aboneliğinin yöneticisine başvurun.

3. **Yeni uygulama kaydı**’na tıklayın.

    Sağ tarafta **Oluştur** penceresi görünür. Bu, AAD uygulamasının ilgili bilgilerini girdiğiniz yerdir.

4. Uygulama için bir ad girin.
5. Uygulama türü için **Yerel**' i seçin.
6. Yeniden yönlendirme URI 'si gibi bir yer tutucu URL 'si girin `http://resources` , çünkü bu gerekli bir alandır, ancak değer daha sonra kullanılmaz.  Uygulamayı kaydetmek için onay kutusuna tıklayın.
7. **Oluştur**’a tıklayın.

### <a name="add-an-application"></a>Uygulama ekleme

1. Uygulama oluşturulduktan sonra **API izinleri**' ne tıklayın.
2. **+ Izin Ekle**' ye tıklayın.
4. **Microsoft API 'Leri Seç**' e basın.
5. **Azure hizmet yönetimi**'ni seçin.
6. **Seç**' e basın.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="Uygulama izinlerinin nasıl ekleneceğini gösteren ekran görüntüsü."::: 

7. Yeni eklenen uygulamanın yanındaki **temsilci izinleri** ' ne tıklayın, **Azure hizmet yönetimi 'ne (Önizleme) erişim** kutusunu işaretleyin.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="Atanmış uygulama izinleri eklemeyi gösteren ekran görüntüsü.":::

8. **Seç**' e basın.
9. **Izin Ekle**' ye tıklayın.

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

`{tenant id}` `{application id}` `{redirect uri}` Aşağıdaki yönergeleri kullanarak, ve değiştirin:

1. `{tenant id}`Oluşturduğunuz Azure Active Directory uygulamasının KIRACı kimliğiyle değiştirin. **Uygulama kayıtları**  ->  **uç noktalar**' a tıklayarak kimliğe erişebilirsiniz.

    ![Uç Noktalar][api-management-endpoint]

2. `{application id}` **Ayarlar** sayfasına giderek, aldığınız değerle değiştirin.
3. Değerini, `{redirect uri}` Azure Active Directory uygulamanızın **yeniden yönlendirme URI 'leri** sekmesinden değiştirin.

    Değerler belirtildiğinde, kod örneği aşağıdaki örneğe benzer bir belirteç döndürmelidir:

    ![Belirteç][api-management-arm-token]

    > [!NOTE]
    > Belirtecin belirli bir süre sonunda süresi dolacak. Yeni bir belirteç oluşturmak için kod örneğini yeniden yürütün.

## <a name="calling-the-backup-and-restore-operations"></a>Yedekleme ve geri yükleme işlemlerini çağırma

REST API 'Leri, [API Management hizmeti-yedekleme](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) ve [API Management hizmeti-geri yükleme](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)' dir.

Aşağıdaki bölümlerde açıklanan "yedekleme ve geri yükleme" işlemlerini çağırmadan önce REST çağrınızın yetkilendirme isteği üst bilgisini ayarlayın.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>API Management hizmeti yedekleme

Bir API Management hizmetini yedeklemek için aşağıdaki HTTP isteğini sorun:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

burada:

-   `subscriptionId` -Yedeklemeye çalıştığınız API Management hizmeti tutan aboneliğin KIMLIĞI
-   `resourceGroupName` -Azure API Management hizmetinizin kaynak grubunun adı
-   `serviceName` -oluşturma sırasında belirtilen bir yedeği yaptığınız API Management hizmetin adı
-   `api-version` -ile Değiştir `2019-12-01`

İsteğin gövdesinde, hedef Azure depolama hesabı adı, erişim anahtarı, blob kapsayıcısı adı ve yedekleme adı ' nı belirtin:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

`Content-Type`İstek üst bilgisinin değerini olarak ayarlayın `application/json` .

Yedekleme, tamamlanması bir dakikadan uzun süreolabilecek uzun süredir çalışan bir işlemdir. İstek başarılı olursa ve yedekleme işlemi başladıysa, `202 Accepted` üst bilgiyle bir yanıt durum kodu alırsınız `Location` . İşlemin durumunu öğrenmek için üstbilgideki URL 'ye ' GET ' istekleri yapın `Location` . Yedekleme devam ederken, bir ' 202 kabul edildi ' durum kodu almaya devam edersiniz. Yanıt kodu `200 OK` , yedekleme işleminin başarıyla tamamlandığını gösterir.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>API Management hizmeti geri yükleme

Daha önce oluşturulmuş bir yedekten bir API Management hizmetini geri yüklemek için aşağıdaki HTTP isteğini yapın:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

burada:

-   `subscriptionId` -Bir yedeği geri yüklemekte olduğunuz API Management hizmetini tutan aboneliğin KIMLIĞI
-   `resourceGroupName` -bir yedeği geri yüklemekte olduğunuz Azure API Management hizmetini tutan kaynak grubunun adı
-   `serviceName` -Yükleme sırasında belirtilen API Management hizmetin adı
-   `api-version` -ile Değiştir `api-version=2019-12-01`

İsteğin gövdesinde, yedekleme dosyası konumunu belirtin. Diğer bir deyişle, Azure depolama hesabı adı, erişim anahtarı, blob kapsayıcısı adı ve yedekleme adını ekleyin:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

`Content-Type`İstek üst bilgisinin değerini olarak ayarlayın `application/json` .

Restore, tamamlanması 30 veya daha fazla dakika süren uzun süredir çalışan bir işlemdir. İstek başarılı olursa ve geri yükleme işlemi başladıysa, `202 Accepted` üst bilgiyle bir yanıt durum kodu alırsınız `Location` . İşlemin durumunu öğrenmek için üstbilgideki URL 'ye ' GET ' istekleri yapın `Location` . Geri yükleme devam ederken, ' 202 kabul edildi ' durum kodunu almaya devam edersiniz. Yanıt kodu `200 OK` , geri yükleme işleminin başarıyla tamamlandığını gösterir.

> [!IMPORTANT]
> Geri yüklenen hizmetin **SKU 'su** , geri yüklenmekte olan yedeklenen hizmetin SKU 'su **ile aynı olmalıdır** .
>
> Geri yükleme işlemi devam ederken hizmet yapılandırmasında yapılan **değişiklikler** (örneğin, API 'ler, ilkeler, geliştirici portalı görünümü) **üzerine yazılabilir**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Yedekleme ve geri yükleme işlemleri, sırasıyla PowerShell [_yedekleme-azapimanave_](/powershell/module/az.apimanagement/backup-azapimanagement) [_geri yükleme-azapimanayönetimi_](/powershell/module/az.apimanagement/restore-azapimanagement) komutları ile de gerçekleştirilebilir.

## <a name="constraints-when-making-backup-or-restore-request"></a>Yedekleme veya geri yükleme isteği yapılırken kısıtlamalar

-   İstek gövdesinde belirtilen **kapsayıcı** **mevcut olmalıdır**.
-   Yedekleme devam ederken, hizmette SKU yükseltme veya düşürme, etki alanı adında değişiklik ve daha fazlası gibi **yönetim değişikliklerinden kaçının** .
-   Bir yedeklemenin geri yüklenmesi yalnızca, oluşturulduktan sonra **30 gün boyunca garanti edilir** .
-   Yedekleme işlemi sırasında hizmet yapılandırmasında yapılan **değişiklikler** (örneğin, API 'ler, ilkeler ve geliştirici portalı görünümü) **, yedeklemeden dışlanmayabilir ve kaybolacaktır**.
-   Azure depolama hesabı [güvenlik duvarı][azure-storage-ip-firewall] etkinse, müşteri, yedekleme veya geri yükleme Için depolama hesabındaki [Azure API Management Denetim düzlemi IP adresleri][control-plane-ip-address] kümesine **izin** vermelidir. Azure depolama hesabı, API Management hizmetinin bulunduğu konum dışında herhangi bir Azure bölgesinde olabilir. Örneğin API Management hizmeti Batı ABD ise, Azure depolama hesabı Batı ABD 2 ve müşterinin güvenlik duvarında denetim düzlemi IP 13.64.39.16 (Batı ABD API Management Denetim düzlemi IP 'si) açması gerekir. Bunun nedeni, Azure depolama 'ya yönelik isteklerin aynı Azure bölgesindeki Işlem (Azure API Management Denetim düzlemi) ile genel bir IP 'ye karşı bir şekilde değil. Çapraz bölge depolama isteği, genel IP adresi olarak ele alınacaktır.
-   Azure depolama hesabındaki blob hizmetinde, [çıkış noktaları arası kaynak paylaşımı (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) **etkinleştirilmemelidir.**
-   Geri yüklenen hizmetin **SKU 'su** , geri yüklenmekte olan yedeklenen hizmetin SKU 'su **ile aynı olmalıdır** .

## <a name="what-is-not-backed-up"></a>Yedeklenmeyen
-   Analiz raporları oluşturmak için kullanılan **kullanım verileri** yedeklemeye **dahil değildir** . [Azure API Management REST API][azure api management rest api] kullanarak safekeeping için analiz raporlarını düzenli aralıklarla alın.
-   [Özel etki alanı TLS/SSL](configure-custom-domain.md) sertifikaları.
-   Müşteri tarafından karşıya yüklenen ara veya kök sertifikaları içeren [özel CA sertifikası](api-management-howto-ca-certificates.md).
-   [Sanal ağ](api-management-using-with-vnet.md) tümleştirme ayarları.
-   [Yönetilen kimlik](api-management-howto-use-managed-service-identity.md) yapılandırması.
-   [Azure Izleyici tanısı](api-management-howto-use-azure-monitor.md) Yapılandırmada.
-   [Protokoller ve şifre](api-management-howto-manage-protocols-ciphers.md) ayarları.
-   [Geliştirici Portalı](developer-portal-faq.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) içeriği.

Hizmet yedeklemeleri gerçekleştirdiğiniz sıklık, kurtarma noktası hedefini etkiler. Bunu en aza indirmek için, API Management hizmetinize değişiklikler yaptıktan sonra düzenli yedeklemeler uygulamanızı ve isteğe bağlı yedeklemeler gerçekleştirmenizi öneririz.

## <a name="next-steps"></a>Sonraki adımlar

Yedekleme/geri yükleme işleminin farklı talimatları için aşağıdaki kaynaklara göz atın.

-   [Azure API Management hesaplarını çoğaltma](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Logic Apps ile API Management Yedekleme ve Geri Yükleme İşlemlerini Otomatikleştirme](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: yapılandırmayı](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) 
     yedekleme ve geri yükleme _Stuart tarafından ayrıntılı yaklaşım resmi kılavuzumuzu karşılamıyor ancak ilginç._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
