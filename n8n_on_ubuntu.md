# How to Install n8n on an Ubuntu Server  
*Based on the guide from sliplane.io*

---

## 1. Update Your Server

Start by updating your server to ensure all packages are current:

```bash
sudo apt-get update
sudo apt-get upgrade -y
```
2. Configure UFW Firewall
Allow only the necessary ports:
```bash
sudo apt install ufw -y
sudo ufw allow 22    # SSH
sudo ufw allow 80    # HTTP
sudo ufw allow 443   # HTTPS
sudo ufw enable
sudo ufw status verbose
```
3. Install Docker
Install required dependencies:
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
```
Add Docker's GPG key:
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```
Add the Docker repository:
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
    https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo $VERSION_CODENAME) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```
Install Docker Engine:
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```
Verify installation:
```bash
sudo docker run hello-world
```
4. Install Caddy (Reverse Proxy with Automatic HTTPS)
Install required packages and Caddy repository:
```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' \
  | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' \
  | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy -y
```
Configure Caddy
Edit the Caddyfile:
```bash
sudo nano /etc/caddy/Caddyfile
```
```bash
yourdomain.com {
    reverse_proxy localhost:5678
}
```
```bash
sudo systemctl restart caddy
```
5. Install n8n Using Docker Compose
Create directory for n8n:
```bash
mkdir ~/n8n
cd ~/n8n
```
Create a docker-compose.yml file:
```bash
services:
  n8n:
    image: docker.io/n8nio/n8n:latest
    restart: always
    ports:
      - "5678:5678"
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
```
Start n8n:
```bash
sudo docker compose up -d
```
6. Access n8n
Open a browser and navigate to:

https://yourdomain.com

7. Updating n8n
To update to the latest version:
```bash
sudo docker compose pull
sudo docker compose up -d
```
8. Recommended Security Practices

Keep your system updated
Use strong passwords and secure your n8n instance
Monitor logs for suspicious behavior
Consider adding Fail2Ban or additional hardening