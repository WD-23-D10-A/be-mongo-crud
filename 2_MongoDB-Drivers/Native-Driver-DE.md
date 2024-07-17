# Arbeiten mit dem nativen MongoDB-Treiber

Was wir bis jetzt gelernt haben, ist so ziemlich MongoDB in seiner reinsten Form in einer isolierten Umgebung. Aber das nützt uns nichts, denn wir wollen Anwendungen erstellen, die Datenbanken enthalten.

Wir müssen also lernen, wie wir alle MongoDB-Datenbanken in eine Node.js-Anwendung integrieren können.

Wenn Sie eine Node-Anwendung erstellen, die sich mit einer MongoDB-Datenbank verbinden muss, haben Sie im Wesentlichen zwei Möglichkeiten zur Auswahl.

Eine ist die Verwendung des nativen MongoDB-Treibers, über den wir gleich noch sprechen werden.

Eine andere ist die Verwendung eines ODM oder eines Object Document Mappers namens Mongoose, den wir etwas später vorstellen werden.

Die beliebteste Art, mit MongoDB und Node.js zu arbeiten, ist die Verwendung dieses Pakets namens mongoose. Der Grund dafür ist, dass es den Code, der für die Arbeit mit der MongoDB-Datenbank erforderlich ist, erheblich vereinfacht und reduziert. Aber ich möchte Ihnen zuerst zeigen, wie der native MongoDB-Treiber funktioniert, und dann werden wir die Verwendung von Mongoose durchgehen, damit Sie selbst sehen können, wie viel einfacher es ist, Mongoose einzubinden, und wie sehr es Ihr Leben als Entwickler verbessert. Aber fangen wir erst einmal an und sehen uns an, wie wir den nativen MongoDB-Treiber verwenden.

