# set_up_splash_4_scrapy
Set up of splash scrapy docker

Dans ce cas, voici ce que tu dois faire :  

### 1. **Installer et exécuter Splash avec Docker**  
Exécute ces commandes :  
```bash
docker pull scrapinghub/splash
docker run -p 8050:8050 scrapinghub/splash
```
Splash sera accessible à `http://localhost:8050`.

### 2. **Installer scrapy-splash dans ton environnement virtuel**  
Dans ton environnement virtuel où tourne Scrapy, installe `scrapy-splash` :  
```bash
pip install scrapy-splash
```

### 3. **Configurer Scrapy pour utiliser Splash**  
Dans `settings.py` de ton projet Scrapy, ajoute :  
```python
# Ajouter Splash au middleware des téléchargements
DOWNLOADER_MIDDLEWARES = {
    'scrapy_splash.SplashCookiesMiddleware': 723,
    'scrapy_splash.SplashMiddleware': 725,
    'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware': 810,
}

# Activer le cache de Splash pour améliorer les performances
SPIDER_MIDDLEWARES = {
    'scrapy_splash.SplashDeduplicateArgsMiddleware': 100,
}

# Utiliser Splash pour la déduplication des requêtes
DUPEFILTER_CLASS = 'scrapy_splash.SplashAwareDupeFilter'

# Si tu utilises Scrapy avec un backend d'indexation
HTTPCACHE_STORAGE = 'scrapy_splash.SplashAwareFSCacheStorage'

# URL du serveur Splash
SPLASH_URL = 'http://localhost:8050'
```

### 4. **Utiliser Splash dans ton Spider**  
Dans ton spider, utilise `SplashRequest` au lieu de `Request` :
```python
import scrapy
from scrapy_splash import SplashRequest

class MySpider(scrapy.Spider):
    name = "my_spider"
    start_urls = ["https://example.com"]

    def start_requests(self):
        for url in self.start_urls:
            yield SplashRequest(url, self.parse, args={'wait': 2})

    def parse(self, response):
        # Extraire les données ici
        pass
```

Splash permet d’exécuter JavaScript, donc si un site charge du contenu dynamiquement, cette config devrait fonctionner.

Tu veux une config plus poussée avec des scripts Lua pour Splash ?
