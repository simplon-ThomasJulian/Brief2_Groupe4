```mermaid
flowchart LR
    subgraph Local
    Ordinateur[Ordinateur] -.-|SSH : IP publique| Box{Box}
    end
    Box -.-|SSH| Internet[Fournisseur <br /> internet]
    Internet -.-|SSH : IP publique| VMAdmin[VM Admin Linux <br /> Firewall <br /> 1 port <br /> 2 cartes réseau]
    subgraph Azure - 10.0.4.0/24
    VMAdmin ---|SSH : IP privée| VN((Virtual Network privé))
    VN ---|SSH : IP privée| BDD[VM BDD Linux <br /> Carte réseau]
    VN ---|SSH : IP privée| Appli[VM Application Linux <br /> Carte réseau]
    end
```
