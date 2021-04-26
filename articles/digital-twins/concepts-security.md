---
title: Azure Digital Twins çözümleri için güvenlik
titleSuffix: Azure Digital Twins
description: Azure dijital TWINS ile en iyi güvenlik uygulamalarını anlayın.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 81a44d4d0025c841cf56e19d6afee5e95bd44a55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101730516"
---
# <a name="secure-azure-digital-twins"></a>Güvenli Azure dijital TWINS

Azure dijital TWINS güvenliği için, dağıtımınızdaki belirli veriler, kaynaklar ve eylemler üzerinde kesin erişim denetimi imkanı sunar. Bunu, **Azure rol tabanlı erişim denetimi (Azure RBAC)** adlı ayrıntılı bir rol ve izin yönetimi stratejisi aracılığıyla yapar. Azure RBAC 'nin genel ilkeleri hakkında [buradan](../role-based-access-control/overview.md)bilgi edinebilirsiniz.

Azure dijital TWINS, bekleyen verilerin şifrelenmesini de destekler.

## <a name="roles-and-permissions-with-azure-rbac"></a>Azure RBAC ile roller ve izinler

Azure RBAC, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ile tümleştirme yoluyla Azure dijital TWINS 'e sağlanır.

Bir Kullanıcı, Grup veya uygulama hizmeti sorumlusu olabilecek bir *güvenlik sorumlusu* için izin vermek üzere Azure RBAC kullanabilirsiniz. Güvenlik sorumlusu Azure AD tarafından doğrulanır ve döndürülen bir OAuth 2,0 belirteci alır. Bu belirteç, bir Azure dijital TWINS örneğine erişim isteği yetkilendirmek için kullanılabilir.

### <a name="authentication-and-authorization"></a>Kimlik doğrulaması ve yetkilendirme

Azure AD ile, erişim iki adımlı bir işlemdir. Bir güvenlik sorumlusu (bir Kullanıcı, Grup veya uygulama) Azure dijital TWINS 'e erişmeyi denediğinde, isteğin *kimliğinin doğrulanması* ve *yetkilendirilmiş* olması gerekir. 

1. İlk olarak, güvenlik sorumlusunun kimliği *doğrulanır* ve bir OAuth 2,0 belirteci döndürülür.
2. Ardından, belirteç, belirtilen kaynağa erişimi *yetkilendirmek* Için Azure dijital TWINS hizmetine yapılan bir isteğin parçası olarak geçirilir.

Kimlik doğrulama adımı, herhangi bir uygulama isteğinin çalışma zamanında bir OAuth 2,0 erişim belirteci içermesini gerektirir. Bir uygulama, [Azure işlevleri](../azure-functions/functions-overview.md) uygulaması gibi bir Azure varlığı içinde çalışıyorsa, kaynaklara erişmek için **yönetilen bir kimlik** kullanabilir. Sonraki bölümde Yönetilen kimlikler hakkında daha fazla bilgi edinin.

Yetkilendirme adımı, güvenlik sorumlusuna bir Azure rolünün atanmasını gerektirir. Bir güvenlik sorumlusu 'na atanan roller, sorumlunun sahip olacağı izinleri belirleyebilir. Azure dijital TWINS, Azure dijital TWINS kaynakları için izin kümelerini çevreleyen Azure rolleri sağlar. Bu roller, bu makalenin ilerleyen kısımlarında açıklanmıştır.

Azure 'da desteklenen roller ve rol atamaları hakkında daha fazla bilgi edinmek için bkz. Azure RBAC belgelerindeki [*farklı rolleri anlama*](../role-based-access-control/rbac-and-directory-admin-roles.md) .

#### <a name="authentication-with-managed-identities"></a>Yönetilen kimliklerle kimlik doğrulaması

[Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) , uygulama kodunuzun çalıştırıldığı dağıtımla ilişkili güvenli bir kimlik oluşturmanıza olanak sağlayan bir çapraz Azure özelliğidir. Böylece, uygulamanızın ihtiyaç duyacağı belirli Azure kaynaklarına erişim için özel izinler vermek üzere bu kimliği erişim denetimi rolleriyle ilişkilendirebilirsiniz.

