---
title: AzCopy & Azure Active Directory blob 'lara erişim yetkisi verme | Microsoft Docs
description: AzCopy işlemleri için Azure Active Directory (Azure AD) kullanarak yetkilendirme kimlik bilgileri sağlayabilirsiniz.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/01/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: dd3aeaf133c02ef54eceaff776ead34cc2318260
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220486"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>AzCopy ve Azure Active Directory (Azure AD) ile bloblara erişim yetkisi verme

Azure AD 'yi kullanarak yetkilendirme kimlik bilgileri ile AzCopy sağlayabilirsiniz. Bu şekilde, her komuta bir paylaşılan erişim imzası (SAS) belirteci eklemeniz gerekmez. 

Rol atamalarınızı doğrulayarak başlayın. Ardından, yetkilendirmek istediğiniz _güvenlik sorumlusu_ türünü seçin. Bir [Kullanıcı kimliği](../../active-directory/fundamentals/add-users-azure-active-directory.md), [yönetilen kimlik](../../active-directory/managed-identities-azure-resources/overview.md)ve [hizmet sorumlusu](../../active-directory/develop/app-objects-and-service-principals.md) her bir güvenlik sorumlusu türüdür.

Kullanıcı kimliği, Azure AD 'de kimliği olan herhangi bir kullanıcı. Bu, yetkilendirmenin en kolay güvenlik sorumlusundan biridir. Yönetilen kimlikler ve hizmet sorumluları, Kullanıcı etkileşimi olmadan çalışan bir betiğin içinde AzCopy kullanmayı planlıyorsanız harika seçeneklerdir. Yönetilen bir kimlik, bir Azure sanal makinesinden (VM) çalışan betiklerde daha uygundur ve hizmet sorumlusu, şirket içinde çalışan betikler için daha uygundur. 

AzCopy hakkında daha fazla bilgi için [AzCopy ile çalışmaya](storage-use-azcopy-v10.md)başlayın.

## <a name="verify-role-assignments"></a>Rol atamalarını doğrulama

İhtiyacınız olan yetkilendirme düzeyi, dosyaları karşıya yüklemeyi veya yalnızca indirmeyi planladığınızı temel alır.

Yalnızca dosyaları indirmek istiyorsanız, [Depolama Blobu veri okuyucusu](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) rolünün kullanıcı kimliğiniz, yönetilen kimlik veya hizmet sorumlusu için atandığını doğrulayın.

Dosyaları karşıya yüklemek isterseniz, bu rollerden birinin güvenlik sorumlusuna atandığını doğrulayın:

