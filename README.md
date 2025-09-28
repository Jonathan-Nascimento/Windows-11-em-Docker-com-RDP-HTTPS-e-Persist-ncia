# Windows 11 em Docker com RDP, HTTPS e Persistência
Windows 11 em Docker com RDP, HTTPS e Persistência

Que excelente ideia compartilhar essa configuração\! Usar o GitHub com um `README.md` bem estruturado é a melhor forma de documentar e permitir que outros (ou você mesmo no futuro) subam o ambiente rapidamente.

Aqui está o texto formatado em **Markdown** para o seu arquivo `README.md`. Ele explica o propósito, os requisitos e os passos para iniciar o container Windows 11.

-----

## 🚀 Windows 11 em Docker com RDP, HTTPS e Persistência

Este repositório contém uma configuração robusta de **Docker Compose** para rodar uma instância completa do **Windows 11** dentro de um container Docker, utilizando virtualização **KVM/QEMU**.

A configuração é ideal para testes isolados, ambientes de desenvolvimento que exigem Windows, ou simplesmente para ter um desktop Windows acessível via RDP e HTTPS, com recursos dedicados e persistência garantida.

-----

## Pré-requisitos

Para que o container funcione corretamente, seu sistema host (Linux, como o Linux Mint) deve atender aos seguintes requisitos:

1.  **Docker e Docker Compose V2:** Devem estar instalados.
2.  **Virtualização KVM:** O módulo KVM do kernel deve estar habilitado e acessível.
      * Você pode verificar rodando `lsmod | grep kvm`.
      * **Solução Comum:** Se precisar carregar os módulos em cada boot, adicione `kvm` e `kvm_intel` (ou `kvm_amd`) no arquivo `/etc/modules-load.d/kvm.conf`.

-----

## Configuração do `docker-compose.yml`

O arquivo `docker-compose.yml` está pronto para uso e inclui:

| Configuração | Valor | Descrição |
| :--- | :--- | :--- |
| **Imagem** | `dockurr/windows` | Imagem base para virtualização KVM. |
| **Versão** | `Windows 11` | Instalação automática do SO. |
| **Recursos** | 4 Cores / 8GB RAM | Recursos dedicados à VM (ajuste conforme seu host). |
| **Idioma** | `pt-br` | Idioma Português do Brasil. |
| **Persistência** | `windows_data` | Volume nomeado para salvar o disco rígido virtual. |

### 🚨 Atenção: Senha

Antes de subir o container, **ajuste a senha** na variável de ambiente `RDP_PASSWORD` para uma senha forte.

```yaml
# Trecho do docker-compose.yml
environment:
  # ...
  RDP_PASSWORD: "SuaSenhaMuitoSegura123!" # <--- OBRIGATÓRIO ALTERAR!
```

-----

## Como Iniciar o Ambiente

1.  **Baixe o arquivo** `docker-compose.yml` e salve-o em uma pasta.
2.  **Suba o ambiente** (o Docker fará o download da imagem e iniciará a instalação do Windows. A primeira inicialização é lenta).

<!-- end list -->

```bash
docker compose up -d
```

### Acesso ao Windows 11

Após a conclusão da instalação (que pode ser monitorada via `http://localhost:8006`), você pode se conectar:

| Protocolo | Endereço | Credenciais |
| :--- | :--- | :--- |
| **RDP** | `localhost:3389` | **Usuário:** `docker` / **Senha:** (O valor de `RDP_PASSWORD`) |
| **HTTPS** | `https://localhost` | Acessa serviços rodando na porta 443 do Windows. |

### Parar e Manter os Dados

Para desligar o container de forma segura, mantendo todos os seus dados e a instalação do Windows no volume persistente:

```bash
docker compose stop
```

-----

## Mapeamento de Portas Detalhado

O `docker-compose.yml` mapeia as seguintes portas do host para o container:

  * **`3389` (TCP/UDP):** RDP (Remote Desktop Protocol)
  * **`443` (TCP):** HTTPS (para qualquer serviço web que você queira rodar no Windows)
  * **`8006` (TCP):** VNC/Web GUI (para monitorar a instalação ou usar como fallback)
