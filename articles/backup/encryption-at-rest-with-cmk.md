---
title: Müşteri tarafından yönetilen anahtarları kullanarak yedekleme verilerini şifreleme
description: Azure Backup, müşteri tarafından yönetilen anahtarları (CMK) kullanarak yedekleme verilerinizi şifrelemenize nasıl olanak sağladığını öğrenin.
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: bd51be06e707674f3e35b3478d7f99d096be912a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718782"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları kullanarak yedekleme verilerini şifreleme

Azure Backup, varsayılan olarak etkinleştirilen platform tarafından yönetilen anahtarlar kullanmak yerine, müşteri tarafından yönetilen anahtarları (CMK) kullanarak yedekleme verilerinizi şifrelemenize olanak tanır. Yedekleme verilerini şifrelemek için kullanılan anahtarlarınızın [Azure Key Vault](../key-vault/index.yml)' de depolanması gerekir.

Yedeklemeleri şifrelemek için kullanılan şifreleme anahtarı, kaynak için kullanılan birinden farklı olabilir. Veriler, sırasıyla anahtarlarınız kullanılarak (KEK) korunan, AES 256 tabanlı bir veri şifreleme anahtarı (DEK) kullanılarak korunur. Bu sayede veriler ve anahtarlar üzerinde tam denetim elde edersiniz. Şifrelemeye izin vermek için, kurtarma hizmetleri kasasının Azure Key Vault şifreleme anahtarına erişim verilmesi gerekir. Anahtarı gerektiğinde ve olarak değiştirebilirsiniz.

Bu makalede aşağıdakiler ele alınmaktadır:

- Kurtarma Hizmetleri Kasası oluşturma
- Kurtarma Hizmetleri kasanızı, müşteri tarafından yönetilen anahtarları kullanarak yedekleme verilerini şifrelemek üzere yapılandırma
- Müşteri tarafından yönetilen anahtarlar kullanılarak şifrelenmiş yedekleme gerçekleştirme
- Yedeklerden verileri geri yükleme

## <a name="before-you-start"></a>Başlamadan önce

- Bu özellik **yalnızca yeni kurtarma hizmetleri kasalarını** şifrelemenizi sağlar. Kayıtlı veya kayıtlı olan öğeleri içeren kasaların hiçbiri desteklenmez.

- Bir kurtarma hizmetleri Kasası için etkinleştirildikten sonra, müşteri tarafından yönetilen anahtarları kullanarak şifreleme, platform tarafından yönetilen anahtarlar (varsayılan) ile geri döndürülemez. Şifreleme anahtarlarını gereksinimlerinize göre değiştirebilirsiniz.

- Bu özellik şu anda **Mars Aracısı 'nı kullanarak yedeklemeyi desteklemiyor** ve aynı şekilde CMK şifreli bir kasa kullanmayabilir. MARS Aracısı Kullanıcı parolası tabanlı bir şifreleme kullanır. Bu özellik, klasik VM 'lerin yedeklenmesini de desteklemez.

- Bu özellik [Azure disk şifrelemesi](../security/fundamentals/azure-disk-encryption-vms-vmss.md)ile ilgili değildir ve BitLocker (Windows için) ve DM-Crypt (Linux için) kullanarak bir VM disklerinin Konuk tabanlı şifrelemesini kullanır

- Kurtarma Hizmetleri Kasası, yalnızca **aynı bölgede** bulunan bir Azure Key Vault depolanan anahtarlarla şifrelenebilir. Ayrıca anahtarlar yalnızca **RSA anahtarları** olmalıdır ve **etkin** durumda olmalıdır.

- CMK şifreli kurtarma hizmetleri kasasını kaynak grupları ve abonelikler arasında taşıma Şu anda desteklenmemektedir.
- Müşteri tarafından yönetilen anahtarlarla zaten şifrelenmiş bir kurtarma hizmetleri kasasını yeni bir kiracıya taşıdığınızda, kasasının yönetilen kimliğini ve CMK 'yi yeniden oluşturmak ve yeniden yapılandırmak için kurtarma hizmetleri kasasını güncelleştirmeniz gerekir (yeni kiracıda olması gerekir). Bu yapılmazsa, yedekleme ve geri yükleme işlemleri başarısız olur. Ayrıca, abonelik içinde ayarlanmış tüm rol tabanlı erişim denetimi (RBAC) izinlerinin yeniden yapılandırılması gerekir.

