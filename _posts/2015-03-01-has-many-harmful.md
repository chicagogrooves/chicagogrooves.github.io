# has_many considered: harmful

TL;DR Despite a recent current of thought asserting the opposite, Monoliths are still bad and you still should not be writing them. Also, `has_many` is an anti-pattern, seductive for sure, but you can give up your dependence on it, if you are up for questioning some things you've till now taken for granted.

First - what defines a monolithic application? It is one that cannot be split apart because everything depends on everything else. 

One major culprit of not being able to split an application up is a dependency cycle. If you have a guitar cable sending its sound into the guitar - it's a bad idea to connect the amp back up to the guitar right? Feedback loops amplify problems, and while in audio they can blow out speakers (although sound amazing in the hands of a master), in software they turn your app into a Big Ball of Mud the moment they appear. So what explains the persistence of dependency cycles in almost every Rails app ever? 

`has_many`. It has seduced you with it's allure. First it woos you with its eerily easy way of getting to child entities - `user.posts`, `customer.accounts`, etc. Then it distracts you with its apparent complementary nature with `belongs_to`. You can't split up this dynamic duo, like peanut butter & jelly, right? Wrong. `has_many` is the Siren that lured your ship straight into the Cliffs of Monolith.

What is the first model you added to your application? Probably **User**, right? So, once you wrote `user.rb` and its corresponding tests, and committed it - why did you ever open that file up again to tell it about something that it did not need to know existed? Rails keeps you from reopening `user.rb` if you add a column to the **User** table, and this is good right? So, why when you added a table far away, did you open **User** up again to tell **User** about it? Did you not realize you were violating the Open-Closed principle, one of the 5 principles of SOLID design? Somewhere inside I bet you knew it felt dirty to keep opening up **User** and making it aware of things that it had been blissfully unaware of. But you did it anyway, and so did I for a long time, and I'm still getting over that dirty feeling. Damn temptress, `has_many`.

On the other hand, `belongs_to` is a SOLID, dependable creature like a loyal Saint Bernard. It's necessity follows from the need to enforce the rule that a Post must never exist without a **User**. It is described as being "*functionally dependent*", in the language of databases. Never once in a relational database model does a parent table know about its children, except in strategic cases like creating a denormalized post_count field on User. Boyce and Codd, the seriously smart and analytical creators of the Relational Model would tell you that if you have two tables that each have foreign keys to each other a) you can't insert records separately, only within the same transaction and b) these probably belong in the same table. Do we think somehow that these rules don't apply any more?

Some seriously smart people designed the Relational Model and they knew it needed to discourage this kind of cycle. The definition of the Normal Forms, and the recommendation to achieve 3rd Normal Form is exactly that knowledge in action. Now, some seriously smart people designed Rails as well, but their design goals had more to do with creating dynamic websites quickly than helping you stay off the Cliffs of Monolith. And that is precisely why `has_many` has led us to the predicament we're in today - it's established practice, yet fundamentally flawed, and a primary cause of Monolith syndrome.

So here we are, friends. `has_many` is unnecessary. It is mostly to blame for `user.rb` having the worst thrash in the codebase. It is to blame for your turning your data model into a web of dependency cycles. And you must now try to see that it's not as convenient as you once thought. Ask yourself if you can live with the second one of these, if you know it will reduce thrash of the user.rb file, allow you to follow SOLID principles, and break dependency cycles in your application? 

```
posts = user.posts
posts = Posts.for(user)
#assuming polymorphic 'for', otherwise...
posts = Posts.for_user(user)
```


----------
