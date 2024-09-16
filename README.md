# **Instalando e Configurando o Unbound Exporter para Grafana**

## **Introdução**

O Unbound Exporter é uma ferramenta que coleta métricas do servidor DNS Unbound e as expõe em um formato compatível com o Prometheus. Ao integrá-lo com o Grafana, você pode criar painéis informativos para monitorar sua infraestrutura de DNS.

### **Pré-requisitos**

-   Um sistema Linux baseado em Debian (como Ubuntu, Debian)
-   Privilegios de root ou sudo
-   Conhecimento básico do terminal Linux
-   Um servidor DNS Unbound em execução

### **Instalação**

1.  **Atualize a lista de pacotes:**
    
    
    
    ```Bash
    sudo apt update
    ```
    
    
2.  **Atualize os pacotes existentes:**
    
  
    
    ```Bash
    sudo apt upgrade -y
    ```
    
3.  **Instale as ferramentas necessárias:**
    
      
    ```Bash
    sudo apt install sudo git curl golang
    ```


### **Configuração do Unbound**

1.  **Edite a configuração do Unbound:**

    
    ```Bash
    sudo nano /etc/unbound/unbound.conf
    ```

    
2.  **Adicione ou modifique as seguintes linhas:**
    
    ```Bash
    server:
        statistics-interval: 0
        statistics-cumulative: yes
        extended-statistics: yes
        remote-control:
            control-enable: yes
            control-interface: 127.0.0.1
            control-port: 8953     
3.  **Gere os certificados:**
    

    
    ```Bash
    unbound-control-setup
    ```
    
### **Instalando o Unbound Exporter**

1.  **Clone o repositório do Exporter:**
        
    ```Bash
    git clone https://github.com/letsencrypt/unbound_exporter.git
      ```
   
    _Nota: Substitua pela URL correta do repositório se estiver usando uma versão ou fork diferente._
2.  **Compile e instale:**
        
    ```Bash
    cd node_exporter
    make build
    sudo cp unbound_exporter /usr/local/bin/
### **Criando um Serviço Systemd**

1.  **Crie o arquivo de serviço:**
    
    ```Bash
    sudo nano /etc/systemd/system/unbound_exporter.service
2.  **Adicione o seguinte conteúdo:**
 
    ```Bash
    [Unit]
    Description=Unbound Exporter for Prometheus
    After=network.target
    
    [Service]
    User=root
    ExecStart=/usr/local/bin/unbound_exporter \
        -unbound.host "tcp://127.0.0.1:8953" \
        -unbound.cert "/etc/unbound/unbound_control.pem" \
        -unbound.key "/etc/unbound/unbound_control.key" \
        -unbound.ca "/etc/unbound/unbound_server.pem"
    Restart=on-failure
    RestartSec=10
    
    [Install]
    WantedBy=multi-user.target
    ```

3.  **Recarregue o systemd:**
    
   
    ```Bash
    sudo systemctl daemon-reload
    ```

4.  **Habilite e inicie o serviço:**

    ```Bash
    sudo systemctl enable unbound_exporter
    sudo systemctl start unbound_exporter
    ```


### **Verificando a Instalação**

1.  **Verifique o status do serviço:**

    ```Bash
    sudo systemctl status unbound_exporter
    ```

2.  **Acesse o endpoint das métricas:**

    -   Use um navegador web ou curl para acessar o endpoint das métricas (por exemplo, http://<seuipdns>:9167/metrics).