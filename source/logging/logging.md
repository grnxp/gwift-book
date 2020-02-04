Si on veut propager les logs entre applications, il faut bien spécifier l'attribut `propagate`, sans quoi on s'arrêtera au module sans prendre en considération les sous-modules.

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',
        },
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': os.path.join(SRC_DIR, 'log', 'log.txt'),
        },
    },
    'loggers': {
        'mv': {
            'handlers': ['file'],
            'level': 'DEBUG',
            'propagate': True,
        },
    },
}
```

Par exemple:

```python
'loggers': {
    'mv': {  # Parent
        'handlers': ['file'],
        'level': 'DEBUG',
    },
    'mv.models': { # Enfant
        'handlers': ['console'],
        'level': 'DEBUG',
        'propagate': True,
    }
},
```

Et dans le fichier `mv/models.py`, on a ceci: 

```python
logger = logging.getLogger(__name__)
logger.debug('helloworld');
```

Le log sera écrit dans la console **ET** dans le fichier.
Par contre, si on retire l'attribut `propagate: True` (ou qu'on le change en `propagate: False`), le même code ci-dessus n'écrit que dans la console. Simplement parce que le log associé à un package considère par défaut ses enfants, alors que le log associé à un module pas. [Par exemple](https://docs.djangoproject.com/en/2.1/topics/logging/#examples).