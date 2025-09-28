## 🚀 Windows 11 em Container Docker (via KVM/QEMU)

Este repositório contém uma configuração robusta de **Docker Compose** para rodar uma instância completa do **Windows 11** dentro de um container Docker, utilizando virtualização **KVM/QEMU**.

A configuração é ideal para testes isolados, ambientes de desenvolvimento que exigem Windows, ou para ter um desktop Windows acessível via RDP e HTTPS, com recursos dedicados e **persistência garantida**.

-----

## Pré-requisitos

Para que o container funcione corretamente, seu sistema host (Linux, como o Linux Mint) deve atender aos seguintes requisitos:

1.  **Docker e Docker Compose V2:** Devem estar instalados.
2.  **Virtualização KVM:** O módulo KVM do kernel deve estar habilitado e acessível.
      * Você pode verificar rodando `lsmod | grep kvm`.
      * **Carregamento Permanente:** Se necessário, adicione `kvm` e o módulo específico da sua CPU (`kvm_intel` ou `kvm_amd`) no arquivo `/etc/modules-load.d/kvm.conf`.

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

### 🔑 Credenciais Padrão (Configuradas no YML)

As credenciais do RDP estão definidas na variável `RDP_PASSWORD` do `docker-compose.yml`:

```yaml
# Trecho do docker-compose.yml
environment:
  # ...
  # Senha para o usuário padrão 'docker'
  RDP_PASSWORD: "admin" 
```

-----

## Como Iniciar o Ambiente

1.  **Baixe o arquivo** `docker-compose.yml` e salve-o em uma pasta.
2.  **Suba o ambiente** (o Docker fará o download da imagem e iniciará a instalação do Windows. A primeira inicialização é lenta).

<!-- end list -->

```bash
docker compose up -d
```

-----

## 🔒 Acesso ao Windows 11

Após a conclusão da instalação (que pode ser monitorada via `http://localhost:8006`), utilize um cliente de Área de Trabalho Remota para se conectar.

### Detalhes do Login RDP

| Campo | Valor | Observação |
| :--- | :--- | :--- |
| **Endereço** | `localhost:3389` | A porta `3389` é mapeada diretamente para a máquina virtual. |
| **Usuário Padrão** | **`docker`** | Este nome de usuário é **fixo** pelo script de inicialização do container. |
| **Senha** | **`admin`** | Senha definida na variável `RDP_PASSWORD` do `yml`. |

### Outros Acessos

| Protocolo | Endereço |
| :--- | :--- |
| **Web VNC (Monitoramento)** | `http://localhost:8006` |
| **HTTPS (Serviço Web)** | `https://localhost` | (Acessa serviços rodando na porta 443 do Windows.) |

-----

## Gerenciamento e Persistência de Dados

Todos os dados da instalação do Windows (o disco virtual VHD) são salvos permanentemente no **volume nomeado** do Docker: `windows_data`.

  * **Parar:** Para desligar o container de forma segura, use:
    ```bash
    docker compose stop
    ```
  * **Remover (CUIDADO - Perde Todos os Dados):** Para remover o container **e apagar todos os dados** (incluindo o disco virtual do Windows), use o comando com o flag `-v`:
    ```bash
    docker compose down -v
    ```
