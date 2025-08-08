# Univerzální Claude.md Template

<!-- ========================================== -->
<!-- 📋 UNIVERZÁLNÍ CLAUDE INSTRUKCE           -->
<!-- ⚠️  NAČTI TAKÉ: docs/shared/CLAUDE-UNI.md -->
<!-- ========================================== -->

## 🤖 Claude Konfigurace & Workflow

### **Komunikační preference**
- **Jazyk**: Česky (pokud není výslovně požadována angličtina)
- **Oslovení**: "Droku" (5. pád)
- **Styl**: Přímý, technický, bez zbytečných zdvořilostí
- **Verbosity**: Stručně k věci, detaily jen když jsou potřeba
- **Vizuální označení**: Začínej odpovědi modrou tečkou 🔵 pro lepší odlišení v chatu

### **Session Continuity Pattern**

#### **Začátek každé session:**
```bash
1. Claude automaticky načte CLAUDE.md
2. Zkontroluje "POSLEDNÍ SESSION" sekci
3. Pokračuje dle "PŘÍŠTÍ KROKY" nebo čeká na instrukce
```

#### **Během session:**
- Používej TodoWrite pro tracking úkolů
- Aktualizuj progress v reálném čase
- Commituj změny s rozumnými messages

#### **Konec každé session:**
- Aktualizuj "POSLEDNÍ SESSION" sekci
- Zapiš "PŘÍŠTÍ KROKY" pro navazující session
- Poznamenej aktivní problémy a kontext

### **🚨 KRITICKÉ PRAVIDLO PRO CLAUDE**

#### **NIKDY NEBUDU TVRDIT VĚCI KTERÉ NEMÁM OVĚŘENÉ!!!!!!!**

**DŮLEŽITÉ:** Tento zápis je mířený na CLAUDE - on musí VŽDY:
- ✅ OVĚŘIT si fakta před tvrzením
- ✅ ŘÍCT "nevím, ověřím si to" místo hádat
- ✅ PŘIZNAT chybu okamžitě při zjištění
- ❌ NIKDY neříkat technické informace bez 100% jistoty
- ❌ NIKDY neplýtvat časem uživatele dezinformacemi

**POVINNÉ VÝRAZY PŘI NEJISTOTĚ:**
Pokud si Claude NENÍ 100% jistý, MUSÍ použít jeden z těchto výrazů:
- "asi"
- "nejsem si jistý" 
- "možná je to tak, možná ne"
- "nevím"
- "myslím, že"
- "pravděpodobně"
- "zdá se mi"
- "pokud si správně pamatuji"
- "nejspíš"

**CLAUDE MUSÍ BÝT SPOLEHLIVÝ, NE RYCHLÝ!**

### **Preferované nástroje & approach**
- **Search**: Task tool pro komplexní hledání místo manuálního grep/glob
- **File operations**: Raději Edit než Write (zachovat existující soubory)
- **Planning**: TodoWrite pro transparentnost postupu
- **Documentation**: Aktualizuj CLAUDE.md průběžně, ne až na konci

### **⚠️ KRITICKÉ BEZPEČNOSTNÍ PROTOKOLY - GIT WORKFLOW**

#### **🚨 POVINNÉ KONTROLY PŘED KAŽDÝM COMMITEM:**
1. **VŽDY spusť `git status`** - zkontroluj počet a typ změněných souborů
2. **NIKDY necommituj více než 5 souborů najednou** - vysoké riziko chyby
3. **STAGING jednotlivě**: `git add soubor1.ts soubor2.ts` (NIKDY `git add .`)
4. **Verify před commit**: `git diff --cached` - co přesně commitujeme
5. **Atomic commits**: Jeden commit = jedna logická změna

