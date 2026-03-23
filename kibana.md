
# Installation et Configuration de Kibana
## 1. Installation de Kibana
Exécuter la commande suivante pour installer Kibana
`sudo apt-get install kibana`

## 2. Configuration de Kibana
Ouvrir le fichier de configuration
`nano /etc/kibana/kibana.yml`

modifier les paramètres suivants

```
# The default port for the web interface.
server.port: 5601
# Allows access via this specific IP.
server.host: "172.16.50.140"
# A descriptive name for your Kibana instance.
server.name: "node-1"
# Since Kibana is on the same node as ES Node-1. 
elasticsearch.hosts: ["http://172.16.50.140:9200"]
```
## 3. Activation du service Kibana
sudo systemctl daemon-reload
sudo systemctl enable kibana

## 4. Démarrage du service
sudo systemctl start kibana

## 5. Vérification
`sudo systemctl status kibana`

Accéder à Kibana via le navigateur  `http://172.16.50.140:5601`

