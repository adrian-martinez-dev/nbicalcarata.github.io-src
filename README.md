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
or
```
make publish
```
### Update output folder submodule ###
```
git rm --cached output
git rm output
git submodule add git@github.com:nbicalcarata/nbicalcarata.github.io.git output
```
### Deploy  ###
```
make deploy
```
