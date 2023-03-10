### List all jokes in a category

Lists all jokes from a certain category.

```endpoint
GET /api/jokes/categories/:category
```

#### Example request

```curl
$ curl http://74.234.41.60:8000/api/jokes/categories/ComputerJokes
```

```javascript
app.get("/api/jokes/categories/:category", (req, res, next) => {
    var sql = 
    `SELECT j.id, joke, category FROM jokes j
     JOIN joke_category jc ON jc.joke_id = j.id
     JOIN categories c ON c.id = jc.category_id 
     WHERE c.category = ?`
    var params = [req.params.category]
    db.all(sql, params, (err, rows) => {
        if (err) {
          res.status(400).json({"error":err.message});
          return;
        }
        res.json({
            "message":"success",
            "data":rows
        })
      });
});
```

#### Example response

```json
{
    "message": "success",
    "data": [
        {
            "id": 1,
            "joke": "Debugging is like being the detective in a crime movie where you are also the murderer.",
            "category": "ComputerJokes"
        },
        {
            "id": 2,
            "joke": "What is the biggest lie in the entire universe? I have read and agree to the Terms & Conditions.",
            "category": "ComputerJokes"
        },
        {
            "id": 5,
            "joke": "There are 10 types of people in the world: those who understand binary, and those who don’t.",
            "category": "ComputerJokes"
        }
    ]
}
```
### Get a joke with id

Lists a joke with given id.

```endpoint
GET /api/jokes/:id
```

#### Example request

```curl
$ curl http://74.234.41.60:8000/api/jokes/5
```

```javascript
app.get("/api/jokes/:id", (req, res, next) => {
    var sql = 
    `SELECT j.id, joke, likes, dislikes, category FROM jokes j
     JOIN joke_category jc ON jc.joke_id = j.id
     JOIN categories c ON c.id = jc.category_id 
     WHERE j.id = ?`
    var params = [req.params.id]
    db.all(sql, params, (err, row) => {
        if (err) {
          res.status(400).json({"error":err.message});
          return;
        }
        res.json({
            "message":"success",
            "data":row
        })
      });
});
```

#### Example response

```json
{
    "message": "success",
    "data": [
        {
            "id": 5,
            "joke": "There are 10 types of people in the world: those who understand binary, and those who don’t.",
            "likes": 3,
            "dislikes": 1,
            "category": "ComputerJokes"
        }
    ]
}
```

### Add a joke to a category

Creates a new joke to a category with category id.

```endpoint
POST /api/joke/category/:id
```

#### Example request

```curl
curl -d "joke={joke}" http://74.234.41.60:8000/api/joke/category/2
```

```javascript
app.post("/api/joke/category/:id", (req, res, next) => {
    var errors = []
    if (errors.length){
        res.status(400).json({"error":errors.join(",")});
        return;
    }
    var data = {
        joke: req.body.joke,
        likes: 0,
        dislikes: 0,
        jokeid: 0
    }
    var sql ='INSERT INTO jokes (joke, likes, dislikes) VALUES (?,?,?)'
    var sql1 ='INSERT INTO jokecategory (category_id, joke_id) VALUES (?,?)'
    var params =[data.joke, data.likes, data.dislikes]
    var params1 = []
    db.run(sql, params, function (err, result) {
        if (err){
            res.status(400).json({"error": err.message})
            return;
        }
        jokesId = this.lastID
        params1.push(req.params.id, jokesId.toString())

        res.json({
            "message": "success",
            "data": data,
            "id" : this.lastID
        })

        db.run(sql1, params1, function (err, result) {
        if (err){
            res.status(400).json({"error": err.message})
            return;
        }

    });

    })

})
```

#### Example request body

```json
{
  "joke": "Why do Java developers wear glasses? Because they can’t C#"
}
```


#### Example response

```json
{
    "message": "success",
    "data": {
        "joke": "Why do Java developers wear glasses? Because they can’t C#",
        "likes": 0,
        "dislikes": 0,
        "jokeid": 0
    },
    "id": 9
}
```
### Create a joke category

Creates a new joke category.

```endpoint
POST /api/joke/category
```

#### Example request

```curl
curl -d "category={category}" http://74.234.41.60:8000/api/joke/category
```