- Bu özellik Azure portal ve PowerShell aracılığıyla yapılandırılabilir.

    >[!NOTE]
    >Kurtarma Hizmetleri kasasındaki yedeklemeler için müşteri tarafından yönetilen anahtarları kullanmak için az Module 5.3.0 veya üstünü kullanın.
    
    >[!Warning]
    >Yedekleme için şifreleme anahtarlarını yönetmek üzere PowerShell kullanıyorsanız, bu anahtarları portaldan güncelleştirmeyi önermiyoruz.<br>Anahtarı portaldan güncelleştirirseniz, yeni modeli desteklemek için bir PowerShell güncelleştirmesi kullanılabilir olduğunda, şifreleme anahtarını daha fazla güncelleştirmek için PowerShell 'i kullanamazsınız. Ancak, Azure portal anahtarı güncelleştirmeye devam edebilirsiniz.

Kurtarma Hizmetleri kasanızı oluşturup yapılandırmadıysanız, [nasıl yapılacağını burada](backup-create-rs-vault.md)bulabilirsiniz.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları kullanarak şifrelemek için bir kasa yapılandırma

Bu bölüm aşağıdaki adımları içerir:

1. Kurtarma Hizmetleri kasanızda yönetilen kimliği etkinleştirin

1. Azure Key Vault şifreleme anahtarına erişmek için kasaya izin atayın

1. Azure Key Vault geçici silme ve Temizleme korumasını etkinleştirme

1. Şifreleme anahtarını kurtarma hizmetleri kasasına atama

Bu adımların, istenen sonuçlara ulaşmak için yukarıda bahsedilen sırayla izlenmesi gerekir. Her adım aşağıda ayrıntılı olarak ele alınmıştır.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Kurtarma Hizmetleri kasanızda yönetilen kimliği etkinleştirin

Azure Backup, Azure Key Vault depolanan şifreleme anahtarlarına erişmek üzere Kurtarma Hizmetleri kasasının kimliğini doğrulamak için sistem tarafından atanan Yönetilen kimlikler ve Kullanıcı tarafından atanan Yönetilen kimlikler kullanır. Kurtarma Hizmetleri kasanızda yönetilen kimliği etkinleştirmek için aşağıda bahsedilen adımları izleyin.

>[!NOTE]
>Etkinleştirildikten sonra, yönetilen kimlik devre dışı **bırakılmamalıdır (** geçici olarak bile). Yönetilen kimliğin devre dışı bırakılması tutarsız davranışa yol açabilir.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>Kasa için sistem tarafından atanan yönetilen kimliği etkinleştirme

**Portalda:**

