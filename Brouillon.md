```mermaid
flowchart LR
    subgraph Local
    Ordinateur[Ordinateur] -.-|SSH : IP publique| Box{Box}
    end
    Box -.-|SSH| Internet[Fournisseur <br /> internet]
    Internet -.-|SSH : IP publique <br /> ETH1 : | VMAdmin[VM Admin Linux <br /> Firewall <br /> 1 port <br /> 2 cartes réseau]
    subgraph Azure - 10.0.4.0/24
    VMAdmin ---|SSH  <br /> ETH0 : 10.0.4.1| VN((Virtual Network privé <br /> ETH0))
    VN ---|SSH  <br /> ETH0 : 10.0.4.2| BDD[VM BDD Linux <br /> Carte réseau]
    VN ---|SSH  <br />  ETH0: IP 10.0.4.3| Appli[VM Application Linux <br /> Carte réseau]
    end
```