```javascript
app.post("/api/joke/category", (req, res, next) => {
    var errors=[]
    if (!req.body.category){
        errors.push("No category specified");
    }
    if (errors.length){
        res.status(400).json({"error":errors.join(",")});
        return;
    }
    var data = {
        category: req.body.category
    }
    var sql ='INSERT INTO categories (category) VALUES (?)'
    var params =[data.category]
    db.run(sql, params, function (err, result) {
        if (err){
            res.status(400).json({"error": err.message})
            return;
        }
        res.json({
            "message": "success",
            "data": data,
            "id" : this.lastID
        })
    });
})
```

#### Example request body

```json
{
  "category": "Knock Knock"
}
```
#### Example response

```json
{
    "message": "success",
    "data": {
        "category": "Knock Knock"
    },
    "id": 5
}
```
### List all categories

Returns a list of categories.

```endpoint
GET /api/jokes/category/list/all
```

#### Example request

```curl
curl http://74.234.41.60:8000/api/jokes/category/list/all
```

```javascript
app.get("/api/jokes/category/list/all", (req, res, next) => {
    var sql = 
    `SELECT *
     FROM categories`
    var params = [req.params.gategory]
    db.all(sql, params, (err, rows) => {
        if (err) {
          res.status(400).json({"error":err.message});
          return;
        }
        res.json({
            "message":"success",
            "data":rows
        })
      });
});
```

#### Example response

```json
{
    "message": "success",
    "data": [
        {
            "id": 1,
            "category": "BadJokes"
        },
        {
            "id": 2,
            "category": "ComputerJokes"
        },
        {
            "id": 3,
            "category": "FunnyJokes"
        },
        {
            "id": 4,
            "category": "FoodJokes"
        },
        {
            "id": 5,
            "category": "Knock Knock"
        }
    ]
}
```

### Add an existing joke to a categpry

Add an existing joke to a category, both with id

```endpoint
POST /api/jokes/
```

#### Example request

```curl
curl -d "category_id={id}&joke_id={id}http://74.234.41.60:8000/api/joke/jc/add
```

```javascript
app.post("/api/joke/jc/add", (req, res, next) => {
    var errors=[]
    if (!req.body.joke_id){
        errors.push("No id specified");
    }
    if (!req.body.category_id){
        errors.push("No category specified");
    }
    if (errors.length){
        res.status(400).json({"error":errors.join(",")});
        return;
    }
    var data = {
        joke_id: req.body.joke_id,
        category_id: req.body.category_id
    }
    var sql ='INSERT INTO joke_category (joke_id, category_id) VALUES (?,?)'
    var params =[data.joke_id, data.category_id]
    db.run(sql, params, function (err, result) {
        if (err){
            res.status(400).json({"error": err.message})
            return;
        }
        res.json({
            "message": "success",
            "data": data,
            "id" : this.lastID
        })
    });
})
```
#### Example request body

```json
{
  "category_id": 3,
  "joke_id": 1
}
```
#### Example response

```json
{
    "message": "success",
    "data": {
        "joke_id": "1",
        "category_id": "3"
    },
    "id": 10
}
```

### Get random joke

Get a random joke from all jokes.

```endpoint
GET /api/jokes/random/all
```

#### Example request

```curl
$ curl http://74.234.41.60:8000/api/jokes/random/all
```

```javascript
app.get("/api/jokes/random/all", (req, res, next) => {
    var sql = 
    `SELECT j.id, joke, likes, dislikes, category FROM jokes j
     JOIN joke_category jc ON j.id = jc.joke_id
     JOIN categories c ON jc.category_id = c.id
     ORDER BY random() limit 1`
    var params = []
    db.all(sql, params, (err, rows) => {
        if (err) {
          res.status(400).json({"error":err.message});
          return;
        }
        res.json({
            "message":"success",
            "data":rows
        })
      });
});

```

#### Example response

```json
{
    "message": "success",
    "data": [
        {
            "id": 3,
            "joke": "What do you call a fish with no eyes? A fsh.",
            "likes": 1,
            "dislikes": 1,
            "category": "BadJokes"
        }
    ]
}
```
### Get random joke from a category

Get a random joke from a specified category with id.

```endpoint
GET /api/jokes/random/:id
```

