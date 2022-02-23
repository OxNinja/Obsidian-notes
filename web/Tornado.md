#web #python #templating #io #async
# Tornado
> https://www.tornadoweb.org

Mieux que [[Flask]] car :
* Pas de race condition
* Gere bien l'In/Out
Mais sinon un peu galere a utiliser

Comparaison avec [[Flask]] : [https://codeahoy.com/compare/flask-vs-tornado](https://codeahoy.com/compare/flask-vs-tornado "https://codeahoy.com/compare/flask-vs-tornado")

En gros Tornado c'est mieux pour les gros projets car meilleure optimisation des threads et de l'asynchrone.

Tornado peut aussi gérer l'interaction avec une base de données via [[SQLAlchemy]] par example