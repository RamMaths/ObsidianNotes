# Aggregation pipeline 

An aggregation pipeline consists of one or more [stages](https://www.mongodb.com/docs/manual/reference/operator/aggregation-pipeline/#std-label-aggregation-pipeline-operator-reference) that process documents:

- Each stage performs an operation on the input documents. For example, a stage can filter documents, group documents, and calculate values. ==they start with $==
- The documents that are output from a stage are passed to the next stage.
- An aggregation pipeline can return results for groups of documents. For example, return the total, average, maximum, and minimum values.

### few examples from our project

```js
exports.getMonthlyPlan = async (req, res) => {
  try {
    const year = req.params.year * 1;

    const plan = await Tour.aggregate([
      {
        $unwind: '$startDates'
      },
      {
        $match: { startDates: {
            $gte: new Date(`${year}-01-01`),
            $lte: new Date(`${year}-12-31`),
          }
        }
      },
      {
        $group: {
          _id: { $month: '$startDates' },
          numTourStarts: { $sum: 1 },
          tours: { $push: '$name' }
        }
      },
      {
        $addFields: { month: '$_id' }
      },
      {
        $project: {
          _id: 0,
        }
      },
      {
        $sort: { numTourStarts: -1 }
      },
      {
        $limit: 12
      }
    ]);

    res.status(200).json({
    status: "success",
    data: plan
    });

  } catch(err) {
    res.status(404).json({
      status: 'failed',
      message: {mongoErr: err, errMessage: err.message}
    });
  }
};
```

# Virtual properties

They are basically fields that we can define in our schema but they will not be saved in our database (they will not be persistent). Virtual properties make a lot of sense for fields that can be derived from one another, for example a conversion from miles to kilometers.

```js
{
// here's where you put all the documents
},
{
  toJSON: { virtuals: true },
  toObject: { virtuals: true }
});
```

```js
tourSchema.virtual('durationWeeks').get(function() {
  return this.duration / 7;
});
```

==We use a regular function because an arrow function does not get its own this keyword because in this case the== `this` ==keyword it's going to be pointing to the current document.==

We could have done this process each time after we query the data, but that would not be the best practice because we want to keep business logic and application logic as much separated as posible.

# Mongoose middleware

Mongoose has the middleware concept as well as express, we can make mongoose middleware to make something happen between two events, for example, each time a new document is saved to the database we can run a function between the save command is issued and the actual saving of the document or after the actual saving, this is why mongoose middleware it's also called ==pre and post hooks==.

There are four types of middleware in Mongoose:
- document 
- query
- aggregate
- model middleware

## Document middleware

### pre middleware

```js 
tourSchema.pre('save', function(next) {
  this.slug = slugify(this.name, { lower: true });
  next();
});
```

document middleware runs before the `.save()` command and `.create()` but not on `insertMany` this method will not trigger the save middleware

- the 'save' in the code it's what we call a hook
- the document middle ware will only work with the `save` and `create` functions.

### post middleware

```js
tourSchema.post('save', function(doc, next) {
  console.log(doc);
  next();
});
```

the post middleware has access not only to next, but also to the document that was just saved to the database 
post middleware function are executed after all pre middleware functions have been completed, so we have the finished document as the first parameter that the callback function receives.

---
We can also have multiple pre or post middleware for the same hook but it's important to remember that we need to call the next function because otherwise our middleware is going to stuck in the pre save hook.

## Query Middleware

This middleware looks exactly like the other middleware examples that we have the only difference here it's really the 'find' hook, which will make this query middleware and not document middleware. The big difference here is that the `this` keyword will point at the current query and not at the current  document, because ==we are not really processing any documents in here we're really be processing a query==.

```js
tourSchema.pre('find', function(next) {
  this.find({secretTour: { $ne: true }});
  next();
});
```

(The context: imagine that we want to keep some documents in secret for example for vip users so they will not appear publicly)

the above example would not work with the findOne query method, because they are not the same, we can replace 'findOne' instead if we want to execute middleware query for this method, but we can do better with a **regular expression** 

```js
tourSchema.pre(/^find/, function(next) {
  this.find({secretTour: { $ne: true }});
  next();
});
```

what the regular expression will mean, it's all the strings that starts with find (including findOne)

## Aggregation middleware

This middleware it's for the aggregation, since we use middleware to filter some data in our example using the aggregation pipeline we will realize that our response it's still showing the one that we had kept secret because in fact we didn't add any functionality to our pipeline.

The `this` keyword it's pointing to the aggregation object in this example, this object has a `pipeline` property which it's  a function so if we do `console.log(this.pipeline())` we will see the following

```json
[
  { '$match': { ratingsAverage: [Object] } },
  {
    '$group': {
      _id: [Object],
      numTours: [Object],
      numRatings: [Object],
      avgRating: [Object],
      avgPrice: [Object],
      minPrice: [Object],
      maxPrice: [Object]
    }
  },
  { '$sort': { avgPrice: 1 } }
]
```

In fact is the aggregation pipeline that it's being executed.

```js
tourSchema.pre('aggregate', function(next) {
  console.log(this.pipeline().unshift({  
    $match: { secretTour: { $ne: true }}
  })); 
  next();
})
```

- `unshift` aggregates an element at the beginning of an array
- `shift` aggregates the element at the end

# Data validation with mongoose

**Validation**: is basically checking if the entered values are in the right format for each field in our document schema, and also that values have been entered for all of the required fields.

**Sanitization**: ensure that the inputted data is basically clean, so that there is no malicious code being injected into our data base or the application itself. In this step we remove unwanted characters or even code form the input data.

*Never ever accept input data coming from a user as it is*, so we always need to sanitize that incoming data.

## Built-in validators

Mongoose comes with built-in data validators, for example...

```js
  name: { 
    type: String,
    required: [true, 'A tour must have a name'],
    unique: true,
    trim: true,
    maxlength: [40, 'A tour name must have less or equal than 40 characters'],
    minlength: [10, 'A tour name must have more or equal than 10 characters']
  },
```

the `maxlength` and `minlength` are data validators that we can add while we are defining our Schema.

This will only work if we set the run validators option to true when we are awaiting for the query.

```js
exports.updateTour = async (req, res) => {
  try {

    const tour = await Tour.findByIdAndUpdate(req.params.id, req.body, {
      new: true,
      runValidators: true
    });

	.
	.
	.
}
```

We also have validators for the numbers. And not only numbers the `min` and `max` validators will also work with dates.

```js
  ratingsAverage: {
    type: Number,
    default: 4.5,
    min: [1, 'Ratings must be above 1'],
    max: [1, 'Ratings must be below 5']
  },
```

If we want the user to only choose between some set of values, we use the `enum` validator which only needs to point to an array with the values that user must choose. **This validator runs only with strings** 

```js
  difficulty: {
    type: String,
    required: [true, 'A tour must have a difficulty'],
    enum: ['easy', 'medium', 'difficult']
  },
```

But we are not sending a message here, what we can do it's wrap the array into an object as the `value` property and with the `message` as well.

```js
  difficulty: {
    type: String,
    required: [true, 'A tour must have a difficulty'],
    enum: {
      values: ['easy', 'medium', 'difficult'],
      message: 'Difficulty is either easy, medium or difficult'
    }
  },
```

# Custom validators

Sometimes the built-in validators are simply not enough and in that case we can also build our own custom validators, this would be actually really just a simple function which should return either *true* or *false* and if returns false it's because there's an error.

We specify an object for the schema type options, then we will set the property `validate`  to the function that we want to validate, we should use this callback function as a real function and not an arrow function because we want the `this` keyword to point to the current document. *The this keyword will only point to the current document while we are creating a new document* so this function is not going to work on update. This callback function will have access to the value that was inputted so in this case the `priceDiscount` that the use specified.

```js
  priceDiscount: {
    type: Number,
    validate: function(value) {
      return this.price > value;
    }
  },
```

If we want to specify a message we do almost the same as the last example. We pass an object in which we will define the `validator` property for our custom validator and the `message` validator as well as the last example. Just like the function the `message` property has actually access to the actual value but we access it with this mongoose syntax `({VALUE})`.

```js
  priceDiscount: {
    type: Number,
    validate: {
      validator: function(value) {
        return this.price > value;
      },
      message: 'Discount price ({VALUE}) must be below the regular price'
    }
  },
```
