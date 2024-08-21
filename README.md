## ATIVIDADE LINUX & AWS - Compass UOL - AWS & DevSecOps - 2024

1. Requisitos AWS
   
   - GERAR UMA CHAVE DE ACESSO PÚBLICO;
   - CRIAÇÃO DE INSTÂNCIA EC2 COM O SO AMAZON LINUX 2 (familia t3. small, 16GB SSD);
   - GERAR UM IP ELÁSTICO PARA SER ANEXADO A INSTÂNCIA EC2;
   - LIBERAÇÃO DAS PORTAS DE COMUNICAÇÃO PARA PERMITIR ACESSO PÚBLICO (22/TCP, 111/TCP e UDP, 2049/TCP/UDP, 80/TCP, 443/TCP).
  
3. Requisitos LINUX
   
   - CONFIGURAÇÃO DO NFS;
   - CRIAÇÃO DO DIRETÓRIO DENTRO DO FILESYSTEM COM SEU NOME;
   - SUBINDO O APACHE NO SERVIDOR (DEVE ESTAR ONLINE E RODANDO);
   - CRIAÇÃO DO SCRIPT DE VALIDAÇÃO DO STATUS DO SERVIÇO/APACHE (contendo DATA+HORA, NOME DO SERVIÇO, STATUS E MENSAGEM PERSONALIZADA DE ONLINE OU OFFLINE);
   - AUTOMATIZAÇÃO DA EXECUÇÃO DO SCRIPT DE 5 EM 5 MINUTOS;
   - REALIZAR O VERSIONAMENTO DA ATIVIDADE.


## Requisitos AWS

### GERAR A CHAVE SSH DE ACESSO PÚBLICO AO AMBIENTE

A chave poderá ser gerada junto à criação da instância EC2. Para isso, acessamos o portal inicial da AWS,entramos na guia EC2 e clicamos na opção Instâncias, localizada na barra de navegação à esquerda.

