# Try Hack Me - IDE

**Maquina: [IDE](https://tryhackme.com/room/ide)** 

**Categorias:** Enumeration, public exploit, privilege escalation, ftp  
**Dificuldade:** Fácil

## $IP = ip da maquina

## 1: Enumeração

Após a verificar que a maquina iniciou, executamos o nmap realizando um scan completo (nmap $IP -p-).

![Screenshot_2021-11-11_20_10_28](https://user-images.githubusercontent.com/32500664/141431465-63e9d447-a12a-48c5-80fc-43994a2f56ae.png)

Seguindo o scan nas 4 portas encontradas (21, 22, 80 ,62337) utilizando scripts nmap. ([Lista de Scripts nmap](https://nmap.org/nsedoc/)) (21, 22, 80 ,62337):

## Vemos que um script detectou que é possivel se logar como anonymous

![InkedScreenshot_2021-11-11_21_00_22_LI](https://user-images.githubusercontent.com/32500664/141433252-aa447938-c761-4861-beea-5f3759026026.jpg)

![imagem_2021-11-12_052952](https://user-images.githubusercontent.com/32500664/141435523-bfcee4b4-9ca9-4315-8e2b-cb0b518e77ce.png)

## Ao logar encontramos:

![Screenshot_2021-11-11_21_25_21](https://user-images.githubusercontent.com/32500664/141433697-bfefe08f-eb36-4261-b010-e4dc63d90dd0.png)

![Screenshot_2021-11-11_21_25_29](https://user-images.githubusercontent.com/32500664/141433726-06dd1a67-d45a-4167-b392-2343977a85a3.png)

Temos 2 possiveis nomes de usuários - Drac e John - e sabemos que John tem uma senha "default", mas aonde eu uso isso ?

## 2. Codiad
A pagina http rodando na porta 62377 

![image](https://user-images.githubusercontent.com/32500664/141434714-1a5c7132-3cd0-4e74-8153-7ce53b71ccf8.png)

Tentando possiveis senhas defaults com os usuários encontrados antes, consigo logar com o usuário jhon usando a senha password.

![Screenshot_2021-11-11_21_31_35](https://user-images.githubusercontent.com/32500664/141436391-68241763-25ef-48ef-864d-f8ae91459086.png)

# Procurando Exploits e Executando.

Normalmente você pode usar o burp ou o wappalyzer para detectar a versão das tecnelogias usadas em websites, porém, no caso o próprio titulo do site é o nome e a versão. Utilize o comando searchsploit para buscar exploits publicos para essa aplicação

![Screenshot_2021-11-11_22_56_50](https://user-images.githubusercontent.com/32500664/141437088-65b3b9e3-fbf4-4215-a8b1-d995f12e76b6.png)

O exploit que vamos usar é o primeiro da lista e ao executalo temos a seguinte mensagem:

python exploit.py [URL] [USERNAME] [PASSWORD] [IP] [PORT] [PLATFORM]

executando o exploit.

![InkedScreenshot_2021-11-11_23_22_59_LI](https://user-images.githubusercontent.com/32500664/142223593-0cd11b3a-fe64-4234-8a7a-95cf6c0d9d5d.jpg)

## Estamos dentro 😎
![Screenshot_2021-11-11_23_23_05](https://user-images.githubusercontent.com/32500664/141437566-b596b513-5db5-4cfe-950b-9457a615ccf4.png)

## Buscando as Flags

Procurando a flag user.txt vamos olhar as pastar dos usuários em /home. Ao entrar na pasta do usuário drac damos de cara com a flag, porém....

![31321](https://user-images.githubusercontent.com/32500664/141438196-444580d7-6230-4200-b5da-0c94fe327b43.png)

Como não temos permissão para ler o arquivo vamos olhar em volta.
Podemos olhar o bash_history do usuário drac pra ver oque ele anda executando e.. BUM!

![Screenshot_2021-11-11_20_11_235](https://user-images.githubusercontent.com/32500664/141438716-b24a9ba0-8962-4215-8a3e-2f66d8297044.png)

Mudando de usuário utilizando o comando su drac com a senha encontrada.

![InkedScreenshot_2021-11-11_23_34_35_LI](https://user-images.githubusercontent.com/32500664/141439281-d44ef266-e6cf-46b4-bd29-53390a26aafe.jpg)

## Escalando privilégios. 

Fazendo o upload de um script que procura formas de escalar privilégios em ambiente linux [linpeas](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS).

Com o script na minha maquina inicio um servidor http utilizando python com o seguinte comando:

![minha maq](https://user-images.githubusercontent.com/32500664/141440269-f411c57a-0a20-4d63-b131-9cd1c5e31b4c.png)

Na maquina da vitima utilizo a sequencia de comandos curl para pegar o conteudo da url e utilizo o '|' para fazer com que o conteudo da url rode em outro comando, que no caso é no bash da maquina da vitima:

![maq vit](https://user-images.githubusercontent.com/32500664/141440302-0e53a1bb-a434-405b-a14f-2737ea3ca7e7.jpg)

Retorno dos comandos:

![Screenshot_2021-11-11_23_40_46](https://user-images.githubusercontent.com/32500664/141441250-aa434576-c7d2-46dc-835f-f1b3c3d33ac3.png)

Linpeas trouxe algo interessante...

![Screenshot_2021-11-11_23_41_25](https://user-images.githubusercontent.com/32500664/141441395-ed3e5667-d471-403b-9529-897e09777881.png)

![Screenshot_2021-11-11_23_44_43](https://user-images.githubusercontent.com/32500664/141441518-3cdadc27-6e8d-40ce-b297-ef34226c494e.png)

O arquivo do vsftpd.service nos permite configurar um comando que será feito após o seu inicio.
Sendo assim podemos inserir um comando dentro do arquivo e reiniciar o serviço para força-lo a rodar o comando.

Inserindo o comando dentro do arquivo.

![image (2)](https://user-images.githubusercontent.com/32500664/141442137-a91bf16f-6b3a-4fb9-8958-6987a6c5515b.png)

Após fazer a modificação execute os seguintes comandos para reiniciar o serviço.

![image (3)](https://user-images.githubusercontent.com/32500664/141442519-e140b96c-e519-447a-b6a9-72700b06201a.png)

## Tudo nosso nada deles.

![image (5)](https://user-images.githubusercontent.com/32500664/141442581-9bbda547-c9c9-42f9-b4ef-17d53200403a.png)

Esse é o meu primeiro write-up espero que tenha ajudado alguem.



