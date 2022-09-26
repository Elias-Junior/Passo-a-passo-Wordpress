# Passo-a-passo-Wordpress
 criando um ambiente WordPress web

---

Apps necessarios 

- Filezilla
- Putty
- algum navegador 

---

- Antes de tudo é importante lembrar que temos que fazer um backup do servidor na digitalocean para se caso houver algum problema ter mais facilidade para resolver.

- E para isso criaremos um snapshot do servidor que queremos.

Passo 1
- Criar um banco de dados para a empresa que você deseja usando putty:
    - Utilize a “digitalocean” para pegar o IP e entrar no terminal do putty e começar a criar .
        - Alguns comandos que vc pode usar utilizar:
        - `mysql -u root -p`  para acessar a área de Mysql
        - `show databases;` para acessar a lista de banco de dados já criados
        - `create database nomedobancodedados;` para crirar o banco de dados 
            - Altere o nomedobancodedados para o nome que deseja colcoar no banco de dados que está prestes a criar
        - para outros comandos acesse o link abaixo:
        - drop database `nomedobancodedados`; caso precise apagar algum banco de dados
        - [https://www.diegobrocanelli.com.br/mysql/comandos-basicos-mysql-no-terminal/#:~:text=Abra o terminal vá ao diretório onde encontra-se o,importação será realizada com sucesso](https://www.diegobrocanelli.com.br/mysql/comandos-basicos-mysql-no-terminal/#:~:text=Abra%20o%20terminal%20v%C3%A1%20ao%20diret%C3%B3rio%20onde%20encontra%2Dse%20o,importa%C3%A7%C3%A3o%20ser%C3%A1%20realizada%20com%20sucesso).

Passo 2
- Depois de criar o banco de dados, vamos criar o usuario `usuario`
    - Para isso, ainda dentro do mysql utilize o seguinte codigo:
        - E lembre de colocar uma `senha` segura para o seu `usuario`.
    - Para criar o usuario: `CREATE USER 'usuario'@'%' IDENTIFIED BY 'suasenhasegura';`
    
    - Para dar o acesso ao usuario: `GRANT ALL PRIVILEGES ON *.* TO 'usuario'@'%';`
    
    - Para ver a lista de usuarios: `SELECT user FROM mysql.user;`
    
    - para excluir usuarios caso necessario `DROP USER 'usuario'@'localhost';`
    
    - salvar a "suasenhasegura" no arquivo da digitalocean na pasta /root no FTP

    - no terminal utilize `exit;` para sair do mysql, e continuar o passo a passo.
    
    - para mais informações segue o link abaixo:
    - [https://www.hostinger.com.br/tutoriais/listar-usuarios-mysql-linux#:~:text=Basta usar o comando abaixo,os usuários criados no MySQL](https://www.hostinger.com.br/tutoriais/listar-usuarios-mysql-linux#:~:text=Basta%20usar%20o%20comando%20abaixo,os%20usu%C3%A1rios%20criados%20no%20MySQL).
    - [https://www.digitalocean.com/community/tutorials/como-criar-um-novo-usuario-e-conceder-permissoes-no-mysql-pt](https://www.digitalocean.com/community/tutorials/como-criar-um-novo-usuario-e-conceder-permissoes-no-mysql-pt)
    - [https://linuxeprogramacao.blogspot.com/2013/07/criando-um-banco-de-dados-mysql.html](https://linuxeprogramacao.blogspot.com/2013/07/criando-um-banco-de-dados-mysql.html)
    - [https://receitasdecodigo.com.br/banco-de-dados/exibindo-informacoes-do-banco-de-dados,-tabelas-e-colunas-do-mysql#:~:text=use databasename%3B,os bancos de dados existentes](https://receitasdecodigo.com.br/banco-de-dados/exibindo-informacoes-do-banco-de-dados,-tabelas-e-colunas-do-mysql#:~:text=use%20databasename%3B,os%20bancos%20de%20dados%20existentes).
    
Passo 3
- Dentro do no FTP entrar com o ip do servidor e criar uma pasta dentro do diretorio  /var/www como o nome que desejar.
    - Após criar uma pasta com o nome por exemplo pastawordpress ficará assim "/var/www/pastawordpress".
    - Subir o wordpress baixado da Internet para dentro da pasta criada e aguardar a transferência.
    - Em seguida dar permissões para a pastawordpress que acabamos de criar
        - `find /var/www/pastawordpress -type d -exec chmod 755 {} \;`
        - `find /var/www/pastawordpress -type f -exec chmod 644 {} \;`
    - Agora vamos alterar o proprietario/grupo da pasta, alterar html pelo nome da pasta que quer alterar:
        - `sudo chown -R www-data:www-data /var/www/pastawordpress`
    
passo 4
- Colocar um subdominio utilizando a cloudflare, na aba dns o ip do servidor que esta utilizando, colocar o sub-dominio que deseja, por exemplo: "beta-blog.meu.site".               

- Seguindo devemos ir no diretorio /etc/apache2/sites-available e criar um arquivo .conf para validação da url.

dados do arquivo:

---

# Added to mitigate CVE-2017-8295 vulnerability

UseCanonicalName On

<VirtualHost *:80>
ServerAdmin webmaster@localhost

    ServerName beta-blog.meu.site
    ServerAlias www.beta-blog.meu.site

    DocumentRoot /var/www/pastawordpress

<Directory /var/www/pastawordpress/>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>

---

Em seguida alterar os dados das linhas para o nome que deseja:

ServerName beta-blog.meu.site
ServerAlias www.beta-blog.meu.site
DocumentRoot /var/www/pastawordpress
<Directory /var/www/pastawordpress/>

- salvar o arquivo com o "nome que deseja.conf" e enviar via FTP para o diretorio /etc/apache2/sites-avaliable
    - caso tenha feito no bloco de notas conferir se o arquivo foi salvo com o final .conf e não .txt por exemplo.

Passo 5
- Com todas essas alterações feitas voltamos no terminal para ativar o arquivo que acabou de ser modificado e salvo.
    - `sudo su` para conceder acesso total
    - caso eu queira copiar uma pasta pelo terminal eu utilizo o comando `sudo cp /diretorio /diretorio` → esse comando pega o diretorio e copia e manda para o outro diretorio
    - posso usar o `cd /diretorio`  para acessar a pasta que eu quero dentro do terminal
    - `sudo nano /diretorio` esse comando irá fazer com que eu consiga acessar o arquivo e editar ele dentro do terminal caso eu não tenha editado por fora e subido via FTP.

Passo 6
- usar o comando `sudo a2ensite nomedoarquivo.conf` para fazer valer a validação.

- usar o comando: `service apache2 restart` para reiniciar e fazer valer todas as alteções.



- entrar na URL criada e configurar dados necessário para o acesso ao wordpress.


- instalar plugin  Really Simple SSL
    - na sequência habilitar o "https://" no site e em seguida ativar o plugin

- Caso o wordpress esteja em ingles alterar o idioma no menu lateral: configurações → geral → idioma do site

Fim.
