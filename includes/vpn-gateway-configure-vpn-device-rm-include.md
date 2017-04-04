Bir şirket içi ağı ile Siteden Siteye bağlantılar için VPN cihazı gerekir. Azure ile çalışabilecek çok sayıda farklı VPN cihazı vardır. VPN cihazları ve yapılandırma ayarları hakkında daha fazla bilgi için bkz. [VPN Cihazları](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). VPN cihazınızı yapılandırmadan önce, kullanmak istediğiniz VPN cihazının [Bilinen cihaz uyumluluğu sorunları](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) olup olmadığını denetleyin. VPN cihazı yapılandırmayla ilgili belirli bilgiler için cihazınızın üreticisine danışın.

VPN cihazınızı yapılandırırken aşağıdaki öğeler gerekir:

- Sanal ağ geçidinizin **Genel IP adresi**.

    -  Azure portalını kullanarak Genel IP adresini bulmak için **Sanal ağ geçitleri**’ne gidin ve ağ geçidinizin adına tıklayın. 

    - PowerShell kullanarak sanal ağ geçidinizin Genel IP adresini bulmak için, değerleri kendi değerlerinizle değiştirerek aşağıdaki örneği kullanın.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **Paylaşılan bir anahtar**. Siteden Siteye VPN bağlantınızı oluştururken belirteceğiniz paylaşılan anahtarın aynısıdır. Bu örneklerde çok temel bir paylaşılan anahtar kullanılır. Kullanmak için daha karmaşık bir anahtar oluşturmanız gerekir.




