# 🚀 Infraestrutura Automática e Deploy Contínuo (GitOps)

Este repositório documenta a arquitetura e a implementação da infraestrutura em nuvem para o projeto prático da disciplina de Fundamentos de DevOps do Instituto Federal Catarinense (IFC - Campus Araquari). O ambiente hospeda uma aplicação Full-Stack e utiliza práticas modernas de automação para garantir entregas contínuas com zero downtime.

---

## 🏗️ Arquitetura e Stack Tecnológico

O ecossistema foi desenhado para ser resiliente, descentralizado e escalável, utilizando as seguintes tecnologias:

*   **Frontend:** HTML, CSS e JavaScript
*   **Backend:** API em Python
*   **Banco de Dados:** PostgreSQL 15-alpine
*   **Infraestrutura como Código (IaC):** Terraform
*   **Orquestração de Contêineres:** Kubernetes (distribuição K3s)
*   **Ingress Controller:** Traefik
*   **Automação (CI/CD):** GitHub Actions & ArgoCD

---

## ☁️ Topologia Cloud (AWS Learner Lab)

Toda a infraestrutura base foi provisionada de forma automatizada na AWS utilizando Terraform. O cluster Kubernetes opera sobre quatro instâncias EC2 com Ubuntu:

*   **1x Control Plane (`t2.medium`):** Nó principal que gerencia o estado do cluster, orquestra os agendamentos e recebe o tráfego externo primário.
*   **3x Worker Nodes (`t2.micro`):** Nós de trabalho dedicados exclusivamente à execução e processamento dos pods da aplicação e do banco de dados.

---

## 🔄 Pipeline de Integração e Entrega (CI/CD)

O deploy obedece rigorosamente ao paradigma **GitOps**, onde este repositório atua como a única fonte da verdade (*Single Source of Truth*) para o ambiente de produção.

1.  **Desenvolvimento:** O código é desenvolvido e enviado para a branch `main` dos repositórios da aplicação (frontend ou backend).
2.  **Integração Contínua (CI):** O GitHub Actions intercepta o evento, constrói uma nova imagem Docker otimizada e a envia para o Docker Hub. O versionamento ocorre utilizando o hash único do commit.
3.  **Gestão de Manifestos:** A mesma Action acessa este repositório de infraestrutura de forma segura (via *Personal Access Token*) e modifica o arquivo YAML, atualizando a tag da imagem em produção.
4.  **Entrega Contínua (CD):** O ArgoCD, rodando internamente no cluster K3s, identifica a alteração nos arquivos do GitHub. Ele inicia o sincronismo automático (*Auto-Sync*), substituindo os contêineres antigos pela nova versão gradativamente.

---

## 🌐 Acesso aos Serviços

O roteamento externo para os serviços internos do Kubernetes é gerenciado pelo Traefik, que divide o tráfego com base nas rotas de URL, utilizando o IP público dinâmico gerado pelo Terraform:

*   **Interface de Usuário:** `http://<IP-DO-CONTROL-PLANE>/`
*   **Endpoints da API:** `http://<IP-DO-CONTROL-PLANE>/api`
*   **Dashboard do ArgoCD:** `https://<IP-DO-CONTROL-PLANE>:30080` *(Acesso via porta NodePort, exige permissão no navegador para certificado não verificado).*

---

## 💡 Desafios e Soluções

A transição de um ambiente de desenvolvimento isolado para uma arquitetura distribuída exigiu adaptações estruturais. 

O principal desafio superado envolveu a comunicação entre a interface web e a API. Durante o desenvolvimento, o frontend consumia dados via `localhost`. Na arquitetura em nuvem, foi necessário mapear e configurar corretamente o Ingress Controller (Traefik) para capturar o tráfego HTTP na porta 80 e rotear inteligentemente qualquer requisição com o prefixo `/api` para os contêineres do backend em Python, garantindo o funcionamento do sistema sem erros de CORS ou rotas perdidas.

---

**Autor:** Vitor Ferreira  
**Formação:** Bacharelado em Sistemas de Informação (5º Período)
