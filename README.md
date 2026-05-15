# Alat za lekturu novinskih tekstova

Automatska provjera novinskih tekstova na bosanskom/hrvatskom/srpskom jeziku. Unesi URL — alat preuzima tekst i pronalazi:

- **Tipografske greške** — tipfeler, izostavljeno slovo, ćirilično slovo u latiničnom tekstu
- **Gramatičke greške** — padeži, slaganje, interpunkcija
- **Formatiranje** — dupli razmaci, višak praznih redova
- **Logičke nekonzistentnosti** — kontradikcije u sadržaju

Koristi GPT-4o preko GitHub Models (besplatno).

## Pokretanje

```bash
pip install -r requirements.txt
cp .env.example .env
# Dodaj GitHub Personal Access Token u .env
# Token kreiraš na: github.com/settings/tokens → Generate new token (classic) → bez scope-a
python app.py
# Otvori http://localhost:5001
```

## Deploy na server (nginx + systemd)

```bash
git clone https://github.com/USERNAME/REPO
cd REPO && pip install -r requirements.txt
cp .env.example .env && nano .env

sudo nano /etc/systemd/system/lektura.service
```

```ini
[Unit]
Description=Alat za lekturu
After=network.target

[Service]
User=www-data
WorkingDirectory=/opt/lektura
EnvironmentFile=/opt/lektura/.env
ExecStart=/usr/local/bin/gunicorn -w 2 -b 127.0.0.1:5001 app:app
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable lektura && sudo systemctl start lektura
```

Nginx:
```nginx
server {
    listen 80;
    server_name lektura.example.com;
    location / {
        proxy_pass http://127.0.0.1:5001;
        proxy_set_header Host $host;
    }
}
```

```bash
sudo certbot --nginx -d lektura.example.com
```

## Promjena modela

U `app.py` promijeni `MODEL` i `client` za drugi provider (Anthropic, OpenAI, Groq...).
