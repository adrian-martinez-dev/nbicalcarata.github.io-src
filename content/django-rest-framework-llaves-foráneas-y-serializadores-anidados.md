Title: Django Rest Framework: Llaves foráneas y serializadores anidados
Slug: django-rest-framework-llaves-foráneas-y-serializadores-anidados
Date: 2020-05-09 00:02:16
Tags: Django, DRF

Con Django Rest Framework, es relativamente rapido crear apis y levantar servicios para realizar operaciones CRUD (Create, Read, Update y Delete).

Para hacer el guardado de objectos existentes, me puse a la tarea de investigar la posibilidad de que el servicio reciba una llave foránea en la peticion POST, y que al hacer GET me devuelva el objeto relacionado pero con sus campos completos (el comportamiento por defecto corresponde a devolver solo la llave primaria).

Entonces teniendo los siguientes modelos:

```python
class Album(models.Model):
    album_name = models.CharField(max_length=100)
    artist = models.CharField(max_length=100)


class Track(models.Model):
    album = models.ForeignKey(Album, related_name='tracks', on_delete=models.CASCADE)
    order = models.IntegerField()
    title = models.CharField(max_length=100)
    duration = models.IntegerField()
```

Y con sus correspondientes Serializers, sobreescribimos el metodo to_representation:

```python
class AlbumSerializer(ModelSerializer):
    class Meta:
        model = Album
        fields = '__all__'


class TrackSerializer(ModelSerializer):
    class Meta:
        model = Track
        fields = '__all__'

    def to_representation(self, instance):
        response = super().to_representation(instance)
        response['album'] = AlbumSerializer(instance.album).data
        return response
```

El cual nos devolverá una instancia album con todos sus campos en el objeto json.

Referencias:

[Stackoverflow](https://stackoverflow.com/a/52246232/4032361)
