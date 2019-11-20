---
title: OPC Kasası sertifika hizmetini yönetme-Azure | Microsoft Docs
description: OPC Kasası kök CA sertifikalarını ve Kullanıcı izinlerini yönetin.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203659"
---
# <a name="manage-the-opc-vault-certificate-service"></a>OPC Kasası sertifika hizmetini yönetme

Bu makalede, Azure 'daki OPC Kasası sertifika yönetimi hizmeti için yönetim görevleri açıklanmaktadır. Sertifikayı veren CA sertifikalarını yenileme, sertifika Iptal listesini (CRL) yenileme ve Kullanıcı erişimini verme ve iptal etme hakkında bilgiler içerir.

## <a name="create-or-renew-the-root-ca-certificate"></a>Kök CA sertifikası oluşturma veya yenileme

OPC kasasını dağıttıktan sonra kök CA sertifikası oluşturmalısınız. Geçerli bir veren CA sertifikası olmadan, uygulama sertifikalarını imzalayamıyorum veya veremez. Sertifikaları makul, güvenli ömürlerle yönetmek için [Sertifikalar](howto-opc-vault-secure-ca.md#certificates) bölümüne bakın. Bir veren CA sertifikasını yaşam sürelerinin yarısından sonra yenileyin. Yenilendiğinde, yeni imzalanmış bir uygulama sertifikasının yapılandırılan ömrünün, veren CA sertifikasının ömrünü aşmaması de göz önünde bulundurun.
> [!IMPORTANT]
> Yönetici rolü, veren CA sertifikasını oluşturmak veya yenilemek için gereklidir.

1. Sertifika hizmetinizi adresinde `https://myResourceGroup-app.azurewebsites.net`açın ve oturum açın.
2. **Sertifika gruplarına**gidin.
3. Listelenen bir varsayılan sertifika grubu vardır. **Düzenle**’yi seçin.
4. **Sertifika grubu ayrıntılarını Düzenle**' de, CA ve uygulama sertifikalarınızın konu adını ve ömrünü değiştirebilirsiniz. Konu ve yaşam süreleri yalnızca ilk CA sertifikası verilmeden önce bir kez ayarlanmalıdır. İşlemler sırasında yaşam süresi değişiklikleri, verilen sertifikaların ve CRL 'lerin tutarsız ömürleri oluşmasına neden olabilirler.
5. Geçerli bir konu girin (örneğin, `CN=My CA Root, O=MyCompany, OU=MyDepartment`).<br>
   > [!IMPORTANT]
   > Konuyu değiştirirseniz, veren sertifikasını yenilemeniz gerekir, aksi takdirde hizmet uygulama sertifikalarını imzalamaz. Yapılandırmanın konusu, etkin veren sertifikasının konusuna göre denetlenir. Konular eşleşmezse, sertifika imzalama reddedilir.
6. **Kaydet**’i seçin.
7. Bu noktada "yasak" hatasıyla karşılaşırsanız, Kullanıcı kimlik bilgilerinizin yeni bir kök sertifika değiştirme veya oluşturma izni yoktur. Varsayılan olarak, hizmeti dağıtan kullanıcının hizmeti ile yönetici ve imzalama rolleri vardır. Diğer kullanıcıların, Azure Active Directory (Azure AD) uygulama kaydında uygun şekilde onaylayan, yazıcı veya yönetici rollerine eklenmesi gerekir.
8. **Ayrıntılar**' ı seçin. Bu, güncelleştirilmiş bilgileri göstermelidir.
9. İlk veren CA sertifikasını vermek veya veren sertifikasını yenilemek için **CA sertifikasını Yenile** ' yi seçin. Sonra **Tamam**’ı seçin.
10. Birkaç saniye sonra **sertifika ayrıntılarını**görürsünüz. OPC UA uygulamalarınıza dağıtılmak üzere en son CA sertifikasını ve CRL 'yi indirmek için **veren** veya **CRL**' yi seçin.

Artık OPC UA sertifika yönetimi hizmeti OPC UA uygulamalarına yönelik sertifikalar vermek için hazırdır.

## <a name="renew-the-crl"></a>CRL 'YI yenileme

CRL Yenileme, düzenli aralıklarla uygulamalara dağıtılması gereken bir güncelleştirmedir. CRL dağıtım noktası x509 uzantısını destekleyen OPC UA cihazları, CRL 'YI mikro hizmet uç noktasından doğrudan güncelleştirebilir. Diğer OPC UA cihazları el ile güncelleştirme gerektirebilir veya güven listelerini sertifikalar ve CRL 'Ler ile güncelleştirmek için GDS sunucu gönderme uzantıları (*) kullanılarak güncelleştirilemeyebilir.

Aşağıdaki iş akışında, silinen durumlardaki tüm sertifika istekleri, verildiği sertifikayı veren CA sertifikasına karşılık gelen CRL 'lerde iptal edilir. CRL 'nin sürüm numarası 1 artırılır. <br>
> [!NOTE]
> Verilen tüm CRL 'Ler veren CA sertifikasının süresi dolduktan kadar geçerlidir. Bunun nedeni OPC UA belirtiminin CRL için zorunlu ve belirleyici bir dağıtım modeli gerektirmesidir.

> [!IMPORTANT]
> Veren CRL 'YI yenilemek için yönetici rolü gereklidir.

1. Sertifika hizmetinizi adresinde `https://myResourceGroup.azurewebsites.net`açın ve oturum açın.
2. **Sertifika grupları** sayfasına gidin.
3. **Ayrıntılar**' ı seçin. Bu, geçerli sertifika ve CRL bilgilerini göstermelidir.
4. OPC kasa depolama alanındaki tüm etkin veren sertifikaları için güncelleştirilmiş bir CRL vermek üzere **CRL Iptal listesini (CRL) Güncelleştir** ' i seçin.
5. Birkaç saniye sonra **sertifika ayrıntılarını**görürsünüz. OPC UA uygulamalarınıza dağıtılmak üzere en son CA sertifikasını ve CRL 'yi indirmek için **veren** veya **CRL**' yi seçin.

## <a name="manage-user-roles"></a>Kullanıcı rollerini yönetme

Azure AD kurumsal uygulamasındaki OPC Kasası mikro hizmeti için Kullanıcı rollerini yönetirsiniz. Rol tanımlarının ayrıntılı bir açıklaması için bkz. [Roller](howto-opc-vault-secure-ca.md#roles).

Varsayılan olarak, Kiracıdaki kimliği doğrulanmış bir Kullanıcı hizmette bir okuyucu olarak oturum açabilir. Daha yüksek ayrıcalıklı roller Azure portal veya PowerShell kullanarak el ile yönetim gerektirir.

### <a name="add-user"></a>Kullanıcı ekle

1. Azure portalı açın.
2. **Azure Active Directory** > **kurumsal uygulamalara**gidin.
3. OPC Kasası mikro hizmetinin (varsayılan olarak, sizin `resourceGroupName-service`) kaydını seçin.
4. **Kullanıcılar ve gruplar**'a gidin.
5. **Kullanıcı Ekle**' yi seçin.
6. Kullanıcıyı belirli bir role atamayı seçin veya davet edin.
7. Kullanıcılar için rol seçin.
8. **Ata**'yı seçin.
9. Yönetici veya onaylayan rolündeki kullanıcılar için Azure Key Vault erişim ilkeleri eklemeye devam edin.

### <a name="remove-user"></a>Kullanıcıyı kaldır

1. Azure portalı açın.
2. **Azure Active Directory** > **kurumsal uygulamalara**gidin.
3. OPC Kasası mikro hizmetinin (varsayılan olarak, sizin `resourceGroupName-service`) kaydını seçin.
4. **Kullanıcılar ve gruplar**'a gidin.
5. Kaldırılacak rolü olan bir kullanıcı seçin ve ardından **Kaldır**' ı seçin.
6. Yönetici veya onaylayan rolünde kaldırılan kullanıcılar için de onları Azure Key Vault ilkelerinden kaldırın.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Azure Key Vault Kullanıcı erişim ilkesi Ekle

Onaylayanlar ve yöneticiler için ek erişim ilkeleri gerekir.

Varsayılan olarak, hizmet kimliği, yükseltilmiş işlemleri veya değişikliklerin kullanıcı kimliğine bürünme olmadan gerçekleşmesini engellemek için Key Vault erişim için yalnızca sınırlı izinlere sahiptir. Her iki gizli dizi ve sertifika için temel hizmet izinleri get ve List ' dir. Gizli diziler için yalnızca bir istisna vardır: hizmet, bir kullanıcı tarafından kabul edildikten sonra gizli bir anahtarı gizli depolamadan silebilir. Tüm diğer işlemler için kullanıcı kimliğine bürünme izinleri gerekir.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Bir onaylayan rolü için aşağıdaki izinler Key Vault eklenmelidir

1. Azure portalı açın.
2. Dağıtım sırasında kullanılan OPC kasaıza `resourceGroupName`gidin.
3. Key Vault `resourceGroupName-xxxxx`gidin.
4. **Erişim ilkeleri**' ne gidin.
5. **Yeni Ekle**' yi seçin.
6. Şablonu atlayın. Gereksinimlerle eşleşen bir şablon yok.
7. **Sorumluyu Seç**' i seçin ve eklenecek kullanıcıyı seçin veya kiracıya yeni bir kullanıcı davet edin.
8. Aşağıdaki **anahtar izinlerini**seçin: **Al**, **Listele**ve **imzala**.
9. Aşağıdaki **gizli izinleri**seçin: **Al**, **Listele**, **Ayarla**ve **Sil**.
10. Aşağıdaki **Sertifika izinlerini**seçin: **Al** ve **Listele**.
11. **Tamam**' ı seçin ve **Kaydet**' i seçin.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Yönetici rolü için aşağıdaki izinler Key Vault eklenmelidir

1. Azure portalı açın.
2. Dağıtım sırasında kullanılan OPC kasaıza `resourceGroupName`gidin.
3. Key Vault `resourceGroupName-xxxxx`gidin.
4. **Erişim ilkeleri**' ne gidin.
5. **Yeni Ekle**' yi seçin.
6. Şablonu atlayın. Gereksinimlerle eşleşen bir şablon yok.
7. **Sorumluyu Seç**' i seçin ve eklenecek kullanıcıyı seçin veya kiracıya yeni bir kullanıcı davet edin.
8. Aşağıdaki **anahtar izinlerini**seçin: **Al**, **Listele**ve **imzala**.
9. Aşağıdaki **gizli izinleri**seçin: **Al**, **Listele**, **Ayarla**ve **Sil**.
10. Aşağıdaki **Sertifika izinlerini**seçin: **Alma**, **Listeleme**, **güncelleştirme**, **oluşturma**ve **içeri aktarma**.
11. **Tamam**' ı seçin ve **Kaydet**' i seçin.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Azure Key Vault Kullanıcı erişim ilkesini kaldırma

1. Azure portalı açın.
2. Dağıtım sırasında kullanılan OPC kasaıza `resourceGroupName`gidin.
3. Key Vault `resourceGroupName-xxxxx`gidin.
4. **Erişim ilkeleri**' ne gidin.
5. Kaldırılacak kullanıcıyı bulun ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Artık OPC Kasası sertifikalarını ve kullanıcılarını yönetmeyi öğrendiğinize göre şunları yapabilirsiniz:

> [!div class="nextstepaction"]
> [OPC cihazlarıyla güvenli iletişim](howto-opc-vault-secure.md)
