# ActiveRecord Review Discussion

## ActiveRecord

Before we learned about ActiveRecord, we were able to call on a class method such as `Pet.all` which would return a collection of all the pet instances. That method would look like this:

```ruby
  class Pet
    # ...

    def self.all
      ALL
    end

    # ...
  end
```

When our class inherits from `ActiveRecord::Base`, we get the `all` method (and many more methods) for free.

Discuss with your table the steps involved in ActiveRecord's implementation of the class method `all`.  How is SQL used? What's the return value?

-Person.find(:all) # returns an array of objects for all the rows fetched by SELECT * FROM people, so ActiveRecord's all is not actually it's own method, its just a shortcut to the find method. SELECT * FROM people (where people is the table name). Return value is an array of instances.

## Domain Modeling

With your table discuss how you would model out the relationships between three models `Voter`, `Vote`, and `Candidate`.  On which table do the foreign keys belong?

      Voter        Vote                        Candidate
      -name       -both foreign keys          -name
                                              -party


## AR Query Methods

Take a look at the following `Voter` class:

```ruby
class Voter < ActiveRecord::Base
  has_many :votes
  has_many :candidates, through: :votes
end
```

By providing the macros `has_many :votes` and `has_many :candidates, through: :votes`, ActiveRecord gives the `Voter` class the instance methods `Voter#votes` and `Voter#candidates`. These methods will fire some SQL, grab some rows from the database, and return the appropriate Ruby instances.

Your task is to discuss and write out the SQL statements that will run for each of the following expressions

```ruby
voter = Voter.create

INSERT INTO voters (name) VALUES ('Olivia')

candidate = Candidate.create

INSERT INTO candidates (name, party) VALUES ('Bobby', 'Green')

voter.votes

SELECT * FROM votes
JOIN votes ON voter.id = votes.voter_id
WHERE votes.voter_id = voter.id

voter.candidates

SELECT * FROM candidates
JOIN votes ON candidate.id = votes.candidate_id
JOIN voters ON voter.id = votes.voter_id
WHERE voter.id = candidate.id

#Almost like saying WHERE voter.id = votes.voter_id.map{|i| i.candidate_id} = candidate.id

voter = Voter.create()
candidate = Candidate.create()

vote = Vote.create(voter_id: voter.id, candidate_id: candidate.id)

INSERT INTO votes (voter_id, candidate_id) VALUES (voter.id, candidate.id)

vote = voter.votes.create(candidate: candidate)

INSERT INTO votes (voter_id, candidate_id) VALUES (voter.id, candidate.id)

vote.voter

SELECT * FROM voters
JOIN votes ON voter.id = votes.voter_id
WHERE votes.id = vote.id


```