#### Example request

```curl
$ curl http://74.234.41.60:8000/api/jokes/random/ComputerJokes
```

```javascript
app.get("/api/jokes/random/:category", (req, res, next) => {
    var sql = 
    `SELECT j.id, joke, likes, dislikes, category FROM jokes j
     JOIN joke_category jc ON j.id = jc.joke_id
     JOIN categories c ON jc.category_id = c.id
     WHERE c.category = ? 
     ORDER BY RANDOM() LIMIT 1`
    var params = [req.params.category]
    
    db.all(sql, params, (err, row) => {
        if (err) {
          res.status(400).json({"error":err.message});
          return;
        }
        res.json({
            "message":"success",
            "data":row
        })
      });
});
```

#### Example response

```json
{
    "message": "success",
    "data": [
        {
            "id": 5,
            "joke": "There are 10 types of people in the world: those who understand binary, and those who don’t.",
            "likes": 3,
            "dislikes": 1,
            "category": "ComputerJokes"
        }
    ]
}
```

### Give a vote to a joke 

Give "like" or "dislike" vote to a joke by it's id. Only one vote is recognized.

```endpoint
PUT /api/jokes/vote/:id
```

#### Example request

```curl
curl -d "likes={number}&dislikes={number}" http://74.234.41.60:8000/api/jokes/vote/5
```

```javascript
app.put("/api/jokes/vote/:id", (req, res, next) => {
    var data = {
        likes: req.body.likes,
        dislikes: req.body.dislikes,
    }
    var voted = {
        like: 0,
        dislike: 0
    }
    if (!data.likes&& !data.dislikes){
        console.log("No votes given!");
    }else {
        if (data.likes){
            voted.like = 1;
        }
        if (data.dislikes){
            voted.dislike = 1;
        }
        db.run(
            `UPDATE jokes SET 
               likes = likes + ?,
               dislikes = dislikes + ?
               where id=?
               `,
            [voted.like, voted.dislike, req.params.id],
            function (err, result) {
                if (err){
                    res.status(400).json({"error": res.message})
                    return;
                }
                res.json({
                    message: "success",
                    data: data,
                    changes: this.changes
                })
        });
    }

})
```

#### Example request body

```json
{
  "likes": 1
}
```

#### Example response

```json
{
    "message": "success",
    "data": {
        "likes": "1"
    },
    "changes": 1
}
```
### Show all jokes in order by number of likes

Shows a list of jokes in order by number of likes

```endpoint
GET /api/jokes/likes/order
```

#### Example request

```curl
curl http://74.234.41.60:8000/api/jokes/likes/order
```

```javascript
app.get("/api/jokes/likes/order", (req, res, next) => {
    var sql =
    `SELECT * FROM jokes 
     ORDER BY likes DESC`
    var params = []
    db.all(sql, params, (err, rows) => {
        if (err) {
          res.status(400).json({"error":err.message});
          return;
        }
        res.json({
            "message":"success",
            "data":rows
        })
      });
});
```

#### Example response

```json
{
    "message": "success",
    "data": [
        {
            "id": 1,
            "joke": "Debugging is like being the detective in a crime movie where you are also the murderer.",
            "likes": 4,
            "dislikes": 1
        },
        {
            "id": 5,
            "joke": "There are 10 types of people in the world: those who understand binary, and those who don’t.",
            "likes": 4,
            "dislikes": 1
        },
        {
            "id": 2,
            "joke": "What is the biggest lie in the entire universe? I have read and agree to the Terms & Conditions.",
            "likes": 3,
            "dislikes": 1
        },
        {
            "id": 3,
            "joke": "What do you call a fish with no eyes? A fsh.",
            "likes": 1,
            "dislikes": 1
        },
        {
            "id": 4,
            "joke": "What do you call a can opener that doesn’t work? A can’t opener!",
            "likes": 0,
            "dislikes": 1
        },
        {
            "id": 8,
            "joke": "What do you call cheese that’s feeling a little sad? Blue cheese.",
            "likes": 0,
            "dislikes": 0
        },
        {
            "id": 9,
            "joke": "Why do Java developers wear glasses? Because they can’t C#",
            "likes": 0,
            "dislikes": 0
        }
    ]
}
```
