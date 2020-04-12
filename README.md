# nbicalcarata.github.io-src

#### Install pellican ####

```
python -m venv venv
source venv/bin/activate
pip install pelican Markdown pelican_gist
```

#### Run server ####

```
pelican --listen --autoreload

```
or
```
make devserver
```
#### Preview ####
```
http://localhost:8000/
```
#### New post ####
```
make newpost NAME='Post name'
```
#### Site generation ####
```
pelican content
```

