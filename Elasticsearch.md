# Installation et Configuration d’Elasticsearch 9.3.2 sur un Cluster Multi-Nœuds
## 1. Architecture du Cluster

|Node	|Hostname	|IP           |
| ----- | --------- | ----------- |
|Node 1	|node-1   	|172.16.50.140|
|Node 2	|node-2	    |172.16.50.141| 
|Node 3	|node-3	    |172.16.50.142|


Configuration `/etc/hosts`
Sur chaque nœud, ajouter les entrées suivantes :
```
172.16.50.140 node-1
172.16.50.141 node-2
172.16.50.142 node-3
```

## 2. Téléchargement et Vérification d’Elasticsearch
Exécuter sur tous les nœuds :

```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.3.2-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.3.2-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-9.3.2-linux-x86_64.tar.gz.sha512
```
## 3. Installation
Extraction et déplacement du package
```
tar -xzf elasticsearch-9.3.2-linux-x86_64.tar.gz
mv elasticsearch-9.3.2 /opt/elasticsearch
```


## 4. Configuration d’Elasticsearch

Modifier le fichier de configuration `/opt/elasticsearch/config/elasticsearch.yml`  en fonction du nœud

```
 cat /opt/elasticsearch/config/elasticsearch.yml | grep -v "#" |tr -s [:space:]
cluster.name: Elk-Demo
node.name: node-1
network.host: 172.16.50.140
transport.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["node-1", "node-2", "node-3"]
cluster.initial_master_nodes: ["node-1", "node-2", "node-3"]
node.roles: [ master, data ]
xpack.security.enabled: false
```

Répétez cette configuration sur chaque nœud en modifiant node.name et network.host pour correspondre à chaque serveur.


## 5. Création de l’utilisateur Elasticsearch

```
useradd -m elasticsearch
chown -R elasticsearch:elasticsearch /opt/elasticsearch
```
 
## 6. Test du lancement manuel (sur chaque nœud)
Se connecter en tant qu’utilisateur elasticsearch :
```
su - elasticsearch
cd /opt/elasticsearch
./bin/elasticsearch
```


**Vérification du cluster**
`curl http://localhost:9200/_cluster/health?pretty`
![image](https://hackmd.io/_uploads/H1Eo5jAq-l.png)

`curl http://localhost:9200/_cat/nodes?v`
![image](https://hackmd.io/_uploads/S1To9oR9-e.png)

À ce stade, tous les nœuds doivent apparaître dans le cluster
## 7. Configuration du service Systemd
**Important** Pour cette étape, vous devez être connecté en **root**
Créer le fichier `/etc/systemd/system/elasticsearch.service` :

nano /etc/systemd/system/elasticsearch.service

```
[Unit]
Description=Elasticsearch
After=network.target

[Service]
Type=simple
User=elasticsearch
Group=elasticsearch
ExecStart=/opt/elasticsearch/bin/elasticsearch
Restart=always
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```
**Activer et démarrer le service**

```
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch

sudo systemctl start elasticsearch
sudo systemctl status elasticsearch
```

