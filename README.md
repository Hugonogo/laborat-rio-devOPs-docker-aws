
```md
# Deploy de Site Estático com Docker + AWS (ECR + EC2)

Este projeto demonstra o processo completo de containerização, publicação de imagem e deploy de uma aplicação em ambiente cloud utilizando Docker e serviços da AWS.

A aplicação consiste em um site estático servido por um container Nginx. A imagem foi construída localmente, enviada para o Amazon ECR e posteriormente utilizada em uma instância EC2.

---

## Tecnologias Utilizadas

- Docker
- Docker Compose
- Nginx
- AWS (ECR, EC2, IAM)
- Linux
---

````

## ⚙️ Etapas do Projeto

### 1. Clonagem do repositório base

Foi utilizado como base o seguinte repositório:

https://github.com/marialazara/laboratorio-devops

```bash
git clone https://github.com/marialazara/laboratorio-devops.git
cd laboratorio-devops
````

---

### 2. Criação do Dockerfile

Foi criado um Dockerfile utilizando a imagem oficial do Nginx baseada em Alpine Linux:

```Dockerfile
FROM nginx:alpine

WORKDIR /usr/share/nginx/html

COPY website/ .

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

### 3. Criação do Docker Compose

```yaml
services:
  web:
    build: .
    image: nginx_static_site:1.0
    container_name: site01
    ports:
      - "8080:80"
    restart: always
```

---

### 4. Build e execução local

```bash
docker compose up --build
```

A aplicação ficou disponível em:

[http://localhost:8080](http://localhost:8080)

---

### 5. Criação do repositório no Amazon ECR

Foi criado um repositório no Amazon ECR para armazenar a imagem Docker.

---

### 6. Autenticação no ECR

```bash
aws ecr get-login-password --region us-east-2 | \
docker login --username AWS --password-stdin <URI_DO_ECR>
```

---

### 7. Tag da imagem

```bash
docker tag nginx_static_site:1.0 <URI_DO_ECR>/site-prod:1.0
```

---

### 8. Push da imagem para o ECR

```bash
docker push <URI_DO_ECR>/site-prod:1.0
```

---

### 9. Criação da instância EC2

Foi criada uma instância no AWS EC2 com acesso via SSH utilizando chave privada.

---

### 10. Acesso à instância EC2

```bash
ssh -i sua-chave.pem ubuntu@<IP_PUBLICO>
```

---

### 11. Instalação do Docker na EC2

```bash
sudo apt update
sudo apt install docker.io -y

sudo systemctl start docker
sudo systemctl enable docker
```

---

### 12. Configuração de permissões IAM

Foi associado um Role IAM à instância EC2 com permissões de leitura no Amazon ECR, permitindo autenticação e pull de imagens.

---

### 13. Pull da imagem no EC2

```bash
docker pull <URI_DO_ECR>/site-prod:1.0
```

---

### 14. Execução do container

```bash
docker run -d -p 80:80 --name site01 <URI_DO_ECR>/site-prod:1.0
```

---

## Conceitos Aplicados

* Containerização de aplicações com Docker
* Criação e versionamento de imagens
* Utilização de registry (Amazon ECR)
* Deploy em ambiente cloud (Amazon EC2)
* Gerenciamento de permissões com IAM

---

## 📚 Referência

Repositório base utilizado:

[https://github.com/marialazara/laboratorio-devops](https://github.com/marialazara/laboratorio-devops)

---

## 👨‍💻 Autor

Desenvolvido por Hugo Nogueira.
