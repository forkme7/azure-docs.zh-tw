---
title: 建立公用負載平衡器 - PowerShell | Microsoft Docs
description: 了解如何使用 PowerShell 在資源管理員中建立公用負載平衡器
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4ce11b0b06e1feaf55d17e25500c43a7eb1bf3d5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="get-started"></a>使用 PowerShell 在資源管理員中建立公用負載平衡器

> [!div class="op_single_selector"]
> * [入口網站](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [範本](../load-balancer/load-balancer-get-started-internet-arm-template.md)



[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>使用 Azure PowerShell 部署方案

下列程序說明如何搭配使用 Azure Resource Manager 與 PowerShell 建立公用負載平衡器。 使用 Azure Resource Manager 時，會個別建立並設定每項資源，然後放在一起來建立負載平衡器。

您必須建立並設定下列物件，才能部署負載平衡器：

* 前端 IP 組態：包含傳入網路流量的公用 IP (PIP) 位址。
* 後端位址集區：包含虛擬機器的網路介面 (NIC)，可從負載平衡器接收網路流量。
* 負載平衡規則：包含將負載平衡器上的公用連接埠對應至後端位址集區中的連接埠的規則。
* 輸入 NAT 規則：包含將負載平衡器上的公用連接埠對應至後端位址集區中特定虛擬機器之連接埠的規則。
* 探查：包含用來檢查後端位址集區中虛擬機器執行個體可用性的健全狀態探查。

如需詳細資料，請參閱 [Azure Resource Manager 的負載平衡器支援](load-balancer-arm.md)。

## <a name="set-up-powershell-to-use-resource-manager"></a>設定 PowerShell 以使用 Resource Manager

請確定您擁有適用於 PowerShell 之 Azure Resource Manager 模組的最新生產版本：

1. 登入 Azure。

    ```powershell
    Login-AzureRmAccount
    ```

    出現提示時，請輸入您的認證。

2. 檢查帳戶的訂用帳戶。

    ```powershell
    Get-AzureRmSubscription
    ```

3. 選擇其中一個要使用的 Azure 訂用帳戶。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. 建立資源群組。 (如果您使用現有的資源群組，請略過此步驟。)

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>建立前端 IP 集區的虛擬網路和公用 IP 位址

1. 建立子網路和虛擬網路。

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. 建立名為 **PublicIP** 的 Azure 公用 IP 位址資源，讓具有 DNS 名稱 **loadbalancernrp.westus.cloudapp.azure.com** 的前端 IP 集區使用。下列命令會使用靜態配置類型。

    ```powershell
    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -DomainNameLabel loadbalancernrp
    ```

   > [!IMPORTANT]
   > 負載平衡器會使用公用 IP 的網域標籤做為其 FQDN 的前置詞。 這與傳統部署模型不同，其使用雲端服務作為負載平衡器 FQDN。
   > 在此範例中，FQDN 是 **loadbalancernrp.westus.cloudapp.azure.com**。

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>建立前端 IP 集區與後端位址集區

1. 建立名為 **LB-Frontend** 的前端 IP 集區，它使用 **PublicIp** 資源。

    ```powershell
    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
    ```

2. 建立名為 **LB-backend**的後端位址集區。

    ```powershell
    $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend
    ```

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>建立 NAT 規則、負載平衡器規則、探查及負載平衡器

此範例會建立下列項目：

* NAT 規則，將連接埠 3441 上的所有傳入流量轉譯至連接埠 3389
* NAT 規則，將連接埠 3442 上的所有傳入流量轉譯至連接埠 3389
* 一個可在名為 **HealthProbe.aspx**
* 負載平衡器規則，將連接埠 80 上的所有傳入流量，負載平衡至後端集區中位址的連接埠 80
* 使用所有上述物件的負載平衡器

使用這些步驟：

1. 建立 NAT 規則。

    ```powershell
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    ```

2. 建立健全狀況探查。 有兩種方式可以設定探查：

    HTTP 探查

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    TCP 探查

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

3. 建立負載平衡器規則。

    ```powershell
    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

4. 使用先前建立的物件來建立負載平衡器。

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

## <a name="create-nics"></a>建立 NIC

建立網路介面 (或修改現有的)，然後將它們關聯至 NAT 規則、負載平衡器規則和探查：

1. 取得需要在其中建立 NIC 的虛擬網路和虛擬網路子網路。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. 建立名為 **lb-nic1-be**的 NIC，並將它與第一個 NAT 規則以及第一個 (唯一) 後端位址集區產生關聯。

    ```powershell
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
    ```

3. 建立名為 **lb-nic2-be**的 NIC，並將它與第二個 NAT 規則以及第一個 (唯一) 後端位址集區產生關聯。

    ```powershell
    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
    ```

4. 檢查 NIC。

        $backendnic1

    預期的輸出：

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. 使用 `Add-AzureRmVMNetworkInterface` Cmdlet 以將 NIC 指派給不同的 VM。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

如需建立虛擬機器和指定 NIC 的指引，請參閱[使用 PowerShell 建立 Azure VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)。

## <a name="add-the-network-interface-to-the-load-balancer"></a>將網路介面新增至負載平衡器

1. 從 Azure 擷取負載平衡器。

    將負載平衡器資源載入到變數之中 (如果您還沒這麼做)。 此變數稱為 **$lb**。使用來自稍早建立之負載平衡器資源的相同名稱。

    ```powershell
    $lb= get-azurermloadbalancer -name NRP-LB -resourcegroupname NRP-RG
    ```

2. 將後端組態載入到變數之中。

    ```powershell
    $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
    ```

3. 將已建立的網路介面載入到變數之中。 變數名稱是 **$nic**。 網路介面名稱是來自先前範例的相同名稱。

    ```powershell
    $nic =get-azurermnetworkinterface -name lb-nic1-be -resourcegroupname NRP-RG
    ```

4. 變更網路介面上的後端組態。

    ```powershell
    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
    ```

5. 儲存網路介面物件。

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```

    網路介面在新增至負載平衡器的後端集區後，便會開始根據該負載平衡器資源的負載平衡規則接收網路流量。

## <a name="update-an-existing-load-balancer"></a>更新現有負載平衡器

1. 使用先前範例中的負載平衡器，透過 `Get-AzureLoadBalancer` 將負載平衡器物件指派給變數 **$slb**。

    ```powershell
    $slb = get-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
    ```

2. 在下列範例中，您會使用前端集區的連接埠 81 和後端集區的連接埠 8181，將輸入 NAT 規則加入現有的負載平衡器。

    ```powershell
    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
    ```

3. 使用 `Set-AzureLoadBalancer`儲存新的組態。

    ```powershell
    $slb | Set-AzureRmLoadBalancer
    ```

## <a name="remove-a-load-balancer"></a>移除負載平衡器

使用命令 `Remove-AzureLoadBalancer` 刪除資源群組 **NRP-RG** 中先前建立的負載平衡器 **NRP-LB**。

```powershell
Remove-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> 您可以使用選擇性參數 **-Force** 以避免出現刪除提示。

## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