Zunächst möchte ich auf die [MongoDB-Dokumentation](https://www.mongodb.com/docs/drivers/) für new with atlas verweisen und wir werden [dies](https://mongodb.github.io/node-mongodb-native/3.5/quick-start/quick-start/) ohne atlas verwenden

Übersetzt mit DeepL.com (kostenlose Version)
Und hier gehen wir zur Registerkarte mit der Bezeichnung Treiber. Auf der linken Seite können Sie sehen, dass es eine ganze Reihe von Treibern für verschiedene Sprachen gibt.

Der Treiber ermöglicht es MongoDB, mit unserer Anwendung zu interagieren. Je nachdem, in welcher Sprache Ihre Anwendung entwickelt wurde, müssen Sie einen anderen Treiber verwenden. In unserem Fall entscheiden wir uns für Node.js und sehen uns die aktuelle Dokumentation an. Dort wird Schritt für Schritt beschrieben, wie Sie mit dem nativen MongoDB-Node.js-Treiber beginnen können.

Im Wesentlichen versuchen wir also, unsere MongoDB-Datenbank mit unserer Node.js-Anwendung zu verbinden. Und das hier wird die Syntax und der Code sein, und wir werden den Treiber installieren, um das zu ermöglichen.

Das Wichtigste zuerst: Wir brauchen ein neues Projekt.

```
mkdir fruits
```

```
cd fruits
```

```
touch app.js
```

```bash
npm init -y
```

MongoDB-Treiber installieren

```
npm i mongodb@3.5
```

Jetzt müssen wir nur noch unsere app.js in VSC öffnen und mit der Entwicklung einer Node-JS-Anwendung beginnen, die mit einer MongoDB-Datenbank zusammenarbeitet.

kopieren Sie dies:
```js
//jshint esversion:6

const MongoClient = require('mongodb').MongoClient;
// assert ist im Grunde ein Test
// assert, das nur unsere Dateneingabe und unsere Verbindung zur MongoDB-Datenbank validiert.
const assert = require('assert');

// Verbindungs-URL
const url = 'mongodb://localhost:27017';

// Datenbankname
const dbName = 'fruitsDB';

// Einen neuen MongoClient erstellen
const client = new MongoClient(url);

// Verwenden Sie die connect-Methode, um sich mit dem Server zu verbinden
client.connect(function(err) {
  assert.equal(null, err);
  console.log(„Verbindung zum Server erfolgreich hergestellt“);

  const db = client.db(dbName);

  client.close();
});

```

Der Grund, warum ich Sie hier durchführe und den Code erkläre, ist, dass in der Realität die meisten Entwickler, die mit Node und MongoDB arbeiten, selten den nativen MongoDB-Treiber verwenden werden. Das liegt aber nicht daran, dass er nicht gut ist, er funktioniert und erlaubt eine Menge Personalisierung und man kann bis ins Detail gehen und seine MongoDB-Datenbank mit einem hohen Maß an Kontrolle einrichten und verwenden.

Nun, da wir dies erstellt haben, können wir unsere app.js mit node app.js ausführen und sehen, dass wir einen Fehler erhalten. Er lautet: MongoNetworkError failed to connect to server. you have to run mongod again

OK, nachdem wir nun erfolgreich eine Verbindung zu unserem Mongo-Server hergestellt haben, müssen wir als nächstes einige Daten zu unserer Datenbank hinzufügen.

Wie machen wir das also?

Nun, es geht zurück zur Dokumentation und der nächste Teil ist natürlich das Einfügen eines Dokuments.


```js
const insertDocuments = function(db, callback) {
  // Abrufen der Dokumentensammlung
  const collection = db.collection('Dokumente');
  // Einfügen einiger Dokumente
  collection.insertMany([
    {a : 1}, {a : 2}, {a : 3}
  ], function(err, result) {
    assert.equal(err, null);
    assert.equal(3, result.result.n);
    assert.equal(3, result.ops.length);
    console.log(„Es wurden 3 Dokumente in die Sammlung eingefügt“);
    callback(result);
  });
}
```

Und hier unten können Sie sehen, dass die nächsten Zeilen der Validierung gewidmet sind. Sie haben also einen ganzen Haufen von Asserts hinzugefügt. Und wenn wir sie Zeile für Zeile durchgehen,

```js
function(err, result) {
  //hier steht validate, um sicherzustellen, dass es keine Fehler gibt, wenn wir unser Dokument einfügen.
    assert.equal(err, null);
    //sichert, dass wir drei Ergebnisse haben, die in unsere Sammlung eingefügt werden. 
    assert.equal(3, result.result.n);
    assert.equal(3, result.ops.length);
    // Und wenn das der Fall ist, werden wir die drei in die Sammlung eingefügten Dokumente protokollieren. 
    console.log(„Es wurden 3 Dokumente in die Sammlung eingefügt“);
    // Der nächste Teil besteht darin, diese Funktion innerhalb unserer Funktion „Dokumente einfügen“ auszuführen, und der Client wird angezeigt.
    callback(result);
  });

```

in client.connect:

```js
client.connect(function (err) {
  assert.equal(null, err);
  console.log(„Verbindung zum Server erfolgreich hergestellt“);

  const db = client.db(dbName);

 insertDocuments(db, () => {
  client.close();
 })
});
```
Jetzt klicken wir auf Speichern und führen unseren Code erneut mit node app.js aus. Wie Sie sehen, haben wir uns erfolgreich mit dem Server verbunden und drei Dokumente in die Sammlung eingefügt.

Führen Sie nun in Ihrem Mongo-Terminal show dbs und fruitsDB aus.

Führen Sie dies aus, um zu überprüfen, ob alles funktioniert hat

```use fruitsDB db.fruits.find()```und wenn alles korrekt war, sollten Sie brandneue Einträge mit den angegebenen Früchten haben

Der nächste Schritt ist nun, wie bekommen wir unsere app.js dazu, all diese Einträge zu finden? Wie lesen wir unsere Daten innerhalb unserer Node.js-App?

Nun, das ist nur einen kurzen Bildlauf entfernt.

Der nächste Teil ist also, alle Dokumente zu finden, und wir fügen dies einfach in unseren Code ein:

```js
const findDocuments = function(db, callback) {  // Abrufen der Dokumentensammlung  const collection = db.collection('Dokumente');
  // Einige Dokumente finden
  collection.find({}).toArray(function(err, docs) {
    assert.equal(err, null);
    console.log(„Die folgenden Datensätze wurden gefunden“);    console.log(docs)    callback(docs);  });}
```

client.connect ändern:

```js
client.connect(function (err) {  assert.equal(null, err);  console.log(„Verbindung zum Server erfolgreich hergestellt“);

const db = client.db(dbName); findDocuments(db, function() {
      client.close();
    });});
```

Sie sehen also, dass wir die folgenden Datensätze gefunden haben, und das, was hier ausgedruckt wird, ist ein Array von Javascript-Objekten, und Sie können sehen, dass es sehr ähnlich aussieht wie das, was wir in der Mongo-Shell haben, aber was hier protokolliert wird, sind tatsächlich die Dokumente aus unserer MongoDB, aber hier haben wir tatsächlich unsere Javascript-Objekte. Wenn wir nun also Zugriff auf diese lebenden Javascript-Objekte in einem Array in unserer app.js haben, können wir unseren Javascript-Code und Node.js verwenden, um damit zu tun, was wir wollen.

Wie Sie sehen können, ist dieser Code sehr wortreich, und ein großer Teil davon ist einfach nur Standardcode, den Sie jedes Mal hinzufügen müssen, wenn Sie den nativen MongoDB-Treiber verwenden. Und für manche Leute, vor allem, wenn sie schnell Anwendungen entwickeln wollen, kann das ein bisschen lästig sein.

Deshalb haben wir mongoose und es wird den Code, den wir hier haben, enorm vereinfachen und wir werden durchgehen, wie man mongoose benutzt, um das Leben bei der Entwicklung von Mongo- und Node-Anwendungen so viel einfacher zu machen.