1. Kurtarma Hizmetleri kasasına gidin-> **kimliği**

    ![Kimlik ayarları](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. **Sistem atandı** sekmesine gidin.

1. **Durumu** **Açık** olarak değiştirin.

1. Kasa kimliğini etkinleştirmek için **Kaydet** ' e tıklayın.

Kasadaki sistem tarafından atanan yönetilen kimlik olan bir nesne KIMLIĞI oluşturulur.

>[!NOTE]
>Etkinleştirildikten sonra, yönetilen kimlik devre dışı bırakılmamalıdır (geçici olarak bile). Yönetilen kimliğin devre dışı bırakılması tutarsız davranışa yol açabilir.


**PowerShell ile:**

Kurtarma Hizmetleri Kasası için sistem tarafından atanan yönetilen kimliği etkinleştirmek üzere [Update-Azrecoveryserviceskasa](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) komutunu kullanın.

Örnek:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Çıkış:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault"></a>Kasaya Kullanıcı tarafından atanan yönetilen kimlik atama

Kurtarma Hizmetleri kasanızın Kullanıcı tarafından atanan yönetilen kimliğini atamak için aşağıdaki adımları uygulayın:

1.  Kurtarma Hizmetleri kasasına gidin-> **kimliği**

    ![Kasaya Kullanıcı tarafından atanan yönetilen kimlik atama](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  **Kullanıcı atandı** sekmesine gidin.

1.  Kullanıcı tarafından atanan yönetilen kimlik eklemek için **+ Ekle** ' ye tıklayın.

1.  Açılan **Kullanıcı tarafından atanan yönetilen kimlik Ekle** dikey penceresinde, kimliğinize yönelik aboneliği seçin.

1.  Listeden kimliği seçin. Ayrıca, kimliğin veya kaynak grubunun adına göre de filtre uygulayabilirsiniz.

1.  İşiniz bittiğinde, kimlik atamaya son vermek için **Ekle** ' ye tıklayın.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Azure Key Vault şifreleme anahtarına erişmek için kurtarma hizmetleri kasasına izinler atayın

>[!Note]
>Kullanıcı tarafından atanan kimlikler kullanıyorsanız, Kullanıcı tarafından atanan kimliğe aynı izinlerin atanması gerekir.

Artık kurtarma hizmetleri kasasının şifreleme anahtarını içeren Azure Key Vault erişmesine izin vermeniz gerekir. Bu, kurtarma hizmetleri kasasının yönetilen kimliğinin Key Vault erişmesine izin verilerek yapılır.

**Portalda**:

1. Azure Key Vault > **erişim ilkelerine** gidin. **+ Erişim Ilkeleri Ekle**' ye geçin.

    ![Erişim Ilkeleri ekleme](./media/encryption-at-rest-with-cmk/access-policies.png)

1. **Anahtar izinleri** altında **Al**, **Listele**, **anahtar kaydırmayı kaldır** ve **anahtarı sarmalama** işlemlerini seçin. Bu, anahtardaki, izin verilecek eylemleri belirtir.

    ![Anahtar izinleri atama](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. **Sorumlu Seç** ' e gidin ve ad veya yönetilen kimlik ' i kullanarak arama kutusunda kasanızı arayın. Bir kez daha olduktan sonra kasayı seçin ve bölmenin en altında bulunan **Seç** ' i seçin.

    ![Sorumlu seçin](./media/encryption-at-rest-with-cmk/select-principal.png)

1. İşiniz bittiğinde yeni erişim ilkesini eklemek için **Ekle** ' yi seçin.

1. Azure Key Vault erişim ilkesinde yapılan değişiklikleri kaydetmek için **Kaydet** ' i seçin.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Azure Key Vault geçici silme ve Temizleme korumasını etkinleştirme

Şifreleme anahtarınızı depolayan Azure Key Vault **, geçici silme ve Temizleme korumasını etkinleştirmeniz** gerekir. Bunu aşağıda gösterildiği gibi Azure Key Vault kullanıcı arabiriminden yapabilirsiniz. (Alternatif olarak, Key Vault oluşturulurken bu özellikler ayarlanabilir). Bu [Key Vault özellikleri hakkında](../key-vault/general/soft-delete-overview.md)daha fazla bilgi edinin.

![Geçici silmeyi ve temizleme korumasını etkinleştirme](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

Aşağıdaki adımları kullanarak, PowerShell aracılığıyla geçici silme ve Temizleme korumasını de etkinleştirebilirsiniz:

1. Azure hesabınızda oturum açın.

    ```azurepowershell
    Login-AzAccount
    ```

1. Kasanızı içeren aboneliği seçin.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Geçici silmeyi etkinleştirme

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Temizleme korumasını etkinleştir

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>RS kasasına şifreleme anahtarı atama

>[!NOTE]
> Devam etmeden önce aşağıdakilerden emin olun:
>
> - Yukarıda belirtilen tüm adımlar başarıyla tamamlandı:
>   - Kurtarma Hizmetleri kasasının yönetilen kimliği etkinleştirildi ve gerekli izinler atandı
>   - Azure Key Vault, geçici silme ve Temizleme koruması etkin
> - CMK şifrelemesini etkinleştirmek istediğiniz kurtarma hizmetleri kasasında korunan veya kendisine kayıtlı **öğe yok**

Yukarıdaki bir kez başarılı olduktan sonra, kasanızın şifreleme anahtarını seçmeye devam edin.

### <a name="to-assign-the-key-in-the-portal"></a>Portala anahtar atamak için

1. Kurtarma Hizmetleri kasanıza gidin-> **özellikleri**

    ![Şifreleme ayarları](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. **Şifreleme ayarları** altında **Güncelleştir** ' i seçin.

1. Şifreleme ayarları bölmesinde, **kendi anahtarınızı kullan** ' ı seçin ve aşağıdaki yollarla anahtarı belirtmek için devam edin. **Kullanmak istediğiniz anahtarın etkin durumda olan bir RSA 2048 anahtarı olduğundan emin olun.**

    1. Bu kurtarma hizmetleri kasasındaki verileri şifrelemek istediğiniz **anahtar URI** 'sini girin. Ayrıca, Azure Key Vault (Bu anahtarı içeren) olduğu aboneliği de belirtmeniz gerekir. Bu anahtar URI, Azure Key Vault ilgili anahtardan elde edilebilir. Anahtar URI 'sinin doğru şekilde kopyalandığından emin olun. Anahtar tanımlayıcısıyla birlikte sunulan **Panoya Kopyala** düğmesini kullanmanız önerilir.

        >[!NOTE]
        >Anahtar URI 'sini kullanarak şifreleme anahtarını belirtirken, anahtar otomatik olarak döndürülmeyecektir. Bu nedenle, gerekli olduğunda yeni anahtar belirtilerek önemli güncelleştirmelerin el ile yapılması gerekir.

        ![Anahtar URI 'SI girin](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Anahtar Seçicisi bölmesindeki Key Vault tuşuna gidip seçin.

        >[!NOTE]
        >Anahtar Seçicisi bölmesini kullanarak şifreleme anahtarını belirtirken, anahtar için her yeni sürüm etkinleştirildiğinde anahtar otomatik olarak döndürülür. Şifreleme anahtarlarının otomatik döndürmesini etkinleştirme hakkında [daha fazla bilgi edinin](#enabling-auto-rotation-of-encryption-keys) .

        ![Anahtar kasasından anahtar seçin](./media/encryption-at-rest-with-cmk/key-vault.png)

1. **Kaydet**’i seçin.

1. **Şifreleme anahtarı güncelleştirmesinin ilerlemesini ve durumunu izleme**: sol gezinti çubuğundaki **yedekleme işleri** görünümünü kullanarak şifreleme anahtarı atamasının ilerlemesini ve durumunu izleyebilirsiniz. Durum yakında **tamamlandı** olarak değişir. Artık kasanız, belirtilen anahtara sahip tüm verileri KEK olarak şifreler.

    ![Durum tamamlandı](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    Şifreleme anahtarı güncelleştirmeleri kasanın etkinlik günlüğüne de kaydedilir.

    ![Etkinlik günlüğü](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>Anahtarı PowerShell ile atamak için

Müşteri tarafından yönetilen anahtarlar kullanılarak şifrelemeyi etkinleştirmek ve kullanılacak şifreleme anahtarını atamak veya güncelleştirmek için [set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) komutunu kullanın.

Örnek:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Çıkış:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Bu işlem, şifreleme anahtarını güncelleştirmek veya değiştirmek istediğinizde aynı kalır. Başka bir Key Vault (kullanılmakta olan birinden farklı) bir anahtar güncelleştirmek ve kullanmak istiyorsanız şunlardan emin olun:
>
> - Anahtar Kasası, kurtarma hizmetleri kasasıyla aynı bölgede bulunur
>
> - Anahtar kasasında geçici silme ve Temizleme koruması etkin
>
> - Kurtarma Hizmetleri Kasası, anahtar kasasına erişmek için gerekli izinlere sahiptir.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla şifrelenen bir kasaya yedekleme

Korumayı yapılandırmaya devam etmeden önce aşağıdaki denetim listesinin öğesine bağlı olduğundan emin olmanızı önemle öneririz. Bu, bir öğe, CMK olmayan şifreli bir kasaya yedeklenmek üzere yapılandırıldıktan (veya yapılandırılmakta denenmeye denendiğinde), müşteri tarafından yönetilen anahtarları kullanarak şifreleme üzerinde etkinleştirilemez ve platform tarafından yönetilen anahtarlar kullanılmaya devam eder.

>[!IMPORTANT]
> Korumayı yapılandırmaya devam etmeden önce aşağıdaki adımları **başarıyla** tamamlamış olmanız gerekir:
>
>1. Yedekleme kasanızı oluşturma
>1. Kurtarma Hizmetleri kasasının sisteme atanan yönetilen kimliği etkin veya kasaya Kullanıcı tarafından atanan bir yönetilen kimlik atandı
>1. Key Vault şifreleme anahtarlarına erişmek için yedekleme kasanıza (veya Kullanıcı tarafından atanan yönetilen kimliğe) atanan izinler
>1. Key Vault için geçici silme ve Temizleme koruması etkinleştirildi
>1. Yedekleme kasanıza yönelik geçerli bir şifreleme anahtarı atandı
>
>Yukarıdaki adımların tümü onaylanırsa, yalnızca yedeklemeyi yapılandırmaya devam edin.

Müşteri tarafından yönetilen anahtarlarla şifrelenen bir kurtarma hizmetleri kasasındaki yedeklemeleri yapılandırma ve gerçekleştirme işlemi, bu **deneyim üzerinde değişiklik** yapmadan, platform tarafından yönetilen anahtarlar kullanan bir kasaya benzer. Bu, [Azure VM 'lerinin yedeklenmesi](./quick-backup-vm-portal.md) ve bir VM içinde çalışan iş yüklerinin yedeklenmesi için (örneğin, [SAP HANA](./tutorial-backup-sap-hana-db.md), [SQL Server](./tutorial-sql-backup.md) veritabanları) doğru bir durum içerir.

## <a name="restoring-data-from-backup"></a>Verileri yedeklemeden geri yükleme

### <a name="vm-backup"></a>VM yedeklemesi

Kurtarma Hizmetleri kasasında depolanan veriler, [burada](./backup-azure-arm-restore-vms.md)açıklanan adımlara göre geri yüklenebilir. Müşteri tarafından yönetilen anahtarlar kullanılarak şifrelenen bir kurtarma hizmetleri kasasından geri yükleme yaparken, geri yüklenen verileri bir disk şifreleme kümesiyle (DES) şifrelemeyi seçebilirsiniz.

#### <a name="restoring-vm--disk"></a>VM/disk geri yükleniyor

1. Disk/VM 'yi bir "Snapshot" kurtarma noktasından kurtarırken, geri yüklenen veriler, kaynak VM 'nin disklerini şifrelemek için kullanılan DES ile şifrelenir.

1. Kurtarma türü olan bir kurtarma noktasından disk/VM 'yi "kasa" olarak geri yüklerken, geri yüklenen verilerin geri yükleme sırasında belirtilen DES kullanarak şifrelenmesini seçebilirsiniz. Alternatif olarak, DES belirtmeden verileri geri yükleme işlemine devam etmeyi seçebilirsiniz ve bu durumda Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir.

Geri yükleme işlemi tamamlandıktan sonra geri yüklenen diski/VM 'yi, geri yükleme işlemi sırasında yapılan seçimden bağımsız olarak şifreleyebilirsiniz.

![Geri yükleme noktaları](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Kasa kurtarma noktasından geri yüklerken bir disk şifreleme kümesi seçin

**Portalda**:

Disk şifreleme kümesi, aşağıda gösterildiği gibi geri yükleme bölmesindeki şifreleme ayarları altında belirtilir:

1. **Anahtarınızı kullanarak disk (ler) i şifreleyin**, **Evet**' i seçin.

1. Açılan menüden, geri yüklenen diskler için kullanmak istediğiniz DES ' i seçin. **DES 'e erişiminizin olduğundan emin olun.**

>[!NOTE]
>Azure disk şifrelemesi kullanan bir VM 'yi geri yüklüyorsanız, geri yükleme sırasında DES seçme özelliği kullanılamaz.

![Anahtarınızı kullanarak diski şifreleyin](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**PowerShell ile**:

[](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) `-DiskEncryptionSetId <string>` Geri yüklenen diski ŞIFRELEMEK için kullanılacak [des 'i belirtmek](/powershell/module/az.compute/get-azdiskencryptionset) Için, [] parametresiyle Get-azrecoveryservicesbackupıtem komutunu kullanın. Diskleri VM yedeklemesinden geri yükleme hakkında daha fazla bilgi için [Bu makaleye](./backup-azure-vms-automation.md#restore-an-azure-vm)bakın.

Örnek:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Dosyalar geri yükleniyor

Dosya geri yükleme gerçekleştirirken, geri yüklenen veriler hedef konumu şifrelemek için kullanılan anahtarla şifrelenir.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Azure VM 'lerinde SAP HANA/SQL veritabanlarını geri yükleme

Azure VM 'de çalışan yedeklenen SAP HANA/SQL veritabanından geri yükleme yaparken, geri yüklenen veriler, hedef depolama konumunda kullanılan şifreleme anahtarı kullanılarak şifrelenir. Bu, sanal makinenin disklerini şifrelemek için kullanılan, müşteri tarafından yönetilen bir anahtar veya platform tarafından yönetilen bir anahtar olabilir.

## <a name="additional-topics"></a>Ek konu başlıkları

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>Kasa oluşturma sırasında müşteri tarafından yönetilen anahtarları kullanarak şifrelemeyi etkinleştirin (önizlemede)

>[!NOTE]
>Müşteri tarafından yönetilen anahtarları kullanarak kasadaki şifrelemeyi etkinleştirmek sınırlı genel önizlemeye sunuldu ve aboneliklerin listelenmesi için izin vermeniz gerekiyor. Önizlemeye kaydolmak için [formu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) doldurup bizimle yazın [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

Aboneliğiniz izin verdiğinde, **yedekleme şifreleme** sekmesi görüntülenir. Bu, yeni bir kurtarma hizmetleri kasasının oluşturulması sırasında, müşteri tarafından yönetilen anahtarları kullanarak yedeklemede şifrelemeyi etkinleştirmenizi sağlar. Şifrelemeyi etkinleştirmek için aşağıdaki adımları uygulayın:

1. **Temel bilgiler** sekmesinin yanındaki **Yedekleme Şifrelemesi** sekmesinde şifreleme anahtarını ve şifreleme için kullanılacak kimliği belirtin.

   ![Kasa düzeyinde şifrelemeyi etkinleştir](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >Ayarlar yalnızca yedekleme için geçerlidir ve isteğe bağlıdır.

1. Şifreleme türü olarak **müşteri tarafından yönetilen anahtarı kullan** ' ı seçin.

1. Şifreleme için kullanılacak anahtarı belirtmek için uygun seçeneği belirleyin.

   Şifreleme anahtarı için URI sağlayabilir veya anahtara gözatıp seçimi yapabilirsiniz. **Key Vault Seç** seçeneğini kullanarak anahtarı belirttiğinizde, şifreleme anahtarının otomatik dönüşü otomatik olarak etkinleştirilir. [Otomatik döndürme hakkında daha fazla bilgi edinin](#enabling-auto-rotation-of-encryption-keys). 

1. Müşteri tarafından yönetilen anahtarlarla şifrelemeyi yönetmek için Kullanıcı tarafından atanan yönetilen kimliği belirtin. Gitmek için **Seç** ' e tıklayın ve gerekli kimliği seçin.

1. İşiniz bittiğinde, etiketler ekleme (isteğe bağlı) ve kasayı oluşturmaya devam edin.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>Şifreleme anahtarlarının otomatik döndürmesini etkinleştirme

Yedeklemeleri şifrelemek için kullanılması gereken müşteri tarafından yönetilen anahtarı belirttiğinizde, belirtmek için aşağıdaki yöntemleri kullanın:

- Anahtar URI 'sini girin
- Key Vault arasından seçim yapın

**Key Vault Seç** seçeneğinin kullanılması seçili anahtar için Otomatik döndürmeyi etkinleştirmeye yardımcı olur. Bu, sonraki sürüme güncelleştirme için el ile çaba ortadan kaldırır. Ancak, bu seçeneği kullanarak:
- Anahtar sürüm güncelleştirmesinin etkili olması bir saate kadar sürebilir.
- Anahtarın yeni bir sürümü geçerli olduğunda, anahtar güncelleştirmesi geçerli olduktan sonra en az bir sonraki yedekleme işi için eski sürüm de kullanılabilir (etkin durumda).

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>Müşteri tarafından yönetilen anahtarlar kullanılarak şifrelemeyi denetleme ve zorunlu tutma için Azure Ilkelerini kullanma (önizlemede)

Azure Backup, kurtarma hizmetleri kasasındaki verilerin müşteri tarafından yönetilen anahtarlarını kullanarak şifrelemeyi denetlemek ve zorlamak için Azure Ilkeleri kullanmanıza olanak sağlar. Azure Ilkelerini kullanarak:

- Denetim ilkesi, 04/01/2021 sonrasında etkinleştirilen müşteri tarafından yönetilen anahtarları kullanarak kasaların denetlenmesi için kullanılabilir. Bu tarihten önce CMK Şifrelemesi etkin olan kasaların, ilke uygulanamayacak veya hatalı negatif sonuçları gösterebilir (yani, **CMK şifrelemesi** etkin olsa da bu kasalar uyumsuz olarak bildirilebilir).
- 04/01/2021 öncesinde **CMK şifrelemesi** etkin olan denetim kasalarına yönelik denetim ilkesini kullanmak için, bir şifreleme anahtarını güncelleştirmek üzere Azure Portal kullanın. Bu, yeni modele yükseltmeye yardımcı olur. Şifreleme anahtarını değiştirmek istemiyorsanız, anahtar URI 'SI veya anahtar seçim seçeneği ile aynı anahtarı yeniden sağlayın. 

   >[!Warning]
    >Yedekleme için şifreleme anahtarlarını yönetmek üzere PowerShell kullanıyorsanız, bu anahtarları portaldan güncelleştirmeyi önermiyoruz.<br>Anahtarı portaldan güncelleştirirseniz, yeni modeli desteklemek için bir PowerShell güncelleştirmesi kullanılabilir olduğunda, şifreleme anahtarını daha fazla güncelleştirmek için PowerShell 'i kullanamazsınız. Ancak, Azure portal anahtarı güncelleştirmeye devam edebilirsiniz.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Mevcut bir yedekleme kasasını, müşteri tarafından yönetilen anahtarlarla şifreleyebilir miyim?

Hayır, CMK şifreleme yalnızca yeni kasaların etkinleştirilebilmesi için etkinleştirilebilir. Bu nedenle kasada hiçbir öğe korunmamış olmalıdır. Aslında, müşteri tarafından yönetilen anahtarlar kullanılarak Şifrelemeyi etkinleştirmeden önce hiçbir öğenin kasaya hiçbir şekilde korunması gerekir.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Bir öğeyi kasayma korumaya çalıştım, ancak başarısız oldu ve kasa hala onunla korunan herhangi bir öğe içermiyor. Bu kasa için CMK şifrelemesini etkinleştirebilir miyim?

Hayır, kasadaki herhangi bir öğeyi geçmişteki bir şekilde korumak için hiçbir girişimde bulunulmamalıdır.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>CMK şifrelemesini kullanan bir kasam var. Kasaya korunan yedekleme öğeleri olsa bile daha sonra platforma yönetilen anahtarlar kullanılarak şifrelemeye dönebilir miyim?

Hayır, CMK şifrelemesini etkinleştirdikten sonra platform tarafından yönetilen anahtarlar kullanılmak üzere geri döndürülemez. Gereksinimlerinize göre kullanılan anahtarları değiştirebilirsiniz.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Azure Backup için CMK şifrelemesi de Azure Site Recovery için geçerlidir.

Hayır, bu makalede yalnızca yedekleme verilerinin şifrelenmesi anlatılmaktadır. Azure Site Recovery için, özelliği hizmetten kullanılabilir şekilde ayrı olarak ayarlamanız gerekir.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Bu makaledeki adımlardan birini kaçırdım ve veri kaynağınızı korudum. CMK şifrelemesini kullanmaya devam edebilir miyim?

Makaledeki adımları takip etmez ve öğeleri korumaya devam etmek, kasada müşteri tarafından yönetilen anahtarlar kullanılarak şifrelemeyi kullanmamıza yol açabilir. Bu nedenle, öğeleri korumaya devam etmeden önce [Bu denetim listesine](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) başvurmanız önerilir.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>CMK-Encryption kullanarak yedeklemelerimin maliyetine eklensin mi?

Yedekleme için CMK şifrelemesini kullanmak size ek ücret vermez. Ancak, anahtarınızın depolandığı Azure Key Vault kullanmaya yönelik maliyetlerle devam edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Backup güvenlik özelliklerine genel bakış](security-overview.md)