- [Depolama Blob Verileri Katkıda Bulunanı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Depolama Blob Verileri Sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

Bu roller, bu kapsamların hiçbirinde güvenlik sorumlusuna atanabilir:

- Kapsayıcı (dosya sistemi)
- Depolama hesabı
- Kaynak grubu
- Abonelik

Rolleri doğrulama ve atamayı öğrenmek için bkz. [BLOB ve kuyruk verilerine erişim Için Azure rolü atamak üzere Azure Portal kullanma](./storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Azure rol atamalarının yaymanın en fazla beş dakika sürebileceğini aklınızda bulundurun.

Güvenlik sorumlunuz Hedef kapsayıcının veya dizinin erişim denetim listesine (ACL) eklendiyse güvenlik sorumlusuna bu rollerden birine sahip olmanız gerekmez. ACL 'de güvenlik sorumlunuz hedef dizinde yazma iznine sahip olmalıdır ve kapsayıcıda ve her üst dizinde yürütme iznini gerektirir.

Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. erişim denetimi modeli](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Kullanıcı kimliğini yetkilendirme

Kullanıcı kimliğinize gerekli yetkilendirme düzeyi verildiğini doğruladıktan sonra, bir komut istemi açın, aşağıdaki komutu yazın ve ENTER tuşuna basın.

```azcopy
azcopy login
```

Bir hata alırsanız, depolama hesabının ait olduğu kuruluşun kiracı KIMLIĞINI de kullanmayı deneyin.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>`Yer tutucusunu, depolama hesabının ait olduğu kuruluşun KIRACı kimliğiyle değiştirin. Kiracı KIMLIĞINI bulmak için Azure portal **Azure Active Directory > özellikler > DIZIN kimliği** ' ni seçin.

Bu komut, bir kimlik doğrulama kodu ve bir Web sitesinin URL 'sini döndürür. Web sitesini açın, kodu sağlayın ve sonra **İleri** düğmesini seçin.

![Kapsayıcı oluşturma](media/storage-use-azcopy-v10/azcopy-login.png)

Oturum açma penceresi görüntülenir. Bu pencerede, Azure hesabı kimlik bilgilerinizi kullanarak Azure hesabınızda oturum açın. Başarıyla oturum açtıktan sonra, tarayıcı penceresini kapatabilir ve AzCopy kullanmaya başlayabilirsiniz.

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>Yönetilen kimliği yetkilendirme

Bu, Kullanıcı etkileşimi olmadan çalışan bir betiğin içinde AzCopy kullanmayı planlıyorsanız ve betik bir Azure sanal makineden (VM) çalışıyorsa harika bir seçenektir. Bu seçeneği kullanırken, sanal makinede herhangi bir kimlik bilgisi depolamanız gerekmez.

VM 'niz üzerinde etkinleştirdiğiniz sistem genelinde yönetilen kimlik kullanarak hesabınızda oturum açabilir veya sanal makinenize atadığınız kullanıcı tarafından atanan yönetilen kimliğin istemci KIMLIĞINI, nesne KIMLIĞINI veya kaynak KIMLIĞINI kullanabilirsiniz.

Sistem genelinde yönetilen bir kimliği etkinleştirme veya Kullanıcı tarafından atanan yönetilen kimlik oluşturma hakkında daha fazla bilgi edinmek için bkz. [Azure Portal kullanarak BIR VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Sistem genelinde yönetilen kimlik kullanarak yetkilendirme

İlk olarak, VM 'niz üzerinde sistem genelinde yönetilen bir kimlik etkinleştirdiğinizden emin olun. Bkz. [sistem tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Ardından, komut konsolunuza aşağıdaki komutu yazın ve ENTER tuşuna basın.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik kullanarak yetkilendirme

İlk olarak, VM 'niz üzerinde kullanıcı tarafından atanan bir yönetilen kimliği etkinleştirdiğinizden emin olun. Bkz. [Kullanıcı tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Ardından, komut konsolunuza aşağıdaki komutlardan birini yazın ve ENTER tuşuna basın.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

`<client-id>`Yer tutucusunu Kullanıcı tarafından atanan yönetilen kimliğin ISTEMCI kimliği ile değiştirin.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

`<object-id>`Yer tutucusunu Kullanıcı tarafından atanan yönetilen kimliğin nesne kimliğiyle değiştirin.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

`<resource-id>`Yer tutucusunu Kullanıcı tarafından atanan yönetilen kimliğin kaynak kimliği ile değiştirin.

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>Hizmet sorumlusu yetkilendirme

Bu, özellikle de şirket içinde çalışırken kullanıcı etkileşimi olmadan çalışan bir betiğin içinde AzCopy kullanmayı planlıyorsanız harika bir seçenektir. AzCopy 'i Azure 'da çalışan VM 'lerde çalıştırmayı planlıyorsanız, yönetilen bir hizmet kimliğinin yönetilmesi daha kolaydır. Daha fazla bilgi edinmek için bu makalenin [yönetilen kimlik yetkilendirme](#authorize-a-managed-identity) bölümüne bakın.

Bir betiği çalıştırmadan önce, hizmet sorumlunuzu kimlik bilgileriyle AzCopy sağlayabilmeniz için, etkileşimli olarak en az bir kez oturum açmanız gerekir.  Bu kimlik bilgileri güvenli ve şifreli bir dosyada depolanır, böylece komut dosyanız bu hassas bilgileri sağlamalıdır.

Bir istemci gizli anahtarını veya hizmet sorumlusunun uygulama kaydıyla ilişkili bir sertifikanın parolasını kullanarak hesabınızda oturum açabilirsiniz.

Hizmet sorumlusu oluşturma hakkında daha fazla bilgi edinmek için bkz. [nasıl yapılır: portalı kullanarak kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md).

Genel olarak hizmet sorumluları hakkında daha fazla bilgi için, bkz. [Azure Active Directory uygulama ve hizmet sorumlusu nesneleri](../../active-directory/develop/app-objects-and-service-principals.md)

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>İstemci parolası kullanarak hizmet sorumlusu yetkilendirme

`AZCOPY_SPA_CLIENT_SECRET`Ortam değişkenini, hizmet sorumlusunun uygulama kaydının istemci gizli anahtarı olarak ayarlayarak başlayın.

> [!NOTE]
> Bu değeri, işletim sisteminizin ortam değişkeni ayarlarında değil, komut isteminizden ayarladığınızdan emin olun. Bu şekilde, değer yalnızca geçerli oturum için kullanılabilir.

Bu örnek, PowerShell 'de bunu nasıl yapakullanabileceğinizi gösterir.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Bu örnekte gösterildiği gibi bir istem kullanmayı düşünün. Bu şekilde, parolanız konsolunuzun komut geçmişinde görünmez.  

Sonra, aşağıdaki komutu yazın ve ENTER tuşuna basın.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

`<application-id>`Yer tutucusunu, hizmet sorumlusunun uygulama kaydının uygulama kimliğiyle değiştirin. `<tenant-id>`Yer tutucusunu, depolama hesabının ait olduğu kuruluşun KIRACı kimliğiyle değiştirin. Kiracı KIMLIĞINI bulmak için Azure portal **Azure Active Directory > özellikler > DIZIN kimliği** ' ni seçin. 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Bir sertifika kullanarak hizmet sorumlusu yetkilendirme

Yetkilendirme için kendi kimlik bilgilerinizi kullanmayı tercih ediyorsanız, uygulama kaydınıza bir sertifika yükleyebilir ve sonra bu sertifikayı kullanarak oturum açabilirsiniz.

Sertifikanızı uygulama kaydıza yüklemeye ek olarak, AzCopy 'in çalıştığı makineye veya VM 'ye kaydedilmiş sertifikanın bir kopyasına sahip olmanız da gerekir. Sertifikanın bu kopyası içinde olmalıdır. PFX veya. PEK biçimi ve özel anahtarı içermelidir. Özel anahtar parola korumalı olmalıdır. Windows kullanıyorsanız ve sertifikanız yalnızca bir sertifika deposunda mevcutsa, sertifikayı bir PFX dosyasına (özel anahtar dahil) aktardığınızdan emin olun. Rehberlik için bkz. [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate)

Ardından, `AZCOPY_SPA_CERT_PASSWORD` ortam değişkenini sertifika parolası olarak ayarlayın.

> [!NOTE]
> Bu değeri, işletim sisteminizin ortam değişkeni ayarlarında değil, komut isteminizden ayarladığınızdan emin olun. Bu şekilde, değer yalnızca geçerli oturum için kullanılabilir.

Bu örnek, PowerShell 'de bu görevi nasıl yapakullanabileceğinizi gösterir.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Sonra, aşağıdaki komutu yazın ve ENTER tuşuna basın.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

`<path-to-certificate-file>`Yer tutucusunu, sertifika dosyasının göreli veya tam yoluyla değiştirin. AzCopy, bu sertifikaya olan yolu kaydeder ancak sertifikanın bir kopyasını kaydetmez, bu nedenle bu sertifikayı yerinde sakladığınızdan emin olun. `<tenant-id>`Yer tutucusunu, depolama hesabının ait olduğu kuruluşun KIRACı kimliğiyle değiştirin. Kiracı KIMLIĞINI bulmak için Azure portal **Azure Active Directory > özellikler > DIZIN kimliği** ' ni seçin.

> [!NOTE]
> Bu örnekte gösterildiği gibi bir istem kullanmayı düşünün. Bu şekilde, parolanız konsolunuzun komut geçmişinde görünmez. 

## <a name="authorize-without-a-secret-store"></a>Gizli depo olmadan yetkilendir

`azcopy login`Komut bir OAuth belirteci alır ve bu belirteci sisteminizdeki gizli bir depoya koyar. İşletim sisteminizin Linux *kimlik anahtarlığı* gibi bir gizli deposu yoksa, belirtecin yerleştirileceği bir sorun olduğu için `azcopy login` komut çalışmaz. 

Komutunu kullanmak yerine `azcopy login` , bellek içi ortam değişkenlerini ayarlayabilirsiniz. Ardından herhangi bir AzCopy komutunu çalıştırın. AzCopy, işlemi gerçekleştirmek için gereken kimlik doğrulama belirtecini alır. İşlem tamamlandıktan sonra, belirteç bellekten kaybolur. 

### <a name="authorize-a-user-identity"></a>Kullanıcı kimliğini yetkilendirme

Kullanıcı kimliğinize gerekli yetkilendirme düzeyi verildiğini doğruladıktan sonra, aşağıdaki komutu yazın ve ENTER tuşuna basın.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

Ardından, herhangi bir AzCopy komutunu çalıştırın (örneğin: `azcopy list https://contoso.blob.core.windows.net` ).

Bu komut, bir kimlik doğrulama kodu ve bir Web sitesinin URL 'sini döndürür. Web sitesini açın, kodu sağlayın ve sonra **İleri** düğmesini seçin.

![Kapsayıcı oluşturma](media/storage-use-azcopy-v10/azcopy-login.png)

Oturum açma penceresi görüntülenir. Bu pencerede, Azure hesabı kimlik bilgilerinizi kullanarak Azure hesabınızda oturum açın. Başarıyla oturum açtıktan sonra işlem tamamlanabilir.

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Sistem genelinde yönetilen kimlik kullanarak yetkilendirme

İlk olarak, VM 'niz üzerinde sistem genelinde yönetilen bir kimlik etkinleştirdiğinizden emin olun. Bkz. [sistem tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Aşağıdaki komutu yazın ve ENTER tuşuna basın.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Ardından, herhangi bir AzCopy komutunu çalıştırın (örneğin: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik kullanarak yetkilendirme

İlk olarak, VM 'niz üzerinde kullanıcı tarafından atanan bir yönetilen kimliği etkinleştirdiğinizden emin olun. Bkz. [Kullanıcı tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Aşağıdaki komutu yazın ve ENTER tuşuna basın.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Ardından, aşağıdaki komutlardan birini yazın ve ENTER tuşuna basın.

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

`<client-id>`Yer tutucusunu Kullanıcı tarafından atanan yönetilen kimliğin ISTEMCI kimliği ile değiştirin.

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

`<object-id>`Yer tutucusunu Kullanıcı tarafından atanan yönetilen kimliğin nesne kimliğiyle değiştirin.

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

`<resource-id>`Yer tutucusunu Kullanıcı tarafından atanan yönetilen kimliğin kaynak kimliği ile değiştirin.

Bu değişkenleri ayarladıktan sonra herhangi bir AzCopy komutunu (örneğin: `azcopy list https://contoso.blob.core.windows.net` ) çalıştırabilirsiniz.

### <a name="authorize-a-service-principal"></a>Hizmet sorumlusu yetkilendirme

Bir istemci gizli anahtarını veya hizmet sorumlusunun uygulama kaydıyla ilişkili bir sertifikanın parolasını kullanarak hesabınızda oturum açabilirsiniz.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>İstemci parolası kullanarak hizmet sorumlusu yetkilendirme

Aşağıdaki komutu yazın ve ENTER tuşuna basın.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
export AZCOPY_TENANT_ID=<tenant-id>
```

`<application-id>`Yer tutucusunu, hizmet sorumlusunun uygulama kaydının uygulama kimliğiyle değiştirin. `<client-secret>`Yer tutucusunu, istemci gizli anahtarı ile değiştirin. `<tenant-id>`Yer tutucusunu, depolama hesabının ait olduğu kuruluşun KIRACı kimliğiyle değiştirin. Kiracı KIMLIĞINI bulmak için Azure portal **Azure Active Directory > özellikler > DIZIN kimliği** ' ni seçin. 

> [!NOTE]
> Kullanıcıdan parolayı toplamak için bir istem kullanmayı düşünün. Bu şekilde, parolanız komut geçmişinizde görünmez. 

Ardından, herhangi bir AzCopy komutunu çalıştırın (örneğin: `azcopy list https://contoso.blob.core.windows.net` ).

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Bir sertifika kullanarak hizmet sorumlusu yetkilendirme

Yetkilendirme için kendi kimlik bilgilerinizi kullanmayı tercih ediyorsanız, uygulama kaydınıza bir sertifika yükleyebilir ve sonra bu sertifikayı kullanarak oturum açabilirsiniz.

Sertifikanızı uygulama kaydıza yüklemeye ek olarak, AzCopy 'in çalıştığı makineye veya VM 'ye kaydedilmiş sertifikanın bir kopyasına sahip olmanız da gerekir. Sertifikanın bu kopyası içinde olmalıdır. PFX veya. PEK biçimi ve özel anahtarı içermelidir. Özel anahtar parola korumalı olmalıdır. 

Aşağıdaki komutu yazın ve ENTER tuşuna basın.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
export AZCOPY_TENANT_ID=<tenant-id>
```

`<path-to-certificate-file>`Yer tutucusunu, sertifika dosyasının göreli veya tam yoluyla değiştirin. AzCopy, bu sertifikaya olan yolu kaydeder ancak sertifikanın bir kopyasını kaydetmez, bu nedenle bu sertifikayı yerinde sakladığınızdan emin olun. `<certificate-password>`Yer tutucusunu sertifikanın parolasıyla değiştirin. `<tenant-id>`Yer tutucusunu, depolama hesabının ait olduğu kuruluşun KIRACı kimliğiyle değiştirin. Kiracı KIMLIĞINI bulmak için Azure portal **Azure Active Directory > özellikler > DIZIN kimliği** ' ni seçin. 

> [!NOTE]
> Kullanıcıdan parolayı toplamak için bir istem kullanmayı düşünün. Bu şekilde, parolanız komut geçmişinizde görünmez. 

Ardından, herhangi bir AzCopy komutunu çalıştırın (örneğin: `azcopy list https://contoso.blob.core.windows.net` ).

## <a name="next-steps"></a>Sonraki adımlar

- AzCopy hakkında daha fazla bilgi için [AzCopy ile çalışmaya başlayın](storage-use-azcopy-v10.md)

- Sorularınız, sorunlarınız veya genel geri bildiriminiz varsa bunları [GitHub](https://github.com/Azure/azure-storage-azcopy) sayfasına gönderin.