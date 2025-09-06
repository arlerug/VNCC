# Progetto VNCC — Novità di Kubernetes v1.33 e confronto con v.32

## Obiettivi e motivazioni
L’obiettivo del progetto è analizzare e confrontare alcune funzionalità chiave di Kubernetes nelle versioni **1.32.2** e **1.33.4**, all’interno di un ambiente di laboratorio realizzato con **VirtualBox** e **Kubespray**.  
Il lavoro nasce dall’esigenza di comprendere in modo pratico i cambiamenti introdotti, con particolare attenzione alle novità che incidono sulla gestione dei Pod e dei servizi.  

Motivazioni principali:
- Verificare deprecazioni e nuove API introdotte in 1.33.  
- Dimostrare con esperimenti pratici le differenze rispetto alla 1.32.  
- Fornire una base di conoscenza operativa utile per futuri sviluppi o deploy reali.  

---

## Architettura dei cluster
Il laboratorio è stato realizzato predisponendo due cluster distinti, ciascuno composto da 2 nodi:  

- Cluster 1.32.2  
  - node1 → master  
  - node2 → worker  

- Cluster 1.33.4  
  - node1-133 → master  
  - node2-133 → worker  

Rete e configurazione:  
- Rete VirtualBox NAT (192.168.43.0/24)  
- IP statici:  
  - Master: 192.168.43.10  
  - Worker: 192.168.43.11  
<img width="1773" height="936" alt="Setup" src="https://github.com/user-attachments/assets/293f443c-098e-4505-83cd-a6eeff895ae5" />

---

## Specifiche tecniche
- Sistema operativo VM: Xubuntu 24.04 (OVA clonata)  
- Risorse VM:  
  - 2 vCPU  
  - 4 GB RAM  
  - 25 GB disco minimo  
- Tool di provisioning: Kubespray (installato su node1)  
- Container runtime: containerd 2.1.4  
- CNI: Calico  
- Versioni Kubernetes testate:  
  - v1.32.2  
  - v1.33.4  
- Strumenti aggiuntivi:  
  - kubectl (via snap)  
  - Helm (per installazione driver DRA)  

---

## Funzionalità analizzate
1. **Endpoints vs EndpointSlice**  
   - In 1.32 i Service generano oggetti *Endpoints* monolitici.  
   - In 1.33 gli *Endpoints* sono deprecati a favore di *EndpointSlice*, più scalabili ed efficienti.  

2. **Sidecar nativo**  
   - In 1.32 i sidecar devono essere definiti come container “pari” nello stesso Pod.  
   - In 1.33 è introdotto il supporto nativo con `initContainer` e `restartPolicy: Always`.  

3. **In-place Pod Resize**  
   - In 1.32 non è possibile modificare CPU/memoria senza ricreare il Pod.  
   - In 1.33 si può aggiornare un Pod già in esecuzione senza downtime, usando la nuova subresource `resize`.  

4. **Dynamic Resource Allocation (DRA)**  
   - Nuovo framework per gestire risorse speciali (GPU, FPGA, SmartNIC).  
   - Introduce API `resource.k8s.io/v1beta2` con oggetti DeviceClass, ResourceSlice, ResourceClaim.  
   - Dimostrata la richiesta e l’uso simulato di una GPU tramite un driver di esempio.  

5. **Image Volumes**  
   - Funzionalità beta che permette di montare un’immagine OCI come volume di sola lettura.  
   - Su containerd 2.1.4 non funzionante nativamente, testata tramite fallback (initContainer + emptyDir).
  
  ---

## Documentazione completa
Per i dettagli sul setup dei cluster e per gli step delle esperienze pratiche, consultare il file PDF della tesina allegata.

## Riferimenti

- Kubernetes Documentation — [Sidecar Containers](https://kubernetes.io/docs/concepts/workloads/pods/sidecar-containers)  
- Kubernetes Blog — [Kubernetes v1.33 Release: Octarine](https://kubernetes.io/blog/2025/04/23/kubernetes-v1-33-release/)  
- Kubernetes Blog — [Kubernetes v1.33: DRA Updates](https://kubernetes.io/blog/2025/05/01/kubernetes-v1-33-dra-updates/)  
- Kubernetes Blog — [In-place Pod Resize Beta in Kubernetes v1.33](https://kubernetes.io/blog/2025/05/16/kubernetes-v1-33-in-place-pod-resize-beta/)  
- Kubernetes Blog — [Image Volume Beta in Kubernetes v1.33](https://kubernetes.io/blog/2025/04/29/kubernetes-v1-33-image-volume-beta/)  
