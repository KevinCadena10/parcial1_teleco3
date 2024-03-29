# Autenticacion PAM

## Instalacion
Para empezar con la cofiguracion de nuestro archivo PAM se hace la instalalcion de la autenticacion

```bash
sudo apt-get update
sudo apt-get install apache2 libapache2-mod-authnz-pam
```
Despues de la instalacion se creo la carpeta archivos_privados de la sigiente manera
```bash
sudo mkdir /var/www/html/archivos_privados
```
Luego de eso se le cambio la propiedad la carpeta por la de www-data
```bash
sudo chown www-data.www-data /var/www/html/archivos_privados -R
```
Cuando se le da ls -la en el directorio, el usuario denomidado www-data tendra permisos sobre este.

Luego se edito la configuracion por default de los sitios de apache con el siguiente commando
```bash
sudo vim /etc/apache2/sites-enabled/000-default.conf
```
Al final de el archivo antes de que cerrar la etiqueta virtualhost debe quedar asi
```bash
<VirtualHost *:80>
        . . . cofiguracion anterior

<Directory "/var/www/html/archivos_privados">
	AuthType Basic
	AuthName "area privada"
	AuthBasicProvider PAM
	AuthPAMService apache
	Require valid-user
</Directory>
</VirtualHost
```
Luego de eso se creo el archivo de configuracion llamado apache asi
```bash
sudo vim /etc/pam.d/apache
```
Al principio se coloco las siguientes lineas de codigo
```bash
auth required pam_unix.so
account required pam_unix.so
```
Despues se habilito el grupo de SHADOW de la siguiente manera
```bash
sudo groupadd shadow
sudo usermod -a -G shadow www-data
sudo chown root:shadow /etc/shadow
sudo chmod g+r /etc/shadow
```
Al terminar se reinicio el servicio de apache con el comando
```bash
sudo service apache2 restart
```
se hizo una pequeña prueba con un usuario admin, por lo tanto se creo asi
```bash
sudo adduser admin
```
se le puso de contraseña 123qwe

Se realizaron las pruebas y funcionaba, se crearon mas usuarios con el mismo comando entonces creamos a kevin, geral, 
victoria, carlos y mateo con todos y cada uno de los usuarios se pudo loguear, luego de eso se paso a realizacion de
la denegacion de usuarios, primero se creo una lista en la carpeta de apache.
```bash
sudo vim /etc/apache2/denied_user
```
Luego de eso se modifico el archivo de configuracion de apache, para ello se hizo
```bash
sudo vim /etc/pam.d/apache
```
luego se modificaron las lineas de codigo por
```bash
auth       sufficient     pam_listfile.so item=user sense=deny file=/etc/apache2/denied_users
auth       sufficient     pam_auth.so
account    required      pam_unix.so
password   sufficient     pam_unix.so nullok
```
Con todo lo anterior se realizo la configuracion de la autenticacion PAM

# Ngrok

## Instalacion
Para el ngrok se realizo la instalacion de este con el siguiente comando para obtener el archivo zip
```bash
wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
```
luego se instalo el unzip para poder obtener el archivo
```bash
sudo apt install unzip
```
despues de eso se desempaqueto
```bash
unzip ngrok-stable-linux-amd64.zip
```
Teniendo el archivo desempaquetado, se realizo puso el sigiente comando
```bash
./ngrok http 8000
```
con ese comando nos aparecio un error que era para poder loguearnos en la pagina de ngrok
luego de eso se procedio a realizar la instalacion usando la ayuda que esta pagina presenta
```bash
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc \
  | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" \
  | sudo tee /etc/apt/sources.list.d/ngrok.list && sudo apt update && sudo apt install ngrok
```
despues de esto, se procedio a darle la clave de acceso con el siguiente comando
```bash
ngrok config add-authtoken 2dMzb5nXWRRfqkmyDqFEbJaDdup_3bZabcvWYcNeR59fJQ7xR
```
y listo con esto pudimos darle los permisos a el ngrok para poder funcionar  
