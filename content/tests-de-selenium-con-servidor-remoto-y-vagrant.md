Title: Tests de Selenium con servidor remoto y Vagrant
Slug: tests-de-selenium-con-servidor-remoto-y-vagrant
Date: 2017-07-31 18:11:04
Tags: behat, selenium

## Servidor remoto ##
Con el entorno de desarrollo (neovim + tmux) ejecutándose en servidor remoto, habilitamos en éste el ssh tunneling de la siguiente forma:

En el archivo `/etc/ssh/ssh_config` modificamos la siguiente linea:

```text
GatewayPorts yes
```

Reiniciamos el servicio ssh.

```text
sudo systemctl restart sshd
```

Abrimos dos terminales en la maquina local, en la primera, iniciamos el ssh tunneling.

```text
ssh -R 4444:localhost:4444 usuario@<ip-servidor>
```

En la segunda ejecutamos Selenium con Chromedriver.

```text
java -Dwebdriver.chrome.driver='/home/adrian/chromedriver' -jar selenium-server-standalone-3.4.0.jar
```

En nuestro `behat.yml`, `base_url` debe apuntar a la IP de nuestro servidor y al puerto que tenemos abierto, donde se esta ejecutando nuestra aplicación de laravel con `php artisan serve`.

```text
php artisan serve --host=<ip_servidor> --port=<puerto>
```

En `behat.yml`

```text
base_url: http://<ip_servidor>:<puerto>
```

## Vagrant ##

Hacemos el ssh tunneling, pero sobre la dirección IP de homestead.

```text
ssh -R 4444:localhost:4444 vagrant@192.168.10.10
```
En una terminal ejecutamos selenium y chromedriver como se indicó anteriormente:

```text
java -Dwebdriver.chrome.driver='/home/adrian/chromedriver' -jar selenium-server-standalone-3.4.0.jar
```

En `behat.yml`, en `base_url` va indicada la url de nuestra aplicación, y en la sección de `selenium`, `localhost:4000/wd/hub` como si se ejecutara en la maquina virtual.

### behat.yml ###
```
default:
    translation:
        locale: en
    extensions:
        Laracasts\Behat: ~
            # env_path: .env.behat
        Behat\MinkExtension:
            base_url: 'http://interpos.app'
            default_session: laravel
            laravel: ~
            selenium2:
              wd_host: http://localhost:4444/wd/hub
              browser: chrome
```

En mi caso, usando el plugin [vim-test](https://github.com/janko-m/vim-test), `vagrant ssh` no me permitía mandar el comando a ejecutar directamente a la maquina virtual, así que usando la función VagrantTransform (adaptada del ejemplo de la documentación) lo conseguí satisfactoriamente:

```vim
function! VagrantTransform(cmd) abort
  let l:vagrant_project = fnamemodify(getcwd(),":t")
  return "cd ~/Homestead && ssh -tt vagrant@192.168.10.10 ".shellescape('cd /home/vagrant/sitios/'.vagrant_project.'; '.a:cmd)
endfunction

let g:test#custom_transformations = {'vagrant': function('VagrantTransform')}
let g:test#transformation = 'vagrant'
```

