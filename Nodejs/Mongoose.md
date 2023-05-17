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
tourSchema.virtual('durationWeeks').get(function() {
  return this.duration / 7;
});
```

==We use a regular function because an arrow function does not get its own this keyword because in this case the== `this` ==keyword it's going to be pointing to the current document.==

We could have done this process each time after we query the data, but that would not be the best practice because we want to keep business logic and application logic as much separated as posible.

# Document middleware

Mongoose has the middleware concept as well as express, we can make mongoose middleware to make something happen between two events, for example, each time a new document is saved to the database we can run a function between the save command is issued and the actual saving of the document or after the actual saving, this is why mongoose middleware it's also called ==pre and post hooks==.

There are four types of middleware in Mongoose:
- document 
- query
- aggregate
- model middleware

### pre middleware

```js 
tourSchema.pre('save', function(next) {
  this.slug = slugify(this.name, { lower: true });
  next();
});
```

document middleware runs before the `.save()` command and `.create()` but not on `insertMany` this method will not trigger the save middleware

- the 'save' in the code it's what we call a hook

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

### query middleware

This middleware looks exactly like the other middleware examples that we have the only difference here it's really the 'find' hook, which will make this query middleware and not document middleware. The big difference here is that the `this` keyword will not point at the current query and not at the current  document, because we are not really processing any documents in here we're really be processing a query.

```js
tourSchema.pre('find', function(next) {
  this.find({secretTour: { $ne: true }});
  next();
});
```

(The context: imagine that we want to keep some documents in secret for example for vip users so they will not appear publicly)

the above example would not work with the findOne query method, because they are not the same, what we can replace 'findOne' instead if we want to execute middleware query for this method, but we can do better with a **regular expression** 

```js
tourSchema.pre(/^find/, function(next) {
  this.find({secretTour: { $ne: true }});
  next();
});
```

what the regular expression will mean, it's all the strings that starts with find (including findOne)