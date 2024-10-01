# Política de contraseñas

## Contexto
Por defecto, la mayoría de sistemas operativos, vienene con una política de contraseña muy laxa, una contraseña segura presenta una barrera más al atacante, que en su intento de acceder al sistema, indudablemete dejará más rastro y en algunos casos a provocar el abandondo de ataque.

## Objetivos
* Entender el sistema de contraseñas del sistema operativo linux
* Entender el PAM (Plugable authentication Module)
* Instalar y configurar la libreria _pwquality_
* Comprobar y verificar que se ha llevado la práctica de forma correcta
* Documentar la práctica.

## Realizaión de la practica

### Conceptos

PAM:

Sistema de contraseñas de Linux: Todos las contraseñas de los usuarios, en el sistema operativo Linux, se guardan en /etc/shadow. 

### 1. Instalar y configurar la librería pwquality

Instalamos la librería con el comando: 
```
sudo apt install libpam-pwquality
```

Verificamos que en **/etc/pam.d/common-password** sale la línea: 
```
password        requisite                       pam_pwquality.so retry=3
```

Y para establecer póliticas de contraseñas, editamos los paremetros de **/etc/security/pwquality.conf**

En esta practica vamos a tener 3 políticas a configurar:
- Política débil (6 caracteres min, *)
- Política media (8 caracteres min, [0-9], [A-Z], *)
- Política extrema (10 caracteres min, [0-9][A-Z][a-z][#_@...])

Para la pólitica débil, configuramos los parametros siguientes (pwscore 38-50):

```
minlen=6 (Esto asegura que la contraseña tiene que contener mnínimo 6 caractéres)
dcredit= -1 (Debe contener un digito mínimo, el -1 representa que debe aparcer 1 digito mínimo)
ucredit= -1 (Debe contener una letra en Mayúscula)
ocredit= -1 (Debe contener almenos 1 caracter que no sea una letra)
maxcredit = 0 (No se pueden usar el mismo caracter consecutivamente)
```
Parametros a configurar para la política media (pwscore 50 - 80):

```
minlen= 8 (8 carateres mínimo)
dcredit= -1 
ucredit= -1 
ocredit= -1 
maxcredit = 0 
```
Y por último, los parametros de la la poítica extrema son (pwscore >80):


```
minlen= 10 (8 carateres mínimo)
dcredit= -1 
ucredit= -1 
ocredit= -1 
maxcredit = 0 
```

Resultados: 

| Politica | Score | l.min | Ejemplos |
|----------|-------|-------|----------|
| No valida|  <35  |  -    | MyPassword |
|   Débil  | 38-50 |   6   | ~ñl8rY |
|  Media   | 50-80 |   8   | @9tYuLn_i |
| Extrema  |  >80  |  10   | 9.dfj3KF8dn_f$f

### Comprobacion:

Para comprobar que funciona, con la política extrema configurado y activado, he creado el usuario "test" con una contraseña, inicio sesion como "test" y ejecuto el comando **passwd** para cambiar la contraseña, cuando me pide la nueva contraseña que quiero utilizar, introduzco una contraseña que no cumple con los requisitos y no la coje, con esto me permite saber que las restricciones funcionan:

```
test@generic-sad:/home/vagrant$ passwd
Changing password for test.
Current password: 
New password: 
BAD PASSWORD: The password is shorter than 10 characters
New password: 
BAD PASSWORD: The password contains less than 1 uppercase letters
New password: 
BAD PASSWORD: The password contains less than 1 digits
passwd: Have exhausted maximum number of retries for service
passwd: password unchanged
test@generic-sad:/home/vagrant$ 
```