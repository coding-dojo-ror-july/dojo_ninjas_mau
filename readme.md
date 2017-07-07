# Assignment: Dojo Ninjas
Create a new project called 'dojo_ninjas' and have it use the default SQLite database for both development and testing environment. For this project, you're going to need the following tables/models. Please see the following diagram and create the necessary model:

Dojo
Have it include the name of the dojo and the city and state of each dojo
Have the first dojo be "CodingDojo Silicon Valley" in "Mountain View", "CA".
Have the second dojo be "CodingDojo Seattle" in "Seattle", "WA".
Have the third dojo be "CodingDojo New York" in "New York", "NY".

Ninja
Have it include first_name, last_name of each ninja in the dojo.
Each dojo can have multiple ninjas and each ninja belongs to a specific dojo.
IMPORTANT INFO: When you do any of these assignments in this chapter, you should not reference the foreign key directly. For example, instead of writing Ninja.create(dojo_id: 1, ...) , you should write do something like this: Ninja.create(dojo: Dojo.first, ...).  By doing this, we are preserving our database from having bad information. In the first example, a Ninja will be created even if a dojo with id of 1 does not yet exist. In the second example, Active Record will only create a ninja if there is a first dojo in our database.

This is what you'll do:

Start a new project (the name of the project should be 'dojo_ninjas')
Create appropriate tables/models.
Using Ruby console:
Create 3 dojos (insert some blank entries just to make sure it's allowing you to insert empty entries)
Change your models so that it does the following validations:
For the dojo, require the presence of the name, city, and state; also require the state to be two characters long
For the ninja, require the presence of the first name and last name
Make sure that your ninja model belongs to the dojo (specify this in the model for both the dojo and the ninja)
Using ruby console
Delete the three dojos you created (e.g. Dojo.find(1).destroy; also look up destroy_all method)
Create 3 additional dojos by using Dojo.new.
Try to create a few more dojos but without specifying some of the required fields. Make sure that the validation works and generates the errors.
Create 3 ninjas that belong to the first dojo you created.
Create 3 more ninjas and have them belong to the second dojo you created.
Create 3 more ninjas and have them belong to the third dojo you created.
Make sure you understand how to get all of the ninjas for any dojo (e.g. get all the ninjas for the first dojo, second dojo, etc).
How would you only retrieve the first_name of the ninjas that belong to the second dojo and order the result by created_at DESC order?
Delete the second dojo. How could you adjust your model so that it automatically removes all of the ninjas associated with that dojo?
Make sure that you feel completely comfortable with using .all, .valid?, .where, .order, .limit, .save, .create, .errors, and other methods discussed in the video.

# 1. Start a new project (the name of the project should be 'dojo_ninjas')
rails new dojo_ninjas
cd dojo_ninjas

# 2. Create appropriate tables/models.
rails generate model Dojo name:string city:string state:string
rails generate model Ninja first_name:string last_name:string dojo:references
rake db:migrate

# 3. Using Ruby console:
rails c

# 3.1 Create 3 dojos (insert some blank entries just to make sure it's allowing you to insert empty entries)
Dojo.create(name: "CodingDojo Silicon Valley", city: "Mountain View", state: "CA")
Dojo.create(name: "CodingDojo Seattle", city: "Seattle", state: "WA")
Dojo.create(name: "CodingDojo New York", city: "New York", state: "NY")
Dojo.create(name: "CodingDojo Los Angeles", state: "CA")

# 4. Change your models so that it does the following validations:
class Dojo < ActiveRecord::Base
  # 5. Make sure that a dojo has many ninjas
  has_many :ninjas
  # 4.1 require the presence of the name, city, and state; also require the state to be two characters long
  validates :name, :city, :state, presence: true
  validates :state, length: { is: 2 }
end

class Ninja < ActiveRecord::Base
  # 5. Make sure that a ninja belongs to a dojo
  belongs_to :dojo
  # 4.2 require the presence of the first name and last name
  validates :first_name, :last_name, presence: true
end

# 6. Using ruby console
# 6.1 Delete the three dojos you created
Dojo.destroy_all

# 6.2 Create 3 additional dojos by using Dojo.new.
dojo1 = Dojo.new(name: "CodingDojo Seattle", city: "Seattle", state: "WA")
dojo1.save
dojo2 = Dojo.new(name: "CodingDojo Silicon Valley", city: "Mountain View", state: "CA")
dojo2.save
dojo3 = Dojo.new(name: "CodingDojo New York", city: "New York", state: "NY")
dojo3.save

# 6.3 Try to create a few more dojos but without specifying some of the required fields. Make sure that the validation works and generates the errors.
dojo = Dojo.new(name: "CodingDojo Los Angeles", city: "Los Angeles")
dojo.save
dojo.errors.full_messages # => ["State can't be blank", "State is the wrong length (should be 2 characters)"]

dojo = Dojo.new(name: "CodingDojo Los Angeles", state: "CA")
dojo.save
dojo.errors.full_messages # => ["City can't be blank"]

dojo = Dojo.new(city: "Los Angeles", state: "CA")
dojo.save
dojo.errors.full_messages # => ["Name can't be blank"]

reload!

# 6.4 Create 3 ninjas that belong to the first dojo you created.
Dojo.first.ninjas.create(first_name: "John", last_name: "Doe")
Dojo.first.ninjas.create(first_name: "Jane", last_name: "Doe")
Dojo.first.ninjas.create(first_name: "Jennifer", last_name: "Doe")

# 6.5 Create 3 more ninjas and have them belong to the second dojo you created.
Dojo.second.ninjas.create(first_name: "Michael", last_name: "Smith")
Dojo.second.ninjas.create(first_name: "Michelle", last_name: "Smith")
Dojo.second.ninjas.create(first_name: "Mitchell", last_name: "Smith")

# 6.6 Create 3 more ninjas and have them belong to the second dojo you created.
Dojo.third.ninjas.create(first_name: "Kevin", last_name: "Johnson")
Dojo.third.ninjas.create(first_name: "Kyle", last_name: "Johnson")
Dojo.third.ninjas.create(first_name: "Kurt", last_name: "Johnson")

# 7. Make sure you understand how to get all of the ninjas for any dojo (e.g. get all the ninjas for the first dojo, second dojo, etc)
Dojo.first.ninjas
Dojo.second.ninjas
Dojo.third.ninjas

# 8. How would you only retrieve the first_name of the ninja that belongs to the second dojo and order the result by created_at DESC order?  
Ninja.where(dojo: Dojo.second).select(:id, :first_name).order(created_at: :desc)

# 9. Delete the second dojo. How could you adjust your model so that it automatically removes all of the ninjas associated with that dojo?
class Dojo < ActiveRecord::Base
  # add dependent: :destroy
  has_many :ninjas, dependent: :destroy
  validates :name, :city, :state, presence: true
  validates :state, length: { is: 2 }
end