Yönetilen kimlikler ile Azure platformu bu çalışma zamanı kimliğini yönetir. Uygulama kodunuzda veya yapılandırmanızda, kimliğin kendisi için ya da erişmeniz gereken kaynaklar için erişim anahtarlarını depolamanız ve korumanız gerekmez. Azure App Service uygulamasının içinde çalışan bir Azure dijital TWINS istemci uygulamasının SAS kurallarını ve anahtarlarını veya diğer erişim belirteçlerini işlemesi gerekmez. İstemci uygulaması yalnızca Azure Digital TWINS ad alanının uç nokta adresine ihtiyaç duyuyor. Uygulama bağlandığında, Azure Digital TWINS yönetilen varlığın bağlamını istemciye bağlar. Yönetilen bir kimlikle ilişkilendirildikten sonra Azure dijital TWINS istemciniz tüm yetkili işlemleri gerçekleştirebilir. Daha sonra, yönetilen bir varlık bir Azure dijital TWINS Azure rolüyle (aşağıda açıklanmıştır) ilişkilendirilerek yetkilendirme verilecektir.

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Yetkilendirme: Azure dijital TWINS için Azure rolleri

Azure, Azure dijital TWINS [veri düzlemi API 'lerine](how-to-use-apis-sdks.md#overview-data-plane-apis)erişim yetkisi veren **iki Azure yerleşik rolü** sağlar. Rollere ada veya KIMLIĞE göre başvurabilirsiniz:

| Yerleşik rol | Description | ID | 
| --- | --- | --- |
| Azure dijital TWINS veri sahibi | Azure dijital TWINS kaynakları üzerinden tam erişim sağlar | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure dijital TWINS veri okuyucusu | Azure dijital TWINS kaynaklarına salt okuma erişimi verir | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

Rolleri iki şekilde atayabilirsiniz:
* Azure portal Azure dijital TWINS için erişim denetimi (ıAM) bölmesi aracılığıyla (bkz [*. Azure Portal kullanarak Azure rolleri atama*](../role-based-access-control/role-assignments-portal.md))
* bir rol eklemek veya kaldırmak için CLı komutları aracılığıyla

Bunun nasıl yapılacağı hakkında daha ayrıntılı adımlar için Azure dijital TWINS [*öğreticisinde deneyin: uçtan uca bir çözümü bağlama*](tutorial-end-to-end.md).

Yerleşik rollerin nasıl tanımlandığı hakkında daha fazla bilgi için bkz. Azure RBAC belgelerindeki [*rol tanımlarını anlama*](../role-based-access-control/role-definitions.md) . Azure özel rolleri oluşturma hakkında daha fazla bilgi için bkz. [*Azure özel roller*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Rolleri otomatikleştirme

Otomatikleştirilmiş senaryolardaki rollere başvuru yaparken, bunlara adları yerine **kimliklerini** yazmanız önerilir. Adlar yayınlar arasında değişebilir, ancak kimlikler bu şekilde Otomasyon 'da daha kararlı bir başvuru haline getirir.

> [!TIP]
> (Başvuru) gibi bir cmdlet 'e sahip rolleriniz assiging `New-AzRoleAssignment` ,[](/powershell/module/az.resources/new-azroleassignment) `-RoleDefinitionId` `-RoleDefinitionName` rol için bir ad yerine kimliği geçirmek yerine parametresini kullanabilirsiniz.

### <a name="permission-scopes"></a>İzin kapsamları

Güvenlik sorumlusuna bir Azure rolü atamadan önce, güvenlik sorumlusunun sahip olması gereken erişimin kapsamını saptayın. En iyi uygulamalar, yalnızca en dar olası kapsamı vermek için idealdir.

Aşağıdaki listede, Azure dijital TWINS kaynaklarına erişimi kapsama ekleyebileceğiniz düzeyler açıklanmaktadır.
* Modeller: Bu kaynak için Eylemler, Azure dijital TWINS 'te karşıya yüklenen [modeller](concepts-models.md) üzerinde denetim dikte ediyor.
* Sorgu dijital TWINS grafiği: Bu kaynak için Eylemler, Azure dijital TWINS grafiğinde dijital TWINS üzerinde [sorgu işlemleri](concepts-query-language.md) çalıştırma yeteneğini belirleme.
* Digital Ikizi: Bu kaynak için Eylemler, ikizi grafiğindeki [dijital TWINS](concepts-twins-graph.md) üzerinde CRUD işlemlerine denetim sağlar.
* Digital Ikizi Relationship: Bu kaynak için Eylemler, ikizi grafiğindeki dijital TWINS arasındaki [ilişkilerdeki](concepts-twins-graph.md) CRUD işlemleri üzerinde denetimi tanımlar.
* Olay rotası: Bu kaynak için Eylemler, Azure dijital TWINS 'deki olayları [Olay Hub](../event-hubs/event-hubs-about.md)'ı, [Event Grid](../event-grid/overview.md)veya [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)gibi bir uç nokta hizmetine [yönlendiren](concepts-route-events.md) izinleri belirleyebilir.

### <a name="troubleshooting-permissions"></a>İzinlerin sorunlarını giderme

Bir kullanıcı rolü tarafından izin verilmeyen bir eylem gerçekleştirmeye çalışırsa, hizmet isteği okumada bir hata alabilir `403 (Forbidden)` . Daha fazla bilgi ve sorun giderme adımları için bkz. [*sorun giderme: Azure dijital TWINS isteği şu durumla başarısız oldu: 403 (yasak)*](troubleshoot-error-403.md).

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Diğer kaynaklara erişmek için yönetilen kimlik (Önizleme)

Azure dijital TWINS örneği için [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **tarafından yönetilen bir kimlik** ayarlamak, örneğin [Azure Key Vault](../key-vault/general/overview.md)gibi diğer Azure AD korumalı kaynaklara kolayca erişmesine izin verebilir. Kimlik, Azure platformu tarafından yönetilir ve herhangi bir gizli dizi sağlamanızı veya döndürmenizi gerektirmez. Azure AD 'de Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [*Azure kaynakları Için Yönetilen kimlikler*](../active-directory/managed-identities-azure-resources/overview.md). 

Azure iki tür yönetilen kimliği destekler: sistem tarafından atanan ve Kullanıcı tarafından atanan. Şu anda Azure Digital TWINS yalnızca **sistem tarafından atanan kimlikleri** destekliyor. 

[Özel tanımlanmış bir uç noktada](concepts-route-events.md#create-an-endpoint)kimlik doğrulaması yapmak Için Azure dijital örneğiniz için sistem tarafından atanan yönetilen bir kimlik kullanabilirsiniz. Azure dijital TWINS, [Olay Hub](../event-hubs/event-hubs-about.md) 'ı ve [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)hedeflerine yönelik uç noktalara   ve atılacak Ileti olayları için bir [Azure depolama kapsayıcısı](../storage/blobs/storage-blobs-introduction.md)   uç noktasına [](concepts-route-events.md#dead-letter-events)sistem tarafından atanan kimlik tabanlı kimlik doğrulamasını destekler. [Event Grid](../event-grid/overview.md)   uç noktalar şu anda yönetilen kimlikler için desteklenmiyor.

Azure dijital TWINS için sistem tarafından yönetilen bir kimliğin nasıl etkinleştirileceği ve olayları yönlendirmek için nasıl kullanılacağı hakkında yönergeler için bkz. [*nasıl yapılır: yönlendirme olayları için yönetilen kimliği etkinleştirme (Önizleme)*](./how-to-enable-managed-identities-portal.md).

## <a name="private-network-access-with-azure-private-link-preview"></a>Azure özel bağlantısı ile özel ağ erişimi (Önizleme)

Azure [özel bağlantı](../private-link/private-link-overview.md) , Azure kaynaklarına ( [Azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure depolama](../storage/common/storage-introduction.md)ve [Azure Cosmos DB](../cosmos-db/introduction.md)gibi) ve Azure 'Da barındırılan müşteri ve iş ortağı hizmetlerine Azure [sanal ağınızdaki (VNet)](../virtual-network/virtual-networks-overview.md)özel bir uç nokta üzerinden erişmenizi sağlayan bir hizmettir. 

Benzer şekilde, sanal Ağınızdaki istemcilerin özel bağlantı üzerinden örneğe güvenli bir şekilde erişmesini sağlamak için Azure Digital Ikizi örneğiniz için özel uç noktalar kullanabilirsiniz. 

Özel uç nokta, Azure VNet adres alanınızda bir IP adresi kullanır. Özel ağınızdaki bir istemci ile Azure dijital TWINS örneği arasındaki ağ trafiği, Microsoft omurgası ağı üzerinde, genel İnternet 'te pozlandırmayı ortadan kaldıran VNet üzerinden ve özel bir bağlantıdan geçer. Bu sistemin görsel bir gösterimi aşağıda verilmiştir:

:::image type="content" source="media/concepts-security/private-link.png" alt-text="İnternet/genel bulut erişimi olmayan korumalı bir sanal ağ olan ve CityOfTwins adlı bir Azure dijital TWINS örneğine özel bağlantıyla bağlantı kurarak PowerGrid şirketinin ağını gösteren bir diyagram.":::

Azure dijital TWINS örneğiniz için özel bir uç nokta yapılandırmak, Azure dijital TWINS örneğinizi güvenli hale getirmenizi ve genel pozlamayı ortadan kaldırmanıza ve sanal ağınızdan veri alımını önlemenize olanak sağlar.

Azure dijital TWINS için özel bağlantı ayarlama hakkında yönergeler için bkz. [*nasıl yapılır: özel bağlantı ile özel erişimi etkinleştirme (Önizleme)*](./how-to-enable-private-link-portal.md).

### <a name="design-considerations"></a>Tasarım konusunda dikkat edilmesi gerekenler 

Azure dijital TWINS için özel bağlantıyla çalışırken göz önünde bulundurmanız isteyebileceğiniz bazı faktörler şunlardır:
* **Fiyatlandırma**: Fiyatlandırma ayrıntıları için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link). 
* **Bölgesel kullanılabilirlik**: Azure Digital TWINS için bu özellik, Azure Digital TWINS 'in kullanılabildiği tüm Azure bölgelerinde kullanılabilir. 
* **Azure dijital TWINS örneği başına en fazla özel uç nokta sayısı**: 10

Özel bağlantı sınırları hakkında daha fazla bilgi için bkz. [Azure özel bağlantı belgeleri: sınırlamalar](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Hizmet etiketleri

**Hizmet etiketi** , belirli bir Azure HIZMETINDEN bir IP adresi önekleri grubunu temsil eder. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir ve ağ güvenlik kuralları için sık sık güncelleştirmelerin karmaşıklığını en aza indirir. Hizmet etiketleri hakkında daha fazla bilgi için bkz. [*sanal ağ etiketleri*](../virtual-network/service-tags-overview.md). 

 [](../virtual-network/network-security-groups-overview.md#security-rules)   Güvenlik KURALLARı oluştururken belirli IP adreslerinin yerine hizmet etiketleri kullanarak ağ güvenlik gruplarında veya [Azure Güvenlik duvarında](../firewall/service-tags.md)ağ erişim denetimleri tanımlamak için hizmet etiketlerini kullanabilirsiniz. Bir kuralın uygun *kaynak* veya hedef alanındaki hizmet etiketi adını (Bu durumda **AzureDigitalTwins**) belirterek    **   , karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. 

Aşağıda **AzureDigitalTwins** hizmeti etiketinin ayrıntıları verilmiştir.

| Etiket | Amaç | Gelen veya giden trafiği kullanabilir miyim? | Bölgesel olabilir mi? | Azure Güvenlik Duvarı ile kullanılabilir mi? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>Note: Bu etiket veya bu etiketin kapsadığı IP adresleri, [olay yolları](concepts-route-events.md)için yapılandırılmış uç noktalara erişimi kısıtlamak için kullanılabilir. | Gelen | Hayır | Yes |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Olay yolu uç noktalarına erişmek için hizmet etiketleri kullanma 

Azure dijital TWINS ile hizmet etiketlerini kullanarak [olay yönlendirme](concepts-route-events.md) uç noktalarına erişme adımları aşağıda verilmiştir.

1. İlk olarak, Azure IP aralıklarını ve hizmet etiketlerini gösteren bu JSON dosyası başvurusunu indirin: [*Azure IP aralıkları ve hizmet etiketleri*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. JSON dosyasında "AzureDigitalTwins" IP aralıklarını arayın.  

3. Bu kaynakla ilgili IP filtrelerini nasıl ayarlayaöğrenmek için uç noktaya bağlı dış kaynağın (örneğin, [Event Grid](../event-grid/overview.md), [Olay Hub](../event-hubs/event-hubs-about.md)'ı, [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)veya [atılacak ileti olayları](concepts-route-events.md#dead-letter-events)için [Azure Storage](../storage/blobs/storage-blobs-overview.md) ) belgelerine bakın.

4. *2. ADıMDAKI* IP aralıklarını kullanarak dış kaynaklar üzerinde IP filtreleri ayarlayın.  

5. IP aralıklarını gerektiği şekilde düzenli olarak güncelleştirin. Aralıklar zaman içinde değişebilir, bu nedenle bunları düzenli olarak denetlemek ve gerektiğinde yenilemek iyi bir fikirdir. Bu güncelleştirmelerin sıklığı farklılık gösterebilir, ancak bunları haftada bir kez denetlemek iyi bir fikirdir.

## <a name="encryption-of-data-at-rest"></a>Bekleyen verilerin şifrelenmesi

Azure dijital TWINS, veri merkezlerimizde yazıldığı sırada verilerin geri kalanı ve aktarım sırasında şifrelenmesini sağlar ve ona erişirken şifresini çözer. Bu şifreleme, Microsoft tarafından yönetilen bir şifreleme anahtarı kullanılarak oluşur.

## <a name="cross-origin-resource-sharing-cors"></a>Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)

Azure dijital TWINS Şu anda **çıkış noktaları arası kaynak paylaşımını (CORS)** desteklememektedir. Sonuç olarak, bir tarayıcı uygulamasından, bir [API Management (APıM)](../api-management/api-management-key-concepts.md) arabiriminden veya bir [Power Apps](/powerapps/powerapps-overview) bağlayıcısından REST API arıyorsanız bir ilke hatası görebilirsiniz.

Bu hatayı çözmek için aşağıdaki eylemlerden birini yapabilirsiniz:
* İletibir bilgisayardan CORS üst bilgisini şerit `Access-Control-Allow-Origin` . Bu üstbilgi yanıtın paylaşılıp paylaşılamayacağını gösterir. 
* Alternatif olarak, bir CORS proxy 'si oluşturun ve Azure dijital TWINS REST API isteği üzerinden isteyin. 

## <a name="next-steps"></a>Sonraki adımlar

* [*Nasıl yapılır: bir örneği ve kimlik doğrulamasını ayarlama*](how-to-set-up-instance-portal.md)bölümünde bu kavramlara bakın.

* [*Nasıl yapılır: yazma uygulaması kimlik doğrulaması kodunda*](how-to-authenticate-client.md)istemci uygulama kodundan bu kavramlarla nasıl etkileşim kuracağınızı öğrenin.

* [Azure RBAC](../role-based-access-control/overview.md)hakkında daha fazla bilgi edinin.