#### **🛡️ AI ASISTENT SAFETY PROTOKOL:**
- **POKUD cítíš JAKÉKOLIV pochybnosti o rozsahu nebo bezpečnosti operace**
- **OKAMŽITĚ zastav a řekni: "Mám pochybnosti o této operaci. Potřebuji potvrzení."**
- **NIKDY nepokračuj s commit operací pokud:**
  - Je ovlivněno více než 5 souborů
  - Vidíš destruktivní změny (smazání souborů)
  - Nejsi si 100% jistý co děláš
  - Uživatel spěchá nebo tlačí na rychlost

#### **📋 RECOVERY PŘÍKAZY (pro záchranné situace):**
```bash
# Zrušit posledních N commitů (zachovat změny na disku):
git reset --soft HEAD~N

# Tvrdé vrácení na konkrétní commit (POZOR - ztráta změn):
git reset --hard [commit_hash]

# Obnovit soubor z konkrétního commitu:
git checkout [commit_hash] -- [file_path]

# Zobrazit co obsahuje konkrétní commit:
git show --name-only [commit_hash]
```

### **Univerzální .env & Secrets Management Standard**

#### **📁 Souborová struktura (pro všechny projekty):**
```
project_parent_directory/
├── .env                           ← Secrets (mimo dosah Claude)
└── project_name/                  ← Hlavní projekt (kde Claude pracuje)
    ├── .env.example               ← Template (v git, bezpečné)
    ├── .gitignore                 ← Obsahuje .env, node_modules
    ├── config/environment.ts      ← Načítá ../.env + fallbacky
    ├── package.json
    └── src/
```

#### **🔒 Bezpečnostní pravidla:**
- **Claude pracuje pouze s `.env.example`** (template soubory)
- **Reálné secrets v `../.env`** (o úroveň výš, mimo dosah)
- **Nikdy nesdílet produkční secrets** přes chat
- **Development secrets** - OK pro sdílení (testovací API klíče)

#### **⚙️ Environment loading priorita:**
```typescript
// config/environment.ts pattern pro všechny projekty:
1. ../.env                    // Development secrets (mimo dosah Claude)
2. .env.local                 // Lokální override  
3. process.env               // Server environment variables
4. .env                      // Fallback template values
```

#### **🚀 Deployment strategy:**
- **Development:** `../.env` soubor s dev secrets
- **Staging/Production:** Server environment variables (VPS/hosting panel)
- **Docker:** Environment variables v compose file
- **CI/CD:** Secrets management systém (GitHub Secrets, atd.)

#### **📋 Checklist pro nové projekty:**
- [ ] Vytvořit `.env.example` s placeholdery
- [ ] Přidat `.env` do `.gitignore`
- [ ] Implementovat prioritní loading v `config/environment.ts`
- [ ] Dokumentovat required secrets v README
- [ ] Nastavit development secrets v `../.env`

### **🌐 Multi-Web VPS Deployment Standard**

#### **🏗️ Server Architecture (Hetzner VPS):**
```
VPS: [username]-webhost-prod
├── /var/www/projekt1/          → port 5000 → doména1.com
├── /var/www/projekt2/          → port 5001 → doména2.com  
├── /var/www/projekt3/          → port 5002 → doména3.com
├── nginx → reverse proxy + SSL
├── PostgreSQL → shared database
└── PM2 → process management
```

#### **⚙️ Port Allocation Pattern:**
```bash
5000 - Main/First project
5001 - Staging environment  
5002 - Second project
5003 - Third project
...
8000+ - Development ports
```

#### **📋 VPS Setup Checklist:**
- [ ] **Server:** CPX11 (2 vCPU, 4GB RAM) nebo CPX21 (3 vCPU, 8GB RAM)
- [ ] **OS:** Ubuntu 22.04 LTS
- [ ] **SSH Key:** bez passphrase (pro GitHub Actions)
- [ ] **Název:** `[username]-webhost-prod`
- [ ] **Location:** Německo (nejblíž ČR)

#### **🔧 Initial Server Setup:**
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install PostgreSQL
sudo apt install postgresql postgresql-contrib -y

