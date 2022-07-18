# Brief 2 : Déploiement manuel d’une application

## **Plan d'action du projet**

## 1. Plan réseau

```mermaid
flowchart TD
    subgraph Local
    Ordinateur[Ordinateur] -.-|SSH : IP publique| Box{Box}
    end
    Box -.-|SSH| Internet[Fournisseur <br /> internet]
    Internet -.-|SSH <br /> ETH1 :20.118.187.209 | VMAdmin[VM Admin Linux <br /> Firewall <br /> 1 port <br /> 2 cartes réseau]
    subgraph Azure - 10.0.4.0/24
    VMAdmin ---|SSH  <br /> ETH0 : 10.0.4.4| VN((Virtual Network privé))
    VN ---|SSH  <br /> ETH0 : 10.0.4.5| BDD[VM BDD Linux]
    VN ---|SSH  <br />  ETH0 : 10.0.4.6| Appli[VM Application Linux]
    end
    Appli --- |ETH1 :20.118.128.190 <br /> https://nextg4.westus3.cloudapp.azure.com:8080/nextcloud| Services[Services applicatifs]
```

## 2. Ressources

### Liste de ressources

- Réseau virtuel Vnet avec plage d'adresses en 10.0.4.0/24
- Interfaces réseau possédant une IP publique pour accéder via internet à la VM administrative (accessible via une clé rsa uniquement) et VM applicative (paramétrée uniquement pour la partie service)
- Trois autres interfaces réseau en IP privée pour permettre l'échange sur le réseau privé entre les trois VMs
- VM Administrative
- VM BDD
- VM Applicative
- 3 disques de stockage choisis en fonction des machines    

### VM Admin Linux
**Nom de la VM :** <mark>VM-Admin-groupe4</mark>

**Groupe de ressources** : Brief_2_Groupe_4  
**Système d'exploitation** : Linux (Debian 11)  
**Emplacement** : West US  
**Adresse IP privée** : 10.0.4.4  
**Adresse IP publique** : 20.118.187.209  
Matériel : 
Processeurs virtuels: 1

                       Intel(R) Xeon(R) Platinum 8370C CPU @ 2.80GHz


**RAM** 3,5 Giga  
**Système d'exploitation** : <mark>Debian 11</mark>  
**vmId:** 25264c78-b9e1-4520-b7ee-7ca32cc8ddae  

**Adresse locale des cartes de la vm**:  
ETH0   inet 10.0.4.4   
Mask: 255.255.255.0  


**Connection sécurisé à la VM**
- [x] SSH
- [ ] RDP

|Nom du disque | Type de stockage | Taille (Gio) | IOPS max. | Débit max. (Mbits/s) | Chiffrement | Mise en cache de l'hôte|
|---|---|---|---|---|---|---|
|VM-Admin-groupe4_OsDisk_1_6ca4f8e0ce0e42799071f7456e46b343| HDD Standard LRS | 30 | 500 | 60 | SSE avec PMK | Lecture/Ecriture |
|VM-Admin-groupe4_DataDisk_03| HDD Standard LRS | 32 | 500 | 60 | SSE avec PMK | Lecture/Ecriture |

### VM Applicative Linux
**Nom de la VM :** <mark>VM-App-Groupe4</mark>

**Groupe de ressources** : Brief_2_Groupe_4  
**Système d'exploitation** : Linux (debian 11)  
**Emplacement** : West US  
**Adresse IP privée** : 10.0.4.6  
**Adresse IP pubique** : 20.118.128.190
Matériel : 
Processeurs virtuels: 2

                       Intel(R) Xeon(R) Platinum 8272CL CPU @ 2.60GHz, 2593 MHz

**RAM** 8 Giga  
**Système d'exploitation** : <mark>Debian-11</mark>  
**vmId:** 25264c78-b9e1-4520-b7ee-7ca32cc8ddae  

**Adresse locale des cartes de la vm**:  
ETH0   inet 10.0.4.6   
Mask: 255.255.255.0  


**Connection sécurisé à la VM**
- [x] SSH
- [ ] RDP

|Nom du disque | Type de stockage | Taille (Gio) | IOPS max. | Débit max. (Mbits/s) | Chiffrement | Mise en cache de l'hôte|
|---|---|---|---|---|---|---|
|VM-App-groupe-4_DataDisk_0 | HDD Standard LRS | 1 024| 500 | 60 | SSE avec PMK | Lecture/Ecriture |
|VM-App-groupe-4_OsDisk_1_33f257959bf04ad7b2c138f0b48e4456| HDD Standard LRS | 30| 500 | 60 | SSE avec PMK | Lecture/Ecriture |


### VM Base de donnée Linux
**Nom de la VM :** <mark>VM-BDD-Groupe4</mark>

**Groupe de ressources** : Brief_2_Groupe_4  
**Système d'exploitation** : Linux (Debian 11)  
**Emplacement** : West US  
**Adresse IP privée** : 10.0.4.5
**Adresse IP publique** :  
Matériel : 
Processeurs virtuels: 2

                       Intel(R) Xeon(R) Platinum 8272CL CPU @ 2.60GHz, 2593 MHz

**RAM** 4 Giga  
**Système d'exploitation** : <mark>Debian-11</mark>  
**vmId:** 25264c78-b9e1-4520-b7ee-7ca32cc8ddae  

**Adresse locale des cartes de la vm**:  
ETH0   inet 10.0.4.5   
Mask: 255.255.255.0  


**Connection sécurisé à la VM**
- [x] SSH
- [ ] RDP

|Nom du disque | Type de stockage | Taille (Gio) | IOPS max. | Débit max. (Mbits/s) | Chiffrement | Mise en cache de l'hôte|
|---|---|---|---|---|---|---|
|VM-BDD-Groupe-4_DataDisk_0 | SSD Premium | 8 | 3500 | 420 | SSE avec PMK | Lecture/Ecriture |
|VM-BDD-Groupe-4_OsDisk_1_5384e6bb6fa140d0b05e81794e2dd7c5| HDD Standard LRS | 30| 500 | 60 | SSE avec PMK | Lecture/Ecriture |



## 3. Tâches 

- Création du groupe de ressource : **Brief_2_Groupe_4**
- Création du réseau avec plage d'adressage des IP privées
- Déploiement des VM
- Configuration différente, basée selon les tâches de travail prévues par VM : 
  - Stockage plus rapide pour la VM Base De Données
  - Plus de CPU et de RAM pour la VM Applicative
  - Basique/Par défaut pour la VM Administrative
- Chargement des clefs publiques dans la VM Administrative
- Configuration des VMs :
  -  Administrative : 
  -  Base De Données : MariaDB
  -  Applicative : Nextcloud et Apache 2 server
- Table de routage des interfaces réseaux
- Création de groupes d'utilisateurs
- Test des performances du système et du service aux utilisateurs