![image](https://github.com/user-attachments/assets/a4602739-45ea-49b6-b31f-5b7d50600bbd)

![image](https://github.com/user-attachments/assets/363f619b-6067-4b4a-a505-87a13fa24c92)

Então, clica no botão Executar Instâncias para criar uma nova VM EC2.

A foto abaixo mostra a tela com os campos de criação do par de chaves (SSH).

![image](https://github.com/user-attachments/assets/3b7cdea3-e1c1-4b62-b180-a9da84899b0b)

Nome não é obrigatório colocar. O tipo do par foi marcado como RSA, e o formato do arquivo que será gerado é .pem, visto que é para ser usado como SSH. Após marcadas as opções, clicar no botão Criar Par de Chaves.

Quando o par estiver pronto, interrompemos a configuração/criação da instância para criar a VPC.

### VPC

Acessamos o serviço VPC, e clicamos no botão Criar VPC. VPC é um recurso para controlar e ter segurança sobre o fluxo de rede.

![image](https://github.com/user-attachments/assets/697f748d-6176-4e47-959d-5e527434bc56)

Foi configurada assim:

![image](https://github.com/user-attachments/assets/036947ed-4a52-4503-8c03-dd934bbe88b8)

![image](https://github.com/user-attachments/assets/58714c28-bd56-40e4-a91a-dc60d3ca0a90)

Dada a criação da VPC, criaremos a subnet que irá controlar as conexões internas/externas das máquinas.

![image](https://github.com/user-attachments/assets/9124aa8b-7c29-4adc-ba26-52b5b274b93c)

Após, será criada uma rota de tabela, para controlar as rotas de tráfego na rede.

![image](https://github.com/user-attachments/assets/1f66ec16-538c-4196-92b1-f04bd1de4e6c)

Agora voltando a criação da instância, deverá ser anexado a VPC e a sub-rede criadas e ativado a auto-atribuição de IP público.

![image](https://github.com/user-attachments/assets/0604b5c2-29ff-4b84-8d01-942d4b5feed0)

As portas de comunicação solicitadas também deverão ser definidas nessa etapa.

A imagem abaixo mostra a configuração das portas solicitadas.

![image](https://github.com/user-attachments/assets/633c3458-1ee1-48ee-822e-e5fc0ebb6c82)

Por fim, é definido o volume da instância, que no caso o requisito era 1x SSD de 16 GBs.

O resultado abaixo contém a configuração geral da EC2.

![image](https://github.com/user-attachments/assets/40ad1d9a-e097-41db-8fe5-4f77abecd231)

Agora, para a VM EC2 acessar a rede externa, precisamos criar um gateway de internet.

![image](https://github.com/user-attachments/assets/6a8bf1fa-0a09-489b-b0f2-49ee8b57171c)

Após a criação, o gateway será associado a VPC já existente.

![image](https://github.com/user-attachments/assets/2512b598-528a-413b-810c-c93dff61d678) (Na imagem, nota-se que a opção de Associar à VPC está indisponível, pois antes da explicação eu já tinha associado meu gateway à VPC, conforme os detalhes abaixo:).

![image](https://github.com/user-attachments/assets/2ab12f55-5e82-4cd3-843a-f20c7628e61f)

Na parte do Ip Elástico, geramos um novo, marcaremos a primeira opção da categoria IPv4. Em grupo de borda de rede, usaremos us-east-1 que é a localização do servidor.

![image](https://github.com/user-attachments/assets/bcdf3075-7fbb-4288-8052-210c3604cbf0)

Criado o ip elástico, será associado à EC2 criada.

![image](https://github.com/user-attachments/assets/fe27613d-85a2-4bed-ac2e-b153d1786dfc) ![image](https://github.com/user-attachments/assets/29072ae7-5abf-48c4-92d6-cb67bc985e6a)

Por fim, como as portas de comunicação já foram configuradas, podemos iniciar a VM EC2  e conectá-la.

![Captura de tela 2024-08-19 171522](https://github.com/user-attachments/assets/4043413c-07cf-459c-b251-becc51cc5e4f) (Desconsiderar os comandos NFS, que serão explicados logo adiante).

## Requisitos Linux

### Instalando e configurando o NFS

```
sudo yum install -y nfs-utils # instala o pacote para gerenciamento de pastas NFS
sudo systemctl start nfs-server
sudo systemctl enable nfs-server
# systemctl configura apps usando o SystemD

sudo mkdir -p /mnt/nfs
sudo vim /etc/exports # arquivo de configuração do NFS

/mnt/nfs *(rw,sync,no_root_squash,no_all_squash)
 # diretorio, * ips que podem se conectar, (leitura e escrita, evita falhas de operação a troco de velocidade, restringe o mapeamento de usuario anonimo)
sudo exportfs -a # exporta a configuração
sudo exportfs -v # verifica se ta funcionando 

sudo chown $(whoami):$(whoami) /mnt/nfs/felipe # libera o usuario atual para poder escrever na pasta | para q o script possa escrever logs
```

![Captura de tela 2024-08-19 171522](https://github.com/user-attachments/assets/1f4d5cb5-4818-4421-9cdd-ce904ac97ce4)

### Criação de diretório no NFS

```
sudo mkdir -p /mnt/nfs/seu_nome
```
![image](https://github.com/user-attachments/assets/f74844f2-dfc6-4103-ae32-55c114d541af)

### Instalando e configurando o Apache

```
sudo yum install -y httpd

sudo systemctl start httpd
sudo systemctl enable httpd

sudo systemctl status httpd # verifica se o serviço está funcionando da maneira correta
```
![Captura de tela 2024-08-19 202518](https://github.com/user-attachments/assets/c29a08df-7d9e-44ba-956b-e2d369b57dbe)
![Captura de tela 2024-08-19 202551](https://github.com/user-attachments/assets/a23531b0-41f2-4e1d-ae89-ccbe9d0f92f3)

### Criando um script de monitoramento do status do Apache

```
sudo vim /usr/local/bin/check_apache.sh
```
```
#!/bin/bash

# Nome do serviço
servico="HTTPD"

# Verifica o status do serviço HTTPD
service_status=$(systemctl is-active httpd)

# Diretório no NFS para salvar o resultado
nfs_dir="/mnt/nfs/felipe"

# Pega a data e hora atuais
data_hora=$(date +"%Y-%m-%d %H:%M:%S")

# Mensagem de status personalizada e arquivos de saída
if [ "$service_status" = "active" ]; then
    mensagem="$data_hora - Serviço: $servico - Status: ONLINE - O HTTPD está funcionando corretamente."
    echo "$mensagem" >> "$nfs_dir/validacao_httpd_online.txt"
else
    mensagem="$data_hora - Serviço: $servico - Status: OFFLINE - O HTTPD não está rodando."
    echo "$mensagem" >> "$nfs_dir/validacao_httpd_offline.txt"
fi
```
![Captura de tela 2024-08-19 193210](https://github.com/user-attachments/assets/ee0c35df-a674-4fe7-bdca-3103a86d52c1) (Nota-se que na imagem os operadores com seta para a direita > contém apenas uma seta. Caso o script seja executado assim, ele não acrescentará mensagens com o status, e sim irá substituí-las a cada 5 minutos. PORTANTO, o operador correto a ser usado no script é duas setas para a direita >>. Assim, será acrescentado um status novo a cada 5 minutos.

Criado o script, aperta ESQ, digita :wq e da um ENTER. Tal comando salva o script e sai do vim imediatamente.

```
sudo chmod +x /usr/local/bin/check_apache.sh
# libera permissões de execução para o script

chmod 775 /mnt/nfs/seu_nome
# também pode funcionar para liberar permissões
```
Saída inicial do script:

2024-08-19 15:20:02 - apache - ONLINE

2024-08-19 20:25:17 -apache - OFFLINE

### Configurando a automatização da execução do Script de 5 em 5 minutos

```
sudo crontab -e

# documento aberto no vim:
*/5 * * * * /usr/local/bin/check_apache.sh
# :wq para salvar
```
![image](https://github.com/user-attachments/assets/adc24e7b-7173-453e-89c0-34d8efa68146)

![Captura de tela 2024-08-19 202813](https://github.com/user-attachments/assets/e389e72a-7e97-4404-882c-d25ebb7d9656) (resultado da automatização do script)

### Versionamento

```
cd /usr/local/bin
sudo git init
sudo git add check_apache.sh
sudo git commit -m "Adicionado script de monitoramento do Apache"
```
![Captura de tela 2024-08-19 203756](https://github.com/user-attachments/assets/29c1d797-f0c5-4035-b55c-caa17a801f27)

![Captura de tela 2024-08-19 203824](https://github.com/user-attachments/assets/92535aa6-baab-4631-b45f-4733166e12c8)

![Captura de tela 2024-08-19 203836](https://github.com/user-attachments/assets/f6a76571-1756-4fbf-acdd-b0ce8e86b7c1)