# Install Nginx
sudo apt install nginx -y

# Install PM2 globally
sudo npm install -g pm2

# Setup UFW Firewall
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```

#### **🌐 Nginx Multi-Domain Pattern:**
```nginx
# /etc/nginx/sites-available/projekt1
server {
    listen 80;
    server_name doména1.com www.doména1.com;
    
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

#### **🔐 SSL Certificate Pattern:**
```bash
# Let's Encrypt pro každou doménu
sudo certbot --nginx -d doména1.com -d www.doména1.com
sudo certbot --nginx -d doména2.com -d www.doména2.com
```

#### **📦 GitHub Actions Secrets (per project):**
```
PRODUCTION_HOST=your-vps-ip
PRODUCTION_USER=root
PRODUCTION_SSH_KEY=private-key-content
PROJECT_PORT=5000  # unique pro každý projekt
```

#### **🚀 Deployment Workflow:**
1. **Development** → push to `development` branch
2. **Staging** → push to `staging` branch → auto-deploy na staging port
3. **Production** → push to `main` branch → auto-deploy na production port
4. **DNS** → Cloudflare A record na VPS IP

#### **💾 Database Strategy:**
```sql
-- Shared PostgreSQL instance
CREATE DATABASE projekt1_prod;
CREATE DATABASE projekt1_staging;
CREATE DATABASE projekt2_prod;
-- Každý projekt vlastní DB s prefixem
```

### **📦 Git Submodule Management**

#### **🔧 Přidání submodulu:**
```bash
# Přidat submodul do konkrétní složky
git submodule add https://github.com/username/repo-name docs/shared

# Přidat submodul s konkrétní větví
git submodule add -b main https://github.com/username/repo-name docs/shared
```

#### **📥 Načtení projektu se submoduly:**
```bash
# Varianta 1: Klonování s automatickým načtením submodulů
git clone --recursive https://github.com/username/main-project.git

# Varianta 2: Ruční načtení po normálním klonování
git clone https://github.com/username/main-project.git
cd main-project
git submodule init
git submodule update

# Varianta 3: Zkrácený příkaz pro inicialiaci + update
git submodule update --init --recursive
```

#### **🔄 Aktualizace submodulů:**
```bash
# Aktualizovat konkrétní submodul na nejnovější commit z origin
git submodule update --remote docs/shared

# Aktualizovat všechny submoduly
git submodule update --remote

# Aktualizovat a merge změny (pokud máš lokální změny)
git submodule update --remote --merge

# Aktualizovat na konkrétní commit
cd docs/shared
git checkout [commit-hash]
cd ../..
git add docs/shared
git commit -m "Update submodule to specific commit"
```

#### **🗑️ Odstranění submodulu:**
```bash
# 1. Odregistrovat submodul
git submodule deinit -f docs/shared

# 2. Odstranit ze staging area a smazat složku
git rm -f docs/shared

# 3. Vyčistit git cache (volitelné)
rm -rf .git/modules/docs/shared
```

#### **⚠️ Důležité poznámky k submodulům:**
- **Commit tracking:** Submodul odkazuje na konkrétní commit, ne na branch
- **Manual updates:** Musíš ručně aktualizovat pomocí `git submodule update --remote`
- **Team workflow:** Všichni vývojáři musí vědět o submodulech
- **CI/CD setup:** Build servery potřebují `--recursive` nebo `git submodule update`

#### **📋 Submodule Checklist:**
- [ ] Přidat submodul: `git submodule add URL path`
- [ ] Dokumentovat v README jak načíst projekt (`git clone --recursive`)
- [ ] Nastavit CI/CD pro recursive cloning
- [ ] Pravidelně aktualizovat: `git submodule update --remote`
- [ ] Commitnout aktualizace submodulů do hlavního projektu

<!-- ========================================== -->
<!-- UNIVERZÁLNÍ TEMPLATE - END                -->
<!-- ========================================== -->